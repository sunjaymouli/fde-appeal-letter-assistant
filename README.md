# FDE Onsite: Appeal Letter Assistant for Prior-Authorization Denials

## Overview

Today, you'll build a small AI-powered application using the **Vercel AI SDK**.

Your goal is to create an **Appeal Letter Assistant**: an assistant that helps a user read a
payer's medical policy and a test requisition (with its attached clinical document), reason
about whether the ordered test meets the policy's medical-necessity criteria, and generate a
payer-ready **appeal letter** that responds to a denial.

The project is intentionally scoped. We are not looking for a production-grade clinical or
coverage system. We care about seeing how you build a useful, working product, how you
structure your code, and how you explain your decisions.

Denials are dense and repetitive, but they turn on a small amount of reasoning that is easy
to lose: which policy criteria apply, which are actually met, what specific evidence in the
chart supports each one, and how to rebut the payer's stated reason. A good appeal makes that
reasoning explicit and cites its sources. Your assistant should help a user produce exactly
that, and should make it clear which claims are backed by the source documents and which are
inferred.

The Vercel AI SDK is a TypeScript toolkit for building AI applications and agents. It supports
text generation, streaming responses, structured outputs, and tool calling. For this exercise,
we will provide an OpenAI API key to use with the SDK. ([AI SDK][1])

## Schedule

We'll discuss the project around **10:00am**.

You'll have the day to build.

By **5:00pm**, you should have a local application running and be ready to demo it.

## Project

Build a local web app that helps a user generate or improve an **appeal letter** for a denied
test requisition.

The app should let a user chat with an AI assistant. The assistant should be able to inspect
the payer policy, the requisition, and the attached clinical document, then use that context
to answer questions or produce the appeal letter.

We provide an example **case packet** (see [`data/`](data)). It includes a payer medical
policy, a lab test requisition, and an attached clinical genetics consultation note. The
requisition also contains the payer's prior-authorization determination — in this case, a
**denial** — with the stated reason and the appeal instructions.

The real problem is that specialists need to map a payer's criteria onto specific clinical
evidence and produce a defensible, cited appeal letter — not just free text. The assistant
should do that mapping and show its work.

Example user prompts:

```txt
Does this test meet the policy's medical necessity criteria?
```

```txt
Generate an appeal letter for this denial.
```

```txt
Which criteria are met, which are not, and which are unclear?
```

```txt
What clinical evidence supports medical necessity, and where is it documented?
```

```txt
What is the payer's stated reason for denial, and how should we rebut it?
```

```txt
Improve this appeal letter and explain what changed.
```

## Requirements

By the end of the day, your app should:

1. Run locally with clear setup instructions.
2. Use the **Vercel AI SDK** for model interaction.
3. Use the provided OpenAI API key for model calls.
4. Provide a simple chat-style interface.
5. Operate on the provided local case packet in [`data/`](data).
6. Implement at least **two tools** that the assistant can use.

Suggested tools:

```txt
listDocuments
readDocument
readPolicy
extractPolicyCriteria
readRequisition
readClinicalDocument
mapEvidenceToCriteria
identifyDenialReason
findEvidence
draftAppealLetter
saveAppealLetter
```

The AI SDK supports tool calling through functions such as `generateText` and `streamText`;
tools can include descriptions, input schemas, and async execute functions. ([AI SDK][2])

7. The assistant should use context from the case packet when answering.
8. The assistant should be able to generate an appeal-letter draft or suggest improvements.
9. The appeal letter should capture case-specific reasoning, including the governing policy,
   a criterion-by-criterion analysis, citations to the clinical documentation, and a rebuttal
   of the payer's stated denial reason.
10. Show enough information in the UI that a user can understand what happened. For example:

    * which documents were read
    * which tools were called
    * which policy criteria were evaluated, and the met / not-met / unclear status of each
    * what evidence was cited, and from which document
    * what was directly supported by the source documents versus inferred
    * what open questions or missing documentation were recorded

## Appeal Letter

The exact format is up to you, but the generated appeal letter should capture more than a
restatement of the denial.

A useful appeal letter might include:

```txt
date and payer appeals address
patient / member identifiers (name, member ID, DOB)
claim / prior-authorization / denial reference number
test requested and CPT codes
governing policy (number, title, effective date)
summary of the payer's stated denial reason
statement of medical necessity
criterion-by-criterion analysis mapped to specific evidence
citations to the clinical documentation (which note, which finding)
rebuttal of the denial rationale
supporting clinical evidence summary
requested action (overturn the denial / authorize the test)
enclosures list
ordering provider signature block
```

You should make it clear which parts of the letter are directly supported by the source
documents and which parts are inferred by the assistant.

## The Case Packet

The provided files live in [`data/`](data):

* [`policy.md`](data/policy.md) — the payer medical policy. Coverage rationale (the
  medical-necessity criteria), definitions, applicable CPT codes, and documentation
  requirements. This is the standard the appeal must satisfy.
* [`requisition.json`](data/requisition.json) — the lab test requisition: patient, insurance,
  ordering provider, the ordered test and CPT codes, the diagnosis/indication, the attached
  documents, and the `priorDetermination` block containing the **denial** (reason, reference
  number, and appeal instructions).
* [`clinical-document.json`](data/clinical-document.json) — the clinical genetics
  consultation note attached to the requisition. This is where the supporting evidence lives:
  history, prior workup, exam findings, developmental assessment, and the plan.

> **Synthetic data.** The patient, provider, lab, and denial are fictional. The policy is a
> condensed excerpt of a public payer medical policy, included as reference material. Don't
> use any of this for real decisions.

## What You Do Not Need to Build

You do **not** need to build:

* authentication
* deployment
* multi-user support
* EHR, clearinghouse, or payer-portal integration
* actual electronic submission or faxing of the appeal
* HIPAA/PHI-grade compliance controls
* clinical decision-making beyond applying the provided policy
* support for every possible payer policy or denial type

Focus on a small, useful, working product.

## Suggested User Flow

One possible flow:

1. User opens the app.
2. User sees a chat box.
3. User asks: "Generate an appeal letter for this denial."
4. The assistant lists the documents in the case packet.
5. The assistant reads the policy, the requisition, and the attached clinical document.
6. The assistant extracts the policy's medical-necessity criteria.
7. The assistant maps each criterion to specific evidence in the clinical document and marks
   it met, not met, or unclear.
8. The assistant identifies the payer's stated denial reason.
9. The assistant produces an appeal-letter draft that cites the policy and the evidence and
   rebuts the denial.
10. The UI shows which documents, criteria, and evidence were used.
11. User asks a follow-up or corrects an assumption.
12. The assistant updates the letter.

You are welcome to design a different flow if you think it is better.

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
* Persistent chat history
* Display tool calls in a timeline
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

The assistant does not need to submit anything to a payer. Reading the documents and
generating the letter is enough for this exercise.

## Deliverables

By 5:00pm, please have:

1. A running local app.
2. A README with setup instructions.
3. A short explanation of what you built.
4. A generated appeal letter for the provided case.
5. Notes on any known limitations.
6. A demo flow you can walk through.
7. Code you are prepared to explain.

## Demo Expectations

In the demo, we'll ask you to:

1. Start the app locally.
2. Show the main user flow.
3. Ask the assistant to inspect the case packet.
4. Generate or improve an appeal letter.
5. Explain how your tools work.
6. Walk through the code structure.
7. Discuss how you separated source-backed facts from assumptions.
8. Discuss what you would improve with more time.

## What We're Looking For

We'll evaluate the project across several dimensions:

### Product thinking

Did you build something useful and understandable?

### Payer-policy and medical-necessity reasoning

Did you correctly extract the policy criteria, map them to specific evidence in the clinical
document, identify the payer's stated denial reason, and handle unmet or uncertain criteria
honestly rather than overstating the case?

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

Can a user understand what the assistant is doing?

### Communication

Can you explain your implementation, tradeoffs, and next steps?

## Final Note

The goal is not to build the most complex agent possible.

The goal is to build a focused, working Appeal Letter Assistant that uses AI, a payer policy,
and clinical context to help a user produce a defensible, well-cited appeal.

[1]: https://ai-sdk.dev/docs/introduction "AI SDK by Vercel"
[2]: https://ai-sdk.dev/docs/foundations/tools "Foundations: Tools"
