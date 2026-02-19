[Annex 2 Section A.2.3.31 Topic 53](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/annexes/annex-2/annex-2.02-high-level-requirements-by-topic.md#a2331-topic-53-zero-knowledge-proofs) lists requirements for ZKP. These requirements have several flaws, including but not limited to:

* Not atomic — Multiple independent functions grouped under a single requirement.
* Mixed modalities — SHALL and SHOULD combined within the same requirement.
* Layer violations — Cryptographic scheme requirements mixed with wallet, protocol, UX, or compliance concerns.
* Vague terminology — Undefined or ambiguous terms used normatively.
* Non-testable statements — Qualitative language without measurable criteria.
* Missing threat model — No defined adversary capabilities or security assumptions.
* Implementation coupling — Requirements tied to specific implementations rather than scheme-level properties.

The purpose of this text is to use Annex 2 as a base for normalized requirements that can provide the foundation for the ETSI TS.

## Annex 2 requirements
> A ZKP scheme SHALL provide support for the following generic functions, while hiding all attributes of PIDs or attestations: (i) generation of a proof that an (some) attribute(s) having a specific value is (are) included in a PID or attestation, (ii) generation of a proof that a PID or attestation is within its validity period, (iii) generation of a proof that a PID or attestation has not been revoked, and (iv) generation of a proof that a PID or device-bound attestation is bound to a key stored in the WSCA/WSCD or in a keystore of the Wallet Unit. Additionally, a ZKP scheme SHOULD provide support for the following function, which SHOULD be used only when hiding the PID Provider or Attestation Provider is necessary: (v) generation of a proof that a PID or attestation has been issued by a trusted PID Provider or Attestation Provider, without revealing the PID Provider or Attestation Provider. Note: See section 4.1.1 of the Discussion Paper for Topic G. - `ZKP_01`

> A ZKP scheme SHOULD support the derivation of a verifiable User pseudonym, by combining an attribute value that is unique for the User with Relying Party-specific context (e.g., the Relying Party identifier) In addition to the generic functions defined in ZKP_01, for this use case, a ZKP scheme SHALL provide support for the following functions: (i) generation of a request for the issuance of an attestation that includes a secret attribute unique to the User, without revealing this attribute to the Attestation Provider, (ii) generation of an attestation presentation that includes a verifiable pseudonym derived from the secret attribute, a Relying Party identifier, and context-related information. - `ZKP_04`

> A ZKP scheme SHALL be usable in both remote and proximity presentation flows. While the inclusion of ZKP will introduce computational and verification delays, these delays SHALL NOT critically undermine or defeat the purpose of the Relying Party service (e.g. because of a critical impact on the User experience of the Wallet Unit). - `ZKP_05`
