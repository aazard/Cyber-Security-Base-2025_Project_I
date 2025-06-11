# Cyber Security Base 2025 - Project I

Basic Information
This project aligns with the OWASP Top 10 - 2021 list, addressing key vulnerabilities. It tackles A03:2021-Injection (noted twice for its significance), A01:2021-Broken Access Control, A05:2021-Security Misconfiguration, A07:2021-Identification and Authentication Failures, and A02:2021-Cryptographic Failures. Screenshots are available in the /screenshots directory (https://github.com/aazard/Cyber-Security-Base-2025_Project_I/tree/main/screenshots). Two sample products and reviews are preloaded in the database; additional entries can follow step seven’s instructions.

User Accounts for Testing
Superuser (admin with /admin/ access):
Username: jane
Password: janeloveshopping
Normal user (create more via /register/, avoid superuser login during this):
Username: harry
Password: harry
Maiden name: evans
Setup Instructions
Ensure Python is installed. Clone the repository:

text

Collapse

Wrap

Copy
git clone https://github.com/aazard/Cyber-Security-Base-2025_Project_I.git
Navigate and activate the virtual environment:

text

Collapse

Wrap

Copy
cd mysite
python -m venv venv
venv\Scripts\activate
Install dependencies:

text

Collapse

Wrap

Copy
pip install django
Initialize the database:

text

Collapse

Wrap

Copy
python manage.py makemigrations store
python manage.py migrate
Create a superuser:

text

Collapse

Wrap

Copy
python manage.py createsuperuser
Run the server:

text

Collapse

Wrap

Copy
python manage.py runserver
Access the site via the displayed link. Add reviews by clicking "View Details" on the homepage, or products via /admin/ under Store/Products.

Flaw Details and Solutions
Flaw 1: A03:2021-Injection
Location: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L426
Description: The search_reviews function suffers from SQL injection due to unsanitized user input in a raw SQL query, e.g., ' OR '1'='1 could return all reviews.
Solution: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L446
Fix: Uses Django’s ORM filter(comment__icontains=query) for parameterized, sanitized queries.
Flaw 2: A01:2021-Broken Access Control
Location: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L330
Description: The api_product_info endpoint lacks authentication, exposing product data to all users.
Solution: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L345
Fix: Adds @login_required and permission checks.
Flaw 3: A05:2021-Security Misconfiguration
Location: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/mysite/settings.py#L23
Description: Issues include enabled debug mode, hardcoded secret key, wildcard ALLOWED_HOSTS, disabled password validation, CSRF, and clickjacking protection, risking exposure and attacks.
Solution: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/mysite/settings.py#L130
Fix: Uses environment variables for secrets, disables debug in production, restricts hosts, and enables protections.
Flaw 4: A03:2021-Injection
Location: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L365
Description: The admin_command function allows command injection with shell=True, risking arbitrary command execution.
Solution: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L378
Fix: Implements a whitelist of commands, sets shell=False, and suggests a UI dropdown.
Flaw 5: A07:2021-Identification and Authentication Failures
Location: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L148
Description: Lacks CSRF protection, rate limiting, email verification, password complexity, logging, and secure session handling, facilitating attacks.
Solution: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L194
Fix: Adds CSRF, rate limiting via failed_attempts, generic error messages, and suspicious login checks.
Flaw 6: A02:2021-Cryptographic Failures
Location: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L48, https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/models.py#L33
Description: Security answers and password resets were stored in plaintext, exposed via /debug-security/, with unhashed password updates.
Solution: https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/views.py#L71, https://github.com/aazard/Cyber-Security-Base-2025_Project_I/blob/main/mysite/store/models.py#L35
Fix: Hashes security answers with make_password(), uses set_password() for resets, adds timing attack protection, and ensures consistent error messages.
The effectiveness of these solutions is confirmed by inspecting the `/debug-security/` endpoint _after_ the fixes are applied. 
