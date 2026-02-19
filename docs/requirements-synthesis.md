# Dervied ZKP requirements from the legal sources and ARF

| Source | Mapping | ZKP requirement | Use case example |
|--------|---------|-----------------|------------------|
| Article 5(7) CIR 2024/2977 | The validity status object must not itself include correlation handles or allow for behavioral mapping | Privacy-preserving revocation | The public revocation registry, or the validity status proof, does not expose any credential identifier |
| Recital (9) and Article 7(4) CIR 2024/2979 | Validity status must be verifiable without exposing revocation identifiers | Privacy-preserving revocation | Proving wallet unit attestation validity without creating a correlation handle |
| Article 5(8) CIR 2024/2977 | Full PID presentation unlinkability when user identification is not required | Multi-show full unlinkability; predicate proofs | All PID presentations where the user does not need to identify itself; Age proofs (e.g. over 18 without revealing date of birth) |
| Recital (8) CIR 2024/2979 | Cross-relying-party unlinkability for wallet unit attestations; proofs freshly generated per session without pre-computation | Multi-show full unlinkability | All use cases where the user does not reveal identity (recommended for WUA) |
| Recital (6) CIR 2024/2977 | Cross-credential linking proofs | Cross-credential presentation | Combined PID and attestation presentation |
| Article 3(5) CIR 2024/2977 | Proof of Possession must not introduce a static correlation handle across presentations | Unlinkable PoP | LoA High PID presentation |
| Article 14(2) CIR 2024/2979 | Support pseudonyms deterministically derived from a seed without exposing the seed itself | Pseudonym derivation | Identity-bound stable site-specific unlinkable pseudonyms |
