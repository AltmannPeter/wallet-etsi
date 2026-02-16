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

## Normalized requirements derived from Annex 2

Herein, ZKP requirements are derived that are atomic and well-formed (with guidance from IEEE 830) from the requirements in Topic 53.

| #         | Normalized requirement                                                                                                                                                                              |
|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ZKP_01.01 | The ZKP scheme SHALL ensure that, within the security level defined by the applicable security specification, no party other than the prover can derive information about a credential beyond the explicitly proven statement(s). |
| ZKP_01.01a | The ZKP profile SHALL specify the applicable Security Specification (e.g., bit-security level, cryptographic primitives) that defines the bounds of the security level for a given use case. |
| ZKP_01.02 | The ZKP scheme SHALL support the generation of a proof that one or more specified attributes of a given credential satisfy specified values or predicates, where the attributes and predicates are determined at proof generation time. |
| ZKP_01.02a | The ZKP scheme SHALL support the generation of a proof of possession of a credential of a specified type. |
| ZKP_01.02b | The ZKP scheme SHALL support the generation of a proof that specified attributes contained in two or more credentials are equal without revealing the attribute values. |
| ZKP_01.03 | The ZKP scheme SHALL support the generation of proofs for attributes representing validity periods using predicates that compare the attribute value against a reference time provided at verification.                                                   |
| ZKP_01.04 | The ZKP scheme SHALL support the generation of a proof that a given credential has not been revoked at the time of verification.                                                                    |
| ZKP_01.05 | The ZKP scheme SHALL support the generation of a proof that binds a credential to a specific user-controlled cryptographic key (e.g., via a proof of possession).                           |
| ZKP_01.06 | The ZKP scheme SHOULD support predicates that verify the credential Provider's membership in a defined set without revealing the specific Provider identity. |
| ZKP_02.01 | The ZKP scheme SHOULD support the derivation of a unique and verifiable pseudonym by combining a unique hidden user attribute with a public context value provided by the verifier and possibly other inputs. |
| ZKP_02.02 | For pseudonyms derived under `ZKP_02.01`, if the pseudonym configuration does not support unmasking, the ZKP scheme SHALL prevent the possibility of identifying the user associated with a presented pseudonym. |
| ZKP_03.01 | The ZKP scheme SHALL provide a technical profile documenting its round trip requirements, proof size, public parameter requirements, and generation/verification latency. |
| ZKP_04.01 | The ZKP scheme SHALL support the generation of proofs for PID formatted in accordance with the PID Rulebook. |
| ZKP_04.02 | The ZKP scheme SHALL support the generation of proofs for (Q)EAAs formatted in accordance with their respective applicable Rulebooks. |
| ZKP_05.01 | The ZKP scheme SHALL NOT include persistent or correlatable data elements in proof messages that enable the linkage of independent proof presentations from the same User. |
| ZKP_06.01 | The ZKP Profile SHALL specify the normative proof presentation message format, encoding, and transport protocol required for interoperable verification. |
| ZKP_06.02 | The ZKP scheme SHALL support verification of proofs generated under this specification. |
| ZKP_06.03 | The ZKP scheme profile SHALL specify the assumptions, threat model, and security goals under which completeness and soundness are claimed for the profile. |

Some requirements in the original Annex 2 are dropped becuase they are neither scheme layer or profile layer requirements:

* `ZKP_08` "A ZKP scheme SHALL rely solely on algorithms included in the ECCG Agreed Cryptographic Mechanisms v2.0." This only constraints primitives to be used.
* `ZKP_09` "Use of a ZKP scheme SHALL NOT prevent the Wallet Unit's ability to provide User authentication with Level of Assurance High." This is a regulatory / assurance classification. This requirement is better handled using a general provision that a ZKP scheme must satisfy.

The end goal has been to derive schema layer requirements that define proof construction requirements (and possibly concrete parameters) and leave out integration layer requirements (e.g., transport) and deployment layer requiremnts (e.g., UX impact) but ALLOW for decisions on that level using profile layer information.

Also, the goal has been to avoid discussions that are not relevant for the specification herein but facilitate the required discussion elsewhere (e.g., `ZKP_06.03`)
