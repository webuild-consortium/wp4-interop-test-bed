# WP4 Interoperability Test Bed

This repository hosts the interoperability test bed for WP4. It provides test frameworks, conformance checks, and use case scenarios to validate interoperability across wallets, protocols, and domain-specific functions.

---

## 📂 Repository Structure

```text
wp4-interop-test-bed/
│
├── docs/                     # Documentation and guides
│   └── overview.md
│
├── tests/                    # Test cases
│   ├── base-protocols/            # Core protocol tests (e.g. OIDC, OpenID4VCI, SIOP)
│   ├── domain-specific-functions/ # Domain-specific tests (e.g. health, payments, education)
│   └── usecases/                 # End-to-end flows and pilot scenarios
│
├── wallet-conformance-src/   # Source code for wallet conformance checks
│
├── README.md                 # This file
└── LICENSE
```

# ✅ Conformance Overview
Here is [an overview of all implementers in WE BUILD that provided a conformance report](https://github.com/webuild-consortium/wp4-interop-test-bed/blob/main/docs/Conformance%20Overview.md) for 1 or more of our test cases showing they passed them.

# 🚀 Getting Started

Follow these steps to set up and run the WP4 Interoperability Test Bed. See [the documentation](docs/overview.md) for more detailed information.

We did a presentation and demo on the ITB in January 2026. [Slides and video](https://portal.webuildconsortium.eu/topic/interoperability-testbed-itb-open-business) are available on OpenSocial.

## Start Testing - Onboarding
**Step 1** Contact [#itb-support](https://we-build-consortium.slack.com/archives/C09K65GLKT2) Slack channel with: 
* Organisation name and contacts (technical + governance)  
* Intended roles: Issuer / Verifier / Holder (one or more)  
* Targeted profiles & protocol versions (e.g. OID4VCI 1.0, OID4VP 1.0, SD-JWT, mdoc)
We will contact you via e-mail to get the information listed in step 2.

**Step 2** Provide base URLs (dev/acc/prod) and security mode (bearer, mTLS, x-apikey). You will now receive the following:
* An ITB tenant with namespaced configuration
* Credentials (per tenant/profile)
* Access to reference test suites and the reporting portal

Before you can test, you need to implement and deploy an operational service (wallet/holder, issuer, verifier) based on the WE BUILD [Conformance Specifications](https://github.com/webuild-consortium/wp4-architecture/tree/main/conformance-specs)

## Start Testing - Integrated Wallets
### 1. Clone the repository
```bash
git clone git@github.com:webuild-consortium/wp4-interop-test-bed.git
cd wp4-interop-test-bed
```

### 2. Install dependencies
If a given test suite requires dependencies, see its README inside:
- `tests/...`
- `wallet-conformance-src/`

### 3. Run tests
```bash
# Example: run base protocol tests
cd tests/base-protocols
# insert your command here (e.g., pytest, npm test, etc.)
```


# 🤝 Contributing

We welcome contributions from all collaborators.  

- Open issues for bugs, improvements, or questions  
- Submit pull requests following the repository structure  
- Use discussions (if enabled) for ideas and proposals  

By contributing, you agree to follow the project’s coding and documentation guidelines.

# 📜 Licence

Licensed under the Apache 2.0 License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. The IP is handled as part of the WE BUILD Consortium IP agreement.

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the LICENSE for the specific language governing permissions and limitations under the License.**

Please review the licence before using or distributing the code.
