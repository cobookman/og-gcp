+++
title = "Compute"
draft = false
date = "2017-02-06T16:05:56-08:00"
categories = ["Compute"]

[owner]
  email = "bookman@google.com"
  name = "Colin Bookman"
+++

# Machine Types [1] {#machine-types-[1]}

A machine type specifies a particular collection of virtualized hardware resources available to a virtual machine (VM) instance, including the memory size, virtual CPU count, and maximum persistent disk capability.

To create a VM instance, see [Creating and Starting an Instance](https://cloud.google.com/compute/docs/instances/creating-and-starting-an-instance). For the prices of machine types, see [Machine type pricing](https://cloud.google.com/compute/pricing#machinetype).

## Predefined Machine Types {#predefined-machine-types}

Predefined machine types have a fixed collection of resources. To define your own machine type, see [Custom machine types](https://cloud.google.com/compute/docs/machine-types#custom_machine_types).

Predefined machine types are managed by Google Compute Engine and come in four classes, Standard, High Memory, High CPU, and Shared Core. To view a list of all available machine types, run the following command:


```
$ gcloud compute machine-types list
```


### Standard Machine {#standard-machine}

Standard machine types are suitable for tasks that have a balance of CPU and memory needs. Standard machine types have 3.75 GB of RAM per virtual CPU.

### High Memory {#high-memory}

High-memory machine types are ideal for tasks that require more memory relative to virtual CPUs. High-memory machine types have 6.50GB of RAM per virtual CPU.

### High CPU Machine Types {#high-cpu-machine-types}

High-CPU machine types are ideal for tasks that require more virtual CPUs relative to memory. High-CPU machine types have 0.90 GB of RAM per virtual CPU.

### Shared-core Machine Types {#shared-core-machine-types}

Shared-core machine types provide one virtual CPU that is allowed to run for a portion of the time on a single hardware hyper-thread on the host CPU running your instance. Shared-core instances can be more cost-effective for running small, non-resource intensive applications than standard, high- memory or high-CPU machine types.

## Custom Machine Types {#custom-machine-types}

If none of the predefined machine types match your needs, you can independently specify the number of vCPUs and the amount of memory for your instance.

Custom machine types are ideal for the following scenarios:



*   Workloads that are not a good fit for the predefined machine types that are available to you.
*   Workloads that require more processing power or more memory, but don't need all of the upgrades that are provided by the next larger predefined machine type.

It costs slightly more to use a custom machine type than an equivalent predefined machine type, and there are still some limitations in the amount of memory and vCPUs you can select. Read [Creating an Instance with a Custom Machine Type](https://cloud.google.com/compute/docs/instances/creating-instance-with-custom-machine-type) for all the details.

# Storage [2] {#storage-[2]}

## Persistent Disk {#persistent-disk}

Persistent disks are durable storage devices that function similarly to the physical disks in a desktop or a server. Compute Engine manages the hardware behind these devices to ensure data redundancy and optimize performance for you.

Persistent disks are located independently from your virtual machine instances, so you can detach or move persistent disks to keep your data even after you delete your instances. Persistent disk performance scales automatically with size, so you can resize your existing persistent disks or add more persistent disks to an instance to meet your performance and storage space requirements.

[Add a persistent disk to your instance](https://cloud.google.com/compute/docs/disks/add-persistent-disk#create_disk) when you need reliable and affordable storage with consistent performance characteristics.

### Ease of use {#ease-of-use}

Compute Engine handles most disk management tasks for you so that you do not need to deal with partitioning, redundant disk arrays, or subvolume management. You can apply these practices to your persistent disks if you want, but you can save time and get the best performance if you [format your persistent disks](https://cloud.google.com/compute/docs/disks/add-persistent-disk#formatting) with a single file system and no partition tables. If you need to separate your data into multiple unique volumes, [create additional disks](https://cloud.google.com/compute/docs/disks/add-persistent-disk#create_disk) rather than dividing your existing disks into multiple partitions.

When you require additional space on your persistent disks, simply [resize your disks](https://cloud.google.com/compute/docs/disks/add-persistent-disk#resize_pd) and [resize your single file system](https://cloud.google.com/compute/docs/disks/add-persistent-disk#resize_partitions) rather than repartitioning and formatting.

### Performance {#performance}

Persistent disks are available as [standard hard disk drives (HDD)](https://en.wikipedia.org/wiki/Hard_disk_drive) or [solid-state drives (SSD)](https://en.wikipedia.org/wiki/Solid-state_drive). Standard HDD persistent disks are efficient and economical for handling sequential read/write operations, but are not optimized to handle high rates of random input/output operations per second (IOPS). If your applications require high rates of random IOPS, use SSD persistent disks.

Compute Engine optimizes performance and scaling on persistent disks automatically. You do not need to stripe multiple disks together or pre-warm disks to get the best performance. When you need more disk space or better performance, simply [resize your disks](https://cloud.google.com/compute/docs/disks/add-persistent-disk#resize_pd) to add more storage space, throughput, and IOPS.

Performance on persistent disks is predictable and scales linearly. See [Optimizing Persistent Disk and Local SSD Performance](https://cloud.google.com/compute/docs/disks/performance) for more information about performance scaling limits and optimization.

Each persistent disk write operation contributes to the cumulative network egress traffic for your instance. This means that persistent disk write operations are capped by the [network egress cap](https://cloud.google.com/compute/docs/disks/performance#egress_performance_cap) for your instance.

### Reliability {#reliability}

Persistent disks have built-in redundancy to protect your data against equipment failure and to ensure data availability through datacenter maintenance events. Checksums are calculated for all persistent disk operations so we can ensure that what you read is what you wrote.

Additionally, you can [create snapshots of persistent disks](https://cloud.google.com/compute/docs/disks/create-snapshots) to protect against data loss due to user error. Snapshots are incremental, and take only minutes to create even if you snapshot disks that are attached to running instances.

### Encryption {#encryption}

Compute Engine automatically encrypts your data before it travels outside of your instance to persistent disk storage space. You do not need to encrypt files before you write them to persistent disks.

If you want to control the encryption keys that are used to encrypt your data, [create your disks with your own encryption keys](https://cloud.google.com/compute/docs/disks/customer-supplied-encryption).

### Limits {#limits}

Each persistent disk can be up to 64 TB in size, so there is no need to manage arrays of disks to create large logical volumes. Each instance can attach only a limited amount of total persistent disk space and a limited number of individual persistent disks. [Predefined machine types](https://cloud.google.com/compute/docs/machine-types#predefined_machine_types) and [custom machine types](https://cloud.google.com/compute/docs/machine-types#custom_machine_types) have the same persistent disk limits.

Most instances can have up to 64 TB of total persistent disk space attached.[Shared-core machine types](https://cloud.google.com/compute/docs/machine-types#sharedcore) or [custom machine types](https://cloud.google.com/compute/docs/machine-types#custom_machine_types) with less than 3.75 GB of memory are limited to 3 TB of total persistent disk space. Total persistent disk space for an instance includes the size of the root persistent disk.

## Local SSDs {#local-ssds}

Local SSDs are physically attached to the server that hosts your virtual machine instance. Local SSDs have higher throughput and lower latency than standard persistent disks or SSD persistent disks. The data that you store on a local SSD persists only until you stop or delete the instance.

Each local SSD is 375 GB in size, but you can attach up to eight local SSD devices per instance for 3 TB of total local SSD storage space.

[Create an instance with local SSDs](https://cloud.google.com/compute/docs/disks/local-ssd#create_local_ssd) when you need a fast scratch disk or cache and do not want to use instance memory. Also use local SSDs when your workload itself is replicated across multiple instances.

### Performance {#performance}

Local SSDs are designed to offer very high IOPS and low latency. Unlike persistent disks, you must manage the striping on local SSDs yourself. [Combine multiple local SSD devices into a single logical volume](https://cloud.google.com/compute/docs/disks/local-ssd#formatmultiple) to achieve the best local SSD performance per instance, or [format local SSD devices individually](https://cloud.google.com/compute/docs/disks/local-ssd#formatmultiple).

Local SSD performance depends heavily on which interface you select. Local SSDs are available through both [SCSI](http://en.wikipedia.org/wiki/SCSI) and [NVMe](http://en.wikipedia.org/wiki/NVM_Express) interfaces. If you choose to use NVMe, you must use a special NVMe-enabled image to achieve the best performance. For more information, see [Choosing a disk interface type](https://cloud.google.com/compute/docs/disks/local-ssd#choose_an_interface).

### Encryption {#encryption}

Compute Engine automatically encrypts your data before it travels outside of your instance to local SSD storage space. You do not need to encrypt files before you write them to local SSDs.

If you want to control the encryption keys that are used to encrypt your data, [create your local SSDs with your own encryption keys](https://cloud.google.com/compute/docs/disks/customer-supplied-encryption).

### Data persistence on local SSDs {#data-persistence-on-local-ssds}

Data on local SSDs persists as long as your instance exists and continues to run. Data persists on local SSDs if you restart your instance, but not if you stop your instance.

Data on your local SSDs persists through [live migration](https://cloud.google.com/compute/docs/instances/setting-instance-scheduling-options#live_migrate) events. If Compute Engine migrates an instance with a local SSD, Compute Engine copies data from your local SSD to the new instance with only a short period of decreased performance.

### Limits {#limits}

You can create instances with up to eight 375 GB local SSD partitions for 3 TB of local SSD space for each instance. However, instances are limited to only four partitions for 1.5 TB of total SSD space in the following zones:



*   us-central1-a
*   europe-west1-b

Performance for local SSDs scales up until you reach a total Local SSD storage space of 1.5 TB. Beyond 1.5 TB, throughput and IOPS does not increase.

Instances with [shared-core](https://cloud.google.com/compute/docs/machine-types#sharedcore) machine types cannot attach any local SSD devices.

## Disk Type Performance Overview [3] [6] {#disk-type-performance-overview-[3]-[6]}

For an up-to-date chart please refer to the following google cloud docs [page](https://cloud.google.com/compute/docs/disks/performance#type_comparison).

Disk Throughput is capped by the number of VCPUs and the size of the disk. For example a 1 VCPU machine with a 1 TiB standard persistent disk can only write 78 MiB/s.

Disk throughput is the lowest of either the network egress cap or disk size throughput cap.

### Write Throughput & IOPs Caps in relation to Disk Size [3] {#write-throughput-&-iops-caps-in-relation-to-disk-size-[3]}

For small reads and writes, the limiting factor is random input/output operations per second [IOPS](http://en.wikipedia.org/wiki/IOPS). For large reads and writes, the limiting factor is [throughput](http://en.wikipedia.org/wiki/Throughput). 


<table>
  <tr>
   <td>
   </td>
   <td><strong>Standard persistent disks</strong>
   </td>
   <td><strong>SSD persistent disks</strong>
   </td>
   <td><strong>Local SSD (SCSI)</strong>
   </td>
   <td><strong>Local SSD (NVMe)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Maximum sustained IOPS</strong>
   </td>
   <td>
   </td>
   <td>
   </td>
   <td>
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td><strong>Read IOPS per GiB</strong>
   </td>
   <td>0.75
   </td>
   <td>30
   </td>
   <td>266.7
   </td>
   <td>453.3
   </td>
  </tr>
  <tr>
   <td><strong>Write IOPS per GiB</strong>
   </td>
   <td>1.5
   </td>
   <td>30
   </td>
   <td>186.7
   </td>
   <td>240
   </td>
  </tr>
  <tr>
   <td><strong>Read IOPS per instance</strong>
   </td>
   <td>3000
   </td>
   <td>25,000
   </td>
   <td>400,000
   </td>
   <td>680,000
   </td>
  </tr>
  <tr>
   <td><strong>Write IOPS per instance</strong>
   </td>
   <td>15,000
   </td>
   <td>25,000
   </td>
   <td>280,000
   </td>
   <td>360,000
   </td>
  </tr>
  <tr>
   <td><strong>Maximum sustained throughput (MiB/s)</strong>
   </td>
   <td>
   </td>
   <td>
   </td>
   <td>
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td><strong>Read throughput per GiB</strong>
   </td>
   <td>0.12
   </td>
   <td>0.48
   </td>
   <td>1.04
   </td>
   <td>1.77
   </td>
  </tr>
  <tr>
   <td><strong>Write throughput per GiB</strong>
   </td>
   <td>0.12
   </td>
   <td>0.48
   </td>
   <td>0.73
   </td>
   <td>0.94
   </td>
  </tr>
  <tr>
   <td><strong>Read throughput per instance</strong>
   </td>
   <td>180
   </td>
   <td>240
   </td>
   <td>1,560
   </td>
   <td>2,650
   </td>
  </tr>
  <tr>
   <td><strong>Write throughput per instance</strong>
   </td>
   <td>120
   </td>
   <td>240
   </td>
   <td>1,090
   </td>
   <td>1,400
   </td>
  </tr>
</table>


### Write Throughput in Relation to Network Egress Caps [6] {#write-throughput-in-relation-to-network-egress-caps-[6]}

Persistent disks have a 3.3x data redundancy. Each write is written 3.3 times. This means that a single write operation counts as 3.3 IOPS. The following figures are the persistent disk I/O caps per virtual machine instance, based on the network egress caps for the virtual machine. These figures are based on an instance that has no additional IP traffic.


<table>
  <tr>
   <td>
   </td>
   <td><strong>Standard persistent disk</strong>
   </td>
   <td>
   </td>
   <td><strong>Solid-state persistent disks</strong>
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td><strong>Number of cores</strong>
   </td>
   <td><strong>Standard persistent disk write limit (MiB/s)</strong>
   </td>
   <td><strong>Standard volume size needed to  reach throughput  limit (GiB)</strong>
   </td>
   <td><strong>SSD persistent disk write limit (MiB/s)</strong>
   </td>
   <td><strong>SSD persistent disk size needed to reach throughput limit (GiB)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>1</strong>
   </td>
   <td>78
   </td>
   <td>650
   </td>
   <td>78
   </td>
   <td>163
   </td>
  </tr>
  <tr>
   <td><strong>2</strong>
   </td>
   <td>120
   </td>
   <td>1300
   </td>
   <td>156
   </td>
   <td>326
   </td>
  </tr>
  <tr>
   <td><strong>4</strong>
   </td>
   <td>120
   </td>
   <td>1333
   </td>
   <td>240
   </td>
   <td>500
   </td>
  </tr>
  <tr>
   <td><strong>8</strong>
   </td>
   <td>120
   </td>
   <td>1333
   </td>
   <td>240
   </td>
   <td>500
   </td>
  </tr>
  <tr>
   <td><strong>16</strong>
   </td>
   <td>120
   </td>
   <td>1333
   </td>
   <td>240
   </td>
   <td>500
   </td>
  </tr>
  <tr>
   <td><strong>32</strong>
   </td>
   <td>120
   </td>
   <td>1333
   </td>
   <td>240
   </td>
   <td>500
   </td>
  </tr>
</table>


To derive the figures above. 1 VCPU gives you 2Gibit/s = 256MiB/s. Then there's a 3.3x data redundancy overhead, giving a maximum write I/O for one core of 256/3.3 = ~78MiB/s of I/O.

# Instance Groups [4] {#instance-groups-[4]}

Compute Engine offers two different types of instance groups: [managed](https://docs.google.com/document/d/1VV44g6cYoGIxTFILZJC2h2Z2MzTeMgl4P6XEHWPHsyM/edit#heading=h.5natc6m0n3lt) and [unmanaged](https://docs.google.com/document/d/1VV44g6cYoGIxTFILZJC2h2Z2MzTeMgl4P6XEHWPHsyM/edit#heading=h.ka6f4fml4o4z) instance groups. These allow one to not have to individually control each instance in your project. 

## Managed Instance Groups {#managed-instance-groups}

A managed instance group uses an instance template to create identical instances. You control a managed instance group as a single entity. If you wanted to make changes to instances that are part of a managed instance group, you would apply the change to the whole instance group. Because managed instance groups contain identical instances, they also offer the following features over unmanaged instance groups:



*   When your applications require additional compute resources, managed instance groups can [automatically scale](https://cloud.google.com/compute/docs/autoscaler) the number of instances in the group.
*   Managed instance groups work with [load balancing](https://cloud.google.com/compute/docs/load-balancing) services to distribute network traffic to all of the instances in the group.
*   If an instance in the group stops, crashes, or is deleted by an action other than the instance groups commands, the managed instance group automatically recreates the instance so it can resume its processing tasks. The recreated instance uses the same name and the same [instance template](https://cloud.google.com/compute/docs/instance-templates) as the previous instance, even if the group references a different instance template.
*   Managed instance groups can automatically [identify and recreate unhealthy instances in a group](https://cloud.google.com/compute/docs/instance-groups/creating-groups-of-managed-instances#monitoring_groups) to ensure that all of the instances are running optimally.

You can create a zonal managed instance group, which contains instances from the same zone, or regional managed instance groups, which contains instances from multiple zones across the same region.

### Zonal versus Regional Instance Groups {#zonal-versus-regional-instance-groups}

It is possible to create a managed instance group with only instances from a single zone, or you can create a regional managed instance group, which contains instances spread across multiple zones in a single region.

To design a robust application, Google recommends using regional managed instance groups so that your application can handle extreme scenarios such as a single zone failure. 

### Instance Templates {#instance-templates}

Instance templates define the machine type, image, zone, and other instance properties for the instances in a [managed instance group](https://cloud.google.com/compute/docs/instance-groups/creating-groups-of-managed-instances). Each managed instance group has a single instance template that it uses to create or update the instances that are part of the group. You can create an instance template once and reuse it for multiple API requests, groups, and configurations.

An instance template is a global resource that is not bound to a zone or a region. However, you can still specify some zonal resources in an instance template, which restricts the template to the zone where that resource resides. For example, if you include a read-only persistent disk from us-central1-b in your instance template, you cannot use that template in any other zone because that specific disk exists only in zone us-central1-b. For more information about zonal resources, read [Regions and Zones](https://cloud.google.com/compute/docs/regions-zones/regions-zones).

## Unmanaged Instance Groups {#unmanaged-instance-groups}

Unmanaged instance groups are groups of dissimilar instances that you can arbitrarily add and remove from the group. Unmanaged instance groups do not offer autoscaling, rolling update support, or the use of instance templates. Google recommends to use managed instance groups whenever possible.

If you must create a group of dissimilar instances that do not follow an instance template, see [Unmanaged Instance Groups](https://cloud.google.com/compute/docs/instance-groups/creating-groups-of-unmanaged-instances). Use unmanaged instance groups only if you need to apply load balancing to your pre-existing configurations or to groups of dissimilar instances.

## Instance Groups and Autoscaling {#instance-groups-and-autoscaling}

Managed instance groups support autoscaling. Autoscaling will dynamically increase or decrease the number of instances in a managed instance group in response to increases or decreases in load.

An autoscaling policy determines how you want to scale. These policies can be scale based on CPU utilization, load balancing capacity, Stackdriver monitoring metrics, or by a queue-based workload like Google Cloud Pub/Sub.. 

Because autoscaling requires adding and removing instances from a group, you can only use autoscaling with managed instance groups so the autoscaler can maintain identical instances. Autoscaling does not work on unmanaged instance groups, which can contain heterogeneous instances.

For more information, see the topic on [Autoscaling Groups of Instances](https://cloud.google.com/compute/docs/autoscaler).

# Preemptible VM Instances [5] {#preemptible-vm-instances-[5]}

A preemptible VM is an instance that you can create and run at a much [lower price](https://cloud.google.com/compute/pricing#machinetype) than normal instances.  However, Compute Engine might terminate (preempt) these instances if it requires access to those resources for other tasks. Preemptible instances are excess Compute Engine capacity so their availability varies with usage.

If your applications are fault-tolerant and can withstand possible instance preemptions, then preemptible instances can reduce your Compute Engine costs significantly. For example, batch processing jobs can run on preemptible instances. If some of those instances terminate during processing, the job slows but does not completely stop. 

Preemptible VMs can be part of a managed instance group, and can be behind a load balancer with an autoscaling policy. If a preemptible VM is preempted in a managed instance group, the group repeatedly tries to recreate that instance using the specified instance template until the necessary resources become available again.

## Preemptible VM Limitations {#preemptible-vm-limitations}

Preemptible instances function like normal instances, but have the following limitations:



*   Compute Engine might terminate preemptible instances at any time due to system events. The probability that Compute Engine will terminate a preemptible instance for a system event is generally low, but might vary from day to day and from zone to zone depending on current conditions.
*   Compute Engine always terminates preemptible instances after they run for 24 hours.
*   Preemptible instances are finite Compute Engine resources, so they might not always be available.
*   Preemptible instances cannot [live migrate](https://cloud.google.com/compute/docs/instances/setting-instance-scheduling-options#preemptiblemaintenance) or be set to automatically restart when there is a maintenance event.
*   Due to the above limitations, preemptible instances are not covered by any Service Level Agreement (and, for clarity, are excluded from the [Google Compute Engine SLA](https://cloud.google.com/compute/sla)).

## Preemption Process {#preemption-process}

You can simulate an instance preemption by [stopping the instance](https://cloud.google.com/compute/docs/instances/stopping-or-deleting-an-instance#stop_an_instance). When an instance is preempted, Compute Engine performs the following steps:



1.  Compute Engine sends a preemption notice to the instance in the form of an [ACPI G2 Soft Off](https://en.wikipedia.org/wiki/Advanced_Configuration_and_Power_Interface#Power_states) signal. You can use a [shutdown script](https://cloud.google.com/compute/docs/instances/create-start-preemptible-instance#handle_preemption) to handle the preemption notice and complete cleanup actions before the instance stops.
1.  If the instance does not stop after 30 seconds, Compute Engine sends an [ACPI G3 Mechanical Off](https://en.wikipedia.org/wiki/Advanced_Configuration_and_Power_Interface#Power_states) signal to the operating system.
1.  Compute Engine transitions the instance to a TERMINATED state.

You can access and recover data from any persistent disks that are attached to the instance, but those disks still incur storage charges until you delete them. 

If Compute Engine terminates a preemptible instance less than 10 minutes after it is created, you are not billed for the use of that virtual machine instance. However, the charges for [premium operating systems](https://cloud.google.com/compute/docs/instances/preemptible#preemptible_with_premium_os) are still calculated as normal.

## Preemption Selection {#preemption-selection}

Generally, Compute Engine avoids preempting too many instances from a single customer and will preempt instances that were launched most recently. This might be a bit frustrating at first, but in the long run, this strategy helps minimize lost work across your cluster. Compute Engine doesn't bill for instances preempted in the first 10 minutes, so you also save on costs.

Historical data shows that the average preemption rate varies between 5% and 15% per seven days per project, occasionally spiking higher depending on time and zone. Keep in mind that this is an observation only: preemptible instances have no guarantees or SLAs for preemption rates or preemption distributions.

# How GCE is Different {#how-gce-is-different}

## Noisey Neighbor Prevention {#noisey-neighbor-prevention}

### Networking [3][6] {#networking-[3][6]}

As mentioned in the disk type performance overview section, for each vCPU in a VM you get 2Gibit/s of throughput to a maximum of 16Gibits/s.  Single streams are capped at around 8.5Gibit/s. These limits ensure that every VM on a host has equal bandwidth and stops VMs from monopolizing resources.

### Local Storage [3][6] {#local-storage-[3][6]}

The disk size determines the IOPS, Read, and Write performance, ensuring that no single user can monopolize the disk cluster giving a consistent performance. 

### Custom VM Types [8] {#custom-vm-types-[8]}

Create a [custom machine type](https://cloud.google.com/compute/docs/instances/creating-instance-with-custom-machine-type) with a specific number of vCPUs and amount of memory if predefined machine types are not optimal for your workloads. Custom machine types also save you the cost of running on a larger, more expensive machine type if you don't need to use all the resources of that machine type.

For example, instead of using an n1-standard-8 machine type when you just need a machine type with 6 vCPUs, create a custom machine type instance with 6 vCPUs and 22.5 GB of memory. Creating a custom machine type can save you up to 40% compared to selecting a larger machine type.

### Live Migration [9] {#live-migration-[9]}

What's remarkable about April 7th, 2014 isn't what happened that day. It's what didn't.

That was the day the Heartbleed bug was revealed, and people around the globe scrambled to patch their systems against this zero-day issue, which came with already-proven exploits. In other public cloud platforms, customers were impacted by rolling restarts due to a requirement to reboot VMs. At Google, we quickly rolled out the fix to all our servers, including those that host [Google Compute Engine](https://cloud.google.com/compute/). And none of you, our customers, noticed.

Live migration allows GCP to keep customer VMs up and running as software updates are rolled out, hardware problems fixed, and some unexpected issues that have arisen are resolved. GCP moves customer's running VMs out of the way of planned hardware and software maintenance events, making the infrastructure protected and reliable—without VMs, applications or workloads noticing that anything happened.

### Sustained Usage Discounts [7] {#sustained-usage-discounts-[7]}

If you run an instance for a significant portion of the billing month, you can qualify for a sustained use discount. When you use an instance for more than 25% of a month, Compute Engine automatically gives you a discount for every incremental minute you use for that instance. The discount increases with usage and you can get up to a 30% net discount for instances that run the entire month. Sustained use discounts are calculated and applied to your bill as your project earns them.

### Fast Spin-Up time 

VMs will spin up in about 90 secs on GCP.

### Persistent Disks {#persistent-disks}

Persistent disks can be increased in size without any downtime.

A persistent disk can be mounted as read-only to multiple machines, which allows you to share static data between multiple instances. Sharing static data between multiple instances from one persistent disk is cheaper than replicating your data to unique disks for individual instances.

Persistent Disks can be up to 64TiB in size.

# Labs {#labs}

[Compute the Cosmos with GCE](https://codelabs.developers.google.com/codelabs/cloud-compute-the-cosmos/index.html?index=..%2F..%2Findex#0)

[Deploy ASP.NET app to Windows Server on Compute Engine](https://codelabs.developers.google.com/codelabs/cloud-compute-engine-aspnet/index.html?index=..%2F..%2Findex#0)

[Deploy Windows Server with ASP.NET Framework to Compute Engine](https://codelabs.developers.google.com/codelabs/cloud-create-vm-windows-dotnet/index.html?index=..%2F..%2Findex#0)


# Related Technologies {#related-technologies}

## Virtual Machines {#virtual-machines}

Amazon Elastic Compute Cloud (EC2)

Azure Virtual Machines


## Persistent Disk {#persistent-disk}

Amazon Elastic Block Store (EBS)

Amazon Elastic File System (EFS)

Azure Page Blobs

Azure Premium Storage

Azure File Storage


## Autoscaling / Instance Groups {#autoscaling-instance-groups}

Amazon Auto Scaling

Azure VM Scale Sets

Azure App Service Autoscaling


# References {#references}

[1] https://cloud.google.com/compute/docs/machine-types

[2] https://cloud.google.com/compute/docs/disks/

[3] https://cloud.google.com/compute/docs/disks/performance

[4] https://cloud.google.com/compute/docs/instance-groups/ 

[5] https://cloud.google.com/compute/docs/instances/preemptible

[6] https://cloud.google.com/compute/docs/disks/performance#egress_performance_cap 

[7] https://cloud.google.com/compute/pricing#sustained_use 

[8] https://cloud.google.com/compute/pricing#custommachinetypepricing 

[9] https://cloudplatform.googleblog.com/2015/03/Google-Compute-Engine-uses-Live-Migration-technology-to-service-infrastructure-without-application-downtime.html 

