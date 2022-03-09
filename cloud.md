## Cloud Security Application Deployment: Common Pitfalls

### On-premises does not always transfer (and vice versa)
- Present performance and functionality may not be transferable
- Current configurations and applications may be hard to replicate
- Not developed for cloud-based services
- Not all anplications are forklifted to the cloud
*Forklifting: The process of migrating an entire application to the cloud with minimal.code changes*

### Not all apps are "Cloud-Ready"
- The code is reevaluated and altered several times in order to run eftectively in the cloud.
- Many high-end applications come with distinct security and regulatory restrictions or rely on legacy coding.
- Encryption of information is performed that was not done earlier.

### Lack of awareness and training

### Lack of documentation and guidelines
- Best practice requires developers to follow relevant documentation, guidelines, methodologies, processes, and lifecycles.
- Most up-to-date guidance may not always be available, particularly for new releases and updates.

### Complexities of integration
- Integration is complicated when the CSP manages infrastructure, applications, and integration platforms.
- Collection or keeping tracks of events and transactions is difficult across interdependent or underlying components
- Using CSPs wherever it's possible.

### Overarching challenges
- Multitenancy
- Third-party administrators

## Cloud Secure Development Life Cycle
1. Planning and Requirements Analysis
2. Defining
3. Designing
4. Developing
5. Testing
6. Maintenance

## Securing Network Configuration

### VLAN
- VLAN is an Institute of Electrical and Electronics Engineers (IEEE) standard networking scheme with specific tagging methods that allow routing of packets to only those ports that are part of the VLAN.
- VLANs do not guarantee that data will be transmitted securely and will not be tampered with or intercepted while on the wire.

### TLS

It uses X.509 certificates to authenticate a connection and to exchange a symmetric key.

Types of protocols:

- TLS handshake protocol: Allows the client and the server to authenticate each other and to negotiate an encryption algorithm along with cryptographic keys before data is sent or received
- TLS record protocol: Provides connection security and ensures that the connection is private and reliable and is used to encapsulate higher-level protocols, including the TLS handshake protocol

### DNS

Threats to the DNS infrastructure:
- Footprinting: The process by which an attacker obtains DNS zone data, including DNS domain names, computer names, and IP addresses for sensitive network resources
- Denial-of-service attack: When an attacker attempts to deny the availability of network services by flooding one or more DNS servers in the network with queries
- Data modification: It is an attempt by an attacker to spoof valid IP addresses in IP packets that the attacker has created. This gives these packets the appearance of coming from a valid IP address in the network.
- Redirection: When an attacker redirects queries for DNS names to servers that are under the control of the attacker, redirection occurs.
- Spoofing: It is an attempt by an attacker where a DNS server accepts and uses incorrect information from a host that has no authority to give that information.

### DNSSEC

- DNSSEC is a suite of extensions that adds security to the Domain Name System (DNs) protocol by enabling DNS responses to be validated.
- DNSSEC provides origin authority, data integrity, and authenticated denial of existence.
- In the presence of DNSSEC, the DNS protocol is much less susceptible to certain types of attacks.
- Validation of DNS responses occurs through the use of digital signatures that are included with DNS responses.
- It does not address confidentiality or availability.

### IPSec

- IPSec includes protocols for establishing mutual authentication at the beginning of the session and negotiating cryptographic keys to be used during the session.
- IPSec supports network-level peer authentication, data origin authentication, data integrity encryption, and replay protection.
- A major difference between IPSec and other protocols such as TLS is that IPsec operates at the internet network layer rather than the application layer, allowing end-to-end encryption of all communications and traffic.

## How are Windows Active Directory and Azure Active Directory different?

| Windows AD | Azure AD |
|:-----------------:|:---------------:|
| It is a directory service that facilitates working with interconnected, complex and different network resources in an unified manner | Azure Active Directory (Azure AD) is Microsoft's multi-tenant, cloud-based directory and identity management service |
| Uses 5 layers to store data, store user details, issue and manage certifications etc. Works with an emphasis on on- premises units like applications, file services, printers, etc. | Integrates the 5 layers of Windows AD into 2, to perform the same operations. Emphasizes on web based services that use RESTful interfaces| 
## What are the types of Queues offered by Azure?

Azure offers two types of queues:

- Storage Queues:
	- Part of Azure's Storage infrastructure.
	- Provides messaging within and between services. 
	- Best suited when users need to stor more than 80 GB of messages in queues.
	- It can provide side logs of all transactions executed against the user's queues.
- Server bus Queues:
	- Part of Azure's messaging infrastructure.
	- Integrates application or application components that span multiple communication protocols, network environments, etc.
	- Provides a FIFO style of delivery
	- The user's queue size has to remain under 80 GB

## Azure Resource Manager

Azure Resource Manager enables users to manage their usage of application resources

- ARM helps deploy, manage and monitor all the resources for an application, a solution or a group
- Users can be granted access to resources they require
- Obtain comprehensive billing information for al the resources in the group
- Provisioning resources is made much easier with the help of templates

## How has integrating hybrid cloud been useful for Azure

The Hybrid Cloud boosts productivity by using Azure and the Azure stack for building and deploying
applications for cloud and on-premises applications

- Obtain greater efficiency with a combination of Azure services and DevOps processes and tools
- Users can take advantage of a constantly updated Azure services and other Azure Marketplace applications
- Enables to deploy applications regardless of its location, the cloud or on-premises.
- This enables applications to be created at a higher speed

## What is Federation in Azure SQL?

SQL Azure Federation provides tools that can enable developers to access or share databases among
themselves in SQL Azure

- It enables users to take advantage of resources within the cloud
- Allows users to have their own database or share databases amongst each other
- Reduces the possibility of a single point of failure
- Provides cost effectiveness, by using cloud resources only when needed

## What are the advantages of Azure Queue Storage

Queue storage enables message queueing for large workloads in a simple, cost-effective
and durable manner

- It provides rich client libraries for Java, Android, C++, PHP Ruby, etc.
- Enables users to build flexible apps and separate functions for greater durability
- Ensures users' applications are scalable and less prone to individual component failure
- Enables queue monitoring to ensure servers aren't overwhelmed by sudder traffic bursts

