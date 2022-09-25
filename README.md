# Enhancing DID Privacy through shared Credential using merkle trees of DIDs and Claims with multiple presentation proofs

by  [Frédéric Martin](mailto:frederic.martin@mydid.com), myDID SA  
and [Imad El Aouny](mailto:imad.elaouny@mydid.com), myDID SA

RWOT XI, 2022, September

## DID Privacy concerns 

We developed a DID+VC Wallet following W3C specifications and were not fully satisfied how Identifiers are used by default everywhere all the time and how easily different actors like credential verifiers (or even websites only using authentication) can match / correlate / link / trace users between services.  
  
User with did:OWND:ID4242...  
* presented a verifiable credential about his age and nationality in order to bet on nop.net gambling website
* presented two verifiable credentials about his name and diploma on qrs.com site in order to postulate  
* presented a verifiable credential about his age and home address on tuv.com in order to buy alcohol online
* only used his DID wallet just to be authenticated, no credential asked, during registration on xyz.io metaverse website where he chose a fancy avatar...  
* ...  
Websites databases can accumulate data to enable correlation between sensible personal information / attributes through stored DID "did:OWND:ID4242"
Service owners can choose to do this willingly or they can be forced to do so and or even simply ignore it can be done behind their backs (stolen/leaked databases).  

## Existing potential solutions 

* Verifiers, website/application owners can avoid to store DIDs -> no guarantee, not always so easy -> it probably won't happen.
* Trusted Third Partys can be involved to anonymize identity and proof -> aren't we supposed to be able to get rif of TTP?
* ZKP/MPC based solutions can help sometimes but usually with some drawbacks -> not always well understood and well implemented, it may involve new constraints about client side cryptography support and new protection mecanism for the private key storage and usage -> it may involve other smart contracts, other parties...
* As a wallet solution provider, we can incite users to create several "accounts / DIDs" to separate / isolate digital identities -> if it is not done transparently and nearly automatically, we doubt it will become reality (People can create several accounts on Metamask wallets for their own sake and they mostly don't)  

## New* kind of "shared credential with multiple preentation proofs"?

*Note: it may have been already proposed and even implemented somewhere we are not aware of  
  
We will be required to attribute several derived key pairs to each users: we probably should adopt Hierarchical Deterministic wallets standards like [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki), [BIP44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) and "extensions" [SLIP44](https://github.com/satoshilabs/slips/blob/master/slip-0044.md).

Example assumptions:

Issuer is a KYC processor, requested credentials are citizenship and year of birth.
Issuer DID is did:example:155cadf51fa199664e6f31aa13b.
Alice is from UK, Bob is from USA, Carol is from Australia.
Year of birth for Alice is 1998 years old, Bob 1974 and Carol 1942.

These are Merle Trees: ──A── means (hash of associated data), ──AB── means (hash of (hash of A + hash of B)) and so on.

```
                            ┌──A──< Alice Derived Public key 1, Claim (YoB = 1998)
                     ┌──AB──┤     
                     │      └──B──< Alice Derived Public key 2, Claim (YoB = 1998)
            ┌──ABCD──┤  
            │        │      ┌──C──< Bob Derived Public key 1, Claim (YoB = 1974)
            │        └──CD──┤     
            │               └──D──< Alice Derived Public key 95, Claim (YoB = 1998)
──ABCDEFGH──┤  
            │               ┌──E──< Carol Derived Public key 1, Claim (YoB = 1942)
            │        ┌──EF──┤     
            │        │      └──F──< Bob Derived Public key 2, Claim (YoB = 1974)
            └──EFGH──┤  
                     │      ┌──G──< Carol Derived Public key 2, Claim (YoB = 1942)
                     └──GH──┤     
                            └──H──< Alice Derived Public key 96, Claim (YoB = 1998)

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
            │               ┌──M──< Alice Derived Public key 1, Claim (citizenship = AU)
            │        ┌──MN──┤     
            │        │      └──N──< Alice Derived Public key 2, Claim (citizenship = US)
            └──MNOP──┤  
                     │      ┌──O──< Carol Derived Public key 50, Claim (citizenship = AU)
                     └──OP──┤     
                            └──P──< Bob Derived Public key 87, Claim (citizenship = UK)

```
As shown here, the two merkle trees for the two credentials contains a few derived keys  
It could be useful if you are still required to at least have the same key / DDO during the presentation of several credentials.

We chose above here to directly reference users public derived keys but it could be a DDO (DID Document) reference containing a derived key  
(assuming having multiple DID Documents is not too expensive and by the way credentials costs are largely reduced here)
With indirect DDO reference beginning of example above would be:

             ┌──I──< Carol DDO1 (containing Carol Derived Public key 1), Claim (citizenship = UK)
      ┌──IJ──┤     
      │      └──J──< Bob DDO1 (containing Bob Derived Public key 1) Claim (citizenship = UK)


In this new model, credentials are still signed by the issuer as the credential "proof" part but:  
* user DID (credentialSubect / id) is the root of the Merkle Tree of accumulated aasociation of derived public key (or DDO) and claim content
* direct claim assertion is empty.

if we take all example assumptions described above,  
"Year of Birth" credential would be :  

```
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "id": "http://example.edu/credentials/1121",
  "type": ["VerifiableCredential", "SharedCredential"],
  "issuer": "did:example:155cadf51fa199664e6f31aa13b"
  "issuanceDate": "2022-09-01T02:21:42Z",
  "credentialSubject": {
    "id": "did:example:ABCDDECGH",
    "SharedCredential": {
        "name": [{
        "value": "Year of Birth",
        "lang": "en"
      }, {
        "value": "Année de naissance",
        "lang": "fr"
      }]
    }
  },
  "proof": {
    "type": "RsaSignature2018",
    "created": "2022-09-01T04:14:03Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:example:155cadf51fa199664e6f31aa13b#key-1",
    "jws": "eyJh[...]BBPM"
  }
}
```
... and "Citizenship" credential would be :  

```
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "id": "http://example.edu/credentials/1121",
  "type": ["VerifiableCredential", "SharedCredential"],
  "issuer": "did:example:155cadf51fa199664e6f31aa13b"
  "issuanceDate": "2022-09-01T03:14:21Z",
  "credentialSubject": {
    "id": "did:example:IJKLMNOP",
    "SharedCredential": {
        "name": [{
        "value": "Citizenship",
        "lang": "en"
      }, {
        "value": "Citoyenneté",
        "lang": "fr"
      }]
    }
  },
  "proof": {
    "type": "RsaSignature2018",
    "created": "2022-09-01T07:03:02Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:example:155cadf51fa199664e6f31aa13b#key-1",
    "jws": "imHm[...]CKZA"
  }
}
```
### Step by step, from credential request to presentation verification

User requests Verfiable Credential from Issuer

* User sends a sublevel master public key to the issuer  
* Issuer acumulates user requests (from other users)
* Issuer derived public keys from users master public keys and create several association of user public key pairs and claim contents
* Issuer uses accumulated data assocations to build a Merkle Tree
* Issuer creates Verifiable Credential markes as issued "to" a "shared" DID referenced by Merkle Tree Root
* Issuer sends the same credential reference to each user but with additional merkle proofs and associated key derivation paths
* User stores 

User sends Verifiable Presention to Verifier  
  
* User signs and sends Verifiable Credential as Verifiable Presentation to Verifier with an additional information: a claim content, a key derivation path and a merkle proof
* Verifier uses credential and additionnal information to check if (Claim and derived public key) asociation is present inside the Merkle tree
* Verifier checks original Issuer Credential signature
* Verifier checks User Preentation signature

### Notes ("Work in Progress")

Advantages:
- Enhanced Privacy
  - a same credential content can be claimed/proven by a user through different keys and proofs
  - a same credential can contain différent claims for different users
- some "shared" claims can be publicly stored with less cost and less privacy concerns
- some new "shared claims" can be added to a bigger merkle by an issuer

Drawbacks / to be discussed
- AFAWK, not present yet inside any standards / specifications
- If we want to take advantage of an updated growing merkle tree for existing we have to inform previous users about the update, meaning keeping a kind of opened communication channel between Issuers and their previous users (Anyway, we think this kind of channel opportunity i simportant -users should be able to closed/ignore these channels on their user side at any time- is important to create so we are working on it)
- Issuers have to send each credential with additional information: a group of Merlkle proofs (note: it can be encrypted with the first derived public key)
- Users have to send each credential with additional information: a Merlkle proof


