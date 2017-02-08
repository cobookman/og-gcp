+++
title = "Networking Overview"
draft = false
categories = ["Networking"]

[owner]
  email = "bookman@google.com"
  name = "Colin Bookman"
+++

Google's private global fiber network provides fast communication
between compute resources and clients. This doc provides an
overview of GCPs networking products.
<!--more-->

# Network Throughput [[1]](#references)

Outbound or egress traffic from a virtual machine is subject to maximum network egress throughput caps. These caps are dependent on the number of cores that a virtual machine instance has. 

Each core (1 VCPU) is subject to a 2 Gibits/second cap for peak performance. A VM can have a theoretical maximum of 16Gibps of network throughput. 

For workloads and benchmarks that the Compute Engine team [regularly measure](https://cloud.google.com/compute/docs/networking#measurenetworkthroughput):

*   A single stream performance can achieve 8.5 Gbps on average.
*   A multistream performance can achieve 15 Gbps on average.

# Subnet Network [[5]](#references)

The default network for a new project is an auto subnet network. You can create up to four additional networks in a project. Additional networks can be auto subnet networks, custom subnet networks, or legacy networks.

A subnet network divides your global network in to regional subnets, each with its own IPv4 prefix. Subnetwork networks are regional in scope. Subnets in a network do not have to be contiguous. For example, one subnet in a network can have a range of 10.240.0.0/16 and another can have a range of 192.168.0.0/16. Each subnetwork has an IP range and gateway address. A subnetwork belongs to only one network and each instance can only belong to one subnetwork.

{{< image src="/images/networking/subnet.png" height="617" width="957" alt="picture of a subnet" link="true" >}}

# Firewalls [[1]](#references)

Each network has its own firewall controlling access to the instances.

All traffic to instances, even from other instances, is blocked by the firewall unless firewall rules are created to allow it.

Firewall rules are only "allow" rules. You cannot create "deny" rules. If you need to restrict traffic from reaching certain instances, create rules that allow traffic to the other instances, then remove the firewall rule that allowed traffic to all of the instances.

The firewall rules automatically created for the `default` network are as follows:


*   `default-allow-internal`
    *   Allows network connections of any protocol and port between instances on the network.

*   `default-allow-ssh`
    *   Allows SSH connections from any source to any instance on the network over TCP port 22.

*   `default-allow-rdp`
    *   Allows RDP (Remote Desktop ) connections from any source to any instance on the network over TCP port 3389.

*   `default-allow-icmp`
    *   Allows ICMP (ping requests) traffic from any source to any instance on the network.

# Internal DNS [[5]](#references)

An internal fully qualified domain name (FQDN) for an instance is in the format:

```
HostName.c.[PROJECT_ID].internal
```

For example a VM with the hostname of `my-vm` in project `my-project` has the internal FQDN of:

```
my-vm.c.my-project.internal
```


# Google Cloud Load Balancer (GCLB) [[2]](#references)[[3]](#references)[[6]](#references)

A Load Balancer distributes incoming network traffic among a pool(s) of compute resources.  The GCLB uses software defined routing to allow for load balancing across regions and zones. This means that when clients hit the load balancer's ip address, the load balancer will route request to the closest available compute resource to the user through a set of rules. The GCLB can also [autoscale GCE VMs](https://docs.google.com/document/d/1VV44g6cYoGIxTFILZJC2h2Z2MzTeMgl4P6XEHWPHsyM/edit#heading=h.u0jtsnxaphjf) to meet needs.

Both the Layer 7 HTTP(S) Load Balancer and Layer 4 TCP/UDP Load Balancer are global load balancers, meaning that a single anycast IP front-ends all your backend instances in regions around the world. Requests are always routed to the instance group that is closest to the user, provided that group has enough capacity and is appropriate for the request. If the closest group does not have enough capacity, the request is sent to the closest group that does have capacity.

## Layer 7 / HTTP(S) Load Balancer [[2]](#references)

A HTTP(S) load balancer routes traffic using rules based on the URL. It will send some URLs to one set of instances, and route other URLs to other instances.

This load balancer also acts as a HTTP/2 to HTTP/1.1 translation layer, which means that web servers always see and respond to HTTP/1.1 requests, but that requests from the browser can be HTTP/1.0, HTTP/1.1, or HTTP/2.

HTTP(S) load balancing does not support WebSockets, however it does support[ HTTP/2 Server Sent Events (SSE)](https://www.w3.org/TR/eventsource/).

An HTTPS load balancer will terminate [client SSL sessions at the load balancer](https://cloud.google.com/compute/docs/load-balancing/http/ssl-certificates). Sessions between the load balancer and compute resource can either be HTTPS or HTTP.  For HTTPS sessions between the GCLB and Compute Instance, each compute instance must have a SSL certificate.

## Layer 4 / TCP & UDP Load Balancer [[3]](#references)

The Layer 4 / TCP & UDP Load Balancer forwards raw TCP / UDP packets to the closest available compute instance. This means that the Layer 4 Load Balancer can be used to route any kind of TCP or UDP network traffic, be it HTTP(s), FTP, XMPP, SSH, Websockets, ICMP, NTP, NFS,..etc where-as the HTTP(S) load balancer can only route HTTP(s) traffic.

In the case of HTTPS traffic the Layer 4 Load Balancer will never inspect the request and only forwards it giving true end to end encryption from the client to compute instance, where-as the layer 7 load balancer decrypts and re-encrypts all HTTPS requests.

## How the GCLB is Different [[4]](#references)[[6]](#references)

The GCLB will scale from zero to greater than 1 million qps with no pre-warming needed. It will distribute compute resources in single or multiple regions, and choose the closest compute resource to your users while meeting high availability requirements. The GCLB can give a single anycast IP, and increase or decrease Compute VMs with intelligent autoscaling.

The GCLBs provide automatic multi-region failover which gently moves traffic if backends become unhealthy. Compared to DNS based Global Load Balancing solutions, GCLBs react instantaneously to changes in users, traffic, networking, backend health, and other related conditions.

# Cloud VPN [[7]](#references)[[8]](#references)

Cloud VPN is a managed IPsec VPN service. It can be used to connect two different GCP networks / regions, or to connect GCP with an external private network across the public internet. Traffic traveling between the two networks is encrypted by one VPN gateway, then decrypted by the other VPN gateway protecting your data as it travels over the Internet.

For example look at the image below. Both 10.21.0.0/16 and 10.0.1.0/24 are private ip address and are not accessable on the public internet. The VPN connection between Google VPN Gateway and Peer Gateway allows the 10.21.0.0/16 network on GCP to access Peer Network's 10.0.1.0/24 through the VPN connection. 


{{< image src="/images/networking/vpn.png" width="915" height="349" alt="Picture of a VPN connection" link="true" >}}


# Ingress & Egress

## Difference between Ingress and Egress [[9]](#references)

**Ingress** traffic is coming **in**. This is `upload` related traffic. An example of ingress is uploading a movie to youtube.

**Egress **traffic is **e**xiting. This is `download` related traffic. An example of egress is viewing a youtube video.

## How it's priced [[9]](#references)

GCP does **not** charge for ingress traffic, egress traffic within the same compute zone, to different GCP services in the same region, or to Google products (such-as Youtube, Maps, Drive).

GCP **does **charge for egress traffic between zones and between regions.

Each service has their own respective egress pricing. For reference here is [Compute Engine's](https://cloud.google.com/compute/pricing#general), [Cloud Storage's](https://cloud.google.com/storage/pricing#network-pricing), and [App Engine's](https://cloud.google.com/appengine/pricing#Billable_Resource_Unit_Costs) egress pricing.

## Interconnect [[10]](#references)

Cloud Interconnect enables you to connect your infrastructure to Google, via an enterprise [ISP service provider](https://cloud.google.com/interconnect/docs#cloud_interconnect_service_providers). Connecting with Cloud Interconnect will enable your infrastructure to connect to Google Cloud Platform with a higher availability, and lower latency. Interconnect also discounts Egress Traffic costs within the region you are connected to. With **Interconnect** you still need to **pay** your **ISP's** fees.

In the image below, the customer's Data Center (DC) is peering with the us-west region.

Traffic from us-west to the customer's DC is discounted, whereas traffic from us-east to the customer's DC is at normal egress pricing. Traffic from us-east to us-west GCP regions are at the normal egress pricing. Traffic within us-west or within us-east is the same normal intra-region (within a region) pricing.

{{< image src="/images/networking/interconnect.png" width="624" height="274" alt="picture of a subnet" link="true" >}}

## Direct Peering [11] {#direct-peering-[11]}

If you are going to have a significant amount of ingress or egress to GCP you'd probably want to directly peering with Google. Any GCP customer that meets Google's technical peering requirements specified in [our peering page](https://peering.google.com/#/options/peering) can be considered for the direct peering service.

Direct Peering differs from interconnect in that you become the ISP and purchase / maintain the peering equipment, avoiding all ISP transit fees / costs. Direct peering has the same high availability, lower latency, and egress cost benefits as interconnect. 


{{< image src="/images/networking/direct_peering.png" height="115px" width="459px" alt="Diagram of direct peering" link="true" >}}

# How GCP's Network is Different

## Points of Presence and Software Defined Networking

Google uses software defined networking. This allows us to route a cloud client to the closest node, and quickly get the client's connection on Google's private fiber network.

For example assume you had a cluster of servers in the us-west1-a zone. And a user in Japan makes a request to this cluster. That customer will have their network connection be routed to Google's Network Peering Point of Presence (POP) in Tokyo. Then the request request will travel over Google's private undersea fiber line from Japan Directly to Google's data center in Oregon. Then its routed over private fiber from the Oregon datacenter directly to us-west1-a zone. This is significantly faster than traveling from japan to us-west1-a over the public internet where the request would travel through more hops and more miles of fiber.

## Noisey Neighbor Prevention [[1]](#references)

As mentioned above, for each vCPU in a VM you get 2Gibit/s of throughput to a maximum of 16Gibits/s.  Single streams are capped at around 8.5Gibit/s. These limits ensure that every VM on a host has equal bandwidth and stops VMs from monopolizing resources.

## Encryption of Data [[12]](#references)[[13]](#references)

Data at rest is encrypted with AES-256, and all network traffic traveling between Google's datacenters and on Google's private fiber is encrypted. All of GCP's APIs and managed services use TLS.

## No Warmup [[4]](#references)

The GCLB will real-time scale and can go from 0 to 1 million requests per second without warming up. The GCLB is also global and can route traffic across all of GCP's regions and is able to withstand the failure of multiple regions / zones.

# Related Technologies

AWS VPC

AWS DirectConnect

AWS Load Balancers

AWS CLB

AWS ELB

AWS Auto Scaling

# References {#references}

[1] https://cloud.google.com/compute/docs/networks-and-firewalls#egress_throughput_caps

[2] https://cloud.google.com/compute/docs/load-balancing/http/

[3] https://cloud.google.com/compute/docs/load-balancing/network/

[4] https://cloud.google.com/load-balancing/

[5] https://cloud.google.com/compute/docs/networking#subnet_network

[6] https://cloud.google.com/load-balancing/

[7] https://cloud.google.com/compute/docs/vpn/overview

[8] https://en.wikipedia.org/wiki/Virtual_private_network

[9] https://cloud.google.com/compute/pricing#network

[10] https://cloud.google.com/interconnect/docs

[11] https://cloud.google.com/interconnect/direct-peering

[12] https://cloud.google.com/security/encryption-at-rest/

[13] https://cloud.google.com/security/whitepaper
