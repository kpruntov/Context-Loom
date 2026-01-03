---
title: Introduction
description: ContextLoom System Overview
---

# ContextLoom System Overview

**Version:** 3.0.0

ContextLoom is an on-premise infrastructure appliance designed to govern the interaction between Enterprise Version Control Systems (VCS) and Artificial Intelligence (AI) agents. It functions as a secure middleware layer, enforcing strict access controls, data sovereignty, and auditability on code context retrieval.

## System Philosophy

The platform operates on the principle of Human-in-the-Loop (HITL). It treats AI agents as augmentation tools that require defined boundaries rather than autonomous entities with unrestricted access. Senior engineers explicitly curate the knowledge base available to an agent, ensuring predictability and preventing hallucination caused by context overload.

## Architecture: The Vault Pattern

ContextLoom enforces a "Zero-Storage" data policy.

1.  **Reference-Based Storage:** The appliance database stores only metadata and pointers (Git references). It does not persist proprietary source code.
2.  **Ephemeral Processing:** Source code flows strictly from the VCS provider to the requesting agent through the appliance's memory. It is never written to disk within the ContextLoom container.
3.  **Audit-First Design:** Every context retrieval event is treated as a compliance transaction, logged with the identity of the requester, the target bundle, and the precise commit SHA served.

## Core Capabilities

### Context Bundles
A Bundle is the atomic unit of governance. It consists of:
*   **System Instructions:** Natural language directives defining the agent's role and constraints.
*   **File Pointers:** Specific references to files across multiple repositories.
*   **Version Strategy:** Bundles can be "Dynamic" (tracking a branch head) or "Pinned" (locked to a specific commit hash for reproducibility).

### Secure MCP Bridge
ContextLoom exposes a secure bridge for the Model Context Protocol (MCP). This feature enables local or remote AI agents (such as Claude Desktop, Cursor, or JetBrains AI) to securely access internal enterprise context.

*   **Authentication:** Connections are secured via Personal Access Tokens (PATs) linked to the user's identity.
*   **Tunneling:** The bridge facilitates connection without requiring the internal VCS to be exposed to the public internet.

### Enterprise Integration
The appliance is designed for integration into existing corporate infrastructure:

*   **Identity Management:** Supports OIDC (OpenID Connect) for Single Sign-On (SSO) integration with providers like Okta, Azure AD, or Keycloak.
*   **Observability:** Emits structured JSON logs (Syslog standard) compatible with SIEM platforms like Splunk or Datadog.
*   **Intranet Connectivity:** Supports Custom Certificate Authority (CA) bundles for secure communication with air-gapped or self-signed GitHub Enterprise and GitLab Self-Managed instances.

## Workflow Overview

1.  **Connect:** Administrators configure the connection to the VCS provider and Identity Provider.
2.  **Curate:** Engineers create Context Bundles, selecting relevant files and defining instructions.
3.  **Serve:** AI Agents request context via the MCP Bridge using a scoped API Token.
4.  **Audit:** The system authenticates the request, fetches the raw content from the VCS, logs the transaction, and serves the compiled context to the agent.
