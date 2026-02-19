# Dervied ZKP requirements from the legal sources and ARF

| Source | Mapping | ZKP requirement | Use case example |
|--------|---------|-----------------|------------------|
| Recital 14 Regulation (EU) 2024/1183 | A relying party must be able to validate a statement derived from identity attributes without learning anything beyond the truth value of that statement | Core ZKP properties (completeness, soundness, zero-knowledgeness) | N/A |
| Recital 14 Regulation (EU) 2024/1183; Article 5(8) CIR 2024/2977; Topic G section 4.1 | Any claim derivable from identity attributes may be proven without revealing the underlying values | Common predicates | Age range proofs (e.g. 18 < age < 65); set membership proofs (e.g. is student proof, proof of valid issuer); proving non-expiration without reveal expiration value |
| Article 5(7) CIR 2024/2977 | The validity status object must not itself include correlation handles or allow for behavioral mapping | Privacy-preserving revocation | The public revocation registry, or the validity status proof, does not expose any credential identifier |
| Recital (9) and Article 7(4) CIR 2024/2979; Topic G HLR | Validity status must be verifiable without exposing revocation identifiers | Privacy-preserving revocation | Proving wallet unit attestation validity without creating a correlation handle; proving that the presented attributes are from an attestation that has not been revoked |
| Article 5(8) CIR 2024/2977 | Full PID presentation unlinkability when user identification is not required | Multi-show full unlinkability | All PID presentations where the user does not need to identify itself |
| Article 5a(5b, 16) Regulation (EU) 2024/1183; Topic G section 4.1 | Randomized proofs across EAA presentations to avoid correlation handles; no information about EAA use may reach the EAA provider | Multi-show full unlinkability | All EAA presentations where the user does not need to identify itself |
| Recital (8) CIR 2024/2979 | Cross-relying-party unlinkability for wallet unit attestations; proofs freshly generated per session without pre-computation | Multi-show full unlinkability | All use cases where the user does not reveal identity (recommended for WUA) |
| Recital (6) CIR 2024/2977; Article 5a(4a) Regulation (EU) 2024/1183 | Cross-credential linking proofs; prove statements from multiple sources and that they are properly paired | Cross-credential presentation | Combined PID and attestation presentation (e.g. vaccination proof linked to PID) |
| Article 3(5) CIR 2024/2977 | Proof of Possession must not introduce a static correlation handle across presentations | Unlinkable PoP | LoA High PID presentation |
| Article 5a(5f) Regulation (EU) 2024/1183 | Prove that a signature validates with the public key in the attestation's PoP field | Hardware-bound PoP | LoA High PID presentation |
| Article 14(2) CIR 2024/2979; Article 5a(4b) Regulation (EU) 2024/1183; Topic G section 4.1 | Support pseudonyms deterministically derived from an attribute in a credential without exposing the attribute itself | Pseudonym derivation | Identity-bound stable site-specific unlinkable pseudonyms dervied from a private seed and public context |

## Requirements without legal basis or that prescribe a solution-specific mechanism

### Blind issuance

Topic G introduces blind issuance as an additional privacy property in Section 2:

> Additional privacy properties have also been defined in related literature. Such properties are:
>  * (Partially) Blind Issuance: A ZKP scheme may enable PID Providers or Attestation Providers to issue attestations with attributes that remain hidden towards the providers (but can be verifiably transferred from other attestations). - Topic G Section 2.

Topic G section 4.1 then grounds this in a specific use case:

> In some use cases, a Relying Party needs to correlate multiple presentations of the same attestation (e.g., to detect a "returning customer"). A ZKP scheme can be used to derive a pseudonym by combining an attribute of an attestation that is unique to the User with the Relying Party identifier [...] The attribute used for deriving the pseudonym shall also be hidden from the Provider; otherwise, a colluding Provider and Relying Parties would be able to correlate User presentations. This property can be achieved using blind issuance. - Topic G section 4.1.

This culminates in REQUIREMENT 4:

> A ZKP scheme SHOULD support the derivation of a verifiable User pseudonym, by combining an attribute value that is unique for the User with Relying Party specific context (e.g., the Relying Party identifier). In addition to the generic functions defined in ZKP_01, for this use case, a ZKP scheme SHALL provide support for the following functions: (i) generation of a request for the issuance of an attestation that includes a secret attribute unique to the User, without revealing this attribute to the Attestation Provider,  - REQUIREMENT 4

REQUIREMENT 4 conflates the mechanism with the requirement. Hiding the attribute from the issuer is one way to prevent a colluding issuer and relying party from unmasking a pseudonym, but it is not the only way. The underlying requirement is that pseudonym derivation cannot be reproduced from issuer-known inputs alone. Blind issuance satisfies this in pairing-based schemes such as BBS+, but circuit-based approaches can satisfy the same property through other mechanisms. Requirement 4 is therefore split and reframed into:

* The ZKP scheme SHOULD support the derivation of a unique and verifiable pseudonym by combining a unique hidden user attribute with a public context value provided by the verifier and possibly other inputs.
* If the pseudonym configuration does not support unmasking, the ZKP scheme SHALL ensure that reversing the pseudonym to a user identifying attribute is infeasible.
