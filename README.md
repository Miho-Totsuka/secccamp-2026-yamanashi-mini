# Security Camp Mini 2026 - Yamanashi

**September 26–27, 2026 · University of Yamanashi, Japan**

I attended **Security Camp Mini 2026 in Yamanashi** to learn about web application vulnerabilities, security in AI-assisted development, and OAuth 2.0 / OpenID Connect (OIDC).

As a student interested in **bioinformatics and computational biology**, I wanted to broaden my understanding of how applications handle data and how developers can protect it.

This repository documents my learning and a guided development exercise. The applications, teaching materials, and development workflow used in the exercise were provided by the workshop.

## What I Learned

The web vulnerability session used **SecCampHub**, a deliberately vulnerable practice application. The examples connected unexpected behavior with its cause, its impact on users, and possible fixes.

| Topic | What I learned |
| --- | --- |
| Authentication and authorization | Identifying a user and checking whether that user may access a particular resource are separate tasks. |
| Insecure Direct Object Reference (IDOR) | A server must check permission for each requested resource. Hiding links or making identifiers harder to guess does not replace authorization checks. |
| Information exposure | Information can still be exposed in an API response even when the page does not display it. |
| Cross-site scripting (XSS) | User input must be handled appropriately for where it is displayed, so it is not interpreted as executable content. |
| Mass assignment | The server should explicitly limit which fields a user may update, rather than accepting every submitted field. |

A useful idea running through these examples was that **the interface does not define everything a user can send to the server**. Security checks need to account for requests that differ from the application's normal use.

This changed how I think about web applications: instead of looking only at what the interface allows a user to do, I also need to consider what the server actually accepts and returns.

## Guided Development Exercise

The AI-assisted development session used an instructor-provided travel-planning application called **「旅のしおり」**.

Its plans have different visibility settings: **public, shared with a group, or private**.

The exercise connected identifying a problem with documenting the expected behavior, making changes, and checking the results.

My exercise repository contains an issue documenting three input-handling findings and a pull request addressing them:

- [Exercise repository](https://github.com/Miho-Totsuka/secccamp-2026-yamanashi-mini)
- [Issue #2: Findings and conditions for the fixes](https://github.com/Miho-Totsuka/secccamp-2026-yamanashi-mini/issues/2)
- [Pull request #3: Fix input-handling security findings](https://github.com/Miho-Totsuka/secccamp-2026-yamanashi-mini/pull/3)

### Changes in the Pull Request

| Finding | Change |
| --- | --- |
| SQL injection in public-plan search | Pass the search pattern as a SQL parameter while preserving the public-only restriction and the intended handling of special characters. |
| Unsafe HTML construction in print headings | Pass the heading to the template as data and use Jinja autoescaping. |
| Path traversal in guide downloads | Allow only the intended guide filename and check that its resolved path stays within the guide directory. |

The pull request also adds regression tests for escaped headings and rejected requests for files outside the guide directory.

These tests help check that the intended protections remain in place after changes.

### Preserving Legitimate Behavior

One detail I found particularly useful was that a security fix should **preserve legitimate behavior**, rather than simply blocking suspicious-looking input.

For example, searching for a title containing an apostrophe should still work, while the input must not be able to change the structure of the SQL query.

This helped me understand that secure development involves balancing:

- preventing unintended behavior,
- preserving intended functionality,
- and providing evidence that the change works as expected.

## Verification and Limitations

The September 27 pull request records the following results:

- `pytest`: **75 tests passed**
- Semgrep: **0 findings** across 217 rules and 13 tracked files
- **14 files were skipped** because of ignore patterns
- Continuous integration (CI) was **not configured or run**
- Browser-based manual checks of the fixes were **not completed**
- Symlink-based path traversal testing was **not completed**

The issue records reproduction of the search problem.

The XSS and path traversal behavior was **not independently reproduced before the fixes**, although regression tests were added for those areas.

This distinction is important to me because **passing tests and receiving no scanner findings do not establish that the whole application is secure**.

The evidence has a defined scope, and some behavior remained unverified.

I therefore want to distinguish between:

> **what I observed, what I tested, and what I have not yet verified.**

That is an important habit I want to carry into future technical and research work.

## OAuth 2.0 and OpenID Connect

The login session introduced the mechanisms behind features such as **“Sign in with Google.”**

An important distinction is that **OAuth 2.0 supports delegated access to resources, while OpenID Connect (OIDC) adds an identity layer** that allows a client to verify information about a user's authentication.

Access tokens and ID tokens therefore have different purposes:

- An **access token** is used to access protected resources.
- An **ID token** carries information about the user's authentication and must be validated by the client.

The workshop tutorial progresses through four stages:

1. The authorization code flow
2. `state`, which connects the authorization response with the initiating request and helps prevent cross-site request forgery (CSRF)
3. Proof Key for Code Exchange (PKCE), which requires a matching verifier when redeeming an authorization code
4. OIDC, including issuing and validating ID tokens

These topics helped me think about the messages exchanged behind a login button:

- Which component sends each request?
- What does each token represent?
- Which component is expected to validate it?
- What must be checked before trusting a response?

Rather than thinking of authentication as a single action such as “logging in,” I began to see it as a sequence of messages and checks between different components.

## Reflection

One idea I want to carry into future projects is to examine **what the server accepts and returns**, including information that is not visible on the page.

The development exercise also showed why a useful security fix needs:

1. a clearly defined problem,
2. explicit conditions for the expected behavior,
3. an implementation that addresses the problem,
4. and evidence that the change works without unnecessarily breaking legitimate behavior.

For AI-assisted development, I learned that generating or modifying code is only one part of the process.

It is also important to:

- understand the security problem,
- examine the proposed changes,
- identify what the changes are intended to protect,
- test the result,
- and understand the limitations of those tests.

In other words, I want to be able to explain **why a change addresses a finding**, rather than simply relying on an AI-generated patch.

This builds on my earlier [CTF for GIRLS cloud security workshop](https://github.com/Miho-Totsuka/CTF-for-GIRLS-Cloud-Security-Workshop), where I learned about permissions and access to stored data.

As I continue studying **bioinformatics and computational biology**, I want to apply these habits when building tools and sharing datasets or analysis results.

For example, when working with biological data, I want to think not only about whether an analysis produces a result, but also about:

- what data a tool accepts,
- what information it exposes,
- who should be able to access it,
- how inputs are validated,
- and how the reliability and limitations of the analysis are documented.

## Next Steps

- Review each merged change and explain how it addresses the original finding.
- Understand what each regression test checks and what remains outside its scope.
- Complete the outstanding manual checks in the practice environment.
- Learn how to run the checks through CI.
- Draw the OAuth/OIDC message flow and document which implementation stages I have completed and tested.
- Continue learning how secure software development practices can be applied to tools that process scientific and biological data.

## Connection to Computational Biology

My main academic interest is **computational biology and bioinformatics**, particularly the use of computational methods to analyze biological data.

I therefore see security as a complementary skill rather than a separate field of study.

Scientific software and biological-data workflows may involve:

- sequencing data,
- analysis pipelines,
- databases,
- APIs,
- shared research results,
- and web-based tools.

Learning how applications validate input, enforce authorization, protect information, and communicate securely gives me another perspective on how computational research systems should be designed.

My goal is not to become a security specialist instead of a computational biologist, but to develop enough security understanding to build and use computational tools more responsibly.

## Acknowledgments and Learning Resources

Thank you to the organizers, instructors, and tutors for the lectures and guided exercises.

- [Security Camp Mini 2026 in Yamanashi — Official Program](https://www.security-camp.or.jp/minicamp/yamanashi2026.html)
- 保坂一希, **“Webサイトに潜む脆弱性を探そう!!”** and the accompanying hands-on materials, provided during the workshop.
- 飯沼翼, **“生成AI時代のWebアプリケーション開発とセキュリティ,”** and its exercise application and guides.
- 永見拓人, [OAuth / OIDC 自作入門](https://coding-oidc.logica0419.dev/)
- calloc134, [ここまでの詳細フロー解説（Confidential Client）](https://zenn.dev/calloc134/books/sikkari-oauth-oidc/viewer/09-detailed-flow-confidential)
- [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0.html)

---

## Related Work

- [CTF for GIRLS — Cloud Security Workshop](https://github.com/Miho-Totsuka/CTF-for-GIRLS-Cloud-Security-Workshop)
- [eDNA Low-Abundance Species Detection](https://github.com/Miho-Totsuka/eDNA-Low-Abundance-Detection)
- [Cancer Genomics Analysis with R](https://github.com/Miho-Totsuka/Cancer-Genomics-Analysis-with-R)
