
# **FractalMesh Genesis: A Foundational Specification for a Modular, User-First  Platform**

*   **Version**: 1.0 (Genesis Document)
*   **Status**: Inception, Open for Contribution & Review
*   **Project Name**: FractalMesh

## **1. Vision & Mission**

**FractalMesh** is an open-source infrastructure for building a new generation of social and creative applications. Our mission is to empower creators and communities with **sovereign, interoperable, and privacy-first digital spaces**.

Instead of building walled gardens, we are creating a composable ecosystem where features are modules, protocols are plug-ins, and user data remains under user control. This project is for developers who want to build platforms that are resilient, adaptable, and respectful of their users, with a long-term vision for data preservation and creator-owned networks.

This document serves as the living blueprint. It is intentionally exhaustive to provide a complete picture, but the implementation is designed to be incremental. We welcome suggestions, critique, and contributions.

## **2. Core Principles: The FractalMesh Doctrine**

This architecture is guided by a set of non-negotiable principles.

1.  **Everything is a Module:** The system is not a monolith. Core functions—from federation protocols to UI components and data stores—are designed as independent, swappable layers. This ensures extensibility and avoids vendor lock-in.
2.  **User Data is Sovereign:** The architecture defaults to user-controlled, local-first data storage (via encrypted SQLite). Remote data is treated as a temporary cache or a user-authorized replica, not the primary source of truth.
3.  **Federation is a Choice, Not a Mandate:** Protocols like AT Protocol and ActivityPub are treated as optional, pluggable gateways. A user or site can operate in a self-contained mode or choose to federate with the broader ecosystem, without a forced change in user experience.
4.  **Opaque by Default:** The underlying complexity of federation, blockchain, or data synchronization must be hidden from the end-user unless they explicitly choose to interact with it. The UX must be as seamless as any centralized application.
5.  **Multi-Tenancy is Native:** The system is built from the ground up to serve multiple, distinct branded sites from a single, shared codebase, ensuring scalability for platform operators.
6.  **Signed, Same-Origin Modules:** Security is paramount. All dynamically loaded extensions and modules must be cryptographically signed and served from a trusted origin to prevent supply-chain attacks.

## **3. High-Level System Architecture**

The system is a collection of containerized microservices and layers orchestrated to work in concert. A client request flows through a unified gateway, which then routes it to the appropriate specialized backend service.

```mermaid
graph TD
    subgraph Client Layer
        A[Next.js Web App - Site A]
        B[Next.js Web App - Site B]
        C[React Native Mobile App]
        D[Third-Party Client]
    end

    subgraph API & Gateway
        E[API Gateway (Apollo GraphQL)]
    end

    subgraph Core Data & State
        F[Supabase (Postgres + Auth)]
        G[MongoDB (Unstructured Data)]
        H[Redis (Cache & Sessions)]
    end

    subgraph Specialized Services
        I[Typesense (Search Engine)]
        J[Apache AGE / Neo4j (Graph Engine)]
        K[Vector DB (For RAG)]
        L[RabbitMQ (Task Queue)]
        M[Unleash (Feature Flags)]
    end

    subgraph Federation Gateways (Plugins)
        N[AT Protocol Gateway]
        O[ActivityPub Gateway]
    end

    subgraph Content & Processing
        P[Media Processing Pipeline (FFmpeg)]
        Q[CDN (Cloudflare/Vercel)]
    end

    A & B & C & D --> E
    E --> F; E --> G; E --> H; E --> I; E --> J; E --> K; E --> L; E --> M
    F --> L; G --> L
    L --> N; L --> O; L --> P
    P --> Q
```

## **4. The Complete Technology Stack (Bill of Materials)**

| Layer | Technology | Rationale & Purpose |
| :--- | :--- | :--- |
| **Frontend Runtime**| Next.js 15 (React) | Provides Server Components, ISR, and a robust framework for building fast, modern web applications. |
| **Shared Components**| shadcn/ui + Tailwind CSS | A composable, accessible, and themeable UI library that avoids runtime overhead. |
| **Structured Data** | Supabase (self-hosted PostgreSQL) | The source of truth for relational data (users, roles, metadata). RLS provides robust multi-tenancy. |
| **Graph Database** | Apache AGE (Postgres Extension) | Enables powerful graph queries directly within the primary database, avoiding data duplication and extra cost. |
| **Unstructured Data**| MongoDB | Ideal for flexible, document-based storage of user-generated content, REA models, and evolving schemas. |
| **Search Engine** | Typesense | A fast, open-source, and self-hostable search engine for full-text and faceted search. |
| **Vector Database**| Qdrant / Weaviate | Open-source vector stores for powering semantic search and Retrieval-Augmented Generation (RAG). |
| **Caching Layer** | Redis | An in-memory data store for high-performance caching, session management, and real-time counters. |
| **API Gateway** | Apollo Server (GraphQL) | A unified data graph that fetches and merges data from Supabase, MongoDB, and other services into a single API. |
| **Task Queuing** | RabbitMQ | A robust message broker for managing asynchronous background jobs, retries, and inter-service communication. |
| **Data Sync** | Yjs (CRDT Library) | Enables real-time collaboration and conflict-free data replication for the local-first architecture. |
| **Local Database** | SQLite (via sql.js / Capacitor) | Empowers users with a private, encrypted, offline-capable database on their own device. |
| **Feature Flags** | Unleash (Open Source) | Manages phased rollouts, A/B testing, and provides kill-switches for features (alpha, beta, prod). |
| **Containerization**| Docker / Docker Compose | Standardizes the development and deployment environment, ensuring consistency across all machines. |
| **CI/CD & Build**| GitHub Actions + Turborepo | Automates testing and deployment, with remote caching for extremely fast builds in the monorepo. |
| **Monitoring** | Prometheus + Grafana + Loki | A complete open-source stack for metrics, visualization, and logging. |

## **5. Architecture as Interoperability Layers**

As per our core principles, FractalMesh is a system of pluggable layers. The following components **must** be implemented as independent, interoperable modules with well-defined interfaces.

| Layer Category | Pluggable Component | Purpose |
| :--- | :--- | :--- |
| **Federation** | ATProto Gateway, ActivityPub Adapter, Farcaster Bridge | Connects FractalMesh to external social protocols, handling identity, content, and graph synchronization. |
| **AI & Semantics** | RAG Engine, Embedding Index Adapter, LLM Router | Provides AI-powered features like semantic search and content generation, with swappable backends. |
| **Identity & VCs** | DID Method Resolver, VC Issuer/Verifier | Manages decentralized identities and verifiable credentials for proving authorship, ownership, and claims. |
| **Content Pipeline** | Publish Adapters, Format Transformers, Scheduler | Manages the flow of content from creation to multi-platform distribution, with customizable steps. |
| **Frontend UI** | Component Registry, Theme Plugins, Field Injection | Allows the user interface to be dynamically composed, themed, and extended per-site or per-user. |
| **Graph System** | Graph Store Plugin, Relationship Type Plugin | Defines the backend for graph storage and allows for the extension of the semantic relationship model. |
| **Compliance** | Licensing Engine, Rights Resolver, Audit Exporter | Manages content licensing, intellectual property rights, and provides tools for legal compliance. |
| **Runtime** | Plugin Loader, Safe Execution Sandbox, Auth Scopes | The core engine that allows for the secure, dynamic loading and execution of all other modules. |
| **Messaging** | Transport Adapters, Notification Providers | Bridges communications across different channels like email, push notifications, and chat protocols (XMTP, Matrix). |
| **Admin & Ops** | Metrics Plugins, Moderation Workflows, Audit Log | Provides pluggable tools for platform administration, content moderation, and observability. |

## **6. Exhaustive Module Breakdown**

This section details the complete list of core and proposed functional modules for the FractalMesh system. Each is designed to be a self-contained, pluggable unit.

| # | Module | Core Functionality |
|:--|:---|:---|
| 1 | **User Identity & Account** | Manages users, roles, permissions, sessions, and optional DID integration. |
| 2 | **Profile & Role Management** | Provides customizable profiles for different entity types (user, creator, business). |
| 3 | **Content Authoring & Post** | Enables creation, editing, versioning, and scheduling of all content formats. |
| 4 | **Multi-Platform Publishing** | Translates and publishes structured content to external platforms via API adapters. |
| 5 | **Local-First Data Layer** | Enables offline data storage via an encrypted on-device SQLite database. |
| 6 | **Federated Creator Network** | Facilitates discovery, connection, and collaboration across creators and organizations. |
| 7 | **Search & RAG Layer** | Powers fast full-text and semantic search, providing embeddings for LLM-based RAG. |
| 8 | **Graph Relationship Engine** | Stores and queries semantic relationships between all entities for recommendations. |
| 9 | **Structured Posting DSL** | Allows posts to be defined from structured data (JSON/YAML) and rendered via templates. |
| 10 | **Modular Extension Framework** | Allows signed, third-party plugins to be safely loaded and executed at runtime. |
| 11 | **Alpha/Beta Feature Staging** | Controls feature visibility and rollout based on user roles, cohorts, or manual flags. |
| 12 | **Messaging & Notifications** | Delivers real-time and asynchronous messaging and platform alerts. |
| 13 | **Metrics & Analytics** | Tracks usage, engagement, and performance at all platform levels. |
| 14 | **Compliance & Licensing** | Manages content licensing, ownership verification, and blockchain-based attestations. |
| 15 | **Admin & Observability** | Provides trusted roles with tools for moderation, system monitoring, and user management. |
| 16 | **Monetization & Funding** | Provides pluggable monetization strategies (donations, subscriptions, royalties). |
| 17 | **Curation & Reputation Engine** | Supports community-driven discovery, trust scoring, and content surfacing. |
| 18 | **Content Integrity & Traceability**| Ensures authorship and provenance through immutable hashes and version chains. |
| 19 | **Multilingual Content & Translation** | Supports global audiences with content tagging, translation, and locale-specific feeds. |
| 20 | **Campaigns & Projects** | Structures collective publishing efforts like timed campaigns or collaborative projects. |
| 21 | **Long-Term Preservation & Archive**| Enables users to commit content to permanent storage solutions like Arweave. |
| 22 | **Assistive & Accessibility Features** | Ensures equitable access with alt-text, captions, and accessible themes. |
| 23 | **Collaborative Authoring** | Unlocks real-time, peer-to-peer editing and versioning for content. |
| 24 | **Sovereign Local Node** | Allows the platform to be fully operational offline, with peer-to-peer sync. |
| 25 | **Developer Console & DevKit**| Provides tools for third-party developers to build, test, and publish plugins. |

## **7. Security Architecture**

Security is not a bolt-on—it is foundational. FractalMesh is designed with a threat-aware, modular security model, balancing developer flexibility with strict enforcement of user data sovereignty and runtime isolation.

*   **7.1. Threat Models & Trust Zones:** The system is divided into trust zones (Core Services, Plugin Runtime, Client Devices, Federated Gateways), each with its own threat model and mitigation strategies against risks like SQLi, injection, privilege escalation, code injection, sandbox escape, device theft, and identity spoofing.
*   **7.2. Runtime Security:** All dynamically loaded modules **must** be cryptographically signed using a trusted key authority before execution. They execute within a secure sandbox (e.g., Web Worker or VM) with restricted global scope and explicit capability grants, enforced by a Same-Origin Policy.
*   **7.3. Access Control Model:** A robust Role-Based Access Control (RBAC) model is enforced at the API Gateway and within the plugin runtime, scoped to both user role and tenant (site). All sensitive operations are logged to a queryable audit trail.
*   **7.4. Local Data Security:** On-device SQLite databases are encrypted using OS-native secure enclaves (e.g., Keychain, Keystore). User consent is required for any temporary, task-specific sharing of private data with AI or other modules.

## **8. Mobile Runtime & Offline-First UX**

Mobile is a first-class citizen, designed for full functionality even in disconnected environments.

*   **8.1. Offline-First Caching:** All user-created content is saved to the local SQLite database first. Synchronization with the cloud is a background process that handles conflicts gracefully using CRDTs and a defined resolution strategy.
*   **8.2. Platform Constraints:** The architecture accounts for iOS/Android limitations on background tasks, file system access, and battery usage, ensuring a performant and compliant user experience.
*   **8.3. Post-Download Modules:** The system supports the secure, on-demand installation of signed modules (like the Chia Sage wallet) after the initial app install, using platform-native dynamic feature delivery.
*   **8.4. UX Safeguards:** Plugin permission dialogs must be native-feeling and explain in plain language what access is being granted. Long-term offline users are prompted to sync and resolve conflicts via guided UI when reconnected.

## **9. Plugin Marketplace & Trust Model**

To foster a healthy ecosystem, FractalMesh will provide a decentralized but trusted system for plugin distribution.

*   **9.1. Distribution Channels:** An **Official Registry**, maintained by the Core Team, will list verified and signed plugins. Organizations can also run **Private Registries** for internal use, and admins can manually trust plugins from a URL.
*   **9.2. Trust Levels:** Plugins will be categorized by trust level: **Official Verified** (code-reviewed, guaranteed safe), **Community Reviewed** (popular, reproducible builds), and **Unverified** (use at your own risk).
*   **9.3. Plugin Capabilities Manifest:** Every plugin must include a `manifest.json` file declaring its required permissions, API scopes, UI injection points, and version compatibility, which is enforced by the runtime.

## **10. Data Portability & Lifecycle Management**

Users have absolute control over their data's entire lifecycle.

*   **10.1. Export & Import:** All user data (content, relationships, identity) is exportable to a single, portable, signed archive file for easy migration or backup.
*   **10.2. Granular Retention Controls:** Users can set per-post expiry dates (Time-To-Live). Admins can define site-wide retention policies (e.g., auto-purge old drafts).
*   **10.3. Consent-Driven AI Sharing:** No private data is used for RAG or AI training without explicit, granular, and revocable user consent for a specific task. AI modules operate on ephemeral, in-memory data copies.

## **11. Testing, QA, and Module Compatibility**

A modular architecture demands a rigorous and automated testing philosophy.

*   **11.1. Testing Pyramid:** The strategy includes **Unit Tests** (Vitest), **Integration Tests** for services, **End-to-End Tests** (Playwright) for user flows, and **Contract Tests** (Pact) to ensure API and plugin interfaces do not break.
*   **11.2. CI/CD Pipeline:** Every pull request automatically triggers the full suite of checks. A nightly build runs a **Plugin Compatibility Suite**, testing all registered plugins against the latest core runtime to catch regressions.

## **12. Federation Protocol Abstraction**

Protocols like ATProto and ActivityPub are not hard-coded; they are pluggable adapters behind a universal interface.

*   **12.1. Federation Gateway Interface:** Any federation protocol must implement a standard `FederationAdapter` interface (e.g., `publish`, `fetchProfile`, `fetchFeed`). This allows new protocols like Nostr or Farcaster to be added without changing the core system.
*   **12.2. Protocol-Agnostic Representation:** All content is modeled internally in a normalized `FractalPost` format. The gateway adapters are responsible for translating this internal format to and from the protocol-specific format (e.g., ATProto records, ActivityPub HTML).

## **13. The Developer Experience**

FractalMesh is designed to be as straightforward for developers as it is powerful. A clean, containerized local environment and a well-structured monorepo are key to enabling rapid, parallel development.

*   **13.1. Local Development Environment:** A "one-command startup" (`pnpm dev`) using Docker Compose launches the entire backend stack (Supabase, MongoDB, Redis, etc.) and runs the Next.js frontend applications locally.
*   **13.2. Monorepo Structure:** The codebase is organized as a Turborepo-powered monorepo with dedicated `apps/` and `packages/` directories to maximize code sharing and simplify dependency management.
*   **13.3. Core Workflows:** Development tasks like testing (`pnpm test`), linting (`pnpm lint`), and database migrations (`pnpm db:diff`) are streamlined with pre-configured scripts.

## **14. Phased Roadmap & Milestones**

Development will proceed in logical, deployable milestones. Each milestone delivers a cohesive set of capabilities.

*   **Milestone 1: Core Platform & Identity (MVP)**
    *   **Focus:** Establish the foundational platform.
    *   **Modules:** Identity, Profile, Content Authoring, Multi-Platform Publishing, Feature Staging, Basic Metrics.
    *   **Outcome:** Two functional, themed social sites capable of creating and publishing content.
*   **Milestone 2: Discovery & Intelligence**
    *   **Focus:** Enhance content discoverability and enable AI-powered features.
    *   **Modules:** Creator Network, Search & RAG, Structured DSL, early Plugin System.
    *   **Outcome:** A searchable network with smart recommendations and advanced content creation tools.
*   **Milestone 3: Interaction & Federation**
    *   **Focus:** Enable real-time user interaction and deep federation.
    *   **Modules:** Local Data Sync, Graph Engine, Messaging, Compliance & Licensing (VCs).
    *   **Outcome:** A deeply interactive platform with offline capabilities and verifiable, on-chain data hooks.
*   **Milestone 4: Governance & Scale**
    *   **Focus:** Harden the platform for large-scale operation and community governance.
    *   **Modules:** Advanced Admin Tools, Full Analytics, full Compliance suite, full Plugin system.
    *   **Outcome:** An enterprise-ready, fully observable, and secure platform.

## **15. Critical Gaps & Unresolved Questions**

A credible project acknowledges its challenges. The following are the most critical open questions that the community must solve to ensure the project's success. This is a call for collaboration.

*   **Strategic Gaps:**
    *   **The Sovereignty vs. Intelligence Paradox:** How do we reconcile the need for absolute data privacy with the data requirements of intelligent, cross-user features like RAG? *A formal consent-driven, ephemeral data-sharing model must be designed.*
    *   **The "Everything Platform" Trap:** What is the single, undeniable "killer feature" for Milestone 1 that will attract the first crucial cohort of users?
    *   **The Missing Business Model:** How will the project sustain itself financially? A plan for a foundation, managed hosting, or an enterprise service model is needed.
*   **Architectural & Technical Gaps:**
    *   **The Unified Sync Problem:** A precise, robust conflict-resolution algorithm for syncing data between on-device SQLite and multiple backend databases (Postgres, Mongo, Graph) is the hardest unsolved technical challenge.
    *   **API Gateway as a Bottleneck:** A detailed data federation strategy (e.g., Apollo Federation) is required to prevent the unified API from becoming a performance bottleneck.
    *   **The True Complexity of Self-Hosting:** A production-grade reference architecture (e.g., Kubernetes) with guides on security, backups, and scaling is needed beyond the local Docker Compose setup.
*   **Product & User Experience Gaps:**
    *   **The Onboarding Cliff:** What is the step-by-step onboarding flow for a non-technical user that introduces powerful concepts progressively?
    *   **The Cold Start Problem:** What is the go-to-market strategy to seed the network with initial users and content?
*   **Community & Ecosystem Gaps:**
    *   **The Contributor Value Proposition:** What are the clear, tangible benefits (e.g., career, business opportunities) for open-source contributors?
    *   **The Plugin Ecosystem Trust Model:** A formal governance and technical model for the plugin registry (signing authority, verification tiers) is required.

## **16. Community & Project Governance**

*   **Contribution Guidelines:** All contributions will be managed via GitHub Issues and Pull Requests, following the Conventional Commits specification.
*   **Governance Model:** The project will operate under a **Core Team Governance Model**, where membership is earned through sustained, high-quality contributions.
*   **Code of Conduct:** We adopt the **Contributor Covenant** to ensure a welcoming and harassment-free environment for all.
*   **Licensing:** The FractalMesh project is licensed under the permissive **Apache License 2.0** to encourage maximum adoption.
*   **Security Reporting:** A private, responsible disclosure process will be detailed in a `SECURITY.md` file.

## **17. Conclusion: A Call to Build**

This document represents the foundational vision for FractalMesh. It is exhaustive by design, but it is not immutable. Like the software it describes, it is a living artifact meant to be improved, clarified, and expanded upon by the community.

The challenge ahead is significant, but the opportunity is greater. We are not just building another social application; we are architecting a more resilient, creator-centric, and interoperable digital public square. The architecture is modular, the roadmap is phased, and the principles are clear.

Whether you are a backend engineer, a frontend developer, a UX designer, a protocol expert, or simply someone who believes in a better web, your contribution is valuable.

**The blueprint is here. Let's build it together.**


---
## **18. Project Management & Execution Plan**

This section translates the architectural vision into an actionable plan. It outlines the current project status, identifies outstanding items that require immediate attention, allocates resources and focus areas, defines communication needs, and sets a target for the completion of this foundational specification phase.

### **18.1. Current Project Status**

*   **Phase:** **Inception & Specification (Complete)**
*   **Artifacts Produced:**
    *   **Genesis Document v1.0:** This comprehensive system-level specification.
    *   **Core Principles & Architecture:** Defined, documented, and diagrammed.
    *   **Exhaustive Module & Feature List:** A complete catalog of the platform's proposed capabilities.
    *   **Plugin-First Layering Strategy:** Explicitly defined as the core architectural approach.
    *   **Federation & Protocol Positioning:** Established as an optional, pluggable gateway system.
    *   **Critical Gaps Analysis:** A formal list of known strategic, architectural, and product challenges has been documented.

The project is now ready to move from high-level design to detailed specification of its most critical unresolved components.

### **18.2. Outstanding Items Requiring Completion (Pre-Development)**

The following items represent the most significant blockers to beginning **Milestone 1** development. They must be addressed to ensure the project starts on a solid and viable footing.

#### **High-Priority Modules Requiring Detailed Specification:**
1.  **Schema Introspection & Ontology Builder:** Define how the system understands its own data types and how plugins can extend them.
2.  **Plugin Signing Authority & Trust Registry:** Design the technical and governance framework for signing, verifying, and distributing trusted plugins.
3.  **Real-Time Collaboration Layer (Deep Dive):** Specify the exact implementation of Yjs/CRDTs for collaborative editing and state synchronization beyond basic theory.
4.  **Legal & Policy Toolkit:** Create template documents and a plugin system for Terms of Service, Privacy Policies, and DMCA request handling.

#### **Critical Architectural & Strategic Gaps to Close:**
1.  **Finalize the Sovereignty vs. Intelligence Model:** Author a whitepaper or formal specification detailing the consent-driven, ephemeral data-sharing pattern for RAG and AI features. This is the top strategic priority.
2.  **Define the Unified Sync Reconciliation Strategy:** Document the precise conflict resolution algorithm for syncing data between on-device SQLite and the backend databases (Postgres, Mongo, Graph), including edge cases and failure modes.
3.  **Author the Formal Plugin Manifest & Lifecycle Specification:** Create the `plugin.json` schema and document the `init`, `register`, `execute`, and `teardown` lifecycle hooks for all plugin types.
4.  **Draft the Production-Grade Reference Deployment:** Move beyond the local Docker Compose setup to a documented Kubernetes (or similar) reference architecture, specifying resource requirements, security hardening, and backup procedures.
5.  **Design the "Killer Feature" Onboarding Flow:** Create a detailed user journey and wireframes for the single most compelling use case of Milestone 1, demonstrating clear value to a non-technical user within the first five minutes of use.

### **18.3. Proposed Resource Allocation & Focus Areas**

To address the outstanding items efficiently, work should be divided among key roles or focus areas.

| Task | Lead / Focus Area | Key Deliverable |
| :--- | :--- | :--- |
| **Remaining Module Specification** | System Architect / AI-Assisted Drafting | Detailed markdown documents for each remaining module in the backlog. |
| **Plugin Lifecycle & Manifest Spec** | Technical Lead / Core Team | A formal technical specification document for plugin developers. |
| **Go-to-Market & M1 Killer Feature**| Product Lead / Core Team | A concise product brief defining the target audience, problem, and solution for Milestone 1. |
| **Onboarding UX & Sync Strategy** | UX Lead + Technical Lead | A clickable prototype or detailed wireflow for onboarding, and a technical document detailing the sync algorithm. |
| **Production Deployment Plan** | DevOps Lead / Core Team | A new section in the repository (`/infra/production`) with deployment scripts and documentation. |

### **18.4. Team Communication & Coordination Needs**

To maintain momentum and alignment, the following questions must be answered by the core team:

1.  **Priority of Gaps:** Which of the critical gaps (Sync, RAG Privacy, Plugin Trust) is the absolute top priority to solve first, as its solution will constrain the others?
2.  **Plugin Governance:** Does the proposed three-tier trust model (Official, Community, Unverified) align with the long-term vision for decentralized authority? Is a foundation or a DAO the ultimate signing authority?
3.  **Onboarding Philosophy:** Is the onboarding experience a universal, core module, or is it a customizable workflow that can be different for each FractalMesh-powered site?

### **18.5. Proposed Deadline for Genesis Document v1.0 Finalization**

**Target Date: Finalized within the next 14 calendar days.**

This timeline is aggressive but achievable, allocated as follows:

*   **Week 1:** Focus on closing the highest-priority architectural and strategic gaps (Sync Strategy, RAG Privacy Model, Plugin Spec). Draft detailed specifications for the remaining core modules.
*   **Week 2:** Review and iterate on the produced specifications. Finalize the M1 Onboarding Flow and Production Deployment guide. Assemble all content into the final, versioned **Genesis Document v1.0**.

Upon completion and approval of this v1.0 document, the project will have the necessary clarity to begin creating GitHub issues, defining sprint backlogs, and officially starting development on **Milestone 1**.