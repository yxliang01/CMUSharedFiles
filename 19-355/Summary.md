19-355 Summary
=====

Author: Xiao Liang Yu

[TOC]

# Cryptocurrency

## Bitcoin

### Attacks

#### Eclipse Attack

Attacker uses a bunch of nodes to force nodes to accept an alternative chain.
This is powerful because we can make miners to build blocks based on the transactions we provide and use their mining powers to make our chain to be the main chain.

### Anonymity

#### Anonymity Set

About the number of possible transaction directions. (TODO)

#### Mixer

Instead of sending bitcoins directly to the receiver, we send them to a mixer who is a man in the middle. Mixer will then send the same amount (or less if mixer should receive some fees) of bitcoins to the receiver. But, the bitcoin spent for sending to the receiver might be from other people which was originally for giving to other receivers.

In order to make this work, mixer should have enough amount of inputs which have similar amount of bitcoins in order to increase the anonymity set (because this way we increase the number of indistinguishable transactions by values). (REPHASE TODO)

# Cryptography

## Hash

### Properties
- One-wayness
    + Given $ y = H(x) $, computationally infeasible to find $ x $
- Weak collision resistance
    + Given $ x $, computationally infeasible to find $ y \ne x $ such that $ H(x) = H(y) $
- Strong collision resistance
    + Computationally infeasible to find any pair $ (x,y) $ such that $ H(x) = H(y) $
- Puzzle-friendliness
    + For every possible output value $ y $, if $ k $ is chosen from a distribution with high min-entropy, then it is computationally infeasible to find $ x $ such that $ H(k | x) = y $
    + Implies that no solving strategy is asymptotically better than trying random values of $ x $

### Hash chain

The hash value of block $ i $ = $ r_i = H(r_{i+1}) $ 

Can't derive $ r_i $ from $ r_j $ for $ j < i $ but efficiently authenticate $ r_j $ using $ r_i $

### Hash pointer

It's a pointer to where some information is stored which contains the cryptographic hash of the information.

Additional to pointing function, it can verify that the data wasn't changed.

However, those pointer links can't have cycle.

#### Application: blockchain

It's a linked list of hash pointers.
All the historical logs can't be modified or the whole chain after the modified block will be changed.

#### Application: Merkle Tree

![Merkle-Tree](https://github.com/yxliang01/CMUSharedFiles/raw/master/19-355/Summary-Images/Merkle-Tree.png)

Membership: $ O(\log{n}) $ time/space

##### Sorted Merkle Tree

Can verify non-membership in $ O(\log{n}) $ time/space



## Birthday paradox

If there are 23 or more people in a room, there is more than 50% chance that two of them have the same birthday

This is different from the (relatively low) chance that someone has the same birthday as me

My conclusion: according to this, we have 1/2 change to generate a hash collision after trying $ 1.17 \sqrt(N), N = \text{number of possible hashes} $ hashes.


## Block cipher
- Operate on blocks of $ n $ bits of plaintext and ciphertext
- Using the same key, the same plaintext block always encrypt to the same ciphertext block
- Block length 
    + If the block length is too small, it's vulnerable to statistical analysis
- Operation modes
    + Electronic Code Book
        * same block of plaintext always maps to the same block of ciphertext
        * frequency analysis is possible
    + Cipher Block Chaining (CBC)
    + Counter (CTR)

## Attacks

### Birthday attack

Generate lots of inputs, try to match with the ciphertext in order to figure the original unencrypted content.



### Indistinguishability

#### Indistinguishability under chosen plaintext attack (IND-CPA)
- Adversary knows encryption algorithm
- Has access to two oracles but not key
    + Encryption oracle
        * submits a plaintext $ m $
        * gets back ciphertext $ c = Enc(m) $
    + Test oracle
        * submits two plaintexts $ m_a $ and $ m_b $
        * gets back ciphertext $ c^* = $ either:
            - $ Enc(m_a) $ with probability 1/2 or,
            - $ Enc(m_b) $ with probability 1/2 or,
- Procedure
    + begins with polynomial number of $ Enc $ calls
    + calls test once on new $ m_a $, $ m_b $ to get $ c^* $
    + then, polynomial number of $ Enc $ calls
    + guess $ c^* $ is $ Enc(m_a) $ or $ Enc(m_b) $
- $ \text{Your advantage} = Pr(\text{correct guess}) - 1/2 $

#### Indistinguishability under chosen ciphertext attack (IND-CCA1 or 2)
- Adversary knows encryption algorithm
- Has access to three oracles but not key
    + Test oracle
    + Encryption oracle
    + Decryption oracle
        * submits ciphertext $ c $
        * gets back plaintext $ m $
- Procedure
    + begins with polynomial number of $ Enc $ calls
    + calls test once on new $ m_a $, $ m_b $ to get $ c^* $
    + then, polynomial number of $ Enc $ calls
    + guess $ c^* $ is $ Enc(m_a) $ or $ Enc(m_b) $
- This whole thing is a TODO


## Frequency analysis attack

For example, different letters have different probabilities of occurring in natural languages -> large enough sample of ciphertext can provide info needed to reconstruct the text.

The structure can be recovered.

To destroy the structure:

- Encrypt more than one text at a time
- Use multiple encryption alphabets
- However, it will remain vulnerable to frequency analysis unless you have certain statistical independence properties


## Symmetric Crypto

![Symmetric-Crypto](https://github.com/yxliang01/CMUSharedFiles/raw/master/19-355/Summary-Images/Symmetric-Crypto.png)
$ K_1 = K_2 = K (\text{or } K_2 \text{ can be trivially obtained from } K_1 $
Generally, $ D_K (\text{The decryption function}) = E_K^{-1} (\text{The encryption function}) $
Both parties need to know the shared key $ K $

### Caesar cipher

Just simply shift the characters by a fixed offset (it is the key)

### Vernam cipher

$ P_i = \text{plaintext} $
$ K_i = \text{key (length as long as the plaintext, statistically independent)} $
$ C_i = P_i \oplus K_i $
$ P_i = C_i \oplus K_i $

### One-time pad

The key must be truly random with no repetitions (other than by pure chance) and no correlation with the plaintext.

This is the only cipher mathematically shown to be unbreakable
    Some ciphers have security proofs but they typically only apply with a given set of assumptions

Since the key is truly random, the same ciphertext can map into any plaintext -> invulnerable to cryptanalysis

Since computers can generate truly random numbers, it's not implementable -> only approximations can be made.

### Stream ciphers
- An attempt to approximate one-time pad
    + Operate on the plaintext a single symbol (bit, byte, letter) at a time
    + The same plaintext symbol
- Advantage
    + Relatively easy to implement in hardware
- Drawbacks
    + Keys must never be used twice
- In practice
    + Fixed-length key $ K $
    + Use pseudo-random generator to generate keystream from seed (initialization vector $ IV $)
    + send $ IV, ciphertext $


## Asymmetric Crypto

Often, $ Enc_{pk} $ and $ Dec_{sk} $ operations are often inverses of each other. Functions with this property are called "trapdoor functions" or "trapdoor permutations".

### Functions

$ Gen(n) $: efficient algorithm that on input $ n $ generates public key $ pk $ and secret key $ sk $
$ Sign_{sk}(m) $: efficient algorithm that on input message $ m $ and secret key $ sk $ outputs a signature $ \sigma <- Sign_{sk}(m) $
$ Ver_{pk}(m, \sigma) $: efficient deterministic (polynomial) algorithm that on input public key $ pk $ and message $ m $ outputs either True or error

Generator: There exists some element $ g \in G $ such that for all $ a \in G $, $ a = g * g * g *....g * g $ for $ k $ times, for some $ 0 \le k < | G | $ 

### Signature
- A means to authenticate a message
- Authentication allows the receiver to verify the original of a message
- Can be used to convince a third-party of the origin of the message

### Diffie-Hellman Key Exchange

![Diffie-Hellman-Key-Exchange](https://github.com/yxliang01/CMUSharedFiles/raw/master/19-355/Summary-Images/Diffie-Hellman-Key-Exchange.png)

### ElGamal Signature Scheme

![ElGamal-Signature-Scheme](https://github.com/yxliang01/CMUSharedFiles/raw/master/19-355/Summary-Images/ElGamal-Signature-Scheme.png)

### Linkable Ring Signature

We have a group of people. Now, we have a signature. We know that this signature must come from that group of people. But, we have no idea who in the group signed it.

`Sign(message, PK_1, PK_2, PK_3, ..., s, SK_s)`

We only need one of the secret key `SK_s` that corresponds to one of the public key `PK_s` to have a valid signature.

#### Traceable Linkable Ring Signature

We still can use one of the secret key `SK_s` that corresponds to one of the public key `PK_s` to have a valid signature. But, now we know the `s`.

# Decentralized Anonymous Market

## Properties

### Correctness
A customer who performs a valid payment transaction for an item can always review that particular item

### Soundness
It's not possible to leave a valid review for an item without purchasing it. When an item is purchased, exactly 1 review may be left.

### Review-payment unlinkability:


### Review-payment deniability

# Distributed Consensus

## Difficulty
- No global notion of time
- Broadcasting is not instantaneous
- Some participants may be malicious

### Byzantine Generals Problem
- Consensus is not guaranteed if more than 1/3 generals are traitors

### Blockchain

Easy to verify validity but hard to compute the desirable answer

#### Assumption

A majority of the network resources is controlled by honest participants

#### 51% attack

Can:

- Can suppress some transactions from the blockchain

Can't:

- Change block reward
- Suppress some transactions from the p2p network
- Steal coins from existing address

# Smart contracts

# Misc

## Game Theory and Cryptocurrency

### Terms
#### Rational
Individuals are able to estimate the benefits and costs of a particular action (i.e. are able to estimate the net benefit)

#### Self-interested
Agents engage in an activity if the benefit is greater than or equal to the cost (i.e. the net benefit is greater than or equal to zero)

#### Social optimum
Set of strategies that maximizes total $ U = \Sigma^N_{i=1} U_i $ network utility

This is ideal and is what a benevolent government would want

#### Nash equilibrium
Set of strategies in which no individual player can increase their individual utility $ U_i $ by changing their strategy

It's a selfish equilibrium.

## Ponzi scheme

A fraudulent investment operation where the operator, an individual or organization, pays returns to its investors from new capital paid to the operators by new investors, rather than from profit earned through legitimate sources.

## Sybil attack

An attack wherein a reputation system is subverted by forging identities.

## Selfish Mining

Instead of reporting whatever blocks you found, you keep them secret and continue mining based on them. Release blocks immediately when you lose lead or N blocks more than main chain where $ N > 1 $. This way you are more likely to have more profit than being honest.

## Miner's dilemma

If all miners are malicious, the equilibrium would be Nash equilibrium. If all miners are honest, we get social optimum. The thing is, if you are the only one malicious, you will get a lot more than if you were honest.

## ASIC Resistant

$ ASIC Resistant functions \implies Memory hard functions $

### Examples
- Ethash

## Scrypt

Not ASIC Resistant.
Litecoin is using.

## Proof-of-burn

This is a technique used to bootstrap a new currency. Basically, you **lock** some money on an established blockchain to get some other coin in other (new)blockchain.

## Instant Karma PKI (IKP)

### Problem

There are unauthorized certificates issued by different root CAs. IKP allows the reporting and punishment process to be automated.

### Incentive for adoption

CAs adopted IKP would increase their competence.

### Desired Properties
- Audibility
- Automation
- Incentivization
- Deterrence

## Proof of Stake

The creator of next block is chosen in a deterministic (pseudo-random) way, and the chance that an account is chosen depends on its wealth (i.e. stake).

## Feather forking

Some miners announce that they won't accept certain transactions. Although the miners collaboratively have a small fraction of mining powers. But, the cost for other miners to adapt is nearly 0 while this can increase the profit. Therefore, a fork is possible. 

## Merge mining

Mining more than one currency using the same operations.

# Questions
- What's the problem of `splitDAO()` (in theDAO contract)
- 