# Session Management Cheat Sheet

References:

- [OWASP Cheat Sheet Series: Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

What is a Session? Sequence of network HTTP request and response transactions associated with the same user.

Web applications can create sessions to keep track of anonymous users after the very first user request (e.g. user language preference).

Http is a stateless protocol, where each request nad response pair is independent of other web interactions.

Session Hijacking Attack Types:

1. Targeted: impersonate a specific victim user
2. Generic: impoersonate any valid or legitimate user

### Session ID

Session Identifier (Session ID or token) assigned at session creation time, and is shared and exchanged by the user and the web application for the duration of the session(sent on every HTTP request).

secure session id properties:

1. Session ID name figerprint: should not be extremely descriptive nor offer unnecessary details about the purpose and meaning of the ID.

[common session id fingerprints](https://wiki.owasp.org/index.php/Category:OWASP_Cookies_Database)

**Note:** It is recommended to change the default session id name to a generic name(e.g. `id`).

2. Session ID Entropy

session identifiers must have at least `64 bits` of entropy (`2^64` possible values for each session id).

[brute force session identifiers time analysis](https://owasp.org/www-community/vulnerabilities/Insufficient_Session-ID_Length#estimating-attack-time)

3. Session ID Length

4. Session ID Content

The Session ID content(or value) must be meaningless to prevent information disclosure attacks.

The stores information can include the client IP address, User-Agent, e-mail, username, user ID, role, privilege level, access rights, language preferences, account ID, current state, last login, session timeouts, and other internal session details.

**Note:** It is recommended to use the session ID created by your language or framework.

### Session Management Implementation

The session management implementation defined the exchange mechanism that will be used between the user and the web application to share and continuously exchange the session ID.

[Session fixation attakcs(id manipulation attack)](https://www.acrossecurity.com/papers/session_fixation.pdf)
