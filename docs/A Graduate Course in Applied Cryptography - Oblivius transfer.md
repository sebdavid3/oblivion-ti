

Additionally, 

when _A_ outputs<sup>ˆ</sup> _b_ : 

if _b_ =<sup>ˆ</sup> _b_ then output 1 else output 0 

We leave it to the reader to verify (11.18). 

Observe that in Game 2, the key _k_ is only used to encrypt the challenge plaintext. As such, the adversary is essentially just playing the SS game with respect to _E_ s at this point. We leave it to the reader to describe an efficient SS adversary _B_ s that uses _A_ as a subroutine, such that 



Combining (11.16), (11.17), (11.18), and (11.19), yields (11.15), which completes the proof of the theorem. _2_ 

# **11.6 A fun application: oblivious transfer based on Diffie-Hellman** 

Bob likes to be well informed. Every day he logs into his favorite newspaper site, reads the headlines, and downloads the articles that he wants to read. The newspaper charges Bob a few cents for every article that he reads, a price that Bob is happy to pay to support the newspaper. All articles have the same price. 

One day Bob realizes that the newspaper can track what articles he is downloading and use this information to learn his political leanings, age group, and other personal information that Bob would rather keep private. He asks his friend Alice the following question: 

The newspaper has articles _m_ 1 _, . . . , mn ∈M_ and I am interested in reading article number _i ∈{_ 1 _, . . . , n}_ . Can I download article _mi_ without the newspaper learning what article I downloaded? 

Alice says that the newspaper can simply send Bob all the articles _m_ 1 _, . . . , mn_ and Bob will read the ones he is interested in. The newspaper does not learn what articles Bob reads. 

When Bob asked the newspaper to implement this scheme, they refused saying that this is a sure way for the newspaper to go out of business. Their readers will pay a few cents for a single article and get the entire newspaper. The newspaper would go along with the proposal if it could ensure that Bob only receives one article per request. 

**Oblivious transfer.** Let’s call the newspaper the _sender_ and call Bob the _receiver_ . Bob wants a solution to the following problem: 

The sender has data _m_ 1 _, . . . , mn ∈M_ and the receiver has an index _i ∈{_ 1 _, . . . , n}_ . They want a protocol with the following property: when the protocol completes the receiver learns _mi_ and nothing else, while the sender learns nothing about _i_ . 

451 

This problem is called 1-out-of- _n_ **oblivious transfer** , or simply 1-out-of- _n_ **OT** . It comes up in many settings beyond newspapers, such as privately buying digital content from an online store without the store learning what item is being purchased. More generally, OT is useful whenever there is a need to privately request a record in a proprietary database. It is also a central building block in many cryptographic protocols, as we will see in Chapter 23. 

**A warning.** Defining security for OT protocols is quite subtle. Here we present two very simple OT protocols and discuss their security somewhat informally. We caution that both of these protocols require modification to ensure that they can be used securely when running several instances of the protocols concurrently, and when using them as subprotocols in arbitrary applications. Our goal here is to just get across some of the essential ideas, not to present protocols that should be deployed “as is” in arbitrary applications. We will discuss these security issues in Chapter 23. 

## **11.6.1 A secure OT from ElGamal encryption** 

Our first 1-out-of- _n_ OT protocol satisfies the basic OT security properties in the random oracle model, assuming only CDH. We let _M_ denote the message space for the sender’s messages, so that _m_ 1 _, . . . , mn ∈M_ . The protocol uses the following ingredients: 

- a cyclic group G of prime order _q_ with generator _g ∈_ G; 

- a hash function _H_ : G<sup>2</sup> _→K_ , which will be modeled as a random oracle; 

- a semantically secure symmetric cipher ( _E_ s _, D_ s) with key space _K_ and message space _M_ . 

We also assume that the sender and receiver communicate over a secure channel (i.e., one that utilizes authenticated encryption as discussed in Chapter 9). 

With these ingredients in place, we are ready to describe an elegant OT protocol using ElGamal encryption in the group G. We use a variant of the system _E_ EG from Section 11.5. The sender generates _n_ ElGamal public keys _u_ 1 _, . . . , un ∈_ G and encrypts message _mj_ under public key _uj_ , for _j_ = 1 _, . . . , n_ . The clever idea is a mechanism that ensures that the recipient knows the secret key for the public key _ui_ , but does not know the secret key for any the other key. This lets the recipient decrypt the _i_ th ciphertext, obtaining _mi_ , but reveals nothing else. Let’s see how the protocol works and then discuss its security. 

- _step 1:_ the sender chooses _β ←_ R Z _q_ , computes _v ← gβ ∈_ G, and sends _v_ to the receiver. 

- _step 2:_ the receiver chooses _α ←_ R Z _q_ , computes _u ← gαv−i ∈_ G, and sends _u_ to the sender. 

- _step 3:_ for _j_ = 1 _, . . . , n_ the sender computes 

_uj ← u · v_<sup>_j_</sup> _∈_ G _// construct an ElGamal public key uj_ = _g_<sup>_α_</sup> _v_<sup>_j−i_</sup> _wj ← u_<sup>_β_</sup> _j // construct an ElGamal ciphertext_ ( _v, cj_ ) = ( _g_<sup>_β_</sup> _, cj_ ) _kj ← H_ ( _v, wj_ ) _∈K // using randomness β and public key uj, cj ←_ R _E_ s( _kj, mj_ ) _// that is the encryption of mj ∈M_ 

and sends the vector of ciphertexts _C_ := ( _c_ 1 _, . . . , cn_ ) to the receiver. Note that all _n_ ElGamal ciphertexts ( _v, c_ 1) _, . . . ,_ ( _v, cn_ ) are generated using the same encryption randomness _β_ . 

452 

- _step 4:_ the receiver, who has the secret key _α_ for the ElGamal public-key _ui_ = _g_<sup>_α_</sup> , decrypts _ci_ as follows: 

compute _w ← v_<sup>_α_</sup> , _k ← H_ ( _v, w_ ) output _m ← D_ s( _k, ci_ ) 

Let’s verify that this works correctly. During decryption in step 4, the key _k_ is computed as _k ← H_ ( _v, w_ ), where _w_ = _v_<sup>_α_</sup> = _g_<sup>_αβ_</sup> . In step 3 the key _ki_ is computed as _ki ← H_ ( _v, wi_ ), where 



Hence, when the parties honestly follow the protocol, we have _wi_ = _w_ and _ki_ = _k_ , and thus the receiver correctly obtains _mi_ . 

Is the protocol secure? The only information that the sender learns about the receiver’s input _i ∈{_ 1 _, . . . , n}_ is the group element _u_ = _g_<sup>_α_</sup> _v_<sup>_−i_</sup> computed in step 2. Because _α_ is not used in any other message sent to the sender, the quantity _g_<sup>_α_</sup> is uniformly distributed over the group G, and therefore perfectly hides _v_<sup>_−i_</sup> . Hence, the sender learns nothing about _i_ , as required. 

To argue that the receiver learns at most one message, it suffices to argue that the receiver can query the random oracle representing _H_ at no more than one point of the form ( _v, wj_ ) for _j_ = 1 _, . . . , n_ . This will ensure that the receiver learns at most one decryption key, and hence at most one message. We briefly argue that if the receiver could query the random oracle at two such points, say ( _v, wj_ 1) and ( _v, wj_ 2), then we could break the CDH for G. In particular, we can transform such a receiver into an algorithm that takes as input _v_ = _g_<sup>_β_</sup> and produces a tuple 



for some _j_ 1 _̸_ = _j_ 2. Dividing the two right most terms in (11.20) one by the other, and raising the result to the power of 1 _/_ ( _j_ 1 _− j_ 2) gives _v_<sup>_β_</sup> which is the same as _g_<sup>(</sup><sup>_β_2)</sup> . Hence, we get an algorithm that takes as input _g_<sup>_β_</sup> and computes _g_<sup>(</sup><sup>_β_2)</sup> . We showed in Exercise 10.17 that this problem is equivalent to CDH in G. We conclude that if CDH holds in G then the receiver can learn at most one message. 

**_Remark 11.2._** As we cautioned at the beginning of the section, the protocol above must be modified in order to satisfy stronger security properties. See [10] for a similar protocol that can be proven to satisfy such properties. In particular, the above informal security analysis did not take into account the possibility of attacks that can arise when multiple instances of the protocol run concurrently. Exercise 11.20 discusses such attacks, and shows how to prevent them by modifying the protocol in two ways. First, in deriving the secret keys using the hash function _H_ , the hash function should also take as input the identities of the two parties (and possibly some type of “channel binding” or “session ID”, as in Section 21.8). Second, instead of a semantically secure symmetric cipher, we should use a cipher that provides one-time authenticated encryption. _2_ 

## **11.6.2 Adaptive oblivious transfer** 

Going back to our newspaper scenario, the OT protocol presented above satisfies the security properties we want. However, when trying to deploy it, the newspaper hits a serious performance problem. For every article that Bob wants to download, the newspaper has to re-encrypt all the articles in the newspaper under new keys and send all these fresh ciphertexts to Bob. The 

453 

computation and communication costs per download would be prohibitive. In case of an online bookstore, the entire bookstore would need to be re-encrypted for every purchase. 

It would be much better if the newspaper could encrypt all the articles once and post the encrypted articles on its web site for anyone to download. Bob, and other readers, will download the entire encrypted newspaper. Later, every time Bob pays for an article, the newspaper will quickly open the article for Bob without learning which article was opened. The computation and communication costs should be independent of the number of articles _n_ . Bob will repeat this for as many articles as he wants to read. 

An OT protocol that operates this way is called an **adaptive OT** . The word “adaptive” refers to Bob choosing the next article to open in a way that depends on the previously opened articles. Here we describe a simple protocol that satisfies the basic adaptive OT security requirements. That is, a receiver (even a malicious one) learns at most one message per run of the protocol, and the sender (even a malicious one) learns nothing about which messages were opened. In addition, the protocol should ensure that a malicious sender cannot make the receiver open the wrong message. This will provide consistency: whenever one or several receivers request the _i_ th message, they always get the same message. 

As we cautioned above, this protocol must be modified in order to satisfy stronger security properties. See [79] for a protocol that can be proven to satisfy such properties. 

Before describing the adaptive OT we need a quick interlude to describe a useful, related concept called an _oblivious PRF_ . 

## **11.6.3 Oblivious PRFs** 

Let _F_ be a secure PRF defined over ( _K, X , Y_ ). Suppose that one party, the _sender_ , has the PRF key _k ∈K_ . Another party, the _receiver_ , has an input _x ∈X_ . An **oblivious PRF protocol** , or simply an **OPRF protocol** , is a protocol that lets the receiver learn the output _y_ = _F_ ( _k, x_ ) without learning anything else about the value of the PRF at any other input; in addition, the sender learns nothing about the input _x_ . Moreover, the OPRF protocol may be run many times, allowing the receiver to learn the value of _F_ ( _k, ·_ ) for several inputs. The sender should learn nothing about any of these inputs, and the receiver should learn nothing about the value of _F_ ( _k, ·_ ) at any other inputs. Actually, all we will really require here is that the receiver cannot learn anything about the value of _F_ ( _k, ·_ ) at _ℓ_ inputs unless it interacts with the sender _ℓ_ or more times. 

We can construct a simple OPRF protocol from the secure PRF _F_<sup>_′_</sup> presented in Exercise 11.3. That PRF makes use of a cyclic group G of prime order _q_ with generator _g ∈_ G and is defined as 



Here _H_ : _X →_ G and _H_<sup>_′_</sup> : _X ×_ G _→Y_ are hash functions modeled as random oracles. The key space is _K_ = Z _q_ . In Exercise 11.3, you are asked to prove that this PRF is secure assuming CDH holds for G, and we model _H_ and _H_<sup>_′_</sup> as random oracles. 

### **Protocol OPRF1** 

We now describe an OPRF protocol for _F_<sup>_′_</sup> , which we call _Protocol OPRF1_ . Suppose that the sender has a key _k ∈_ Z _q_ . In each run of the protocol, the receiver has an input _x ∈X_ , and the sender and receiver interact (over a secure channel) as follows: 

454 

- _receiver:_ choose _ρ ←_ R Z _q \ {_ 0 _}_ , compute _v ← H_ ( _x_ ) _ρ ∈_ G, and send _v_ to the sender. 

- _sender:_ compute _w ← v_<sup>_k_</sup> _∈_ G and send _w_ to the receiver. 

- _receiver:_ compute and output _y ← H_<sup>_′_�</sup> _x, w_<sup>1</sup><sup>_/ρ_�</sup> _∈Y_ . 

First, observe that if sender and receiver are honest, then in the last step, the receiver computes 



and so the receiver ends up with the correct output. 

Second, observe that because of the way the receiver computes _v_ , the value _v_ leaks no information to the sender about the input _x_ : the sender just sees a random group element, statistically independent of _x_ . 

Now suppose the receiver is honest but the sender is malicious. In this case, the sender still learns nothing about _x_ ; however, the sender could send a wrong value for _w_ , causing the receiver to end up with an incorrect value for the output _y_ = _F_<sup>_′_</sup> ( _k, x_ ). Thus, Protocol OPRF1 is only partially secure if the sender is malicious: while the sender does not learn the receiver’s input, the receiver may end up with the wrong output. 

Now consider how much information a possibly malicious receiver can obtain by interacting with the sender. We want to argue that a receiver cannot learn anything about the value of _F_<sup>_′_</sup> ( _k, ·_ ) at _ℓ_ inputs unless it interacts with the sender at least _ℓ_ times. This follows directly from a reasonable assumption called the **one-more Diffie-Hellman** or **1MDH** assumption, if we model _H_ and _H_<sup>_′_</sup> as random oracles. 

Very briefly, we can describe the 1MDH assumption in terms of the following attack game. In this game, the challenger chooses _α ←_ R Z _q_ , computes _u ← gα ∈_ G, and sends _u_ to the adversary. Next, the adversary makes a sequence of queries to the challenger, each of which can be one of the following types: 

_Challenge query:_ the challenger chooses _v ←_ R G, and sends _v_ to the adversary. 

- _CDH solve query:_ the adversary submits _v_ ˆ _∈_ G to the challenger, who computes and sends _w_ ˆ _← v_ ˆ<sup>_α_</sup> to the adversary. 

At the end of the game, the adversary outputs a list of distinct pairs, each of the form ( _i, w_ ), where _i_ is a positive integer bounded by the number of challenge queries, and _w ∈_ G. We call such a pair ( _i, w_ ) _correct_ if _w_ = _v_<sup>_α_</sup> and _v_ is the challenger’s response to the _i_ th challenge query. We say the adversary wins the game if the number of correct pairs exceeds the number of CDH solve queries. The 1MDH assumption says that every efficient adversary wins this game with at most negligible probability. In particular, an efficient adversary who sees a number of challenges raised to the power _α_ , cannot produce any other challenge raised to the power _α_ . 

Clearly, the 1MDH assumption implies the CDH assumption. It is also not too hard to see that the 1MDH assumption implies that in the above OPRF construction, a receiver cannot learn anything about the value of _F_<sup>_′_</sup> ( _k, ·_ ) at _ℓ_ inputs unless it interacts with the sender at least _ℓ_ times. In particular, the receiver cannot query the random oracle representing _H_<sup>_′_</sup> at _ℓ_ distinct points of the form ( _x, H_ ( _x_ )<sup>_k_</sup> ) unless it interacts with the sender at least _ℓ_ times. We leave this argument as an exercise to the reader. 

We note that in certain groups G, winning in the 1MDH game can be slightly easier than computing discrete log in G. In these groups there is also an attack on the scheme OPRF1 that is slightly faster than computing discrete log in G. The details are in Section 16.1.4. 

455 

**_Remark 11.3._** When using the scheme OPRF1, it is important that the sender verify that _v_ is in G before responding, otherwise the sender’s response could inadvertently expose the secret key _k_ (e.g., using an attack similar to the one in Exercises 12.3 or 15.1). _2_ 

### **Protocol OPRF2** 

As we saw, Protocol OPRF1 is only partially secure in the presence of a malicious sender. We can easily modify the protocol to get one that provides a stronger security property in the presence of a malicious sender. In particular, this modified protocol, which we call _Protocol OPRF2_ , guarantees that if the receiver’s computed output _y_ is wrong, then it is in fact a random value in _Y_ that is completely independent of the sender’s view. The protocol begins by having the sender publish the value _u_ = _g_<sup>_k_</sup> , where _k_ is the sender’s secret key. In each run of the protocol, the receiver has an input _x ∈X_ , and the sender and receiver interact (over a secure channel) as follows: 

- _receiver:_ choose _ρ ←_ R Z _q \ {_ 0 _}_ , _τ ←_ R Z _q_ , compute _v ← H_ ( _x_ ) _ρ · gτ ∈_ G, and send _v_ to sender. Note that this _v_ is computed differently than in OPRF1. 

- _sender:_ compute _w ← v_<sup>_k_</sup> _∈_ G and send _w_ to the receiver. 

• _receiver:_ compute and output _y ← H_<sup>_′_�</sup> _x,_ ( _w/u_<sup>_τ_</sup> )<sup>1</sup><sup>_/ρ_�</sup> _∈Y_ . 

Observe that if sender and receiver are honest, then in the last step, the receiver computes 



and so the receiver ends up with the correct output _y_ = _F_<sup>_′_</sup> ( _k, x_ ). 

Next, observe that because of the way the receiver computes _v_ , the value _v_ leaks no information to the sender about the input _x_ , and also leaks no information about the exponent _ρ_ . 

Now suppose the receiver is honest but the sender is malicious. If the sender responds in the second step with a wrong value for _w_ , say _w_ = _v_<sup>_k_</sup> _· d_ , where _d̸_ = 1, then the receiver computes 



As we observed above, the sender knows nothing about _ρ_ , and so from the sender’s point of view, the value _d_<sup>1</sup><sup>_/ρ_</sup> _∈_ G is just a random group element (uniformly distributed over G). It then follows that the output value _y_ computed by the receiver is also just a random element of _Y_ . 

Finally, suppose the sender is honest but the receiver is malicious. Just as for Protocol OPRF1, under the 1MDH assumption, if we model _H_ and _H_<sup>_′_</sup> as random oracles, then the receiver cannot learn anything about the value of _F_<sup>_′_</sup> ( _k, ·_ ) at _ℓ_ inputs unless it interacts with the sender _ℓ_ or more times. 

## **11.6.4 A simple adaptive OT from an oblivious PRF** 

We will need the following ingredients: 

- A PRF _F_ defined over ( _K_ prf _, {_ 1 _, . . . , n}, K_ ). 

- An OPRF protocol for a PRF _F_ , like Protocol OPRF2 in Section 11.6.3, which is secure against a malicious sender, in the sense that the receiver will either get a correct output or a random output. 

456 

- A symmetric cipher ( _E_ s _, D_ s) defined over ( _K, M, C_ ) that provides one-time authenticated encryption. 

The protocol works as follows. Recall that the sender has messages _m_ 1 _, . . . , mn ∈M_ . 

- _step 1:_ the sender begins by doing: 

_k ←_ R _K_ prf for _i_ = 1 _, . . . , n_ : _ki ← F_ ( _k, i_ ) _, ci ←_ R _E_ s( _ki, mi_ ) _// encrypt all messages_ send _C_ := ( _c_ 1 _, . . . , cn_ ) to the receiver _// send all ciphertexts to the receiver_ 

- _step 2:_ when the receiver wants _mi_ , the receiver and sender establish a secure channel between them and use the OPRF protocol to compute _F_ ( _k, i_ ): 

the sender has _k_ and the receiver has _i ∈{_ 1 _, . . . , n}_ , when done, the receiver has _ki_ = _F_ ( _k, i_ ) 

Finally, the receiver computes _mi ← D_ s( _ki, ci_ ). 

That’s it. If both parties honestly follow the protocol then the receiver obtains _mi_ , as required. Moreover, step 2 can be repeated as many times as needed. Every time the receiver gets to open another message of its choice from the sender’s list. The amount of traffic generated in each invocation of step 2 is relatively small, and in particular, is independent of the size of the sender’s list. 

As for security, first suppose that the sender is honest but the receiver is malicious. In this case, from the security property of the OPRF protocol, we know that the number of messages that the receiver can decrypt is no more than the number of times the receiver runs step 2 of the protocol. 

Second, suppose that the receiver is honest but the sender is malicious. In this case, from the security property of the OPRF protocol, we know that the sender learns nothing about which ciphertexts get decrypted by the receiver. Moreover, we know that if the sender deviates from the OPRF protocol, the receiver will obtain a random decryption key, and by the ciphertext integrity property of a symmetric cipher, the receiver will reject the ciphertext with overwhelming probability. 

**A final word of caution.** To conclude this section we note that there are some inherent limitations to the security of oblivious transfer against corrupt parties. Consider again our newspaper example. Suppose that a corrupt newspaper wants to learn if Bob is reading articles in the finance section of the newspaper. The newspaper could choose its input articles _m_ 1 _, . . . , mn_ to the OT protocol so that all the articles in the finance section are blank, while articles in other sections are correct newspaper articles. Now, when Bob wants to read an article in the finance section he will carry out an OT with the newspaper and end up with a blank article, despite having paid the newspaper for the article. Bob will undoubtedly complain that the oblivious transfer failed, at which point the newspaper learns that Bob requested an article in the finance section. This violates OT privacy. Of course, Bob could preserve his privacy by not complaining, but that is difficult to enforce in real life. 

# **11.7 Notes** 

Citations to the literature to be added. 

457 

# **11.8 Exercises** 

**_11.1 (From weak PRF to PRF via hashing)._** This exercise gives a simple application of the random oracle model that will be useful later. Let _F_<sup>_′_</sup> ( _k, x_ ) be a PRF defined over ( _K, X , Y_ ) and suppose that _F_<sup>_′_</sup> is weakly secure (as in Definition 4.3). Let _H_ : _M →X_ be a hash function, and define a new PRF over ( _K, M, Y_ ): 



Show that _F_ is a secure PRF (in the standard sense of a secure PRF) when _H_ is modeled as a random oracle. In particular, you should show that for every adversary _A_ attacking _F_ as a PRF, there exists an adversary _B_ attacking _F_<sup>_′_</sup> as a weak PRF, which is an elementary wrapper around _A_ , such that PRF<sup>ro</sup> adv[ _A, F_ ] _≤_ wPRFadv[ _B, F_<sup>_′_</sup> ]. 

**_11.2 (Simple PRF from DDH)._** Let G be a cyclic group of prime order _q_ generated by _g ∈_ G. Let _H_ : _M →_ G be a hash function, which we shall model as a random oracle (see Section 8.10.2). Let _F_ be the PRF defined over (Z _q, M,_ G) as follows: 



Show that _F_ is a secure PRF in the random oracle model for _H_ under the DDH assumption for G. In particular, you should show that for every adversary _A_ attacking _F_ as a PRF, there exists a DDH adversary _B_ , which is an elementary wrapper around _A_ , such that PRF<sup>ro</sup> adv[ _A, F_ ] _≤_ DDHadv[ _B,_ G] + 1 _/q_ . 

**_Hint:_** First show that DDH implies that the PRF _F_<sup>_′_</sup> ( _k, v_ ) := _v_<sup>_k_</sup> defined over (Z _q,_ G _,_ G) is a _weak_ PRF (as in Definition 4.3). Use Exercise 10.11 for this. Then use Exercise 11.1 to deduce that _F_ is a secure PRF. 

**_11.3 (Simple PRF from CDH)._** Continuing with Exercise 11.2, let _H_<sup>_′_</sup> : _M×_ G _→Y_ be a hash function, which we again model as a random oracle. Let _F_<sup>_′_</sup> be the PRF defined over (Z _q, M, Y_ ) as follows: 



Show that _F_<sup>_′_</sup> is a secure PRF assuming CDH for G, and when _H_ and _H_<sup>_′_</sup> are modeled as random oracles. Section 11.6.2 gives an interesting application for this PRF, exploiting the fact that it can be evaluated obliviously. 

**_Hint:_** Use the result of Exercise 10.5. 

**_11.4 (Broken variant of RSA)._** Consider the following broken version of the RSA public-key encryption scheme: key generation is as in _E_ RSA, but to encrypt a message _m ∈_ Z _n_ with public key _pk_ = ( _n, e_ ) do _E_ ( _pk , m_ ) := _m_<sup>_e_</sup> in Z _n_ . Decryption is done using the RSA trapdoor. 

Clearly this scheme is not semantically secure. Even worse, suppose one encrypts a _random_ message _m ∈{_ 0 _,_ 1 _, . . . ,_ 2<sup>64</sup> _}_ to obtain _c_ := _m_<sup>_e_</sup> mod _n_ . Show that for 35% of plaintexts in [0 _,_ 2<sup>64</sup> ], an adversary can recover the complete plaintext _m_ from _c_ using only 2<sup>35</sup> _e_ th powers in Z _n_ . 

**_Hint:_** Use the fact that about 35% of the integers _m_ in [0 _,_ 2<sup>64</sup> ] can be written as _m_ = _m_ 1 _· m_ 2 where _m_ 1 _, m_ 2 _∈_ [0 _,_ 2<sup>34</sup> ]. 

458 

**_11.5 (Multiplicative ElGamal)._** Let G be a cyclic group of prime order _q_ generated by _g ∈_ G. Consider a simple variant of the ElGamal encryption system _E_ MEG = ( _G, E, D_ ) that is defined over (G _,_ G<sup>2</sup> ). The key generation algorithm _G_ is the same as in _E_ EG, but encryption and decryption work as follows: 

- for a given public key _pk_ = _u ∈_ G and message _m ∈_ G: 

   - _E_ ( _pk , m_ ) := _β ←_ R Z _q_ , _v ← gβ_ , _e ← uβ · m_ , output ( _v, e_ ) 

- for a given secret key _sk_ = _α ∈_ Z _q_ and a ciphertext ( _v, e_ ) _∈_ G<sup>2</sup> : 

   - _D_ ( _sk ,_ ( _v, e_ ) ) := _e/v_<sup>_α_</sup> 

- (a) Show that _E_ MEG is semantically secure assuming the DDH assumption holds in G. In particular, you should show that the advantage of any adversary _A_ in breaking the semantic security of _E_ MEG is bounded by 2 _ϵ_ , where _ϵ_ is the advantage of an adversary _B_ (which is an elementary wrapper around _A_ ) in the DDH attack game. 

- (b) Show that _E_ MEG is not semantically secure if the DDH assumption does not hold in G. 

- (c) Show that _E_ MEG has the following property: given a public key _pk_ , and two ciphertexts _c_ 1 _←_ R _E_ ( _pk , m_ 1) and _c_ 2 _←_ R _E_ ( _pk , m_ 2), it is possible to create a new ciphertext _c_ which is an encryption of _m_ 1 _· m_ 2. This property is called a **multiplicative homomorphism** . 

**_11.6 (An attack on multiplicative ElGamal)._** Let _p_ and _q_ be large primes such that _q_ divides _p −_ 1. Let G be the order _q_ subgroup of Z<sup>_∗_</sup> _p_<sup>generatedby</sup><sup>_g∈_GandassumethattheDDH</sup> assumption holds in G. Suppose we instantiate the ElGamal system from Exercise 11.5 with the group G. However, plaintext messages are chosen from the entire group Z<sup>_∗_</sup> _p_<sup>sothatthesystemis</sup> defined over (Z<sup>_∗_</sup> _p_<sup>_,_G</sup><sup>_×_Z</sup><sup>_∗_</sup> _p_<sup>).Showthattheresultingsystemisnotsemanticallysecure.</sup> 

**_11.7 (Extending the message space)._** Suppose that we have a public-key encryption scheme _E_ = ( _G, E, D_ ) with message space _M_ . From this, we would like to build an encryption scheme with message space _M_<sup>2</sup> . To this end, consider the following encryption scheme _E_<sup>2</sup> = ( _G_<sup>2</sup> _, E_<sup>2</sup> _, D_<sup>2</sup> ), where 



Show that _E_<sup>2</sup> is semantically secure, assuming _E_ itself is semantically secure. 

**_11.8 (Encrypting many messages with multiplicative ElGamal)._** Consider again the multuplicative ElGamal scheme in Exercise 11.5. To increase the message space from a single group element to several, say _n_ , group elements, we could proceed as in the previous exercise. However, the following scheme, _E_ MMEG = ( _G, E, D_ ) defined over (G<sup>_n_</sup> _,_ G<sup>_n_+1</sup> ), is more efficient. 

- the key generation algorithm runs as follows: 



459 



- for a given secret key _sk_ = ( _α_ 1 _, . . . , αn_ ) _∈_ Z<sup>_n_</sup> _q_<sup>andaciphertext</sup><sup>_c_= (</sup><sup>_v, e_1</sup><sup>_, . . . , en_)</sup><sup>_∈_G</sup><sup>_n_+1:</sup> _D_ ( _sk , c_ ) := ( _e_ 1 _/v_<sup>_α_1</sup> _, . . . , en/v_<sup>_αn_</sup> ) 

- (a) Show that _E_ MMEG is semantically secure assuming the DDH assumption holds in G. In particular, you should show that the advantage of any adversary _A_ in breaking the semantic security of _E_ MMEG is bounded by 2( _ϵ_ + 1 _/q_ ), where _ϵ_ is the advantage of an adversary _B_ , which is an elementary wrapper around _A_ , in the DDH attack game. 

**_Hint:_** Use Exercise 10.11 with _n_ = 1. 

- (b) Theorem 11.1 shows that semantic security implies CPA security, but the concrete security bound degrades by a factor equal to the number _Q_ of encryption queries. Show that for _E_ MMEG, the security bound for CPA security only degrades by a factor of _n_ , which is typically much smaller than _Q_ . In particular, show that for every CPA adversary _A_ there is a DDH adversary _B_ , which is an elementary wrapper around _A_ , such that CPAadv[ _A, E_ MMEG] is bounded by 2 _n ·_ ( _ϵ_ + 1 _/q_ ), where _ϵ_ is the advantage of _B_ in the DDH attack game. 

**_Hint:_** Use Exercise 10.12. 

**_11.9 (Modular hybrid construction)._** Both of the encryption schemes presented in this chapter, _E_ TDF in Section 11.4 and _E_ EG in Section 11.5, as well as many other schemes used in practice, have a “hybrid” structure that combines an asymmetric component and a symmetric component in a fairly natural and modular way. The symmetric part is, of course, the symmetric cipher _E_ s = ( _E_ s _, D_ s), defined over ( _K, M, C_ ). The asymmetric part can be understood in abstract terms as what is called a **key encapsulation mechanism** , or **KEM** . 

R 

A KEM _E_ kem consists of a tuple of algorithms ( _G, E_ kem _, D_ kem). Algorithm _G_ is invoked as ( _pk , sk_ ) _←_ R _G_ (). Algorithm _E_ kem is invoked as ( _k, c_ kem) _←_ R _E_ kem( _pk_ ), where _k ∈K_ and _c_ kem _∈C_ kem. Algorithm _D_ kem is invoked as _k ← D_ kem( _sk , c_ kem), where _k ∈K ∪{_ reject _}_ and _c_ kem _∈C_ kem. We say that _E_ kem is **defined over** ( _K, C_ kem). We require that _E_ kem satisfies the following **correctness requirement** : for all possible outputs ( _pk , sk_ ) of _G_ (), and all possible outputs ( _k, c_ kem) of _E_ kem( _pk_ ), we have _D_ kem( _sk , c_ kem) = _k_ . 

We can define a notion of semantic security in terms of an attack game between a challenger and an adversary _A_ , as follows. In Experiment _b_ , for _b_ = 0 _,_ 1, the challenger computes 



and sends ( _kb, c_ kem) to _A_ . Finally, _A_ outputs<sup>ˆ</sup> _b ∈{_ 0 _,_ 1 _}_ . As usual, if _Wb_ is the event that _A_ outputs 1 in Experiment _b_ , we define _A_ ’s **advantage** with respect to _E_ kem as SSadv[ _A, E_ kem] := _|_ Pr[ _W_ 0] _−_ Pr[ _W_ 1] _|_ , and if this advantage is negligible for all efficient adversaries, we say that _E_ kem is **semantically secure** . 

460 

Now consider the **hybrid public-key encryption scheme** _E_ = ( _G, E, D_ ), constructed out of _E_ kem and _E_ s, and defined over ( _M, C_ kem _× C_ ). The key generation algorithm for _E_ is the same as that of _E_ kem. The encryption algorithm _E_ works as follows: 



The decryption algorithm _D_ works as follows: 



- (a) Prove that _E_ satisfies the correctness requirement for a public key encryption scheme, assuming _E_ kem and _E_ s satisfy their corresponding correctness requirements. 

- (b) Prove that _E_ is semantically secure, assuming that _E_ kem and _E_ s are semantically secure. You should prove a concrete security bound that says that for every adversary _A_ attacking _E_ , there are adversaries _B_ kem and _B_ s (which are elementary wrappers around _A_ ) such that 

SSadv[ _A, E_ ] _≤_ 2 _·_ SSadv[ _B_ kem _, E_ kem] + SSadv[ _B_ s _, E_ s] _._ 

- (c) Describe the KEM corresponding to _E_ TDF and prove that it is semantically secure (in the random oracle model, assuming _T_ is one way). 

- (d) Describe the KEM corresponding to _E_ EG and prove that it is semantically secure (in the random oracle model, under the CDH assumption for G). 

- (e) Let _E_ a = ( _G, E_ a _, D_ a) be a public-key encryption scheme defined over ( _K, C_ a). Define the KEM _E_ kem = ( _G, E_ kem _, D_ a), where 



Show that _E_ kem is semantically secure, assuming that _E_ a is semantically secure. 

**_Discussion:_** Part (e) shows that one can always build a KEM from a public-key encryption scheme by just using the encryption scheme to encrypt a symmetric key; however, parts (c) and (d) show that there are more direct and efficient ways to do this. 

**_11.10 (Multi-key CPA security)._** Generalize the definition of CPA security for a public-key encryption scheme to the multi-key setting. In this attack game, the adversary gets to obtain encryptions of many messages under many public keys. Show that semantic security implies multikey CPA security. You should show that security degrades linearly in _Q_ k _Q_ e, where _Q_ k is a bound on the number of keys, and _Q_ e is a bound on the number of encryption queries per key. That is, the advantage of any adversary _A_ in breaking the multi-key CPA security of a scheme is at most _Q_ k _Q_ e _· ϵ_ , where _ϵ_ is the advantage of an adversary _B_ (which is an elementary wrapper around _A_ ) that breaks the scheme’s semantic security. 

**_11.11 (A tight reduction for multiplicative ElGamal)._** We proved in Exercise 11.10 that semantic security for a public-key encryption scheme implies multi-key CPA security; however, the security degrades significantly as the number of keys and encryptions increases. Consider the 

461 

multiplicative ElGamal encryption scheme _E_ MEG from Exercise 11.5. You are to show a _tight_ reduction from multi-key CPA security for _E_ MEG to the DDH assumption, which does not degrade at all as the number of keys and encryptions increases. In particular, you should show that the advantage of any adversary _A_ in breaking the multi-key CPA security of _E_ MEG is bounded by 2( _ϵ_ + 1 _/q_ ), where _ϵ_ is the advantage of an adversary _B_ (which is an elementary wrapper around _A_ ) in the DDH attack game. 

**_Note:_** You should assume that in the multi-key CPA game, the same group G and generator _g ∈_ G is used throughout. 

**_Hint:_** Use Exercise 10.11. 

**_11.12 (An easy discrete log group)._** Let _n_ be a large integer and consider the following subset of Z<sup>_∗_</sup> _n_<sup>2:</sup> 



- (b) Which elements of G _n_ are generators? 

- (c) Choose an arbitrary generator _g ∈_ G _n_ and show that the discrete log problem in G _n_ is easy. 

**_11.13 (Paillier encryption)._** Let us construct another public-key encryption scheme ( _G, E, D_ ) that makes use of RSA composites: 

- The key generation algorithm is parameterized by a fixed value _ℓ_ and runs as follows: 

_G_ ( _ℓ_ ) := generate two distinct random _ℓ_ -bit primes _p_ and _q_ , _n ← pq, d ←_ ( _p −_ 1)( _q −_ 1) _/_ 2 _pk ← n, sk ← d_ output ( _pk , sk_ ) 

- for a given public key _pk_ = _n_ and message _m ∈{_ 0 _, . . . , n −_ 1 _}_ , set _g_ := [ _n_ + 1] _n_ 2 _∈_ Z<sup>_∗_</sup> _n_<sup>2.The</sup> encryption algorithm runs as follows: 

   - _E_ ( _pk , m_ ) := _h ←_ R Z _∗n_<sup>2</sup><sup>_,_</sup> _c ←_ R _gmhn ∈_ Z _∗n_<sup>2</sup><sup>_,_</sup> output _c_ . 

- (a) Explain how the decryption algorithm _D_ ( _sk , c_ ) works. 

**_Hint:_** Using the notation of Exercise 11.12, observe that _c_<sup>_d_</sup> falls in the subgroup G _n_ which has an easy discrete log. 

- (b) Show that this public-key encryption scheme is semantically secure under the following assumption: 

let _n_ be a product of two random _ℓ_ -bit primes, 

let _u_ be uniform in Z<sup>_∗_</sup> _n_<sup>2,</sup> let _v_ be uniform in the subgroup (Z _n_ 2)<sup>_n_</sup> := _{h_<sup>_n_</sup> : _h ∈_ Z<sup>_∗_</sup> _n_<sup>2</sup><sup>_}_,</sup> 

then the distribution ( _n, u_ ) is computationally indistinguishable from the distribution ( _n, v_ ). 

**_Discussion:_** This encryption system, called **Paillier encryption** , has a useful property called an additive homomorphism: for ciphertexts _c_ 0 _←_ R _E_ ( _pk , m_ 0) and _c_ 1 _←_ R _E_ ( _pk , m_ 1), the product _c ← c_ 0 _· c_ 1 is an encryption of _m_ 0 + _m_ 1 mod _n_ . 

462 

**_11.14 (Hash Diffie-Hellman)._** Let G be a cyclic group of prime order _q_ generated by _g ∈_ G. Let _H_ : G _→K_ be a hash function. We say that the **Hash Diffie-Hellman** (HDH) assumption holds for (G _, H_ ) if the distribution � _g_<sup>_α_</sup> _, g_<sup>_β_</sup> _, H_ ( _g_<sup>_β_</sup> _, g_<sup>_αβ_</sup> )� is computationally indistinguishable from the distribution ( _g_<sup>_α_</sup> _, g_<sup>_β_</sup> _, k_ ) where _α, β ←_ R Z _q_ and _k ←_ R _K_ . 

- (a) Show that if _H_ is modeled as a random oracle and the CDH assumption holds for G, then the HDH assumption holds for (G _, H_ ). 

- (b) Show that if _H_ is a secure KDF and the DDH assumption holds for G, then the HDH assumption holds for (G _, H_ ). 

- (c) Prove that the ElGamal public-key encryption scheme _E_ EG is semantically secure if the HDH assumption holds for (G _, H_ ). 

**_11.15 (Anonymous public-key encryption)._** Suppose _t_ people publish their public-keys _pk_ 1 _, . . . , pk t_ . Alice sends an encrypted message to one of them, say _pk_ 5, but she wants to ensure that no one (other than user 5) can tell which of the _t_ users is the intended recipient. You may assume that every user, other than user 5, who tries to decrypt Alice’s message with their secret key, obtains fail. 

- (a) Define a security model that captures this requirement. The adversary should be given _t_ public keys _pk_ 1 _, . . . , pk t_ and it then selects the message _m_ that Alice sends. Upon receiving a challenge ciphertext, the adversary should learn nothing about which of the _t_ public keys is the intended recipient. A system that has this property is said to be **an anonymous public-key encryption scheme** . 

- (b) Show that the ElGamal public-key encryption system _E_ EG is anonymous. 

- (c) Show that the RSA public-key encryption system _E_ RSA is not anonymous. Assume that all _t_ public keys are generated using the same RSA parameters _ℓ_ and _e_ . 

**_11.16 (Proxy re-encryption)._** Bob works for the Acme corporation and publishes a public-key _pk_ bob so that all incoming emails to Bob are encrypted under _pk_ bob. When Bob goes on vacation he instructs the company’s mail server to forward all his incoming encrypted email to Alice. Alice’s public key is _pk_ alice. The mail server needs a way to translate an email encrypted under public-key _pk_ bob into an email encrypted under public-key _pk_ alice. This would be easy if the mail server had _sk_ bob, but then the mail server can read all of Bob’s incoming email. 

- Consider the variation _E_ EG<sup>_′_of</sup><sup>_E_EG,inwhichweonlyhash</sup><sup>_w_insteadof(</sup><sup>_v, w_)(seeRemark11.1).</sup> Suppose that _pk_ bob and _pk_ alice are public keys for _E_ EG<sup>_′_.Then the mail server can do the translation</sup> from _pk_ bob to _pk_ alice while learning nothing about the email contents. (a) Suppose _pk_ alice = _g_<sup>_α_</sup> and _pk_ bob = _g_<sup>_α′_</sup> . Show that giving _τ_ := _α_<sup>_′_</sup> _/α_ to the mail server lets it translate an email encrypted under _pk_ bob into an email encrypted under _pk_ alice, and vice-versa. 

- (b) Assume that _E_ EG<sup>_′_issemanticallysecure.Showthattheadversarycannotbreaksemantic</sup> security for Alice, even if it is given Bob’s public key _g_<sup>_α′_</sup> along with the translation key _τ_ . 

**_11.17 (Online-offline oblivious transfer)._** In Section 11.6 we looked at protocols for oblivious transfer where one party, a sender, has messages _m_ 0 _, . . . , mn−_ 1 _∈M_ = _{_ 0 _,_ 1 _}_<sup>_ℓ_</sup> and another party, a 

463 

receiver, has an index _i ∈_ Z _n_ . At the end of the protocol the receiver has _mi_ and neither party learns anything else about the other party’s data. Suppose that the sender and receiver had previously established a random OT instance: the sender has random _x_ 0 _, . . . , xn−_ 1 _←_ R _M_ and the recipient has ( _j, xj_ ) for some random _j ←_ R Z _n_ . This random data is called an **OT correlation** . Let’s show that the parties can quickly solve the OT problem using a random OT correlation: 

Sender has: ( _m_ 1 _, . . . , mn−_ 1) _,_ ( _x_ 1 _, . . . , xn−_ 1), Receiver has: ( _i, j, xj_ ). Receiver wants _mi_ . 

   - _receiver:_ compute ∆ _←_ ( _j − i_ ) _∈_ Z _n_ and send ∆to the sender. 

   - _sender:_ for all _u_ = 0 _,_ 1 _, . . . , n −_ 1 compute _cu ← mu ⊕ x_ ( _u_ +∆) and send _C_ := ( _c_ 0 _, . . . , cn−_ 1) to the receiver. Here each index ( _u_ + ∆) is an element in Z _n_ . 

   - _receiver:_ output _ci ⊕ xj_ . 

- (a) Show that when both parties honestly follow the protocol, the receiver learns the required value _mi_ . 

- (b) Prove that a (possibly malicious) sender learns nothing about the receiver’s index _i_ . Similarly, a (possibly malicious) receiver cannot learn anything about any messages besides _mi_ , where _i_ is defined as _i_ := _j −_ ∆ _∈_ Z _n_ . 

- (c) A protocol between the sender and the receiver that constructs a random OT correlation, but where the recipient can choose the _j ∈_ Z _n_ that it wants, is called a **Random OT** or **ROT protocol** . ROT is discussed in more detail in Section 23.7. Adapt the protocol in this exercise to show that an ROT protocol implies an OT protocol. 

**_11.18 (All-but-one oblivious transfer)._ All-but-one OT** refers to the following problem: a sender has messages _m_ 1 _, . . . , mn ∈M_ and a receiver has an index _i ∈{_ 1 _, . . . , n}_ . At the end of the protocol the receiver should have all of _m_ 1 _, . . . , mn except_ for message _mi_ . Neither party should learn anything else about the other party’s inputs. Here is a simple solution using a CPA secure cipher ( _E, D_ ) defined over ( _K, M, C_ ): 

- _sender:_ choose a random _k_ in _K_ and compute _cj ←_ R _E_ ( _k, mj_ ) for _j_ = 1 _, . . . , n_ . 

- _both:_ the sender and receiver engage in _n_ independent 1-out-of-2 oblivious transfers. In OT instance number _j_ = 1 _, . . . , n_ , the sender’s input data is the pair ( _cj, k_ ), and the receiver’s input data is a bit _bj_ in _{_ 0 _,_ 1 _}_ . 

- (a) Explain why the receiver can learn at most _n −_ 1 of the sender’s messages, and why the sender learns nothing about _i_ . 

- (b) Generalize the protocol to allow the receiver to learn at most _n −_ 2 of the sender’s messages, where the sender learns nothing about which ones. 

**_Hint:_** try using _n_ independent 1-out-of-3 oblivious transfers. 

**_Discussion:_** The all-but-one OT protocol described in this problem make use of _n_ executions of 1-out-of-2 oblivious transfer. It is possible to implement all-but-one OT using only log2 _n_ executions of 1-out-of-2 oblivious transfer [142, Sec. 3]. 

464 

**_11.19 (Expanding oblivious transfer)._** Suppose we are given a 1-out-of-2 oblivious transfer protocol Π. We wish to construct a 1-out-of- _n_ oblivious transfer protocol, for _n >_ 2, using only _⌈_ log2 _n⌉_ parallel executions of the 1-out-of-2 OT protocol Π. For simplicity assume _n_ = 2<sup>_t_</sup> , for some _t >_ 1. We will need a hash function _H_ : _K_<sup>_t_</sup> _→K_<sup>_′_</sup> , where _K_ := _{_ 0 _,_ 1 _}_<sup>_w_</sup> , and a cipher ( _E, D_ ) defined over ( _K_<sup>_′_</sup> _, M, C_ ). The 1-out-of- _n_ OT protocol Π<sup>_′_</sup> works as follows: 

Sender has: _m_ 0 _, . . . , mn−_ 1 _∈M_ , Receiver has: _i ∈{_ 0 _, . . . , n −_ 1 _}_ . Receiver wants _mi_ . 

- _Setup:_ The receiver computes the binary representation of _i_ , namely _b_ 0 _, . . . , bt−_ 1 _∈{_ 0 _,_ 1 _}_ so that _i_ =<sup>�</sup><sup>_t_</sup> _j_<sup>_−_</sup> =0<sup>12</sup><sup>_jbj_.Thesenderchoosesrandom</sup><sup>_kj_[0]</sup><sup>_, kj_[1]</sup><sup>_←_</sup> R _K_ for _j_ = 0 _, . . . , t −_ 1. 

- _OT phase:_ The sender and receiver engage in _t_ = log2 _n_ parallel instances of the 1-outof-2 OT protocol Π, where in instance number _j_ , the receiver’s input is _bj ∈{_ 0 _,_ 1 _}_ , and the sender’s input is ( _kj_ [0] _, kj_ [1]) _∈K_<sup>2</sup> . At the end of the _t_ protocols, the receiver has _k_ 0[ _b_ 0] _, . . . , kt−_ 1[ _bt−_ 1] _∈K_ . 

- _Sender:_ For _ℓ_ = 0 _, . . . , n −_ 1, the sender does: 

   - let _d_ 0 _, . . . , dt−_ 1 _∈{_ 0 _,_ 1 _}_ be the binary representation of _ℓ_ , 

   - compute _kℓ ← H_ � _k_ 0[ _d_ 0] _, . . . , kt−_ 1[ _dt−_ 1]� _∈K_<sup>_′_</sup> , 

   - **–** set _cℓ ←_ R _E_ ( _kℓ, mℓ_ ). 

Send **_c_** = ( _c_ 0 _, . . . , cn−_ 1) to the receiver. 

- _Receiver:_ set _k_<sup>ˆ</sup> _i ← H_ � _k_ 0[ _b_ 0] _, . . . , kt−_ 1[ _bt−_ 1]� _∈K_<sup>_′_</sup> and outputs _m_ ˆ _← D_ ( _k_<sup>ˆ</sup> _i, ci_ ). 

- (a) Show that protocol Π<sup>_′_</sup> is correct so that _m_ ˆ = _mi_ . 

- (b) Let’s prove a minimal security property for Π<sup>_′_</sup> : the receiver learns nothing about _mj_ for _j̸_ = _i_ . More precisely, consider an efficient adversary that outputs _i_ , and two tuples **_m_** = ( _m_ 0 _, . . . , mn−_ 1) and **_m_**<sup>_′_</sup> = ( _m_<sup>_′_</sup> 0<sup>_, . . . , m_</sup> _n_<sup>_′_</sup> _−_ 1<sup>),with</sup><sup>_mi_=</sup><sup>_m′_</sup> _i_<sup>.Theadversarythenplaystherole</sup> of receiver with input _i_ . We say that Π<sup>_′_</sup> is secure if the adversary cannot distinguish the message **_c_** from the sender when the sender takes **_m_** as input versus when the sender takes **_m_**<sup>_′_</sup> as input. Show that when _H_ is modeled as a random oracle, ( _E, D_ ) is semantically secure, and the 1-out-of-2 OT protocol Π is secure, then the 1-out-of- _n_ OT protocol Π<sup>_′_</sup> is secure. 

**_Discussion:_** This protocol runs many instances of 1-out-of-2 OT. In Section 23.7 we will show how to efficiently construct many instances of 1-out-of-2 OT, as needed here, using a technique called OT extension. 

**_11.20 (An attack on oblivious transfer)._** The first OT protocol presented in Section 11.6 is subject to a kind of “man in the middle” attack, when multiple instances of the protocol may run concurrently. However, the protocol is easily repaired to prevent this. Suppose we run _two_ OT instances of the protocol: in one instance the adversary plays the role of receiver and interacts with an honest sender; in the other instance the adversary plays the role of sender and interacts with an honest receiver. The adversary relays all messages unchanged between the honest sender and honest receiver. However, when the honest receiver sends _u_ := _g_<sup>_α_</sup> _v_<sup>_−i_</sup> , the adversary sends _u_ ˆ := _u · v_ to the honest sender. 

- (a) Show that when the two OT protocols complete, the receiver incorrectly obtains the message _mi−_ 1 instead of _mi_ . You may assume _i >_ 1. 

465 

- (b) Now suppose we modify the protocol along the lines discussed on Remark 11.2. Explain why the attack does not work any more. 

**_Discussion:_** One of the reasons we use secure channels in this protocol is to prevent the adversary from modifying protocol messages. However, even with secure channels, the protocol as stated is subject to a “man in the middle attack” as illustrated in this exercise. Here, the honest sender and honest receiver are talking to the adversary in two separate protocol instances, and we would ideally like these two protocol instances to run relatively independently of each other, without any “strange interactions” between them, such as that illustrated in this exercise. This type of subtle attack, which arises when we run protocol instances concurrently, is one reason why we need better security definitions for interactive protocols, which we will discuss in Section 23.5. 

**_11.21 (Group key exchange)._** Suppose _n_ parties _A_ 1 _, . . . , An_ want to setup a group secret key that they can use for encrypted group messaging. They have at their disposal a public bulletin board that they can all post messages to and whose contents are public. No peer-to-peer communication is allowed. Your goal is to design a group key exchange that is secure against passive eavesdropping. 

- (a) Use a public-key encryption scheme ( _G, E, D_ ) to design a protocol that runs in two rounds. One party, say _A_ 1, reads and writes _n_ values to and from the bulletin board. All other parties read and write only one value to the bulletin board. 

- (b) Define a security model for a group key exchange protocol that ensures security against a passive eavesdropper. Prove security of your protocol from part (a) assuming ( _G, E, D_ ) is semantically secure. 

- (c) Design a fairer protocol where the work is evenly distributed. Specifically, design a protocol based on two-party Diffie-Hellman that runs in at most _k_ rounds, assuming _n_ = 2<sup>_k_</sup> . Let G be a group of prime order _q_ with generator _g ∈_ G and let _H_ : G _→_ Z _q_ be a hash function. In each round every party posts at most one group element in G to the bulletin board and reads at most one group element from the bulletin board. At the end of the protocol, after _k_ rounds, all parties obtain the same secret key and there are at most 2 _n_ group elements on the bulletin board. You may assume that the parties know each other’s identities and can order themselves lexicographically by identity. 

**_Hint:_** think of an _n_ -leaf binary tree where each leaf corresponds to one party. 

- (d) Prove security of your protocol from part (c) against a passive eavesdropper assuming CDH holds in G and _H_ is modeled as a random oracle. 

- (e) Suppose one of the _n_ parties has a malfunctioning random number generator — whenever that party needs to sample a random value, its random number generator always returns “5”. Show that this will sink the entire protocol from parts (a) and (c), even if all the other participants have well functioning random number generators. Specifically, show that an eavesdropper will learn the group secret key. 

- (f) Is it possible to design a group key exchange protocol that is secure even if at most one of the participants suffers from the problem in part (e)? 

466 

