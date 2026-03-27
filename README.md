Phantasm-DB: Autonomous Cognitive Deception Engine

<div align="center">
<img src="https://www.google.com/url?sa=E&source=gmail&q=https://img.shields.io/badge/Database-PostgreSQL%2016-blue.svg" alt="Postgres">
<img src="https://www.google.com/search?q=https://img.shields.io/badge/Backend-Python%25203.11-green.svg" alt="Python">
<img src="https://www.google.com/search?q=https://img.shields.io/badge/Security-Active%2520Deception-red.svg" alt="Security">
<img src="https://www.google.com/search?q=https://img.shields.io/badge/Forensics-Relational%2520Vault-orange.svg" alt="Forensics">
</div>

Phantasm-DB is a security-integrated Database Management System (DBMS) architecture designed to mitigate high-level database breaches through Active Deceptive Defense. Unlike traditional security mechanisms that focus on passive blocking (Denial of Service), Phantasm-DB identifies malicious behavior and transparently migrates the intruder's session into a Shadow Reality.

1. Project Overview

The system acts as an intelligent proxy between the user and the data layer. By leveraging session-level schema routing, the tool ensures that an intruder remains unaware of their detection. This prevents them from pivoting to new attack vectors while their every move is recorded for forensic reconstruction.

2. Problem Statement

Contemporary DBMS security infrastructures suffer from a critical Binary Flaw: a session is either authorized or unauthorized. Once the perimeter is breached—whether via SQL Injection (SQLi), credential stuffing, or leaked session tokens—the database implicitly trusts all subsequent queries from that authenticated channel.

Critical Vulnerabilities Addressed:

Visibility Gap: Standard databases lack the behavioral intelligence to distinguish between a legitimate human employee and a data-scraping bot once authentication is bypassed.

Forensic Volatility: Attackers often delete flat log files to hide their tracks. There is a requirement for a relational, immutable ledger that "fossilizes" intent.

Plaintext Exposure: Sensitive data is vulnerable the moment a session is authorized. There is no middle ground to misdirect an attacker without terminating the connection.

Data Exfiltration Impact: In standard systems, a successful breach results in a 100% loss of data confidentiality.

3. Technical Solution Description

3.1 Intruder Identification Logic

The system identifies malicious actors through a Tri-Layer Detection Engine implemented in the Python middleware:

Signature Matching: Scans raw SQL strings for known injection patterns (e.g., ' OR 1=1 --, UNION SELECT) using Regular Expressions.

Honey-Token Tripping: The production database contains "Lure Records" (e.g., a fake administrative account). Any query targeting these specific primary keys triggers an immediate flag.

Heuristic Velocity: Tracks query frequency to identify automated scrapers (like sqlmap) via non-human query intervals.

3.2 Cognitive Deception Logic

Upon identification, the system initiates a Schema Migration:

The Switch: The middleware executes a session-level configuration change:

SET search_path TO shadow_vault, public;


The Deception: The intruder is served synthetic data. To the attacker, the tables look authentic, but the data is 100% artificial.

Throttling: To prevent rapid data theft, the system adds an intentional delay (pg_sleep) to queries from flagged IPs, simulating heavy server load and buying time for the administrator.

4. Relational Schema & Entity Definitions

The architecture utilizes a 10-entity normalized schema utilizing Generalization/Specialization (Inheritance) and Aggregation.

4.1 Identity Layer (Inheritance Model)

Entity Name

Attributes

Architectural Logic

Global_Identities (Superclass)

identity_id (UUID PK), ip_address (INET), user_agent, risk_score

Acts as a centralized registry for reputation tracking before and after login.

Production_Users (Subclass)

identity_id (FK), username (Unique), password_hash

Manages authorized personnel. Inheriting allows tracking if a legitimate account is accessed from a suspicious IP.

Threat_Actors (Subclass)

identity_id (FK), fingerprint_hash, threat_classification, is_quarantined

Stores metadata specifically for malicious entities, allowing for detailed profiling without polluting clean user tables.

4.2 Deception & Synthesis Layer

Entity Name

Attributes

Architectural Logic

Shadow_Manifest

manifest_id (PK), table_name, prod_schema, shadow_schema

The switchboard mapping production tables to shadow mirrors.

Lure_Blueprints (Aggregation)

blueprint_id (PK), manifest_id (FK), rules_json (JSONB), faker_provider

Stores generation logic. JSONB allows complex rules for how fake data (e.g., credit card formats) should be structured.

Honey_Tokens

token_id (PK), target_id_val, table_name, severity_weight

Defines the tripwires. Querying these confirms malicious intent with 100% certainty.

4.3 Forensic & Operational Layer

Entity Name

Attributes

Architectural Logic

Forensic_Ledger

log_id (PK), identity_id (FK), raw_query (Text), execution_time_ms

The immutable black box fossilizing attacker SQL strings for post-incident reconstruction.

Detection_Policies

policy_id (PK), regex_pattern, risk_weight, action_type

Externalizes security signatures, allowing admins to update "threat libraries" without code changes.

Real_Time_Alerts

alert_id (PK), log_id (FK), escalation_level, is_resolved

Feeds the Admin Dashboard with actionable notifications for real-time monitoring.

System_State

config_id (PK), throttle_delay_ms, deception_active (Bool)

Global singleton to control engine status and the intensity of the latency applied to attackers.

5. Implementation Roadmap

Week

Phase

Key Deliverables

Week 1

Relational Foundation

Setup PostgreSQL schemas; implement 10-entity schema with Inheritance and constraints.

Week 2

Identification Logic

Develop Python middleware; implement IP tracking and RegEx signature scanning.

Week 3

Deception Engine

Implement search_path switching; integrate Faker library for dynamic synthetic data.

Week 4

Forensics & Dashboard

Build Admin UI; conduct "Two-PC" attack simulation (Attacker Laptop vs Server PC).

6. Simulation Methodology

The tool is demonstrated using a Server (Victim) and an Attacker Node (Laptop) on the same network:

Phase 1: Attacker connects and performs a standard login.

Phase 2: Attacker executes a SQL Injection (e.g., ' OR 1=1 --).

Phase 3: Server identifies the IP, updates the Threat_Actors table, and increases the risk score.

Phase 4: Middleware silently redirects the session to the shadow_vault schema.

Phase 5: Attacker "successfully" steals fake records, while the Server records the raw SQL strings in the Forensic_Ledger.

Developed By: Fatima Rehman and Ali Kamran
