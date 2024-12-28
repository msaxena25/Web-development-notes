# **Implementing Security Best Practices in Web Applications**

Securing web applications is a critical aspect of development. My approach involves following industry standards, implementing secure coding practices, and using tools to identify and mitigate vulnerabilities. Here’s how I prevent common vulnerabilities like **XSS (Cross-Site Scripting)**, **CSRF (Cross-Site Request Forgery)**, and **SQL Injection**:

---

### **1. Preventing Cross-Site Scripting (XSS)**

**XSS** occurs when attackers inject malicious scripts into trusted websites viewed by other users.

#### **Best Practices:**
1. **Input Validation**:
   - Validate all user input on the server-side.
   - Reject inputs that don’t match expected patterns.

2. **Output Encoding**:
   - Encode data before displaying it in the browser to prevent the execution of malicious scripts.
   - Use libraries like `DOMPurify` for sanitizing HTML.

3. **Use Content Security Policy (CSP)**:
   - Implement CSP headers to restrict the sources from which scripts can be executed.
   - Example CSP header:
     ```http
     Content-Security-Policy: default-src 'self'; script-src 'self';
     ```

4. **Avoid `eval()`**:
   - Do not use `eval()` or other functions like `setTimeout` with string arguments.

#### **Tools Used**:
- **ESLint** security plugins for JavaScript.
- **Burp Suite** for identifying XSS vulnerabilities during testing.

---

### **2. Preventing Cross-Site Request Forgery (CSRF)**

**CSRF** attacks trick authenticated users into submitting malicious requests.

#### **Best Practices:**
1. **CSRF Tokens**:
   - Include unique, unpredictable CSRF tokens in every form submission or request.
   - Verify the token on the server before processing the request.

2. **SameSite Cookies**:
   - Use `SameSite` attribute for cookies to ensure they are not sent with cross-origin requests:
     ```http
     Set-Cookie: sessionId=abc123; SameSite=Strict; Secure
     ```

3. **User Authentication**:
   - Require re-authentication for sensitive actions (e.g., changing passwords).

4. **Limit HTTP Methods**:
   - Restrict sensitive operations (e.g., DELETE, PUT) to POST requests only.

#### **Tools Used**:
- **OWASP ZAP** for testing CSRF vulnerabilities.
- Framework-specific CSRF protection libraries like **Angular's HTTP Interceptor** or **Express’s `csurf` middleware**.

---

### **3. Preventing SQL Injection**

**SQL Injection** occurs when attackers inject malicious SQL queries into input fields to access or manipulate the database.

#### **Best Practices:**
1. **Parameterized Queries/Prepared Statements**:
   - Use parameterized queries to separate SQL logic from data.
   - Example in Node.js with MySQL:
     ```javascript
     const query = "SELECT * FROM users WHERE id = ?";
     db.execute(query, [userId]);
     ```

2. **ORMs (Object Relational Mappers)**:
   - Use ORMs like Sequelize or Hibernate, which abstract raw SQL queries and automatically handle escaping.

3. **Input Validation and Sanitization**:
   - Validate and sanitize all user inputs to prevent malicious data from being processed.

4. **Least Privilege Principle**:
   - Ensure the database user has minimal permissions required for the application.

5. **Monitor and Log SQL Queries**:
   - Use database monitoring tools to detect unusual query patterns.

#### **Tools Used**:
- Static analysis tools like **SonarQube**.
- Dynamic testing with **SQLMap** to simulate SQL injection attempts.

---

### **General Security Best Practices**

1. **Authentication and Authorization**:
   - Use strong password policies and multi-factor authentication (MFA).
   - Ensure proper role-based access control (RBAC).

2. **HTTPS Everywhere**:
   - Use SSL/TLS to encrypt communication between the client and the server.

3. **Error Handling**:
   - Do not expose sensitive information in error messages.

4. **Regular Security Audits**:
   - Conduct periodic penetration testing and code reviews.
   - Use vulnerability scanning tools like **OWASP Dependency-Check**.

5. **Secure Dependencies**:
   - Regularly update libraries and frameworks to patch known vulnerabilities.
   - Use tools like **npm audit** or **Snyk** to monitor dependency vulnerabilities.

---

### **My Approach in Practice**
- I adhere to **OWASP Top 10** guidelines to mitigate common risks.
- Security is integrated into the **SDLC** (Secure Development Lifecycle) through training, code reviews, and automated testing.
- I leverage CI/CD pipelines to enforce security checks using tools like **GitHub Advanced Security** and **Dependabot**.

-----

# **What is SQL Injection?**

In a typical SQL injection attack, an attacker supplies malicious input into a web application's input fields (e.g., login forms, search bars, or URL parameters). If the application directly includes this input in SQL queries without sanitizing or parameterizing it, the database will execute the malicious SQL.

---

### **How an Attacker Performs SQL Injection**

1. **Identify Injection Points**:
   - The attacker probes the web application for fields or URLs that might interact with the database.
   - For example:
     ```
     http://example.com/products?id=1
     ```

2. **Inject Malicious Input**:
   - The attacker crafts input designed to alter the SQL query's logic.
   - Example Input:
     ```
     1 OR 1=1
     ```
     When injected into a query like this:
     ```sql
     SELECT * FROM products WHERE id = 1 OR 1=1;
     ```
     This query always returns all rows because `1=1` is always true.

3. **Escaping Query Logic**:
   - Attackers may use special characters like `'`, `"`, `--`, or `;` to manipulate the SQL.
   - Example of bypassing a login form:
     Input in the username field:
     ```
     ' OR '1'='1
     ```
     If the application constructs the query like this:
     ```sql
     SELECT * FROM users WHERE username = '' OR '1'='1' AND password = 'password';
     ```
     The `OR '1'='1'` always evaluates to true, granting access.

4. **Exfiltrating Data**:
   - Attackers extract sensitive information using UNION queries or by appending additional SELECT statements.
   - Example:
     ```sql
     1 UNION SELECT username, password FROM users;
     ```
---

### **SQL Injection Prevention Techniques**

1. **Parameterized Queries/Prepared Statements**:
   - Use placeholders for user inputs to prevent dynamic query building.
   - Example in Node.js:
     ```javascript
     const query = "SELECT * FROM users WHERE username = ? AND password = ?";
     db.execute(query, [username, password]);
     ```

2. **Input Validation**:
   - Validate and sanitize all user inputs.
   - Reject inputs that do not meet expected patterns.

3. **Escaping Special Characters**:
   - Properly escape characters like `'` and `"` before including them in SQL queries.

4. **Least Privilege Principle**:
   - Ensure the database user has minimal permissions (e.g., no DROP or DELETE rights unless necessary).

5. **Use ORMs**:
   - Object Relational Mappers like Hibernate or Sequelize abstract raw SQL queries and mitigate injection risks.

6. **Web Application Firewalls (WAFs)**:
   - Deploy WAFs to detect and block malicious requests.

7. **Error Handling**:
   - Do not expose detailed error messages to the user.

8. **Regular Updates and Patching**:
   - Keep the database, frameworks, and libraries up to date to prevent exploitation of known vulnerabilities.

---

### **SQL Injection Testing Tools**

1. **SQLMap**:
   - A powerful tool to automate SQL injection detection and exploitation.

2. **Burp Suite**:
   - For identifying vulnerabilities during penetration testing.

3. **OWASP ZAP**:
   - For security testing in web applications.

4. **Fuzzers**:
   - Tools to test application inputs with unexpected or malicious data.

---

# **How Cross-Site Scripting (XSS) Works?**

**Cross-Site Scripting (XSS)** is a security vulnerability where attackers inject malicious scripts into web applications that are executed in a victim's browser. These scripts can steal sensitive information, manipulate web content, or perform malicious actions on behalf of the user.

Attackers perform XSS by exploiting input fields, URLs, or any part of the application where user input is reflected back on the page without proper validation or sanitization.

---

### **Types of XSS Attacks**

1. **Stored XSS**:
   - The malicious script is stored in the database or server and affects every user who accesses the page.
   - Example: Comment sections, forums.

2. **Reflected XSS**:
   - The malicious script is part of the URL or input and immediately reflected back in the response.
   - Example: Search bars, error messages.

3. **DOM-Based XSS**:
   - The vulnerability exists in the client-side JavaScript code that processes user input.

---

### **Simple Example of XSS**

#### **Scenario**: Reflected XSS in a Search Form

Suppose a website has a search feature with the following functionality:

1. The user enters a search term.
2. The term is displayed back to the user as part of the search results.

---

#### **Vulnerable Code**

Here’s an example of a vulnerable server-side code in JavaScript:

```javascript
const express = require('express');
const app = express();

app.get('/search', (req, res) => {
    const searchQuery = req.query.q;
    res.send(`<h1>Search Results for: ${searchQuery}</h1>`);
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

- The user input (`req.query.q`) is directly included in the HTML response without sanitization.
- If the user enters `<script>alert('XSS')</script>`, the response becomes:

```html
<h1>Search Results for: <script>alert('XSS')</script></h1>
```

---

#### **How the Attack Works**

1. **Attacker’s Input**:
   - The attacker crafts a malicious URL:
     ```
     http://localhost:3000/search?q=<script>alert('XSS')</script>
     ```

2. **Execution in Victim’s Browser**:
   - When the victim clicks the link, the browser renders the response:
     ```html
     <h1>Search Results for: <script>alert('XSS')</script></h1>
     ```
   - This executes the `alert('XSS')` script, demonstrating a successful XSS attack.

3. **Real-World Impact**:
   - The attacker could replace `alert('XSS')` with more harmful payloads, such as stealing cookies or redirecting the user to a phishing site.

---

### **Prevention Tips for Developers**

1. **Escape and Sanitize User Input**:
   - Use libraries like `DOMPurify` to sanitize HTML content.
   - For server-side rendering, escape special characters.

2. **Content Security Policy (CSP)**:
   - Implement CSP to restrict the sources from which scripts can be loaded:
     ```http
     Content-Security-Policy: default-src 'self'; script-src 'self';
     ```

3. **Use Framework Security Features**:
   - Frameworks like Angular and React automatically escape output by default.

4. **Validate Input**:
   - Validate input on both client and server sides.

5. **Avoid Inline JavaScript**:
   - Never use inline `onClick` or `onLoad` attributes for JavaScript execution.

-----

# **What is Cross-Site Request Forgery (CSRF)?**

**Cross-Site Request Forgery (CSRF)** is a web security vulnerability where an attacker tricks a user into performing an unwanted action on a trusted website where they are authenticated. 

By exploiting the user’s authenticated session, attackers can perform actions like transferring funds, changing account details, or deleting records without the user’s consent.

---

### **How CSRF Works**

1. **User Authenticated**:
   - The victim logs into a trusted website (e.g., a banking app) and receives an authenticated session token stored in cookies.

2. **Malicious Request Crafted**:
   - The attacker crafts a malicious request to the same website where the user is authenticated.

3. **Victim Execution**:
   - The attacker tricks the victim into visiting a malicious website or clicking on a crafted link.
   - The browser automatically includes the authenticated session cookies, making the request appear legitimate to the server.

---

### **Example Scenario of CSRF**

#### **Scenario**: Changing a Password

Suppose a web application allows users to change their passwords using a URL like:

```
https://example.com/change-password?newPassword=newpass123
```

If the web application does not verify the authenticity of the request:

1. **Attacker Crafts a Malicious Request**:
   ```html
   <img src="https://example.com/change-password?newPassword=hackedpass" />
   ```

2. **Victim Execution**:
   - The attacker embeds the malicious code in their website or email.
   - When the victim views the page, the browser sends the request to `https://example.com`, including the victim’s session cookie.
   - The server processes the request, and the victim’s password is changed.

---

### **How an Attacker Performs CSRF**

1. **Identify a Vulnerable Action**:
   - Find actions (e.g., changing passwords, transferring money) that can be triggered by simple HTTP requests.

2. **Craft the Malicious Request**:
   - Create an HTTP request that performs the desired action. Examples:
     - **GET Request**:
       ```html
       <img src="https://example.com/transfer?amount=1000&toAccount=attacker" />
       ```
     - **POST Request**:
       ```html
       <form action="https://example.com/transfer" method="POST">
         <input type="hidden" name="amount" value="1000" />
         <input type="hidden" name="toAccount" value="attacker" />
         <input type="submit" />
       </form>
       ```

3. **Deliver the Payload**:
   - Embed the malicious code in a web page, email, or forum post.
   - Trick the victim into loading the malicious page or clicking a link.

4. **Leverage Authenticated Session**:
   - When the victim’s browser makes the request, it automatically includes the session cookies or authentication headers.
---

### **Preventing CSRF Attacks**

1. **CSRF Tokens**:
   - Include a unique token in forms and validate it on the server.
   - Example:
     ```html
     <input type="hidden" name="csrf_token" value="random_token" />
     ```
   - Server verifies the token before processing the request.

2. **SameSite Cookies**:
   - Use the `SameSite` attribute for cookies to restrict cross-origin requests:
     ```http
     Set-Cookie: sessionid=abc123; SameSite=Strict; Secure;
     ```

3. **Referer and Origin Header Validation**:
   - Check the `Referer` or `Origin` header to ensure the request originates from the same domain.

4. **User Authentication for Sensitive Actions**:
   - Require users to re-enter credentials (e.g., passwords) for critical actions.

5. **CORS (Cross-Origin Resource Sharing)**:
   - Configure CORS policies to restrict cross-origin requests.

6. **Limit GET Requests**:
   - Use `POST` instead of `GET` for actions that modify server-side data.

---

### **Example of CSRF Prevention**

#### **Vulnerable Code**:
```javascript
app.post('/change-password', (req, res) => {
    const newPassword = req.body.newPassword;
    // Change password logic
    res.send('Password changed');
});
```

#### **Secure Code with CSRF Token**:
```javascript
app.post('/change-password', (req, res) => {
    const csrfToken = req.body.csrf_token;

    if (!isValidCsrfToken(csrfToken)) {
        return res.status(403).send('Invalid CSRF Token');
    }

    const newPassword = req.body.newPassword;
    // Change password logic
    res.send('Password changed');
});
```

----------

# **Authentication vs. Authorization**


### **Authentication**

**Definition**:  
Authentication is the process of verifying the identity of a user, system, or application attempting to access a resource.

**Key Points**:
- **Purpose**: Ensures that the entity accessing the system is who they claim to be.
- **Process**: The user provides credentials, such as:
  - Username and password
  - Biometric data (e.g., fingerprint, facial recognition)
  - Security tokens or OTPs (One-Time Passwords)
- **Outcome**: If the provided credentials match the stored information, the user is authenticated.

**Example**:
- Logging into an email account with a username and password.

---

### **Authorization**

**Definition**:  
Authorization determines what an authenticated user is allowed to do within the system.

**Key Points**:
- **Purpose**: Controls access to resources or actions based on permissions or roles.
- **Process**: After authentication, the system checks the user's access rights.
- **Outcome**: Grants or denies access to specific resources or actions.

**Example**:
- After logging into an email account, users with "admin" roles can manage account settings, while regular users can only send and receive emails.

---

### **Real-World Analogy**

Imagine going to a secured office building:

- **Authentication**: Showing your ID card to prove you are an employee.
- **Authorization**: The system checks your role and determines whether you can enter the server room or just the cafeteria.

---

### **Common Technologies and Standards**

#### **Authentication**:
- OAuth 2.0
- OpenID Connect
- SAML (Security Assertion Markup Language)
- JWT (JSON Web Tokens)

#### **Authorization**:
- RBAC (Role-Based Access Control)
- ABAC (Attribute-Based Access Control)
- Policies in frameworks like Spring Security or AWS IAM.

---

# **How many ways of authentication of web application** ?

There are several ways to authenticate users in web applications, each with its strengths and weaknesses. Below are the most common methods:

---

### **1. Password-Based Authentication**
- **Description**: The user provides a unique identifier (username) and a password.
- **Implementation**:
  - Store hashed passwords in the database.
  - Use secure algorithms like bcrypt or Argon2.
- **Pros**:
  - Simple and widely understood.
  - Easy to implement.
- **Cons**:
  - Vulnerable to weak passwords, brute force attacks, and password reuse.
  - Relies heavily on the user’s ability to remember strong passwords.

---

### **2. Multi-Factor Authentication (MFA)**
- **Description**: Requires two or more factors to verify identity:
  1. **Something you know**: Password, PIN.
  2. **Something you have**: OTP, hardware token, smartphone.
  3. **Something you are**: Biometric verification (fingerprint, facial recognition).
- **Implementation**:
  - Use third-party services like Google Authenticator or Twilio for OTPs.
- **Pros**:
  - Stronger security by combining factors.
  - Reduces the impact of stolen passwords.
- **Cons**:
  - Adds complexity to the user experience.
  - May require additional hardware or software.

---

### **3. Token-Based Authentication**
- **Description**: Users receive a token (e.g., JWT - JSON Web Token) after authentication, which they send with each request to verify their identity.
- **Implementation**:
  - Use JWT or OAuth 2.0.
  - Store tokens securely (e.g., in HTTP-only cookies or secure storage).
- **Pros**:
  - Stateless and scalable.
  - Works well with APIs and Single Page Applications (SPAs).
- **Cons**:
  - Token theft can lead to unauthorized access.
  - Expiration and renewal management required.

---

### **4. Biometric Authentication**
- **Description**: Uses physical characteristics like fingerprints, facial recognition, or retina scans.
- **Implementation**:
  - Use devices with biometric capabilities (e.g., smartphones with FaceID).
- **Pros**:
  - Convenient for users.
  - Difficult to replicate or steal.
- **Cons**:
  - Requires special hardware.
  - Privacy concerns and potential for spoofing.

---

### **5. OAuth-Based Authentication**
- **Description**: Allows users to log in using third-party accounts like Google, Facebook, or GitHub.
- **Implementation**:
  - Follow OAuth 2.0 or OpenID Connect protocols.
- **Pros**:
  - No need to store user credentials.
  - Simplifies the login process for users.
- **Cons**:
  - Dependence on third-party services.
  - Requires internet connectivity.

---

### **6. Certificate-Based Authentication**
- **Description**: Uses digital certificates issued by a trusted Certificate Authority (CA) to verify identity.
- **Implementation**:
  - TLS/SSL certificates for secure client-server communication.
- **Pros**:
  - Highly secure for enterprise environments.
  - Prevents impersonation and phishing.
- **Cons**:
  - Complex setup and management.
  - Certificate renewal is required.

---

### **7. Social Authentication**
- **Description**: Users authenticate via their social media accounts (e.g., "Login with Facebook").
- **Implementation**:
  - Use OAuth 2.0 or SDKs provided by the social media platform.
- **Pros**:
  - Quick and easy for users.
  - Reduces friction for signup and login.
- **Cons**:
  - Privacy concerns with sharing social media data.
  - Dependency on third-party services.

---

### **8. Single Sign-On (SSO)**
- **Description**: Allows users to authenticate once and gain access to multiple applications.
- **Implementation**:
  - Use protocols like SAML, OAuth, or OpenID Connect.
- **Pros**:
  - Simplifies access for users.
  - Reduces password fatigue.
- **Cons**:
  - Requires centralized identity management.
  - Single point of failure if not properly secured.

---

### **9. API Key Authentication**
- **Description**: Applications use API keys to identify and authenticate requests.
- **Implementation**:
  - Generate and store API keys securely.
- **Pros**:
  - Simple for machine-to-machine communication.
  - Easy to implement.
- **Cons**:
  - Vulnerable to key theft if not secured properly.
  - Limited usability for user-based authentication.

---

### **10. Device-Based Authentication**
- **Description**: Relies on trusted devices (e.g., smartphones, laptops) to verify users.
- **Implementation**:
  - Use cookies, tokens, or cryptographic keys stored on the device.
- **Pros**:
  - Convenient for returning users.
  - Reduces reliance on passwords.
- **Cons**:
  - Device loss or theft can lead to unauthorized access.

---

### **11. CAPTCHA Authentication**
- **Description**: Verifies human users by presenting challenges that are hard for bots to solve.
- **Implementation**:
  - Use CAPTCHA tools like reCAPTCHA.
- **Pros**:
  - Prevents automated attacks like brute force.
- **Cons**:
  - Can frustrate users if challenges are too difficult.

---

### **12. Behavioral Authentication**
- **Description**: Monitors user behavior patterns like typing speed, mouse movements, or location to verify identity.
- **Implementation**:
  - Use AI/ML algorithms for behavioral analysis.
- **Pros**:
  - Passive and non-intrusive.
  - Difficult for attackers to mimic.
- **Cons**:
  - Can have false positives/negatives.
  - Privacy concerns.

---

### **Choosing the Right Authentication Method**
The choice depends on:
- **Application Requirements**: High-security apps (e.g., banking) may use MFA or biometrics.
- **User Experience**: Social authentication or SSO for low-friction logins.
- **Resources**: Smaller projects may start with password-based authentication and scale up.

Combining multiple methods (e.g., password + OTP or biometrics + MFA) enhances security while balancing usability.

----------


# **Token-Based Authentication & JWT**

Token-Based Authentication is a modern approach for authenticating users in web applications. Instead of storing session information on the server, it uses stateless tokens to authenticate and authorize users, enhancing scalability and security.

---

### **Key Concepts**

1. **Token**:
   - A token is a unique string (often JSON Web Token, JWT) generated by the server upon successful authentication.
   - Tokens encode user information and have an expiration time to prevent indefinite usage.

2. **Stateless**:
   - The server does not maintain session state. The token contains all the information needed for authentication.

3. **Bearer Token**:
   - The token is passed as part of the `Authorization` header in HTTP requests:
     ```
     Authorization: Bearer <token>
     ```

---

### **How Token-Based Authentication Works**

#### **Step-by-Step Process**

1. **User Login**:
   - The user provides credentials (e.g., username and password) to the server via a login endpoint.

2. **Server Validation**:
   - The server validates the credentials (e.g., checks hashed password in the database).

3. **Token Generation**:
   - Upon successful authentication, the server generates a token (e.g., JWT) containing:
     - User ID or username.
     - Roles/permissions.
     - Expiration time.
     - Other metadata.
   - The token is signed with a private secret or public/private key pair to prevent tampering.

4. **Token Sent to Client**:
   - The server sends the token back to the client, typically in the response body or as a cookie.

5. **Client Stores Token**:
   - The client stores the token securely (e.g., in local storage or HTTP-only cookies).

6. **Client Requests Protected Resources**:
   - For subsequent requests, the client includes the token in the `Authorization` header:
     ```
     Authorization: Bearer <token>
     ```

7. **Server Validates Token**:
   - The server decodes the token and verifies:
     - Signature validity (ensures token integrity).
     - Expiration time (prevents reuse of expired tokens).
     - Claims (e.g., user roles/permissions).
   - If valid, the server processes the request; otherwise, it rejects it with an error (e.g., `401 Unauthorized`).

---

### **How Token-Based Authentication Secures a Web App**

#### **1. Eliminates Session State on Server**
- Tokens are stateless, meaning the server doesn't store session data, reducing memory overhead and potential vulnerabilities like session fixation.

#### **2. Protects Against Replay Attacks**
- Tokens include an expiration time (`exp` claim in JWT). Once expired, they cannot be reused.

#### **3. Prevents Tampering**
- Tokens are signed with a secret key (HMAC) or a public/private key pair (RSA/EC). Any modification invalidates the token.

#### **4. Mitigates Cross-Site Scripting (XSS)**
- If stored in HTTP-only cookies, tokens are inaccessible to JavaScript, reducing exposure to XSS attacks.

#### **5. Enforces Role-Based Access Control (RBAC)**
- Tokens can include user roles/permissions in their payload, enabling granular access control for different parts of the application.

#### **6. Ensures Secure Communication**
- Tokens should always be transmitted over HTTPS to prevent interception by attackers (e.g., man-in-the-middle attacks).

---

### **JSON Web Token (JWT): The Standard Token**

#### **Structure of a JWT**
A JWT consists of three parts, separated by dots (`.`):
```
Header.Payload.Signature
```

1. **Header**:
   - Specifies the algorithm used for signing and token type.
   ```json
   {
     "alg": "HS256",
     "typ": "JWT"
   }
   ```

2. **Payload**:
   - Contains claims (user data and metadata).
   ```json
   {
     "sub": "1234567890",
     "name": "John Doe",
     "role": "admin",
     "exp": 1693000000
   }
   ```

3. **Signature**:
   - Ensures the token's integrity. Generated using the header, payload, and a secret key.
   ```
   HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
   ```

---

### **Implementation Example**

#### **Backend (Node.js with Express and JWT)**

```javascript
const express = require('express');
const jwt = require('jsonwebtoken');

const app = express();
const secretKey = "mySecretKey";

app.use(express.json());

// Login endpoint
app.post('/login', (req, res) => {
    const { username, password } = req.body;

    // Validate user credentials
    if (username === "admin" && password === "password123") {
        const token = jwt.sign(
            { username, role: "admin" }, 
            secretKey, 
            { expiresIn: "1h" }
        );
        return res.json({ token });
    }

    res.status(401).send("Invalid credentials");
});

// Protected route
app.get('/protected', (req, res) => {
    const token = req.headers.authorization?.split(' ')[1];

    if (!token) return res.status(401).send("Access denied");

    try {
        const decoded = jwt.verify(token, secretKey);
        res.json({ message: "Access granted", user: decoded });
    } catch (error) {
        res.status(401).send("Invalid token");
    }
});

app.listen(3000, () => console.log("Server running on http://localhost:3000"));
```

---

### **Security Best Practices**

1. **Use HTTPS**:
   - Always transmit tokens over secure HTTPS connections.

2. **Secure Token Storage**:
   - Use HTTP-only and Secure cookies for storage to protect against XSS.
   - Avoid storing tokens in localStorage if possible.

3. **Short Token Lifespan**:
   - Set tokens to expire quickly (e.g., 15 minutes) and use refresh tokens for long-lived sessions.

4. **Rotate Signing Keys**:
   - Periodically change the secret key used to sign tokens.

5. **Validate Claims**:
   - Ensure tokens contain valid claims like `iss` (issuer) and `aud` (audience) to prevent misuse.

6. **Revoke Tokens**:
   - Use blacklists or token versioning to invalidate compromised tokens.

7. **Use Refresh Tokens**:
   - Issue a short-lived access token and a long-lived refresh token for session management.

---

### **Advantages**

- Stateless and scalable.
- Simplifies authentication for APIs and SPAs.
- Facilitates single sign-on (SSO).

### **Disadvantages**

- Requires proper token management.
- Vulnerable to token theft if not securely implemented.

By following these practices, Token-Based Authentication ensures secure and efficient access control for web applications.

----

# **How Can I be sure that my angular banking application is safe and secure?**

To ensure the safety and security of your Angular-based banking web application, you need to adopt a comprehensive security strategy addressing multiple layers of the application. Here's a structured response tailored for an architect-level interview:

---

### **1. Secure the Application Codebase**
- **Input Validation**: Validate and sanitize all user inputs to prevent SQL injection, XSS, and other attacks.
- **Angular Security Features**: Leverage Angular's built-in protections:
  - Use **Angular's DomSanitizer** for safe HTML, URLs, and resources.
  - Avoid using `bypassSecurityTrust...` methods unless absolutely necessary.
  - Enable **strict template checking** in `tsconfig.json`.
- **Secure Coding Practices**:
  - Avoid embedding sensitive information in the client-side code.
  - Use environment variables for configuration, and avoid hardcoding secrets.

---

### **2. Prevent Cross-Site Scripting (XSS)**
- **Output Encoding**: Rely on Angular's automatic output encoding to prevent XSS.
- **Content Security Policy (CSP)**: Implement a strict CSP to control the sources of scripts and resources.
- **Third-Party Libraries**: Validate and regularly update third-party libraries to mitigate supply chain vulnerabilities.

---

### **3. Mitigate Cross-Site Request Forgery (CSRF)**
- Angular provides built-in support for CSRF tokens. Ensure:
  - Your backend API generates and validates CSRF tokens.
  - The Angular app includes the CSRF token in each request header.

---

### **4. Protect Sensitive Data**
- **Secure Communication**: Use HTTPS for all data transmission.
- **Encryption**:
  - Encrypt sensitive data at rest using backend storage solutions.
  - Encrypt data in transit using protocols like TLS.
- **Sensitive Data in Forms**: Mask sensitive fields like passwords and PINs. Use `input[type="password"]`.

---

### **5. Authentication and Authorization**
- **Authentication**:
  - Implement secure authentication mechanisms like OAuth 2.0 or OpenID Connect.
  - Use **token-based authentication** (e.g., JWT), and store tokens securely in HTTP-only cookies or browser secure storage.
  - We use refresh token approach and generate token after every 5 minutes.
  - We check user activity to the site and If he is not active from last 10 minutes, we logged out the session.
  - We don't allow refresh feature of browser, because it is SPA and refresh is not required to access any part of application.
- **Authorization**:
  - Implement **role-based access control (RBAC)** to restrict resource access.
  - Use route guards (`CanActivate`, `CanLoad`) in Angular to secure application routes.

---

### **6. API and Backend Security**
- **Input Validation on Server**: Do not rely solely on client-side validation. Perform robust validation and sanitization on the server side.
- **Rate Limiting and Throttling**: Prevent brute force attacks on sensitive endpoints.
- **Error Handling**: Avoid exposing stack traces or sensitive details in error messages.

---

### **7. Secure Application Infrastructure**
- **Dependency Management**:
  - Regularly update dependencies and monitor vulnerabilities using tools like `npm audit` or OWASP Dependency-Check.
- **Static Analysis**:
  - Use static analysis tools like SonarQube or ESLint with security plugins.
- **Deployment Security**:
  - Configure secure headers like HSTS, X-Content-Type-Options, and X-Frame-Options using the backend server.

---

### **8. Implement Logging and Monitoring**
- **Centralized Logging**: Log security events, such as login failures and unauthorized access attempts, using a centralized logging system.
- **Monitoring and Alerts**: Use tools like ELK Stack, Splunk, or New Relic to monitor application behavior and detect anomalies.

---

### **9. Perform Regular Security Testing**
- **Automated Security Scans**:
  - Use tools like OWASP ZAP or Burp Suite for vulnerability scanning.
- **Penetration Testing**:
  - Conduct regular penetration tests to identify and mitigate vulnerabilities.
- **Security Audits**:
  - Perform periodic security reviews of the code and infrastructure.

---

### **10. Educate and Train Development Teams**
- Ensure all developers are trained in secure coding practices.
- Foster a culture of security awareness within the team.

---

### **11. Comply with Banking Security Standards**
- Follow industry standards such as PCI DSS for handling payment-related data.
- Ensure compliance with local banking and data protection regulations like GDPR or CCPA.

---

# **What can happen if somehow active token are stolen? And how to mitigate such risk?**

Yes, if an attacker manages to steal an active token, they can potentially perform suspicious activities, depending on the type of token and how it is implemented. Here's a detailed breakdown of what can happen and how to mitigate such risks:

---

### **1. What Can an Attacker Do With a Stolen Token?**
#### **If Token Is Not Properly Secured**:
- **Impersonation**: 
  - The attacker can impersonate the legitimate user and access resources or perform actions authorized for that user.
- **Privilege Escalation**:
  - If the token includes roles or permissions, the attacker can exploit elevated privileges (e.g., perform administrative actions).
- **Session Hijacking**:
  - The attacker can take over the user's session until the token expires or is revoked.

#### **Even with Expiry**:
- If the token has a long expiration period and no refresh mechanism is in place, the attacker has an extended window to misuse it.

---

### **2. Mitigation Strategies**

#### **Secure Token Transmission**:
1. **Use HTTPS**:
   - Always use HTTPS to encrypt tokens during transit, making it difficult for attackers to intercept them.

#### **Store Tokens Securely**:
2. **Use Secure and HTTP-Only Cookies**:
   - Avoid storing tokens in localStorage or sessionStorage, as they are vulnerable to XSS attacks.
   - Store tokens in HTTP-only cookies, which are inaccessible to JavaScript and protected from XSS.

#### **Limit Token Usage**:
3. **Restrict Token Scope**:
   - Include specific scopes in the token to limit its access to particular resources or actions.
   - Use audience (`aud`) and issuer (`iss`) claims in JWT to ensure the token is used only with intended APIs.

4. **Set Short Expiration Times**:
   - Use short-lived access tokens (e.g., 15 minutes) and implement refresh tokens to reduce the impact of token theft.

---

### **3. Detect and Prevent Token Misuse**

#### **Token Revocation**:
- Implement mechanisms to revoke tokens (e.g., blacklist stolen tokens or use token versioning).

#### **IP and Device Binding**:
- Tie tokens to specific IP addresses or devices, making them unusable from different locations or devices.

#### **Anomaly Detection**:
- Monitor for unusual activity, such as:
  - Requests from unexpected IP addresses or geolocations.
  - Abnormal patterns of usage (e.g., excessive API calls).

#### **Token Signature Validation**:
- Verify the token's signature to ensure it hasn't been tampered with.

---

### **4. Best Practices for Token Design**

#### **Use Claims Wisely**:
- Avoid storing sensitive information (e.g., passwords, PII) in tokens.
- Include claims like `exp` (expiration), `iat` (issued at), and `nbf` (not before) to control token validity.

#### **Refresh Tokens**:
- Use refresh tokens with a secure mechanism to issue new access tokens.
- Store refresh tokens securely and rotate them periodically.

#### **Implement Content Security Policy (CSP)**:
- Prevent malicious scripts from executing in the browser to protect against XSS attacks.

---

### **5. Even With a Stolen Token, How to Reduce Impact?**

1. **Audit Logs**:
   - Keep detailed logs of token usage to trace and identify suspicious activity.

2. **Secondary Authentication**:
   - Use multi-factor authentication (MFA) to add an additional layer of security.

3. **Limit Token Use in Critical Actions**:
   - Require re-authentication for sensitive actions, such as money transfers or password changes.

---


# **Claims in JWT**

A JSON Web Token (JWT) contains **three parts**: a header, a payload, and a signature. Within the **payload**, claims are used to provide statements about an entity (typically a user) and additional metadata. Claims can be broadly categorized into **three types**:

---

### **1. Registered Claims**
These are a set of predefined claims that are optional but recommended for standardization and interoperability. 

#### Common Registered Claims:
- **`iss` (Issuer)**: Identifies the principal that issued the token.  
  *Example*: `"iss": "auth.example.com"`
- **`sub` (Subject)**: Identifies the subject of the token (e.g., a user ID).  
  *Example*: `"sub": "1234567890"`
- **`aud` (Audience)**: Identifies the recipients for whom the token is intended.  
  *Example*: `"aud": "example-app"`
- **`exp` (Expiration Time)**: Specifies the expiration time of the token (in UNIX timestamp format).  
  *Example*: `"exp": 1672531199"`
- **`nbf` (Not Before)**: Specifies the time before which the token must not be accepted.  
  *Example*: `"nbf": 1672520000"`
- **`iat` (Issued At)**: Indicates when the token was issued (in UNIX timestamp format).  
  *Example*: `"iat": 1672526400"`
- **`jti` (JWT ID)**: A unique identifier for the token to prevent replay attacks.  
  *Example*: `"jti": "unique-token-id"`

---

### **2. Public Claims**
These are custom claims that you define and register with the **IANA JSON Web Token Registry** to avoid conflicts.

#### Example:
```json
{
  "role": "admin",
  "organization": "example-corp"
}
```

- Public claims can be useful for sharing common information between systems, but it's essential to avoid collisions by using unique claim names.

---

### **3. Private Claims**
These are custom claims defined and used by parties that agree to use them. They are not registered or standardized.

#### Example:
```json
{
  "userId": "abc123",
  "plan": "premium",
  "features": ["feature1", "feature2"]
}
```

- Private claims are often used for internal purposes, such as passing user-specific data between services.

---

### **Best Practices for JWT Claims**
1. **Minimize Payload Size**:
   - Include only essential claims to reduce the token size.
   
2. **Secure Sensitive Data**:
   - Avoid including sensitive information (e.g., passwords, PII) in the payload, as JWTs can be decoded by anyone with access to the token.

3. **Use Strong Validation**:
   - Validate claims such as `exp`, `nbf`, and `iss` to ensure the token is valid and trustworthy.

4. **Namespace Custom Claims**:
   - Prefix custom claims with a unique namespace to prevent conflicts.
   - *Example*: `"custom:role": "admin"`

---

### **Summary**
JWT claims are grouped into:
- **Registered claims**: Predefined, standard claims (`iss`, `sub`, `exp`, etc.).
- **Public claims**: Custom claims registered with IANA.
- **Private claims**: Custom claims agreed upon by parties for specific use cases.

You can define as many claims as needed, but it’s essential to balance functionality, performance, and security.





