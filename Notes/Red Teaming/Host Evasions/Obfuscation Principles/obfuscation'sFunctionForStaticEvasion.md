## Obfuscation's Function for Static Evasion

Two of the more considerable security boundaries for adversaries are **anti-virus engines** and **EDR (Endpoint Detection & Response)** solutions. As covered in the *Introduction to Anti-virus* room, both platforms leverage an extensive database of known signatures (static signatures) as well as heuristic signatures that consider application behavior.

To evade these signatures, adversaries use a wide range of logic and syntax rules to implement obfuscation. This is commonly achieved by abusing data obfuscation practices that hide important identifiable information in otherwise legitimate applications.

The white paper **Layered Obfuscation Taxonomy** summarizes these practices well under the *code-element layer*. Below is a summary of methods covered by the taxonomy in the *obfuscating data* sub-layer.

> **Diagram:** Objectives of the Obfuscating Data Sub-Layer  
> - Array Transformation  
> - Data Encoding  
> - Data Procedurization  
> - Data Splitting/Merging

| Obfuscation Method      | Purpose                                                        |
|------------------------ |---------------------------------------------------------------|
| Array Transformation    | Transforms an array by splitting, merging, folding, and flattening |
| Data Encoding           | Encodes data with mathematical functions or ciphers           |
| Data Procedurization    | Substitutes static data with procedure calls                  |
| Data Splitting/Merging  | Distributes information of one variable into several new variables |

In the upcoming tasks, we will primarily focus on **data splitting/merging**. Because static signatures are weaker, we generally only need to focus on that one aspect in initial obfuscation.

For more information:
- See the **Encoding/Packing/Binder/Crypters** room for data encoding.
- See the **Signature Evasion** room for data procedurization and transformation.