# EUDI Wallet Interoperability Test Bed - Reference Implementation Specification

Abstract: This reference implementation specification is a guide for all wallet SW vendors to provide their implementation as a reference implementation with the ITB.  

Audience: WP4 - Testing Group, ITB platform developers and maintainers, Testing Group, Test Script Authors

**Table of Contents**

- [EUDI Wallet Interoperability Test Bed - Reference Implementation Specification](#eudi-wallet-interoperability-test-bed---reference-implementation-specification)
- [Revision History](#revision-history)
- [1.	Introduction](#1introduction)
  - [1.1	Purpose](#11purpose)
  - [1.2	Scope](#12scope)
  - [1.3	What the ITB is](#13what-the-itb-is)
  - [1.4	Architectural principles](#14architectural-principles)
- [2.	Architecture and separation of concerns](#2architecture-and-separation-of-concerns)
  - [2.1	ITB platform and user interface](#21itb-platform-and-user-interface)
  - [2.2	Reference implementation software (Issuer/ Holder/ Verifier)](#22reference-implementation-software-issuer-holder-verifier)
  - [2.3	Test cases and profiles](#23test-cases-and-profiles)
  - [2.4	Summary of layer boundaries and ownership](#24summary-of-layer-boundaries-and-ownership)
- [3. Integrations as reference implementor](#3-integrations-as-reference-implementor)
  - [3.1. Degrees of Freedom of the Integration API](#31-degrees-of-freedom-of-the-integration-api)
  - [3.2	Issuer](#32issuer)
  - [3.3	Holder](#33holder)
  - [3.4	Verifier / Relying Party](#34verifier--relying-party)
  - [3.5	Logging Endpoints Verifier/ Relying Party/Issuer/Holder Wallet](#35logging-endpoints-verifier-relying-partyissuerholder-wallet)
  - [3.6	Security considerations](#36security-considerations)
- [4.	Profiles and versions](#4profiles-and-versions)
- [5.	Optional capabilities](#5optional-capabilities)
- [6.	Developer workflows](#6developer-workflows)
- [Appendix A: Example scenario](#appendix-a-example-scenario)
- [Appendix B: OpenAPI yaml](#appendix-b-openapi-yaml)

# Revision History

<table>
  <tr>
   <td><strong>Revision</strong>
   </td>
   <td><strong>Date</strong>
   </td>
   <td><strong>Authors</strong>
   </td>
   <td><strong>Comments</strong>
   </td>
  </tr>
  <tr>
   <td>1.0
   </td>
   <td>21-Nov-2025
   </td>
   <td>Lal Chandran (iGrant.io), Nikos Triantafyllou (UAegean), George Padayatti (iGrant.io)
   </td>
   <td>First version after review within WP4 Testing Group
   </td>
  </tr>
</table>


# 1.	Introduction 


## 1.1	Purpose

This document introduces the technical architecture of the **EUDI Wallet Interoperability Test Bed**. It explains what each layer does, how the parts interact and where responsibilities sit. It is written for architects, platform engineers, Referent Implementation (RI) developers and governance reviewers. Furthermore, it provides an opportunity for any wallet SW vendor to provide a reference implementation. 

The document complements the EUDI Wallet ITB User Guide by detailing the internal architecture, session model, and integration contracts used by the ITB platform and Reference Implementations. 


## 1.2	Scope

The scope covers the ITB platform, the Reference Implementations that act as counterparties during testing and the test case layer that defines what is executed. Business application logic of real issuers, verifiers or relying parties is out of scope. The focus here is on vendors hooking their existing Issuers, Holders, and/or Verifiers as the reference implementation for interoperability tests in ITB. The vendor SW appears as a reference implementation (e.g. an Issuer, Holder or Verifier) in the ITB test portal.


## 1.3	What the ITB is

The ITB is an orchestration and reporting system that sits between systems under test. It coordinates sessions, drives reference services, captures evidence and produces conformance and interoperability reports. It does not embed domain business rules. It focuses on protocol behaviour and profile compliance.


## 1.4	Architectural principles

* Keep business logic outside the platform
* Make tests deterministic and repeatable
* Separate orchestration from execution
* Use small, stateless services that scale horizontally
* Version by profile so multiple protocol versions can run side by side


# 2.	Architecture and separation of concerns

The EUDI Wallet Interoperability Test Bed (ITB) is organised as a layered system to keep orchestration, execution and test logic cleanly separated as illustrated in Figure 01. 

The blue layer is the ITB platform you operate: it manages sessions, applies assertions and produces reports. The yellow layer provides minimal, deterministic Reference Implementations (RIs) for Issuer, Holder and Verifier, acting as stable counterparties during tests. The green layer defines the Test Cases and Profiles that specify what is executed and how results are judged. This separation allows profiles and protocol versions to evolve without altering the platform core, while keeping business logic out of the ITB.

![](itb-layered-arch.png)
Figure 01: EUDI Wallet Interoperability Test - System Architecture 


## 2.1	ITB platform and user interface

**Purpose.** Provide orchestration, evidence capture and operator experience for conformance and interoperability runs. The ITB Platform communicates via the Messaging Service (see §4), which bridges the ITB core to REST/SOAP endpoints of Reference Implementations.

**Components.**

* **Reporting:** Stores logs and evidence; renders signed reports.
* **ITB Platform Core:** Session lifecycle, test execution engine, artefact store and configuration.
* **Messaging Service:** Adapter that speaks GITB ITB SOAP northbound and REST/custom HTTP southbound; normalises responses to the session model.
* **Integration APIs:** Stable operator/automation interfaces.
* **User Interface:** Start runs, monitor progress, download reports.

**Responsibilities.**

* Orchestrate sessions and assertions; enforce tenant isolation and access control.
* Persistent evidence (QRs, JWTs, transcripts) and aggregate logs from MS and RIs.
* Expose safe operator actions via UI and APIs.

**Explicit non-responsibilities.**

* No domain business rules of real issuers/verifiers.
* No long-term storage of personal data beyond evidence retention policies.

## 2.2	Reference implementation software (Issuer/ Holder/ Verifier)

Reference Implementations (RIs) provide stable, low-state endpoints that mirror wallet, issuer, or verifier behaviours required for protocol-level testing. They serve as counterparties to the System Under Test (SUT) and can be reused across profiles.

**Purpose.** Act as deterministic, low-state counterparties for protocol testing only.

**Roles and minimal interfaces.**

<table>
  <tr>
   <td><strong>RI Role</strong>
   </td>
   <td><strong>Minimum Interfaces</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Issuer</strong>
   </td>
   <td> 
<ul>
 
<li><code>POST /request</code> → create VCI offer; return <code>uri</code> and base64 <code>qr</code>.</li>
 
<li><code>GET /poll?sessionId=…</code> → <code>PENDING|SUCCESS|FAIL</code> with <code>errors[]</code>.</li> 
</ul>
   </td>
  </tr>
  <tr>
   <td><strong>Verifier</strong>
   </td>
   <td> 
<ul>
 
<li><code>POST /request</code> → create VP request; return <code>uri</code> and base64 <code>qr</code>.</li>
 
<li><code>GET /poll?sessionId=…</code> → status as above.</li> 
</ul>
   </td>
  </tr>
  <tr>
   <td><strong>Holder</strong>
   </td>
   <td> 
<ul>
 
<li><code>POST /handle-request</code> → accept deep link; bind to session; optional <code>result</code>.</li>
 
<li><code>GET /poll?sessionId=…</code> → status as above.</li> 
</ul>
   </td>
  </tr>
</table>


**Responsibilities.**

* Implement only protocol-level behaviour needed for conformance.
* Remain deterministic and reproducible; support profile-toggled options (trust, revocation, DID resolution)

**Explicit non-responsibilities.**

* No domain workflows (KYC, payments, entitlements) or business logic.


## 2.3	Test cases and profiles

**Purpose.** Define *what* is tested, expected outcomes and evidence rules.

**Categories.**

* **Base Protocol Test Cases:** Canonical VCI and VP flows.
* **Functional Test Cases:** Errors, timeouts, retries, metadata and edge conditions.
* **Domain-Specific Test Cases:** Ecosystem requirements (e.g., Wallet Unit Attestation for PID issuance).

**Profiles.**

* Pin protocol versions (e.g., EWC, DIIPv4, WE BUILD).
* Toggle optional checks (revocation/status, trust, DID strategies).
* Declare trust material (EU Trust Lists, federation docs) and data models (SD-JWT, mdoc, VC-DM).

**Responsibilities.**

* Provide vectors, assertions and evidence mapping.
* Bind steps to RI endpoints through the Messaging Service.
* Produce an explicit conformance statement per run.


## 2.4	Summary of layer boundaries and ownership


<table>
  <tr>
   <td><strong>Layer</strong>
   </td>
   <td><strong>Owner</strong>
   </td>
   <td><strong>Primary responsibilities</strong>
   </td>
   <td><strong>Change control</strong>
   </td>
  </tr>
  <tr>
   <td>Blue. ITB platform, Messaging Service, Integration APIs, UI, Reporting
   </td>
   <td>Platform team
   </td>
   <td>Orchestration, sessioning, persistence, evidence, operator UX, tenancy and security
   </td>
   <td>Versioned by platform releases. Backwards-compatible Integration APIs
   </td>
  </tr>
  <tr>
   <td>Yellow. Reference Implementations for Issuer, Holder, Verifier
   </td>
   <td>RI team
   </td>
   <td>Deterministic protocol behaviour, minimal endpoints, optional features by profile
   </td>
   <td>Versioned per RI. Changes coordinated with profiles
   </td>
  </tr>
  <tr>
   <td>Green. Test Cases and Profiles
   </td>
   <td>Test authors and governance
   </td>
   <td>Test logic, vectors, assertions, evidence mapping, trust config
   </td>
   <td>Versioned test suites. Profiles carry protocol version pins
   </td>
  </tr>
</table>


# 3. Integrations as reference implementor

For any party wishing to be a reference implementor (Issuer/Holder/Verifier), the following interfaces shall be supported.

## 3.1. Degrees of Freedom of the Integration API

The integration API for the Reference Implementations (Issuer, Verifier and Holder Wallet) is defined around a set of degrees of freedom that reflect the capabilities of the supported test suites.

If new testing requirements emerge, this list – and therefore the integration interface – may be extended.


## 3.2	Issuer

Issuer behaviour is parameterised along the following axes:

* **Flow type:**  authorization_code | pre_authorized_code
* **Tx-code (PIN) requirement:**  true | false
* **Credential type**:  e.g. urn:eu.europa.ec.eudi:pid:1, org.iso.18013.5.1.mDL, …
* **Credential format:** sd-jwt | mso_mdoc (for mDL)
* **Signature type:** x509 | jwk | kid-jwk | did:web
* **Session id / test id:**  Correlation identifier: &lt;SESSION_ID>

**Request:**

Description: endpoint that generates the actual credential offer

Method: GET | Path` /vci/offer`

```
GET /vci/offer
    ?session_id=<SESSION_ID>
    &flow=authorization_code
    &tx_code_required=false
    &credential_type=urn:eu.europa.ec.eudi:pid:1
    &credential_format=sd-jwt
    &signature_type=x509
```

**Response:**

```
{
  "uri": "openid-credential-offer://...",
  "qr": "<base64 PNG or SVG>",
}
```

**Poll**

Description: endpoint fetches the status of the issuance flow

Method: GET | Path: `/poll?sessionId=<uuid>`

Response:

```
{
  "sessionId": "<uuid>",
  "status": "PENDING|SUCCESS|FAIL",
  "errors": [ { "code": "RI-001", "message": "..." } ]
}
```

## 3.3	Holder

The Holder Wallet integration is flow-agnostic and driven entirely by the OpenID deep link generated for each test. Every VCI and VP test case is mapped to a deep link, which is then passed to the wallet.

**Request:** 

Description: endpoint accepts a credential offer or vp request

Method: POST | Path: `/handle-request`

```
{
  "deepLink": "openid4vp://?request_uri=https%3A%2F%2Fexample.test%2Fx509%2Fx509VPrequest%2F1234&client_id=x509_san_dns%3Adss.aegean.gr&request_uri_method=post",
  "sessionId": 14074,
  "pin":123,
}
```


**Response**:

```
{
  "sessionId": "<uuid>",
  "status": "ok",
  "result": {
    "credentials": [ { "credential": "<opaque string such as JWT or SD-JWT>" } ]
  },
  "updatedAt": "2025-09-25T13:04:01.417Z"
}
```

**Fields:**

1. **deepLink** (string, required).  The full OpenID deep link as generated by the issuer or verifier:
* VP tests: openid4vp://?...
* VCI tests: openid4vci://?...

    **It** must be passed exactly as encoded in the QR/test case, including URL-encoded query parameters.

2. **sessionId** (string or integer, required):  Correlation identifier used by the test suite to link this wallet run to the corresponding ITB+ session.

3. **pin** (string or integer, **OPTIONAL**) parameter that can accept a transaction code for use in per-authorisation code flows. 

**Expected behaviour**

The wallet implementation SHOULD:

* Parse the deepLink.
* Determine whether it is a VCI or VP flow based on the scheme (openid4vci vs openid4vp).
* Execute the full underlying protocol (issuer or verifier interaction) according to the relevant specifications (OIDC4VCI 1.0 / OpenID4VP 1.0).
* Report status/results back to the test harness (implementation-specific).

**Poll:** Same as 3.2 Poll.


## 3.4	Verifier / Relying Party

Verifier behaviour is parameterised along these axes:

* **Client_id_scheme:**  x509 | did:web | did:jwk
* **Presentation profile:**
    * dcql – DCQL query for PID credentials
    * tx – transaction-data / QES scenarios
    * mdl – mDL / mdoc requests
* **Request URI fetch method:**  GET | POST
* **Response_mode:**  direct_post | direct_post.jwt
* **Credential type / profile**:  e.g. pid | mdl
* **Session id:**  Correlation identifier: &lt;VP_SESSION>

**Request:**

Description: endpoint that generates a vp request

Method: GET | Path: `/vp/request`

Request body:

```
GET /vp/request
    ?session_id=<VP_SESSION>
    &client_id_scheme=x509
    &profile=dcql
    &credential_profile=pid
    &request_uri_method=post
    &response_mode=direct_post
    &tx_data=false
```

**Response:**

```
{
  "uri": "openid4vp://?request_uri=...",
  "qr": "<base64 PNG or SVG>",
}
```

**Poll**

Method: GET | Path: `/poll?sessionId=<uuid>`

Response:

```
{
  "sessionId": "<uuid>",
  "status": "PENDING|SUCCESS|FAIL",
  "errors": [ { "code": "RI-001", "message": "..." } ]
}
```

## 3.5	Logging Endpoints Verifier/ Relying Party/Issuer/Holder Wallet

All implementations (Issuer, Verifier and Holder Wallet) MUST expose a common logging endpoint that allows the test harness to retrieve execution logs for a given test session.

Each component exposes:

Description: endpoint that returns the generated log entries based on the session id provided

**Method**: GET | Path: <code>/logs/{sessionId}</code>

**Request**:

```
GET /logs/{sessionId}
```

**Response**:

```
{
  "sessionId": "123456",
  "logs": [
    {
      "level": "info",
      "message": "[/session] VCI deepLink: openid-credential-offer://?credential_offer_uri=https%3A%2F%2Fdss.aegean.gr%2Frfc-issuer%2Fcredential-offer-code-sd-jwt%2Fb4aea372-81eb-4e43-a501-543569b21c9e%3FcredentialType%3Durn%253Aeu.europa.ec.eudi%253Apid%253A1%26scheme%3Dredirect_uri",
      "timestamp": "2025-09-12T15:42:08.649Z"
    },
    {
      "level": "info",
      "message": "[offer] fetching offer {\"offerUri\":\"https://dss.aegean.gr/rfc-issuer/credential-offer-code-sd-jwt/b4aea372-81eb-4e43-a501-543569b21c9e?credentialType=urn:eu.europa.ec.eudi:pid:1&scheme=redirect_uri\"}",
      "timestamp": "2025-09-12T15:42:08.651Z"
    },
    {
      "level": "info",
      "message": "[offer] offer status {\"status\":200}",
      "timestamp": "2025-09-12T15:42:08.767Z"
    }
    // ... more log entries ...
  ]
}
```


The message field is intentionally free-form; the only requirement is that it is human-readable and useful for debugging the test run.


## 3.6	Security considerations

To reduce the risk of abuse (e.g. crawling, DDoS) implementers may protect the integration endpoints:


*  `/vci/offer`, `/vp/request`, `/session`, `/logs/{sessionId}` 

with lightweight security controls, such as:

1. API key in header (e.g. X-ITB-API-Key: &lt;secret>)
2. HTTP Basic authentication on the integration endpoints
3. IP allowlists (only permit ITB / lab networks)
4. mTLS for more advanced deployments
5. Rate limiting, especially on /logs/{sessionId} and /session

In the OpenAPI specification below, an ApiKeyAuth (header-based) security scheme is defined. It can either:

* Be attached via security: on specific paths, or
* Remain defined but unused for local, unsecured lab environments.


# 4.	Profiles and versions

The following profiles are under consideration. 

* **WE BUILD:** VCI v1.0, VP v1.0

Profiles set protocol versions, feature flags, trust material, DID resolution strategies and data models (SD-JWT, mdoc, VC-DM).


# 5.	Optional capabilities

Trust framework; revocation/status; schema checks with negative tests; use-case specifics such as mandatory Wallet Unit Attestation for PID issuance. Each is switchable per test case definition.


# 6.	Developer workflows

The typical development workflow for any wallet SW provider is as envisioned below. Provide the following metadata to Testing Group via [WEBUILD Slack channel](https://we-build-consortium.slack.com/archives/C09JQQD5865):


<table>
  <tr>
   <td><strong>Wallet SW Vendor Name (Country)</strong>
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td><strong>Business Wallet Functions Provided</strong>
   </td>
   <td><em>For example,</em>
<p>
<em>Issuer</em>
<p>
<em>Holder</em>
<p>
<em>Verifier</em>
   </td>
  </tr>
  <tr>
   <td><strong>Issuer Endpoint</strong>
   </td>
   <td><em>Either “NA” or the URL</em>
   </td>
  </tr>
  <tr>
   <td><strong>Holder Endpoint</strong>
   </td>
   <td><em>Either “NA” or the URL</em>
   </td>
  </tr>
  <tr>
   <td><strong>Verifier Endpoint</strong>
   </td>
   <td><em>Either “NA” or the URL</em>
   </td>
  </tr>
</table>


An example is as provided below:


<table>
  <tr>
   <td><strong>Wallet SW Vendor Name (Country)</strong>
   </td>
   <td>iGrant.io (Sweden)
   </td>
  </tr>
  <tr>
   <td><strong>Business Wallet Functions Provided</strong>
   </td>
   <td>Provides all three implementations: 
<p>
Issuer
<p>
Holder
<p>
Verifier
   </td>
  </tr>
  <tr>
   <td><strong>Issuer Endpoint</strong>
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td><strong>Holder Endpoint</strong>
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td><strong>Verifier Endpoint</strong>
   </td>
   <td>
   </td>
  </tr>
</table>



# Appendix A: Example scenario

**Scenario #01:** A vendor ABC wishing to be a reference issuer can hook their Issuer to the ITB. This will then be available as a choice for any other vendor to select from when they test their respective implementation. E.g. vendor XYZ could use vendor ABC’s Issuer to test against their Holder. 

**Scenario #02:** A vendor ABC has an issuer based on OpenID4VCI v1.0 and v2.0. Vendor XYZ only has v1.0. They can test against ABC’s IDv1.0 in this scenario via the ITB. 


# Appendix B: OpenAPI yaml


```
openapi: 3.0.3
info:
  title: EUDI Wallet Test Issuer, Verifier & Holder API
  version: 1.0.2
  description: |
    Harmonised API for driving VCI (issuance), OpenID4VP (presentation) and
    Holder Wallet test cases as used in the ITB+ test matrix.

servers:
  - url: http://localhost:3000
    description: Local issuer/verifier test server
  - url: http://localhost:4000
    description: Local holder wallet test server

components:
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      in: header
      name: X-ITB-API-Key

  schemas:
    CredentialOffer:
      type: object
      description: >
        Lightweight representation of a credential offer. The offer is primarily
        conveyed through an OpenID-credential-offer URI, with an optional
        QR-code representation.
      required:
        - uri
      properties:
        uri:
          type: string
          description: >
            Credential offer URI using openid-credential-offer:// or openid-vc://
            scheme.
          example: "openid-credential-offer://?credential_offer_uri=https%3A%2F%2Fissuer.example.org%2Fcredential-offer%2Fb4aea372-81eb-4e43-a501-543569b21c9e"
        qr:
          type: string
          description: >
            Base64-encoded PNG or SVG representing the QR code for the credential
            offer. Optional.
          example: "iVBORw0KGgoAAAANSUhEUgAA..."

    VPRequest:
      type: object
      description: >
        Generic placeholder for an OpenID4VP request object or metadata.
        Exact structure is implementation-specific.
      additionalProperties: true

    HolderRequest:
      type: object
      required:
        - deepLink
        - sessionId
      properties:
        deepLink:
          type: string
          description: >
            Full OpenID deep link (openid4vp:// or openid4vci://) as encoded
            in the QR or test case.
          example: "openid4vp://?request_uri=https%3A%2F%2Fexample.test%2Fx509%2Fx509VPrequest%2F1234&client_id=x509_san_dns%3Adss.aegean.gr&request_uri_method=post"
        sessionId:
          oneOf:
            - type: string
            - type: integer
          description: >
            Correlation identifier used by the test harness to link this wallet
            run to the corresponding ITB+ session.
          example: 14074

    HolderResponse:
      type: object
      description: Result of handling a VCI/VP deep link by the holder wallet.
      required:
        - sessionId
        - status
      properties:
        sessionId:
          oneOf:
            - type: string
            - type: integer
          example: "14074"
        status:
          type: string
          description: Overall status of the operation.
          example: "ok"
        result:
          type: object
          description: Optional result details (e.g. issued credentials).
          properties:
            credentials:
              type: array
              items:
                type: object
                properties:
                  credential:
                    type: string
                    description: >
                      Opaque credential representation (e.g. JWT, SD-JWT, mdoc).
                    example: "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9..."
        updatedAt:
          type: string
          format: date-time
          description: Timestamp when the holder last updated the session.
          example: "2025-09-25T13:04:01.417Z"

    PollError:
      type: object
      description: Error item returned by /poll.
      properties:
        code:
          type: string
          description: Implementation-specific error code.
          example: "RI-001"
        message:
          type: string
          description: Human-readable error message.
          example: "Issuer metadata could not be resolved."

    PollStatus:
      type: object
      description: Status of a test session (Issuer, Verifier or Holder).
      required:
        - sessionId
        - status
      properties:
        sessionId:
          oneOf:
            - type: string
            - type: integer
          example: "b4aea372-81eb-4e43-a501-543569b21c9e"
        status:
          type: string
          enum: [PENDING, SUCCESS, FAIL]
          example: "PENDING"
        errors:
          type: array
          description: Optional list of error entries if status is FAIL.
          items:
            $ref: '#/components/schemas/PollError'

    LogEntry:
      type: object
      description: Single log entry for a test session.
      properties:
        level:
          type: string
          description: Log level (e.g. info, error, warn, debug).
          example: "info"
        message:
          type: string
          description: Log message (free-form).
          example: "[offer] offer status {\"status\":200}"
        timestamp:
          type: string
          format: date-time
          description: ISO-8601 timestamp of the log event.
          example: "2025-09-12T15:42:08.767Z"

    LogsResponse:
      type: object
      description: Collection of logs for a given session.
      properties:
        sessionId:
          oneOf:
            - type: string
            - type: integer
        logs:
          type: array
          items:
            $ref: '#/components/schemas/LogEntry'

paths:
  /vci/offer:
    get:
      summary: Generate a credential offer for VCI test flows
      description: |
        Returns a credential offer configured according to the requested flow,
        credential type/format and signing method.

        This endpoint covers:
        - Authorization Code flows (with PKCE)
        - Pre-Authorized Code flows (with/without tx-code/PIN)
        - SD-JWT and mdoc/mso_mdoc credentials
        - x509, embedded JWK, kid-referenced JWK, and did:web signing
      parameters:
        - name: session_id
          in: query
          required: true
          schema:
            type: string
          description: Correlation identifier for the test session.
        - name: flow
          in: query
          required: true
          schema:
            type: string
            enum: [authorization_code, pre_authorized_code]
          description: |
            VCI grant type used for issuance:
            - `authorization_code` - full OAuth2 authorization code flow with PKCE
            - `pre_authorized_code` - pre-authorized code flow as per OIDC4VCI
        - name: tx_code_required
          in: query
          required: true
          schema:
            type: boolean
          description: |
            Indicates whether a transaction code (PIN) is required in the
            pre-authorized flow. Ignored for `authorization_code` flows.
        - name: credential_type
          in: query
          required: true
          schema:
            type: string
          description: |
            Credential type identifier (e.g. `urn:eu.europa.ec.eudi:pid:1`,
            `org.iso.18013.5.1.mDL`).
        - name: credential_format
          in: query
          required: true
          schema:
            type: string
            enum: [sd-jwt, mso_mdoc]
          description: |
            Credential format to issue:
            - `sd-jwt` - SD-JWT based credential
            - `mso_mdoc` - ISO 18013-5 mDL / mso_mdoc credential
        - name: signature_type
          in: query
          required: true
          schema:
            type: string
            enum: [x509, jwk, kid-jwk, did-web]
          description: |
            Signing scheme for the issued credential:
            - `x509` - JOSE/COSE with X.509 certificate, `x5c` in the header
            - `jwk` - embedded public JWK in JOSE header
            - `kid-jwk` - JOSE header with `kid`, key resolved from JWKS
            - `did-web` - JOSE header with DID Web key id, key resolved via DID document
      responses:
        '200':
          description: Credential offer response
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CredentialOffer'
              examples:
                sdJwtPidOffer:
                  summary: SD-JWT PID credential offer (authorization_code)
                  value:
                    uri: "openid-credential-offer://?credential_offer_uri=https%3A%2F%2Fissuer.example.org%2Fcredential-offer%2Fb4aea372-81eb-4e43-a501-543569b21c9e"
                    qr: "iVBORw0KGgoAAAANSUhEUgAA..."
        '400':
          description: Invalid parameters

  /vp/request:
    get:
      summary: Generate an OpenID4VP Verifiable Presentation request
      description: |
        Returns a Verifiable Presentation request object (or request_uri) configured
        for the desired client_id_scheme, response_mode, and profile
        (DCQL PID, transaction/QES, mDL, etc.).

        This endpoint covers:
        - x509 / did:web / did:jwk client_id_schemes
        - DCQL-based PID requests
        - Transaction-data/QES authorisation scenarios
        - mDL / mdoc presentation requests
        - GET/POST retrieval of the request_uri
        - direct_post and direct_post.jwt response modes
      parameters:
        - name: session_id
          in: query
          required: true
          schema:
            type: string
          description: Correlation identifier for the VP test session.
        - name: client_id_scheme
          in: query
          required: true
          schema:
            type: string
            enum: [x509, did:web, did:jwk]
          description: |
            Client identification / metadata resolution scheme:
            - `x509`   - TLS / certificate-based client_id
            - `did:web` - DID Web based client identifier
            - `did:jwk` - DID JWK based client identifier
        - name: profile
          in: query
          required: true
          schema:
            type: string
            enum: [dcql, tx, mdl]
          description: |
            High-level presentation profile:
            - `dcql` - DCQL-based request (e.g. PID with claim filters)
            - `tx`   - Transaction-data / QES authorisation profile
            - `mdl`  - mDL / mdoc-based request
        - name: credential_profile
          in: query
          required: true
          schema:
            type: string
          description: |
            Logical credential profile requested (e.g. `pid`, `mdl`).
            Used to select the appropriate DCQL query or presentation definition.
        - name: request_uri_method
          in: query
          required: true
          schema:
            type: string
            enum: [get, post]
          description: |
            HTTP method that the wallet uses to fetch the VP request from the
            `request_uri` endpoint:
            - `get`  - wallet uses GET to fetch request
            - `post` - wallet uses POST to fetch request
        - name: response_mode
          in: query
          required: true
          schema:
            type: string
            enum: [direct_post, direct_post.jwt]
          description: |
            Response mode used by the wallet when returning the VP token:
            - `direct_post`      - plain direct_post with form-encoded payload
            - `direct_post.jwt`  - JARM-style JWT/JWE wrapper
        - name: tx_data
          in: query
          required: true
          schema:
            type: boolean
          description: |
            Whether transaction data (for QES-style flows) is attached and its
            hash is to be bound in the key-binding JWT.
      responses:
        '200':
          description: VP request or request_uri information
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/VPRequest'
        '400':
          description: Invalid parameters

  /handle-request:
    post:
      servers:
        - url: http://localhost:4000
          description: Local holder wallet test server
      summary: Handle an OpenID VCI/VP deep link
      description: |
        Accepts a credential offer or VP request deep link and executes the
        corresponding issuance or presentation flow within the holder wallet.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/HolderRequest'
            examples:
              vpExample:
                summary: Example VP session
                value:
                  deepLink: "openid4vp://?request_uri=https%3A%2F%2Fexample.test%2Fx509%2Fx509VPrequest%2F1234&client_id=x509_san_dns%3Adss.aegean.gr&request_uri_method=post"
                  sessionId: 14074
      responses:
        '200':
          description: Holder wallet processed the request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/HolderResponse'
              examples:
                okExample:
                  summary: Example holder response with one credential
                  value:
                    sessionId: "14074"
                    status: "ok"
                    result:
                      credentials:
                        - credential: "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9..."
                    updatedAt: "2025-09-25T13:04:01.417Z"
        '400':
          description: Invalid request

  /poll:
    get:
      summary: Fetch status for a test session (Issuer, Verifier or Holder)
      description: |
        Returns the current status of a given session for Issuer, Verifier, or
        Holder Wallet roles. Each implementation exposes this endpoint under
        its own base URL.
      parameters:
        - name: sessionId
          in: query
          required: true
          schema:
            oneOf:
              - type: string
              - type: integer
          description: Session identifier used for the test run.
      responses:
        '200':
          description: Poll status response
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PollStatus'
              examples:
                pending:
                  summary: Pending issuance
                  value:
                    sessionId: "b4aea372-81eb-4e43-a501-543569b21c9e"
                    status: "PENDING"
                success:
                  summary: Successful flow
                  value:
                    sessionId: "b4aea372-81eb-4e43-a501-543569b21c9e"
                    status: "SUCCESS"
                    errors: []
                fail:
                  summary: Failed flow
                  value:
                    sessionId: "b4aea372-81eb-4e43-a501-543569b21c9e"
                    status: "FAIL"
                    errors:
                      - code: "RI-001"
                        message: "Issuer metadata could not be resolved."
        '400':
          description: Invalid session id

  /logs/{sessionId}:
    get:
      summary: Fetch logs for a given test session
      description: |
        Returns the log entries produced during execution of a given test session.
        All roles (issuer, verifier, holder wallet) SHOULD expose this endpoint
        on their respective base URL.
      parameters:
        - name: sessionId
          in: path
          required: true
          schema:
            oneOf:
              - type: string
              - type: integer
          description: Session identifier used for the test run.
      responses:
        '200':
          description: Logs for the requested session
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LogsResponse'
              examples:
                exampleLogs:
                  summary: Example logs response
                  value:
                    sessionId: "123456"
                    logs:
                      - level: "info"
                        message: "[/session] VCI deepLink: openid-credential-offer://?credential_offer_uri=https%3A%2F%2Fdss.aegean.gr%2Frfc-issuer%2Fcredential-offer-code-sd-jwt%2Fb4aea372-81eb-4e43-a501-543569b21c9e%3FcredentialType%3Durn%253Aeu.europa.ec.eudi%253Apid%253A1%26scheme%3Dredirect_uri"
                        timestamp: "2025-09-12T15:42:08.649Z"
                      - level: "info"
                        message: "[offer] offer status {\"status\":200}"
                        timestamp: "2025-09-12T15:42:08.767Z"
        '404':
          description: No logs found for the given session
```