# Evasion via Tactical DoS

Evasion via tactical Denial of Service (DoS) involves overwhelming network security solutions to reduce their effectiveness. Common tactics include:

- **Launching DoS attacks against IDS/IPS:** Overloading Intrusion Detection/Prevention Systems with excessive traffic.
- **Targeting logging servers:** Flooding logging infrastructure to disrupt event recording and analysis.

## Techniques

- **Overload IDS/IPS processing:** Generate large volumes of benign traffic to exhaust the processing capacity of IDS/IPS devices, especially as their rule sets and network traffic increase.
- **Flood logging servers:** Send massive amounts of non-malicious traffic that still triggers logging, congesting communication channels or exceeding disk capacity.
- **Operator fatigue:** Cause numerous false positives to overwhelm IDS operators, increasing the chance that real threats are overlooked.

> **Note:** These tactics aim to degrade the effectiveness of network security monitoring and response by exploiting resource limitations and human factors.