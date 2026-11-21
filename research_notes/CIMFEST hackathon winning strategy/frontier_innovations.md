# Frontier Software/Systems Innovation Vectors for a 24–48h Hackathon (late 2025 – Nov 2026)

Scope: software-only ideas a small team using Claude Code can prototype. Research date: 2026-11-21. About 18 search/fetch calls. Many 2026 sources are vendor blogs or preprints, and these are flagged. Feasibility scale: **A** = a 24h demo is realistic with mature libraries; **B** = 48h is realistic if scope is tightly cut; **C** = a demo is possible only by stubbing the hard part.

## What genuinely novel systems ideas are trending now (late 2025–2026)?

### Takeaway
The strongest 2026 vectors combine a new protocol layer with an established systems problem. Examples: agent interoperability and payments (MCP 2026-07-28 stateless spec + A2A + x402/AP2), local-first sync with end-to-end encryption (Automerge 3.x + Keyhive/BeeKEM + Subduction over iroh), in-browser inference (Transformers.js v4 / WebLLM on WebGPU), and verifiable identity/credentials (OpenID4VP 1.0 + SD-JWT; the EUDI wallet deadline is Dec 2026). Deterministic simulation testing, LLM-aided formal verification (Lean/Dafny/Verus), and capability-based defences against prompt injection are the most "deep system design" topics.

### Cited Findings

**Vector 1 — Agent protocol stack: MCP (tools) + A2A (agent↔agent) + AP2/x402 (payments)**
- What/why new: MCP spec **2026-07-28** is final with SDKs. The headline change is that MCP becomes **stateless at the protocol layer**, with capabilities such as Tasks and MCP Apps defined as explicitly versioned **extensions**. It also adds a Specification Feature Lifecycle and Deprecation Policy — [MCP blog RC](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/); [AAIF: what's changing](https://aaif.io/blog/mcp-2026-07-28-whats-changing-and-how-to-migrate); [CData](https://www.cdata.com/blog/mcp-2026-07-28-release)
- Tasks (experimental in 2025-11-25) moved into the `io.modelcontextprotocol/tasks` extension, which AWS contributed. **Elicitation is now non-blocking**: the server embeds the request in a `tasks/get` response, and the client answers asynchronously via `tasks/update` — [AAIF Tasks extension](https://aaif.io/blog/handle-long-running-mcp-tool-calls-with-the-mcp-tasks-extension); [AWS OSS blog](https://aws.amazon.com/blogs/opensource/shaping-the-future-of-mcp-aws-commitment-and-vision)
- Governance: Anthropic donated MCP to the Linux Foundation's Agentic AI Foundation (AAIF) in Dec 2025. A new roadmap followed in Aug 2026 with working groups and SEPs, but no dates — [AAIF](https://aaif.io/blog/mcp-is-growing-up); [MCP roadmap](https://blog.modelcontextprotocol.io/posts/mcp-roadmap/)
- Layering: MCP = an agent using tools; A2A = agents discovering each other via **AgentCards** and delegating tasks; payments = settlement on top — [Splunk](https://www.splunk.com/en_us/blog/artificial-intelligence/the-agent-economy-when-ais-pay-each-other.html)
- AP2 (Google, launched Sept 2025) runs as an extension of A2A and MCP. It uses signed **mandates** (verifiable objects signed by the user's wallet or agent key) and does not itself move money — [Google Cloud blog](https://cloud.google.com/blog/products/ai-machine-learning/announcing-agents-to-payments-ap2-protocol/); [dev.to comparison](https://dev.to/noopy420/google-ap2-vs-x402-the-two-protocols-competing-to-power-ai-agent-payments-5e9j)
- x402 (HTTP 402-based stablecoin micropayments; the agent holds funds and pays autonomously). An A2A x402 extension exists, built with Coinbase, the Ethereum Foundation, and MetaMask — [Coinbase](https://www.coinbase.com/developer-platform/discover/launches/google_x402) (vendor source)
- The Linux Foundation confirmed the x402 Foundation's operational launch on **14 Jul 2026** (reported ~40 members incl. AWS, Circle, Cloudflare, Google, Mastercard, Visa). **AP2 v0.2 (Apr 2026)** was reportedly handed to the FIDO Alliance. Chainalysis reportedly counted more than 100M cumulative x402 transactions on Base through Q1 2026 — [Splunk](https://www.splunk.com/en_us/blog/artificial-intelligence/the-agent-economy-when-ais-pay-each-other.html). These rest on a single secondary source; I did not find a primary source for the FIDO handover.
- System-design story: stateless request routing vs. durable task state (Tasks extension), async human-in-the-loop (non-blocking elicitation), capability negotiation and versioned extensions, cryptographically signed intent and cart mandates (non-repudiation), and an HTTP-native payment challenge/response.
- Feasibility: **A**. A demo where an agent discovers another via an AgentCard, calls a paid MCP tool, receives a 402, pays a testnet stablecoin, and gets the result is very doable.

**Vector 2 — Local-first software with E2E-encrypted CRDT sync**
- Automerge **v3.3.0–3.3.2** (Rust crate 0.10.0). **Hexane v1**, a rewritten columnar storage engine, has landed with a reported 2–9× speedup. automerge-repo **v2.6.0 alphas** harden long-running sync servers — [This Month in Automerge, July 2026](https://automerge.org/blog/2026-july/)
- New pieces: **Keyhive** brings E2E encryption into automerge-repo, with early work on author provenance. The **BeeKEM** paper is on ePrint (2026/1434). The **Subduction** sync system has a redb-backed server, **iroh v1.0** P2P transport, and in-progress Cloudflare Durable Objects support. Ecosystem projects include Braid, an Automerge-based issue tracker *for LLM agents* — [Automerge July 2026](https://automerge.org/blog/2026-july/)
- Automerge 3 is reported to cut memory use ~10×. Release dates conflict across sources (May 2025 vs 2026) — [automerge.org feed](https://automerge.org/index.xml); [byteiota](https://byteiota.com/?p=12489)
- Loro 1.x (docs.rs shows 1.6.0) offers Rust/JS(WASM)/Swift. Its WASM is ~970KB gzipped, which is a bundle-size cost — [Loro README](https://www.github.com/loro-dev/loro); [docs.rs](https://docs.rs/crate/loro/1.6.0)
- Yjs/Automerge are recommended for offline-first and multi-language bindings — [Velt 2026 roundup](https://velt.dev/blog/best-crdt-libraries-real-time-data-sync); for architecture overview see [Smashing Magazine, May 2026](https://smashingmagazine.com/2026/05/architecture-local-first-web-development/)
- System-design story: strong eventual consistency without coordination, causal history/DAG sync, group key agreement for E2EE with dynamic membership (BeeKEM is a KEM tree for decentralized groups), P2P NAT traversal (iroh), and a "dumb relay" server that cannot read data.
- Feasibility: **A** for a collaborative app with offline mode plus an airplane-mode demo. **B** for adding Keyhive E2EE, which is new, so the API may churn.

**Vector 3 — On-device / in-browser AI (WebGPU, WASM)**
- **Transformers.js v4** preview (Feb 2026) brings a new WebGPU runtime that runs the same code in browser and server runtimes. It caches WASM locally for offline use, and the default export is 53% smaller — [HF blog](https://huggingface.co/blog/transformersjs-v4)
- **WebLLM** offers an OpenAI-compatible API on WebGPU and ML compilers — [arXiv 2412.15803](https://arxiv.org/html/2412.15803v1). It reportedly decodes Llama 3.1 8B at 41.1 tok/s on an M3 Max, ~71% of native MLC-LLM — [pinggy](https://pinggy.io/blog/run_llm_in_browser_webgpu/)
- **LlamaWeb** (arXiv 2605.20706, May 2026) claims a 54–69% decode speedup over WebLLM/Transformers.js. It counts 400 models for WebLLM vs 41,632 for Transformers.js. These are author-reported figures — [arXiv](https://arxiv.org/html/2605.20706)
- Gemma 4 in the browser with Transformers.js + WebGPU tutorial (Jul 2026) — [PyImageSearch](https://pyimagesearch.com/2026/07/27/running-gemma-4-in-the-browser-with-transformers-js-and-webgpu/)
- Split: use WebLLM for chat LLMs, and Transformers.js for Whisper/CLIP/embeddings/segmentation — [dev.to](https://dev.to/pockit_tools/running-llms-in-the-browser-webgpu-transformersjs-and-chromes-built-in-ai-explained-5g18)
- System-design story: a zero-server privacy architecture, model sharding and caching (Cache API/OPFS), quantization trade-offs, GPU memory limits, and hybrid routing between local and cloud models.
- Feasibility: **A**. Live demo idea: pull the Wi-Fi and the app keeps working.

**Vector 4 — Verifiable credentials & decentralized identity (EUDI, MOSIP Inji)**
- The OpenID Foundation provides open-source conformance tests for **OpenID4VP 1.0 + HAIP 1.0**, covering **SD-JWT VC** and **ISO 18013-5 mdoc**, the two EUDIW launch formats. A further OpenID4VP 1.1 interop event was considered for 2026 — [OIDF EUDIW hub](https://openid.net/openid-foundation-launches-eudiw-resource-hub/)
- EU states must offer wallets by **Dec 2026**, and regulated relying parties must accept them by Dec 2027 (Reg. 2024/1183). Open-source verifiers exist: OpenEUDI (TS, MIT), Tessio (.NET, Apache-2.0), walt.id (Kotlin), and SpruceID (Rust) — [OpenEUDI dev.to](https://dev.to/eidas-pro/introducing-openeudi-an-open-source-sdk-for-eu-digital-identity-verification-1eae); [Tessio NuGet](https://www.nuget.org/packages/Tessio.Verifier.OpenId4Vp/)
- MOSIP **Inji** is splitting into an independent open-source credentialing platform (Inji 1.0), covering education, health, employability, trade, and transport credentials — [Biometric Update, Sep 2026](https://www.biometricupdate.com/202609/mosip-broadens-inji-beyond-digital-id-into-open-credentialing-platform). Components are Wallet, Web, **Inji Certify** (OpenID4VCI draft 13; dev release v0.12.2), and **Inji Verify** — [MOSIP Inji](https://www.mosip.io/inji); [IDTechWire](https://idtechwire.com/mosip-releases-inji-certify-developer-tool-for-verifiable-credential-issuance/)
- System-design story: the issuer–holder–verifier trust triangle, selective disclosure (salted hash disclosures in SD-JWT), holder binding (key-bound proofs), revocation via status lists, offline QR verification, and trust registries.
- Feasibility: **A–B**. Issue an SD-JWT credential, present it with selective disclosure via QR, and verify it offline. This works well for DPI-themed (India Stack/MOSIP) judging.

**Vector 5 — Verifiable computation / zkVMs**
- SP1 reportedly proved 99.7% of Ethereum blocks in under 12s on 16× RTX 5090 (Succinct, Nov 2025). SP1, RISC Zero, and audited OpenVM are named as conservative choices, and Jolt is still alpha — [wavect 2026 overview](https://wavect.io/blog/zero-knowledge-proofs-production-2026.md)
- The EF real-time proving roadmap teams are SP1 Turbo, Pico (Brevis), RISC Zero, ZisK, Airbender, OpenVM, and Jolt — [Brevis report](https://medium.com/@0xjacobzhao/brevis-research-report-the-infinite-verifiable-computing-layer-of-zkvm-and-zk-data-coprocessor-3761f902ecd7)
- zk-X509 is an application pattern that proves on-chain identity from legacy PKI certificates using ZK proofs — [arXiv 2603.25190](https://arxiv.org/pdf/2603.25190)
- System-design story: prover/verifier asymmetry, proving as an offloaded, parallelizable job (GPU clusters), and succinct proofs as a trust-minimized API response.
- Feasibility: **B**. Writing a Rust guest program for an SP1/RISC Zero zkVM is approachable, but CPU proving is slow, so use small computations or mock-mode proving for the live demo.

**Vector 6 — Content provenance (C2PA) / deepfake resilience**
- C2PA spec **2.3** was published 5 Jan 2026. July 2026 deployment guidance says the current version is **2.4** — [C2PA 2.3 announcement](https://c2pa.org/the-c2pa-launches-content-credentials-2-3-and-celebrates-5-years-of-impact-across-the-digital-ecosystem/); [Deployment Guidance PDF](https://c2pa.org/wp-content/uploads/sites/33/2026/07/Content-Credentials-Deployment-Guidance.pdf)
- c2patool (in the c2pa-rs repo) is at release 0.26.59 (12 May 2026), with frequent releases — [changelog](https://opensource.contentauthenticity.org/docs/c2patool/docs/changelog/)
- Adoption: C2PA claims 6,000+ members and affiliates with live implementations. TikTok joined the Steering Committee. Google is bringing verification to Gemini, Search, and Chrome, and OpenAI announced C2PA conformance. These are self-reported — [C2PA news](https://c2pa.org/news/); [Adobe](https://business.adobe.com/blog/content-authenticity-for-enterprise); [CAI state of 2026](https://contentauthenticity.org/blog/the-state-of-content-authenticity-in-2026)
- System-design story: a signed manifest chain (a provenance DAG of ingredients), hard binding (hash) vs soft binding (watermark/fingerprint) for stripped metadata, and certificate trust lists.
- Feasibility: **A** with c2patool / c2pa-js. Sign a photo, edit it, show the broken chain, then re-sign with an ingredient.

**Vector 7 — Deterministic simulation testing (DST)**
- TigerBeetle's **VOPR** runs real consensus and storage code under a deterministic simulator. Bugs are reproducible from a seed plus a git commit. Its new "protocol-aware DST" checks invariants on every replica — [TigerBeetle blog, 20 Aug 2026](https://tigerbeetle.com/blog/2026-08-20-protocol-aware-dst); [VOPR docs](https://docs.tigerbeetle.com/about/vopr); [Antithesis BugBash talk](https://antithesis.com/bugbash/talks/protocol-aware-deterministic-simulation-testing/)
- Antithesis offers DST as a service, running the whole system under a deterministic hypervisor with fault and scheduling injection — [Jane Street blog](https://blog.janestreet.com/getting-from-tested-to-battle-tested/); [Antithesis DST primer](https://antithesis.com/resources/deterministic_simulation_testing/)
- tokio-rs **Turmoil** provides deterministic execution plus network fault injection, but no disk faults (2024 status). I found no 2026 update — [Eaton Phil 2024](https://notes.eatonphil.com/2024-08-20-deterministic-simulation-testing.html)
- Feasibility: **A–B**. Write a tiny replicated KV store (e.g., Raft) with a seeded simulator plus a visual "time-travel" replay of a found bug. This is a strong system-design story for judges.

**Vector 8 — LLM-aided formal verification ("vericoding")**
- AlgoVeri benchmark (same tasks across Dafny/Verus/Lean): Gemini-3-Flash scored 40.3% on Dafny, 24.7% on Verus, and 7.8% on Lean. Goedel-Code-Prover's Lean framework reaches 62.3% (author-reported) — [arXiv 2603.19329](https://arxiv.org/pdf/2603.19329)
- Rocq gives actionable proof-state feedback, and in one study a Dafny backend failed within 30 min where Rocq succeeded — [arXiv 2605.30914](https://arxiv.org/html/2605.30914). Dafny RL raised the verified rate from 2.2% to 58.1% but revealed **specification hacking** — [search summary of 2026 papers; arXiv 2611.16886](https://arxiv.org/pdf/2611.16886)
- Lean4Agent formally models and verifies agent workflows and trajectories — [HF papers 2606.06523](https://huggingface.co/papers/2606.06523)
- Feasibility: **B**. Claude Code writes Dafny/Lean specs and proofs in a loop with the verifier as the oracle. Demo: "AI wrote it, the machine proved it." Show the weak-spec pitfall honestly.

**Vector 9 — Agent sandboxing & prompt-injection defence**
- The 2026 default for untrusted agent code is a microVM with its own kernel, the agent loop outside the sandbox, secrets outside, and allowlisted egress. E2B uses Firecracker per execution — [Taskade](https://www.taskade.com/blog/ai-agent-sandboxes.md); [InfoQ Jun 2026](https://infoq.com/news/2026/06/untrusted-ai-agents-sandboxes)
- Sandboxes contain the impact of prompt injection but don't prevent it — [Augment](https://www.augmentcode.com/guides/agent-execution-sandbox); Sandlock paper [arXiv 2605.26298](https://arxiv.org/html/2605.26298)
- CaMeL-style capability gating: a deterministic capability manager must approve side-effecting operations — [arXiv 2606.00288](https://arxiv.org/pdf/2606.00288) (secondary description)
- A Sonrai finding showed code in AWS AgentCore's sandbox reaching the microVM metadata service, which illustrates how misconfiguration breaks the boundary — reported in [InfoQ / search summary](https://infoq.com/news/2026/06/untrusted-ai-agents-sandboxes)
- Feasibility: **A**. A red-team live demo works well: inject via a web page, then show taint tracking and a capability policy blocking exfiltration.

**Vector 10 — Privacy tech: FHE**
- TFHE-rs has a CUDA backend (Linux only) with an API near-identical to the CPU one. v1.4 brings major GPU gains, and the latest crate seen is tfhe **1.6.1** — [Zama docs](https://docs.zama.org/tfhe-rs/hardware-acceleration/run-on-gpu); [v1.4 post](https://www.zama.org/post/tfhe-rs-v1-4-gpu-performance-breakthrough-and-more); [docs.rs](https://docs.rs/crate/tfhe/1.6.1/source/src/core_crypto/gpu/mod.rs)
- Sub-millisecond TFHE bootstrapping on Hopper+ GPUs (FHE.org 2026 poster) — [poster](https://fhe.org/conferences/conference-2026/resources/posters/%5B07%5D%20-%20Sub-Millisecond%20TFHE%20Bootstrapping%20on%20GPU.pdf?raw=true)
- Feasibility: **B–C** on laptops (CPU-only is slow). Demo: an encrypted-vote tally or a private comparison on small integers.

**Vector 11 — Voice-first & low-resource-language AI**
- AI4Bharat **IndicConformer-600M-Multi** covers all 22 scheduled Indian languages with CTC and RNNT decoding under an MIT license. A third-party post says 14 languages, which conflicts with this — [HF card](https://huggingface.co/ai4bharat/indic-conformer-600m-multilingual); [GitHub](https://github.com/AI4Bharat/IndicConformerASR); [AIKosh](https://aikosh.indiaai.gov.in/home/models/details/aibharat_indicconformer_600m_multi.html); [Medium conflict](https://medium.com/@anilmathewm/vexyl-stt-free-self-hosted-indian-language-speech-to-text-server-f2909003aaf6)
- The BHASHINI platform hosts open models, including open-sourced Sarvam models (PIB, 12 Mar 2026; promotional) — [PIB](https://pib.gov.in/PressReleasePage.aspx?PRID=2239132). AI4Bharat also released IndicASR and the Rasa expressive TTS dataset (9 languages) — [AIM](https://analyticsindiamag.com/ai-news-updates/ai4bharat-releases-anudesh-indicasr-rasa-for-indic-language-ai)
- **Pipecat** is the main open-source real-time voice-agent framework (Daily, on the Thoughtworks Radar Apr 2026, release line v1.6.0). Kyutai **Moshi** is a 7B full-duplex speech-to-speech model (CC-BY), and 25+ open S2S models exist. Kyutai Pocket TTS does streaming TTS with voice cloning — [Pipecat GitHub](https://github.com/pipecat-ai/pipecat); [Thoughtworks](https://www.thoughtworks.com/radar/languages-and-frameworks/pipecat); [arXiv 2603.05413](https://arxiv.org/html/2603.05413); [speech-to-speech PyPI](https://pypi.org/project/speech-to-speech/); [NVIDIA/Daily blueprint](https://www.daily.co/blog/building-voice-agents-with-nvidia-open-models/)
- System-design story: cascaded (ASR→LLM→TTS) vs native S2S latency budgets, barge-in/turn detection, and the WebRTC transport — [dev.to architectures](https://dev.to/softcery/real-time-s2s-vs-cascading-stttts-voice-agent-architecture-2h7j)
- Feasibility: **A**. A vernacular voice agent for a public service (e.g., a scheme-eligibility check) is a crowd-pleasing demo.

**Vector 12 — World models / interactive simulation**
- Open alternatives to the closed Genie 3 (720p/24fps, ~1-min memory, available to AI Ultra subscribers via Project Genie): **Matrix-Game 2.0** (25 FPS) and 3.0 (720p, up to 40 FPS), **LingBot-World**, **Waypoint-1** (Overworld, 20 Jan 2026; text/mouse/keyboard control), InSpatio-WorldFM (consumer GPUs), and AlayaWorld (Jul 2026 framework). All figures are author-claimed — [eWeek](https://www.eweek.com/news/google-tencent-open-source-ai-video/); [Waypoint-1 HF blog](https://huggingface.co/blog/waypoint-1); [AlayaWorld arXiv 2607.06291](https://arxiv.org/abs/2607.06291); [LingBot-World arXiv 2601.20540](https://arxiv.org/pdf/2601.20540.pdf); [Genie wiki](https://en.wikipedia.org/wiki/Genie_(world_model))
- Feasibility: **C** without a cloud GPU. It is visually dramatic but offers little system-design depth for judges.

**Vector 13 — Programmable money & offline payments**
- The GENIUS Act (P.L. 119-27) regulates payment-stablecoin issuers. The rulemaking is still proposed (comments closed 9 Jun 2026), and the US prohibits a CBDC — [CRS R49351](https://www.everycrsreport.com/reports/R49351.html); [Debevoise Oct 2026](https://www.debevoise.com/-/media/document/2026/10/payment-stablecoins-under-the-genius-act.pdf)
- RBI's offline e-rupee is NFC-based and rolled out via 15 banks in late 2025. An open-source offline-CBDC prototype uses secure hardware plus ZKPs, and an arXiv paper covers compliance-vs-privacy in offline CBDC — [arXiv 2509.25469](https://arxiv.org/html/2509.25469v1); IMF on limited-connectivity CBDC — [IMF FinTech note](https://www.imf.org/en/publications/fintech-notes/issues/2025/08/07/technology-solutions-to-support-central-bank-digital-currency-with-limited-connectivity-a-569259/index.md); BIS — [othp64](https://www.bis.org/publ/othp64.pdf)
- System-design story: double-spend prevention offline (secure-element counters vs. later reconciliation), with privacy vs AML limits. Feasibility: **B** if the secure element is simulated in software (be transparent about that).

**Investor signals**
- YC's 2026 RFS lists skew to hard tech/physical AI (the Summer 2026 list had 15 categories, 8 hardware/capital-heavy). Spring 2026 included stablecoin financial services. A Winter 2027 list of 11 ideas appeared around 27 Oct 2026 — [TNW](https://thenextweb.com/news/yc-summer-2026-rfs-hard-tech-pivot); [techstartups](https://techstartups.com/2026/02/04/y-combinator-is-out-with-its-2026-request-for-startups/); [FourWeekMBA](https://fourweekmba.com/ai-yc-names-11-startup-ideas-as-ai-bottlenecks-move-past-models/); official list at [ycombinator.com/rfs](https://ycombinator.com/rfs) (not opened)

### Inferences
- The most "2026-new" ideas, as opposed to merely trending, are: the MCP stateless core plus the async Tasks extension (Jul 2026), x402 Foundation and AP2 governance (2026), Keyhive/BeeKEM E2EE local-first (2026), Transformers.js v4 WebGPU (2026), protocol-aware DST (Aug 2026), the EUDI wallet go-live (Dec 2026), and Inji's spin-out as a cross-sector VC platform (Sep 2026).
- Cross-vector combinations make stronger hackathon stories than any single vector. Examples:
  - (a) An offline-first DPI credential wallet: SD-JWT VCs + CRDT sync + on-device Indic voice ASR.
  - (b) A "paid agent marketplace": MCP tools behind x402 + AP2 mandates + a capability-gated sandbox.
  - (c) "Provable AI output": a C2PA-signed AI artifact + a verifier.
  - (d) A DST harness for an agent swarm or CRDT sync engine, showing convergence under injected partitions.

### Gaps
- No primary source confirmed AP2 v0.2's handover to FIDO or the x402 transaction counts. Treat these as secondary claims.
- Exact current versions for Loro (beyond 1.6.0), c2pa-rs (library, not tool), Yjs, A2A SDK, x402 SDKs, and WebLLM were not verified this session.
- I found no 2026 status for Turmoil, and no primary CaMeL paper surfaced in this search (I believe it is a 2025 Google DeepMind paper, but did not verify).
- Other well-known projects were not researched this session and need verification before use: ElectricSQL, Zero (Rocicorp), Jazz, LiveStore, Triplit (local-first); OpenDP/Flower (DP/federated learning); Kokoro TTS; Chrome built-in AI APIs; Sarvam model specifics; Beckn/ONDC protocol.
- Federated learning and differential privacy 2026 developments were not covered by any search.

## Which have strong open-source building blocks right now (libraries/versions)?

### Takeaway
The building blocks are most mature (production-grade, documented) for CRDTs (Automerge 3.3.x/Rust 0.10, Loro 1.x, Yjs), in-browser inference (Transformers.js v4, WebLLM), MCP SDKs (2026-07-28), C2PA (c2patool 0.26.x), and Pipecat. Mid-maturity: zkVMs (SP1/RISC Zero are production; Jolt is alpha), VC verifiers (walt.id, SpruceID, OpenEUDI, Inji Certify v0.12.x dev release), and TFHE-rs 1.6 (mature library, slow without a GPU). Immature or research-grade: Keyhive/Subduction, world models, and the LLM-proof frameworks.

### Cited Findings
| Vector | Building blocks (version) | Maturity | Source |
|---|---|---|---|
| Local-first | Automerge v3.3.2, crate 0.10.0, automerge-repo 2.6.0-alpha, Hexane v1, Keyhive (WIP), Subduction + iroh 1.0 | Core mature; E2EE/sync new | [Automerge Jul 2026](https://automerge.org/blog/2026-july/) |
| Local-first | Loro 1.6.0 (Rust/JS/Swift) | Mature, large WASM | [docs.rs](https://docs.rs/crate/loro/1.6.0); [README](https://www.github.com/loro-dev/loro) |
| Browser AI | Transformers.js v4 (preview Feb 2026), WebLLM | Mature | [HF](https://huggingface.co/blog/transformersjs-v4); [arXiv](https://arxiv.org/html/2412.15803v1) |
| Agents | MCP spec 2026-07-28 + SDKs; Tasks extension | Mature, spec just changed | [MCP blog](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) |
| Agent payments | x402 (+ A2A x402 extension), AP2 (v0.2 reported) | Early-production | [Coinbase](https://www.coinbase.com/developer-platform/discover/launches/google_x402); [Splunk](https://www.splunk.com/en_us/blog/artificial-intelligence/the-agent-economy-when-ais-pay-each-other.html) |
| Identity | OIDF OpenID4VP 1.0/HAIP conformance tests; walt.id, SpruceID, OpenEUDI, Tessio 0.2.1; MOSIP Inji Certify v0.12.2 | Mid | [OIDF](https://openid.net/openid-foundation-launches-eudiw-resource-hub/); [Tessio](https://www.nuget.org/packages/Tessio.Verifier.OpenId4Vp/); [IDTechWire](https://idtechwire.com/mosip-releases-inji-certify-developer-tool-for-verifiable-credential-issuance/) |
| ZK | SP1, RISC Zero, OpenVM (audited); Jolt alpha | Prod for ETH; heavy compute | [wavect](https://wavect.io/blog/zero-knowledge-proofs-production-2026.md) |
| Provenance | c2patool 0.26.59 / c2pa-rs; spec 2.3–2.4 | Mature | [changelog](https://opensource.contentauthenticity.org/docs/c2patool/docs/changelog/) |
| DST | TigerBeetle VOPR (reference), Turmoil (experimental), Antithesis (commercial) | Pattern mature, libs thin | [TigerBeetle](https://tigerbeetle.com/blog/2026-08-20-protocol-aware-dst); [Eaton](https://notes.eatonphil.com/2024-08-20-deterministic-simulation-testing.html) |
| FHE | TFHE-rs 1.6.1 (CUDA on Linux) | Mature lib, perf-bound | [docs.rs](https://docs.rs/crate/tfhe/1.6.1/source/src/core_crypto/gpu/mod.rs) |
| Voice / Indic | Pipecat (v1.6.0 line), Kyutai Moshi 7B, Pocket TTS, IndicConformer-600M (MIT) | Mature | [Pipecat](https://github.com/pipecat-ai/pipecat); [HF](https://huggingface.co/ai4bharat/indic-conformer-600m-multilingual) |
| Formal verif. | Lean 4, Dafny, Verus (industrial use at Microsoft/AWS), Goedel-Code-Prover | Tools mature, LLM success rates modest | [arXiv 2603.19329](https://arxiv.org/pdf/2603.19329) |
| Sandboxing | E2B (Firecracker microVMs) | Mature | [Taskade](https://www.taskade.com/blog/ai-agent-sandboxes.md) |
| World models | Matrix-Game 2/3, Waypoint-1, LingBot-World | Research; GPU-heavy | [eWeek](https://www.eweek.com/news/google-tencent-open-source-ai-video/); [HF](https://huggingface.co/blog/waypoint-1) |

### Inferences
- For a 24–48h build, pick blocks marked "Mature" as the backbone and at most one "new/WIP" component as the novelty hook, so that churning APIs (e.g., Keyhive, the MCP extension migration) don't sink the demo.
- Mixing JS/TS blocks (Automerge, Transformers.js, MCP TS SDK, c2pa-js, OpenEUDI) keeps everything in-browser and deployable to a single static host, which reduces demo risk.

### Gaps
- Exact npm versions of the MCP TypeScript SDK, A2A SDK, x402 packages, WebLLM, Yjs, and c2pa-js were not confirmed.
- GPU availability at the venue is unknown. ZK, FHE, and world-model feasibility depend on it.

## Which make the most dramatic live demo plus the deepest system-design explanation?

### Takeaway
The best demo-to-depth ratios are: (1) local-first E2EE collaboration that survives airplane mode and merges on reconnect; (2) an agent economy with paid tools and signed mandates (MCP + A2A + x402/AP2); (3) an offline verifiable-credential wallet with selective disclosure, fitting DPI/India Stack framing; (4) a deterministic-simulation "bug time machine"; and (5) live prompt-injection red-teaming against a capability-gated agent. Voice-first Indic AI is a strong emotional overlay for any of these.

### Cited Findings
- Local-first: merges, no-server relays, and E2EE group keys (Keyhive/BeeKEM) are all new as of 2026 — [Automerge Jul 2026](https://automerge.org/blog/2026-july/)
- Agent payments: x402 has industry-wide governance (x402 Foundation) and A2A AgentCards give an interop story — [Splunk](https://www.splunk.com/en_us/blog/artificial-intelligence/the-agent-economy-when-ais-pay-each-other.html). The non-blocking elicitation and Tasks design gives a concrete async-protocol story — [AAIF](https://aaif.io/blog/handle-long-running-mcp-tool-calls-with-the-mcp-tasks-extension)
- VC wallets: the Dec 2026 EUDI deadline makes this timely — [OpenEUDI](https://dev.to/eidas-pro/introducing-openeudi-an-open-source-sdk-for-eu-digital-identity-verification-1eae). MOSIP Inji is a DPI public good spanning many credential types — [Biometric Update](https://www.biometricupdate.com/202609/mosip-broadens-inji-beyond-digital-id-into-open-credentialing-platform)
- DST: seed-reproducible bugs plus per-replica invariants make a visually replayable failure — [TigerBeetle](https://tigerbeetle.com/blog/2026-08-20-protocol-aware-dst)
- Injection defence: sandboxes contain but don't prevent injection, while capability gating provides deterministic enforcement — [Augment](https://www.augmentcode.com/guides/agent-execution-sandbox); [arXiv 2606.00288](https://arxiv.org/pdf/2606.00288)
- In-browser AI: offline WASM caching enables a "pull the network cable" demo — [HF Transformers.js v4](https://huggingface.co/blog/transformersjs-v4)

### Inferences
- Ranked by demo drama × system-design depth × 48h feasibility:
  1. Offline-first DPI super-app: SD-JWT credential + Automerge sync + in-browser Indic voice. Rated A/B.
  2. Agent marketplace with x402/AP2 + MCP Tasks + a sandbox. Rated A.
  3. DST harness visualizer for a toy Raft or CRDT system. Rated A/B.
  4. C2PA provenance chain for AI-generated media. Rated A.
  5. LLM-to-Lean/Dafny verified-code loop. Rated B.
  6. zkVM-backed "proof of eligibility" without revealing data. Rated B.
  7. FHE private tally. Rated B/C.
  8. World model. Rated C.
- Judges in system-design contexts likely reward explicit trade-off explanations: CAP/consistency model (strong eventual consistency vs consensus), threat model (holder binding, replay, injection), and failure injection shown live. DST and local-first ideas make these visible.
- Honest maturity labelling (e.g., "secure element simulated", "testnet stablecoin", "mock prover") is advisable and avoids credibility loss.

### Gaps
- No source evaluates which tech categories win hackathons, so the rankings above are inference.
- The CIMFEST judging criteria and theme were not available in this research scope.
