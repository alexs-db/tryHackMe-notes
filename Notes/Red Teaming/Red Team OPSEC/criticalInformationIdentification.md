# Critical Information in a Red Team Operation

What a red teamer considers critical information worth protecting depends on the operation and the assets or tooling used. In this setting, critical information includes, but is not limited to, the red team’s intentions, capabilities, activities, and limitations. Critical information includes any information that, once obtained by the blue team, would hinder or degrade the red team’s mission.

To identify critical information, the red team needs to use an adversarial approach and ask themselves what information an adversary, the blue team in this case, would want to know about the mission. If obtained, the adversary will be in a solid position to thwart the red team’s attacks. Therefore, critical information is not necessarily sensitive information; however, it is any information that might jeopardize your plans if leaked to an adversary. 

## Examples of Critical Information

- **Client information that your team has learned**  
  It's unacceptable to share client-specific information such as employee names, roles, and infrastructure that your team has discovered. Sharing this type of information should be kept on a need-to-know basis as it could compromise the integrity of the operation.  
  The **Principle of Least Privilege (PoLP)** dictates that any entity (user or process) must be able to access only the information necessary to carry out its task. PoLP should be applied in every step taken by the Red Team.

- **Red team information**  
  This includes identities, activities, plans, capabilities, and limitations. The adversary can use such information to be better prepared to face your attacks.

- **Tactics, Techniques, and Procedures (TTP)**  
  The TTPs that your team uses to emulate an attack should remain confidential.

- **Operating System, Cloud Hosting Provider, or C2 Framework used by the Red Team**  
  For example, if your team uses **Pentoo** for penetration testing and the defender knows this, they can monitor logs exposing the OS as Pentoo. This could lead to detection and mitigation.

- **Public IP addresses that your red team will use**  
  If the blue team gains access to this information, they could quickly mitigate the attack by blocking all inbound and outbound traffic to your IP addresses.

- **Domain names that your team has registered**  
  Domain names play a significant role in attacks such as phishing. If the blue team identifies the domain names you plan to use, they could block or sinkhole them to neutralize your attack.

- **Hosted websites, such as phishing websites, for adversary emulation**  
  Any websites hosted to support the attack should be protected from premature exposure.

Protecting this information is essential to maintaining the effectiveness of a red team operation and ensuring realistic attack emulation.