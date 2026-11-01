# From Narrative to Evidence: Designing Information Extraction People Can Review

An interview can contain a fact, a guess and a correction in the same paragraph. If an extraction system turns all three into equally confident database fields, its output may look tidy while the story becomes less accurate.

My work in document intelligence has made me interested in the engineering behind that moment: how do we carry information from a messy source into a structure that a person can inspect, correct and use? Below I connect lessons from that work with an illustrative design for long-form interviews. The interview example is fictional, and the proposed narrative pipeline is a design, not a claim that I have deployed that exact system.

## Begin with the source, not the answer

Imagine an interviewee says: “I think my aunt May moved to Chiang Mai around 2012.” Later, someone mentions “Aunt Mali” and says the move happened before a graduation.

There are several useful signals here, but they are not all facts of the same kind. “Around 2012” is an approximate date. The two names may refer to the same person, but the source has not yet established that. The relative phrase “before graduation” needs another event and its own evidence before it becomes a useful time constraint.

| Observation | A careful representation | What needs review |
| --- | --- | --- |
| “around 2012” | Original phrase plus an approximate year or interval | Whether the source supports a narrower range |
| “Aunt May” / “Aunt Mali” | Two mentions and a proposed identity link | Whether they refer to the same person |
| “before graduation” | A relation to a graduation event, retaining the original phrase | Which graduation and whether its date is known |

For me, the core record is a **claim with evidence**: what was asserted, who or what it concerns, where the supporting passage came from, what is uncertain, and whether a person has reviewed it. A schema is valuable because it makes these fields explicit. A valid schema alone cannot prove that the source supports the value.

## What my document work taught me

In document processing, I have worked with LiteParse and OCR to preserve reading order and page geometry, and with structured extraction that associates candidates with source anchors and processing context. A review interface can then put a proposed value next to the relevant page or passage. That matters when the string is plausible but belongs to a different row, heading or document version.

My work also includes retrieval over source-linked information and workflows for human confirmation and correction. The details in this article are generalized, and the proposed interview architecture should not be read as a description of a particular deployed product.

These experiences transfer naturally to narrative data, even though interview transcripts introduce distinct problems: speakers refer back to earlier people, dates are relative, and two accounts can conflict without either being obviously wrong. My [document intelligence article](https://www.linkedin.com/pulse/building-extraction-pipeline-you-can-inspect-teeraphat-raksawong-p7mnc/) discusses the document side in more detail.

## Chunking without losing the story

I would split a long transcript at meaningful boundaries while retaining speaker, timestamps or passage offsets and overlap where needed. The extraction step would receive enough context to interpret a statement, and every candidate would still point to the original passage. Cross-chunk references need a separate linking step: a person introduced as “my brother Arun” in one section may be “he” pages later.

For identity resolution, I would keep **mention**, **candidate entity** and **reviewed identity** distinct. Rules and similarity can propose matches; context and a reviewer can resolve ambiguous ones. A system that merges eagerly is easy to demo but expensive to correct once the wrong identity has spread through downstream chapters.

## Corrections need a life beyond the next model run

Reprocessing is useful: parsers improve, prompts change and new source material arrives. It is also dangerous if a fresh model response overwrites a correction someone already made.

I would store source versions, extraction runs, proposed claims and review decisions separately. Reprocessing produces new candidates, then compares them with the current reviewed state. Unchanged evidence can retain its decision; changed or conflicting evidence should be surfaced for re-review. The exact reconciliation rule is a product decision as well as a database decision: reviewers need to know why their previous answer is being asked again.

## Evaluate the errors that change the outcome

For a memoir, fluent text is not enough. I would build a reviewed set of passages with expected claims and evidence, including aliases, uncertain dates, conflicting accounts and references across chunks. Alongside extraction precision and recall, I would measure evidence-link accuracy, false merges, temporal errors, unsupported claims, and whether approved corrections survive reprocessing.

The cases matter more than a single score. A system that extracts more facts but attaches them to the wrong person is not necessarily improving. A useful regression suite should make that tradeoff visible and preserve the examples that exposed a failure.

## Related public work

Different projects show different pieces of this engineering approach:

- [Google Maps RPC Scraper](https://github.com/neckttiie090520/Google-map-RPC-scraper): Python data collection, pagination, parsing, filtering and structured export. Its source data and task are different from memoir interviews.
- [MCP Thai Thesis](https://github.com/neckttiie090520/mcp-thai-thesis): document checks, Thai text heuristics and citation tools exposed through MCP.
- [nzs-skills](https://github.com/neckttiie090520/nzs-skills): agent workflows that make verification and evidence explicit.
- [Perch Agent MCP](https://github.com/neckttiie090520/perch-agent-mcp): a tool interface for delegating agent work and following its lifecycle.

I am drawn to this work because the software decision has a human consequence. When the source is someone's story, a good system gives an editor the context to preserve what was actually said—and the means to correct the system when it gets that wrong.

— Teeraphat Raksawong · [Portfolio](https://teeraphat-raksawong.netlify.app/) · [GitHub](https://github.com/neckttiie090520)
