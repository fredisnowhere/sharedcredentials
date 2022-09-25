# Enhancing Privacy with multiple DID and merkle trees of multiple (claim+Public Key) 

by  [Frédéric Martin](mailto:frederic.martin@mydid.com), myDID SA  
and [Imad El Aouny](mailto:imad.elaouny@mydid.com), myDID SA

RWOT XI, 2022, September

## DID Privacy concerns 

We developed a DID+VC Wallet following W3C specifications and were not fully satisfied how Identifiers are used by default everywhere all the time and how easily different actors like credential verifiers (or even websites only using authentication) can match / correlate / link / trace users between services.
did:OWND:ID4242
* presented a verifiable credential about his age and nationality in order to bet on nop.net gambling website
* presented two verifiable credentials about his name and diploma on qrs.com site in order to postulate  
* presented a verifiable credential about his age and home address on tuv.com in order to participate to buy alcohol online
* only used his DID wallet just to be authenticated, no credential asked, during registration on xyz.io metaverse website where he chose a fancy avatar...  
  
Websites databases can accumulate data to enable correlation between sensible personal information / attributes through stored DID
Service owners can choose to do this willingly or they can be forced to do so and or even simply ignore it can be done behind their backs (stolen/leaked databases).  

## Existing / potential solutions 

* Verifiers, website/application owners can avoid to store DIDs -> no guarantee, not always so easy -> it probably won't happen.
* Trusted Third Partys can be involved to anonymize identity and proof -> aren't we supposed to be able to get rif of TTP?
* ZKP/MPC based solutions can help sometimes but usually with some drawbacks -> not always well understood and well implemented, it may involve new constraints about client side cryptography support and new protection mecanism for the private key storage and usage -> it may involve other smart contracts, other parties...
* As a wallet solution provider, we can incite users to create several "accounts / DIDs" to separate / isolate digital identities -> if it is not done transparently and nearly automatically, we doubt it will become reality (People can create several accounts on Metamask wallets for their own sake and they mostly don't)  

## New kind of "shared credential with several possibles proofs"

Note: it may have been already proposed and even implemented somewhere we are not aware of

We will be required to attribute several derived key

Example assumptions:

Alice is from UK, Bob is from USA, Carol is from AUstralia.
Year of birth for Alice is 1998 years old, Bob 1974 and Carol 1942.
Issuer is a KYC processor, given credentials are here user citizenship and year of birth.

This are Merle Trees: ──A── means hash of associated data, ──AB── means hash of (hash of A + hash of B) and so on.

```
                            ┌──A──< Alice Derived Public key 1, Claim (YoB = 1998)
                     ┌──AB──┤     
                     │      └──B──< Alice Derived Public key 2, Claim (YoB = 1998)
            ┌──ABCD──┤  
            │        │      ┌──C──< Bob Derived Public key 1, Claim (YoB = 1974)
            │        └──CD──┤     
            │               └──D──< Alice Derived Public key 3, Claim (YoB = 1998)
──ABCDEFGH──┤  
            │               ┌──E──< Carol Derived Public key 1, Claim (YoB = 1942)
            │        ┌──EF──┤     
            │        │      └──F──< Bob Derived Public key 2, Claim (YoB = 1974)
            └──EFGH──┤  
                     │      ┌──G──< Carol Derived Public key 2, Claim (YoB = 1942)
                     └──GH──┤     
                            └──H──< Alice Derived Public key 4, Claim (YoB = 1998)

```
```
                            ┌──I──< Carol Derived Public key 1, Claim (citizenship = UK)
                     ┌──IJ──┤     
                     │      └──J──< Bob Derived Public key 1, Claim (citizenship = UK)
            ┌──IJKL──┤  
            │        │      ┌──K──< Bob Derived Public key 2, Claim (citizenship = US)
            │        └──KL──┤     
            │               └──L──< Carol Derived Public key 2, Claim (citizenship = UK)
──IJKLMNOP──┤  
            │               ┌──M──< Alice Derived Public key 2, Claim (citizenship = AU)
            │        ┌──MN──┤     
            │        │      └──N──< Alice Derived Public key 1, Claim (citizenship = US)
            └──MNOP──┤  
                     │      ┌──O──< Carol  Derived Public key 50, Claim (citizenship = AU)
                     └──OP──┤     
                            └──P──< Bob Derived Public key 86, Claim (citizenship = UK)

```
  

### Selective Disclosure
Advantages:
- some "shared" claims can be publicly stored with less cost and less privacy concerns

Drawbacks:
- AFAIK, not present yet inside any standards / specifications
- Issuers have more work to do
- Issuers have to send each credential with additional information: a group of Merlkle proofs
