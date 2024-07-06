# Proxmox Rebalancing Service
<img align="left" src="https://cdn.gyptazy.ch/images/proxmox-rebalancing-service-gyptazy-text.jpg"/>
<br>

<p float="center"><img src="https://img.shields.io/github/license/gyptazy/proxmox-rebalancing-service"/><img src="https://img.shields.io/github/contributors/gyptazy/proxmox-rebalancing-service"/><img src="https://img.shields.io/github/last-commit/gyptazy/proxmox-rebalancing-service/main"/><img src="https://img.shields.io/github/issues-raw/gyptazy/proxmox-rebalancing-service"/><img src="https://img.shields.io/github/issues-pr/gyptazy/proxmox-rebalancing-service"/></p>


## Table of Content
* Introduction
  * Video of Migration
* Features
* Usage
  * Dependencies
  * Options
  * Parameters
  * Systemd
  * Manuel
  * Proxmox GUI Integration
  * Quick Start
* Motivation
* References
* Packages
* Misc
  * Bugs
  * Contributing
  * Author(s)

## Introduction
The `Proxmox Rebalancing Service` (PRS) is an advanced tool designed to enhance the efficiency and performance of Proxmox clusters by optimizing the distribution of virtual machines (VMs) across the cluster nodes by using the Proxmox API. PRS meticulously gathers and analyzes a comprehensive set of resource metrics from both the cluster nodes and the running VMs. These metrics include CPU usage, memory consumption, and disk utilization, specifically focusing on local disk resources.

PRS collects resource usage data from each node in the Proxmox cluster, including CPU, (local) disk and memory utilization. Additionally, it gathers resource usage statistics from all running VMs, ensuring a granular understanding of the cluster's workload distribution.

Intelligent rebalancing is a key feature of PRS where it re-balances VMs based on their memory, disk or cpu usage, ensuring that no node is overburdened while others remain underutilized. The rebalancing capabilities of PRS significantly enhance cluster performance and reliability. By ensuring that resources are evenly distributed, PRS helps prevent any single node from becoming a performance bottleneck, improving the reliability and stability of the cluster. Efficient rebalancing leads to better utilization of available resources, potentially reducing the need for additional hardware investments and lowering operational costs.

Automated rebalancing reduces the need for manual actions, allowing operators to focus on other critical tasks, thereby increasing operational efficiency.

### Video of Migration
<img src="https://cdn.gyptazy.ch/images/proxmox-rebalancing-service-gyptazy-video.gif"/>

## Features
* Rebalance the cluster by:
  * Memory
  * Disk (only local storage)
  * CPU
* Performing
  * Periodically
  * One-shot solution
* Filter
  * Exclude nodes
  * Exclude virtual machines
* Migrate VM workloads away (e.g. maintenance preparation)
* Usage
  * One-Shot (one-shot)
  * Periodically (daemon)
  * Proxmox Web GUI Integration (optional)

## Usage
Running PRS is easy and it runs almost everywhere since it just depends on Python3 and the proxmoxer library. Therefore, it can directly run on a Proxmox node, dedicated systems like Debian, RedHat, or even FreeBSD, as long as the API is reachable by the client running PRS.

### Dependencies
* Python3
* proxmoxer (Python module)

### Options
The following options can be set in the `prs.conf` file:

| Option | Example | Description |
|------|:------:|:------:|
| api_host | hypervisor01.gyptazy.ch | Host or IP address of the remote Proxmox API. |
| api_user | root@pam | Username for the API. |
| api_pass | FooBar | Password for the API. |
| verify_ssl | 1 | Validate SSL certificates (1) or ignore (0). (default: 1) |
| method | memory | Defines the balancing method (default: memory) where you can use `memory`, `disk` or `cpu`. |
| ignore_nodes | dummynode01,dummynode02 | Defines a comma separated list of nodes to exclude. |
| ignore_vms | testvm01,testvm02 | Defines a comma separated list of VMs to exclude. |
| daemon | 1 | Run as a daemon (1) or one-shot (0). (default: 1) |
| schedule | 24 | Hours to rebalance in hours. (default: 24) |

An example of the configuration file looks like:
```
[proxmox]
api_host: hypervisor01.gyptazy.ch
api_user: root@pam
api_pass: FooBar
verify_ssl: 1
[balancing]
method: memory
ignore_nodes: dummynode01,dummynode02
ignore_vms: testvm01,testvm02
[service]
daemon: 1
```

### Parameters
The following options and parameters are currently supported:

| Option | Long Option | Description | Default |
|------|:------:|------:|------:|
| -c | --config | Path to a config file. | /etc/proxmox-rebalancing-service/prs.conf (default) |

### Systemd
When installing a Linux distribution (such as .deb or .rpm) file, this will be shipped with a systemd unit file. The default configuration file will be sourced from `/etc/proxmox-rebalancing-service/prs.conf`.

| Unit Name | Options |
|------|:------:|
| proxmox-rebalancing-service | start, stop, status, restart |

### Manual
A manual installation is possible and also supports BSD based systems. Proxmox Rebalancing Service relies on mainly two important files:
* proxmox-rebalancing-service (Python Executable)
* prs.conf (Config file)

The executable must be able to read the config file, if no dedicated config file is given by the `-c` argument, PRS tries to read it from `/etc/proxmox-rebalancing-service/prs.conf`.

### Proxmox GUI Integration
<img align="left" src="https://cdn.gyptazy.ch/images/proxmox-rebalancing-service-GUI-integration.jpg"/> PRS can also be directly be used from the Proxmox Web UI by installing the optional package `pve-proxmox-rebalancing-service-ui` package which has a dependency on the `proxmox-rebalancing-service` package. For the Web UI integration, it requires to be installed (in addition) on the nodes on the cluster. Afterwards, a new menu item is present in the HA chapter called `Rebalancing`. This chapter provides two possibilities:
* Rebalancing VM workloads
* Migrate VM workloads away from a defined node (e.g. maintenance preparation)

### Quick Start
The easiest way to get started is by using the ready-to-use packages that I provide on my CDN and to run it on a Linux Debian based system. This can also be one of the Proxmox nodes itself.

```
wget https://cdn.gyptazy.ch/files/amd64/debian/proxmox-rebalancing-service/proxmox-rebalancing-service_0.9.9_amd64.deb
dpkg -i proxmox-rebalancing-service-0.9.9.deb
# Adjust your config
vi /etc/proxmox-rebalancing-service/prs.conf
systemctl restart proxmox-rebalancing-service
systemctl status proxmox-rebalancing-service
```

### Logging
PRS uses the `SystemdHandler` for logging. You can find all your logs in your systemd unit log or in the journalctl.

## Motivation
As a developer managing a cluster of virtual machines for my projects, I often encountered the challenge of resource imbalance. Nodes within the cluster would become unevenly loaded, with some nodes being overburdened while others remained underutilized. This imbalance led to inefficiencies, performance bottlenecks, and increased operational costs. Frustrated by the lack of an adequate solution to address this issue, I decided to develop the Proxmox Rebalancing Service (PRS) to ensure better resource distribution across my clusters.

My primary motivation for creating PRS stemmed from my work on my BoxyBSD project, where I consistently faced the difficulty of maintaining balanced nodes while running various VM workloads but also on my personal clusters. The absence of an efficient rebalancing mechanism made it challenging to achieve optimal performance and stability. Recognizing the necessity for a tool that could gather and analyze resource metrics from both the cluster nodes and the running VMs, I embarked on developing PRS.

PRS meticulously collects detailed resource usage data from each node in a Proxmox cluster, including CPU load, memory usage, and local disk space utilization. It also gathers comprehensive statistics from all running VMs, providing a granular understanding of the workload distribution. With this data, PRS intelligently redistributes VMs based on memory usage, local disk usage, and CPU usage. This ensures that no single node is overburdened, storage resources are evenly distributed, and the computational load is balanced, enhancing overall cluster performance.

As an advocate of the open-source philosophy, I believe in the power of community and collaboration. By sharing solutions like PRS, I aim to contribute to the collective knowledge and tools available to developers facing similar challenges. Open source fosters innovation, transparency, and mutual support, enabling developers to build on each other's work and create better solutions together.

Developing PRS was driven by a desire to solve a real problem I faced in my projects. However, the spirit behind this effort was to provide a valuable resource to the community. By open-sourcing PRS, I hope to help other developers manage their clusters more efficiently, optimize their resource usage, and reduce operational costs. Sharing this solution aligns with the core principles of open source, where the goal is not only to solve individual problems but also to contribute to the broader ecosystem.

## References
Here you can find some overviews of references for and about the Proxmox Rebalancing Service.

| Description | Link |
|------|:------:|
| General introduction post to Proxmox Rebalancing Service | https://gyptazy.ch/blog/proxmox-rebalancing-service-rebalance-virtual-machines-across-nodes-in-a-cluster/ |
| Howto install and use the Proxmox Rebalancing Service | https://gyptazy.ch/howtos/howto-install-and-use-the-proxmox-rebalancing-service-to-rebalance-resources-in-a-proxmox-cluster/ |

## Packages
Ready to use packages can be found at:
* https://cdn.gyptazy.ch/files/amd64/debian/proxmox-rebalancing-service/
* https://cdn.gyptazy.ch/files/amd64/ubuntu/proxmox-rebalancing-service/
* https://cdn.gyptazy.ch/files/amd64/redhat/proxmox-rebalancing-service/
* https://cdn.gyptazy.ch/files/amd64/freebsd/proxmox-rebalancing-service/

## Misc
### Bugs
Bugs can be reported via the GitHub issue tracker [here](https://github.com/gyptazy/proxmox-rebalancing-service/issues). You may also report bugs via email or deliver PRs to fix them on your own. Therefore, you might also see the contributing chapter.

### Contributing
Feel free to add further documentation, to adjust already existing one or to contribute with code. Please take care about the style guide and naming conventions.

### Author(s)
 * Florian Paul Azim Hoberg @gyptazy (https://gyptazy.ch)
