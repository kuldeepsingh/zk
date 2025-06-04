---
title: Ideas - routing 
date: May 15, 2025
tags: []
---
# Routing 
- Static, Directly Connected and Dynamic routes 

Distance vector 
   - RIP ( works on hop count )
       - Rip V1 - Classfull routing 
       - RIPV2  - Classless routing
Path Vector - BGP
link State - OSPF

OSPF used metrics whereas BGP used attributes to find the next-hop

===============================================================================
# BGP (Path Routing Vector Protocol)
===============================================================================
- AS, 
- Home-AS, 
- Redistribution, 
- Route-map : used to manipulate the routes ( route filtering) and attributes modifications between the BGP peers. 
- Peer-group : group the peers into a single bundle so that less processing and similar config can be done 
- Communities : Provides a way to categories routes in sets with common properties. 
- Weight:
   - Cisco specific but used wifely
   - 32 bit
   - Higher weight is prefered.
- Local-Preference : 
   - 32 bit value,
   - shared between iBGP
   - higher value is preferred
- Conferderations 
- security 
   - Route leaking, prefix hijacking, session hijacking

BGP Attributed 
---
 - used to determine the best path.
 - 


Route Advertisements 
---
  - Done in the BGP-UPDATE packets
  - Fields in a typical UPDATE:

    Withdrawn Routes (if any)
    Path Attributes:
    ORIGIN
    AS_PATH
    NEXT_HOP
    LOCAL_PREF
    MED(Multi Exit Discriminator), COMMUNITY, etc.
    NLRI (Network Layer Reachability Info):
    List of IP prefixes (e.g., 192.0.2.0/24)


iBGP
---
Scalability issue arises in a AS due to the full mess is resolved using the route refector and Conferderations.

# Route Reflector 
  - Works in hub-spoke model
  - RR and Client are be configured manually or done using the orchestration system.
  - Can have multiple RR in the same AS to provide redundency or load-balancing
  - It overrules the split-horizon rules. Thus, we need different mechanism for avoiding loops. Orginator ID and Cluster List is used to avoid the looping 
  - A Centralized BGP router that has been configured to act as a hub which collects routes from each hub and redistributes to all the iBGP .
  - Can have 3 types of peering 
     - eBGP Peering with different arises 
     - iBGP Peering with client 
     - iBGP Peering with non-client neighbour
# RR Client 
- iBGP peer with the RR.
# Non-client iBGP Peer
- Peer configured as non-client.


# BGP Split horizon rule 
    Route learned from one iBGP peer must not be advertised to another iBGP Peer.

# BGP Design Consideration
- Designing AS systems 
- IGPs used within AS
- BGP is used to advertise the networks outside the AS
- Interconnection of AS with other network entities
- redundency
- Security 
- Loopback interface as source of BGP msg.

===============================================================================
# OSPF
===============================================================================
- Link State protocol
- Hirarical Design with multiple Areas. Has Area 0 ( Core Area ) always.
- Network-adjacency.
- Dijkstra Shorted path algorithm.

# How OSPF Works 
 - Area 
    - Limit the information ( change ) to the area.
    - Traffic between areas must flow from the area zero
    - Hub and Spoke Design 

 - Multicast packets for LSAs
    224.0.0.5 for All OSPF router 
    224.0.0.6 for all Designated router 
 - LSAs (Link-State Advertisements): information about the links
 - LSU ( Link-State Update ) : A packet that contains LSA. Routers share information about their links in an Area 
 - LSR ( Link state Request) : Used by the router to request specific LSA information from a neighbour.
 - LSRA ( link state request acknowledgment)
 - LSDB (Link-State Database): All routers in an area build the same map of the network  
 - ABR ( Area Border Router)
 - Shortest Path Calculation: Uses Dijkstra's algorithm 
 - Cost Metric: Path selection based on the speed and reliability of links 
 - Tip: OSPF updates quickly, meaning it can reroute data in case of outages 
 - DR and BDR selection
     - On a multi-access medium ( like all routers connected to the switch), OSPF optimize the LSU by doing DR.
     - highest router priority. Default is 1. Need to enter manually.
     - if tie then higher Router ID wins
 - Neighbourship
     - Need to be same Area.
     - matching Authentication
     - Timers
     - Stubs Fields 
     - Authentication
     - MTU Match 

 - Types of Routers 
     - Internal. Routers • ALL interfaces in single area 
     - Backbone Routers • At Least one Interface in Area 0 
     - ABR — Area Border Routers • Interface(s) in Area 0 and another Area • Maintain an LSDB for each Area • Summarizes LSAs between Areas 
     - ASBR — Autonomous System Border Routers • Redistributing foreign routes into OSPF 

 - Hello packet 
     - Router ID - Identity of each Router 
     - Hello Interval - Frequency of periodic Hello's 
     - Dead Interval - Duration to remember Neighbor 
     - Neighbors - Neighbor Router ID(s) seen on link 
     - Area ID - OSPF Area interface belongs to 
     - Authentication Data - Password restricted peering 
     - Network Mask - Subnet Mask for Link 
     - Area Type - Normal, Stub, NSSA 
     - Designated Router - IP address of DR 
     - Backup Designated Router - IP address of BDR 
     - Priority - Influences DR/BDR election

 - OSPF Adjecency

   State machine ( FSM)
   ----

              |ATTEMPT|
     |DOWN|--------------|2-WAY|---|EXSTART| ----|EXCHANGE|----|LOADING|---|FULL| 
               |INIT|          




  - Scalability issue is resolved using the DR/BDR selection.
  - DR ( Designated Router)/ BDR ( backup designated router)
    - Boardcast / NBMA address



# LSA

TYPE 1 : Router LSA — Router identifies itself and it's links • IP Networks / Subnet Masks / Costs for each Router Link • Used to build topology map of local areality

TYPE 2 : Network LSA — Sent by Designated Router (DR) • When multiple routers connected to the same multi-access Liareality

TYPE 3 : Summary LSA — Contain IP Networks from foreign areas • Sent by Area Border Routers (ABRs), in both directions • Summarizes Type 1 and Type 2 and Type 3 LSAs 

TYPE 4 : ASBR-Summary LSA — Instructions to reach ASBRs • Sent by ABR, when ASBR is in a foreign Area 

TYPE 5 : External LSA — Contain an IP Subnet redistributed into OSPF • Sent by Autonomous System Border Routers (ASBRs) • Forwarded unchanged throughout OSPF domain
