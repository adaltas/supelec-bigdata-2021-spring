# NiFi + Kafka VM

The goal of this lab are to:

- Create a [CentOS 7](https://wiki.centos.org/) VM using [Vagrant](https://www.vagrantup.com/)
- Install and start [Apache NiFi](http://nifi.apache.org/) and [Apache Kafka](http://kafka.apache.org/) on this VM using [Ansible](https://www.ansible.com/)
- Build a small data flow with NiFi and Kafka

## Usefull links

- [Vagrant documentation](https://www.vagrantup.com/docs/)
- [Ansible documentation](https://docs.ansible.com/ansible/latest/index.html)
- [Ansible module index](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html)
- [Kafka Quick Start doc](http://kafka.apache.org/21/documentation.html#quickstart)
- [NiFi Getting Started doc](https://nifi.apache.org/docs/nifi-docs/html/getting-started.html)

## Prerequisites

Before you can start the lab, you have to:

1. Install Virtualbox: https://www.virtualbox.org/wiki/Downloads
2. Install Vagrant on your computer: https://www.vagrantup.com/downloads.html
3. (Optional) **On Windows**, ensure that Hyper-V is disabled:
   1. Open a new Powershell
   2. Run the following command:
      ```powershell
      Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
      ```
4. Download the `centos/7` Vagrant box for the **Virtualbox provider**:

   ```shell
   $ vagrant box add centos/7
   ==> box: Loading metadata for box 'centos/7'
       box: URL: https://vagrantcloud.com/centos/7
   This box can work with multiple providers! The providers that it
   can work with are listed below. Please review the list and   choose
   the provider you will be working with.

   1) hyperv
   2) libvirt
   3) virtualbox
   4) vmware_desktop

   Enter your choice: 3
   ```

5. Clone the lab repository to your computer:
   ```
   git clone https://github.com/adaltas/ece-hadoop.git
   ```
6. Go to the `nifi-kafka` directory:
   ```
   cd supelec-bigdata-2021-spring/nifi-kafka
   ```

## Lab

### Useful information

In the lab, we will use the `ansible_local` provisioner, so Ansible will be automatically installed on the VM by Vagrant. You don't need it on your computer!

### Resources

In the `nifi-kafka` directory, you will find:

- A `Vagrantfile` that defines the VMs to be managed by Vagrant (1 CentOS 7 VM named `nifi_server` in our case)
- A `playbooks/` directory that contains Ansible playbooks to install and start NiFi and Kafka

### Lab steps

1. Take a look at the [`Vagrantfile`](Vagrantfile) and at the YAML files [`playbooks/run.yml`](playbooks/run.yml) and [`playbooks/nifi/tasks/main.yml`](playbooks/roles/nifi/tasks/main.yml)
2. Create and provision the VM
   1. Run the `vagrant up` command
   2. You should end up with the following error (this is planned):
      ```shell
      TASK [nifi : Start NiFi service] ***********************************************
      fatal: [nifi_server]: FAILED! => {"changed": true, "cmd": ["service", "nifi", "start"], "delta": "0:00:00.036977", "end": "2019-11-21 13:58:56.052907", "msg": "non-zero return code", "rc": 1, "start": "2019-11-21 13:58:56.015930", "stderr": "/usr/nifi-1.9.2/bin/nifi.sh: line 139: type: java: not found", "stderr_lines": ["/usr/nifi-1.9.2/bin/nifi.sh: line 139: type: java: not found"], "stdout": "nifi.sh: JAVA_HOME not set; results may vary\nnifi.sh: java command not found", "stdout_lines": ["nifi.sh: JAVA_HOME not set; results may vary", "nifi.sh: java command not found"]}
      ```
   3. Check that everything is ok with the VM by connecting to it through SSH:
      ```
      vagrant ssh nifi_server
      ```
3. Complete the NiFi installation
   1. Find out what is wrong with the installation process (based on the error message)
   2. Make the necessary changes to [`nifi/tasks/main.yml`](playbooks/roles/nifi/tasks/main.yml)
   3. Update the playbooks on the VM using `vagrant upload`:
      ```
      vagrant upload playbooks /vagrant/playbooks nifi_server
      ```
   4. Rerun provisioning with the command `vagrant provision`
4. Test your NiFi installation by connecting to http://20.20.20.21:8080/nifi
5. Complete the `kafka` role
6. Run the `kafka` role only
   1. Update the playbooks on the VM using `vagrant upload`
   2. Connect to the VM using `vagrant ssh`
   3. Run the playbooks using the right tag (replace `TAG`):
      ```
      ansible-playbook /vagrant/playbooks/run.yml --tags TAG -i /tmp/vagrant-ansible/inventory/vagrant_ansible_local_inventory
      ```
7. Test your Kafka installation by creating a topic (see [Kafka Quick Start doc](http://kafka.apache.org/21/documentation.html#quickstart))
