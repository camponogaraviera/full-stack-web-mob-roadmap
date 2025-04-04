<div align='center'>
  <h1> Over-Provisioning for Resiliency</h1>
</div>

# About

Over-provisioning focuses on how to ensure resilience, i.e., how to design a system that is fault-tolerant. One solution is to spread out the database across:

1. Multiple hacks in a single data center.
2. Multiple availability zones (data centers).
3. Multiple regions (Asia, Europe, North America, South America, Africa, Australia).

A load balancer can then be used to geo-route incoming requests to the nearest server. This way, if one region goes down, the system can still function.
