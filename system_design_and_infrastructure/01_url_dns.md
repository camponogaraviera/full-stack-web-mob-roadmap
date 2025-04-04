<div align='center'>
  <h1> URL/DNS/SSL </h1>
</div>

# Table of Contents

- URL
- DNS
- SSL
  - Manage DNS Through registro.br (Keep Existing DNS Zone) 
- Registering a DNS with a Retailer/Registrar/Provider

# URL

URL structure: `protocol://domain.TLD:port/path?query_string#fragment_identifier`.

Meaning:
- `Protocol`: could be FTP (File Transfer Protocol), SMTP (Simple Mail Transfer Protocol), HTTP (HyperText Transfer Protocol), or HTTPS (HyperText Transfer Protocol Secure).
- `Domain`: is the domain name of the server hosting the application. This domain name gets resolved to an IP address via DNS.
- `TLD`: The top-level domain (.com, .net, .org, etc.).
- `Port (optional)`: specifies the connection endpoint on the server where the client should connect. It is an optional parameter and if not specified, defaults to a well-known port for the given protocol (e.g., 80 for HTTP, 443 for HTTPS, or 3000 for a Node.js express server).
- `Path (optional)`: specifies the specific resource or endpoint to be served.
- `query_string (optional)`: contains the parameters for a query to be applied to the resource. Usually in the form of key-value pairs.
- `fragment_identifier (optional)`: identifies a specific location/section within the resource by its ID or name attribute. Works as a bookmark/anchor point for fast access within the page document.

# DNS

A DNS (domain name system) is a naming database that works as a mapping, translating human-friendly domain names (example.com) into IP addresses (e.g. 192.0.2.1).

A single DNS can be associated with multiple IP addresses. This setup helps distribute traffic load across multiple servers, preventing overload on a single server (when there are too many requests to a single server). This technique is often referred to as load balancing or sharding (more on that in the following chapters). This allows millions of users to access the same website at the same time from different locations. Many system designs use GeoDNS for traffic redirection to find the closest server to the client.

One can search for the ownership of a domain at https://www.whois.com/whois/.

Official Domain Name `Registries`:

- US:
  - https://www.enom.com/ 
  - https://www.verisign.com/
- BR: https://registro.br/

# SSL

To install an SSL/TLS certificate on a server or third-party provider, the following files are needed:

1. `certificate.crt`: This is your actual SSL certificate issued specifically for your domain.
2. `ca_bundle.crt (certificate chain)`: This is the certificate chain that links your SSL certificate to a trusted root authority. It usually contains one or more intermediate certificates.
3. `private.key`: This is the private key associated with your SSL certificate. Keep this file secure, as it is used to establish a secure connection.

To acquire these files, you should obtain a public SSL certificate from an external certificate authority (CA) that allows you to download both the certificate and private key. Some popular CA options include: 
- [Let's Encrypt](https://letsencrypt.org/getting-started/), [SSL For Free](https://www.sslforfree.com), `ZeroSSL`, `Sectigo`, or other paid providers.
- PS: CNAME records (name and value) are typically used for domain verification via DNS validation and they are not included in the certificate export (not part of the certificate itself).

## Manage DNS Through registro.br (Keep Existing DNS Zone)

Suppose a website (my_homepage.com.br) is being hosted by a third-party provider, and your domain name is managed by a registry, such as [registro.br](https://registro.br/). For the website to be displayed, the DNS of the hosting provider must be added to registro.br's DNS section. 

However, when you switch to custom DNS servers (e.g., the hosting provider's DNS), you are no longer using registro.br's DNS Zone (advanced settings). This means any DNS records managed in registro.br's interface, including SSL-related records, will no longer be active. 

To avoid this, instead of using the hosting provider's DNS servers, you can obtain the required DNS records (A, CNAME, TXT, etc.) from the hosting provider and manually configure them in registro.br's DNS Zone. This way, you retain control of the DNS records at registro.br. Another option is to send the SSL certificate files (obtained via AWS ACM or another provider) to the hosting provider and install the certificate through their management panel.

# Registering a DNS with a Retailer/Registrar/Provider

Unlike `registro.br`, which is a management organization for `.br` domains (the ccTLD for Brazil), generic top-level domains (gTLDs) are managed by different `registries` (e.g., Verisign for .com). However, registrations for these gTLDs are typically done through accredited domain `registrars` or providers (e.g., [Amazon Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-register.html), [Squarespace](https://domains.squarespace.com/)), who interface with the registry on behalf of the customer.
