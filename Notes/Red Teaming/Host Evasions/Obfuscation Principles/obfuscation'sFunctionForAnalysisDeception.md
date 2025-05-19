After obfuscating the basic functions of malicious code, it may evade software detections but remains vulnerable to human analysis. While not a security boundary without additional policies, analysts and reverse engineers can still gain deep insight into the functionality of a malicious application and potentially halt its operations.

Adversaries can use advanced logic and mathematics to create more complex and harder-to-understand code, making analysis and reverse engineering more challenging.

> For more information about reverse engineering, see the **Malware Analysis** module.

---

### Obfuscation Methods

The white paper **Layered Obfuscation Taxonomy** summarizes these practices under the code-element layer, specifically the *obfuscating layout* and *obfuscating controls* sub-layers.

Below is a table of common obfuscation methods and their purposes:

| Obfuscation Method         | Purpose                                                                 |
|---------------------------|-------------------------------------------------------------------------|
| Junk Code                 | Add non-functional instructions (code stubs)                            |
| Separation of Related Code| Separate related instructions to increase program complexity             |
| Stripping Redundant Symbols| Remove symbolic information such as debug info or symbol tables         |
| Meaningless Identifiers   | Rename meaningful identifiers to meaningless ones                        |
| Implicit Controls         | Convert explicit control instructions to implicit ones                   |
| Dispatcher-based Controls | Determine the next block to execute at runtime                          |
| Probabilistic Control Flows| Replicate control flows with the same semantics but different syntax    |
| Bogus Control Flows       | Add control flows that are never executed                               |

*Diagram: Objectives of the Obfuscating Layer sub-layer include adding junk code, separating related code, stripping redundant symbols, and using meaningless identifiers.*

---

In the upcoming tasks, several of these methods will be demonstrated in an agnostic format.

> For more information about anti-analysis and anti-reversing, check out the **Sandbox Evasion** room.
