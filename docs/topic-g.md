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

## ZKP requirements

Topic G outlines ZKP capabilities that may serve as building blocks for EUDIW. These ZKP building blocks are implemented using underlying proof systems for various relations (e.g. polynomial commitment openings, inner-product arguments, and generic linear-relation proofs) and include inequality and range proofs, set (non-)membership proofs, and various discrete-log knowledge proofs, to name a few. The building blocks can then be combined to implement higher-level statements relevant for the EUDIW, including but not limited to age proofs, validity status checks, issuer hiding, pseudonymous authentication, combined presentations from multiple attestations, and blind issuance.

The Topic G text describes ZKP use cases and privacy properties at a functional level (what a ZKP scheme should enable), but it does not introduce an explicit layering between underlying relation-level proof systems, intermediate building-block gadgets, and their composition into higher-level statements. As a result, it is not entirely clear from Topic G alone what concrete ZKP requirements are implied or which specific ZKP capabilities implementations are expected to support.

Instead, guidance is offered by examining the need for privacy across use cases. Topic G notes that some use cases—such as opening a bank account—may require full identity verification rendering unlinkability requirements moot. Others only need proof of a specific attribute or property, such as an age proof. This distinction is recognized by the Regulation. In several places, the legal text mandates support for various techniques and solutions that prevent linkability when user identification is not required.

> Relying parties shall not refuse the use of pseudonyms, where the identification of the user is not required by Union or national law. - [Regulation (EU) 2024/1183](https://eur-lex.europa.eu/eli/reg/2024/1183/oj/eng) Article 5b, 9

> enable privacy preserving techniques which ensure unlinkability, where the attestation of attributes does not require the identification of the user. - 910/2014 Article 5a, 16(b)
> Providers of person identification data shall enable privacy preserving techniques which ensure unlinkability where the electronic attestations of attributes do not require the identification of the user. - [CIR 2024/2977](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202402977) Article 5, 8

> enable privacy preserving techniques which ensure unlinkability where the electronic attestations of attributes do not require the identification of the wallet user, when presenting attestations or person identification data across different wallet-relying parties. - [CIR 2024/2982](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202402982) Article 3, 10

One way to elicit ZKP requirement is thus by analyzing use case where user identification is not required. Topic G lists use cases where "ZKP shall be used once available":

* **Selective disclosure of identity attributes**. Use cases where the user needs to prove that they possess a PID or attestation that includes a specific attribute, or a valid predicate of this attribute, without revealing any additional information about their identity may use a ZKP scheme. Such a scheme shall provide a proof that:
  1. the PID or attestation includes the revealed attribute (or predicate thereof)
  2. a validity status proof of the PID or attestation that includes the attribute
  3. the PID or attestation is issued by a trusted issuer, optionally without revealing the issuer, and
  4. The PID or attestation is is bound to a key stored in the WSCD of the wallet unit.
* **Proof of possession of an attestatio type**. In some use cases, it may be enough that the user proves that they are in possession of a particular attestation type. For instance, stores offering various discounts to certain groups of users, e.g., students, may require only proof of possession of a student card.


> NOTE TO SELF: SOME OF THE ABOVE REQUIRE ZKP BUT NOT ALL. USE CASE REQUIREMENTS SHOULD BE FRAMED MORE FROM THE CAPABILITIES THAT ZKP CAN OFFER THAT OTHER USE CASES CANNOT. AND SOME USE CASES ARE NOT USE CASES BUT CAPABILITIES. SIGH.






Such capabilities can be leveraged in various ways to support privacy-preserving revocation, issuer hiding, pseudonyms

The document identifies privacy properties relevant to digital identity systems (both under collusion scenarios and under data breach scenarios):

1. Selective disclosure
2. Relying party unlinkability
3. Full unlinkability
4. Range proofs
5. Privacy-preserving revocation
6. Issuer hiding
7. Pseudonymity
8. Composite proofs
9. Blind issuance
10. Conditional disclosure

The document then defines scenarios where ZKP SHALL or SHOULD be used once available:

1. Selective disclosure of attributes
2. Proof of personhood
3. Proof of attestation type
4. Privacy preserving binding betewen an attestation and the PID
5. Privacy-preserving pseudonym derivation for relying parties

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

