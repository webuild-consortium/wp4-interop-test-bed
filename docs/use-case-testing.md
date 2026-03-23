# Instructions for WE BUILD Use Cases 

Instructions for WE BUILD Use Cases (WP2 and WP3) and groups (WP4) on how to get test cases supporting their functional flows and scenarios.  

Keep in mind when setting this up: A first draft is good enough - this doesn’t need to be final/complete yet - you can always update and create new versions.  

## Design full functional flows
These are usually part of a (human readable) descriptive document, which can be an attestation rulebook, a scenario specification, an implementation guide or whatever you want to name it.

Compare the flows to the existing WE BUILD [Conformance Specifications (CS)](https://github.com/webuild-consortium/wp4-architecture/tree/main/conformance-specs):
- Can you work with them? (If so, please reference them in your document, clarifying which parts of your functional flow are covered by which CSs.)
- If you have different requirements:
  - Are those specific to your use case? Then write a new draft CS (ask Architecture for help if needed).
  - If your requirements are relevant to all participants in WE BUILD, regardless of the use case, then raise an issue with the involved CS on GitHub. Or create a pull request with the required changes.
  - Make sure to discuss changes with the relevant groups (Architecture can help you find them). 

## Design your attestations
Describe the generic elements/attributes in your attestation rulebook, or as a separate document (e.g. "attestation description"). It is strongly recommended to have a human-readable document describing the attestation in context of the use case (which can be the same document used for the functional flow).
- Provide information on the label, description, optionality and encoding format. (Semantics can help you with this.)
- Create data schemas for each type of attestation you want to use (e.g. SD-JWT or mdoc). The issuers for this attestation will be able to help you with this (ask Semantics or the QEAA subgroup in the QTSP group).

## Consider other aspects
Consider other aspects, such as binding, revocation methods, status lists, policies, metadata
- Are they relevant to your use case?
- If so: describe them in the descriptive document or attestation rulebook (get help if needed).

## Keep in scope of the ADRs
Ensure that your generic requirements are covered by the [Architecture Decision Records (ADR)](https://github.com/webuild-consortium/wp4-architecture/tree/main/adr)
- This applies to all Conformance Specifications and data formats – they should be in scope of the ADRs
- Raise issues or create a pull request to suggest changes or new ones.
- Make sure to discuss these with the Architecture Group.

## Getting test cases
The Test Group will write test cases for all new (approved) CSs. We will write the test cases in collaboration with your use case or group.  
Reach out through the [#itb-support](https://we-build-consortium.slack.com/archives/C09K65GLKT2) Slack channel to contact us on this.

## Updating scenarios, flows, attestations
When you update specifications, keep in mind that this will affect existing implementations and definitions.  
- The Test Group will provide test cases in different versions, making the ITB backward compatible. The Test Group will also ensure that new versions of the Conformance Specifications are being covered by new test cases.
- You need to ensure that the new information is being covered by a rulebook (including the changes, so keep the “old” information in there) and that that rulebooks points to the new/correct CSs and data schemas.
- What you need to collaborate on, are the definitions that are shared by multiple use cases and groups:
  - You might need to create a new version or instance of an attestation or data schema
  - The same goes for rulebooks if they are used across use cases
If you need to deprecate an old version, plan for a transition period and communicate this to all parties who work with the current version.
