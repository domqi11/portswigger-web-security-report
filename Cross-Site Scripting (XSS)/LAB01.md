Lab Report: Stored XSS into HTML Context with Nothing Encoded
Overview
This lab demonstrates a stored Cross-Site Scripting (XSS) vulnerability in the comment functionality of a blog application. Stored XSS occurs when malicious script is injected into a website and stored on the target server (in this case, in the comment database), allowing the attack to persist and affect any users who view the compromised page.
Objective
To solve this lab, we need to submit a comment containing JavaScript code that calls the alert() function when the blog post is viewed.
Vulnerability
The vulnerability exists because the application:

Allows users to submit HTML and JavaScript in comment fields
Stores this input in the database without proper sanitization
Renders the stored content directly in the HTML when displaying comments
Does not encode or escape special characters that could be interpreted as code

Exploitation Steps
1. Identify the Comment Functionality

Located the comment section on a blog post
Observed that it accepts text input for comments

2. Craft the XSS Payload

Inserted the following payload into the comment field:

html<script>alert(1)</script>
3. Complete the Comment Form

Filled in the required fields:

Name: "Dominic"
Email: "Email@example.com"
Website: "http://example.com"
<img width="500" alt="image" src="https://github.com/user-attachments/assets/328c1496-6724-480c-9e87-ffd87030d6a6" />



4. Submit the Comment

Clicked the "Post Comment" button to store the malicious script on the server

5. Verify the Exploit

Returned to the blog post
When the page loaded, the stored JavaScript executed automatically
The alert function was triggered, displaying a popup with the value "1"

Results
<img width="781" alt="image" src="https://github.com/user-attachments/assets/446e0322-bae9-4199-8fb4-a2d91891d8be" />

The lab was successfully solved. The XSS attack worked because:

The user input containing <script>alert(1)</script> was accepted by the application
The input was stored in the database without sanitization
When the blog was viewed, the browser interpreted and executed the script tag as legitimate JavaScript
The alert popup appeared, confirming the vulnerability

Impact
In a real-world scenario, this vulnerability could be exploited to:

Steal session cookies or authentication tokens
Perform unauthorized actions on behalf of users
Redirect users to phishing sites
Deface websites
Install keyloggers or other malicious client-side code

Mitigation Recommendations
To fix this vulnerability, the application should:

Implement proper input validation to reject potentially malicious input
Use context-aware output encoding when rendering user-supplied content
Apply Content Security Policy (CSP) headers to restrict script execution
Consider using HTML sanitization libraries to remove dangerous tags and attributes
Implement XSS filters to detect and block common XSS patterns

Conclusion
This lab demonstrates how easily stored XSS vulnerabilities can be exploited when user input is not properly sanitized before storage and rendering. The successful execution of the JavaScript alert function proves the concept and highlights the importance of implementing proper security controls for user-generated content.
