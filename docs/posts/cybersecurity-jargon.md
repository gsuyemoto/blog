---
title: Cybersecurity Jargon
slug: cybersecurity-jargon
date: 2023-06-03
---
  [prompt engineer]: https://www.axios.com/2023/02/22/chatgpt-prompt-engineers-ai-job
  [Black Lantern]: https://www.blacklanternsecurity.com/careers.html

# Cybersecurity Jargon

I just recently spoke to a pleasant tech recruiter and in the conversation she mentioned that I should take the opportunity to express any tech jargon that I would know related to the position I was interested in. With this, out of curiosity, I decided to make a quick comparison.

<!-- more -->

Logging into ChatGPT, I then queried it for cybersecurity jargon and here were the results:

> Certainly! Here's a list of cybersecurity jargon along with brief descriptions for each term:
> 
> 1. Firewall: A network security device that monitors and controls incoming and outgoing network traffic based on predetermined security rules.
> 
> 2. Malware: Short for malicious software, it refers to any software designed to harm or exploit computer systems, including viruses, worms, ransomware, and spyware.
> 
> 3. Phishing: A cyber attack where attackers pose as legitimate entities to trick individuals into revealing sensitive information, such as passwords, credit card numbers, or social security numbers.
> 
> 4. Encryption: The process of converting information into a code to prevent unauthorized access. Encryption ensures that data remains secure even if intercepted or accessed by unauthorized individuals.
> 
> 5. Intrusion Detection System (IDS): A system that monitors network traffic for suspicious activities or behaviors that may indicate a cyber attack or unauthorized access attempts.
> 
> 6. Denial-of-Service (DoS) attack: An attack aimed at rendering a computer system, network, or website unavailable by overwhelming it with a flood of illegitimate requests or excessive traffic.
> 
> 7. Vulnerability: A weakness or flaw in a computer system or software that can be exploited by attackers to gain unauthorized access, execute malicious code, or compromise the system's integrity.
> 
> 8. Two-Factor Authentication (2FA): A security mechanism that requires users to provide two different types of identification before accessing a system or account, typically combining a password with a unique verification code or biometric factor.
> 
> 9. Zero-day exploit: A cyber attack that takes advantage of a previously unknown vulnerability in software or hardware before a patch or fix is available.
> 
> 10. Social Engineering: The use of psychological manipulation techniques to deceive individuals into divulging confidential information or performing actions that may compromise security.
> 
> 11. Penetration Testing: Also known as ethical hacking, it involves simulating a cyber attack on a computer system or network to identify vulnerabilities and weaknesses in order to enhance security measures.
> 
> 12. Patch: A software update or fix released by developers to address vulnerabilities, bugs, or other security issues found in software or operating systems.
> 
> 13. VPN (Virtual Private Network): A secure network connection that allows users to access the internet or a private network securely by encrypting their connection and routing it through a remote server.
> 
> 14. Brute Force Attack: An automated technique that systematically tries all possible combinations of passwords or encryption keys until the correct one is found.
> 
> 15. Ransomware: A type of malicious software that encrypts a victim's files or locks their system, demanding a ransom payment in exchange for restoring access.
 
These are just a few examples of cybersecurity jargon, but the field is vast, and there are many more terms and concepts to explore.

Ha! Not that impressive. I mean, they ARE cybersecurity terminololgy, but very basic and very general. Let's try another query:

> As a web server operator, it is important to be aware of various cybersecurity vulnerabilities that can affect your web server. Here are some specific vulnerabilities to consider:
> 
> 1. Server Misconfiguration: Improperly configured web servers can lead to security vulnerabilities, such as open directories, default or weak passwords, unnecessary services or ports left open, or outdated software versions. Regularly review and update server configurations to ensure they align with security best practices.
> 
> 2. Cross-Site Scripting (XSS): XSS vulnerabilities allow attackers to inject malicious scripts into web pages, potentially leading to unauthorized access, data theft, or session hijacking. Employ input validation and output encoding to prevent XSS attacks.
> 
> 3. SQL Injection: SQL injection occurs when an attacker is able to insert malicious SQL code into a query, manipulating the database or obtaining unauthorized access to data. Implement proper input validation and parameterized queries to prevent SQL injection attacks.
> 
> 4. Server-Side Request Forgery (SSRF): SSRF vulnerabilities enable attackers to make requests from the server to internal resources or external systems, potentially bypassing firewalls and accessing sensitive information. Validate and sanitize user-supplied URLs and restrict access to internal resources.
> 
> 5. Remote File Inclusion (RFI) and Local File Inclusion (LFI): RFI and LFI vulnerabilities allow attackers to include and execute remote or local files on the web server, leading to unauthorized access, data disclosure, or remote code execution. Avoid including user-supplied input in file paths and ensure proper access controls.
> 
> 6. Denial-of-Service (DoS) Attacks: DoS attacks aim to overwhelm the server's resources, rendering it inaccessible to legitimate users. Implement measures such as rate limiting, traffic filtering, and load balancing to mitigate DoS attacks.
> 
> 7. Brute Force Attacks: Brute force attacks involve attempting various username and password combinations to gain unauthorized access to the server. Enforce strong password policies, implement account lockouts, and consider multi-factor authentication to protect against brute force attacks.
> 
> 8. File Upload Vulnerabilities: Insecure file upload functionality can lead to the execution of malicious code on the server or the dissemination of malicious files. Validate file types, restrict file permissions, and consider server-side scanning of uploaded files.
> 
> 9. Server-side Code Injection: Server-side code injection vulnerabilities occur when untrusted input is executed on the server, allowing attackers to run arbitrary code. Ensure proper input validation and implement whitelisting for input values.
> 
> 10. Insecure Direct Object References (IDOR): IDOR vulnerabilities arise when internal objects or resources are directly referenced in URLs, allowing attackers to access unauthorized information. Implement access controls and enforce proper authorization checks.
> 
> Regularly updating and patching software, conducting security assessments, and following security best practices are crucial to minimize these vulnerabilities and maintain the security of your web server.

Much, much better. Still, a bit lacking. Obviously, my ChatGPT skills need improvement, maybe everyone can benefit from brushing up on their '[prompt engineer]{target=_blank}' skills??

The last result did provide a nice gem, number 10 is definitely a term I feel a lot of the general dev community might not be as familiar with and have the necessary awareness.

OK, so now I know it's best for me as a n00b prompt engineer (n00b is such an interesting term to me as a former military veteran who came across this a lot in the military and to see how much it is used in the civilian world, did it originate in the military and then cross over? the civilian world and cross into the military? anyways...) to just use the old fashion way and do some manual search from various open source materials available to me (online AND offline). I DO have a bit experience in aggregating data from various open sources.

As gleaned from various sources

### Terms From Job Postings

Thanks to [Black Lantern].

**Knowledge of:** XSS, XXE, SQL Injection, Deserialization Attacks, File Inclusion/Path Traversal Attacks, Server-side Request Forgery, Remote Execution Flaws, Server Configuration Flaws and Authentication Flaws

**OWASP** testing methodology

**Certs:** GWAPT, OSCP, OSCE, GPEN

### Terms From Lethal Skills site:

deserialization, node vulnerabilities, template injection

### Terms From The Hacker Playbook 3:

DNS sinkhole, Time To Detect (TTD), Time To Mitigate (TTM), Tactics Techniques and Procedures (TTPs), Domain Admin (DA), Domain Controller (DC), stegonagraphy, Advanced Persistent Threat (APT), [Mitre Att&ck matrix](https://attack.mitre.org/)

[Penetration Testing Execution Standard](http://pentest-standard.org)

Study the specific tools and techniques (TTPs) of APTs [here](https://docs.google.com/spreadsheets/d/1H9_xaxQHpWaa4O_Son4Gx0YOIzlcBWMsdvePFX68EKU/htmlview?usp=gmail#). Additional TTPs can be found in the Google sheet as [links](https://attack.mitre.org/groups/G0006/) to Mitre references for each APT.

Commercial Off The Shelf (COTS) offensive tools: Metasploit, Cobalt Strike, DNS Cat

### Terms from Other:

- Remote Access Trojan (RAT)
- CIA triad (confidentiality, integrity and availability)

This is obviously a work in progress, stay tuned!
