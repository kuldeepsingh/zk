---
title: Ideas - network-protocol
date: May 15, 2025
tags: []
---
# Routing 
- Static, Directly Connected and Dynamic routes 

Distance vector 
   - RIP ( works on hop count )
       - Rip V1 - Classfull routing 
       - RIPV2 - Classless routing
Path Vector - BGP
link State - OSPF

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
    MED, COMMUNITY, etc.
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
-

===============================================================================
# OSPF
===============================================================================
- Link State protocol
- Hirarical Design 
- Has Area 0 ( Core Area ) always.
- Network-adjacency.
- Dijkstra Shorted path algorithm.

# How OSPF Works 
 - LSAs (Link-State Advertisements): Routers share information about their links 
 - LSDB (Link-State Database): All routers in an area build the same map of the network  
 - Shortest Path Calculation: Uses Dijkstra's algorithm 
 - Cost Metric: Path selection based on the speed and reliability of links 
 - Tip: OSPF updates quickly, meaning it can reroute data in case of outages 


  - Scalability issue is resolved using the DR/BDR selection.
  - DR ( Designated Router)/ BDR ( backup designated router)
    - Boardcast / NBMA address

