# Enhancing Privacy with multiple DID and merkle trees of multiple (claim+Public Key) 

by  [Frédéric Martin](mailto:frederic.martin@mydid.com), myDID SA  
and [Imad El Aouny](mailto:imad.elaouny@mydid.com), myDID SA

RWOT XI, 2022, September

## DID Privacy concerns 

We developed a DID+VC Wallet following W3C specifications and were not fully satisfied how Identifiers are used by default everywhere all the time and how easily different actors like credential verifiers (or even websites only using authentication) can match / correlate / link / trace users between services.

did:OWND:ID4242 presented a verifiable credential about his age on nop.com in order to gamble
did:OWND:ID4242 presented a verifiable credential about his diploma on qrs.com site in order to postulate
did:OWND:ID4242 presented a verifiable credential about his address on tuv.org in order to participate to a local party
did:OWND:ID4242 only used his wallet to be authenticated, no credential, during a simple registration on a xyz.com metaverse where he chose a fancy avatar.
Websites owners or even website hackers can accumulate data to correlate users identity.

As a wallet solution provider we can ease and incite users to create 

For each user, DID (identifier) is a central information used everywhere.

Verifiable Issuer, 

Since DID are usually exposed to credentials verifiers and even simple authentication

e.g. Issuer is a KYC processor, given credential is user citizenship 



Alice is UK, Bob iS US, Carol is AU
```
                            ┌──A──< Alice Derived Public key 1, Claim (UK)
                     ┌──AB──┤     
                     │      └──B──< Alice Derived Public key 2, Claim (UK)
            ┌──ABCD──┤  
            │        │      ┌──C──< Bob Derived Public key 1, Claim (US)
            │        └--CD--│     
            │               └──D──< Alice Derived Public key 3, Claim (UK)
──ABCDEFGH──┤  
            │               ┌──E──< Carol Derived Public key 1, Claim (AU)
            │        ┌──EF──│     
            │        │      └──F──< Bob Derived Public key 2, Claim (US)
            └──EFGH──┤  
                     │      ┌──G──< Carol Derived Public key 2, Claim (AU)
                     └──GH──│     
                            └──H──< Alice Derived Public key 4, Claim (UK)

```
  
Proponents (such as myself) of zero-knowledge proofs and their use with
Verifiable Credentials often assert that the additional complications introduced
by such cryptographic techniques are necessary because they provide a means to
reduce the number of correlating factors shared during the exchange of VCs.

These VC-related correlating factors may include:
- claims made about a credential subject
- credential metadata, such as: 
  - subject identifiers
  - credential identifiers
  - revocation registry information
  - issuance and expiration timestamps
- credential signatures

The purpose of this document is to stimulate a conversation around efforts to
reduce correlating factors, for which use cases such efforts make sense, and to
what extent a reduction in correlating factors should be sought.

## Techniques for Reducing Correlation
Most techniques for reducing correlation involve either giving the holder power
to only share subsets of verifiable data, or involve removing from interactions
the necessity of sharing static values. There is an interest in finding ways to
make it unnecessary to share such static values, and to put as much power to
determine precisely what data to share into the hands of the holder as possible.

### Selective Disclosure
Selective disclosure is the ability of a holder to only share a subset of data
from a credential with a verifier. The fewer number of claims and pieces of
metadata from a credential are shared, the fewer correlating factors end up in
the databases of verifiers.
