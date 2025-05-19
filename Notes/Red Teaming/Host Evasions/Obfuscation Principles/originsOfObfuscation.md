# Origins of Obfuscation

Obfuscation is widely used in many software-related fields to protect intellectual property (IP) and other proprietary information that an application may contain.

For example, the popular game **Minecraft** uses the obfuscator **ProGuard** to obfuscate and minimize its Java classes. Minecraft also releases obfuscation maps with limited information as a translator between the original unobfuscated classes and the new obfuscated classes to support the modding community.

This is just one example of the many ways obfuscation is publicly used. To document and organize the variety of obfuscation methods, we can reference the research paper _Layered Obfuscation: A Taxonomy of Software Obfuscation Techniques for Layered Security_. This paper organizes obfuscation methods by layers, similar to the OSI model but for application data flow. Below is an overview of each taxonomy layer:

> **Diagram:** Four layers of the layered obfuscation taxonomy:  
> - Code-Element Layer  
> - Software-Component Layer  
> - Inter-Component Layer  
> - Application Layer

Each sub-layer is further broken down into specific methods that achieve the overall objective of the sub-layer.

In this room, we will primarily focus on the **Code-Element Layer** of the taxonomy, as shown below:

> **Diagram:** Sub-layers of the Code-Element Layer:  
> - Obfuscating Layout  
> - Obfuscating Controls  
> - Obfuscating Data  
> - Obfuscating Methods  
> - Obfuscating Classes

To use the taxonomy, we can determine an objective and then select a method that fits our requirements. For example, suppose we want to obfuscate the layout of our code but cannot modify the existing code. In that case, we can inject junk code, as summarized by the taxonomy:

```
Code Element Layer > Obfuscating Layout > Junk Codes
```

But how could this be used maliciously? Adversaries and malware developers can leverage obfuscation to break signatures or prevent program analysis. In the upcoming tasks, we will discuss both perspectives of malware obfuscation, including the purpose and underlying techniques of each.