# Project Specifications and Preliminary Report

Filename: GUID_Surname_UESTCHN4006P_Preliminary_year

**UESTCHN4006P | Preliminary report | Working draft**

| Field | Information |
| --- | --- |
| Student Name | [To be completed] |
| Placement Company (if appropriate) | [To be completed / Not applicable] |
| Student Matriculation Number / GUID | [To be completed] |
| Working Title of Project | Design and Implementation of a Multi-Agent Collaboration and Verification System for Code Repair |
| UESTC Student Number | [To be completed] |
| Name of First Supervisor | [To be completed] |
| Degree Programme | [To be completed] |
| Academic Year | [To be completed] |

## Declaration of Originality and Submission Information

*I affirm that this submission is all my own work in accordance with the University of Glasgow and UESTC Regulations, and the James Watt School of Engineering requirements.*

Signed (Student): ____________________    Date: ____________________

Submission: Upload through Moodle and the GC-UESTC/GC-UESTCHN FYP management system by the deadline in Table 1 of the FYP Student Handbook. The official deadline and academic year must be confirmed by the student.

This draft uses the current project title recorded in the yeCoding thesis materials. Student details, supervisor approval and calendar dates remain to be completed. Weeks in the work plan are relative to an agreed project start date.

<!-- pagebreak -->

## Project Description and Background

Software defect repair involves reproducing a failure, locating relevant code, modifying an implementation and checking for regressions. A language model can produce a plausible patch without establishing that the defect has been resolved. Repository repair therefore requires executable acceptance criteria, controlled file changes and evidence that connects a candidate patch to its test results. ReAct provides a basis for interaction with tools, while SWE-agent shows the importance of repository navigation and editing interfaces [1,2].

This project will develop and evaluate a Python terminal-based repair system on the existing yeCoding platform. A coordinator will outline a repair approach, a worker will edit a disposable repository copy, and a verifier will inspect the candidate and available evidence. Roles will exchange explicit artefacts through sequential handoffs, with one writer. Fixed tests will determine acceptance; a favourable model review alone will not establish success. Restricted containers will execute commands, and the system will export a patch and a structured report.

![Figure 1. Proposed repair workflow. Failed candidates return to the worker only while the shared budget permits another attempt.](preliminary/figures/workflow.png)

The intended contribution is an integrated, inspectable repair workflow and a controlled assessment of collaboration costs. The project will investigate whether separating responsibilities helps under a fixed budget, rather than assume that adding roles improves repair quality. This matters to developers who need reproducible results and clear failure explanations before applying generated changes.

**Starting position and scope.** yeCoding already provides tool use, code indexing and terminal interfaces; a repair prototype and archived pilot records also exist. These are prior project work. The proposed activities are refinement, explicit acceptance testing and further controlled evaluation. The scope is Python repositories with runnable tests, without model training or concurrent patch merging. Container execution constrains commands but does not provide virtual-machine-level isolation.

## SMART Objectives

1. **Specify by Week 2:** define the task schema, three role contracts, three comparison strategies and at least eight functional acceptance scenarios, with requirements mapped to executable checks.
2. **Integrate by Week 7:** deliver a runnable repair command that isolates edits, enforces fixed acceptance tests and shared budgets, and exports patches and JSON evidence; demonstrate source preservation and timeout/cancellation cleanup.
3. **Evaluate by Week 12:** freeze two development tasks and at least eight held-out defect tasks; run three strategies with three independent runs per task and strategy (at least 72 held-out runs), using matched model and budget settings.
4. **Report by Week 16:** deliver the system, reproducible evaluation artefacts and a dissertation analysing completion, independent-test outcomes, resource use and failure categories, including negative results and limitations.

<!-- pagebreak -->

## Literature Review

### Tool use and repository-level repair

ReAct interleaves reasoning, actions and observations, enabling a model to revise its behaviour using information from an environment [1]. This is relevant to repair because a failing test or search result can change the next editing decision. However, ReAct is a general interaction method: its original experiments do not establish repository repair reliability. For this project, the useful design principle is an observable tool loop; acceptance must be supplied separately through executable tests.

SWE-agent studies interfaces designed for software engineering agents, including navigation, file editing and execution [2]. Its significance is that the same model operates through an interface whose constraints affect how effectively it explores and changes a repository. It motivates concise tool outputs, line-based inspection and clear execution feedback. Its benchmark results remain tied to its models, task collection and evaluation environment; they cannot be transferred to yeCoding or used to infer a collaboration benefit.

Agentless provides a complementary approach using a more structured localisation, repair and validation pipeline [8]. It is a useful counterpoint to open-ended agent interaction: additional autonomy and conversational steps are not automatically necessary for every repair task. A structured pipeline can make search stages and costs easier to inspect. The proposed project will therefore retain a single-role baseline and measure the cost of role handoffs rather than compare only increasingly elaborate multi-role systems.

RepairAgent investigates autonomous repair with a tool-supported language model [9]. It supports the relevance of combining code inspection, editing and test execution in a repair loop. Nevertheless, adopting an interaction pattern does not reproduce a published system or its results. The present work will focus on a smaller, explicitly supported Python setting and will assess the reliability of its own workspace, acceptance and reporting mechanisms.

### Collaboration and feedback

AutoGen describes configurable agents that interact through conversations and tools [3]. It supplies a framework for assigning responsibilities and organising exchanges. For repair, this motivates separate planning, editing and inspection contexts. The limitation is that role separation also repeats context and consumes model calls. A verifier that sees an incomplete patch or lacks test evidence may add commentary without improving acceptance. Consequently, this project's comparison will hold the outer budget constant and record consumption across all roles.

Reflexion uses textual feedback and episodic memory to inform later attempts without updating model weights [4]. Self-Refine similarly studies an iterative cycle of generation, feedback and revision [10]. These approaches motivate retaining failure information, but feedback quality remains a central issue: a model may confidently endorse incorrect output. This project will distinguish model-authored review from execution evidence. Tests will remain the acceptance gate in every strategy, and the experiment will vary the feedback given to the verifier and worker.

The reviewed work suggests a design trade-off between more explicit responsibility and increased coordination overhead. This is a synthesis for the proposed system, not a claim that the literature establishes one universally superior architecture. The research question is whether evidence-aware handoffs provide useful repair behaviour within the same resource allowance, and where their costs prevent completion.

<!-- pagebreak -->

### Repository context and evaluation

RepoCoder combines repository retrieval with iterative generation for code completion [5]. It establishes the relevance of information outside the current file, but completion and defect repair have different correctness conditions. The proposed system will use existing Python AST and symbol-index facilities to retrieve definitions and approximate call relationships. This is a practical support mechanism, not a reproduction of RepoCoder, and its contribution to repair accuracy will not be claimed without a separate ablation.

Lost in the Middle reports position-sensitive use of information in long contexts on its evaluated tasks and models [7]. This motivates selecting relevant evidence rather than assuming that a larger prompt guarantees better use of a repository. The finding does not imply that every current model behaves identically. Here, compact role handoffs will carry the task, patch and selected diagnostics; request sizes and cumulative token use will be recorded so that context overhead is visible.

SWE-bench formulates repair using real GitHub issues and repository-level evaluation [6]. It highlights the value of executable assessment over the apparent plausibility of generated code. This project's initial task set will instead use defects injected into pinned external Python source. This gives controlled failures and a manageable scope, but limits realism and diversity. Such tasks will be labelled synthetic and will not be presented as SWE-bench instances or directly compared with published benchmark success rates.

| Research strand | Main insight for this project | Limitation requiring a project-specific check |
| --- | --- | --- |
| ReAct; SWE-agent [1,2] | Couple model decisions to repository tools and observations. | Tool completion does not establish patch correctness. |
| AutoGen [3] | Define role responsibilities and handoff messages. | Coordination may consume the available repair budget. |
| Reflexion; Self-Refine [4,10] | Preserve useful feedback across attempts. | Self-generated feedback may be wrong or incomplete. |
| RepoCoder; long-context study [5,7] | Select repository evidence under a context budget. | Retrieval and position effects differ by task and model. |
| SWE-bench; Agentless; RepairAgent [6,8,9] | Evaluate complete repair processes with execution. | Dataset and environment differences limit comparisons. |

*Table 1. Critical comparison of related research and its implications.*

![Figure 2. Literature-to-design mapping. The project combines established ideas and evaluates the resulting workflow; the links do not imply that prior papers validate this implementation.](preliminary/figures/literature.png)

### Research direction and project rationale

Across these selected studies, the emphasis extends from generating code towards managing repository interactions, feedback and executable assessment. Structured pipelines also challenge the assumption that more interaction is always beneficial [2,6,8,9]. The proposed direction is therefore a bounded, evidence-based repair workflow with explicit failure outcomes. The project-specific question is how role handoffs, independent acceptance and stopping rules behave together under matched budgets. This is an engineering research question, not a claim that these components have never been combined.

Docker's documentation further informs execution constraints: container security depends on configuration, and resource limits require explicit settings [11,12]. These documents support implementation choices, not a claim of absolute isolation. Functional tests will check the actual configured behaviour of this system.

<!-- pagebreak -->

## Technical Background

### Research questions and hypotheses

**RQ1:** Can the system keep the original repository unchanged while producing a candidate patch with traceable execution evidence, including on failure, timeout and cancellation?

**RQ2:** Under matched model and outer budget settings, how do single-role execution feedback, multi-role review feedback and multi-role execution-plus-review feedback differ in completion, independently checked repair outcomes and resource use?

**Working hypothesis:** supplying actual execution evidence to a separate verifier may make feedback more actionable than review text alone. Additional role calls may nevertheless reduce completion under tight budgets. A null or adverse result is an acceptable outcome; no improvement is assumed from the existing prototype or pilot work.

### Architecture and task model

A repair task will contain an issue description, pinned source state, prepared container image, fixed acceptance commands and explicit resource budgets. The implementation will reuse yeCoding's model client, tool executor, Python symbol index and usage reporting. A dedicated repair orchestrator will manage the task independently of general conversational modes.

The coordinator and verifier will receive read-only business-code tools; the worker will receive bounded editing and container-command tools. Roles will have separate message histories. The coordinator will return a short plan; the worker will produce actual file edits; the verifier will inspect the candidate and the evidence allowed by the selected strategy. Roles are logical contexts and may use the same model. Sequential handoffs avoid concurrent editing and automatic merge conflicts.

The source snapshot will define the task's baseline. The worker will edit a disposable copy, and the orchestrator will construct a separate verification copy from the candidate. Candidate checks will reject out-of-scope paths, unsupported file types and changes to protected tests or configuration. The exported patch will be associated with source and candidate fingerprints. The system will not automatically apply it to the original repository.

### Execution and stopping rules

Before model editing, the original unmodified library must pass the task suite, while the injected-defect version must produce a genuine test failure. Environment failures will be diagnosed separately. If the supplied repair input already passes acceptance, the system will report that condition without claiming to have repaired a defect.

Commands will run in prepared Docker containers with disabled network access, a read-only root filesystem, restricted capabilities and explicit CPU, memory, process and time limits [11,12]. The worker's copy may be writable; fixed acceptance will use a read-only verification mount where supported by the test setup. The host process will still handle model communication and bounded file operations. Containers share the host kernel, so these controls do not establish protection against every possible escape.

All roles will share limits for attempts, model calls, cumulative tokens and elapsed time. Token thresholds checked before requests may be exceeded by the final response; this will be documented rather than described as a strict token cap. Failed tests will guide a bounded retry. No progress, invalid candidates, unavailable environments, cancellation and exhausted budgets will yield distinct records. Success will require fixed acceptance and completion of the strategy's required steps; an unfinished verifier call cannot silently convert a run into success.

<!-- pagebreak -->

### Controlled comparison

| Strategy | Roles | Feedback supplied for subsequent repair |
| --- | --- | --- |
| S: execution feedback | Worker | Fixed-test output and candidate checks. |
| R: review feedback | Coordinator, worker, verifier | Review text; verifier receives no new candidate test output. |
| V: execution plus review | Coordinator, worker, verifier | Fixed-test output together with evidence-aware review. |

*Table 2. All strategies retain the same orchestrator-controlled fixed acceptance gate.*

The comparison isolates feedback organisation within a common repair harness. It does not compare an untested baseline with a tested system. Because R and V contain extra role calls, actual consumption may differ even when limits match. The S-to-R contrast combines role organisation with feedback differences; R-to-V is the closer test of access to execution evidence.

The initial dataset will use pinned python-dotenv source with one documented behavioural mutation per task. Two development tasks will support debugging; at least eight held-out tasks will cover parsing, interpolation and environment-update behaviour. Clean source must pass, and each mutation must fail before use. Development tasks will be excluded from the evaluation denominator. Existing pilot tasks and results will be disclosed; tasks previously used to adjust the method will be reclassified as development and replaced with new held-out mutations before confirmatory testing.

The planned minimum is eight held-out tasks, three strategies and three independent runs per condition: 72 runs. Each run will start from a fresh copy and context. Model identity, generation settings, image digest, acceptance suite and outer budgets will match. Strategy order will be randomised within task/repetition blocks. Cross-task memory will be disabled, while procedure skills will remain constant. Three repetitions support descriptive variability estimates but do not create 24 independent defect cases per strategy.

### Outcomes, evidence and analysis

**Workflow completion rate** will be the number of runs that pass fixed acceptance and finish required handoffs within budget, divided by all initiated evaluation runs. **Independent-test pass rate** will use the same denominator, with unavailable patches separately marked as not assessed. **False acceptance** will count workflow-success patches that fail additional held-out tests. These tests will be added only in a separate evaluation directory after the run and never fed back to the model.

The report will include calls, cumulative input/output tokens, elapsed time and failure categories for every run, including failures. Candidate correctness and workflow completion will be reported separately: a candidate can pass tests while later coordination exhausts its budget. Task-level results and within-task variation will accompany aggregates. Analysis will remain descriptive unless the number and independence of tasks justify stronger statistical claims; observed zero false acceptances cannot establish zero risk.

Functional checks will cover source preservation, protected-test edits, misleading positive reviews, failed-then-passing retries, budget exhaustion, unavailable images, command timeout and cancellation cleanup. A real CLI demonstration will verify the public entry point. Patches, reports, event records and a manifest of configuration fingerprints will enable inspection and reruns, subject to external model availability.

**Validity and feasibility.** Synthetic mutations, a small Python-only corpus, remotely served models and limited test coverage constrain generalisation. Additional tests can still miss defects. The 72-run plan depends on measured pilot cost and available API credit; any reduced scope must be agreed and reported before the evaluation is frozen. API charges and required hardware will be estimated in Week 2. No participant data are planned; third-party source licences and credential-free artefact storage will be checked. Skills and memory are integration facilities, without a claimed independent accuracy benefit.

<!-- pagebreak -->

## References

[1] S. Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models," ICLR, 2023. [Original paper](https://openreview.net/forum?id=WE_vluYUL-X).

[2] J. Yang et al., "SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering," Advances in Neural Information Processing Systems, vol. 37, 2024. [Publisher](https://proceedings.neurips.cc/paper_files/paper/2024/hash/5a7c947568c1b1328ccc5230172e1e7c-Abstract-Conference.html).

[3] Q. Wu et al., "AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversations," First Conference on Language Modeling, 2024. [Original paper](https://openreview.net/forum?id=BAakY1hNKS).

[4] N. Shinn, F. Cassano, A. Gopinath, K. Narasimhan and S. Yao, "Reflexion: Language Agents with Verbal Reinforcement Learning," Advances in Neural Information Processing Systems, vol. 36, 2023. [Publisher](https://proceedings.neurips.cc/paper_files/paper/2023/hash/1b44b878bb782e6954cd888628510e90-Abstract-Conference.html).

[5] F. Zhang et al., "RepoCoder: Repository-Level Code Completion Through Iterative Retrieval and Generation," EMNLP, pp. 2471-2484, 2023. [doi:10.18653/v1/2023.emnlp-main.151](https://doi.org/10.18653/v1/2023.emnlp-main.151).

[6] C. E. Jimenez et al., "SWE-bench: Can Language Models Resolve Real-World GitHub Issues?," ICLR, 2024. [Original paper](https://openreview.net/forum?id=VTF8yNQM66).

[7] N. F. Liu et al., "Lost in the Middle: How Language Models Use Long Contexts," Transactions of the Association for Computational Linguistics, vol. 12, pp. 157-173, 2024. [doi:10.1162/tacl_a_00638](https://doi.org/10.1162/tacl_a_00638).

[8] C. S. Xia et al., "Agentless: Demystifying LLM-based Software Engineering Agents," arXiv:2407.01489, 2024. [Preprint](https://arxiv.org/abs/2407.01489).

[9] I. Bouzenia, P. Devanbu and M. Pradel, "RepairAgent: An Autonomous, LLM-Based Agent for Program Repair," arXiv:2403.17134, 2024. [Preprint](https://arxiv.org/abs/2403.17134).

[10] A. Madaan et al., "Self-Refine: Iterative Refinement with Self-Feedback," Advances in Neural Information Processing Systems, vol. 36, 2023. [Original paper](https://arxiv.org/abs/2303.17651).

[11] Docker, "Docker Engine security," official documentation, accessed 26 October 2026. [Documentation](https://docs.docker.com/engine/security/).

[12] Docker, "Resource constraints," official documentation, accessed 26 October 2026. [Documentation](https://docs.docker.com/engine/containers/resource_constraints/).

<!-- pagebreak -->

## Measurable Outcomes & Work Plan

**Project category:** software development and experimental evaluation. The proposed 16-week schedule starts at an agreed Week 1; it describes remaining work from the existing prototype, not a reconstructed historical schedule. Calendar dates require supervisor confirmation.

| Deliverable | Measurable acceptance criterion | Due |
| --- | --- | --- |
| Specification and feasibility record | Task schema, role contracts, eight or more acceptance scenarios, and pilot-based API cost estimate. | W2 |
| Integrated repair workflow | Public CLI demonstration; patch and report export; isolation, acceptance, failure and cleanup checks pass. | W7 |
| Frozen evaluation package | Two development tasks; at least eight genuinely held-out mutations; clean-pass and defect-fail records; additional tests. | W8 |
| Evaluation and analysis | At least 72 planned runs, or a prospectively agreed revised scope; complete denominators, usage and failure records. | W12 |
| Final submission package | Reproducible artefacts, dissertation, documented limitations and supervisor-reviewed submission forms. | W16 |

![Figure 3. Proposed Gantt chart. Week numbers are relative; milestones are specification (W2), integration (W7), dataset freeze (W8), analysis (W12) and submission (W16).](preliminary/figures/gantt.png)

The critical dependencies are a usable model API, a prepared Docker image and reliable task tests. Week 2 will establish cost and runtime feasibility. The task set and budget will be frozen before comparative runs; environment failures and reruns will be retained with reasons. Weeks 13-16 provide time for analysis, writing and corrections rather than unreported experimental tuning.

**Resources and accompanying submissions.** Required resources are a Docker-capable computer, Python 3.11+, disk space for images and evidence, and supervisor-agreed model API credit. No additional hardware is currently proposed. The component/resources request must be completed and emailed separately to the first supervisor. The official Risk Assessment Form and Ethical Consideration Declaration must be completed and signed with the supervisor, and the first logbook must be submitted separately. Their official templates and deadlines should be checked on FYP Moodle; this report does not replace those documents.

## Technical Background

[Proposed method, research hypothesis and evaluation design.]

## References

[At least ten authentic references, cited in the report.]

## Measurable Outcomes & Work Plan

[Deliverables, acceptance criteria and a Gantt chart. Calendar dates await confirmation.]
