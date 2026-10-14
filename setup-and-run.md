# ⚙️ Setup & Run Notes — FRESCO Two-Party Computation

## 1. Prerequisites

- Java 8 (JDK)
- Apache Maven
- Git
- Optional: Docker

## 2. Build FRESCO from source

```bash
git clone https://github.com/aicis/fresco.git
cd fresco
mvn install                 # build all modules and run the test suite (takes several minutes)
mvn install -DskipTests     # faster build, skips tests
```

**Verification:** Maven places a JAR in each module's `./target` directory, and the terminal shows `BUILD SUCCESS`.

## 3. Docker alternative

```bash
docker pull aicis/fresco
docker run -it aicis/fresco
```

## 4. Add FRESCO as a dependency

```xml
<dependency>
  <groupId>dk.alexandra.fresco</groupId>
  <artifactId>core</artifactId>
  <version>1.3.0</version>
</dependency>
```

## 5. Implement the application

- Create a Java class implementing FRESCO's `Application` interface.
- Party 1 **closes** (secret-shares) its input; Party 2 **closes** its input.
- Add the two closed values.
- **Open** (reveal) only the final result to both parties.

## 6. Configure the protocol

- Create a `SecureComputationEngine` with a `TinyTablesProtocolSuite`.
- Point it at two localhost endpoints on different ports, one per party.
- To switch to malicious security, replace the protocol suite with SPDZ — a one-line change; the application code stays the same.

## 7. Run both parties

Open two terminals and run the same JAR with different party IDs:

```bash
# Terminal 1 — Party 1, private input 10
# Terminal 2 — Party 2, private input 15
```

**Output in both terminals:** `25`. Neither party saw the other's value; only secret shares crossed the network.
