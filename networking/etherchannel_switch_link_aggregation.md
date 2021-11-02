# EtherChannel Switch Link Aggregation

## What is EtherChannel

As a rule of thumb, network resilience should be your number one priority. In order to achieve High Availability and Uninterrupted Service Continuity you should make sure that you have duplicate hardware devices as well as duplicate interconnection links, where appropriate. We have learned that having multiple connection links between network switches imposes packet loops unless a protection mechanism such as STP is implemented. But we also know that STP disables all redundant links. Therefore, with STP running we could never be able to implement backup interconnection links.

Do not get disappointed, for there is a solution to this problem. The solution is called “EtherChannel”. The idea is to have multiple physical links between switches combined into a single logical link with built-in mechanisms to prevent packet loops. The benefits of aggregating switch links are:

* Increased perceived redundancy level; a single or double link failure between two switches can be immediately overcome.
* Expansion of bandwidth between switches; added bandwidth requirements can be satisfied by bundling interconnection links.

Prerequisites of Enabling EtherChannel Nothing comes without a price. Fortunately the prerequisites for enabling EtherChannel between two switches are simple and easy to accomplish. These are:

* EtherChannel bundle links must be of the same type and speed. For example if we intend to combine FastEthernet links \(100Mbps\) then all bundle links MUST be FastEthernet links of 100Mbps speed.
* All links intended to be bundled MUST NOT exceed the number of eight physical links. Up to eight physical links can be combined into one logical link.
* All links MUST belong to the same VLAN if used as access links. They MUST carry the same VLANS if used as trunk links and MUST be configured with identical STP settings.

## EtherChannel Negotiation Protocols

Link aggregation wouldn’t exist if it wasn’t for Control protocols to negotiate and establish EtherChannel connectivity. On Catalyst switches, two EtherChannel negotiation protocols are supported:

* Port Aggregation Protocol \(PAgP\): Cisco Proprietary protocol; EtherChannel negotiation is carried out on EtherChannel capable ports in order to achieve bundling of physical links.
* Link Aggregation Control Protocol \(LACP\): Standardized protocol defined in IEEE 802.3ad; Similar to PAgP. Nevertheless, LACP, has the following major differences compared to PAgP:

– LACP assigns roles to interconnected switches, so that the master switch is allowed to make decisions on which ports are participating in the EtherChannel at any given time.

– Up to sixteen switch links can be part of the bundle group, though, eight of them can be active at the same time. Activation of a standby links is performed only when an active link goes down.

## EtherChannel Load Balancing Methods

Various methods exist for efficiently distributing traffic load across bundled links. There is no “best method.” The effectiveness of each method relies upon the individual network architecture and the specific traffic architecture. The most important EtherChannel load distribution methods are:

* **Src-dst-ip**: Traffic is distributed to bundled links according to source and destination IP address combination. Therefore, in networks where traffic originates from many different sources destined for the same or different destinations, is distributed in a balance way across aggregated links.
* **Src-dst-mac**: Traffic distribution is based upon source and destination mac-address combination. This method accomplishes fair load distribution only in cases that only Layer 2 switching takes place, and traffic route patterns do not cross router interfaces.
* **Src-dst-port**: In case that we have most of the traffic traversing constant end-devices distributing traffic based on source and destination ports might be the better solution to achieve better load balancing between aggregated links.

In any case, you need to monitor your links and observe how traffic is distributed among them. Based on your observations and considering the pattern of your traffic you should be able to decide upon the optimal loading balance method applicable for you.

## How to Configure a LACP EtherChannel

Configuring a LACP EtherChannel between two interconnected switches is straightforward. You need to perform the following:

* Specify the Switch interfaces to be aggregated by assigning each one of these interfaces to the same channel group number \(a number from 1 to 64\). Also, you need to specify the EtherChannel protocol which in this example is LACP and the channel negotiation type which can basically take one of two values: **passive** \(wait to be asked\) or **active** \(normal behavior\).

  ```text
  interface GigabitEthernet1/0/1
  channel-protocol lacp
  channel-group 1 mode active
  ```

  You should repeat the above commands to all aggregated interfaces on both switches.

* Configure the EtherChannel Load Balancing Method on Global config mode

**port-channel load-balance src-dst-ip**

## Troubleshoot EtherChannel Status

There are various troubleshooting commands to test and verify the behavior of your EtherChannel. The most important commands are presented below:

**Show etherchannel port**

![](../.gitbook/assets/24.jpg)

You should be able to see all aggregated links along with a “Load” value, similar to the following:

**Show EtherChannel port-channel**

![](../.gitbook/assets/33.jpg)

**Show etherchannel load-balance**

The above command can be used to verify the load balancing method. The output will be similar to this:

EtherChannel Load-Balancing Operational State \(src-dst-ip\):

## Good practice

You should incorporate into your monthly routines, monitoring of the EtherChannel links. You need to make sure that you have adequate interconnection bandwidth to accommodate your traffic. Moreover, useful information regarding link utilization could be obtained, thus helping you decide upon appropriate measures to balance this utilization among your links.

