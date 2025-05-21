# 🔒 Secure Code Review & Vulnerability Remediation in Django

This project, completed during my **#cadealpha** **#internship** in **#cybersecurity**, demonstrates practical experience in conducting secure code reviews and implementing remediation strategies for web applications. The focus of this work was on identifying and fixing common vulnerabilities within a sample **Python Django blog application**.

---

## 🚀 Project Goal

The primary objective was to thoroughly review a Django application's codebase to pinpoint security weaknesses, propose concrete solutions based on best practices, and understand how to leverage security tools for static and dynamic analysis.

---

## 💻 The Sample Django Application

I worked with a simplified Django blog application featuring:

- **Article Listing:** Displaying all published articles.
- **Article Detail View:** Showing the content of a specific article.
- **Commenting System:** Allowing authenticated users to post comments on articles.

### 📁 Key Files Reviewed:

Here are the core files that were part of the secure code review process:

- `blog/models.py`
- `blog/views.py`
- `blog/forms.py`
- `blog/templates/blog/article_detail.html`
- `blog/templates/blog/add_comment.html`

---

## 🔍 Manual Code Review & Identified Vulnerabilities

Through manual inspection, I identified critical security vulnerabilities:

### 1. SQL Injection 💥

- **Location:** `blog/views.py`, specifically in the `search_articles` function.
- **Issue:** The direct use of `cursor.execute(f"SELECT * FROM blog_article WHERE title LIKE '%%{query}%%' OR content LIKE '%%{query}%%'")` with unvalidated user input (`request.GET.get('q')`) creates a classic SQL Injection flaw. A malicious query could lead to unauthorized data access or database manipulation.
- **Remediation:** Replaced raw SQL with Django's **Object-Relational Mapper (ORM)**: `Article.objects.filter(Q(title__icontains=query) | Q(content__icontains=query))`. Django's ORM handles secure parameterization, preventing injection.

### 2. Cross-Site Scripting (XSS) 😈

- **Location:** `blog/templates/blog/article_detail.html`.
- **Issue:** The `<div>{{ article.content|safe }}</div>` template tag explicitly disables Django's auto-escaping. If `article.content` contained malicious JavaScript, it could execute in other users' browsers, leading to session hijacking or defacement.
- **Remediation:** Removed the `|safe` filter to re-enable Django's **automatic escaping**. For scenarios requiring legitimate HTML, integrated a robust sanitization library like `bleach` to clean content before rendering, ensuring only safe HTML tags and attributes are allowed.

### 3. Cross-Site Request Forgery (CSRF) 🔄

- **Location:** General form handling within the application.
- **Issue:** While `blog/add_comment.html` included `{% csrf_token %}`, it's vital to ensure _all_ POST forms use this tag to prevent CSRF attacks, where an attacker could trick an authenticated user into performing unintended actions.
- **Remediation:** Verified the `django.middleware.csrf.CsrfViewMiddleware` is enabled in `settings.py` (default for new Django projects) and confirmed consistent use of `{% csrf_token %}` across all POST forms.

---

## ⚙️ Static Code Analysis with Bandit

To complement manual review, I utilized **Bandit**, a static analysis tool for Python code.

- **Command:** `bandit -r .` (executed in the project root).
- **Bandit's Findings:** Bandit successfully flagged potential issues like:
  - `./blog/views.py:28:0:B608: Possible SQL injection vulnerability via parameter query (High)`
  - `./blog/templates/blog/article_detail.html:3:5:S308: Django template autoescape is off for variable article.content (Medium)`

This demonstrated the effectiveness of static analysis in automating vulnerability detection.

---

## ✅ Secure Coding Best Practices & Recommendations

Beyond fixing immediate issues, the project reinforced key secure coding principles for Django:

- **Always use Django ORM:** Prefer `Model.objects` for database interactions to prevent SQL injection.
- **Embrace Django's auto-escaping:** Never disable `|safe` unless content is rigorously sanitized.
- **CSRF Protection:** Always include `{% csrf_token %}` in all POST forms.
- **Built-in Authentication/Permissions:** Leverage Django's robust `django.contrib.auth` system.
- **Stay Updated:** Regularly update Django and third-party libraries to patch known vulnerabilities.
- **Secure Configuration:** Properly configure `settings.py` parameters like `SECRET_KEY`, `DEBUG`, and `ALLOWED_HOSTS` for production environments.

---

## 📈 Learning & Impact

This internship at CodeAlpha provided invaluable hands-on experience in the practical aspects of application security. I gained a deeper understanding of common web vulnerabilities, learned to apply secure coding patterns in a real-world Django context, and utilized industry-standard security tools. This project significantly enhanced my ability to write, review, and maintain more secure codebases.
