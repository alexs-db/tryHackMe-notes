## Classification of Network Traffic

Network traffic can be classified into two main categories:

- **Benign traffic:** The usual, expected traffic that should not trigger IDS alerts.
- **Malicious traffic:** Abnormal or unexpected traffic that the IDS should detect.

Similarly, host activity can also be classified in this way. An IDS detection engine is designed to either detect malicious activity or recognize normal activity. By understanding what is "normal," the IDS can easily identify deviations.

## IDS Detection Engine Types

The detection engine of an IDS can be:

- **Signature-based:**  
        - Requires detailed knowledge of malicious (or unwanted) traffic.
        - Uses explicit rules or signatures to match against known malicious patterns.
        - Commonly used in anti-virus software, which relies on a database of known virus signatures. Anything matching a signature is flagged as malicious.

- **Anomaly-based:**  
        - Requires knowledge of what regular (normal) traffic looks like.
        - The IDS is "taught" what normal is, either through machine learning or manual rules, to establish a baseline.
        - Anything deviating from this baseline is considered potentially malicious.
        - This approach is similar to human perception: you notice when something (like your web browser) behaves abnormally compared to your expectations.

In summary, a signature-based IDS detects known malicious activity, while an anomaly-based IDS detects deviations from normal behavior.