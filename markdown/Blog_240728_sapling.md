ZK tech and privacy in blockchain are concepts that are deemed daunting by many web3 enthusiasts, and not to diss the incredibly talented developers that work on them, it is actually quite easy to grasp when you get the basic concept. In this article we dive into the components of shielded transactions and specifically multi-asset shielded pools as they exist on Penumbra, Namada, Aleo, Aztec and many other networks. We explore this concept by dissecting the, still brilliant and incredibly influential, sapling circuit which was originally created by the Electric coin foundation aka zCash.

## How ZK enables privacy
In the world of private compute we often talk about encrypted data, private state and users envision large protected databases full of garbled text no once can understand. However, it is important to understand that in any (privacy focused) ZK blockchain it is actually nothing like that. Why? Well, ZK-proofs don't take private inputs (at least for now) but only compute over 100% publicly available data. And this computation, it is done off-chain. Zero-knowledge proofs allow any party to verifiably prove they executed the function in the circuit correctly, over a certain input, without actually revealing the input. By creating a ledger of these transaction proofs and their associated impact on the global state we create a system where a user always retains ownership of their own data. A caveat to this system is that no proofs can be created over data the user can't see, this is why multi-party use-cases (aka global shared private state) are not feasible with ZK without a trusted intermediary.

<img src="https://github.com/LavenderFive/blogs/blob/master/images/blog/Henry_zk_tweet.png?raw=true " class="height-constrained" alt="Image henry twitter" />

## What is Zcash/sapling 

<span class="italic">Before diving much further, if you don't understand the concept of UTXOs and how they are uttilized in Bitcoin, zCash, Monero etc. - Read about it in the [Bitcoin documentation](https://developer.bitcoin.org/devguide/transactions.html#introduction).</span>
<br/><br/>
[Sapling](https://github.com/zcash/zips/blob/main/protocol/sapling.pdf) is the ZK circuit for zCash, on of the first uses of ZK-proofs in blockchain, which enables private transfers of assets between users. Sapling relies on a UTXO architecture meaning the blockchain database consists of thousands (if not millions) of user-generated commitments called notes. These notes indicate an amount a user is willing to spend and are secured by an associated user-held spending key. The blockchain database is a tree architecture of all of these notes with their associated commitments, basically a graph of users sending funds to each other without claiming the funds. As soon as funds are claimed using the associated spending keys the notes get "nullified" and removed from the tree.
<br/><br/>
In the above architecture it becomes clear that if someone can prove that their note is valid by showing the commitment, they can add it to the chain state only to be spend upon later authorized action by the spending key. This is where ZK-SNARKS come in to play to create shielded inputs and outputs for these user-generated notes.
<br/><br/>
As long as a user can prove to the network that they know their authorized spending key and that they correctly added a valid (non double spending) commitment to the on-chain note tree they can transact - privately! When talking about Shielded transfers and blockchain privacy with web3 enthusiasts I always describe this as: "zCash makes transfers private by letting users proof they are not double spending" - simple yet majestic.
<br/><br/>
As the whitepaper would put it:
<span class="italic">"In each shielded transfer, the nullifiers of the input notes are revealed (preventing them from being spent again) and the commitments of the output notes are revealed (allowing them to be spent in future). A transaction also includes computationally sound zk-SNARK proofs and signatures, which prove that all required information was committed correctly."</span>
<br/><br/>
These commitments in the tree of notes are homomorphic so that at any point <span class="bold">the multiplication of all notes in the tree</span> (remember there is no public value attached to these) reveal a commitment that represents <span class="bold">the sum of the value of the tree of commitments</span>. This is how a user can prove their transaction is a valid modification to the existing state of trees. They know the value they will be transferring and the complete Pederson commitment that should follow from that (multiply with all existing notes) and can proof that the sum of values is equal to that already noted on-chain.
<br/><br/>
Read that again
<br/><br/>
and maybe.... again

## Making Sapling multi-asset

But sapling has a property that is no longer fitting to the current interconnected web of blockchains - The reliance on a single native asset.
<br/><br/>
A multi-asset shielded pool (MASP) as implemented in Penumbra and coming to many other networks is a derivative of the above Sapling circuit where any asset can be shielded. In a MASP an additional "asset type" property is added to each note as well as an independent value base for each of these asset types. The result is an asset-wide anonymity set for any potential asset that exists inside this shielded pool, independent on wether or not these assets are native or bridged in. In practice this means someone depositing and spending USDC also improves the privacy of a user transacting in the native currency of the network improving the privacy for all users.
<br/><br/>
Thinking back to the Pedersen commitment balance check:
What changed here is that instead of calculating the sum of all commitments on a single value_base (ZEC), you calculate the sum of commitments to come to a balance for all valid value bases. By proving your value transfer is a valid commitment you can now move any asset in or out of the shielded pool or transfer it to another shielded address without a validator or other observer being able to distinguish which asset is being transferred.
<br/><br/>
If you want to better understand the math behind Sapling and multi-asset shielded pools consider watching [this awesome explainer](https://lavenderfive.com/networks?id=namada) by Christopher Goes of Anoma and Namada.

## Extending the usage of ZK-proofs for multi-party apps

So we have seen that in order to have a private shared state (The transfers/value committed by users) we also rely on additive homomorphic encryption. However, for more complex applications that are not just about sending value between users there is no such solutions. In the intro we talked about how ZK-proofs only allow privacy in systems where users don't have to compute on each others data, and although this is true we should explore some alternative app designs that still make this possible.

### Trusted middlemen - Sealed bid auctions
A first-price sealed-bid auction (or blind auction) is a type of auction in which each participant submits a bid without knowing the bids of the other participants. The bidder with the highest bid wins the auction. To make this work in an environment where we don't have global shared private state (remember, we cant proof something over data we don't see) we change the system slightly. We assume the auctioneer thereby selecting this party as the trusted middlemen. All bidders submit their bids to the auctioneer with a <span class="italic">place_bid</span> function call after which the auctioneer calls the <span class="italic">resolve</span> function call to extract the winning bid. All bidders get back their money at the end of the auction and the contract will only allow the identity of the highest bidder to pay for and claim the item.
<br/><br/>
We have now executed an auction where the blockchain merely acts as a middlemen and all bidders privately interact with the program while sharing all needed data with the trusted party running the program so it can be resolved fairly. Read more about an implementation of this using Leo, the Aleo zk programming language in this [article](https://medium.com/@ololo70/unveiling-the-secrets-of-first-price-sealed-bid-auctions-with-aleo-0f3076410ca7).

### Shared public state with batching - Concentrated liquidity AMM
Penumbra is an IBC enabled tendermint network with a UTXO model and Multi-asset shielded pool. Besides this novel chain concept it also hosts a Concentrated liquidity AMM for users to trade on directly from their shielded addresses. So penumbra has a public shared state (the AMM reserves/pools) but allows private interaction with those pools through a similar method as the zCash sapling circuit. In order to improve the privacy of traders (so observers don't dissect their trade by looking at change on the public pools) they have enabled users to do asynchronous NFT-like swaps. A user swaps first by committing a note to the blockchain through the <span class="italic">swap</span> action after which it is executed in a batch with other swaps by the validators and the right is given to the user to claim the assets at some point in the future. the <span class="italic">claim_swap</span> action privately mints new tokens of the output type, and proves consistency with the user’s input contribution (via the swap) and with the effective clearing prices (which are part of the public chain state).
<br/><br/>
A user has now privately interacted with a public AMM while being able to choose their own anonymity set for the retrieval back of their swap contents. In later versions Penumbra can add threshold encryption to allow users to perform swaps using sealed-bids protected by an honest minority assumption of validator voting shares. Want to learn more about zSwap, the AMM on Penumbra? Then continue to [their documentation](https://protocol.penumbra.zone/main/dex.html).
<br/><br/>

----

The Sapling circuit and the multi-asset shielded pool are the most used Zero-knowledge proof based privacy system in all of web3 and is finding a home in projects like Penumbra, Namada, Aleo, Aztec and many others. It perfectly shows how proving off-chain compute can create private interactions on a global state by utilizing additive homomorphic encryption and ZK-proofs. More complex applications like the Penumbra Concentrated liquidity AMM or the the Aleo Sealed-bid auctions scheme will have to rely on either Shared public state with batching or a trusted intermediary to provide enhanced user privacy but are paving the way for the zero-knowlegde powered future of web3 Apps.
<br/><br/>
Learn more about your favourite ZK blockchain through our dedicated pages - [Aleo](https://lavenderfive.com/networks?id=aleo), [Aztec](https://lavenderfive.com/networks?id=aztec), [Namada](https://lavenderfive.com/networks?id=namada), [Penumbra](https://lavenderfive.com/networks?id=penumbra)