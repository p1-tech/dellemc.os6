OS6 Out-Of-Band Interface Role
==============

This role creates and optionally deploys configuration for the out-of-band (OOB) management interface on Dell EMC Networking OS6 devices.  This role is abstracted for Dell EMC PowerSwitch platforms running Dell EMC OS6.

The option to deploy the configuration can be used to generate the config to be manually deployed, or can be deployed automatically to switch that is available over SSH.

### CAUTION: CHANGING THE MANAGEMENT IP ADDRESS OF A NETWORKED DEVICE MAY MAKE IT UNREACHABLE.

Role Variables
--------------

- Role is abstracted using the `ansible_network_os` variable that can take `dellemc.os6.os6` as a value
- If `os6_cfg_generate` is set to true, the variable generates the role configuration commands in a file
- If `os6_cfg_deploy` is set to true, the variable deploys the role configuration commands to the ansible_host
- `os6_oob` (dictionary) holds a dictionary with the out-of-band CIDR network and out-of-band gateway address; the actual IP address used for the OOB interface should generally be the `ansible_host` variable with the subnet denoted in CIDR notation, as this is the management interface

**os6_oob name keys**

| Key        | Type                      | Description                                             | Support               |
|------------|---------------------------|---------------------------------------------------------|-----------------------|
| ``cidr``  | string         | Configures the OOB IP address and subnet in CIDR notation  | os6 |
| ``gateway``      | string         | Configures the OOB Gateway | os6 |

Connection variables
--------------------

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible *group_vars* or *host_vars* directories or inventory, or in the playbook itself.

| Key         | Required | Choices    | Description                                         |
|-------------|----------|------------|-----------------------------------------------------|
| ``ansible_host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport |
| ``ansible_port`` | no       |            | Specifies the port used to build the connection to the remote device; if value is unspecified, the `ANSIBLE_REMOTE_PORT` option is used; it defaults to 22 |
| ``ansible_ssh_user`` | no       |            | Specifies the username that authenticates the CLI login for the connection to the remote device; if value is unspecified, the `ANSIBLE_REMOTE_USER` environment variable value is used  |
| ``ansible_ssh_pass`` | no       |            | Specifies the password that authenticates the connection to the remote device.  |
| ``ansible_become`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the `ANSIBLE_BECOME` environment variable value is used, and the device attempts to execute all commands in non-privileged mode |
| ``ansible_become_method`` | no       | enable, sudo\*   | Instructs the module to allow the become method to be specified for handling privilege escalation; if value is unspecified, the `ANSIBLE_BECOME_METHOD` environment variable value is used. |
| ``ansible_become_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if ``ansible_become`` is set to no this key is not applicable. |
| ``ansible_network_os`` | yes      | os6, null\*  | This value is used to load the correct terminal and cliconf plugins to communicate with the remote device. |

> **NOTE**: Asterisk (*) denotes the default value if none is specified.

Example playbook
----------------

This example uses the *os6-oob* role to configure the OOB interface.  The example creates a *hosts* files with te switch details and corresponding variables.  The hosts file should define the `ansible_network_os` variable with corresponding Dell EMC OS6 name.

**Sample hosts file**

    switch1 ansible_host= <ip_address> 

**Sample host_vars/switch1**

    hostname: "switch1"
    ansible_become: yes
    ansible_become_method: enable
    ansible_become_pass: xxxxx
    ansible_ssh_user: xxxxx
    ansible_ssh_pass: xxxxx
    ansible_network_os: dellemc.os6.os6
    build_dir: ../temp/temp_os6

    os6_oob:
      cidr: "{{ ansible_host }}/24"
      gateway: "172.16.10.1"

**Simple playbook to setup system — switch1.yaml**

    - hosts: switch1
      roles:
         - dellemc.os6.os6_oob

**Run**

    ansible-playbook -i hosts switch1.yaml