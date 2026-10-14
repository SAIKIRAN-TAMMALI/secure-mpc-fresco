# 🔐 Secure Multi-Party Computation with FRESCO — SPDZ · TinyTables · Secret Sharing

[![Java](https://img.shields.io/badge/Java_8-ED8B00?style=flat-square&logo=openjdk&logoColor=white)](https://openjdk.org) [![Maven](https://img.shields.io/badge/Apache_Maven-C71A36?style=flat-square&logo=apachemaven&logoColor=white)](https://maven.apache.org) [![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com) [![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)](https://git-scm.com) [![FRESCO](https://img.shields.io/badge/FRESCO-MPC_Framework-1F3A6E?style=flat-square)](https://github.com/aicis/fresco)

> **Hands-on implementation of a two-party secure computation: two parties jointly compute a result while neither one ever sees the other's private input.**
> Completed as a cryptography course project at **Stevens Institute of Technology**.

---

## 📌 Disclaimer

All work was performed on my own machine for academic and portfolio purposes. The inputs used are simple test values, not real data. FRESCO is an open-source framework (MIT license) developed by the Alexandra Institute; this repository documents my implementation and evaluation of it.

---

## 🎯 Objective & Scenario

Organizations often need a combined answer from data that no single party is allowed to share — for example, aggregating security telemetry across teams or sharing threat intelligence between companies without exposing raw records.

**My goal:** build the FRESCO framework from source, implement a working two-party computation, run it under different security models, and evaluate the framework's strengths and limits against similar tools.

---

## 🧭 Scope

| Item | Detail |
|------|--------|
| **Framework** | FRESCO (Framework for Efficient and Secure Computation), Java |
| **Computation** | Private sum of two secret integers held by two separate parties |
| **Protocols tested** | TinyTables (semi-honest) and SPDZ (malicious-secure) |
| **Setup** | Two parties simulated as two processes on localhost, on different ports |
| **Deliverables** | Working application, protocol comparison, framework evaluation report |

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| **Java 8 (JDK)** | Runtime required by FRESCO |
| **Apache Maven** | Building and installing FRESCO modules from source |
| **Git** | Cloning the FRESCO repository |
| **Docker** *(optional)* | Containerized alternative setup |
| **Two terminal sessions** | Running Party 1 and Party 2 independently |

---

## 🗺️ Methodology

1. **Research** — studied the MPC model, additive secret sharing, and the SPDZ and TinyTables protocols.
2. **Build** — cloned FRESCO and built every module from source with Maven.
3. **Implement** — wrote a Java application defining the computation (what to compute), leaving the cryptography (how to compute it securely) to the framework.
4. **Run & verify** — executed both parties and confirmed the correct result with no raw input exposed.
5. **Evaluate** — switched protocol suites, documented pros and cons, and compared FRESCO with JIFF and MP-SPDZ.

---

## 🧠 Core Concept — Additive Secret Sharing

A secret value `s` is split into `n` random shares so that:

```
s1 + s2 + ... + sn = s (mod p)
```

No single share reveals anything about `s`. Only when the shares are combined can the secret be reconstructed. In FRESCO, a developer marks values as **closed** (secret-shared) or **opened** (revealed), and the framework handles the shares and network messages.

| Protocol | Security Model | Adversary Type | Best For |
|----------|----------------|----------------|----------|
| **SPDZ** | Information-theoretic | Malicious (active) | High-security use; uses an offline phase to precompute Beaver triples |
| **TinyTables** | Computational | Semi-honest (passive) | Fast prototyping, low overhead |

---

## ⚙️ Lab Setup & Commands

> Full command notes live in [`methodology/setup-and-run.md`](methodology/setup-and-run.md). Summary below.

**Build FRESCO from source**
```bash
git clone https://github.com/aicis/fresco.git
cd fresco
mvn install                 # build all modules and run the test suite
mvn install -DskipTests     # faster build without tests
```

**Docker alternative**
```bash
docker pull aicis/fresco
docker run -it aicis/fresco
```

**Add FRESCO to a project**
```xml
<dependency>
  <groupId>dk.alexandra.fresco</groupId>
  <artifactId>core</artifactId>
  <version>1.3.0</version>
</dependency>
```

---

## 🚀 Implementation — Two-Party Private Sum

| Step | What I did |
|------|------------|
| **1. Define the application** | Created a Java class implementing FRESCO's `Application` interface. Party 1 closes (secret-shares) its value, Party 2 closes its value, the two are added, and the result is opened to both parties. |
| **2. Choose the protocol** | Configured the `SecureComputationEngine` with a `TinyTablesProtocolSuite`, pointing at two localhost endpoints on different ports. |
| **3. Run both parties** | Ran the same JAR in two terminals with party IDs 1 and 2. |

**Result**

| Party 1 input | Party 2 input | Output (both parties) | Raw inputs exposed? |
|:---:|:---:|:---:|:---:|
| 10 | 15 | **25** | ❌ No — only secret shares crossed the network |

**Protocol swap:** I later replaced TinyTables with SPDZ by changing **one line of configuration**. The application code did not change, confirming that FRESCO's protocol abstraction works as documented.

---

## 🔍 Evaluation — What I Observed

| Pros | Cons |
|------|------|
| Java + Maven setup was familiar and fast to integrate | Requires Java 8 specifically |
| Supports both semi-honest and malicious adversary models | Additive secret sharing only; full threshold schemes not yet implemented |
| Protocol backend can be swapped without rewriting the application | Steeper learning curve than JavaScript tools like JIFF |
| Add-on libraries: fresco-stat, fresco-dp, fresco-ml | Smaller community than MP-SPDZ; fewer examples |
| MIT licensed; Docker support | Full Maven test suite takes several minutes |
| Preprocessing and parallelization for large computations | Online performance depends on offline preprocessing quality |

**Troubleshooting:** when I hit a network configuration issue with few online examples available, I resolved it by reading the FRESCO source code directly.

---

## ⚖️ Comparison with Similar Frameworks

| Feature | FRESCO | JIFF | MP-SPDZ |
|---------|--------|------|---------|
| Language | Java | JavaScript | C++ / Python DSL |
| Security model | Semi-honest + Malicious | Semi-honest only | Semi-honest + Malicious |
| Ease of install | Medium (Maven) | Easy (npm) | Hard (C++ build) |
| Performance | Good | Moderate | Best |
| Documentation | Good | Good | Excellent |

---

## 🧠 Key Takeaways

1. **Data can be useful without being shared.** MPC let two parties compute a joint answer while each input stayed private end to end.
2. **Security model is a deliberate choice.** TinyTables is fast but trusts parties to follow the protocol; SPDZ defends against parties that actively cheat, at a higher cost.
3. **Good abstraction matters.** Separating *what* to compute from *how* to compute it securely let me change the cryptographic protocol with a one-line change.
4. **Practical friction is real.** Build time, a pinned Java version, and dense documentation are the main costs of adopting the framework.

---

## 📂 Repository Structure

```
secure-mpc-fresco/
├── README.md                     ← this file
├── reports/
│   └── fresco-mpc-report.pdf     ← full report (protocols, setup, implementation, evaluation)
└── methodology/
    └── setup-and-run.md          ← build steps and run commands
```

---

## 📚 References

- FRESCO GitHub Repository — https://github.com/aicis/fresco
- FRESCO Documentation — https://fresco.readthedocs.io
- awesome-mpc project list — https://github.com/rdragos/awesome-mpc
- Damgård et al. (2012), *SPDZ*, CRYPTO
- Damgård et al. (2016), *TinyTables*, EUROCRYPT

---

## 👤 Author

**Saikiran Tammali** — Cybersecurity Graduate · Cryptography · Security Engineering

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/saikiran-tammali-cybersec32)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/SAIKIRAN-TAMMALI)

*Cryptography course project — M.S. Cybersecurity, Stevens Institute of Technology.*
