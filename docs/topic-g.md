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

It is necessary to distinguish between use cases that require full identity verification, where unlinkability is unnecessary, and those that require attribute-specific proofs (e.g., age). This distinction aligns with the Regulation:

> Relying parties shall not refuse the use of pseudonyms, where the identification of the user is not required by Union or national law. - [Regulation (EU) 2024/1183](https://eur-lex.europa.eu/eli/reg/2024/1183/oj/eng) Article 5b, 9

> enable privacy preserving techniques which ensure unlinkability, where the attestation of attributes does not require the identification of the user. - 910/2014 Article 5a, 16(b)

> Providers of person identification data shall enable privacy preserving techniques which ensure unlinkability where the electronic attestations of attributes do not require the identification of the user. - [CIR 2024/2977](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202402977) Article 5, 8

> enable privacy preserving techniques which ensure unlinkability where the electronic attestations of attributes do not require the identification of the wallet user, when presenting attestations or person identification data across different wallet-relying parties. - [CIR 2024/2982](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202402982) Article 3, 10

Where full identification is not required, a ZKP scheme must enable the Wallet to demonstrate the following without compromising user privacy:

* **PID/(Q)EAA & status integrity**: The Person Identification Data (PID) or attestation contains the disclosed attribute or its corresponding predicate, has a valid status, and originates from a trusted issuer (optionally withholding the issuer’s identity).
* **Validity & origin**: The attestation possesses a valid status proof and originates from a trusted issuer, with the option to withhold the issuer’s specific identity.
* **Cryptographic binding**: The attestation is bound to a key stored within a WSCD to ensure hardware-backed PoP. The PoP must not reveal correlation handles (e.g., static PoP keys).
* **Pseudonym management**: The scheme must support pseudonym seeds and derivation such that the issuer cannot derive the resulting pseudonym, even if the seed is known or used during issuance.
* **Operational compliance**: Implementations must maintain Level of Assurance (LoA) High, minimize latency for remote/proximity flows, and ensure compatibility with ISO/IEC 18013-5 or SD-JWT VC formats.

Topic G concludes with HLRs to be added to Annex 2. A ZKP scheme:

| Requirement | Strength |
| :--- | :--- |
| Support attribute equality proofs. | **MUST** |
| Support attribute predicate proofs. | **MUST** |
| Support attribute equality proofs between multiple attestations. | **MUST** |
| Support non-expiration proofs. | **MUST** |
| Support validity status proofs. | **MUST** |
| Support hardware-bound PoP proofs without revealing correlation handles. | **MUST** |
| Support blind issuance of attestations. | **MUST** |
| Ensure full transaction unlinkability. | **MUST** |
| Maintain Level of Assurance (LoA) High. | **MUST** |
| Support both remote and proximity flow interactions. | **MUST** |
| Introduce latency detrimental to the user experience. | **MUST NOT** |
| Support ISO/IEC 18013-5 or SD-JWT VC attestation formats. | **SHOULD** |
| Use only algorithms recognized by European Commission standards. | **MUST** |
| Support pseudonym derivation from a user attribute and a specific context. | **SHOULD** |
| Support binding an attestation to a PID. | **SHOULD** |
| Support proof of valid issuer without revealing the specific issuer identity. | **SHOULD** |
