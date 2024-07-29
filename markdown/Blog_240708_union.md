
In this blog series we tackle the investment and technology thesis for one our partners at Lavender.Five Nodes so that you don't have to. It is a overview that will assume industry knowledge from the reader so we can move through material quickly. However, we provide extensive guidance along the way. Grab a cup of Coffee, set the timer to 5 minutes and learn about your favourite interoperability protocol Union.

## The overview

Union is a small team of (mainly) engineers tackling the interoperability problem. Union is a cosmos-esque network which acts as a bridging hub between the IBC world (CometBFT chains) and external ecosystems like Ethereum and Solana. The core of the product is a concept called ZK-IBC, all other features are improvements to support specifically this concept. IBC relies on the connected chains having light clients of each other within their state, so that the trust assumptions of the bridging protocol are reduced to consensus verification of both chains. ZK-IBC scales this trust-minimized approach to blockchains which normally don't have the computational overhead or architecture to keep an updated light client of another chain by moving this computational requirement to a separate set of provers. The over-majority of the technical innovation of Union is focused around making the proving cheaper and faster so that the system becomes truly decentralized, permissionless and trustless - How do we benchmark this? A Laptop can generate a proof for a 128 validator set within seven seconds using just 5gb of RAM.

## The assumptions

- Web3 is becoming ever more fragmented due to Modular centric scaling - 
- The trust assumptions of IBC are superior to the most common MPC committee bridges
- Cosmos has a liquidity problem which has prevented it from DeFi growth
- If IBC derivates make concessions on the trust assumptions it loses the advantages it has
- The fee model and sustainability of IBC is broken and stuck in a model it can't properly change
- Zero knowledge technology, security and code quality gets better all the time
- Lean engineer-led teams have an efficiency advantage
- Composability between contract environments will prevail over re-deployment of protocols at some point in the future

## The execution

Trying to solve a problem is one thing, actually bringing a sustainable product to market is another. Union has a large group of 6 co-founders and 5 of them can code, a true super team that is supplemented with a lean and strong operations and business duo that is raking in partnership after partnership. The engineering team is slowly growing so they can use the extra talent without stalling development progress completely. The engineering has just one north star, faster proofs without skipping on security, and the whole tech-stack is centered around exactly that. 
<br/><br/>
1. **CometBLS**
Every chain Union connects to will have to run a Union light-client, so where can they make most gains, by optimizing their own chain first. CometBLS has a reduced transaction size and on-chain computation cost in comparison to Tendermint and also makes ZKProofs of the consensus state significantly more efficient.
2. **EPOCH based POS**
By running the same validator set for an Epoch and rotating it every so often based on the adjusted stake weight Union reduces the number of light client updates needed for secure operation of the bridge which improves relayer profitability and thereby the experience for users.
3. **Galois**
The Zero-Knowledge Consensus Proving system Union built is the core of the entire product. It utilizes the zk-SNARK library Gnark to perform consensus proofs of the Union CometBLS state within 7 seconds at an extremely low cost. This helps with censorship resistance, as close to any consumer laptop can run this process without issues, and helps with sustainability of the wider Union product; as relayers and provers don't need insane cloud clusters.
4. **Relayer sustainability**
Although voyager, the core relayer software for Union's IBC protocol, is a super important piece of the puzzle; even more important is the changes made to IBC by Union so that it becomes sustainable over time. No longer does a relayer pay gas fees to be left with nothing, Union allows for packet and later even light client update incentivisation to support the fast and long-term sustainable clearing of transfers in the system. Additionally users of Union IBC for extensive contract composability might opt into contracts for the support of their dedicated channels through Union themselves separating relayer incentivisation from only the # of processed packets.
5. **Generating profit of IBC usage**
Although the details are still unknown it is expected Union will have the power to charge fees for its bridging protocol that go back to growing the Union protocol itself. Many investors would have dreamed to take a bet on IBC but this has never been really possible with networks like the Cosmos hub never fulfilling its promise as an IBC and fee generating hub. As a bridging hub the Union chain will navigate traffic not just between Ethereum and Cosmos but it will also serve the wider bridging needs of chains like Berachain, Polygon AggLayer and Scroll (through L2 conditional clients) driving potentially sky-high volumes through Union.

## The summary

IBC in its current form is a closed off garden yet, by far the most utilized and trusted web3 bridging protocol. Union lets IBC breakout into the wider web3 ecosystem and is proving it can compete in finality times and costs with permissioned and centralized competitors. The engineering team is experienced and familiar with the issues that plague bridging, IBC and specifically ZK variants of it. Their optimized tech-stack will enable a wide set of new use-cases for cross-chain composable products one wouldn't dare to opt in before while simultaneously powering token-connectivity for the largest ecosystems in crypto.