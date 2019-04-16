##############
Smart City Net
##############
Smart cities rely on a robust, yet flexible network foundation to interconnect sensors, people and businesses with cloud-based applications. A city net is the cornerstone of a smart city since all use cases are reliant on it.

City net technical requirements

.. csv-table:: Table 1  City net technical requirements
   :widths: 20, 60

    "Multi-tenancy", "A “city net” provides services to multiple tenants. L3 VPN services are needed for most government agencies, schools and hospitals. L2 VPN services are needed in specific situations such as DC interconnect. IoT devices of different class need to be isolated in their own L2 or L3 container for security."
    "Availability", "Availability is of utmost importance since the city net provides services to multiple agencies and supports multiple use cases. The city net must implement redundancy at all layers to protect from node or link failure and to facilitate in-service maintenance. In the event of a failure, the network must re-converge in under 1 second."
    "Scalability","The city net architecture must be scalable to support the required number of tenants, containers, devices and users as well as bandwidth and multicast flow and more."
    "Simple Operations","The city net can be comprised of thousands of network nodes and IoT devices. Because of its size, template-driven automatic provisioning of nodes and devices is necessary to reduce deployment cost, time and errors."
    "Security","Because a smart city net supports mission-critical infrastructure, security is even more critical than traditional enterprise security. A multi-layered approach is required including security hardening of nodes and devices, network admission control (NAC) and role-based access, protection of data integrity and confidentiality as well as quarantine and remediation among others."
    "Environmental","Smart city net nodes will be deployed in locations such as within a road-side cabinet where they will be subject to extreme temperatures, dust, vibration and shock. The network devices must tolerate such harsh conditions."

Technologies
############

L2/L3 VPN
^^^^^^^^^
The concept of L3 VPNs for government agencies, schools and hospitals is shown in the figure below.
In this diagram, schools, hospitals and other government agencies are tenants of the city net and each have their own L3 VPN. VPN technology ensures all these different tenants can coexist on the same infrastructure and that tenant data is kept private from other tenants. 
In a L3 VPN, tenants exchange routes with the city net and the city net routes tenant data from site to site over the most optimal route without traffic tromboning back and forth to a central site for routing. 
In addition, tenant data is isolated from other tenants and tenant routes are not advertised to other tenants.

.. figure:: /images/l2l3vpn.png
    :width: 600px
    :align: center
    :height: 300px
    :alt: alternate text
    :figclass: align-center

Alcatel-Lucent provide two technologies to build L2/L3 VPN.  The two technologies are MPLS, and SPB.  In this document, we
will cover SPB (Shortest Path Bridging) to build L2/L3 VPN.

MPLS vs SPB
^^^^^^^^^^^
Even since Layer 3 VPN is implemented in SPB, there has been a lot of debate with the use of SPB as an alternative to MPLS.
Below are some of the common arguments we shared with our customers.

.. figure:: /images/mplsvsspb.png
    :width: 1600px
    :align: center
    :height: 180px
    :alt: alternate text
    :figclass: align-center

OmniSwitch SPB
^^^^^^^^^^^^^^
The OmniSwitch implementation of Shortest Path Bridging (SPB) supports SPB MAC (SPBM) as defined in the IEEE 802.1aq standard. 
SPBM is defined for use in Provider Backbone Bridge (PBB) networks as specified in the IEEE 802.1ah standard.
SPBM provides a mechanism to automatically define a shortest path tree (SPT) bridging configuration through a Layer 2 Ethernet network. SPBM Ethernet services use this configuration to encapsulate and tunnel data through the PBB network. The following main components of the OmniSwitch implementation of SPBM provide this type of functionality:

* ISIS-SPB—A version of the Intermediate to Intermediate System (IS-IS) link state protocol that supports SPB TLV extensions. SPBM uses ISIS-SPB to build sets of symmetric shortest path trees (SPTs) between any SPB switch.
* Provider Backbone Bridge (PBB) IEEE 802.1ah— Defines a MAC-in-MAC data encapsulation path for PBB networks that is supported by SPBM.
* Provider Backbone Bridge Network (PBBN)—A network comprised of Backbone Edge Bridges (BEBs) and Backbone Core Bridges (BCB) that is used to interconnect Provider Bridge Networks (PBN) with other networks.
* Backbone Edge Bridge (BEB)—An SPB switch positioned at the edge of the PBB network that learns and encapsulates (adds an 802.1ah backbone header to) customer frames for transport across the backbone network. The BEB interconnects the customer network space with PBB network space.
* Backbone Core Bridge (BCB)—An SPB node that resides inside the PBB network core. The BCB employs the same BVLAN on two or more network ports. This BVLAN does not terminate on the switch itself; traffic ingressing on an SPB network port is switched to other SPB network ports. As a result, the BCB does not have to learn any of the customer MAC addresses. It mainly serves as a transit bridge for the PBB network.
* SPBM Service—An OmniSwitch Service Manager service configured on the BEBs. Each service maps to a service instance identifier (I-SID) which is bound to a backbone VLAN. One backbone VLAN can accommodate multiple I-SIDs.
* Backbone VLAN (BVLAN)—A VLAN that serves as a transport VLAN for the SPBM service instances and to connect SPB bridges together through SPT sets. Unlike standard VLANs, BVLANs do not learn source MAC addresses or flood unknown destination or multicast frames. Instead, BVLANs only forward on the basis of the forwarding database (FDB) as populated by the ISIS-SPB protocol.

SPBM Shortest Path Trees
^^^^^^^^^^^^^^^^^^^^^^^^

The shortest path between two points is a straight line. Shortest Path Bridging (SPB) implements frame forwarding on the shortest path between any two bridges in an Ethernet network. The shortest path trees (SPTs) calculated by SPB provide the shortest and most efficient path to and from the intended destination. SPTs are formed along the direct, straight-line links between switches to make up an overall path through the topology that provides a robust, efficient direction for network traffic to travel.
The SPBM network topology consists of two layers:
* The backbone infrastructure (control plane) layer. ISIS-SPB builds the backbone layer by defining loop-free, shortest path trees (SPTs) through the backbone network.
* The services (data plane) layer. The service layer is based on the Provider Backbone Bridging (PBB) framework as defined in the IEEE 802.1ah standard. SPBM supports the 802.1ah MAC-in-MAC method for data encapsulation.

SPB Services
^^^^^^^^^^^^
The SPBM network topology consists of two layers:

* The backbone infrastructure (control plane) layer. ISIS-SPB builds the backbone layer by defining loop-free, shortest path trees (SPTs) through the backbone network.
* The services (data plane) layer. The service layer is based on the Provider Backbone Bridging (PBB) framework as defined in the IEEE 802.1ah standard. SPBM supports the 802.1ah MAC-in-MAC method for data encapsulation. SPBM services transport the encapsulated traffic over the ISIS-SPB infrastructure.

The SPB service layer framework is comprised of the following components:

* Backbone Edge Bridge (BEB). An OmniSwitch is considered a BEB if the switch is SPB capable and at least one service access point (SAP) and one SPB interface is configured on the switch. The BEB marks the boundary between the customer network and the PBB network (PBBN).
* Backbone Core Bridge (BCB). An OmniSwitch is considered a BCB if the switch is SPB capable and no SAPs are configured but at least one SPB interface is configured on the switch to forward encapsulated SPBM network traffic. Note that the requirement for configuring a BCB is based on whether or not the network topology includes a transit bridge.
* Service Instance Identifier (I-SID). Configured only on a BEB, this component identifies a backbone service instance that will tunnel the encapsulated data traffic through the PBBN between BEBs. The I- SID is bound to a BVLAN ID and a Service Manager SPB service ID when the service is created.
* Access Port. A port or link aggregate configured as an SPB access port. This type of port is configured on the BEBs and defines the point at which traffic from other provider networks or directly from customer networks enters the PBBN. The access port is also associated with a Layer 2 profile that specifies how to process protocol control frames received on the port
* Service Access Point (SAP)—A SAP is a logical service entity (also referred to as a virtual port) that is configured on a BEB to bind an access port to an SPB service ID and specify the type of customer traffic ((untagged, single-tagged, double-tagged, or all) to encapsulate and tunnel through the PBBN.
* SPB Interface (Network Port)—A port or link aggregate configured as an SPB interface that resides on either a BEB or a BCB and connects to the backbone network. Network ports carry customer traffic encapsulated in 802.1ah frames and are associated with all BVLANs on the switch. Customer traffic ingressing on a network port is switched to another network port (on BCBs) or to an access port (on BEBs).

Once the ISIS-SPB infrastructure and the SPB service-based architecture is defined, the following service components are dynamically created by the OmniSwitch. No user-configuration is required.

* Service Distribution Point (SDP)—A SDP provides a logical point at which customer traffic is directed from one BEB to another BEB. SDPs are used to set up distributed services, which consist of at least one SAP on a local node, one SAP on a remote node, and an SDP binding the service on both nodes.
* SDP Bind—An SDP binding represents the binding of an SPB service instance to an SDP. The SDP then distributes the service connectivity to other BEBs through the ISIS-SPB shortest path trees.

IP over SPBM
^^^^^^^^^^^^
The OmniSwitch implementation of SPBM provides L2 VPN capability that bridges L2 customer LAN segments. Customer edge (CE) devices form peers and exchange routing information, as well as perform the necessary IP forwarding. Then the SPBM BEBs bridge the already routed IP traffic across the SPBM backbone.
In addition to L2 VPN, the OmniSwitch also provides an IP over SPBM capability that consolidates the routing functionality of CE devices into the BEB devices. The Virtual Routing and Forwarding (VRF) instances on different BEBs are tied together via backbone I-SIDs across the same SPBM backbone that is used to support Layer 2 VPNs.
The OmniSwitch IP over SPBM solution supports two methods for combining L3 routing and L2 SPBM in the same switch: VPN-Lite and L3 VPN.

L3 VPN
^^^^^^
When the L3 VPN method is implemented, the OmniSwitch acts as an access or edge router to multiple VRFs and connects these VRFs across an SPBM PBB network. Each VPN is identified by a local VRF instance on each BEB and globally in the backbone by an I-SID in the PBB header. ISIS-SPB will import and export routes from the local routing protocols running inside their respective VRFs. In essence, ISIS- SPB is creating tunnels between BEBs through which routed frames are sent to reach their target networks.
The OmniSwitch L3 VPN solution is based on the IETF drafts IP/IPVPN services with IEEE 802.1aq SPB(B) networks and uses IS-IS TLVs to exchange routes between the BEBs that host the same VPN services. This approach also gives an administrator the ability to build VPNs and extend them over an SPBM core.


High level architecture
#######################

Below is a figure that shown the high level architecture of a smart city net.
At the core of the city net is P(MPLS) or BCB(SPB) routers, offering L2/L3 VPNs to the various agencies.
The L2/L3 VPN services are extended to the edge via various access technologies such as Metro-E, GPoN, and SPB.

Topology
^^^^^^^^

.. figure:: /images/hld.png
    :width: 800px
    :align: center
    :height: 300px
    :alt: alternate text
    :figclass: align-left

Below are the products used for building the Smart City Net.

Products
^^^^^^^^

.. figure:: /images/solutions.png
    :width: 800px
    :align: center
    :height: 400px
    :alt: alternate text
    :figclass: align-left

Configure SPB in OmniSwitch
###########################


Configure the SPBM Backbone (ISIS-SPB)

* Create a BVLAN. The BVLAN provides the foundation of the SPBM infrastructure. A BVLAN is associated with an equal cost tree (ECT) algorithm ID and an SPB service instance ID that is used to carry customer traffic through the backbone network.

.. code-block:: console

    -> spb bvlan 4001 name spb-bvlan-4001
    -> spb bvlan 4001 name spb-bvlan-4002



* Configuring the Control BVLAN. One of the BVLANs configured on each switch serves as the control BVLAN for the ISIS-SPB instance. The control BVLAN exchanges ISIS-SPB control packets with neighboring SPB switches on behalf of all BVLANs configured on the local switch. The control packets are tagged with the control BVLAN ID. To designate a BVLAN as the control BVLAN, use the spb isis control-bvlan command. For example:

.. code-block:: console

    -> spb isis control-bvlan 4002

* Configuring an IP Interface on the Control BVLAN. To configure an IP interface on the Control BVLAN to support in-band management access in the SPBM domain, use the ip interface command.

.. code-block:: console

    -> ip interface "spb-mgmt-int" address 10.1.1.1/24 vlan 4002

Only one Control BVLAN can be configured on a switch, and only IPv4 interface is supported. ISIS-SPB is the only protocol supported in the IP BVLAN domain for exchanging or advertising IP routing information. No other routing protocol (including VRRP) is supported.

* Verifying the BVLAN Configuration. To view the BVLAN configuration for the switch, use the show spb isis bvlans command. For example:

.. code-block:: console

    -> show spb isis bvlans
    -> show vlan

* Configure SPB interfaces. An SPB interface is associated with each BVLAN that is configured on the switch. At the ISIS-SPB level, this type of interface sends and receives ISIS Hello packets and link state PDU (LSP) to discover adjacent SPB switches and calculate the shortest path trees through the SPBM network topology. At the services level, the SPB interfaces serve as network ports that are used to carry encapsulated customer traffic through the network

.. code-block:: console

    -> spb isis interface port 1/10         or
    -> spb isis interface linkagg 5
    -> spb isis interface linkagg 5 hello-interval 20 hello-multiplier 5 metric 200
    -> show spb isis interface 

* Configure global ISIS-SPB parameters. In addition to enabling/disabling the ISIS-SPB instance for the switch, global configuration includes settings such as a system name for the switch, global bridge parameters, and various wait time intervals. When ISIS-SPB is enabled for the switch, default settings for these global bridge parameters and wait time intervals are active. It is only necessary to change these values if the default settings are not sufficient.

.. code-block:: console

    -> system name BEB-1
    -> spb isis bridge-priority 25590
    -> spb isis area-address 1.1.1
    -> spb isis source-id 07-0b-d3
    -> spb isis source-id auto
    -> spb isis control-address alll2
    -> spb isis spf-wait max-wait 2500 initial-wait 1000 second-wait 1500
    -> spb isis lsp-wait max-wait 2000 initial-wait 1000 second-wait 1500
    -> spb isis overload
    -> spb isis graceful-restart
    -> spb isis admin-state enable

Create SPB service
^^^^^^^^^^^^^^^^^^

An SPB service is identified by a service ID number, which represents an association between a backbone service instance identifier (I-SID) and an existing BVLAN. Basically, creating an SPB service binds the backbone I-SID to a BVLAN ID. All traffic mapped to the specific I-SID is then encapsulated and forwarded on the associated BVLAN to the intended destination.
The service spb command is used to create an SPB service. For example, the following command creates SPB service 1 and binds I-SID 100 to BVLAN 4001:

.. code-block:: console

    -> service 1 spb isid 500 bvlan 4001 admin-state enable

The BVLAN ID specified with the service spb command must already exist in the switch configuration. However, the I-SID number specified creates a new I-SID that is bound to the BVLAN for this service.

.. note::  

    When adding another BVLAN to an existing SPBM topology instance, create the new BVLAN and its associated ECT ID on every switch first, then configure the SPB service association for the BVLAN. Creating SPB services before the BVLAN configuration is complete on all switches can cause problems with forming adjacencies or may even cause an SPB switch to drop existing adjacencies.

Using VLAN Translation
^^^^^^^^^^^^^^^^^^^^^^

VLAN translation refers to the egress translation of VLAN tags on service access points (SAPs). When enabled for a service, the VLAN tags for outgoing customer frames on SAPs associated with that service are processed according to the local SAP configuration (the SAP on which the frames will egress) and not according to the configuration of the SAP on which the frames were received.

* If the local SAP is configured for untagged traffic (slot/port:0), the egress traffic is always sent out as untagged.
* If the local SAP is configured for 802.1q-tagged traffic (slot/port:ctag), the egress traffic is single- tagged with the tag value specified by the ctag (customer VLAN tag) value.
* If the local SAP is configured for double-tagged traffic (slot/port:outer_tag.: inner_tag), the egress traffic is double-tagged with the tag values specified by the outer_tag and inner_tag values.

When VLAN translation is disabled, frames simply egress without any modification of the VLAN tags. In other words, the frames are transparently bridged without tag modification.
The following table shows the required translation (tag is added or replaced) that takes place when the egress SAP configuration is applied to the possible frame types (untagged, tagged, double-tagged). Note that in this table the terms “ITAG” and “OTAG” refer to inner tag and outer tag, respectively.

.. figure:: /images/saptags.png
    :width: 800px
    :align: center
    :height: 300px
    :alt: alternate text
    :figclass: align-left

Enabling VLAN translation is required at two different levels: first at the access port level and then at the service level. This activates VLAN translation for all of the SAPs on an access port that belong to the same service.
To enable translation at the service level, use the service vlan-xlation command. For example:

.. code-block:: console

    -> service 1 vlan-xlation enable

To enable VLAN translation for all services, use the all parameter with the same command. For example:

.. code-block:: console

    -> service all vlan-xlation enable

To enable VLAN translation at the access port level, use the service access vlan-xlation command. For example:

.. code-block:: console

    -> service access port 1/11 vlan-xlation enable

Enable the Service
^^^^^^^^^^^^^^^^^^

By default, the SPB service is disabled when the service is created. Once the service is created and any optional service parameters are configured, use the service admin-state command with the enable option to enable the service. For example:

.. code-block:: console

    -> service 1 admin-state enable
    -> show service spb

Configure the SAP points
^^^^^^^^^^^^^^^^^^^^^^^^

A SAP identifies the location where customer traffic enters the Provider Backbone Bridge Network (PBBN) edge, the type of customer traffic to service, parameters to apply to the traffic, and the service that will process the traffic for tunneling through the provider network.
Configuring a SAP requires several steps. These steps are outlined here and further described throughout this section:

* Configure customer-facing ports or link aggregates as service access ports.
* Configure Layer 2 profiles to determine how control packets are processed on access ports.
* Create a SAP by associating a SAP ID with an SPB service ID. A SAP ID is comprised of an access port and an encapsulation value, which is used to identify the type of customer traffic (untagged, single-tagged, or double-tagged) to map to the associated service.

To configure a port or link aggregate as an access port, use the service access command. For example, the following command configures port 1/2 and link aggregate 5 as access ports:

.. code-block:: console

    -> service access port 1/2
    -> service access linkagg 5

VLAN Translation on Access Ports
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

VLAN translation refers to the egress translation of VLAN tags on service access points (SAPs). For more information about how VLAN translation is applied, see “Using VLAN Translation” on page 7-40.
By default, VLAN translation is disabled on access ports. Enabling VLAN translation on an access port implicitly enables translation for all SAPs associated with that port. However, translation must also be enabled for the services associated with these SAPs. This ensures that all SAPs associated with a service will apply VLAN translation.
To enable VLAN translation on an access port, use the service access vlan-xlation command with the enable option. For example:

.. code-block:: console

    -> service access port 1/3 vlan-xlation enable
    -> service access linkagg 10 vlan-xlation enable

Configuring Layer 2 Profiles for Access Ports
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A Layer 2 profile determines how control frames ingressing on an access port are processed. When a port is configured as an access port, a default Layer 2 profile (def-access-profile) is applied to the port with the following default values for processing control frames:

.. figure:: /images/l2profile.png
    :width: 300px
    :align: center
    :height: 200px
    :alt: alternate text
    :figclass: align-center

If the default profile values are not sufficient, use the service l2profile command with the tunnel, drop, and peer options to create a new profile. For example, the following command creates a profile named “DropL2”:

.. code-block:: console

   -> service l2profile DropL2 stp drop gvrp drop 802.1ab drop

Assigning Layer 2 Profiles to Access Ports
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After a Layer 2 profile is created, it is then necessary to assign the profile to an access port or link aggregate. When this is done, the current profile associated with the port is replaced with the new profile.
The service access l2profile command is used to assign a new profile to an access port. For example, the following command assigns the “DropL2” profile to access port 1/4 and link aggregate 5:
 
 .. code-block:: console

   -> service access port 1/4 l2profile DropL2
   -> service access linkagg 5 l2profile DropL2

To change the profile associated with the access port back to the default profile (def-access-profile), use the default option with the service access l2profile command. For example:

.. code-block:: console

   -> service port 1/4 l2profile default
   -> service access linkagg 5 l2profile default

Creating the Service Access Point
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Each SPB service is bound to at least one Service Access Point (SAP). A SAP identifies the point at which customer traffic enters the Provider Backbone Bridge Network (PBBN). Creating a SAP on an SPB switch designates that switch as a Backbone Edge Bridge (BEB) in the PBBN. An SPB switch that does not have a SAP but does have at least one BVLAN and an SPB interface is designated as Backbone Core Bridge (BCB) in the PBBN.
Once the SPB topology is determined and switches that will serve as BEBs are identified, a SAP is created on each BEB. A SAP is created by associating a SAP ID with an SPB service. A SAP ID is comprised of a customer-facing port (referred to as an access port) and an encapsulation value that is used to identify the type of customer traffic (untagged, single-tagged, or double-tagged) to map to the associated service.
The service sap command is used to configure a SAP. This command specifies the SPB service ID number and the SAP ID (slot/port:encapsulation). The following parameter values are used with this command to specify the encapsulation value:


.. figure:: /images/sapencap.png
    :width: 500px
    :align: center
    :height: 200px
    :alt: alternate text
    :figclass: align-center

.. note::  

 :all (wildcard) parameter is also configurable as the inner tag value for double-tagged frames (for example, “10:all” specifies double-tagged packets with an outer tag equal to 10 and an inner tag with any value).

The following service sap command example creates a SAP that will direct customer traffic ingressing on access port 1/4 that is tagged with VLAN ID 50 to service 100:
 
 .. code-block:: console

   -> service 100 sap 1/4:50 description “BEB1 to SPB100 CVLAN 50”

In the above example, the 1/4:50 designation is referred to as the SAP ID or the encapsulation ID. This means that if no other SAPs are configured for port 1/4, then any traffic ingressing on that port is dropped if the traffic is not tagged with VLAN 50.
It is possible to configure more than one SAP for the same access port, which provides a method for segregating incoming traffic into multiple services. For example, the following SAP configuration for port 2/3 sends incoming traffic to three different services based on the VLAN tags of the frames received:

.. code-block:: console

   -> service 2000 sap port 2/3:all
   -> service 200 sap port 2/3:100
   -> service 1000 sap port 2/3:100.200

In this example,

* Frames double-tagged with 100 (outer tag) and 200 (inner tag) are sent on service 1000.
* Frames single-tagged with VLAN 100 are sent on service 200.
* All other frames (those that are not single-tagged with 100 or double-tagged with 100 and 200) are sent on service 2000

The following SAP ID classification precedence is applied when there are multiple SAPs for one access port:

#. Double-tagged (Outer VLAN + Inner VLAN) - Highest
#. Double-tagged (Outer VLAN + all)
#. Single-tagged (VLAN)
#. Single-tagged (wildcard)
#. Untagged - Lowest.

Configuring IP over SPB (Layer 3 VPN)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Configuring IP over SPB requires the following general steps:

* Define an L3 VPN interface to serve as a gateway address to remote networks. There are two options for configuring an L3 VPN interface based on the switch platform:

 - An external loopback port configuration.
 - An IP service-based interface for in-line routing (OmniSwitch 9900 only).

* Determine whether to use the VPN-Lite or L3 VPN (SPB-ISIS) approach for routing L3 traffic over an L2 SPBM backbone network.

This document, we will cover only the SPB-ISIS approach.

* To implement the L3 VPN (SPB-ISIS) approach, configure a binding between a VRF instance, an SPB I-SID, and an IP gateway (the L3 VPN interface address). This binding will enable bidirectional exchange of routes between the VRF and SPB I-SID via the Global Route Manager (GRM).

 - Optionally configure a route map to filter routes that are imported or exported between the VRF and I-SID defined in an L3 VPN binding.
 - Optionally configure additional methods for route leaking, such as route redistribution to allow routing between a VRF instance and an I-SID or between two I-SIDs.

The following steps are used to configure an L3 VPN interface:

1 Identify the BEBs that will participate in routing L3 traffic through the SPBM core. On each of these BEBs, configure the required L3 VPN interface configuration. There are two options for configuring this type of interface based on the switch platform: an external loopback port configuration or an IP service- based interface (OmniSwitch 9900 only).

a. Configure an L3 VPN loopback interface. The following commands create an external loopback port configuration that will serve as an L3 VPN interface:

.. code-block:: console

    -> vlan 200
    -> vlan 200 members 1/1/1 tagged
    -> spb bvlan 500
    -> service access port 1/1/2
    -> service 1000 spb isid 1000 bvlan 500 admin-state enable
    -> service 1000 sap port 1/1/2:200
    -> vrf create vrf-1
    vrf-1::-> ip interface IPv4-L3vpn1 vlan 200 address 10.1.1.1/24
    vrf-1::-> ipv6 interface IPv6-L3vpn1 vlan 200 address 1000::1
    vrf-1::-> ipv6 interface IPv6-L3vpn2 vlan 200 address 2000::1

.. note::  

    Once the above loopback port configuration is defined, use a physical cable to connect port 1/1/1 to access port 1/1/2. 
    The SPB BVLAN and I-SID are required to create the SAP for access port 1/1/2. 

b. Configure an IPv4 L3 VPN service-based interface (OmniSwitch 9900). The following commands provide an example for creating an L3 VPN service-based interface:

.. code-block:: console

    -> vrf create vrf-1
    vrf-1::-> ip interface IPv4-L3vpn3 service 10 address 20.1.1.1/24
    vrf-1::-> ipv6 interface IPv6-L3vpn4 service 10 address 4000::1

In this example, the “IPv4-L3vpn3” and “IPv6-L3vpn4” interfaces are created in the “vrf-1” instance and bound to SPB service 10. See “IPv4 L3 VPN In-Line Routing: Two I-SIDS, Two VRFs” on page 7-62 for an example configuration of an L3 VPN service-based interface.

**Configuring the L3 VPN (SPB-ISIS) Solution**

The L3 VPN (SPB-ISIS) approach requires configuring a VRF-ISID binding to identify the L3 VPN interface that will exchange routes between the VLAN domain (VRF instance) and the SPB service domain (I-SID). VRF import and export commands are used to exchange routes between the VRF and the I-SID specified in the binding configuration. For example:

.. code-block:: console

    vrf-1::-> ip export all-routes
    vrf-1::-> vrf default
    -> spb ipvpn bind vrf-1 isid 1000 gateway 10.1.1.1 all-routes
    -> vrf vrf-1
    vrf-1::-> ip import isid 1000 all-routes

In this example, “vrf-1” is bound to SPB I-SID 1000 and gateway 10.1.1.1 identifies the IPv4 L3 VPN interface. All IPv4 routes in “vrf-1” are exported to the Global Route Manager (GRM), which then exports the routes to I-SID 1000. The last command in this sequence sets up the import of I-SID 1000 routes from the GRM into the “vrf-1” instance. The all-routes parameter specifies that no route-map filtering is applied to exported or imported routes; all routes are allowed.
 
 .. code-block:: console

    vrf-1::-> ipv6 export all-routes
    vrf-1::-> vrf default
    -> spb ipvpn6 bind vrf-1 isid 3000 gateway 1000::1
    -> vrf vrf-1
    vrf-1::-> ipv6 import isid 3000 all-routes

In this example, “vrf-1” is bound to SPB I-SID 3000 and gateway 1000::1 identifies the IPv6 L3 VPN interface. All IPv6 routes in “vrf-1” are exported to the GRM, which then exports the routes to I-SID 3000. The last command in this sequence sets up the import of I-SID 3000 routes from the GRM into the “vrf-1” instance. The all-routes parameter specifies that no route-map filtering is applied to exported or imported routes; all routes are allowed.

**Filtering Imported or Exported Routes**

To filter routes that are imported or exported, define a route map to use with the ip import or ip export commands. For example, the following commands create the “ipvpn-vrf1” route map and filter exported and imported routes:

 .. code-block:: console

        vrf-1::-> ip access-list ipaddr
        vrf-1::-> ip access-list ipaddr address 15.0.0.0/8 action permit redist-control
        all-subnets
        vrf-1::-> ip route-map ipvpn-vrf1 sequence-number 1 action permit
        vrf-1::-> ip route-map ipvpn-vrf1 sequence-number 1 match ip-address ipaddr
        vrf-1::-> ip export ipvpn-vrf1
        vrf-1::-> ip import isid 1000 ipvpn-vrf1
        vrf-1::-> ipv6 access-list ip6addr
        vrf-1::-> ipv6 access-list ip6addr address 2001::/64 action permit redist- control all subnets
        vrf-1::-> ip route-map ipvpn6-vrf1 sequence-number 1 action permit
        vrf-1::-> ip route-map ipvpn6-vrf1 sequence-number 1 match ipv6-address ip6addr vrf-1::-> ipv6 export ipvpn6-vrf1
        vrf-1::-> ipv6 import isid 3000 ipvpn6-vrf1

Configuring Route Redistribution Between VRFs and/or I-SIDs
The L3 VPN (SPB-ISIS) solution also allows for the redistribution of routes between a VRF instance and an I-SID or between two I-SIDs (inter-I-SID route leaking). For example, the following commands redistribute routes from I-SID 2000 into I-SID 1000, from “vrf-1” into I-SID 2000, from I-SID 4000 into I-SID 3000, and from “vrf-1” into I-SID 4000.
 
  .. code-block:: console

      -> spb ipvpn redist source-isid 2000 destination-isid 1000 all-routes
      -> spb ipvpn redist source-vrf vrf-1 destination-isid 2000 all-routes
      -> spb ipvpn6 redist source-isid 4000 destination-isid 3000 all-routes
      -> spb ipvpn6 redist source-vrf vrf-1 destination-isid 4000 all-routes

**Verifying L3 VPN Configuration and Routes**

VRFs are bound to I-SIDs to identify a VRF mapping to a specific SPB service instance for the purposes of exchanging routes between the VRF and I-SID via the switch GRM. To verify the VRF mapping configuration on the local switch, use the show spb ipvpn bind or show spb ipvpn6 bind command. For example:

  .. code-block:: console

        -> show spb ipvpn bind
        Legend: * indicates bind entry is active
        SPB IPVPN Bind Table:
                VRF              ISID            Gateway                 Route-Map
        --------------------+------------+----------------------+----------------------
        * vrf-1                  1000         10.1.1.1
        * vrf-2                  2000         20.1.1.1
        Total Bind Entries: 2
        -> show spb ipvpn6 bind
        Legend: * indicates bind entry is active
        SPB IPVPN Bind Table:

Security
########

OmniSwitch implements a Multi-layered Security approach to secure the data, control and management plane of the switch.
The following sections covered the various security implementations:

Data plane
^^^^^^^^^^
The figure below shown the various features used to protect the data plane of the switch.
At the edge, access guardian feature in OmniSwitch provide network access control functionality
to authentication the user/device based on any of the following methods:

* MAC authentication
* Captive Portal, or
* 802.1x authentication.  

OmniSwitch supports Radius, Tacacs+, LDAP and RSA for authentication.

If access guardian is not enabled, administrator can implement rogue device detection capability
to protect the switch from unauthorized access.

.. figure:: /images/dataplane.png
    :width: 800px
    :align: center
    :height: 300px
    :alt: alternate text
    :figclass: align-center

After a device passed the network access control phrase, the switch perform device fingerprinting and attempt to discover 
the Device Type/ Vendor Type/ Operating System/ and OS version via different means such as mac-address, dhcp, http agent etc.
These information collected formed the context of the user/device which in turns is used to derive the appropriate network policy.
The switch subsequently classify the user/device into the relevant service/vlan based on:

* Mac-address (static associated)
* IP address (static associated)
* Contextual information (dynamic derived associated)

Before the user/device is given full access, the switch can limit the access based on the posture of the device (PC in particular).
An OnGuard agent can be pre-installed in the PC to check the PC for compliance; and only allow the PC full access if the device passed
all the compliance checks.  Posture check implementation is optional.

The OnGuard agent can check for:

* Installed applications
* Anti-Virus pattern file agencies
* Host Firewall status
* Network connections
* Patch Management
* Services
* Virtual Machine
* Processes
* Windows Hotfixes
* USB / File checks

The OnGuard agent continue to check for compliance throughout the entire session.  Should the device failed the checklist or policy
at anythime, the policy manager will revoke the network access.  This is achieved via Radius COA or OnGuard agent-bounced.

Besides the OnGuard agent, the switch also perform various security functions to protect the network.  These includes

* Flood control - Limit broadcast, multicase, and unknown-unicast
* Microsegmentation - Port / Device level acl 
* Quarantine Manager - quarantine device that trigger security events on IDS/IPS/Firewall
* MACSec - Layer 2 point-to-point encryption between two switches
* User behaviour (Wireless only) - reports all the sessions of the users for further processing
* App Visibility (req. DPI) - provide App visibility of the network through OmniVista 2500


Control & Management plane
^^^^^^^^^^^^^^^^^^^^^^^^^^

OmniSwitch implements multiple features to ensure the control and management plane is protected from external threats.

Alcatel-Lucent Enterprise and LGS Innovations have combined to provide the first network equipment to be hardened by an independent group. 
CodeGuardian promotes security and assurance at the network device level using independent verification and validation of source code, software diversification to prevent exploitation and secure delivery of software to customers.
CodeGuardian employs multiple techniques to identify vulnerabilities such as software architecture reviews, source code analysis (using both manual techniques and automated tools), vulnerability scanning tools and techniques, as well as analysis of known vulnerabilities in third party code.

.. figure:: /images/controlplanesecurity.png
    :width: 800px
    :align: center
    :height: 300px
    :alt: alternate text
    :figclass: align-center

OmniSwitch provide secure access to network administrator via SSH/ HTTPS / SFTP and SNMPv3.  All access can be protected by password policy and 
the idetntify can be challenged against a AAA infrastructure.   In addition, the OmniSwitch AOS gives administrator the flexibile to bind
or unbind the management services to the respective IP interfaces.  Certainly, the AOS allow trusted host to access the switch management plane.

Besides controlling management access, OmniSwitch AOS also guard the switch against DDoS attacks.  The built-in DDoS filtering
capability protects the CPU against attacks such as ICMP Ping of death, Land attach, ARP Flood attach, Invalid IP attack etc.

Control plane protection is also one of the crucial feature of OmniSwitch AOS.
OmniSwitch AOS implements standard-based protocol authentication to guard against learning or sharing routing information
with unauthorized devices.  In addition, OmniSwitch AOS also include the key-chain management module that further enhance the 
secure keys used in device authentication.

The keychain module is a centralized key management mechanism in AOS. Any module using key management service ensures enhanced security with regular rotation of the keys. Each keychain defines set of keys with start time and end time.
To configure a key chain, an administrator defines a series of keys, and the router software rotates through them. Each key also has an associated time interval, or ‘lifetime’ for which it will be activated. The authentication code included in each key is called the key string.
When a user application (like OSPF, ISIS) receives a packet, it has to be authenticated as per the authentication type, key, and message digest. When a keychain is associated with a user application, hello packets are authenticated using key provided by the keychain module.

The authentication is passed when following conditions are satisfied. Else, the adjacency is not formed and hello packet is discarded.

* Current active key defined in keychain and key in the packet are same.
* Authentication type of the current key in the keychain and the authentication type mentioned in the packet are same.
* Message digest calculated by the keychain manager based on the active key and message digest carried in the packet are same.

Interworking with other Networks
################################

MPLS to SPB interconnect
^^^^^^^^^^^^^^^^^^^^^^^^

GPON to SPB interconnect
^^^^^^^^^^^^^^^^^^^^^^^^

Metro-E to SPB interconnect
^^^^^^^^^^^^^^^^^^^^^^^^^^^

WLAN to SPB interconnect
^^^^^^^^^^^^^^^^^^^^^^^^

Operation, Administration, and Management (OAM)
###############################################

SPB OAM
^^^^^^^

Ethernet OAM
^^^^^^^^^^^^

Link OAM
^^^^^^^^

Service OAM
^^^^^^^^^^^

Service Assurance
^^^^^^^^^^^^^^^^^

For solution guide click here :download:`Smart City Solution Guide </pdf/smartcities.pdf>` 