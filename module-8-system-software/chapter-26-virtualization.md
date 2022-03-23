# Chapter 26: Virtualization

## Virtual Machines and Containers

In virtual machines is where a complete operating system is installed on virtual hardware using a hypervisor to manage processes.

{% hint style="danger" %}
The hypervisor is software that is installed on top of hardware, such as server and acting as a platform for VMs to be created on.
{% endhint %}

The hypervisor creates virtual resources such as CPUs, memory, hard disk and I/O communication interfaces like network and serial ports.

![](<../.gitbook/assets/image (10).png>)

There are 2 methods of installning virtualization on a computer system:

* Type 1 hypervisor - also called **bare-metal hypervisor,** directly on top of the hardware and below the virtual machine.
* Type 2 hypervisotr - **Hosted virtualization ,**needs to be installed on top of the operating system that already exists.

![](<../.gitbook/assets/image (4).png>)

{% hint style="info" %}
One of the main benefits of **virtualization** is that it makes **compunting resources more efficient.**
{% endhint %}

Linux Containers allow system designers to bypass traditional operating systems and access computign resources differently. A containerized application relies on a container engine to communicate with the host OS without a hypervisor, or a guest VM.

![](<../.gitbook/assets/image (20).png>)

Doing this provides several advantages, including requiring fewer resources, cutting release cycle times, and abstracting program elements from host operating systems for better portability.

* Use fewer physical resources than VMs.
* offer enchanced reliability and scability.
* Requires less storage
* Best performance in startup time and application

## Virtualization Options

### VMware

Their vSphere and ESXi hypervisor platforms are nearly ubiquitous across industries where customers value stability and support.

### KVM

The Xen project is another open source hypervisor. Is the most frequently used with Linux, it's not tied to any one OS. Xen is currently the only **bare-metal hypervisor** offered as open-source software and is a Linux Foundation project.

### Hyper-V

**Hyper-V** is a hardware virtualization product offered by Microsoft.&#x20;

### VirtualBox

**VirtualBox** is a popular open source product from Oracle that is relatively easy to install and configure. It is a Type 2 hypervisor that runs on Windows, Macintosh, and Linux hosts.

### Docker

Docker is the container engine that allows programmers and system engineers to create containerizes applications. These applications are stand-alone components that do not rely on any host OS to peform their functions.

### Kubernetes

Developed by Google, was converted to an open source project in 2014. Kubernetes can be described as a plataform that "provides a container-centric management environment" for containers, microservices and cloud infrastructure.

## VM and Container Configuration

One factor that needs to be managed is the unique id of a system, or uuid. This uuid should be generated and added to the D-Bus machine ID configuration file during installation or when first booting the VM or container. The `dbus-uuidgen` command can be used to generate a unique ID for a system. It takes a form of a 128-bit number that is unique.

The `cloud-init` utility can be used to automate initial configuration parameters on a Linux virtual machine.

Other critical settings to verify to ensure virtualized products, such as VMs and containers, function properly are the virtualization extensions, which are effectively hardware support for virtualization that is built into the CPU, such as Intel’s VT-x and AMD’s AMD-V extensions. These settings can be verified on a host system in the `/proc/cpuinfo` file and in the system BIOS. For example, the flags in the `/proc/cpuinfo` file below can be used to determine if Intel’s VT-x and AMD’s AMD-V extensions are installed:

```
sysadmin@localhost:~$ cat /proc/cpuinfo
Output Omitted...
physical id     : 1
siblings        : 8
core id         : 0
cpu cores       : 4
apicid          : 32
initial apicid  : 32
fpu             : yes
fpu_exception   : yes
cpuid level     : 11
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca
cmov
pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdt scp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf pni pclmulqdq dtes64 monitor ds_cplvmxsmx est tm2 ssse3 cx16 xtprpdcm pcid dca sse4_1 sse4_2 popcnt aes lahf_lm tpr_shadow vnmi flexpriority ept vpid dtherm ida arat
bugs            :
bogomips        : 4787.47
clflush size    : 64
```

In the output above, in the `flags` field, the `lm` flag means that the system has a 64-bit CPU and the `vmx` flag means that the Intel’s VT-x virtualization extension is enabled in the BIOS.

\
