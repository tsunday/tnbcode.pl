+++
categories = ["python"]
comments = false
date = "2017-02-01T23:00:13-00:00"
draft = false
showpagemeta = true
showcomments = true
slug = ""
tags = ["python", "python3.7", "coding", "software", "security", "owasp", "django"]
title = "Django OWASP compliance"
description = "Django comes with built-in security mechanisms. In this article I'm going to compare them with OWASP directives"

+++


## General steps:

Hardening of django application can be verified with:
https://pypi.python.org/pypi/django-secure/
https://www.ponycheckup.com/
https://www.htbridge.com/websec/
https://pypi.python.org/pypi/dependency-check/  

Deployment checklist recommended by OWASP:
https://docs.djangoproject.com/en/1.11/howto/deployment/checklist/

## OWASP Top 10 Audit:

### 1. Injection

Django is using ORM models to separate the users from creating SQL queries for they own. Django implements querysets with parametrization which additionaly protects the database to be attacked by malicious input.  

> https://docs.djangoproject.com/en/2.0/topics/security/#sql-injection-protection

### 2. Broken Authentication

Django apps use strict password policies. They check for password complexity, length, similarity to user attributes or to one of the 1000 common passwords.

> https://docs.djangoproject.com/en/2.0/topics/auth/passwords/#enabling-password-validation

### 3. Sensitive Data Exposure

Most of django deployments qre complient with this requirements because there is TSL encryption turned on for all network traffic. Passwords stored in the database are encrypted with PBKDF2 algorithm with SHA256 hash defautly in Django. This way passwords are securily obfuscated with salted hashing.

> https://docs.djangoproject.com/en/2.0/topics/auth/passwords/#how-django-stores-passwords

### 4. XML External Entities (XXE)

Django uses simpler data formats like JSON in its API endpoints. That mitigates the risk of attack with uploading malicious XML file. Even though Django is still very nice prepared for handling XML inputs with server-side form validation, data serialization and more. Any XML vulnerabilities that existed in previous versions of the framework were fixed in Django 1.5

> https://www.djangoproject.com/weblog/2013/feb/19/security/

### 5. Broken Access Control

Django implements extensive permission model with superuser and staff roles. Permissions can be extended by custom rules and such behavior is commmonly used in your application.

### 6. Security Misconfiguration

It's always a good idea to regularly test your application with security testing tools. Results from such tests reveal any security misconfigurations, which are immediately being fixed. OWASP also recommend to follow the Django Deployment checklist. Developers can also use an automatic tool that comes with django-secure library that checks security vulnerabilities of the application.

> https://www.htbridge.com/websec/
> https://www.owasp.org/index.php/SCG_WF_Django
> https://docs.djangoproject.com/en/1.11/howto/deployment/checklist/
> https://pypi.python.org/pypi/django-secure/

### 7. Cross-Site Scripting (XSS)

Django provides built-in support for escaping XSS with proper security headers and filters. You can additionally use django-csp library to cover Content Security Policy requirements. Third-party scripts used by Cube are strictly limited to specified list of public providers.

> https://docs.djangoproject.com/en/2.0/topics/security/#cross-site-scripting-xss-protection
> https://django-csp.readthedocs.io/

### 8. Insecure Deserialization

Django API is using JSON objects with primitive values, which shouldn't generate any of insecure deserialization issues. Application uses deserializators from Django REST framework.

> http://www.django-rest-framework.org/tutorial/1-serialization/

### 9. Using Components with Known Vulnerabilities

Developers should use only official sources of third-party libraries and stays up to date with currently supported versions. Developers keeps an eye on the changes in that matter and when any of the libraries comes outdated the problem is being solved with proper upgrade. Django applications should also be supplied with automatic dependency-check tool that confronts used libraries with the list of National Vulnerability Database (NVD) from NIST.

> https://pypi.python.org/pypi/dependency-check/  
> https://nvd.nist.gov/

### 10. Insufficient Logging & Monitoring

Django framework instantly logs any action (requests, failures etc.) taken by the user. Developers should also be familiar with company regulations in the matter of Incident Handling. Such regulations are based on NIST 800-61 rev 2 document.

> https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf
> https://docs.djangoproject.com/en/2.0/topics/logging/


