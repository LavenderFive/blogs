Want to better understand the setup and security exposure of the Lavender.Five Validators? Then this is the blog for you! Lets dive into our validator infrastructure. We will start with explaining the basis on which we built our nodes and validators, the bare metal servers. We also go deep into our remote signing setup for the cosmos ecosystem, our access control and key management and of course monitoring.

## Geographically distributed Bare-Metal

At Lavender.Five we operate close to all of our services on rented or owned bare-metal servers. This means we control what happens to the servers, who is able to access them and how they are networked. This provides a huge improvement over the cloud model where these security features can't be guaranteed by the centralized hosts. Our machines sit in datacenters owned and managed by parties like OVH, LeaseWeb, Cherry, Latitude etc allowing us to distribute our systems across 4 continents with ease. There is 24/7 secure, out of band, multi-factor based remote management in place for all machines.

### Networking and security
All servers have gated access through hardware SSH authentication (yubikeys) and are separated from public networking through our overlay tooling [Nebula](https://github.com/slackhq/nebula). The overlay network allows us to create separate clusters which are only allowed to interact with each other, how this helps us with distributing risk will be discussed in the remote signing section. Secure, high bandwidth, encrypted connectivity is in place between data center networks and public nodes to allow for node relocation, data duplication, and encrypted communication across sites. We operate different server clusters for our testnet, whitelabel and RPC activities making it impossible for us to co-mingle key material, access policies or networking settings. In summary, nobody can access our machines but us and public connectivity (like peering and RPC) is tightly managed through our overlay architecture so that there is additional fault isolation.

![Network Architecture drawio (1)](https://github.com/LavenderFive/blogs/blob/master/images/blog/network_topology.png?raw=true)

## Operator- & Signing Key management
A validator node generally requires management of two different types of keys, the signing and the operator key. At lavender.Five we take incredible care to secure both types of keys and have them backed up for persistence of our validator identity into the future independent of external factors. As a basis we generate all keys locally to reduce exposure risk and only move them to the appropriate server/node as necessary. We use hardware keys to secure operator keys when possible (even adding this to the binary or delaying our launch if there is no genesis support), and multi-party compute remote-signing solutions (think SSV for Ethereum, or Horcrux for the Cosmos ecosystem) whenever possible for signing/consensus keys. 
<br/><br/>
Our key generation process is covered by an automation playbook (Ansible) which ensures proper handling and limits human error. The general process is as follows:
<br/><br/>
1. Generate respective keys on local machine
2. Back up keys to local, encrypted, offline hard drive
3. Back up keys to encrypted cloud 
4. Act upon keys
 * a. If possible, split the key for MPC/Remote signer usage
 * b. Otherwise, transfer to respective server and If possible, utilize encrypted key storage mechanism Otherwise, leave key in plaintext
<br/><br/>
Lavender.Five operates all testnets on separate keys and will never reutilize testnet keys as a hard policy control. Additionally, Lavender.Five hosts separate "custody" keys so that there is no funds present on our validator identity which removes the interest for hackers and scammers. Our key backups are only accessible by the core of Lavender.Five. Key generation and access is similarly handled on a priority basis; if it’s a mainnet network, only previously stated individuals may generate and act upon the keys.

### Key rotation

Within the Cosmos ecosystem there is no key rotation for validator keys nor validator signer keys. In practice what this means is that we do everything we can to secure said keys and not require their use so as to reduce risk of exposure. For the validator key we use a Ledger for key generation, then use the authz module to offload any responsibility from the validator key to a separate key for responsibilities such as pulling commissions or voting. For the validator signer key, we utilize horcrux (an MPC remote-signing solution) to split and encrypt the key and use that for signing so we don't have to host our key material on servers that have public ports open for peering. MPC signers also significantly reduce the risk for remote signing (which is most commonly an operator error) and therefore offer our delegators additionally security.
<br/><br/>
For networks that do support key rotation, we practice rotating keys on the testnet and create a run book in case we need to rotate the keys. In this way we are able to rotate compromised keys cleanly and quickly. Due to our encrypted connectivity between servers we can do automated failovers securely at any moment of day through our playbooks and could even do so programmatically when needed to limit downtime.

![MPC signing](https://github.com/LavenderFive/blogs/blob/master/images/blog/MPC_remote_signing.png?raw=true)

## Performance monitoring and slashing protection

Lavender.Five has extensive monitoring in place to track performance and avoid downtime. When we join a new network, the first thing we do is build out a monitoring suite and open source it so others can provide feedback and use it as well. The best example of this is [our Aptos monitoring suite](https://github.com/LavenderFive/aptos-monitorin), all other are also listed on our [github](https://github.com/LavenderFiv). As a general rule the monitoring suites use a combination of Prometheus, Zabbix, Grafana, Alertmanager, Loki, and Pagerduty to consume, present, and alert upon various metrics. An example of such a metrics dashboard (which can be publicized to teams as well) is visible [here](http://grafana.lavenderfive.com/public-dashboards/bb5fd690bde64709bd552496d060d032 ). All monitoring uses both our own internal RPCs/endpoints in addition to using external/third party endpoints as a fallback in the case of catastrophic failure. So beyond consuming metrics provided by the nodes and validators themselves we track the signing performance of our validators by scanning the network. An example of this design is displayed in our [TenderDuty dashboard](https://tenderduty.lavenderfive.com/), we are the core maintainers of this monitoring software used by 50+ other Cosmos validator teams. The health of the bare metal machines themselves is monitored with Zabbix and also present in the aforementioned dashboards so we can alert on a combination of metrics.

![Monitoring image](https://github.com/LavenderFive/blogs/blob/master/images/blog/monitoring.png?raw=true)

Once the monitoring suites are built, we connect them to Pagerduty which notifies us if there is an issue. Our Pagerduty policy includes weekly rotations, and if a notification isn’t acknowledged within 15 minutes, it is escalated to the entire team. We alert in tranches meaning discord, standard push, and email notifications go out first on smaller downtime windows or errors before the priority notification is forwarded. This helps our engineers with alert fatigue and empowers our standard systems to auto-heal where this is applicable making our entire practice more sustainable.

### security patches
Additionally Lavender.Five has a security alerting mechanism which keeps us up to date on critical bugs in packages like Ubuntu, SSH, Nebula or even binaries of networks themselves which get passed to our security email - security@lavenderfive.com. We have seen this system payoff for us with the [recent zero-day exploit in SSH](https://terrapin-attack.com/) that we patched within 8 hours of release giving us time and resources to help other teams push notifications to their validators and infrastructure providers. 

## Upgrade procedure and maintenance

As mentioned above Lavender.Five operates with a minimum of 2 nodes per network, these nodes are geographically- and provider- distributed. For example we’ll have one node in EU-WEST and one in US-EAST provided by OVHCloud and Latitude.sh. The purpose of this is always having a stable backup in case the primary node/validator should go down. This backup is an exact (or close to) copy of the validator as possible such that failing over doesn’t result in poorer performance or adverse behavior.
<br/><br/>
This is important for our maintenance and upgrade procedure as we always upgrade the backup node first to ensure stability and prove the upgrade procedure. Once we’ve confirmed the upgrade was successful with the backup, we then upgrade the primary machine. This is why we can guarantee no downtime for scheduled maintenance and Soft-fork upgrades. Additionally we can ensure the same no-downtime policy for hard-fork style upgrades as we utilize our custom upgrade scheduling playbook to perform upgrades in-kind at the designated moment in time. This you can rely on us to be pre-signed on the upgrade block as one of the first validators every single time. The use of our tools also allows us to upgrade many networks at the same time giving our partners ultimate flexibility in the scheduling of their releases.
<br/><br/>
Just as with key management all upgrades are managed by our Ansible playbooks so they are repeatable and reduces risk of human error. In case of an errorous upgrade or other failure our 24/7 alerting will kick in and we will be able to manually perform the upgrade or in the worse case restore our validator from our own database snapshot service within minutes.
<br/><br/>

----

Lavender.Five Nodes operates at the highest standards for validator security and are in the process of getting ISO and SOC2 certified for our existing procedures. We have managed 3 years of active service on over 50 networks with little to no issues. We are confident in our setup and offer 100% downtime slash protection (through a rebate) to all of our delegators to further alleviate any concerns. We happily share our expertise with the community which is why you will find all our tooling available open-source on our [github](https://github.com/lavenderfive). Additionally we have helped many validators get their first start and are known for our quick response to other infrastructure providers in the discords of the 80+ networks we are working with. If you want to ask more questions about our process after reading this post, then don't hesitate to [get in contact](https://lavenderfive.com/#contact)!
