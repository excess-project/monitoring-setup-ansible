# EXCESS ATOM Monitoring Ansible Provision

> ATOM enables users to monitor applications at run-time with ease. This repository contains playbooks and roles to configure the monitoring framework with Ansible. In addition, we provide a simple Vagrant file to have a basic installation running in three VMs.

## Motivation

We already provide some Shell scripts in order to install dependencies, and setup the ATOM monitoring framework locally. However, we would also give developers the chance to test our monitoring framework in a cluster configuration. For this purpose, this repository contains Ansible playbooks to setup a master node (monitoring-server + monitoring-frontend + Elasticsearch) and multiple slave nodes (monitoring-agent) that send metric data to the master node.

## Prerequisites

Before you proceed, please ensure that you have installed a current version of Vagrant (if you intend to try out our pre-configured, VM-based setup), and the latest version of Ansible. Please note that the Ansible playbooks require Ansible >= 2.x.

Please execute the following commands in order to setup the basic deployment environment. If you should use an operating system other than Ubuntu, please adapt the following commands for your distribution of choice.

```bash
sudo apt-get install vagrant
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

## Vagrant Demonstration

Using Vagrant, you can setup quickly a three VM-based cluster on your local machine, where one VM represents a master node, and
the other two VMs serve as slaves that report the current used and free memory to the master.

```bash
git clone https://github.com/excess-project/monitoring-setup-ansible.git
cd monitoring-setup-ansible
vagrant up
```

After all three VMs are deployed, you can access the Web front-end of the ATOM monitoring framework at the following URL:

```bash
http://192.168.33.10:3000
```

You should see in the list of experiments one entry that reports memory data for the two slave VMs. In addition to the
Web front-end, the RESTful service is accessible via:

```bash
http://192.168.33.10:3030
```

If you intend to change available metrics, please have a look at the **config** folder. You'll find there the **mf_config.ini** file, where you can enable/disable plugins and metrics individually. Please note that most plugins are not coming with VM support, and thus won't work. That's why we use the memory plugin for demonstration purposes.

Of course, you can also just setup a master node

```bash
vagrant up node1
```

or a single slave node via

```bash
vagrant up node2
```


## Setup a master node via Ansible

Aside from Vagrant, you can use the Ansible playbooks directly. In order to setup a master node with the monitoring-server,
monitoring-frontend, and Elasticsearch, please execute the following commands:

```bash
git clone https://github.com/excess-project/monitoring-setup-ansible.git
cd monitoring-setup-ansible
ansible-playbook -i hostfile --extra-vars "user=$USER" ansible/monitoring-server.yml
```

With **-i**, you can specify the host that should be configured as the monitoring master node. An example **hostfile**
can look as follows:

```bash
[master]
192.168.0.21

[worker]
192.168.0.22
192.168.0.23
```

Here, we have defined two groups (master and worker), and assigned a list of nodes to them.

With **-u**, we set the installation user. For Vagrant VMs, the user equals **vagrant**. Finally, we can specify
the playbook for the monitoring server, namely **ansible/monitoring-server.yml**. Please ensure that you can access
the remote server via SSH. For testing, you can replace **192.168.0.21** with **localhost**, and then allow to SSH
into localhost with:

```bash
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod og-wx ~/.ssh/authorized_keys
```


## Setup a monitoring node via Ansible

In order to setup a monitoring node, on which the monitoring agent should run, please follow the next steps.

```bash
git clone https://github.com/excess-project/monitoring-setup-ansible.git
cd monitoring-setup-ansible
ansible-playbook -i hostfile --extra-vars "user=$USER" ansible/monitoring-agent.yml
```

Please have a look at the previous section with respect to the arguments provided for **ansible-playbook**.


## Acknowledgment

This project is realized through [EXCESS][excess]. EXCESS is funded by the EU 7th
Framework Programme (FP7/2013-2016) under grant agreement number 611183. We are
also collaborating with the European project [DreamCloud][dreamcloud].


## Contributing
Find a bug? Have a feature request?
Please [create](https://github.com/excess-project/monitoring-setup-ansible/website/issues) an issue.


## Main Contributors

**Dennis Hoppe, HLRS**
+ [github/hopped](https://github.com/hopped)


## Release History

| Date        | Version | Comment                                    |
| ----------- | ------- | ------------------------------------------ |
| 2016-10-14  | 16.6    | Updated recipes to support ATOM v16.6      |
| 2016-04-27  | 16.2    | Public release compatible with ATOM v16.2  |


[excess]: http://www.excess-project.eu
[dreamcloud]: http://www.dreamcloud-project.eu
