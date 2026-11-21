# SVARAGUARD: Voice-Only Affect Intelligence for Debt Collections

**Team:** PRAKAMYA (ID TM-3ECDDC28793) | **Problem 1:** Paralinguistic State Detection from Voice, Not Transcript

**Central innovation:** uncertainty-aware, telephony-robust acoustic intelligence with structurally enforced borrower protection.

## 1. Problem and Domain

We address Problem 1: estimating vocal-affective state from speech alone. A borrower in hardship must reach a human, never a bot's pressure; transcripts record words, not how they are said. "I'll pay next week" can express different vocal states with identical words.

## 2. Understanding of the Challenge

Affect must be separated from content, speaker and channel. Encoders can carry lexical information; reshuffling public corpora by transcript clusters changes reported results (Pesan et al., 2024). Accent, age, handset and noisy narrowband audio can mimic affect, so distress is scored against each speaker's session baseline, not absolute pitch. The system must estimate arousal and valence from sound, state uncertainty, and never let a score trigger pressure.

## 3. Proposed Approach and Architecture

A workflow orchestrator passes each 2-second window through five specialist components.

1. **Channel assessment:** estimates bandwidth, signal-to-noise ratio and degradation; severe cases go straight to abstention.
2. **Affect estimation:** outputs continuous arousal and valence (each −1 to +1) and probabilities for seven states: calm, stressed, angry, evasive, distressed, resigned, cooperative. A causal GRU reads frame-level eGeMAPS descriptors (Eyben et al., 2016); WavLM-Base or HuBERT-Base replaces it only if p95 CPU latency is under 500 ms. A gradient-reversal lexical adversary (Ganin et al., 2016) in training discourages word-dependent representations; inference is transcript-free. Normalisation is population-based at first, session-local thereafter, frozen after any distress flag; no persistent voice profile is kept.
3. **Uncertainty assessment:** temperature scaling (Guo et al., 2017) calibrates discrete-state probabilities; arousal and valence use ensemble prediction intervals. A risk–coverage curve on a calibration split sets abstention.
4. **Policy enforcement:** the gate maps states to four actions: continue, slow down and empathise, offer a policy-approved restructuring option, or hand off to a human.
5. **Handoff and audit:** a window whose calibrated distress probability exceeds the pre-registered threshold latches protection at once; later calm windows cannot clear it. Only authorised human resolution clears it. Unacknowledged handoff escalates to a supervisor. If the gate fails, collection pressure fails closed.

A turn's state is the confidence-weighted vote of non-abstained windows, never overriding a latch; an all-abstained turn goes to human review. Training simulates telephone audio (8 kHz, 300–3,400 Hz, G.711 or AMR-NB, background noise); runtime uses audio as received.

The Acoustic Commitment-Consistency Indicator (0–1) measures arousal, valence and prosody consistency across each timestamped promise-to-pay segment. It is exploratory; it cannot trigger pressure, affect eligibility or establish honesty.

Labels: CREMA-D (Cao et al., 2014) gives categorical labels, mapped provisionally (anger→angry; neutral→calm proxy; fear→stressed only if supported; others merged). Windows need aligned arousal and valence labels from a consented role-play corpus, rated per window by two annotators. MSP-Podcast (Lotfian and Busso, 2019) gives turn-level supervision under academic terms.

## 4. Minimum Viable Scope

Mandatory: (a) file or microphone input producing a time-resolved arousal and valence track and a discrete state per turn, at least once per second, under 500 ms per update on a standard laptop CPU; (b) distress alert with human handoff and the four-action decision layer; (c) CCC and UAR on speaker-independent held-out data, a content-invariance test and a fairness breakdown.

Stretch: commitment-consistency validation against a transcript-only baseline on marked proxy outcomes.

## 5. Evaluation Plan

Discrete states use speaker-independent CREMA-D folds (7,442 clips, 91 actors). Self-recorded English and Hindi role-played calls are scored clean and after 8 kHz narrowband simulation. Development and test sets are speaker-disjoint; the test set is never used for tuning.

Metrics: CCC and ICC between raters (arousal, valence); UAR and Krippendorff's alpha (states); distress recall at the pre-registered false-alarm rate; post-flag violation rate (target zero), reported separately from missed-distress rate; interval coverage and width; p50 and p95 latency. Report group sizes and bootstrap 95% intervals.

Content invariance: on identical-transcript pairs, a text-only model should be at chance while the acoustic model discriminates above chance (pairwise ranking accuracy). A Whisper probe follows. Baselines: eGeMAPS with gradient boosting or SVR; a text-only classifier. Ablations: adversary, codec augmentation, abstention, encoder. Fairness: CCC, UAR and distress recall by gender, age band and region or language; inconclusive if subgroups are small.

Falsified if matched-pair discrimination is not above chance or the text-only baseline.

## 6. Novelty and Differentiation

Existing voice-emotion and contact-centre analytics mostly score transcripts or classify acoustics; to our knowledge, few combine lexical-invariance tests, telephony-aware abstention and a latched protection gate.

## 7. Feasibility and Team

Aditya Kalagatoori (team leader): signal engineering, telephony, latency and the gate. Thakur Aswin Singh: machine learning, encoder, fairness audit. Preenithi: data, consent, annotation, matched-text set.

## 8. Risks and Limitations

Acted corpora overstate affect; MSP-Podcast is turn-level and not telephone-band; no licensed Indian-language dimensional corpus is known; 8 kHz labels are scarce; licences and latency are open. Mitigations: eGeMAPS fallback, abstention, counsel review.

## 9. Responsible Design and Expected Impact

The gate blocks collection pressure, never escalation to a human. Tests cover latching, overrides, conflicting predictions, handoff failure and audit logging. Consent is specific and withdrawable; recordings are kept for a disclosed period and access is role-restricted. Data is never used for credit decisions. The brief treats emotion inference as sensitive; counsel confirms DPDP and RBI obligations.

Expected impact: fewer distressed borrowers reach pressure, measured by post-flag violation (target zero) and missed-distress rate.
