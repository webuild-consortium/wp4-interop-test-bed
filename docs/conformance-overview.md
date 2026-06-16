# Conformance overview

This document provides an overview of all implementers in WE BUILD that provided a conformance report for 1 or more of our test cases showing they passed them.

For a full listing of all the wallets, including documentation, go to the [Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/) provided by the Wallet Group (also available as [list view](https://webuild-consortium.github.io/wp4-wallets-group/?view=table)). Contact them on their [GitHub Repository](https://github.com/webuild-consortium/wp4-wallets-group) for questions on their listing.

If you want to be included in this overview:

- Run the test cases in the ITB.
- Generate a conformance statement report (PDF) for the test cases / test suites you want to be listed for.
  - Upload this to the folder ["Conformance Statement Reports" in Testing/Files](https://portal.webuildconsortium.eu/group/testing/files)
    - Give the document at least your organisation's name (if you have multiple files, add the test case/suite number or a date)
  - Send a message to the [#itb-support](https://we-build-consortium.slack.com/archives/C09K65GLKT2) Slack channel asking us to update our listing here
    - We will check the report before doing so
    - You can also include your report in this message & we'll upload it for you

The test cases cover the WE BUILD [Conformance Specifications](https://github.com/webuild-consortium/wp4-architecture/tree/main/conformance-specs) managed by Architecture.
A test suite contains multiple test cases. Not all of them may be required to pass on full conformance. If there is a rationale for not passing the complete test suite, please provide this in writing to the Test Group (upload it with your conformance statement report).

## Base Protocols

Conformance against the base protocols that are part of the [WE BUILD Conformance Test Suite](https://github.com/webuild-consortium/wp4-interop-test-bed/tree/main/tests/base-protocols) covering [CS-01 Credential Issuance](https://github.com/webuild-consortium/wp4-architecture/blob/main/conformance-specs/cs-01-credential-issuance.md) and [CS-02 Credential Presentation](https://github.com/webuild-consortium/wp4-architecture/blob/main/conformance-specs/cs-02-credential-presentation.md).

| Organisation    | Service           | CTSv1.0 | Notes                                              | More Information                                                                                                                                           |
| --------------- | ----------------- | ------- | -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Aricoma         | Wallet            | ✅      | Full conformance                                   | <ul> <li>[Aricoma in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=4)</li> </ul>                                    |
| Cleverbase      | Issuer / Verifier | ✅      | Full conformance                                   | <ul> <li>[Cleverbase in QTSP Service Catalogue](https://webuild-consortium.github.io/wp4-qtsp-group/?id=cleverbase)</li> </ul>                             |
| Cleverbase      | Wallet            | ✅      | Full conformance                                   | <ul> <li>[Cleverbase in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=18)</li> </ul>                                |
| Credenco        | Wallet            | ✅      | Full conformance                                   | <ul> <li>Business wallet</li> <li>[Credenco in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=161)</li> </ul>        |
| Digitel TS      | Issuer / Verifier | ✅      | Full conformance                                   | <ul> <li>[Digitel TS in QTSP Service Catalogue](https://webuild-consortium.github.io/wp4-qtsp-group/?id=digitel-ts)</li> </ul>                             |
| GUnet           | Wallet            | ✅      | Full conformance, except for support for DID:web   | <ul> <li>wwWallet </li> <li>[GUnet in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=35)</li> </ul>                  |
| IDUnion         | Wallet            | ✅      | Full conformance                                   | <ul> <li>Business wallet</li> </ul>                                                                                                                        |
| iGrant.io       | Wallet            | ✅      | Full conformance                                   | <ul> <li>Organisation Wallet Suite</li> <li> [iGrant.io in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=52)</li> <li> [Organisation Wallet Suite by iGrant.io](https://docs.igrant.io/docs/organisation-wallet-overview/)</li> </ul> |
| Intesi Group    | Issuer            | ✅      | Full conformance                                   | <ul> <li>[Intesi Group TS in QTSP Service Catalogue](https://webuild-consortium.github.io/wp4-qtsp-group/?id=intesi-group)</li> </ul>                      |
| Izertis         | Wallet            | ✅      | Full conformance, except for support for mdoc      | <ul> <li>[Izertis in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=51)</li> </ul>                                   |
| KVK             | Wallet            | ✅      | Full conformance, except for support for DID:web   | <ul> <li>KVK NL Wallet </li> <li>[NL Wallet by KVK](https://wallet-connect.eu/?mode=personal&lang=en)</li> </ul>                                           |
| Procivis        | Wallet            | ✅      | Full conformance                                   | <ul> <li>Procivis One Wallet</li> <li> [Procivis in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=183)</li> </ul>   |
| Siros           | Wallet            | ✅      | Full conformance, except for support for DID:web   | <ul> <li>Siros Wallet</li> <li> [Siros in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=193)</li> </ul>             |                   
| Spherity        | Wallet            | ✅      | Full conformance, except for support for mdoc      | <ul> <li>[Spherity in Wallet Capability Viewer](https://webuild-consortium.github.io/wp4-wallets-group/?id=75)</li> </ul>                                  |
