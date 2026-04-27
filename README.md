# Handling-Security-in-Java-Applications
🛡️ Complete Guide: Handling Security in Java Applications
Security in Java applications is not just one feature — it’s a layered system. A medium-level company backend must protect:

User data
Authentication & authorization
APIs
Database
Server

If you miss even one layer, your system becomes vulnerable.

🔐 1. Authentication (Who are you?)

Authentication verifies the user identity.

✅ Best Practice: Use JWT (JSON Web Tokens)
🔹 Example (Basic JWT Generation)
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import java.util.Date;

public class JwtUtil {

    private static final String SECRET_KEY = "mySecretKey";

    public static String generateToken(String username) {
        return Jwts.builder()
                .setSubject(username)
                .setIssuedAt(new Date())
                .setExpiration(new Date(System.currentTimeMillis() + 86400000)) // 1 day
                .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
                .compact();
    }
}
🔐 2. Authorization (What can you do?)

After login, control what users can access.

✅ Use Role-Based Access Control (RBAC)
if(user.getRole().equals("ADMIN")) {
    // allow admin access
} else {
    throw new SecurityException("Access Denied");
}

👉 Better approach (Spring Security style):

@PreAuthorize("hasRole('ADMIN')")
public void deleteUser() {
    // only admin can access
}
🔐 3. Password Security (Never store plain passwords ❌)
✅ Use BCrypt Hashing
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();

String hashedPassword = encoder.encode("userPassword");

// Validate
boolean match = encoder.matches("userPassword", hashedPassword);

👉 Why?

Even if DB is hacked → passwords are safe
🔐 4. Secure APIs (Prevent attacks)
🔹 Common Attacks:
SQL Injection
XSS
CSRF
✅ Prevent SQL Injection

❌ Wrong:

String query = "SELECT * FROM users WHERE name = '" + name + "'";

✅ Correct:

PreparedStatement ps = conn.prepareStatement(
    "SELECT * FROM users WHERE name = ?");
ps.setString(1, name);
✅ Prevent XSS (Cross Site Scripting)

Always sanitize input:

import org.apache.commons.text.StringEscapeUtils;

String safeInput = StringEscapeUtils.escapeHtml4(userInput);
✅ CSRF Protection (Spring Security auto handles)
http.csrf().disable(); // only disable for APIs if needed carefully
🔐 5. Secure Communication (HTTPS 🔥)

Always use:

HTTPS instead of HTTP

👉 Why?

Encrypts data between client & server
Prevents man-in-the-middle attacks
🔐 6. Input Validation (Very Important)

Never trust user input.

if(email == null || !email.contains("@")) {
    throw new IllegalArgumentException("Invalid Email");
}

👉 Use libraries:

Hibernate Validator
Bean Validation (JSR-380)
@NotNull
@Email
private String email;
🔐 7. Exception Handling (Don’t leak data)

❌ Wrong:

e.printStackTrace();

✅ Correct:

logger.error("Error occurred", e);
throw new RuntimeException("Something went wrong");

👉 Never expose:

DB queries
Stack traces
Internal logic
🔐 8. Session Management
Best Practice:
Use JWT instead of session
If using session → set timeout
session.setMaxInactiveInterval(15 * 60); // 15 mins
🔐 9. Secure Headers

Add security headers:

response.setHeader("X-Content-Type-Options", "nosniff");
response.setHeader("X-Frame-Options", "DENY");
response.setHeader("X-XSS-Protection", "1; mode=block");
🔐 10. Logging & Monitoring

Track suspicious activity:

logger.warn("Multiple failed login attempts for user: " + username);

👉 Use tools:

ELK Stack
Prometheus
Grafana
🏗️ Real Medium-Level Company Architecture

A proper secure Java backend looks like this:

Client (React / Angular)
        ↓
API Gateway (Auth + Rate Limit)
        ↓
Spring Boot Backend
        ↓
Security Layer (JWT, RBAC)
        ↓
Service Layer
        ↓
DAO Layer (Prepared Statements)
        ↓
Database (Encrypted + Hashed Passwords)
🚀 Final Thoughts (Important)

Security is not a feature — it’s a mindset.

A strong Java developer always ensures:

No plain passwords
No raw queries
No open endpoints
No unvalidated input
