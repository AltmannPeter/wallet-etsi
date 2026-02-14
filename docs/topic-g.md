# ZKP requirements from Topic G

[Topic G](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/g-zero-knowledge-proof.md) defines HLR for integrating ZKP into the EUDIW ARF. It evaluates ZKP as a privacy-enhancing technology and specifices foundational requirements for its future integration. It aligns technical requirements with legal mandates on selective disclosure, unlinkability, and anti-profiling obligations.

## Legal requirements

### Regulation (EU) 2024/1183

[Regulation (EU) 2024/1183](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202401183) discusses the use of privacy-preserving technologies in Recital 14:

> Member States should integrate different privacy-preserving technologies, such as zero knowledge proof, into the European Digital Identity Wallet. Those cryptographic methods should allow a relying party to validate whether a given statement based on the person’s identification data and attestation of attributes is true, without revealing any data on which that statement is based

Notice that Recital 14 places no restriction on the type of statement a relying party may validate, nor on how such statements relate to identification data. This openness suggests that the provision applies to any claim derivable from a person’s identity attributes. Recital 15 builds on this by outlining functional expectations for the wallet and identifying features:

> [Wallet users] should be empowered to securely request, select, combine, store, delete, share and present data related to their identity [...] while enabling selective disclosure of personal data. [...] aiming towards the highest level of security, privacy, user convenience [...].

Recital 15 packs multiple goals into one breath: empower users to "request, select, combine, store, delete, share and present" identity data via selective disclosure, all while simultanously requireing the highest levels of security, privacy etc. No guidance is offered on how to resolve design tensions or tradeoffs.

[Section 4.1](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/g-zero-knowledge-proof.md#41-expectations-from-zkp-systems) provides clarity by first outlining what non-ZKP approaches can achieve—like verifier-unlinkable selective disclosure through batch issuance. It then pivots by stating that certain privacy properties can only be ensured through ZKP. Consider fully unlinkable attestations as an example. These demand presentations that expose no correlation handles that can be used to link presentations together. No practical non-ZKP solutions can meet this need. Where full unlinkability matters, ZKP is the only viable alternative.

These strengths matter for several of the functional requirements stated in [Article 5a](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202401183#sct_1):

* Manage (request, obtain, select, combine, store, delete, share and present using selective disclosure) personal identification data possibly in combination with electronic attestations of attributes, to authenticate to service providers (4a).
* Manage pseudonyms locally using their wallet (4b).
* Not provide any information to TSPs of EAAs about the use of EAAs (5b).
* Meet assurance level high requirements (5d).
* The PID must be bound to the user's EUDIW (5f).
* No party shall be able to obtain data that allows transactions or user behavior to be tracked, linked, or correlated after the issuance of an EAA (16a).
* Ensure privacy preserving techniques which ensure unlinkability (16b).

These requirements, especially requirements for unlinkability, device binding, and pseudonym management, collectively position ZKP as a strong candidate for high privacy scenarios.

> NOTE: The legal text does not define the concept of unlinkability. Instead, the Discussion Paper on Topic G refers to ETSI TR 119 476 for a formal definition. According to ETSI, unlinkability is defined as a privacy property that persists across multiple presentations (multi-show unlinkability). It can take two forms:
> 1) Verifier unlinkability: When data observed by relying parties cannot be linked across presentations, and
> 2) Full unlinkability: When linkage remains impossible even if issuers and verifiers collude or share information.

### Deriving ZKP requirements

Below is a table eliciting ZKP requirements from the above discussed sources.

| Source                                                                                 | Mapping                                                                         | ZKP requirement                   | Use case example                                                          |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|-----------------------------------|---------------------------------------------------------------------------|
| Recital 14 (validate whether a statement is true without revealing anything else)      | Completeness, soundness, zero-knowledgeness                                     | These are core ZKP properties     | N/A                                                                       |
| Recital 14 + Topic G ("given statement" from attributes; implied for derivable claims) | Various predicate proofs about attribute values                                 | Common predicates (to be defined) | Age proofs (e.g., 18 < age < 65) Set membership proofs (e.g., is student) |
| Article 5a (4a, 5b, 16) + Topic G section 4.1 (full unlinkability)                     | Randomized proofs across presentations to avoid correlation handles             | Multi-show full unlinkability     | All use cases where user does not reveal identity                         |
| Article 5a(4a) (combine PID + electronic attestations)                                 | Prove statements from multiple sources and prove that they are properly paired  | Combined presentation             | Vaccination proof (vaccination card linked to PID)                        |
| Article 5a(5f) (PID bound to user's EUDIW)                                             | Prove that a signature validates with public key in the attestation's PoP field | Hardware bound PoP                | LoA High PID presentation                                                 |
| Article 5a(4b) + Topic G Section 4.1 (pseudonym management)                            | Prove a pseudonym was derived from an attribute in an attestation               | Pseudonym derivation              | Identity-bound stable pseudonyms                                          |

Additional ZKP requirements in Topic G are explored next.

## ZKP requirements

The Topic G text describes ZKP use cases and privacy properties only at a functional level (what a ZKP scheme should enable), or by mentioning certain higher-level statements relevant for the EUDIW, including but not limited to age proofs, validity status checks, issuer hiding, pseudonymous authentication, combined presentations from multiple attestations, and blind issuance.. It is thus unclear what the concrete ZKP requirements are.

Analyzing use cases offers some guidance. Topic G notes that some use cases—such as opening a bank account—may require full identity verification rendering unlinkability requirements moot. Others only need proof of a specific attribute or property, such as an age proof. This distinction is recognized by the Regulation:

> Relying parties shall not refuse the use of pseudonyms, where the identification of the user is not required by Union or national law. - [Regulation (EU) 2024/1183](https://eur-lex.europa.eu/eli/reg/2024/1183/oj/eng) Article 5b, 9

> enable privacy preserving techniques which ensure unlinkability, where the attestation of attributes does not require the identification of the user. - 910/2014 Article 5a, 16(b)

> Providers of person identification data shall enable privacy preserving techniques which ensure unlinkability where the electronic attestations of attributes do not require the identification of the user. - [CIR 2024/2977](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202402977) Article 5, 8

> enable privacy preserving techniques which ensure unlinkability where the electronic attestations of attributes do not require the identification of the wallet user, when presenting attestations or person identification data across different wallet-relying parties. - [CIR 2024/2982](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202402982) Article 3, 10

One way to elicit ZKP requirement is thus by analyzing use case where user identification is not required. Topic G provides some suggestions:

* **Selective disclosure of identity attributes**. Use cases where the user needs to prove that they possess a PID or attestation that includes a specific attribute, or a valid predicate of this attribute, without revealing any additional information about their identity may use a ZKP scheme.
* **Proof of possession of an attestatio type**. In some use cases, it may be enough that the user proves that they are in possession of a particular attestation type. For instance, stores offering various discounts to certain groups of users, e.g., students, may require only proof of possession of a student card.
* **Pseudonymous authentication**. Service providers may have account management needs (returning customer, known-offender matching, account recovery etc.) that can be met using ZKP. A pseudonym can be derived using an attribute from a user attestation and a public context to derive a service-specific pseudonym. Non-ZKP ways cannot meet this need without some tradeoff (e.g., requiring a third party to do the derivation, revealing the presentation target to the issuer, or limiting the number of pseudonyms the user can have etc.).

From these use cases, Topic G derives properties that a ZKP solution must have. A ZKP scheme must provide a proof that:

1. the PID or attestation includes the revealed attribute (or predicate thereof)
2. a validity status proof of the PID or attestation that includes the attribute
3. the PID or attestation is issued by a trusted issuer, optionally without revealing the issuer, and
4. The PID or attestation is is bound to a key stored in the WSCD of the wallet unit.
5. The pseudonym seed is present in a user attestation without revealing this seed
6. The pseudonym seed is hidden from the attestation issuer

> This text ignores requirement 6 as this is not always desirable. Unmaskability is a requirement only in certain pseudonym use cases and is not generally desirable. Also, it does not require blind issuance to satisfy, but can be met with other mechanisms. A better requirement would be to state that the issuer cannot derive the pseudonym as opposed to not knowing the seed.

The document further details some solution constraints:

1. Any solution must support privacy-preserving device binding. Specifically, a WU must prove possession of a valid attestation bound to a WSCD without revealing a correlation handle (e.g., PoP key) used for the binding.
2. The solution must work both in remote and proximity flows.
3. The solution must consider latency constraints for the context it is intended for.
4. Avoid excessive proof size or auxiliry data.
5. Not introduce tracking risks through additional communication.

Integration considerations mention that a ZKP deployment may require changes to issuance and presentation protocols, metadata extensions, public parameter distribution, compatability with existing attestation formats, and support for already issued attestations.

Includes a set of HLR:

1. Supports selective disclosure, validity, revocation, device binding, and optional issuer hiding.
2. Supports proof of possession of attestations.
3. Supports privacy-preserving attestation–PID binding.
4. Supports pseudonym derivation using hidden user attributes.
5. Is usable in both remote and proximity contexts without degrading service purpose.
6. Works with already issued credentials.
7. Introduces no tracking vectors.
8. Uses recognised standardised cryptographic algorithms.
9. Does not prevent high Level of Assurance user authentication.

