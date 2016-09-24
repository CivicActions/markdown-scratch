# PROJECT Overview

*Note: this "develop" directory contains templates that could be used for many CivicActions projects. This front page readme.md file contains the required Security Prologue (alternate name suggestions welcome), pointers to documents in the docs directory, and project-specific information. The contents of this folder can be copied into the base of a project dir (so that the `docs` folder resides next to `docroot`). Search for "`CLIENT`" and "`PROJECT`" in the copied files and replace with the client and project names, respectively. Of course, feel free to make pull requests against these source files to help them become more general and more comprehensive.*

*But first: where should this doc/script template go? A new or existing github repository? Gitlab? And does it make sense to mix scripts with docs in the same repo?*

Table of Contents
=================

  * [Security Prologue](#security-prologue)
    * [Security Requirements for All Projects](#security-requirements-for-all-projects)
  * External Links
    * [Creating Your Sandbox](docs/sandbox.md)
    * [Git Workflow](docs/workflow.md)
    * [Behat Testing Instructions](tests/behat/)

# Security Prologue
As a CivicActions Employee or Contractor, you have read and agreed to abide by the [CivicActions Security Policy](https://github.com/CivicActions/security-policy)

CivicActions Developers are expected to know, understand and integrate into your work:
- [Drupal coding standards and best practices](https://www.drupal.org/developing/best-practices).
- [How to write secure code in Drupal](https://www.drupal.org/writing-secure-code)

## Security Requirements for All Projects

CivicActions has defined a set of security requirements to ensure the security of the code produced by its developers. Most of these requirements are defined in the [CivicActions Security Policy](https://github.com/CivicActions/security-policy) but they are repeated here for clarity:
- Maintain up-to-date versions of software on all systems (sandbox, development, staging and production). This includes:
 - Drupal
 - Operating System (GNU/Linux; some developers may use Mac OSX)
 - Editors, debuggers, and other tools
- Always apply relevant CVE patches within 30 days of their release, usually within two weeks
- Limit live data on development machines. In particular:
 - no user PII (other than test PII created by developers)
 - no copyrighted content
 - no access restricted files
- Custom code must conform to [Drupal coding](https://www.drupal.org/docs/develop/standards) and [security standards](https://www.drupal.org/docs/7/security/writing-secure-code/overview)
- Before being integrated into the development branch, all code updates must:
 - have an associated ticket in JIRA
 - undergo static code analysis with PHP_CodeSniffer (exceptions for CSS, features, perhaps others)
 - peer review
 - full automated testing 

_**If you have questions about any of the above or the linked documents, ask your Project Manager or Tech Lead.**_


