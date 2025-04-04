<div align='center'>
  <h1> Servers </h1>
</div>

# Table of Contents

- Stateful vs Stateless Servers
- Cold standby
- Warm standby
- Hot standby

# Stateful vs Stateless Servers

- Stateful server: requests from the same client are routed to the same server. This server maintains session state across multiple requests from the same client. This approach is ideal for vertical scaling.

- Stateless server: any server can handle any request without depending on any previous session-specific data (data from a particular request) or state. In other words, there is no history of responses between requests or information about where the request was routed. This approach is ideal for horizontal scaling.

# Cold standby

Data from master is copied to a standby slave. The process can take days if the master database is too large.

# Warm standby

Data from master is replicated constantly to the standby slave.

# Hot standby

There is no replication. Data is written simultaneously to the master and the standby slave. This is close to a horizontal scaling solution since it's also possible to read simultaneously.
