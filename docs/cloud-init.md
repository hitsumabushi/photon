Overview
=================
`cloud-init` is the defacto multi-distribution package that handles early initialization of a cloud instance.

In-depth documentation for cloud-init is available here: https://cloudinit.readthedocs.org/en/latest/

Supported installations
=================
<dl>
<dt><code>Photon Container OS (Minimal)</code></dt>
<dt><code>Photon Full OS (All)</code></dt>
</dl>

Supported capabilities
=================
`Photon` supports `cloud-init` starting with the following capabilities
<dl>
<dt><code>run commands</code></dt>
<dd>execute a list of commands with output to console.</dd>
<dt><code>configure ssh keys</code></dt>
<dd>add entry to ~/.ssh/authorized_keys for the configured user</dd>
<dt><code>install package</code></dt>
<dd>install additional packages on first boot</dd>
<dt><code>configure networking</code></dt>
<dd>update /etc/hosts, hostname etc</dd>
<dt><code>write files</code></dt>
<dd>write arbitrary file(s) to disk</dd>
<dt><code>add yum repo</code></dt>
<dd>add a yum repository to /etc/yum.repos.d</dd>
<dt><code>create groups and users</code></dt>
<dd>add groups and users to the system. set user/group properties</dd>
<dt><code>run yum upgrade</code></dt>
<dd>upgrade all packages</dd>
<dt><code>reboot</code></dt>
<dd>reboot or power off when done with cloud-init</dd>
</dl>

Getting Started
=================
photon cloud config has `ec2 datasource` turned on by default so an `ec2` configuration is accepted. 
However, for testing, the following methods provide ways to do `cloud-init` with `photon` standalone.

Using a seed iso
----------------
This will be using the `nocloud` datasource. In order to init this way, an iso file needs to be created 
with a meta-data and an user-data file as shown below
<code><pre>
$ { echo instance-id: iid-local01; echo local-hostname: cloudimg; } > meta-data
$ printf "#cloud-config\nhostname: testhost\n" > user-data
$ genisoimage  -output seed.iso -volid cidata -joliet -rock user-data meta-data
</pre>
</code>

Attach the above generated seed.iso to your machine and reboot for the init to take effect. 
In this case, the hostname is set to `testhost`

Using a seed disk file
----------------
To init using local disk files, do the following
<code><pre>
mkdir /var/lib/cloud/seed/nocloud
cd /var/lib/cloud/seed/nocloud
$ { echo instance-id: iid-local01; echo local-hostname: cloudimg; } > meta-data
$ printf "#cloud-config\nhostname: testhost\n" > user-data
</pre></code>
Reboot the machine and the hostname will be set to `testhost`

Frequencies
-----------
cloud-init modules have pre-determined frequencies. Based on the frequency setting, multiple runs will yield different results. 
For the scripts to run always, remove instances folder before reboot.
<code><pre>
rm -rf /var/lib/cloud/instances
</code></pre>

Module frequency info
---------------------
Name  |  Frequency
------|-------------
disable_ec2_metadata  | Always
users_groups  | Instance
write_files  | Instance
update_hostname  | Always
final_message  | Always
resolv_conf  | Instance
growpart  | Always
update_etc_hosts  | Always
power_state_change  | Instance
phone_home  | Instance
