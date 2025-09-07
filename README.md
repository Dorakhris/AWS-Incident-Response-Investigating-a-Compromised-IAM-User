# AWS Incident Response: Investigating a Compromised IAM User



## Case Summary
- **Objective:** To design and execute a realistic cloud incident response exercise in AWS. I simulated an attack using a compromised IAM user, then switched roles to act as the responding analyst, using AWS CloudTrail to detect, analyze, and remediate the threat.
- **Scope:** The exercise was conducted within a live AWS account, focusing on the interactions between AWS IAM (Identity and Access Management), Amazon S3 (Simple Storage Service), and AWS CloudTrail logging.
- **Tools Used:** AWS IAM, AWS S3, AWS CloudTrail.
- **Outcome:** I successfully simulated the attack, then used CloudTrail's Event History to trace the attacker's TTPs from initial login to S3 bucket enumeration. I documented a full incident timeline and executed the remediation steps, including deleting the compromised user.



## Tools & Environment
| Tool | Purpose |
| :--- | :--- |
| **AWS CloudTrail** | The primary tool for investigation, used to provide a detailed audit trail of all API calls. |
| **AWS IAM** | Used to create the compromised user for the attack simulation and later to manage and delete the threat. |
| **AWS S3** | The target environment, hosting the sensitive data that the simulated attacker aimed to access. |
| **OS/VM Used** | AWS Management Console. |



## Case Background
To demonstrate and hone my cloud incident response skills, I designed an end-to-end security exercise within my personal AWS environment. I took on two roles: first, as an attacker who has gained access to an IAM user's credentials, and second, as the SOC analyst tasked with investigating the resulting security alert. The scenario was based on a common threat: a compromised user account (`KeyHunter`) being used to discover and access sensitive data stored in an S3 bucket



## Methodology
My investigation followed a structured, multi-phase process that mirrored a real-world incident.

1.  **Attack Simulation (Red Team Phase):** I began by acting as the adversary. I created a new IAM user named `KeyHunter` and used its credentials to perform a `ConsoleLogin`. From there, I executed a series of discovery actions, most notably `ListBuckets`, to enumerate all S3 buckets in the account and identify `dora-cloudbucket` as a high-value target.
2.  **Detection & Analysis (Blue Team Phase):** Switching to my defender role, I started my investigation in **AWS CloudTrail**. I filtered the "Event history" for the username `KeyHunter`. This immediately revealed a clear and logical chain of malicious activity. I correlated the initial `ConsoleLogin` event with the subsequent `ListBuckets` API calls, pinpointing the attacker's source IP address and the exact timestamp of their discovery actions.
3.  **Containment & Eradication:** Based on the conclusive evidence from CloudTrail, I took decisive action. I navigated to the IAM dashboard and performed the primary eradication step: **deleting the compromised `KeyHunter` user** to immediately revoke all access.
4.  **Reporting & Recommendations:** I documented the full timeline of the attack and developed a set of strategic recommendations to prevent similar incidents, focusing on proactive security controls like MFA, least privilege, and automated alerting.



## Findings & Evidence
The CloudTrail logs provided a definitive, immutable record of the attacker's activities within the AWS environment.

| Artifact Type | Location / Value | Finding |
| :--- | :--- | :--- |
| **Initial Access** | CloudTrail Event: `ConsoleLogin` | The user `KeyHunter` successfully logged into the AWS console from the source IP `102.88.109.159`. |
| **Discovery** | CloudTrail Event: `ListBuckets` | The attacker enumerated all S3 buckets in the account to identify potential targets for data theft. |
| **Targeted Access** | Access to `dora-cloudbucket` | Following the `ListBuckets` discovery, the attacker focused on the `dora-cloudbucket`, which contained the sensitive file `Threat Intelligence.docx`. |
| **Indicator of Compromise** | User: `KeyHunter` <br> IP: `102.88.109.159` | The user account and source IP address were confirmed as malicious IoCs. |



## Screenshots
The following screenshots provide a visual narrative of the detection, analysis, and remediation process.

**Exhibit A: The Compromised User**
The investigation began by identifying the suspicious IAM user, `KeyHunter`, which had been recently created and showed recent activity.
<img width="1366" height="614" alt="eventjson" src="https://github.com/user-attachments/assets/ca4afd3e-141e-4fc7-bc40-23957eb8706a" />



<img width="1365" height="563" alt="events1" src="https://github.com/user-attachments/assets/743f042e-57f6-465d-854e-0375b8265fd2" />

**Exhibit B: The High-Value Target**
The sensitive file, `Threat Intelligence.docx`, residing in the `dora-cloudbucket`. This was the objective of the simulated attack.
<img width="1352" height="516" alt="upload" src="https://github.com/user-attachments/assets/81913f7c-855e-483e-b0cc-7a8ab17d4601" />



**Exhibit C: Eradication and Remediation**
The final step in the response was to delete the compromised `KeyHunter` user from IAM, immediately revoking the attacker's access and containing the threat.
<img width="1148" height="249" alt="deleted done" src="https://github.com/user-attachments/assets/59abfc05-4ccd-4ba2-8b67-3501fa984b7a" />
<img width="1366" height="584" alt="delete user" src="https://github.com/user-attachments/assets/ecaf3c07-0f67-4881-b9d6-9f2b4426c6e7" />





## Conclusion
This exercise successfully demonstrated a full-cycle cloud incident response. The breach was caused by a compromised IAM user with permissions sufficient to discover and access sensitive data. The investigation, driven by CloudTrail log analysis, provided a clear and actionable picture of the attacker's movements.

**Impact:** Had this been a real incident, the exposure of a file named "Threat Intelligence.docx" would be a critical security failure, potentially revealing defensive strategies, known vulnerabilities, or IoCs to an adversary.

**Recommendations:**
1.  **Enforce MFA:** Immediately mandate Multi-Factor Authentication (MFA) for all IAM users, especially those with console access.
2.  **Apply Least Privilege:** The `KeyHunter` user had overly broad permissions. IAM roles and policies should be scoped down to grant only the minimum permissions required for a specific task.
3.  **Implement Automated Alerting:** Configure AWS GuardDuty to detect anomalous API activity (like logins from unusual locations or S3 enumeration) and use CloudWatch Events to trigger automated alerts to the security team.



## Lessons Learned / Reflection
Playing both the attacker and the defender in this exercise provided a uniquely powerful perspective. As the attacker, I was struck by how quickly I could move from initial access to valuable data with just a single set of credentials. As the defender, it reinforced that **CloudTrail is the single most critical service for cloud forensics.** It is the ground truth. Every action an attacker takes leaves a trace, and the ability to quickly filter and correlate those traces is the core of effective cloud incident response. This project solidified my understanding that in the cloud, strong Identity and Access Management isn't just a best practice; it's the primary security perimeter.

