## TYPES OF PENETRATION TEST

**Network infrastructure test**

Evaluating the security posture of the actual network infrastructure and how it is able to help defend against attacks. This often includes the switches, routers, firewalls, and supporting resources, such as authentication, authorization, and accounting (AAA) servers and IPSs. A penetration test on wireless infrastructure may sometimes be included in the scope of a network infrastructure test. However, additional types of tests beyond a wired network assessment would be performed. For instance, a wireless security tester would attempt to break into a network via the wireless network either by bypassing security mechanisms or breaking the cryptographic methods used to secure the traffic.


**Application-Based Tests**


This type of pen testing focuses on testing for security weaknesses in enterprise applications. These weaknesses can include but are not limited to misconfigurations, input validation issues, injection issues, and logic flaws. Because a web application is typically built on a web server with a back-end database, the testing scope normally includes the database as well. However, it focuses on gaining access to that supporting database through the web application compromise. A great resource that we mention a number of times in this book is the Open Web Application Security Project (OWASP).

**Penetration Testing in the Cloud**

Cloud service providers (CSPs) such as Azure, Amazon Web Services (AWS), and Google Cloud Platform (GCP) have no choice but to take their security and compliance responsibilities very seriously. For instance, Amazon created the Shared Responsibility Model to describe the AWS customers’ responsibilities and Amazon’s responsibilities in detail (see https://aws.amazon.com/compliance/shared-responsibility-model).
The responsibility for cloud security depends on the type of cloud model (software as a service [SaaS], platform as a service [PaaS], or infrastructure as a service [IaaS]). For example, with IaaS, the customer (cloud consumer) is responsible for data, applications, runtime, middleware, virtual machines (VMs), containers, and operating systems in VMs. Regardless of the model used, cloud security is the responsibility of both the client and the cloud provider. These details need to be worked out before a cloud computing contract is signed. These contracts vary depending on the security requirements of the client. Considerations include disaster recovery, service-level agreements (SLAs), data integrity, and encryption. For example, is encryption provided end to end or just at the cloud provider? Also, who manages the encryption keys–the CSP or the client?
Overall, you want to ensure that the CSP has the same layers of security (logical, physical, and administrative) in place that you would have for services you control. When performing penetration testing in the cloud, you must understand what you can do and what you cannot do. Most CSPs have detailed guidelines on how to perform security assessments and penetration testing in the cloud. Regardless, there are many potential threats when organizations move to a cloud model. For example, although your data is in the cloud, it must reside in a physical location somewhere. Your cloud provider should agree in writing to provide the level of security required for your customers. As an example, the following link includes the AWS Customer Support Policy for Penetration Testing: https://aws.amazon.com/security/penetration-testing.
Understanding the concept of first-party vs. third-party hosted applications is very important. Applications today can not only be hosted in one public cloud (such as AWS, GCP, or Azure) but also in private and hybrid clouds. As a penetration tester, you must become familiar with any restrictions and limitations dictated by any third-party hosting or cloud providers
- *Unknown-Environment Test*:
In an unknown-environment penetration test, the tester is typically provided only a very limited amount of information. For instance, the tester may be provided only the domain names and IP addresses that are in scope for a particular target. The idea of this type of limitation is to have the tester start out with the perspective that an external attacker might have.
- *Known-Environment Test*:
In a known-environment penetration test, the tester starts out with a significant amount of information about the organization and its infrastructure. The tester would normally be provided things like network diagrams, IP addresses, configurations, and a set of user credentials. If the scope includes an application assessment, the tester might also be provided the source code of the target application. The idea of this type of test is to identify as many security holes as possible
With the sophistication and capabilities of adversaries today, it is likely that most networks will be compromised at some point, and a white-box approach is not a bad option.
- *Partially Known Environment Test*:
A partially known environment penetration test is somewhat of a hybrid approach between unknown- and known-environment tests. With partially known environment testing, the testers may be provided credentials but not full documentation of the network infrastructure.



## Most common penetration testing methodologies and other standards


**MITRE ATT&CK**

The MITRE ATT&CK framework (https://attack.mitre.org) is an amazing resource for learning about an adversary’s tactics, techniques, and procedures (TTPs). Both offensive security professionals (penetration testers, red teamers, bug hunters, and so on) and incident responders and threat hunting teams use the MITRE ATT&CK framework today. The MITRE ATT&CK framework is a collection of different matrices of tactics, techniques, and subtechniques. These matrices–including the Enterprise ATT&CK Matrix, Network, Cloud, ICS, and Mobile–list the tactics and techniques that adversaries use while preparing for an attack, including gathering of information (open-source intelligence [OSINT], technical and people weakness identification, and more) as well as different exploitation and post-exploitation techniques
MITRE ATT&CK is a detailed knowledgebase of attacker tactics, techniques, and procedures (TTP) that have been gathered from real attacks. It is not a manual or standard regarding how to conduct penetration tests. However, penetration testers can use it for ideas and guidance about how to exploit vulnerabilities as part of a test.


**OWASP WSTG**

The OWASP Web Security Testing Guide (WSTG) is a comprehensive guide focused on web application testing. It is a compilation of many years of work by OWASP members. OWASP WSTG covers the high-level phases of web application security testing and digs deeper into the testing methods used. For instance, it goes as far as providing attack vectors for testing cross-site scripting (XSS), XML external entity (XXE) attacks, cross-site request forgery (CSRF), and SQL injection attacks; as well as how to prevent and mitigate these attacks.


**NIST SP 800-115**

Special Publication (SP) 800-115 is a document created by the National Institute of Standards and Technology (NIST), which is part of the U.S. Department of Commerce. NIST SP 800-115 provides organizations with guidelines on planning and conducting information security testing.


The Open Source Security Testing Methodology Manual (OSSTMM

The Penetration Testing Execution Standard (PTES) (http://www.pentest-standard.org)

The Information Systems Security Assessment Framework (ISSAF)

TIP Omar Santos created a GitHub repository that includes numerous cybersecurity resources. You can access the repository at https://h4cker.org/github.



**Planning & scoping a penetration testing Assessment**

Many things can go wrong if you do not scope and plan a penetration
testing engagement appropriately. In particular, you need
to be aware of local laws and legal concepts related to penetration testing

Scope creep is a project management term that refers to the uncontrolled growth of a project’s scope. It is also often referred to as kitchen sink syndrome, requirement creep, and function creep. Scope creep can put you out of business. Many security firms suffer from scope creep and are unsuccessful because they have no idea how to identify when the problem starts or how to react to it.

One of the most important phases (if not the most important) of any penetration testing engagement is the planning and preparation phase. During this phase, you clearly scope your engagement. If you do not scope correctly, you will definitely run into issues with your client (if you work as a consultant) or with your boss (if you are part of a corporate red team), and you might even encounter legal problems


**Regulatory Compliance Considerations**

You must be familiar with several regulatory compliance considerations in order to be successful in ethical hacking and penetration testing – not only to complete compliance-based assessments but also to understand what regulations may affect you and your client.

The Payment Card Industry Data Security Standard (PCI DSS) regulation aims to secure the processing of credit card payments and other types of digital payments. PCI DSS specifications, documentation, and resources can be accessed at https://www.pcisecuritystandards.org.
PCI DSS must be adopted by any organization that transmits, processes, or stores payment card data or that directly or indirectly affects the security of cardholder data. If the PAN is not stored, processed, or transmitted, PCI DSS requirements do not apply

The original intent of the Health Insurance Portability and Accountability Act of 1996 (HIPAA) regulation was to simplify and standardize healthcare administrative processes. Administrative simplification called for the transition from paper records and transactions to electronic records and transactions. The U.S. Department of Health and Human Services (HHS) was instructed to develop and publish standards to protect an individual’s electronic health information while permitting appropriate access and use of that information by healthcare providers and other entities. Information about HIPAA can be obtained from https://www.cdc.gov/phlp/publications/topic/hipaa.html.

The U.S. federal government uses the Federal Risk and Authorization Management Program (FedRAMP) standard to authorize the use of cloud service offerings. You can obtain information about FedRAMP at https://www.fedramp.gov.

General Data Protection Regulation (GDPR). GDPR includes strict rules around the processing of data and privacy. One of the GDPR’s main goals is to strengthen and unify data protection for individuals within the European Union (EU), while addressing the export of personal data outside the EU.You can obtain additional information about GDPR at https://gdpr-info.eu.


**Local Restrictions**

You should be aware of any local restrictions when you are hired to perform penetration testing.
Some penetration testers have been accused and even arrested for allegedly violating the Computer Fraud and Abuse Act of America Section 1030(a)(5)(B). You must always have clear documentation from your client (the entity that hired you) indicating that you have permission to perform the testing.

During your pre-engagement tasks, you should identify testing constraints, including tool restrictions

You should clearly communicate any technical constraints with the appropriate stakeholders of the organization that hired you prior to and during the testing.


## Legal Concepts

**Service-level agreement (SLA)**

An SLA is a well-documented expectation or constraint related to one or more of the minimum and/or maximum performance measures (such as quality, timeline/timeframe, and cost) of the penetration testing service. You should become familiar with any SLAs that the organization that hired you has provided to its customers.

Confidentiality
You must discuss and agree on the handling of confidential data. For example, if you are able to find passwords or other sensitive data, do you need to disclose all those passwords or all that sensitive data? Who will have access to the sensitive data? What will be the proper way to communicate and handle such data? Similarly, you must protect sensitive data and delete all records, per your agreement with your client.

**Statement of work (SOW)**

An SOW is a document that specifies the activities to be performed during a penetration testing engagement. It can be used to define some of the following elements:

Project (penetration testing) timelines, including the report delivery schedule
The scope of the work to be performed
The location of the work (geographic location or network location)
Special technical and nontechnical requirements
Payment schedule
Miscellaneous items that may not be part of the main negotiation but that need to be listed and tracked because they could pose problems during the overall engagement
The SOW can be a standalone document or can be part of a master service agreement (MSA).
Master service agreement (MSA)
MSAs, which are very popular today, are contracts that can be used to quickly negotiate the work to be performed. When a master agreement is in place, the same terms do not have to be renegotiated every time you perform work for a customer. MSAs are especially beneficial when you perform a penetration test, and you know that you will be rehired on a recurring basis to perform additional tests in other areas of the company or to verify that the security posture of the organization has been improved as a result of prior testing and remediation.

**Non-disclosure agreement (NDA)**

An NDA is a legal document and contract between you and an organization that has hired you as a penetration tester. An NDA specifies and defines confidential material, knowledge, and information that should not be disclosed and that should be kept confidential by both parties. NDAs can be classified as any of the following:

Unilateral: With a unilateral NDA, only one party discloses certain information to the other party, and the information must be kept protected and not disclosed. For example, an organization that hires you should include in an NDA certain information that you should not disclose. Of course, all of your findings must be kept secret and should not be disclosed to any other organization or individual.
Bilateral: A bilateral NDA is also referred to as a mutual, or two-way, NDA. In a bilateral NDA, both parties share sensitive information with each other, and this information should not be disclosed to any other entity.
Multilateral: This type of NDA involves three or more parties, with at least one of the parties disclosing sensitive information that should not be disclosed to any entity outside the agreement. Multilateral NDAs are used in the event that an organization external to your customer (business partner, service provider, and so on) should also be engaged in the penetration testing engagement.

**Contracts**

The contract is one of the most important documents in a pen testing engagement. It specifies the terms of the agreement and how you will get paid, and it provides clear documentation of the services that will be performed. A contract should be very specific, easy to understand, and without ambiguities. Any ambiguities will likely lead to customer dissatisfaction and friction. Legal advice (from a lawyer) is always recommended for any contract
Disclaimers
You might want to add disclaimers to your pre-engagement documentation, as well as in the final report. For example, you can specify that you conducted penetration testing on the applications and systems that existed as of a clearly stated date
Some measures are just guidelines, but others are legally required.

**Rules of Engagement**

The rules of engagement document specifies the conditions under which the security penetration testing engagement will be conducted
You need to document and agree upon these rule of engagement conditions with the client or an appropriate stakeholder.

Scoping is one of the most important elements of the pre-engagement tasks with any penetration testing engagement. You not only have to carefully identify and document all systems, applications, and networks that will be tested but also determine any specific requirements and qualifications needed to perform the test. The broader the scope of the penetration testing engagement, the more skills and requirements that will be needed.

You may also be hired to perform an assessment of modern applications using different application programming interfaces (APIs)
Simple Object Access Protocol (SOAP) project files: SOAP is an API standard that relies on XML and related schemas.
The SOAP specification can be accessed at https://www.w3.org/TR/soap.
Swagger (OpenAPI) documentation is a modern framework of API documentation and development that is now the basis of the OpenAPI Specification (OAS). These documents are used in representational state transfer (REST) APIs. REST is a software architectural style designed to guide development of the architecture for web services (including APIs). REST, or “RESTful,” APIs are the most common types of APIs used today. Swagger documents can be extremely beneficial when testing APIs. Additional information about Swagger can be obtained at https://swagger.io. The OAS is available at https://github.com/OAI/OpenAPI-Specification.
Web Services Description Language (WSDL) is an XML-based language that is used to document the functionality of a web service.
GraphQL is a query language for APIs. It is also a server-side runtime for executing queries using a type system you define for your data
Web Application Description Language (WADL) is an XML-based language for describing web applications


The following are some additional support resources that you might obtain from the organization that hired you to perform the penetration test
SDKs can also help pen testers understand certain specialized applications and hardware platforms within the organization being tested.
Some organizations may allow you to obtain access to the source code of applications to be tested.
Examples of Application Requests
system and network architectural diagrams

If you initially engaged with your client after a request for proposal (RFP), and additional work is needed that was not part of the RFP or your initial SOW, you should ask for a new SOW to be signed and agreed upon.

**Validating the Scope of Engagement**

The first step in validating the scope of an engagement is to question the client and review contracts. You must also understand who the target audience is for your penetration testing report. You should understand the subjects, business units, and any other entity that will be assessed by such a penetration testing engagement.

TIP Time management is very important in a penetration testing engagement. Time management is the process of planning and organizing how you divide and allocate your time to complete different tasks during the penetration testing engagement. Failing to manage your time and learn how to prioritize important tasks may damage your effectiveness and cause unnecessary stress. The benefits of time management during a penetration test are enormous and include greater productivity and increased opportunity to find additional vulnerabilities in targeted systems.

**Clients may ask questions like these.**

- How do I explain the overall cost of penetration testing to my boss?
- Why do we need penetration testing if we have all these security technical and nontechnical controls in place?
- How do I build in penetration testing as a success factor?
- Can I do it myself?
- How do I calculate the ROI for the penetration testing engagement?

At the same time, the tester needs to answer questions like these.

- How do I account for all items of the penetration testing engagement to avoid going over budget?
- How do I do pricing?
- How can I clearly show ROI to my client?

The answers to these questions depend on how effective you are at scoping and clearly communicating and understanding all the elements of the penetration testing engagement
Demonstrating an Ethical Hacking Mindset by Maintaining Professionalism and Integrity
Working in Cybersecurity is not always about stopping cyber attacks. As a Cybersecurity specialist, your organization may entrust you with some of the most sensitive customer data. You will be confronted with challenging ethical dilemmas which may not have an easy or clear answer.
There are several approaches or perspectives on ethical decision making, including utilitarian ethics, the rights approach, and the common good approach. Other ethical decision models include the fairness or justice approach as well as the virtue approach.




## Information Gathering and Vulnerability Scanning
To search for username using OSINT: https://whatsmyname.app/  Username searching can identify accounts that important enterprise personnel may have on various sites. The types of sites that personnel have registered for can also provide details of their lives and interests. These details could be used in social engineering attacks.
You can easily identify domain technical and administrative contacts by using the Whois tool. Many organizations keep their registration details private and instead use the domain registrar organization contacts. e.g whois xultechng.com
Social Media Scraping
Attackers can easily gather valuable information about victims by scraping social media sites such as Twitter, LinkedIn, Facebook, and Instagram. People post too many things online. They publicly talk about their hobbies, the restaurants they visit, what they do for work, work promotions, where they travel for business and pleasure, and much more.
Attackers often leverage job listings in websites like Indeed, LinkedIn, CareerBuilder, and individual company websites to obtain information about the technologies these companies use (for example, the technology stack of an organization)
Similarly, attackers have also created job posts to attract people to apply for those positions. Then they interview their victims to try to get them to talk about what they do at work and the technologies used by their employer.

Unauthorized access to data, computer, and network systems is a crime in many jurisdictions and often is accompanied by severe consequences, regardless of the perpetrator’s motivations

- Perform an audit
- place of employment
- where you live
- online shopping preferences
- daily schedule
- vacation plans
- political beliefs
- interests
- hobbies
- education
- cultural beliefs
- family members
- pets

**Items to investigate include:**

All your social media profiles - name, birthdate, contact information, etc.
Your status updates - life events, work relationships and status, political and religious beliefs.
Location data - hometown information and geo check-ins.
Shared content - pictures and comments you have posted and those where you are mentioned or tagged.
Posts from friends and family.
Any online discussions you have joined or participated in.

**Cryptographic Flaws**

During the reconnaissance phase, attackers often can inspect Secure Sockets Layer (SSL) certificates to obtain information about the organization, potential cryptographic flaws, and weak implementations.
Certificate transparency allows certificate authorities (CAs) to provide details about all certificates that have been issued for a given domain and organization. Attackers can also use this information to reveal what other subdomains and systems an organization may own
Tools such as crt.sh enable you to obtain detailed certificate transparency information about any given domain

SSL/TLS certificates provide two broad functions. First, they provide a way that the ownership of a website can be validated by people who are accessing it. Second, they provide a means by which communication between a client and server is encrypted so that it cannot be read or altered by unauthorized parties. They also provide the information required for a browser to create a secure, encrypted connection to a web site over the HTTPS protocol
Certain versions of software, such as OpenSSL, have widely known vulnerabilities that can be exploited, including vulnerability to the heartbleed bug. In addition, it is possible that some certificates could use weak encryption algorithms.
To

To view stored certificates in the operating system. Enter certmgr.msc in the search box and press Enter to open it on windows.
Run sslscan and save the output to an HTML file:
sslscan netacad.com | aha > netacad_cert.html


Attackers can leverage information from past security breaches that an organization might have experienced. They may, for example, leverage the following data while trying to gather information about their victims:
Password dumps: An example of a tool that allows you to find email addresses and passwords exposed in previous breaches is h8mail.
File metadata:You can obtain a lot of information from metadata in files such as images, Microsoft Word documents, Excel files, PowerPoint files, and more. Several tools can show Exif details. One of the most popular of them, ExifTool.


Strategic search engine analysis/enumeration: hackers and attackers can use it to do something that has been termed Google hacking. advanced Google hacking. We recommend that you visit the Google Hacking Database (GHDB) repositories at https://www.exploit-db.com/google-hacking-database/.
Website archiving/caching: Several organizations archive and cache website data on the Internet. One of the most popular repositories is the “Wayback Machine” of Internet Archive (https://archive.org/web).
Public source code repositories: An attacker can obtain extremely valuable information from public source code repositories such as GitHub and GitLab. Most of the applications and products we consume today use open-source software that is freely available in these public repositories. Attackers can find vulnerabilities in those software packages and use them to their advantage.

Resources exist that will allow access to these data breach files. From there, you may be able to find usernames, email addresses, passwords, and other information about employees.

Using Advanced Google searches and parsing through archived internet sites are two popular methods of passive reconnaissance. The hacker will search using specific key words and Google search operators to try and find what they are looking for. This is called Google dorking.
The Wayback Machine web archive is another useful tool for uncovering potential vulnerabilities. Valuable personal and corporate information can sometimes be gleaned from archived web pages. Using the Wayback machine, a hacker can browse through the history of a website and visit snapshots of the site at various times in the past. This allows the hacker to uncover information no longer available on the live internet that may be useful for further attacks.
Search operator
What it does
Example
“ ”
Search for results that mention a word or phrase.
“steve jobs”
OR
Search for results related to X or Y. 
jobs OR gates
|
Same as OR:
jobs | gates
AND
Search for results related to X and Y. 
jobs AND gates
-
Search for results that don’t mention a word or phrase.
jobs -apple
*
Wildcard matching any word or phrase.
steve * apple 
( )
Group multiple searches.
(ipad OR iphone) apple
define:
Search for the definition of a word or phrase. 
define:entrepreneur
cache:
Find the most recent cache of a webpage.
cache:apple.com
filetype:
Search for particular types of files (e.g., PDF).
apple filetype:pdf
ext: 
Same as filetype:
apple ext:pdf
site:
Search for results from a particular website.
site:apple.com
related:
Search for sites related to a given domain.
related:apple.com
intitle:
Search for pages with a particular word in the title tag.
intitle:apple
allintitle:
Search for pages with multiple words in the title tag. 
allintitle:apple iphone
inurl:
Search for pages with a particular word in the URL. 
inurl:apple
allinurl:
Search for pages with multiple words in the URL. 
allinurl:apple iphone
intext:
Search for pages with a particular word in their content.
intext:apple iphone
allintext:
Search for pages with multiple words in their content.
allintext:apple iphone
weather:
Search for the weather in a location. 
weather:san francisco
stocks:
Search for stock information for a ticker.
stocks:aapl
map:
Force Google to show map results.
map:silicon valley
movie:
Search for information about a movie.
movie:steve jobs
in
Convert one unit to another.
$329 in GBP
source:
Search for results from a particular source in Google News.
apple source:the_verge
before:
Search for results from before a particular date.
apple before:2007-06-29
after:
Search for results from after a particular date.
apple after:2007-06-29

The syntax is search term operator:domain--ethical hacker site:pearson.com
You can combine multiple operators: ethical hacker site:pearson.com filetype:pdf
hacker intitle:certificethicalation
ethical hacker inurl:free
allintext:free ethical hacker practice test questions
Conduct passive reconnaissance with advanced search operators.
site:examplecompany.com inurl:admin
site:examplecompany.com intitle:login
site:examplecompany.com filetype:pdf
site:examplecompany.com intext:employee filetype:pdf
site:linkedin.com intitle:example company. Experiment by searching for the company name with and without the .com at the end

Open-source intelligence (OSINT) gathering is a method of gathering publicly available intelligence sources to collect and analyze information about a target. OSINT is “open source” because collecting the information does not require any type of covert methods
Two tools that can be used for OSINT gathering: Recong-ng and Shodan.

Recon-ng is a modular framework, which makes it easy to develop and integrate new functionality. It is highly effective in social networking site enumeration because of its use of application programming interfaces (APIs) to gather information
commands
To start: recon-ng
View available commands: help
search available modules: marketplace search. The letter D indicates that the module has dependencies. The letter K indicates that an API key is needed in order to use the resources used in a particular module. For example, the module with the path recon/companies-contacts/censys_email_address has dependencies and needs an API key in order to query the Censys database. (Censys is a very popular resource for querying OSINT data.)
Refresh the Marketplace: marketplace refresh
Search the marketplace: marketplace search < keyword >. We can use the module bing_domain_web to try to find any subdomains leveraging the Bing search engine.
Install a module: marketplace install <module full name>
Show installed modules: modules search
Load a module: modules load <module name>.After the module is loaded, you can display the module options by using the info command.
Change the source: options set SOURCE xultechng.com. After the source domain is set, you can type run to run the query. The result will be blank if you haven't added the API key

Recon-ng is incredibly powerful because it uses the APIs of various OSINT resources to gather information. Its modules can query sites such as Facebook, Indeed, Flickr, Instagram, Shodan, LinkedIn, and YouTube.


Shodan is an organization that scans the Internet 24 hours a day, 365 days a year. The results of those scans are stored in a database that can be queried at shodan.io or by using an API. You can use Shodan to query for vulnerable hosts, Internet of Things (IoT) devices, and many other systems that should not be exposed or connected to the public Internet.

Shodan provides a method to filter your search results using the syntax filter:value with no spaces. If the value contains spaces, such as city:"los angeles", you must enclose the value in double quotes. Some of the most popular search filters are:
country:XX Searches for a 2 digit country code

city:city-name Searches for a city by name

region:region-or-state-name Searches for a specific state or region

product:product-name Searches for a specific product by name

version:XX Searches for a specific product version

vuln:XX Searches for vulnerabilities that match a specific CVE number
Enter a filter on the Shodan search bar. This example returns all the devices with “webcam” in a banner that Shodan finds in the city of Toronto.
webcam city:Toronto
A common configuration issue found on the internet is FTP servers that permit anonymous logins. Use the search string to find the FTP servers in San Jose, California.
port:21 country:US region:CA city:"San Jose" 230
This search uses the standard FTP TCP port 21, with location filters, and a text search for 230. 230 is the FTP successful login response code.





