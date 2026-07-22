# Reverse-Studies

A repository containing notes, technical breakdowns, and case studies on reverse engineering across web applications, mobile platforms, and desktop binaries.

---

## About This Repository

This repository documents practical research and analysis of application architecture, client-side security logic, network protocols, and compiled binaries. The goal is to build a structured knowledge base covering web, Android, and desktop software inspection.

Rather than focusing on a single platform, these studies examine how data flows, how client-side protection mechanisms are structured, and how applications interact with backend systems across different environments.

### Core Focus Areas

* Web Application RE: Analyzing obfuscated client-side JavaScript, API signature reconstruction, and state initialization in modern frontend frameworks.
* Mobile Ecosystems (Android): Static analysis of APKs using JADX, inspecting Java/Kotlin bytecode, analyzing native libraries (.so), and dynamic hooking fundamentals with Frida.
* Desktop and Binary Fundamentals: Basic binary analysis using Ghidra, understanding executable structure, and inspecting control flow in compiled binaries.
* Telemetry and Anti-Bot Environments: Studying TLS fingerprinting (JA3/JA4), proxy detection mechanics, and browser automation behavior.

---

## Scope and Legal Guidelines

This repository follows standard security research and educational principles:

1. Authorized and Public Targets Only: Research is conducted on publicly distributed assets, open-source projects, or local binaries in controlled testing environments.
2. Non-Malicious Purpose: No actionable exploits, malware, or tools intended to break access controls on commercial systems are hosted in this repository.
3. Anonymization and Abstraction: Case studies use simplified code snippets, abstract pseudocode, or generic target names to focus on design patterns rather than specific live services.
4. Educational Focus: The contents document software analysis techniques, security concepts, and reverse engineering methodologies for research and skill development.

---

## Repository Structure

Reverse-Studies/

├── studies/

│   ├── web/

│   │   └── ...

│   ├── mobile/

│   │   └── ...

│   └── desktop/

│       └── ...

└── README.md

Each directory contains a technical breakdown alongside simplified Python, JavaScript, or Java/bytecode examples.

---

## Key Takeaways

* Client-side logic is always transparent: Whether analyzing a web frontend, an Android APK, or a desktop executable, client-side obfuscation or dynamic checks cannot replace backend validation.
* Multi-tool workflow: Combining static inspection (JADX, Ghidra, Chrome DevTools) with dynamic tools (Frida, network proxies) gives a complete view of how an application functions.
* Protocol awareness: Understanding network-level details such as TLS handshakes, HTTP headers, and custom payload encoding is necessary for accurate analysis.

---

## Disclaimer & Ethics

For details regarding research boundaries, legal scope, and contact policy, please refer to [DISCLAIMER.md](DISCLAIMER.md).

---

## License

MIT License. Free to use for research, learning, and educational purposes.
