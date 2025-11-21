## Base Protocol Test Cases – EUDI Wallet Interoperability Test Bed+

This folder contains the **Base Protocol test cases** used by the **EUDI Wallet Interoperability Test Bed (ITB+)**.  
These test cases implement the **protocol-level test matrix** for:

- **Issuance (VCI)** – OpenID for Verifiable Credential Issuance v1.0 flows.
- **Presentation (OpenID4VP)** – OpenID for Verifiable Credential Presentation v1.0 flows for PID and mDL profiles.

The test cases here are the *green layer* “Test Cases and Profiles” described in the ITB architecture (see `docs/reference-implementation-interoperability-test-bed.md`).  
They are executed by the ITB platform against Reference Implementations (Issuer / Holder / Verifier) via the harmonised integration API (`/vci/offer`, `/vp/request`, `/handle-request`, `/poll`, `/logs/{sessionId}`).

The concrete XML artefacts and scriptlets implementing each test case are located in:

- `scriptlets/` – low-level executable steps for each test.
- `test-cases/` – individual test case definitions referencing scriptlets.
- `test-suite*.xml` – suite definitions that group test cases into runnable profiles.

Within `tests/base-protocols` there are two main profiles:

- `test-suite-we-build-v1/` – contains the **test suite for holder wallets**, exercising VCI v1.0 and VP v1.0 flows (PID and mDL) via the `/vci/offer` and `/vp/request` endpoints. These tests validate that a **holder wallet** correctly drives the issuance and presentation flows against the ITB refernce issuers and verifiers.
- `test-we-build-wallet-client/` – contains **two relying party test cases** that use a **reference implementation wallet inside the ITB+** to automatically test issuers and verifiers: one test targets **issuance (VCI v1.0)** and the other targets **presentation (VP v1.0)**. These flows allow issuers and verifiers to be tested in an automated way without requiring a human-operated wallet.

Implementers can use this README alongside the architectural and integration description in `docs/reference-implementation-interoperability-test-bed.md` to understand **which protocol behaviours are exercised by each test** and **which endpoint configuration is expected**.

---

## Issuance (VCI) Test Matrix

The following tests cover **VCI v1.0** issuance flows for PID (SD‑JWT) and mDL (mso_mdoc), across **authorization_code** and **pre_authorized_code** grants and different signing schemes.

| Test  | Scenario                             | Endpoint (Issuer RI)                                                                                                                                                             | Notes                                                                                                                         |
|-------|--------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| AC-01 | Auth code, SD-JWT, X.509 signer      | `/vci/offer?session_id=<SESSION_ID>&flow=authorization_code&tx_code_required=false&credential_type=urn:eu.europa.ec.eudi:pid:1&credential_format=sd-jwt&signature_type=x509`    | Baseline VCI v1.0 auth code flow for PID (SD‑JWT) with X.509 signing and `x5c` header; full `/authorize` → `/token` → `/credential` path. |
| AC-02 | Auth code, SD-JWT, embedded JWK      | `/vci/offer?session_id=<SESSION_ID>&flow=authorization_code&tx_code_required=false&credential_type=urn:eu.europa.ec.eudi:pid:1&credential_format=sd-jwt&signature_type=jwk`     | Same as AC‑01, but SD‑JWT is signed with an embedded public JWK in the JOSE `jwk` header.                                    |
| AC-03 | Auth code, SD-JWT, kid‑referenced JWK | `/vci/offer?session_id=<SESSION_ID>&flow=authorization_code&tx_code_required=false&credential_type=urn:eu.europa.ec.eudi:pid:1&credential_format=sd-jwt&signature_type=kid-jwk` | SD‑JWT auth code flow where JOSE header contains `kid`; key is resolved via issuer JWKS (`/jwks`).                           |
| AC-04 | Auth code, SD-JWT, did:web signer    | `/vci/offer?session_id=<SESSION_ID>&flow=authorization_code&tx_code_required=false&credential_type=urn:eu.europa.ec.eudi:pid:1&credential_format=sd-jwt&signature_type=did-web` | SD‑JWT auth code flow signed with DID Web (`did:web:{controller}#keys-1`); key is resolved from the DID document.            |
| AC-05 | Auth code, mDL, X.509 signer         | `/vci/offer?session_id=<SESSION_ID>&flow=authorization_code&tx_code_required=false&credential_type=org.iso.18013.5.1.mDL&credential_format=mso_mdoc&signature_type=x509`        | Auth code flow issuing ISO 18013‑5 mDL (`mso_mdoc`) using CBOR/COSE Sign1 with an X.509 signer.                              |
| PA-01 | Pre‑auth, no PIN, SD-JWT, X.509     | `/vci/offer?session_id=<SESSION_ID>&flow=pre_authorized_code&tx_code_required=false&credential_type=urn:eu.europa.ec.eudi:pid:1&credential_format=sd-jwt&signature_type=x509`   | Pre‑authorized code flow without tx‑code; wallet exchanges code at `/token` and calls `/credential` (X.509‑signed SD‑JWT).  |
| PA-02 | Pre‑auth, PIN, SD-JWT, X.509        | `/vci/offer?session_id=<SESSION_ID>&flow=pre_authorized_code&tx_code_required=true&credential_type=urn:eu.europa.ec.eudi:pid:1&credential_format=sd-jwt&signature_type=x509`    | Pre‑authorized code flow requiring tx‑code (PIN); SD‑JWT PID is X.509‑signed with `x5c` header.                             |
| PA-03 | Pre‑auth, no PIN, mDL               | `/vci/offer?session_id=<SESSION_ID>&flow=pre_authorized_code&tx_code_required=false&credential_type=org.iso.18013.5.1.mDL&credential_format=mso_mdoc&signature_type=x509`       | Pre‑authorized mDL issuance using CBOR/COSE Sign1 with X.509 signing.                                                        |

---

## Presentation (OpenID4VP) Test Matrix

The following tests cover **OpenID4VP v1.0** presentation flows for PID and mDL, across different **client_id_scheme**, **request_uri_method**, **response_mode**, and **transaction data** options.

| Test  | Scenario                        | Endpoint (Verifier RI)                                                                                                                                                            | Notes                                                                                                                                |
|-------|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| VP-01 | x509, POST, DCQL query         | `/vp/request?session_id=<VP_SESSION>&client_id_scheme=x509&profile=dcql&credential_profile=pid&request_uri_method=post&response_mode=direct_post&tx_data=false`                  | Baseline DCQL PID request with x509 client; wallet posts VP token to `/direct_post/{sessionId}` (key‑binding JWT, selective disclosure). |
| VP-02 | x509, POST, direct_post.jwt    | `/vp/request?session_id=<VP_SESSION>&client_id_scheme=x509&profile=dcql&credential_profile=pid&request_uri_method=post&response_mode=direct_post.jwt&tx_data=false`             | Same as VP‑01, but `response_mode=direct_post.jwt`; VP token is wrapped in signed (optionally encrypted) JWT/JWE (JARM‑style).     |
| VP-03 | x509, GET, DCQL                | `/vp/request?session_id=<VP_SESSION>&client_id_scheme=x509&profile=dcql&credential_profile=pid&request_uri_method=get&response_mode=direct_post&tx_data=false`                   | DCQL PID request where wallet fetches VP request via GET from `request_uri`, then posts VP to `/direct_post/{sessionId}`.          |
| VP-04 | x509, POST, tx‑data payload    | `/vp/request?session_id=<VP_SESSION>&client_id_scheme=x509&profile=tx&credential_profile=pid&request_uri_method=post&response_mode=direct_post&tx_data=true`                     | QES‑style transaction authorisation; VP request carries base64url‑encoded tx‑data, wallet binds tx‑data hash in key‑binding JWT.   |
| VP-05 | did:web, POST, DCQL            | `/vp/request?session_id=<VP_SESSION>&client_id_scheme=did:web&profile=dcql&credential_profile=pid&request_uri_method=post&response_mode=direct_post&tx_data=false`               | DCQL PID request with DID Web verifier (`did:web:{controller}`); wallet verifies signature via DID document.                       |
| VP-06 | did:web, GET, DCQL             | `/vp/request?session_id=<VP_SESSION>&client_id_scheme=did:web&profile=dcql&credential_profile=pid&request_uri_method=get&response_mode=direct_post&tx_data=false`                | Same as VP‑05, but wallet uses GET to fetch VP request from `request_uri`.                                                          |
| VP-07 | did:jwk, POST, DCQL            | `/vp/request?session_id=<VP_SESSION>&client_id_scheme=did:jwk&profile=dcql&credential_profile=pid&request_uri_method=post&response_mode=direct_post&tx_data=false`               | DCQL PID request with DID JWK identifier; wallet decodes public key directly from DID JWK (no HTTP resolution).                    |
| VP-09 | x509, GET, mDL request         | `/vp/request?session_id=<VP_SESSION>&client_id_scheme=x509&profile=mdl&credential_profile=mdl&request_uri_method=get&response_mode=direct_post&tx_data=false`                    | mDL/`mso_mdoc` presentation over OpenID4VP; wallet fetches request via GET and posts mDL response to `/direct_post/{sessionId}`.   |

---

For a full description of the ITB architecture, integration endpoints and logging model, see `docs/reference-implementation-interoperability-test-bed.md`. This README focuses on **which base protocol variants are exercised by this test suite** and **how they map to the harmonised endpoints**.

