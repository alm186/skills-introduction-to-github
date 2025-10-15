RIP LAB
               ┌────────────────────┐
               │       [SW1]        │
               │ Loopback: 192.0.2.1│
               │ RIP: ether2        │
               └────────────────────┘
                    10.0.0.0/31
                  ether2 || ether2
                 (10.0.0.0)||(10.0.0.1)
                          ||
                          ||
               ┌────────────────────┐
               │       [SW2]        │
               │ Loopback: 192.0.2.2│
               │ RIP: ether2, ether3│
               └────────────────────┘
                    10.0.0.2/31
                  ether3 || ether2
                 (10.0.0.2)||(10.0.0.3)
                          ||
                          ||
               ┌────────────────────┐
               │       [SW3]        │
               │ Loopback: 192.0.2.3│
               │ RIP: ether2, ether3│
               └────────────────────┘
                    10.0.0.4/31
                  ether3 || ether2
                 (10.0.0.4)||(10.0.0.5)
                          ||
                          ||
               ┌────────────────────┐
               │       [SW4]        │
               │ Loopback: 192.0.2.4│
               │ RIP: ether2        │
               └────────────────────┘

─────────────────────────────────────────────────────────────
⚠️  RIP Redistribution Warning:
To ensure RIP-learned routes propagate through SW2 and SW3,
you *must* set:
  redistribute=connected,rip

Without this, only directly connected networks will be advertised,
and loopbacks or remote subnets will not be seen beyond one hop.
─────────────────────────────────────────────────────────────
all 4 Switches
routing/rip instance/add name=rip1 redistribute=connected,rip            
routing/rip/interface-template/add instance=rip1 interfaces=ether2
routing/rip/interface-template/add instance=rip1 interfaces=ether3 (SW2 and SW3 only)


/routing rip instance
add name=rip1 out-filter-chain=RIP_loopback_filter redistribute=connected,rip routing-table=main
/routing filter rule	
add chain=RIP_loopback_filter comment=Allow_only_loopbacks="if (dst in 192.0.2.0/24) {accept} else {reject}"
