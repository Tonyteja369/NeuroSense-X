# Security Policy

## NEUROSENSEX Cranial Mechanical Resonance Mapping (CMRM)

### 1. Overview

Security and data privacy are important considerations in the development of the Cranial Mechanical Resonance Mapping (CMRM) platform.

CMRM integrates embedded hardware, sensor acquisition, firmware, desktop software, signal processing and experimental data storage.

This document defines the security principles and recommended practices for protecting the system, its software components and experimental data.

CMRM is an experimental biomedical engineering research prototype and is not a clinically validated medical device.

---

## 2. Supported Versions

Security updates should be prioritized for actively maintained development versions.

| Version               | Security Support   |
| --------------------- | ------------------ |
| Development branch    | Active development |
| Latest stable release | To be defined      |
| Previous releases     | Not guaranteed     |

Update this table when official releases are established.

---

## 3. Security Principles

The project follows these core security principles:

1. Data confidentiality.
2. Data integrity.
3. System availability.
4. Secure communication.
5. Secure firmware deployment.
6. Controlled access.
7. Privacy-conscious data management.
8. Secure configuration.
9. Responsible vulnerability disclosure.

---

## 4. Hardware Security

### 4.1 ESP32 Security

The ESP32 is the primary embedded controller.

Recommended security mechanisms, where supported by the selected ESP32 variant, include:

* Secure Boot.
* Flash Encryption.
* Firmware integrity verification.
* Restricted debugging interfaces.
* Secure provisioning.
* Controlled firmware updates.

Security configuration must be verified against the exact ESP32 chip variant.

### 4.2 Wireless Communication

Wireless communication should remain disabled when it is not required.

When wireless communication is enabled:

* Use authenticated connections.
* Avoid transmitting sensitive information over unsecured channels.
* Protect network credentials.
* Use encrypted communication protocols.
* Restrict access to authorized devices.

### 4.3 Physical Security

Physical access to the hardware should be controlled during experiments.

Recommended practices:

* Protect exposed programming interfaces.
* Secure the development board.
* Prevent unauthorized firmware modification.
* Protect removable storage.
* Avoid exposing debugging interfaces in deployed configurations.

---

## 5. Firmware Security

Firmware is responsible for sensor communication, experimental control and data acquisition.

Recommended practices:

* Validate incoming commands.
* Reject malformed communication packets.
* Avoid hardcoded credentials.
* Use safe default configurations.
* Handle communication failures.
* Validate sensor responses.
* Prevent uncontrolled experimental activation.
* Maintain firmware version information.
* Verify firmware integrity before deployment.

Firmware should not activate the mechanical excitation subsystem without appropriate experimental authorization and safety checks.

---

## 6. Software Security

The desktop application should follow secure software development practices.

### Authentication and Access Control

Where authentication is implemented:

* Use secure password-handling mechanisms.
* Apply role-based access control where required.
* Restrict access to sensitive experimental records.
* Avoid storing plaintext passwords.
* Implement appropriate session management.

### Input Validation

All external inputs should be validated.

This includes:

* Sensor data.
* Serial communication packets.
* Configuration files.
* Dataset imports.
* User inputs.
* Export parameters.

### Dependency Management

Software dependencies should be reviewed regularly.

Recommended practices:

* Use trusted package repositories.
* Avoid unnecessary dependencies.
* Update vulnerable packages.
* Maintain dependency versions.
* Review third-party library permissions.

---

## 7. Data Privacy and Protection

CMRM may eventually handle sensitive experimental information.

The following principles should be followed.

### Data Minimization

Collect only the information necessary for the intended experiment.

### Pseudonymization

Use experiment identifiers and pseudonymous session IDs instead of directly identifying participants.

### Local Data Storage

Local experimental data should be protected against unauthorized access.

### Encryption

Sensitive records should be encrypted at rest and during transmission.

SQLite does not provide encryption automatically. An appropriate encryption mechanism must be separately implemented and verified.

### Data Export

Before exporting experimental data:

* Check for personal identifiers.
* Remove unnecessary metadata.
* Verify the destination.
* Obtain authorization where required.

### Data Retention

Experimental records should follow an established retention and deletion policy.

---

## 8. Dataset Security

The `datasets/` directory must not contain private or identifiable participant information.

Public datasets should be reviewed before publication.

Recommended practices:

* Use synthetic or appropriately de-identified sample data.
* Remove personal identifiers.
* Document dataset sources.
* Respect dataset licenses.
* Avoid publishing private recordings.
* Separate public demonstration data from private experimental records.

---

## 9. GitHub Repository Security

The following files and information must never be committed publicly:

* API keys.
* Access tokens.
* Passwords.
* Wi-Fi credentials.
* Private certificates.
* Database credentials.
* Private participant information.
* Confidential experimental records.
* Private configuration files.

### Recommended `.gitignore` Entries

```gitignore
# Environment variables
.env
.env.*
!.env.example

# Credentials
secrets/
credentials.json
*.pem
*.key

# Python
__pycache__/
*.pyc
.venv/
venv/

# Database
*.db
*.sqlite
*.sqlite3

# Private datasets
private_data/
raw_private_data/

# Build outputs
build/
dist/

# IDE
.vscode/
.idea/

# Operating system
.DS_Store
Thumbs.db

# Logs
*.log
```

Review all files before pushing them to a public repository.

Deleting a credential from the latest commit does not remove it from Git history. If a secret is exposed, revoke or rotate it immediately.

---

## 10. Communication Security

Communication between the ESP32 and desktop application should be designed to protect data integrity.

Recommended mechanisms:

* Input validation.
* Packet integrity checks.
* Communication timeouts.
* Error handling.
* Device identification.
* Authenticated connections where supported.

USB serial communication should not automatically be considered secure against physical access.

---

## 11. Experimental Safety

Security also includes protecting the physical experimental environment.

The mechanical excitation subsystem must be evaluated before experimental use.

Recommended precautions:

* Use suitable non-human headforms or mechanical phantoms during initial testing.
* Validate electrical power limits.
* Verify amplifier and transducer compatibility.
* Prevent uncontrolled excitation.
* Monitor system temperature.
* Inspect electrical connections.
* Establish emergency power-disconnection procedures.

Do not use an unvalidated prototype on human subjects.

---

## 12. Vulnerability Reporting

If you discover a potential security vulnerability in this project, please report it responsibly.

When reporting a vulnerability, include:

1. A clear description of the issue.
2. The affected component.
3. Steps to reproduce the issue.
4. Potential security impact.
5. Suggested mitigation, if available.

Avoid publicly disclosing exploitable vulnerabilities before the maintainers have had an opportunity to investigate.

**Security contact:** Add a dedicated project-maintainer email address here before publishing the repository.

Do not invent or publish an unverified contact address.

---

## 13. Security Review Checklist

Use this checklist during development.

### Hardware

* [ ] Verify ESP32 variant and security capabilities.
* [ ] Review firmware deployment configuration.
* [ ] Secure debugging interfaces where appropriate.
* [ ] Verify power-management safety.

### Firmware

* [ ] Validate incoming commands.
* [ ] Remove hardcoded credentials.
* [ ] Implement communication error handling.
* [ ] Review firmware integrity mechanisms.

### Software

* [ ] Validate user inputs.
* [ ] Review third-party dependencies.
* [ ] Protect sensitive configuration.
* [ ] Implement appropriate access controls.

### Data

* [ ] Protect experimental records.
* [ ] Encrypt sensitive data.
* [ ] Remove personal identifiers from public datasets.
* [ ] Review export permissions.

### GitHub

* [ ] Add `.gitignore`.
* [ ] Scan for exposed credentials.
* [ ] Review repository history.
* [ ] Verify documentation and dataset licenses.

---

## 14. Disclaimer

This security policy describes recommended engineering practices for the CMRM research prototype.

It does not constitute a formal security certification, penetration-test report or guarantee of system security.

Security features should only be described as implemented after they have been developed, configured and verified.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)

**Document:** Security Policy

**Classification:** Public Repository Documentation

**Status:** Development Policy
