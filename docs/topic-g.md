# ZKP requirements from Topic G

[Topic G](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/g-zero-knowledge-proof.md) defines HLR for integrating ZKP into the EUDIW ARF. It evaluates ZKP as a privacy-enhancing technology and specifices foundational requirements for its future integration. It aligns technical requirements with legal mandates on selective disclosure, unlinkability, and anti-profiling obligations.

## Legal requirements

### Regulation (EU) 2024/1183

[Regulation (EU) 2024/1183](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202401183) discusses the use of privacy-preserving technologies in Recital 14:

> Member States should integrate different privacy-preserving technologies, such as zero knowledge proof, into the European Digital Identity Wallet. Those cryptographic methods should allow a relying party to validate whether a given statement based on the person’s identification data and attestation of attributes is true, without revealing any data on which that statement is based

Recital 14 imposes no limitations on the type of statement a relying party may validate. It broadly applies to any statement derived from a person’s identification data or attributes. Recital 15 builds on this by outlining functional expectations for the wallet and identifying priority features:

> [Wallet users] should be empowered to securely request, select, combine, store, delete, share and present data related to their identity [...] while enabling selective disclosure of personal data. [...] aiming towards the highest level of security, privacy, user convenience [...].

Note both the functional requirements and the expectation of strong privacy guarantees. Where design tensions exist between privacy and e.g., user convenience, a ZKP solution should prioritize privacy (solutions for other prioritizations). Users may need to tolerate e.g., increased latency in exchange for stronger privacy protections.

[Section 1 Article 5a](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202401183#sct_1) lists functional requirements that a wallet must allow users to do. Several requirements have strong privacy expectations and/or implications:

* Manage (request, obtain, select, combine, store, delete, share and present using selective disclosure) personal identification data possibly in combination with electronic attestations of attributes, to authenticate to service providers (4a).
* Manage pseudonyms locally using their wallet (4b).
* Not provide any information to TSPs of EAAs about the use of EAAs (5b).
* Meet assurance level high requirements (5d).
* The PID must be bound to the user's EUDIW (5f).
* No party shall be able to obtain data that allows transactions or user behavior to be tracked, linked, or correlated after the issuance of an EAA (16a).
* Ensure privacy preserving techniques which ensure unlinkability (the legal text requires *unlikeability* but it is likely safe to assume they meant unlinkability) (16b).

##

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

