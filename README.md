# Enhancing Privacy with multiple DID and merkle trees of multiple (claim+Public Key) 

by  [Frédéric Martin](mailto:frederic.martin@mydid.com), myDID SA
and [Imad ElAouny](mailto:imad.elaouny@mydid.com), myDID SA

Since DID are usually exposed to credentails verifiers and even simple authentication

e.g. Issuer is a KYC processor, Alice and Bob

               ┌ --A--< Alice Derived Public key 1 , Claim UK
        ┌--AB--│     
        │      └--B--< Alice Derived Public key 2 , Claim UK
 sqdqs
        │      └--B--< Alice Derived Public key 2 , Claim UK
                │      └--B--< Alice Derived Public key 2 , Claim UK
                        │      └--B--< Alice Derived Public key 2 , Claim UK
        │      ┌--B--< Alice Derived Public key 2 , Claim UK
        │      
        │      └--B--< Alice Derived Public key 2 , Claim UK

  ABCD--│
        |        ┌--C--< Bob Derived Public key 1, Claim (US)
        └--CD--|
                 \--D--< Alice Derived Public key 3, Claim (UK)

  
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
