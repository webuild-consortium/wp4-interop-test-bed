# EUDI Wallet Interoperability Test Bed - User Guide

**Abstract**:  

This guide explains how issuers, verifiers, and wallet providers onboard the EUDI Wallet Interoperability Test Bed (ITB), run reference tests, collect evidence, and request compliance sign-off. It focuses on operational steps and minimal REST contracts; it does not describe the internal ITB architecture.

**Audience**: Wallet Providers (Individual and Business Wallet), Issuers and Verifiers

**How to Use this Guide**:  

* If you are NEW to the ITB: read Sections 1–2 (Onboarding and Running tests).  
* If you are an IMPLEMENTER: use Section 3 (Minimal Endpoints) and the OpenAPI templates.  
* If you are a TEST AUTHOR: see Section 2.3 for adding test cases and required metadata.

**Table of Contents**

- [EUDI Wallet Interoperability Test Bed - User Guide](#eudi-wallet-interoperability-test-bed---user-guide)
- [Revision History](#revision-history)
- [1. Introduction](#1-introduction)
  - [1.1 Purpose](#11-purpose)
  - [1.2 Scope](#12-scope)
  - [1.3 What the ITB is](#13-what-the-itb-is)
  - [1.4 Architectural principles](#14-architectural-principles)
- [2. Onboarding to ITB](#2-onboarding-to-itb)
  - [2.1 Obtaining the access](#21-obtaining-the-access)
  - [2.2 Using the test cases provided](#22-using-the-test-cases-provided)
  - [2.3 Adding more test cases](#23-adding-more-test-cases)
- [3. Minimal integration contracts](#3-minimal-integration-contracts)
  - [3.1 Issuer / Verifier: Required endpoints](#31-issuer--verifier-required-endpoints)
  - [3.1 Issuer and Verifier endpoints](#31-issuer-and-verifier-endpoints)
  - [3.2 Holder Wallet endpoints](#32-holder-wallet-endpoints)
  - [3.3 Observability and logs (optional)](#33-observability-and-logs-optional)
  - [3.4 OpenAPI reference with example](#34-openapi-reference-with-example)
- [Appendix: User workflow with ITB](#appendix-user-workflow-with-itb)

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
   <td>Nikos Triantafyllou (UAegean),
<p>
Lal Chandran (iGrant.io),
<p>
George Padayatti (iGrant.io)
   </td>
   <td>First version after review within Testing Group
   </td>
  </tr>
</table>

# 1. Introduction

This guide describes how to connect your Issuer, Verifier, or Holder Wallet to the EUDI Wallet Interoperability Test Bed (ITB), execute deterministic reference tests, and submit evidence for compliance sign-off. The ITB provides neutral orchestration, logging, and reporting to validate protocol behavior and profile conformance across implementations.

## 1.1 Purpose

The purpose of this guide is to describe what you must do to join the ITB and achieve compliance sign off. It defines the artefacts to supply, the interfaces to use, the test steps to execute, and the evidence to submit. It also clarifies responsibilities for issuers, verifiers, wallet providers, and the ITB operator, and sets out the sequence of activities from registration to approval.

Deliverables expected from the implementers looking to use the ITB: Endpoints, Configuration(s).

Outputs (you receive): Session-bound reports (PASS/FAIL), logs, and a sign-off record if all mandatory tests pass.

## 1.2 Scope

This guide covers onboarding and compliance activities only. It includes environment preparation, reference test execution, log and report submission, management of non conformities, and the request for final sign off. It does not provide architectural explanations or design details of the ITB. In a nutshell:

In scope:

* Onboarding steps, environment preparation, running tests, collecting evidence.
* Minimal REST contracts for Issuer, Verifier, and Holder Wallet roles.
* Reporting, non-conformity handling, and sign-off request process.

Out of scope:

* Deep architectural design of the ITB internals.
* Product-specific business logic and domain rules.
* Full profile specifications (refer to profile rulebooks and OIDC specs).

## 1.3 What the ITB is

The ITB is an orchestration and reporting system that sits between systems under test. It coordinates sessions, drives reference services, captures evidence and produces conformance and interoperability reports. It does not embed domain business rules. It focuses on protocol behaviour and profile compliance. The ITB does not enforce domain business rules; it validates protocol behaviour and profile compliance.

## 1.4 Architectural principles

* Keep business logic outside the platform
* Make tests deterministic and repeatable
* Separate orchestration from execution
* Use small, stateless services that scale horizontally
* Version by profile so multiple protocol versions can run side by side

# 2. Onboarding to ITB

## 2.1 Obtaining the access

**Objective:** Establish a tenant, credentials, and profile so your implementation can run reference tests.

**Procedure:** Follow the steps below to get onboarded to ITB:

**Step 1:** Contact [#itb-support Slack Channel](https://we-build-consortium.slack.com/archives/C09K65GLKT2) with:

* Organisation name and contacts (technical + governance)
* Intended roles: Issuer / Verifier / Holder (one or more)
* Targeted profiles & protocol versions (e.g., OID4VCI 1.0; OID4VP 1.0; SD-JWT; mdoc)

**Step 2:** Provide base URLs (dev/acc/prod) and security mode (bearer, mTLS, x-api-key).

You will now receive the following:

* An ITB tenant with namespaced configuration  
* Credentials (per tenant/profile)  
* Access to reference test suites and the reporting portal

```Tip: Use distinct "Systems" in your tenant per environment to keep evidence reproducible.```

## 2.2 Using the test cases provided

**Objective:** execute reference/conformance tests deterministically and collect evidence for reporting.

**Procedure:** Follow the steps below to carry out a specific test case in the ITB:

1. Create a test session in the ITB: select test suite and/or specific test case
2. Run flow: complete any wallet interaction (scan, consent) as prompted.
3. Report: download the conformance/interoperability report with debug logs and artefacts.

**Evidence collected:** Request URIs, QR images (base64), JWT/SD-JWT artifacts (or redacted equivalents). ITB Reference implementation logs are bound to the sessionId. Timestamps and status transitions.

**Outcome:** Each mandatory test is marked PASS/FAIL. Warnings and non-conformities (if any) are listed with evidence pointers.

## 2.3 Adding more test cases

**Objective:** Extend coverage with deterministic, tenant-specific test scenarios to enable interop testing across the ecosystem.

**What you submit:**

* A test case/suite description containing technical implementation details of the system exposed via the test suite (e.g. issuance or verification, revocation/status support, metadata resolution, PAR/no-PAR, PIN/no-PIN, credential formats etc. including profile & versions of protocol supported).

* Configuration yml: A yml defining the configurations necessary for your specific test case including, test vectors & outcomes such as credential issued/requested, the necessary endpoints of the system exposed for testing & auth (if required): i.e. base URLs and security mode of the system under test.

**How ITB integrates:** ITB registers the new test suite in your tenant and enables it for all ITB tenants for interoperability testing. ITB handles the orchestration of the flow; human interaction might be necessary depending on the system being tested (e.g. scan QR codes displayed).

**Evidence** (QRs, URIs, logs, timestamps) is collected automatically in reports.

**Acceptance:** Deterministic outcomes per sessionId. Terminal state reached within the time budget (SUCCESS or FAIL).

**Implementation details**, required endpoints, payloads and polling semantics are specified in Section 3. Minimal integration contracts.

# 3. Minimal integration contracts

The ITB interacts with connected systems via **a minimal set of deterministic REST endpoints**. All operations are keyed by a unique `**sessionId**` generated by the ITB. Endpoints MUST be:

* Stateless or idempotent per `sessionId`,
* Optionally authenticated (bearer token and/or mTLS; x-api-key supported),
* JSON-based with explicit success/fail semantics,
* Observable (report PENDING → terminal state: SUCCESS or FAIL).
* Default polling policy (unless overridden by the test case):
  * cadence: every 10 seconds
  * timeout: 60 seconds

```
NOTE: In the following sections, the endpoint names (e.g. /request, /poll) are given as an example and are up to the implementer to provide the correct values 
```

## 3.1 Issuer / Verifier: Required endpoints

## 3.1 Issuer and Verifier endpoints

**Request:**

Method: POST | Path: <code>/request</code>

**Purpose:**

Create a VCI request (Issuer) or a VP request (Verifier) bound to an ITB session. \

**Request body:**

```
{
  "sessionId": "<uuid>",
  "credentialType": "<string>"
}
```

**Response:**

```
{
  "uri": "openid-credential-offer://...",
  "qr": "<base64 PNG or SVG>",
}
```

**Poll**

Method: GET | Path: `/poll?sessionId=<uuid`

Response:

```
{
  "sessionId": "<uuid>",
  "status": "PENDING|SUCCESS|FAIL",
  "errors": [ { "code": "RI-001", "message": "..." } ]
}
```

## 3.2 Holder Wallet endpoints

**Request:**

Method: POST | Path: `/handle-request`

Request body:

```
{
  "sessionId": "<uuid>",
  "uri": "openid-credential-offer://... | openid-vc://..."
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

**Poll:** Same as 3.1 Poll.

## 3.3 Observability and logs (optional)

Method: GET | Path: `/logs/{sessionId}`

Response:

```
{
  "sessionId": "123456",
  "logs": [
    { "level": "info", "message": "message string", "timestamp": "2025-09-12T15:42:08.649Z" }
  ]
}
```

## 3.4 OpenAPI reference with example

Each system being integrated for testing MUST document its implementation using an OpenAPI 3.1 description.

The following reference definitions are provided:

* **Issuer and Verifier OpenAPI**: Defines /request, /poll, and /logs/{sessionId} endpoints.
* **Holder Wallet OpenAPI:** Defines /handle-request and /poll endpoints.

These templates specify payload schemas (sessionId, credentialType, status, errors[]) and expected HTTP codes (200, 400, 401, 500). Implementers adapt base URLs per tenant but MUST preserve the endpoint contract and status model.

**Example A: Issuer and Verifier OpenAPI**

```
openapi: 3.1.0
info:
  title: ITB Reference Implementation (Issuer/Verifier)
  version: "1.0.0"
  description: >
    Minimal Issuer/Verifier RI for the EUDI Wallet Interoperability Test Bed (ITB).
    Exposes endpoints to create a request (VCI/VP), poll session status,
    and optionally fetch logs for reporting.
servers:
  - url: https://ri.example.com
    description: RI base URL (per-tenant base paths are allowed)
tags:
  - name: request
    description: Create credential issuance (VCI) or presentation (VP) requests
  - name: session
    description: Polling and logs for a testing session
paths:
  /request:
    post:
      tags: [request]
      summary: Create a VCI or VP request bound to an ITB session
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateRequest'
            examples:
              vpExample:
                summary: Verifier VP request
                value: { sessionId: "c2fe20a4-1a11-4c8e-9a07-1f0b9f2ad7a2", mode: "vp", credentialType: "org.example.EmployeeCard" }
              vciExample:
                summary: Issuer VCI request
                value: { sessionId: "9c8a1f2b-53e7-45e5-9d8f-1c7e8a4a2b11", mode: "vci", credentialType: "org.example.EmployeeCard" }
      responses:
        '200':
          description: Request created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CreateRequestResponse'
              examples:
                ok:
                  value:
                    uri: "openid-credential-offer://?issuer=https%3A%2F%2Fri.example.com%2Fissuer..."
                    qr: "data:image/svg+xml;base64,PHN2ZyB4bWxucz0i..."
        '400': { $ref: '#/components/responses/BadRequest' }
        '401': { $ref: '#/components/responses/Unauthorized' }
        '500': { $ref: '#/components/responses/ServerError' }

  /poll:
    get:
      tags: [session]
      summary: Poll session status
      parameters:
        - name: sessionId
          in: query
          required: true
          schema: { $ref: '#/components/schemas/SessionId' }
      responses:
        '200':
          description: Session status
          content:
            application/json:
              schema: { $ref: '#/components/schemas/PollResponse' }
              examples:
                pending: { value: { sessionId: "c2fe20a4-1a11-4c8e-9a07-1f0b9f2ad7a2", status: "PENDING", errors: [] } }
                success: { value: { sessionId: "c2fe20a4-1a11-4c8e-9a07-1f0b9f2ad7a2", status: "SUCCESS", errors: [] } }
                fail:
                  value:
                    sessionId: "c2fe20a4-1a11-4c8e-9a07-1f0b9f2ad7a2"
                    status: "FAIL"
                    errors: [ { code: "RI-001", message: "Invalid presentation" } ]
        '400': { $ref: '#/components/responses/BadRequest' }
        '500': { $ref: '#/components/responses/ServerError' }

  /logs/{sessionId}:
    get:
      tags: [session]
      summary: Fetch RI logs for a session (optional)
      parameters:
        - name: sessionId
          in: path
          required: true
          schema: { $ref: '#/components/schemas/SessionId' }
      responses:
        '200':
          description: Logs for the session
          content:
            application/json:
              schema: { $ref: '#/components/schemas/LogsResponse' }
              examples:
                sample:
                  value:
                    sessionId: "123456"
                    logs:
                      - level: "info"
                        message: "Verifier request created"
                        timestamp: "2025-09-12T15:42:08.649Z"
        '404': { $ref: '#/components/responses/NotFound' }
        '500': { $ref: '#/components/responses/ServerError' }

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
  schemas:
    SessionId:
      type: string
      format: uuid
      description: ITB session identifier
    Mode:
      type: string
      enum: [vci, vp]
      description: "'vci' for issuance (Issuer role) or 'vp' for presentation (Verifier role)"
    CreateRequest:
      type: object
      required: [sessionId, credentialType]
      properties:
        sessionId: { $ref: '#/components/schemas/SessionId' }
        credentialType:
          type: string
          description: Profile-specific credential/presentation type identifier
        mode:
          $ref: '#/components/schemas/Mode'
    CreateRequestResponse:
      type: object
      required: [uri]
      properties:
        uri:
          type: string
          description: Deep-link (e.g., openid-credential-offer:// or openid-vc://)
        qr:
          type: string
          description: Base64 data URL for QR image (PNG or SVG)
    PollStatus:
      type: string
      enum: [PENDING, SUCCESS, FAIL]
    ErrorItem:
      type: object
      required: [code, message]
      properties:
        code: { type: string, description: "Implementation-defined error code (e.g., RI-001)" }
        message: { type: string }
    PollResponse:
      type: object
      required: [sessionId, status]
      properties:
        sessionId: { $ref: '#/components/schemas/SessionId' }
        status: { $ref: '#/components/schemas/PollStatus' }
        errors:
          type: array
          items: { $ref: '#/components/schemas/ErrorItem' }
          default: []
    LogEntry:
      type: object
      required: [level, message, timestamp]
      properties:
        level: { type: string, enum: [debug, info, warn, error] }
        message: { type: string }
        timestamp: { type: string, format: date-time }
    LogsResponse:
      type: object
      required: [sessionId, logs]
      properties:
        sessionId: { type: string }
        logs:
          type: array
          items: { $ref: '#/components/schemas/LogEntry' }
  responses:
    BadRequest: { description: Invalid input }
    Unauthorized: { description: Missing or invalid bearer token }
    NotFound: { description: Session not found }
    ServerError: { description: RI internal error }
```

**Example B: Holder Wallet OpenAPI**

```
openapi: 3.1.0
info:
  title: ITB Reference Implementation (Holder Wallet)
  version: "1.0.0"
  description: >
    Minimal Holder Wallet RI for the EUDI Wallet Interoperability Test Bed (ITB).
    Accepts VCI/VP request URIs, binds them to sessions, exposes polling and optional logs.
servers:
  - url: https://holder.example.com
tags:
  - name: holder
    description: Handle external request URIs and return results
  - name: session
    description: Polling and logs for a testing session
paths:
  /handle-request:
    post:
      tags: [holder]
      summary: Accept a VCI or VP request URI and bind to session
      security: [ { bearerAuth: [] } ]
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/HandleRequest' }
      responses:
        '200':
          description: Request handled
          content:
            application/json:
              schema: { $ref: '#/components/schemas/HandleResponse' }
        '400': { $ref: '#/components/responses/BadRequest' }
        '401': { $ref: '#/components/responses/Unauthorized' }
        '500': { $ref: '#/components/responses/ServerError' }

  /poll:
    get:
      tags: [session]
      summary: Poll session status
      parameters:
        - name: sessionId
          in: query
          required: true
          schema: { $ref: '#/components/schemas/SessionId' }
      responses:
        '200':
          description: Session status
          content:
            application/json:
              schema: { $ref: '#/components/schemas/PollResponse' }
        '400': { $ref: '#/components/responses/BadRequest' }
        '500': { $ref: '#/components/responses/ServerError' }

  /logs/{sessionId}:
    get:
      tags: [session]
      summary: Fetch Holder RI logs for a session (optional)
      parameters:
        - name: sessionId
          in: path
          required: true
          schema: { $ref: '#/components/schemas/SessionId' }
      responses:
        '200':
          description: Logs for the session
          content:
            application/json:
              schema: { $ref: '#/components/schemas/LogsResponse' }
        '404': { $ref: '#/components/responses/NotFound' }
        '500': { $ref: '#/components/responses/ServerError' }

components:
  securitySchemes:
    bearerAuth: { type: http, scheme: bearer, bearerFormat: JWT }
  schemas:
    SessionId: { type: string, format: uuid, description: ITB session identifier }
    HandleRequest:
      type: object
      required: [sessionId, uri]
      properties:
        sessionId: { $ref: '#/components/schemas/SessionId' }
        uri: { type: string, description: Deep-link request URI }
    HandleResponse:
      type: object
      required: [sessionId, status]
      properties:
        sessionId: { $ref: '#/components/schemas/SessionId' }
        status: { type: string }
        result:
          type: string
          nullable: true
          description: Opaque result payload (JSON/JWT/SD-JWT/base64)
        updatedAt: { type: string, format: date-time }
    PollStatus: { type: string, enum: [PENDING, SUCCESS, FAIL] }
    ErrorItem:
      type: object
      properties:
        code: { type: string }
        message: { type: string }
    PollResponse:
      type: object
      properties:
        sessionId: { $ref: '#/components/schemas/SessionId' }
        status: { $ref: '#/components/schemas/PollStatus' }
        errors:
          type: array
          items: { $ref: '#/components/schemas/ErrorItem' }
          default: []
    LogEntry:
      type: object
      properties:
        level: { type: string, enum: [debug, info, warn, error] }
        message: { type: string }
        timestamp: { type: string, format: date-time }
    LogsResponse:
      type: object
      properties:
        sessionId: { type: string }
        logs:
          type: array
          items: { $ref: '#/components/schemas/LogEntry' }
  responses:
    BadRequest: { description: Invalid input }
    Unauthorized: { description: Missing or invalid bearer token }
    NotFound: { description: Session not found }
    ServerError: { description: Internal error }
```

# Appendix: User workflow with ITB

For those who wish to learn more and get familiarised with ITB UI, refer [to ITB User Guide here](reference-implementation-interoperability-test-bed.md).
