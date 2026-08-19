# FDE Onsite: Appeal Letter Assistant for Claim Denials

## Overview

Today, you'll build a small AI-powered application using the **Vercel AI SDK**.

Your goal is to create an **Appeal Letter Assistant** that helps a user review a denied claim,
the test requisition, the clinical documentation, and the payer's medical policy. It should
help the user determine whether the service met the policy's medical-necessity criteria and
prepare a payer-ready **claim appeal letter**.

The project is intentionally scoped. We are not looking for a production-grade clinical or
coverage system. We care about seeing how you build a useful, working product, how you
structure your code, and how you explain your decisions.

We often work on problems like this: a case spans several documents, the rules are not always
easy to apply, and someone needs to review the result before it moves forward. The case here
is synthetic and intentionally narrow, but the product and engineering tradeoffs are similar
to the ones we encounter in our work.

Denial packets are dense and repetitive, but an appeal usually depends on a few specific
questions: which policy criteria apply, which are met, what evidence supports them, and how
the denial should be addressed. A good appeal answers those questions and cites its
sources. It should also be clear when the source documents support a statement and when the
assistant is making an inference.

The Vercel AI SDK is a TypeScript toolkit for building AI applications and agents. It supports
text generation, streaming responses, structured outputs, and tool calling. For this exercise,
we will provide an OpenAI API key to use with the SDK. ([AI SDK][1])

## Schedule

We'll discuss the project around **10:00am**.

You'll have most of the day to build.

At **4:00pm**, we'll regroup for a demo and debrief. Other members of the team will join, so
you'll have a chance to meet the rest of the team, show what you built, and discuss your
decisions and tradeoffs.

## Project

Build a local web app that takes the provided claim-denial packet and helps a user prepare a
defensible, cited **claim appeal letter**.

The user should be able to tell what the app has done, what still needs attention, and where
their input is needed. You may include a conversational interface, but the user should not
have to know the right prompt to move the work forward.

How you divide the work between automation and user review is up to you. The same goes for how
you show progress, handle missing or uncertain evidence, and let the user correct or approve
the result.

We provide an example **case packet** (see [`data/`](data)). It includes a denied healthcare
claim, the payer's denial and appeal instructions, a hereditary cancer test requisition, a
cancer genetics consultation note, a breast pathology report, and the governing medical
policy. The requisition, consultation note, and pathology report support the claim; none of
the clinical documents contain claim or reimbursement information.

The result should be more than generated prose. A user should be able to see how the policy
criteria, clinical evidence, and claim-adjustment code connect. How you design that experience
is up to you.

## Requirements

By the 4:00pm debrief, your app should:

1. Run locally with clear setup instructions.
2. Use the **Vercel AI SDK** for model interaction.
3. Use the provided OpenAI API key for model calls.
4. Make it clear what the app has done, what comes next, and where review is needed. The
   interface and flow are up to you.
5. Operate on the provided local case packet in [`data/`](data).
6. Implement at least **two AI SDK tools** that do useful work.

Suggested tools:

```txt
listDocuments
readDocument
readPolicy
extractPolicyCriteria
readClaim
readDenial
readRequisition
readClinicalDocument
readPathologyReport
mapEvidenceToCriteria
identifyDeniedClaimLines
interpretClaimAdjustment
findEvidence
draftAppealLetter
saveAppealLetter
```

The AI SDK supports tool calling through functions such as `generateText` and `streamText`;
tools can include descriptions, input schemas, and async execute functions. ([AI SDK][2])

7. The application should use context from the claim, denial, requisition, clinical note,
   pathology report, and policy in its analysis and outputs.
8. The application should be able to generate an appeal-letter draft or help improve one.
9. The appeal letter should capture case-specific reasoning, including the claim and denied
   service lines, the governing policy, a criterion-by-criterion analysis, citations to the
   supporting documentation, and an explanation of why the denial should be overturned.
10. Show enough detail for a user to follow how the app reached its result. For example:

    * which documents were read
    * which tools were called
    * which policy criteria were evaluated, and the met / not-met / unclear status of each
    * what evidence was cited, and from which document
    * what was directly supported by the source documents versus inferred
    * what open questions or missing documentation were recorded

11. Let the user review, correct, and approve important model conclusions.
12. Show the case's status, what has been completed, and any issues that remain unresolved.

## Appeal Letter

The exact format is up to you, but the generated appeal letter should capture more than a
restatement of the denial.

A useful appeal letter might include:

```txt
date and payer claim-appeals address
patient / member identifiers (name, member ID, DOB)
claim number, payer claim-control number, and denial reference number
date of service, denied claim lines, CPT codes, and billed amounts
associated requisition / order information
governing policy (number, title, effective date)
claim-adjustment code
statement of medical necessity
criterion-by-criterion analysis mapped to specific evidence
citations to the clinical documentation (which document, which finding)
explanation of why the service meets the policy
supporting clinical evidence summary
requested action (overturn the denial and reprocess the claim for payment)
enclosures list
ordering provider signature block
```

You should make it clear which parts of the letter are directly supported by the source
documents and which parts are inferred by the assistant.

## The Case Packet

The provided files live in [`data/`](data):

* [`policy.md`](data/policy.md) — a condensed excerpt of the payer's hereditary-cancer genetic
  testing policy. It contains the relevant coverage pathways, definitions, applicable CPT
  codes, and documentation guidance.
* [`claim.json`](data/claim.json) — the submitted healthcare claim: patient and insurance
  identifiers, billing and ordering providers, date and place of service, diagnoses, billed
  amounts, and line-level CPT codes.
* [`denial.json`](data/denial.json) — the payer's claim adjudication: denied service lines,
  claim-adjustment code, appeal deadline, and submission instructions.
* [`requisition.json`](data/requisition.json) — the hereditary breast cancer panel
  requisition: patient, insurance, ordering provider, panel genes and methodology, clinical
  indication, records supplied with the order, and genetic-counseling information.
* [`clinical-document.json`](data/clinical-document.json) — the patient-focused cancer
  genetics consultation note created before the test was ordered. It documents the personal
  and family cancer history, the reason for testing, and how a result could affect care.
* [`pathology-report.json`](data/pathology-report.json) — the breast core-biopsy report. It
  documents the cancer diagnosis and the ER, PR, and HER2 results.

## What You Do Not Need to Build

You do **not** need to build:

* authentication
* deployment
* multi-user support
* EHR, claims clearinghouse, or payer-portal integration
* actual electronic submission or faxing of the appeal
* HIPAA/PHI-grade compliance controls
* clinical decision-making beyond applying the provided policy
* support for every possible payer policy or denial type

Focus on a small, useful, working product.

## Product Design Expectations

Design the path from opening the supplied case packet to producing a reviewable appeal. There
is no required sequence, number of screens, or interaction model.

You will need to decide:

* which tasks happen automatically and which need user review
* how the user knows what has happened and what needs attention
* how uncertain, conflicting, or missing evidence is handled
* what happens to later results when the user makes a correction
* what needs to be saved so the work does not live only in generated text or chat history

## Stretch Goals

Once the base app works, add one or more features that make the product more useful.

Possible stretch features:

* Criteria coverage table (met / not met / unclear, each with its supporting evidence)
* Citations that link each argument back to the source document and field
* A "missing evidence" checklist: what documentation would strengthen the appeal
* "Regenerate this section" button
* Letter preview (Markdown) and export to PDF or DOCX
* Save the generated letter to disk
* Diff view before saving changes
* Policy effective-date / version checks against the service date
* Packet consistency checks across patient, member, claim, and requisition identifiers
* An appeal-readiness check for missing required information or unsupported arguments
* Persistent case history or resumable work
* Display tool calls in a timeline
* Recovery from a failed or interrupted operation
* Basic tests for your document-reading and persistence tools

We prefer a small number of polished, working features over many incomplete ones.

## Technical Notes

Use the OpenAI API key we provide for model calls.

You may use any app framework, but we recommend keeping it simple. A Next.js app is a
reasonable choice, but not required.

You may use AI coding assistants or code generation tools. That is allowed. You are
responsible for understanding, explaining, and debugging the code you submit.

Please avoid destructive file operations unless you implement a clear preview or confirmation
step.

The application does not need to submit anything to a payer or reprocess the claim. Reading
the packet and generating the claim appeal letter is enough for this exercise.

## Deliverables

By 4:00pm, please have:

1. A running local app.
2. A README with setup instructions.
3. A generated appeal letter for the provided case.
4. A demo flow you can walk through.
5. Code you are prepared to explain.

## Demo Expectations

In the demo, we'll ask you to:

1. Start the app locally.
2. Show the main flow you designed for the supplied case.
3. Show how the product uses the case packet and how a user can follow its reasoning.
4. Show how a user can inspect or correct an important conclusion.
5. Generate or improve an appeal letter.
6. Explain what you chose to automate and what you left for the user to review.
7. Explain how your tools work.
8. Walk through the code structure.
9. Discuss how you separated source-backed facts from assumptions.
10. Discuss the current limitations and what you would improve with more time.

## What We're Looking For

We'll evaluate the project across several dimensions:

### Product thinking

Did you build something useful and understandable? Is the flow clear? Were your choices about
automation and user review sensible?

### Payer-policy and medical-necessity reasoning

Did you correctly interpret the denied claim lines, extract the policy criteria, map them to
specific evidence in the supporting documents, interpret the claim-adjustment code, and
handle unmet or uncertain criteria honestly rather than overstating the case?

### Execution

Does the app run? Does the main flow work?

### AI SDK usage

Did you use the SDK thoughtfully, especially around prompts, tools, structured outputs, and
model interaction?

### Appeal letter usefulness

Does the generated letter make a defensible, well-cited medical-necessity argument that a
reviewer could act on?

### Code quality

Is the code organized, readable, and reasonably maintainable?

### User experience

Can a user understand what the application is doing, where the case stands, and what needs
attention?

### Communication

Can you explain your implementation, tradeoffs, and next steps?

## Final Note

Keep the scope focused. The goal is a working Appeal Letter Assistant that uses AI, a payer
policy, and clinical context to help a user produce a defensible, well-cited appeal. It should
feel like a tool for working through a case, not just a chat window that generates a letter.

[1]: https://ai-sdk.dev/docs/introduction "AI SDK by Vercel"
[2]: https://ai-sdk.dev/docs/foundations/tools "Foundations: Tools"
