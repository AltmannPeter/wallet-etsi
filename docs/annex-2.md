[Annex 2 Section A.2.3.31 Topic 53](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/annexes/annex-2/annex-2.02-high-level-requirements-by-topic.md#a2331-topic-53-zero-knowledge-proofs) lists requirements for ZKP. These requirements are not atomic. Herein, ZKP requirements are derived that are atomic and well-formed (with guidance from IEEE 830) from the requirements in Topic 53.

> A ZKP scheme SHALL provide support for the following generic functions, while hiding all attributes of PIDs or attestations: (i) generation of a proof that an (some) attribute(s) having a specific value is (are) included in a PID or attestation, (ii) generation of a proof that a PID or attestation is within its validity period, (iii) generation of a proof that a PID or attestation has not been revoked, and (iv) generation of a proof that a PID or device-bound attestation is bound to a key stored in the WSCA/WSCD or in a keystore of the Wallet Unit. Additionally, a ZKP scheme SHOULD provide support for the following function, which SHOULD be used only when hiding the PID Provider or Attestation Provider is necessary: (v) generation of a proof that a PID or attestation has been issued by a trusted PID Provider or Attestation Provider, without revealing the PID Provider or Attestation Provider. Note: See section 4.1.1 of the Discussion Paper for Topic G. - `ZKP_01`

> 

The following normalized requirements are derived from the requirement above (`ZKP_02, ZKP_03` are both special cases of `ZKP_01`):

| #         | Normalized requirement                                                                                                                                                                              |
|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ZKP_01.01 | The ZKP scheme SHALL ensure that, within the security level defined by the applicable security specification, no party other than the prover can derive information about a credential beyond the explicitly proven statement(s).                                    |
| ZKP_01.02 | The ZKP scheme SHALL support the generation of a proof that one or more specified attributes of a given credential satisfy specified values or predicates, where the attributes and predicates are determined at proof generation time.                                          |
| ZKP_01.03 | The ZKP scheme SHALL support the generation of a proof that a given credential is within its defined validity period at the time of verification.                                                   |
| ZKP_01.04 | The ZKP scheme SHALL support the generation of a proof that a given credential has not been revoked at the time of verification.                                                                    |
| ZKP_01.05 | The ZKP scheme SHALL support the generation of a proof that binds a credential to a specific cryptographic key (e.g., via a proof of possession).                           |
| ZKP_01.06 | The ZKP scheme SHOULD support predicates that verify the credential Provider's membership in a defined set without revealing the specific Provider identity. |
| ZKP_02.01 | The ZKP scheme SHOULD support the derivation of a unique and verifiable pseudonym by combining a unique hidden user attribute with a public context value provided by the verifier and possibly other inputs. |
| ZKP_02.02 | For pseudonyms derived under `ZKP_02.01`, if the pseudonym configuration does not support unmasking, the ZKP scheme SHALL prevent the possibility of identifying the user associated with a presented pseudonym. |
| ZKP_03.01 | The ZKP scheme SHALL provide a technical profile documenting its message-pass complexity, proof size, public parameter requirements, and generation/verification latency to facilitate assessment of service suitability and user experience impact. |

In the requirements above:

- `ZKP_02`, proving possession of an attestation type, is treated as a special case of `ZKP_01.2`, specifically an equality test between a public value and a hidden value
- `ZKP_03`, proving binding between an (Q)EAA and a PID, is treated as a special case of `ZKP_01.2`, specifically an equality test between two hidden values
