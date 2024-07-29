
It has been almost two years since the fall of FTX and 3AC kicked off an extended bear market in web3. In this time, we have seen the wider web3 ecosystem adopt different visions of what blockchains will look like. Not long ago, Ethereum alignment was the most important feature of any network, while a year before that, sovereignty was deemed a necessity to become a prominent product. Now, this thesis is flipped on its head with Solana taking the majority of web3 usage and proving this can be done on a single synchronous state machine. Despite these changes in the mindset of builders and investors, one constant component has been fragmentation. It is not surprising that this has gone hand in hand with billion-dollar raises for interoperability products like Wormhole and LayerZero, and the launch of a long-tail of additional GMP competitors. However, through all this time, one protocol remains at the top of interoperability: IBC.

## IBC Explained
The core vision of Cosmos is to scale horizontally by having an ecosystem of interconnected, application-specific blockchains. The Inter-Blockchain Communication Protocol (IBC) is the bridging protocol that is immediately compatible with all CosmosSDK + tendermint networks and relays data packets between the state machines (i.e., application blockchains). Unlike other bridging protocols that rely on external trust assumptions, often through MPC committees of operators, core teams, or the wider PoS network, IBC validates transactions with light clients on both chains which eliminates any external trust assumptions.

## Permissionless and trust-minimized
IBC provides a standardized and trustless way for blockchains to communicate without middlemen or external validation. This "native security" of IBC means that if you trust chain A and chain B, then you also trust IBC-Token A on chain B and vice versa. IBC security does not depend on third parties to verify the validity of transactions between blockchains. Instead, it relies on:
<br/><br/>
- Trust in the chains you connect with: If you trust the integrity of chains A and B, you can trust the transactions facilitated by IBC between them.
- Fault isolation mechanisms: These limit damage if a chain acts maliciously, maintaining the overall security of the network.
<br/><br/>
IBC remains Byzantine resistant thanks to the proof validation by the light client. If a relayer acts maliciously, the packet would be rejected by the counterparty chain because the proof would be invalid.

![image_IBC_flow](https://github.com/LavenderFive/blogs/blob/master/images/blog/IBC_1.png?raw=true)

## How messages move between chains
IBC is composed of two layers:
<br/><br/>
- Transport layer (IBC/TAO): Provides the infrastructure for establishing secure connections and data packet authentication between chains.
- Application layer (IBC/APP): Defines how data packets should be packaged and interpreted by sending and receiving chains.
<br/><br/>
Relayers are off-chain actors ensuring the “physical” connection between two chains. They scan the state of the interconnected chains, look for intentions to send transactions, and relay the data and its commitment proof to the other chain. For example, in the case of fungible token transfers (App ics-20), relayers prove that your tokens are locked in Chain A and provide a representation (Voucher) on Chain B. They use the light client of each blockchain to verify incoming messages on chain A and submit them (with the proof of commitment) on chain B. Chain A cannot send data directly to chain B; instead, it commits the hash of the data packet in its own state machine. Relayers monitor this state to send the packet and its proof to chain B.

### Relayer incentives - ICS29
However, the question of how to incentivize these relayers effectively remains a topic of active discussion. Most relayers currently support themselves by delegations to their validators on the respective network making relay a form of public good. Sometimes blockchain teams also provide extra delegation ensuring that relayers are compensated for their efforts. However, the wider cosmos ecosystem is expecting a new channel standard called ICS-29 which aims to make the relaying system self-sustainable by introducing fees for relayers.
<br/><br/>
ICS-29 adds an optional tip the sender of the token can add to the IBC packet. The tip goes to the address that provides the final acknowledgement of the packet on the sending chain thereby incentivizing the relaying/finality of the packet. Because the packet itself is only incentivized when finalised and now incentives are provided for updating light clients or submitting packets to external chains there is still a lot of missing parts to this mechanism. In order for IBC to support ICS-29 going forward all older channels have to be updated to the new standard, a way of doing this is still under testing which means so far no real ics-29 adoption has handed and relayers are still paying gas for all IBC transactions without a direct return.


## Composable contracts and accounts

IBC allows for more than just fungible token transfers. It supports non-fungible token transfers, multi-chain smart contracts, interchain accounts (enabling interaction on a blockchain account from another source blockchain) and even interchain queries. Applications like Nolus, Secret Network, Quasar, DaoDao, TimeWave, Euclid protocol and many others are the first large users of these more complex composability apps on top of IBC. 
<br/><br/>

----

IBC stands out in the landscape of interoperability protocols by providing a trust-minimized solution that relies solely on the security of the interconnected chains and the integrity of relayers. This approach ensures secure, standardized, and trustless communication between diverse blockchain networks, paving the way for a truly interconnected ecosystem.
