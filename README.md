# ECDSA Nonce-Analysis Proof-of-Concept (English README)

**Purpose:**  
This repository is a *proof-of-concept* (PoC) demonstrating techniques used in research to analyze ECDSA nonce (`k`) generation and to illustrate the risks that weak, biased, or otherwise predictable nonces pose to private keys. It combines simple probabilistic modelling, a basic LSTM (TensorFlow/Keras) predictor, and a brute-force search strategy — **for research, testing and defensive purposes only**.

---

## Overview

This project contains a small Python PoC that:

- Loads a list of example ECDSA signatures `(r, s, z)` (where `z` is the message/hash).
- Maintains a history of observed or guessed nonces (`k`) and uses:
  - a simple probabilistic model (fit to observed `k` values) to sample candidate nonces, and
  - a basic LSTM model to try to predict probable future `k` values from past ones.
- Combines model-generated candidate nonces with a brute-force strategy to attempt private key recovery in controlled test scenarios.
- Logs results and demonstrates common failure modes and mitigations.

---

## Intended use & ethics

This repository is intended for:
- Academic research, security assessments and demonstrations.
- Defensive testing of implementations that rely on ECDSA (to ensure nonce generation is robust).
- Education about why deterministic or poorly randomized nonces are dangerous.

**Do not** use this code against systems or keys that you do not own or do not have explicit permission to test. Attempting to recover private keys without authorization is illegal and unethical.

---

## High-level design (non-actionable)

1. **Data input**  
   The PoC uses a small static list of example signatures (`r`, `s`, `z`) to simulate observed signatures in a testing environment.

2. **Nonce history**  
   A collection of previously-observed or randomly-guessed nonces (`historical_k`) is kept. This is used only to build toy statistical models for demonstration.

3. **Probabilistic model**  
   A simple statistical fit (e.g., Gaussian fit to observed `k` values) is used to sample candidate nonces. This highlights how biased distributions can leak information.

4. **Sequence model (LSTM)**  
   A minimal LSTM network is trained on sequences of observed `k` values to show how temporal correlations in nonce generation could be learned by a sequence model.

5. **Brute-force / verification**  
   Candidate `k` values are combined with signature parameters in a verification loop to check whether a candidate yields the correct `s` value and thus reveals a private key — this is strictly a controlled test to validate the models’ outputs.

6. **Logging & analysis**  
   The PoC prints status messages, stores recovered keys (if any) in a local file, and reports possible reasons for failure (e.g., RFC 6979 deterministic nonces, insufficient samples, or truly uniform randomness).

---

## Limitations & responsible disclosure

- This PoC is intentionally simplistic. Real-world key recovery is substantially harder when proper nonce generation (e.g., RFC 6979 or CSPRNGs) is used.
- The LSTM and probabilistic components are minimal and meant for demonstration, not production-grade attack tooling.
- If you discover a vulnerability in a third-party implementation, follow responsible disclosure and contact the software/project maintainers instead of exploiting it.

---

## Security recommendations (defensive)

- Use deterministic nonces per RFC 6979 or a cryptographically secure random number generator (CSPRNG) with sufficient entropy.
- Avoid reusing nonces and resist implementations that derive nonces from low-entropy or predictable sources (time, weak RNGs, user input).
- Implement thorough test coverage for cryptographic primitives and perform periodic entropy checks in environments where keys are generated or used.

---

## Files & structure

- `poC_script.py` — the example PoC script (toy dataset + models + brute-force loop).
- `found_keys.txt` — output file where recovered keys (in test runs) are appended.
- `README.md` — this document.

---

## License

Use, modification and redistribution of this PoC are permitted under the repository's license (see `LICENSE`). If no license is present, assume "All rights reserved" and request permission from the repository owner before reusing.

---

## Final notes

This project exists to educate and to encourage stronger cryptographic practices. The examples are intentionally small and designed for controlled laboratory experiments only. If your goal is to improve security, use this PoC as a learning tool and a starting point for defensive testing — not for misuse.

BTC donation address: bc1q4nyq7kr4nwq6zw35pg0zl0k9jmdmtmadlfvqhr
