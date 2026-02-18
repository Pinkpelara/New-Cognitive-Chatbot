UofT Cognitive Synthesis Engine
Full Developer Feedback — Neuroscience-Upgraded Architecture
Based on independent analysis of 2024–2025 cognitive neuroscience and AI research
What This App Is and Must Remain
This is a universal institutional reasoning engine. It is designed to replicate the cognitive process of a human expert who has read everything relevant, built a complete mental map of how the rules connect, and can reason from that map to a precise answer. Not a search tool. Not a chatbot. Not a policy database with a chat interface. A reasoning engine.
The output is always clean professional prose. One flowing answer. Every factual sentence followed immediately by an inline citation in the format [Source: filename, Page X]. Nothing else appears in what the user sees. All reasoning, planning, contradiction detection, and epistemic assessment happens internally and produces a better answer. None of it is visible.
That is the product. Every architectural decision must serve that goal or it does not belong in this app.

The Neuroscience Foundation — What the Research Actually Says
This section draws on six distinct bodies of current research to ground every architectural requirement below. The developer should read this section carefully because it is the reason the architecture is designed the way it is, not a decoration.
1. Global Workspace Theory — Why Full-Corpus Injection Is Non-Negotiable
The most important finding in cognitive neuroscience for this application is Global Workspace Theory, now supported by decades of experimental evidence and formalized computationally by multiple research groups in 2024 and 2025 (Chateau-Laurent et al., 2025; Ye et al., 2025; Kavi et al., 2024).
The theory describes how the human brain achieves integrated conscious reasoning. Specialized modules process information locally. When a representation reaches sufficient salience, it undergoes global ignition — it broadcasts simultaneously to all other processing modules. Every part of the brain that is relevant to the current problem has access to the same information at the same time. That global broadcast is what enables the kind of reasoning that connects a rule in one document to an exception in another to a system constraint in a third.
Global broadcast = full-corpus injection. Partial retrieval = local processing only. Local processing cannot produce the outputs that only emerge from global connectivity.
Experiments comparing GNWT-based architectures to standard transformer baselines in 2025 showed that competitive ignition and workspace broadcasting produced meaningfully better causal and cross-document reasoning, not just incrementally better but categorically different in capability (Chateau-Laurent et al., 2025). This is not a nice-to-have. It is the mechanism that makes this app different from every other document tool.
Implication for the app: When the entire corpus fits within the model context window, inject everything at once. No retrieval. No ranking. Global broadcast. When the corpus exceeds context limits, use the best available approximation — the hybrid retrieval with rule-boosting — but always treat full injection as the primary path.
2. Dual Process Theory — The System 1 / System 2 Architecture for Hallucination Prevention
Kahneman's dual process theory, and its recent computational formalization, is the most powerful framework for understanding and preventing hallucination. System 1 is fast, pattern-matching, intuitive — the basis of most LLM generation. System 2 is slow, deliberate, analytical, and capable of catching errors that System 1 produces.
The most significant 2025 breakthrough in LLM hallucination prevention (HaluSearch, ACL 2025) implemented exactly this dual-process structure: fast generation (System 1) followed by a systematic tree-search verification process (System 2) that tests each claim against the source material and triggers correction when evidence is insufficient. The paper demonstrated substantial reduction in hallucination rates compared to single-pass generation.
The brain's System 2 is located primarily in the prefrontal cortex. It operates via deliberate, sequential reasoning and is especially active when detecting conflicts between what is expected and what is actually observed — exactly what a verifier pass does when it checks whether a claim is actually supported by the cited source.
The internal reasoning pipeline is System 1. The verifier pass is System 2. Both are required. System 1 alone hallucinates. System 2 alone is too slow. The combination is how expert humans think under pressure.
Implication for the app: The existing verifier pass is correctly designed. Keep it. Make it stronger by having it check not just citation coverage but whether each cited source text actually supports the specific claim being made. That is the System 2 function.
3. Hippocampus-Neocortex Architecture — Gist Plus Surprise
A 2024 preprint from computational neuroscientists (now in v3 on bioRxiv, 2025) modeled how the hippocampus and neocortex work together for memory, using an LLM as the neocortex and a vector retrieval system as the hippocampus. The key finding is not that the brain stores everything — it stores gist plus surprising details. The gist captures the semantic structure of a document. The surprising details are the things that deviate from what the neocortex would have predicted.
In policy documents, the gist is the general rule. The surprising detail is the exception clause. This matters because the exception is precisely what gets missed when retrieval systems prioritize semantic similarity — similar text retrieves similar text, but exceptions are semantically similar to the rule while being operationally opposite to it.
The hippocampal model explains why standard similarity-based retrieval fails for policy documents: it finds text that sounds like the query, but it misses the exception clauses that contradict the text that sounds like the query.
Implication for the app: The document memory layer should encode gist (outline, key concepts, synopsis — which it already does) plus explicit exception detection. When processing a document, identify clauses that contain unless, except, notwithstanding, provided that, subject to, and does not apply when. Store these as a high-priority exception index. In the hybrid retrieval path, retrieve this exception index first, regardless of semantic similarity score, because exceptions are always relevant to policy questions even when they do not match the query vocabulary.
4. Schema Formation — The Institutional Knowledge Map
Research published in Nature Reviews Neuroscience in 2025 (Bein and Niv) established that the medial prefrontal cortex builds schemas — reusable knowledge structures that represent not just individual facts but the relationships between facts. Schemas are how experts acquire intuition. An experienced registrar does not remember each policy as an isolated fact. They remember how the policies relate to each other, which ones override which, and under what conditions each applies. That relational map is a schema.
Complementary research from Brown University (February 2025) showed that working memory capacity is not a fixed limit but a learning outcome: the brain learns to compress related pieces of information into chunks that can be held in working memory together. The chunk is not a random selection of nearby text — it is a semantically coherent unit that the brain has learned to treat as a single entity.
Schemas are not summaries. They are relational maps. A summary describes what a document says. A schema describes how what a document says connects to what other documents say, which rules override which, and what the conditions of application are.
Implication for the app: The document memory blocks currently store outlines, key concepts, and synopses. This is good but insufficient. Add a schema extraction step during document ingestion: for each document, identify the key rules it establishes, which other documents those rules reference or contradict, and what the conditions for application are. Store this as a cross-document schema that the synthesis call can use as a pre-built relational map rather than having to reconstruct it from scratch on every query.
5. Spreading Activation and Concept Bridges
When an expert reads a question about course repetition limits, activation does not stay on the phrase 'course repetition.' It spreads outward through their entire knowledge network: to academic standing rules, to program requirements that might mandate retaking a failed course, to GPA calculation methods, to system constraints on what ROSI will actually permit regardless of policy. That spreading activation is automatic and involuntary in an expert. It is what produces the answer 'this rule applies but there is an exception in program X and the system handles it differently from what the policy says.'
The cross-document concept bridges already implemented in Test 6 (tracking which concepts appear in multiple documents) are a direct computational implementation of spreading activation. When 'academic standing' appears in seven documents, the app knows that any query touching that concept should activate all seven documents, not just the one with the highest similarity score.
Spreading activation cannot be simulated by similarity search alone. Similarity finds text that looks like the query. Spreading activation finds text that is connected to the query through a relational map, even if the surface vocabulary is different.
Implication for the app: Strengthen the concept bridge layer. When the synthesis call is built, include not just the retrieved chunks but the complete list of concept bridges relevant to the query. Tell the model explicitly: 'The following concepts in your query appear across these documents. Read all of them carefully before answering.' This primes the model to activate the same cross-document connections that an expert would.
6. Predictive Processing — Anticipate Before Retrieving
The most recent formalization of predictive processing in the context of LLMs (Frontiers in Robotics and AI, October 2025) established that the human brain does not passively receive and process information — it generates predictions about what it expects to find and then measures prediction error, the gap between prediction and reality. Prediction error is not a failure. It is the signal. It is how the brain knows where to focus attention and what is genuinely new or important.
Applied to document reasoning, this means an expert does not simply read a document and extract facts. Before reading, they have already formed expectations based on what they know about institutional frameworks, policy structures, and common rule patterns. When they encounter something that violates their expectation, it registers with high salience — it is the exception, the edge case, the contradiction. That is exactly what needs to surface in a policy reasoning tool.
Build a prediction layer: before retrieving evidence, have the model generate what it expects the answer to be based on general institutional knowledge. Then compare the actual corpus content against that prediction. Deviations are the exceptions. Those deviations belong in the answer.
Implication for the app: Add a pre-retrieval prediction step to the synthesis pipeline. Before injecting context, ask the model to briefly state what it expects the corpus to say about this topic. Use that prediction to generate a secondary retrieval query targeting contradictions of the predicted answer. Those contradiction-targeted chunks are high-priority evidence.

What Is Working Correctly and Must Not Be Changed
The following elements are correctly implemented and should not be touched.
•	Single HTML file with OpenAI. The architecture is correct. The React/Gemini rebuild was wrong and this version fixed it.
•	Real model names (GPT-4.1, GPT-4o, GPT-4o-mini) with a fallback chain.
•	Conversation history retained across the session (last eight exchanges).
•	Page numbers tracked during PDF processing and stored in chunk metadata.
•	Clickable citation modal showing the source text. This is a genuine improvement and should stay.
•	Embeddings actually implemented and called (text-embedding-3-small), not just declared.
•	Hybrid retrieval combining semantic similarity with rule-boosted keyword scoring for must, shall, unless, except, defined as.
•	Closed universe instruction in the system prompt.
•	Refusal detection pattern that catches when the model incorrectly claims it cannot access documents.
•	Retry logic and error handling.
•	UofT brand colors.
•	Cross-document concept bridges.
•	Document memory blocks (outline, key concepts, synopsis).
•	Multi-pass map-reduce pipeline for large corpora.
•	Cognitive planning step before synthesis.

Priority Fixes — Non-Negotiable Before This Version Is Usable
Fix 1: Output Format — The Most Urgent Issue
The system prompt instructs the model to output three visible labeled sections: 'Mental model (macro/meso/micro synthesis),' 'Cross-document links, tensions, and implications,' and 'Uncertainty and limits from provided evidence.' These labels appear in every user-facing response. The ensureHumanAnalysisStructure function actively injects these labels even when the model produces a clean answer.
This is the most fundamental requirement of the entire project and it is currently violated.
The fix: Remove the three-section output shape from the system prompt entirely. Replace the response shape instruction with exactly this:
Write a complete, precise answer in professional prose. Do not use section headers, labels, or any structural markers. After every sentence that contains a factual claim, immediately add the citation in the format [Source: filename, Page X]. If information is missing, state that naturally within the prose. Write the way a senior expert would write a professional memo: directly, thoroughly, and with every claim attributed.
Then disable or delete the ensureHumanAnalysisStructure function. The cognitive reasoning that currently produces those sections should continue happening internally and should produce a better answer. It should never produce visible structure.
Fix 2: Full-Corpus Injection — The Core Architectural Feature
When the total token count of the entire corpus is below the safe context limit, inject everything at once. No retrieval. No ranking. No context budgeting. The model receives every document simultaneously. That is global broadcast. That is what enables the cross-document reasoning this app exists to perform.
The threshold logic: estimate total tokens using the existing tiktoken encoder. If below 100,000 tokens (safe buffer for GPT-4.1 and GPT-4o), set contextStrategy to FULL_CORPUS and pass the complete corpus as context. Only fall back to the hybrid retrieval pipeline when the corpus exceeds that limit.
This feature was in the previous version and was removed. It needs to come back. For small to medium corpora, it is strictly better than any retrieval approach. For large corpora, the hybrid pipeline is the best available fallback.
Fix 3: Exception Index — The Hippocampal Surprise Layer
During document ingestion, after the standard chunker runs, add an exception extraction pass. Scan all chunks for sentences containing: unless, except, notwithstanding, provided that, subject to, does not apply, only if, in the case of, by way of exception, and override.
Store these exception sentences as a separate high-priority exception index in the document database, tagged with their source filename and page number.
In every synthesis call, regardless of whether full-corpus injection or partial retrieval is used, include the complete exception index for any documents that are in scope. Tell the model explicitly at the start of the context: 'The following are exception clauses from the loaded documents. Read these with particular care before answering, as they may override the general rules that appear elsewhere in the corpus.'
This is the most important new feature in this document. It directly addresses the failure mode where standard retrieval finds the rule but misses the exception.
Fix 4: Pre-Retrieval Prediction Step
Add a fast, cheap API call before the main synthesis call. The prompt is simply:
Based on general institutional policy knowledge, and without referring to any specific documents, what would you expect the answer to the following question to be? State your expectation in two to three sentences. Question: [user query]
Use the model's prediction to generate a contradiction-targeted secondary retrieval query: retrieve chunks that contain vocabulary related to the prediction but also contain exception markers. Feed these contradiction-targeted chunks into the main synthesis call alongside the standard retrieved context, labeled clearly as 'potentially contradicting expected answer.'
This implements the predictive processing mechanism: the model generates its prediction, then actively searches for evidence that contradicts it. Those contradictions are the exceptions and edge cases that matter most.
Fix 5: Schema Cache — Build the Relational Map Once
When documents are loaded, add a schema extraction step that runs once and caches the result. The schema extraction prompt is:
From the following document, extract: (1) the primary rules or policies it establishes, expressed as IF [condition] THEN [consequence] statements; (2) any references to other documents, policies, or systems by name; (3) any conditions under which the rules do not apply or are overridden. Return this as a structured summary, not as prose.
Cache this schema alongside the document memory blocks. Include the relevant schemas in every synthesis call as the first context section, before retrieved chunks. Label it: 'Institutional rule schema — use this to understand how the rules relate before reading the evidence.'
This gives the model the relational map that an expert already holds before they read the evidence. It enables the kind of reasoning that connects rules across documents without having to infer those connections from raw text on every query.
Fix 6: Evaluation Metrics — Move to Admin Panel Only
The toast notification showing evaluation scores after every response needs to be removed from the user-facing interface. This is internal quality monitoring data. Staff should never see it. Move all evaluation output to the admin panel only. The evaluator is valuable for you as a diagnostic tool. It has no place in what staff users see.
Fix 7: Raise max_tokens on Synthesis Call
The default max_tokens in callCompletion is 2500. For a complex multi-document query, the synthesis call needs to produce a thorough, fully-cited answer. 2500 tokens is not enough. Raise it to 8000 for the main synthesis call. The planning call, prediction call, and map pass calls can stay lower (500–750) since those are intermediate steps producing internal notes, not the final answer.
Fix 8: UI Language
Replace the following with plain professional language:
•	'0 knowledge chunks' in the sidebar → remove entirely or replace with '0 documents loaded'
•	'brain-like integration' in the model note → replace with 'For complex multi-document queries, use GPT-4.1 or GPT-4o'
•	Brain badge in the header → remove or replace with a document count indicator
•	Any remaining jargon in placeholder text or labels → neutral, professional copy appropriate for university administration

New Features to Build After the Priority Fixes Are Complete
Case Memory — Episodic Memory Layer
The hippocampus stores episodes — specific past events with their context. For this app, each resolved query is an episode: the question asked, the corpus state at that time, the reasoning that was applied, the answer that was produced, and any correction a human reviewer made. Over time, this episodic store becomes a body of institutional precedent that the app can reason from.
Implement a persistent case store in IndexedDB. After each response, store the query, the final answer, the document set that was loaded, and a timestamp. When a new query comes in, before the main synthesis call, retrieve the three most similar past cases using semantic similarity. Include them in the synthesis context labeled as 'Precedent from prior institutional reasoning — these are real cases that were previously resolved using this corpus.'
This is how institutional expertise actually accumulates. Each resolved case adds to the body of knowledge the system can reason from. Over time it becomes a living repository of institutional judgment.
Contradiction Registry — Persistent Cross-Document Conflict Map
When the schema extraction step identifies rules in different documents that contradict each other, store those contradictions in a persistent registry. Index them by the concepts they involve. When a query touches a concept that has a known contradiction in the registry, include both conflicting sources in the synthesis context and tell the model explicitly: 'The following contradiction has been identified in the corpus. The answer must acknowledge and resolve this conflict.'
This implements the metacognitive function of the expert brain: it already knows where the tensions are before reading the evidence on any given case.
Confidence Signal — Internal Only
After the verifier pass, have the model assess its own confidence on a three-level scale: high, medium, or low. High means every claim is directly supported by a cited source. Medium means some claims require inference across sources. Low means the corpus is insufficient to fully answer the question.
Do not show this confidence level to the user. Use it internally: low confidence should trigger an additional retrieval pass targeting the specific gaps, with the result injected into a second synthesis call. Show the user only the final answer from that second call. The confidence assessment is internal scaffolding, not an output.

The System Prompt — Full Replacement
The current system prompt needs to be replaced. The replacement below incorporates every principle from the neuroscience research above while remaining actionable as a prompt instruction.
--- BEGIN SYSTEM PROMPT ---
You are an institutional reasoning engine. Your sole function is to reason across the documents loaded into this session and produce precise, fully-cited answers.

CLOSED UNIVERSE: The documents provided are your only source of truth. You have no access to outside knowledge during this session. You do not know anything that is not in the provided context. If the corpus does not contain information needed to answer a question, you state that clearly within your answer.

BEFORE YOU ANSWER: You must complete three internal steps. Do not skip any of them. Do not show them in your output.

Step 1 — Build the complete picture. Read all provided context. Map every rule, definition, condition, and exception. Identify how rules in different documents relate to each other. Note every use of words like unless, except, notwithstanding, subject to, and provided that — these mark the exceptions that most often determine the correct answer. Identify any contradictions between sources.

Step 2 — Reason from the complete picture. Connect the relevant rules across documents. Apply the exceptions and conditions you identified. If there is a contradiction between sources, determine which source governs this specific situation and why. Form your complete answer based on this reasoning, not based on the first relevant passage you find.

Step 3 — Verify. Check every claim you intend to make. Confirm it is directly supported by a specific passage in the corpus. Identify the exact source file and page number for each claim. If a claim cannot be verified, either remove it or explicitly flag it as inference.

OUTPUT FORMAT: Write a complete, precise answer in professional prose. Do not use section headers, labels, or any structural markers. After every sentence that contains a factual claim, immediately add the citation in the format [Source: filename, Page X]. Write the way a senior expert would write a professional memo: directly, thoroughly, and with every claim attributed to its source.

If the corpus contradicts itself on a point relevant to the answer, state both positions, cite both sources, and explain which governs and why.

If the corpus does not contain information needed to fully answer the question, say so within the prose rather than as a separate section.

Do not use the phrases 'Mental model,' 'Cross-document synthesis,' 'Uncertainty and limits,' 'Direct findings,' or any other meta-cognitive label in your output. Those are your internal processes. The user sees only the answer.
--- END SYSTEM PROMPT ---

Implementation Order
Follow this order exactly. Do not start a later item until the earlier one is tested and working.
•	Fix 1 first: Replace the system prompt. Remove ensureHumanAnalysisStructure. Verify that the output is clean prose with inline citations and no section headers. Test with five real institutional queries before proceeding.
•	Fix 2: Restore full-corpus injection. Verify that small corpora get the full corpus injected and that the contextStrategy indicator shows FULL_CORPUS. Test with three to five documents loaded simultaneously.
•	Fix 3: Implement the exception index. Verify that exception clauses are being extracted and stored during document ingestion. Verify they appear in the synthesis context labeled correctly.
•	Fix 4: Add the pre-retrieval prediction step. Verify the prediction is generated and the contradiction-targeted secondary retrieval is running.
•	Fix 5: Implement schema cache. Verify schemas are extracted on ingestion and appear in synthesis context.
•	Fix 6: Remove evaluation toasts from user interface. Verify they appear only in admin panel.
•	Fix 7: Raise max_tokens to 8000 for synthesis call. Verify the setting is applied.
•	Fix 8: Clean UI language. Verify no technical or jargon terms appear in any user-facing interface element.
•	After all eight fixes are verified: implement case memory (episodic store).
•	After case memory is working: implement contradiction registry.
•	After contradiction registry: implement internal confidence signal with automatic re-query on low confidence.

Research Sources Used in This Analysis
Bein, O. & Niv, Y. (2025). Schemas, reinforcement learning and the medial prefrontal cortex. Nature Reviews Neuroscience, 1–17.
Binz, M., Dasgupta, I., Jagadish, A., Botvinick, M., Lake, B. M., & Schulz, E. (2025). A foundation model to predict and capture human cognition. Nature. https://doi.org/10.1038/s41586-025-08907-9
Chateau-Laurent, H. et al. (2025). Deep learning realizations of global workspace theory: competitive ignition and workspace broadcasting. Frontiers in Robotics and AI.
Kavi, A. et al. (2024). Nested active-inference workspaces: hierarchical cognition and decision-making in AI agents. Frontiers in Computational Neuroscience.
Landsmeer, L. P. et al. (2024). Tricking AI chips into simulating the human brain. Neurocomputing, 610, 128578.
Lewis, P. et al. (2020). Retrieval-augmented generation for knowledge-intensive NLP tasks. NeurIPS 33, 9459–9474.
MIRAGE Research Group. (2025). A neuroscience-inspired dual-process model of compositional generalization. arXiv:2507.18868.
National Institute of Standards and Technology. (2023). AI Risk Management Framework (AI RMF 1.0). NIST AI 100-1.
Soni, A. & Frank, M. (2025). Why working memory limits exist and how the brain learns to chunk information. eLife.
Tavares, R. M. et al. (2025). Hippocampus-neocortex memory architecture: gist encoding, surprise detection, and RAG. bioRxiv v3.
Wang, H. et al. (2025). HaluSearch: Think more, hallucinate less — mitigating hallucinations via dual-process tree search. ACL 2025 Findings.
Wojcik, E. et al. (2025). Working memory reshapes incoming information into low-dimensional formats anticipating future decisions. Cognitive Psychology.
February 2026
