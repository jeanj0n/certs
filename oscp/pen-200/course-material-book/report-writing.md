# Report Writing

Remember

* Who is your target (eg. bank, hospital)
* Different clients have different needs (PCI vs HIPAA)
* Means to rectify can be different (eg. slow patching to minimize downtime vs immediate fix to prevent future compromise)
* Executive vs Technical summary (Group vulns if a pattern is seen eg. user input not validated)

{% code title="Testing Note" %}
```
Testing for Cross-Site Scripting 

Testing Target: 192.168.1.52 
Application:    XSSBlog
Date Started:   31 March 2022

1.  Navigated to the application
    http://192.168.1.52/XSSBlog.html
    Result: Blog page displayed as expected
    
2.  Entered our standard XSS test data: 
    You will rejoice to hear that no disaster has accompanied the
    commencement of an enterprise which you have regarded with such
    evil forebodings.<script>alert("Your computer is infected!");</script> 
    I arrived here yesterday, and my first task is to assure my dear
    sister of my welfare and increasing confidence in the success of
    my undertaking. 

3.  Clicked Submit to post the blog entry.
    Result: Blog entry appeared to save correctly.

4.  Navigated to read the blog post
    http://192.168.1.52/XSSRead.php
    Result: The blog started to display and then the expected alert popped up.

5.  Test indicated the site is vulnerable to XSS.

PoC payload: <script>alert(‘Your computer is infected!')</script>
```
{% endcode %}

&#x20;

## Exec Snippets

```
Executive Summary:

- Scope: https://kali.org/login.php
- Timeframe: Jan 3 - 5, 2022
- OWASP/PCI Testing methodology was used
- Social engineering and DoS testing were not in scope
- No testing accounts were given; testing was black box from an external IP address
- All tests were run from 192.168.1.2
```

```
- "The Client hired OffSec to conduct a penetration test of
their kali.org web application in October of 2025. The test was conducted
from a remote IP between the hours of 9 AM and 5 PM, with no users
provided by the Client."
```

```
- "The application had many forms of hardening in place. First, OffSec was unable to upload malicious files due to the strong filtering
in place. OffSec was also unable to brute force user accounts
because of the robust lockout policy in place. Finally, the strong
password policy made trivial password attacks unlikely to succeed.
This points to a commendable culture of user account protections."
```

```
- "However, there were still areas of concern within the application.
OffSec was able to inject arbitrary JavaScript into the browser of
an unwitting victim that would then be run in the context of that
victim. In conjunction with the username enumeration on the login
field, there seems to be a trend of unsanitized user input compounded
by verbose error messages being returned to the user. This can lead
to some impactful issues, such as password or session stealing. It is
recommended that all input and error messages that are returned to the
user be sanitized and made generic to prevent this class of issue from
cropping up."
```

```
"These vulnerabilities and their remediations are described in more
detail below. Should any questions arise, OffSec is happy
to provide further advice and remediation help."
```

## Technical Summary

Group findings into common areas

* User and Privilege Management
* Architecture
* Authorization
* Patch Management
* Integrity and Signatures
* Authentication
* Access Control
* Audit, Log Management and Monitoring
* Traffic and Data Encryption
* Security Misconfigurations

```
4. Patch Management

Windows and Ubuntu operating systems that are not up to date were
identified. These are shown to be vulnerable to publicly-available
exploits and could result in malicious execution of code, theft
of sensitive information, or cause denial of services which may
impact the infrastructure. Using outdated applications increases the
possibility of an intruder gaining unauthorized access by exploiting
known vulnerabilities. Patch management ought to be improved and
updates should be applied in conjunction with change management.
```

### Technical Findings and Recommendations

The meat of the report, all detailed technical info dumped here

A tabular summary including severity, issue description and implications along with recommendations comes at the top.&#x20;

You then go detailed with issue, showing step by step how you did it referring notes, code snippets and screenshots.

> This includes steps that we take for granted (such as running with administrative privileges), as these may not be obvious to the reader.

Good remediations must be suggested that aren't unacceptable (eg. disabling RDP)

### Appendices, Further Information, and References

The final part of the report is the _Appendices_ section. Things that go here typically do not fit anywhere else in the report or are too lengthy or detailed to include inline. This includes long lists of compromised users or affected areas, large proof-of-concept code blocks, expanded methodology or technical write-ups, etc. A good rule to follow is if it's necessary for the report but would break the flow of the page, put it in an appendix.
