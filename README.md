# CIVIC-FPGA Protocol Verification

This repository contains the formal verification results for the CIVIC-FPGA (Confidential IP Validation In Cloud for FPGA) protocol, which enables secure validation and deployment of FPGA designs in multi-tenant cloud environments.

## Protocol Overview

CIVIC-FPGA is a security protocol that:
- Ensures confidentiality of tenant FPGA designs during validation and deployment
- Provides secure design validation within cloud-provided Trusted Execution Environments (TEEs)
- Implements mutual authentication between tenants and FPGAs
- Establishes secure communication channels for bitstream transmission
- Prevents unauthorized access to tenant intellectual property

## Formal Verification Results

The protocol has been formally verified using ProVerif, an automated cryptographic protocol verifier. The verification confirms the following security properties:

### 1. Design Confidentiality
✅ `not attacker(secret_design[])` - Verified
- Confirms that tenant designs remain confidential and cannot be accessed by attackers

### 2. Design Validation Integrity
✅ `event(TEEValidatesDesign(d,h)) ==> event(TenantSendsDesign(d,n,sid))` - Verified
- Ensures TEE only validates designs that were sent by legitimate tenants

✅ `event(TEEValidatesDesign(d,h)) ==> event(TEEReceivesDesign(d,n,sid))` - Verified
- Confirms TEE validates only designs it has properly received

### 3. Design Reception Authentication
✅ `event(TEEReceivesDesign(d,n,sid)) ==> event(TenantSendsDesign(d,n,sid))` - Verified
- Verifies that designs received by TEE were genuinely sent by the tenant

### 4. Replay Attack Prevention
✅ `event(TEEReceivesDesign(d1,n,sid)) && event(TEEReceivesDesign(d2,n,sid)) ==> d1 = d2` - Verified
- Prevents replay attacks by ensuring unique association between designs and nonces

### 5. TEE Authentication
✅ `event(TenantVerifiesTEE(...)) ==> event(TEEProvidesAttestation(...))` - Verified
- Confirms proper TEE attestation and verification

### 6. Bitstream Verification
✅ `event(FPGAVerifiesBitstream(d,h)) ==> event(TEEValidatesDesign(d,h))` - Verified
- Ensures FPGA only configures designs that have been validated by TEE

### 7. Mutual Authentication
✅ `inj-event(SharedKeyEstablished(...)) ==> inj-event(MutualAuthentication(...))` - Verified
- Confirms proper mutual authentication before key establishment

✅ `event(MutualAuthentication(...)) ==> event(TenantAuthenticatesFPGA(...)) && event(FPGAAuthenticatesTenant(...))` - Verified
- Verifies both parties authenticate each other

### 8. Secure Key Establishment
✅ `event(SharedKeyEstablished(...)) ==> event(ECDHEPerformed(...)) && event(KeyDerivationPerformed(...))` - Verified
- Confirms proper key exchange and derivation procedures

## Implementation Requirements

### FPGA Manufacturer
- Must provision each device with unique cryptographic identity
- Must provide Endorsement Key (EK) pair
- Must store private EK in eFUSE memory
- Must sign public EK for device authenticity

### Cloud FPGA Provider
- Must manage TEE bootstrap and execution
- Must support workload isolation
- Must provide remote attestation capabilities
- Must maintain secure communication channels

## Security Guarantees

The formal verification confirms that CIVIC-FPGA provides:
- End-to-end confidentiality of tenant designs
- Protection against man-in-the-middle attacks
- Prevention of replay attacks
- Secure key establishment
- Authentic design validation
- Verified deployment process

## Tools Used

- **Verification Tool**: ProVerif
- **Model Type**: Applied Pi-calculus
- **Security Model**: Active attacker model
- **Cryptographic Primitives**: Asymmetric encryption, digital signatures, hash functions, ECDHE

## Repository Contents

- `civic-fpga.pv`: ProVerif model of the protocol
- `result.txt`: Complete verification results
- Supporting documentation and implementation guidelines

## Citation

If you use this protocol or verification results in your research, please cite:
```bibtex
@inproceedings{CIVIC-FPGA-2025,
  title={CIVIC-FPGA: A Trusted FPGA Design Validation by Multi-Tenant Cloud Providers},
  author={[Authors]},
  booktitle={The 33rd ACM/SIGDA International Symposium on Field-Programmable Gate Arrays},
  year={2025}
}
```