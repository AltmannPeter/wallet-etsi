# Deriving ZKP requirements

## ZKP requirements for Annex 2

[Annex 2 Section A.2.3.31 Topic 53](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/annexes/annex-2/annex-2.02-high-level-requirements-by-topic.md#a2331-topic-53-zero-knowledge-proofs) lists requirements for ZKP. These requirements have several flaws, including but not limited to:
*SR: the above link references the `main` branch, so the linked document can change whenever updates to the ARF are pushed. You probably want to link to a specific commit so that your statement doesn't stop being true if they would fix the issues you mention.*

* Not atomic — Multiple independent functions grouped under a single requirement.
* Mixed modalities — SHALL and SHOULD combined within the same requirement.
* Layer violations — Cryptographic scheme requirements mixed with wallet, protocol, UX, or compliance concerns.
* Vague terminology — Undefined or ambiguous terms used normatively.
* Non-testable statements — Qualitative language without measurable criteria.
* Missing threat model — No defined adversary capabilities or security assumptions.
* Implementation coupling — Requirements tied to specific implementations rather than scheme-level properties.

The purpose of this text is to use Annex 2 as a base for normalized requirements that can provide the foundation for the ETSI TS. It includes both normalized requirements, and a justification when a requirement is dropped.

The normalized requirements build on three Appendices. 

1. Appendix 1 includes a mapping that shows how the normalized requirements map to, and from, the original Annex 2 requirements.
2. Appendix 2 includes the sources used for deriving the normalized requirements.
3. Appendix 3 is an extended mapping to legal texts, operational and conformance requirements, and threat registers that are mapped to privacy requirments, and then to ZKP requirements.

Note that the contents in Appendix 2 and Appendix 3 may differ from the final synthesis and are provided only for method transparency reasons.

Next, the normalized requirements are presented, followed by the Appendecies. 

### Normalized requirements from Annex 2

The numbering groups requirements by function: predicate proofs (01), validity status (02), key binding and PoP (03), unlinkability (04), issuer hiding (05), pseudonyms (06), cross-credential binding (07). performance (08), and conformance (C). 

There are four predicate requirements:

| #         | Normalized predicate requirement                                                                                                                                                                              |
|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ZKP_01.01 | The ZKP scheme SHALL generate a proof that an attribute equals a specified value, without revealing information beyond the truth value of the proven statement. |
| ZKP_01.02 | The ZKP scheme SHALL generate a proof that an attribute satisfies a range predicate against a specified value, without revealing information beyond the truth value of the proven statement. |
| ZKP_01.03 | The ZKP scheme SHALL generate a proof that an attribute is a member of a specified set, without revealing information beyond the truth value of the proven statement. |
| ZKP_01.04 | If cross-credential binding (ZKP_07.01) applies, the ZKP scheme SHALL generate a proof that two unrevealed attributes are equal, without revealing information beyond the truth value of the proven statement. |

Requirements related to validity status checks are:

| #         | Normalized validity status requirement |
|-----------|---------------------------------------|
| ZKP_02.01 | Of an attribute representing a validity period, the ZKP scheme SHALL generate a ZKP that it satisfies a predicate against a reference time provided at verification.<br>*SR: the previous phrasing suggests that the scheme shall present a ZKP saying that the attribute (1) represents a validity period and (2) satisfies a predicate, whereas in actuality, (1) is context coming from the outside from the perspective of the ZKP scheme.* |
| ZKP_02.02 | The ZKP scheme SHALL generate a ZKP that an attestation has not been revoked at the time of verification.<br>*SR: This might be too restrictive: not all approaches suggested in TS14 consist of actual ZKPs. Also: you generally show that the attestation is not revoked within an epoch; it is generally not instant, as the current phrasing suggests.* |
| ZKP_02.03 | The ZKP scheme SHALL generate a ZKP of non-revocation against published revocation status data.<br>*SR: what does this requirement add, given that we already have ZKP_02.02?* |

Requirements related to Proof of Possession and Issuer signature proofs:

| #         | Normalized credential binding proof requirement |
|-----------|-------------------------------------------|
| ZKP_03.01 | The ZKP scheme SHALL generate a ZKP of Proof of Possession of the key referenced in the attestation key binding field. |
| ZKP_03.02 | A ZKP scheme claiming conformance for LoA High presentations SHALL generate a ZKP of a valid Issuer signature where the issuer signing key is hardware-protected. |


Unlinkability requirements that apply when user identification is not required:

| #         | Normalized unlinkability requirement |
|-----------|--------------------------------------|
| ZKP_04.01 | The ZKP scheme SHALL NOT include static or reusable elements in proof presentations that enable linking independent presentations to the same user. |
| ZKP_04.02 | The ZKP scheme SHALL ensure that unlinkability holds under collusion between issuers and verifiers, such that no combination of data observable by colluding parties enables linking presentations or issuance to the same user. |

ZKP_04.02 explicitly addresses issuer-verifier collusion as an adversary model, consistent with TR84 in the threat register. This represents a stronger adversarial assumption than those used by salted attribute digest based approaches, which is appropriate given the full unlinkability capabilities of ZKP schemes in scope for this specification.

The Issuer hiding requirement is:

| #         | Normalized issuer hiding requirement |
|-----------|--------------------------------------|
| ZKP_05.01 | The ZKP scheme SHOULD generate a proof that the Issuer is trusted for the given type, without revealing the Issuer's identity.<br>*SR: "that the Issuer is trusted" is a bit vague. Don't these systems prove something along the lines of "that the Issuer is one of a set of trusted Issuers"?* |

Pseudonym requirements for identity-bound stable and site-specific pseudonyms:

| #         | Normalized pseudonym requirement |
|-----------|----------------------------------|
| ZKP_06.01 | The ZKP scheme SHOULD support the derivation of a unique and verifiable pseudonym by combining a unique hidden user attribute with a public context value provided by the verifier and possibly other inputs. *<br>SR: What is "verifiable"?* |
| ZKP_06.02 | For pseudonyms derived under ZKP_06.01, where no party is permitted to reverse the pseudonym to the user's identity, the ZKP scheme SHALL ensure that doing so is infeasible. |

Requirements related to cross-credential binding:

| #         | Normalized cross-credential binding requirement |
|-----------|------------------------------------------------|
| ZKP_07.01 | The ZKP scheme SHOULD generate a ZKP that an attribute in an attestation and an attribute in a PID are equal. |

## Implementation and conformance requirements

The following requirements apply to implementations and profiles of the ZKP scheme rather than to the scheme itself. They are separated here to maintain the layer boundary established throughout this specification.

### Cryptographic and performance conformance

| #          | Requirement |
|------------|-------------|
| ZKP_C.01 | Implementations of the ZKP scheme SHALL use only cryptographic algorithms listed in the ECCG Agreed Cryptographic Mechanisms. <br>*SR: Does not yet include BBS(+), right?* |
| ZKP_C.02 | The ZKP profile SHALL declare conformance to one or more performance tiers as defined in ETSI TS 119 476-2. |

### Format conformance

| #          | Requirement |
|------------|-------------|
| ZKP_C.03 | The ZKP scheme claiming conformance for PID presentation SHALL support the generation of proofs for PIDs in accordance with the credential formats defined in the PID Rulebook. |
| ZKP_C.04 | A ZKP scheme claiming conformance for (Q)EAA presentations SHALL support the generation of proofs for (Q)EAAs in accordance with the credential format defined in their respective applicable Rulebooks.|

> The original Annex 2 `ZKP_06` uses SHOULD, scoped to ISO/IEC 18013-5 and SD-JWT VC. The modality is strengthened to SHALL here on the basis that every deployment context has a mandatory rulebook that defines allowed formats. A ZKP scheme that does not conform to the applicable rulebook formats cannot be deployed in any compliant use case.

### Requirements better defined elsewhere

The original Annex 2 requirement `ZKP_05` addresses two distinct concerns: transport layer compatibility and performance impact:

> A ZKP scheme SHALL be usable in both remote and proximity presentation flows. While the inclusion of ZKP will introduce computational and verification delays, these delays SHALL NOT critically undermine or defeat the purpose of the Relying Party service (e.g. because of a critical impact on the User experience of the Wallet Unit). - `ZKP_05`

These are legitimate concerns but are not ZKP scheme-level requirements. Transport layer requirements are outside the scope of ZKP scheme requirements and must be addressed in the relevant transport and protocol requirements. Relatedly, performance impact is addressed through the performance tier framework defined in ETSI TS 119 476-2. A ZKP scheme declaring conformance to a performance tier as detailed above in `ZKP_C.02`. Conformance to a performance tier provides implementers with the information needed to assess suitability for a given deployment context.

# Appendicies

## Appendix 1 - Mapping from Annex 2 to the derived requirements

Mapping from original Annex 2 requirement to normalized requirement

| Annex 2 requirement | Synthesis coverage |
|--------------------|--------------------|
| ZKP_01(i) | ZKP_01.01 |
| ZKP_01(ii) | ZKP_01.02; ZKP_02.01 |
| ZKP_01(iii) | ZKP_02.02; ZKP_02.03 |
| ZKP_01(iv) | ZKP_03.01; ZKP_03.02 |
| ZKP_01(v) | ZKP_05.01 |
| ZKP_01 preamble | ZKP_01.01; ZKP_01.02; ZKP_01.03; ZKP_01.04 |
| ZKP_02 | ZKP_03.01 |
| ZKP_03 outer | ZKP_07.01 |
| ZKP_03 inner | ZKP_01.04; ZKP_07.01 |
| ZKP_04 outer | ZKP_06.01 |
| ZKP_04 inner | ZKP_06.02 |
| ZKP_05 | N/A as requirement better defined elsewhere |
| ZKP_06 | ZKP_C.03; ZKP_C.04 |
| ZKP_07 | ZKP_04.01; ZKP_03.02 |
| ZKP_08 | ZKP_C.01 |
| ZKP_09 | ZKP_03.01; ZKP_03.02; ZKP_03.03 |

Mapping from normalized requirement to original Annex 2 requirement

| Synthesis requirement | Annex 2 mapping |
|----------------------|-----------------|
| ZKP_01.01 | ZKP_01(i) |
| ZKP_01.02 | ZKP_01(ii) |
| ZKP_01.03 | No direct Annex 2 mapping; grounded in Recital 14 and Topic G |
| ZKP_01.04 | ZKP_03 inner |
| ZKP_02.01 | ZKP_01(ii) |
| ZKP_02.02 | ZKP_01(iii) |
| ZKP_02.03 | ZKP_01(iii) |
| ZKP_03.01 | ZKP_01(iv); ZKP_02 |
| ZKP_03.02 | ZKP_07 |
| ZKP_03.03 | ZKP_09 |
| ZKP_04.01 | ZKP_07 |
| ZKP_04.02 | No direct Annex 2 mapping; grounded in TR84 and Topic G |
| ZKP_05.01 | ZKP_01(v) |
| ZKP_06.01 | ZKP_04 outer |
| ZKP_06.02 | ZKP_04 inner |
| ZKP_07.01 | ZKP_03 outer |
| ZKP_C.01 | ZKP_08 |
| ZKP_C.02 | No direct Annex 2 mapping; grounded in Topic G HLR |
| ZKP_C.03 | ZKP_06 |
| ZKP_C.04 | ZKP_06 |

## Appendix 2 - Sources

The following documents derived requirements from legal text:

* https://github.com/AltmannPeter/wallet-etsi/blob/main/docs/cir-2024-2977.md
* https://github.com/AltmannPeter/wallet-etsi/blob/main/docs/cir-2024-2979.md
* https://github.com/AltmannPeter/wallet-etsi/blob/main/docs/additional-sources.md
* https://github.com/AltmannPeter/wallet-etsi/blob/main/docs/topic-g.md
* https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/annexes/annex-2/annex-2.02-high-level-requirements-by-topic.md#a2331-topic-53-zero-knowledge-proofs

## Appendix 3 - Legal obligations and ZKP requirements

### Legal obligations and related ZKP requirements

| Source | Mapping | ZKP requirement | Use case example |
|--------|---------|-----------------|------------------|
| Recital 14 Regulation (EU) 2024/1183 | A relying party must be able to validate a statement derived from identity attributes without learning anything beyond the truth value of that statement | Core ZKP properties (completeness, soundness, zero-knowledgeness) | N/A |
| Recital 14 Regulation (EU) 2024/1183; Article 5(8) CIR 2024/2977; Topic G section 4.1 | Any claim derivable from identity attributes may be proven without revealing the underlying values | Common predicates | Age range proofs (e.g. 18 < age < 65); set membership proofs (e.g. is student proof, proof of valid issuer); proving non-expiration without reveal expiration value |
| Article 5(7) CIR 2024/2977 | The validity status object must not itself include correlation handles or allow for behavioral mapping | Privacy-preserving revocation | The public revocation registry, or the validity status proof, does not expose any credential identifier |
| CIR 2025/1569 Article 4(4) | Revocation techniques for QEAAs and public sector EAAs must not enable linkability or traceability of the holder | Privacy-preserving revocation | QEAA revocation status check that does not reveal usage patterns or link presentations to the same holder |
| Recital (9) and Article 7(4) CIR 2024/2979; Topic G HLR | Validity status must be verifiable without exposing revocation identifiers | Privacy-preserving revocation | Proving wallet unit attestation validity without creating a correlation handle; proving that the presented attributes are from an attestation that has not been revoked |
| Article 5(8) CIR 2024/2977 | Full PID presentation unlinkability when user identification is not required | Multi-show full unlinkability | All PID presentations where the user does not need to identify itself |
| Article 3(10) CIR 2024/2982 | Unlinkability must be enabled at the wallet unit level for all attestation presentations where user identification is not required, across different relying parties | Multi-show full unlinkability | All attestation presentations across different relying parties where the user does not need to identify itself |
| Article 5a(5b, 16) Regulation (EU) 2024/1183; Topic G section 4.1 | Randomized proofs across EAA presentations to avoid correlation handles; no information about EAA use may reach the EAA provider | Multi-show full unlinkability | All EAA presentations where the user does not need to identify itself |
| Recital (8) CIR 2024/2979 | Cross-relying-party unlinkability for wallet unit attestations; proofs freshly generated per session without pre-computation | Multi-show full unlinkability | All use cases where the user does not reveal identity (recommended for WUA) |
| Recital (6) CIR 2024/2977; Article 5a(4a) Regulation (EU) 2024/1183 | Cross-credential linking proofs; prove statements from multiple sources and that they are properly paired | Cross-credential presentation | Combined PID and attestation presentation (e.g. vaccination proof linked to PID) |
| Article 3(5) CIR 2024/2977 | Proof of Possession must not introduce a static correlation handle across presentations | Unlinkable PoP | LoA High PID presentation |
| Article 5a(5f) Regulation (EU) 2024/1183 | Prove that a signature validates with the public key in the attestation's PoP field | Hardware-bound PoP | LoA High PID presentation |
| Article 14(2) CIR 2024/2979; Article 5a(4b) Regulation (EU) 2024/1183; Topic G section 4.1 | Support pseudonyms deterministically derived from an attribute in a credential without exposing the attribute itself | Pseudonym derivation | Identity-bound stable site-specific unlinkable pseudonyms dervied from a private seed and public context |

### Operational and conformance obligations and related ZKP requirements

| Requirement | Source | Strength |
|-------------|--------|----------|
| The ZKP scheme SHALL support the generation of a proof that binds a credential to a specific user-controlled cryptographic key via a proof of possession | Topic G HLR; Article 5a(5d) Regulation (EU) 2024/1183 | MUST |
| The ZKP scheme SHALL support issuance using a hardware-protected key | Topic G HLR; Article 5a(5d) Regulation (EU) 2024/1183 | MUST |
| The ZKP scheme SHALL use only algorithms recognized by European Commission standards | Topic G HLR; eIDAS cryptographic requirements | MUST |
| The ZKP scheme SHALL declare conformance to one or more performance tiers as defined in ETSI TS 119 476-2 | Topic G HLR | MUST |
| The ZKP scheme SHALL support the generation of proofs for PID formatted in accordance with the PID Rulebook | Topic G HLR | MUST |
| The ZKP scheme SHALL support the generation of proofs for (Q)EAAs formatted in accordance with their respective applicable Rulebooks | Topic G HLR | MUST |

### The wallet threat register (CIR 2024/2981) and related ZKP requirements

| Threat | Legal source | Mapping | ZKP requirement |
|--------|-------------|---------|-----------------|
| TR29 — attacker identifies wallet user where identification is not required | Article 3(10) CIR 2024/2982; Article 5a(16) Regulation (EU) 2024/1183 | Presentations must not expose attributes or metadata that enable identification where identification is not required | Multi-show full unlinkability |
| TR36 — multiple transactions linkable to the same person within or across relying parties | Article 5(8) CIR 2024/2977; Article 3(10) CIR 2024/2982; Article 5a(16) Regulation (EU) 2024/1183 | No observable element in a presentation may serve as a correlation handle across transactions, whether within a single relying party or across different relying parties | Multi-show full unlinkability |
| TR37 — public revocation list reveals user attestation usage | Article 5(7) CIR 2024/2977; Article 7(4) CIR 2024/2979; CIR 2025/1569 Article 4(4) | Revocation status checks must not expose credential identifiers or allow behavioral mapping | Privacy-preserving revocation |
| TR39 — attacker traces wallet users through unique or traceable identifiers | Article 5(8) CIR 2024/2977; Article 3(10) CIR 2024/2982 | No static or reusable identifier may appear across presentations | Multi-show full unlinkability |
| TR83 — relying party derives identity beyond data shared with them | Recital 14 Regulation (EU) 2024/1183 | The ZKP scheme must not leak information beyond the truth value of the proven statement; zero-knowledgeness must hold against a single verifier | Zero-knowledgeness core property |
| TR84 — colluding relying parties or PID providers derive identity beyond data known to them individually | Article 5a(16) Regulation (EU) 2024/1183; Topic G section 4.1 | Unlinkability must hold even under collusion between issuers and verifiers; full unlinkability as defined by ETSI TR 119 476 is required | Full unlinkability under collusion |
| TR85 — attacker tracks user through PID where identification is not required | Article 5(8) CIR 2024/2977 | PID presentations must not expose correlation handles where identification is not required | Multi-show full unlinkability |

### Requirements without legal basis or that prescribe a solution-specific mechanism

#### Blind issuance

Topic G introduces blind issuance as an additional privacy property in Section 2:

> Additional privacy properties have also been defined in related literature. Such properties are:
>  * (Partially) Blind Issuance: A ZKP scheme may enable PID Providers or Attestation Providers to issue attestations with attributes that remain hidden towards the providers (but can be verifiably transferred from other attestations). - Topic G Section 2.

Topic G section 4.1 then grounds this in a specific use case:

> In some use cases, a Relying Party needs to correlate multiple presentations of the same attestation (e.g., to detect a "returning customer"). A ZKP scheme can be used to derive a pseudonym by combining an attribute of an attestation that is unique to the User with the Relying Party identifier [...] The attribute used for deriving the pseudonym shall also be hidden from the Provider; otherwise, a colluding Provider and Relying Parties would be able to correlate User presentations. This property can be achieved using blind issuance. - Topic G section 4.1.

This culminates in REQUIREMENT 4:

> A ZKP scheme SHOULD support the derivation of a verifiable User pseudonym, by combining an attribute value that is unique for the User with Relying Party specific context (e.g., the Relying Party identifier). In addition to the generic functions defined in ZKP_01, for this use case, a ZKP scheme SHALL provide support for the following functions: (i) generation of a request for the issuance of an attestation that includes a secret attribute unique to the User, without revealing this attribute to the Attestation Provider,  - REQUIREMENT 4

REQUIREMENT 4 conflates the mechanism with the requirement. Hiding the attribute from the issuer is one way to prevent a colluding issuer and relying party from unmasking a pseudonym, but it is not the only way. The underlying requirement is that pseudonym derivation cannot be reproduced from issuer-known inputs alone. Blind issuance satisfies this in pairing-based schemes such as BBS+, but circuit-based approaches can satisfy the same property through other mechanisms. Requirement 4 is therefore superceded by:

* The ZKP scheme SHOULD support the derivation of a unique and verifiable pseudonym by combining a unique hidden user attribute with a public context value provided by the verifier and possibly other inputs.
* If the pseudonym configuration does not support unmasking, the ZKP scheme SHALL ensure that reversing the pseudonym to a user identifying attribute is infeasible.
