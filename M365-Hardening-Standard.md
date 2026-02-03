# 🛡️ Microsoft 365 Enterprise Hardening Standard (v2.0)

**Classification:** Internal / Client Confidential
**Compliance Mapping:** NIS2 (EU 2022/2555) | ISO/IEC 27001:2022 (A.5, A.9)
**Owner:** Sesecpro Engineering Team
**Last Updated:** February 2026

---

## 1. Executive Summary
Este documento define la **Configuración Base de Seguridad** para entornos Microsoft 365 gestionados por Sesecpro.

El objetivo es mitigar los vectores de ataque más comunes en 2026 (Ransomware, BEC y Robo de Identidad) alineándose con los requisitos de la directiva europea **NIS2** para la protección de la cadena de suministro.

---

## 2. Identity Defense (Entra ID / Azure AD)
*Objective: Zero Trust Enforcement (Verify explicitly).*

### 2.1 Conditional Access Strategy
Se aplica un modelo de "Denegar por defecto". Las siguientes políticas son mandatorias:

| Policy ID | Control Name | Target Scope | Action |
| :--- | :--- | :--- | :--- |
| **CA-CORE-01** | `Block Legacy Authentication` | All Users | **BLOCK** (POP3, IMAP, SMTP) |
| **CA-CORE-02** | `Require MFA & Compliant Device` | Admin Roles | **GRANT** (MFA + Hybrid Join required) |
| **CA-CORE-03** | `Risk-Based Access Control` | All Users | **BLOCK** (Sign-in Risk: High/Medium) |
| **CA-CORE-04** | `Geo-Fencing Defense` | All Users | **BLOCK** (Locations outside business scope) |

### 2.2 Break-Glass Accounts (Emergency Access)
**ISO 27001 A.9.1.1** | Para garantizar la recuperación ante desastres (DR) en caso de fallo de MFA o Entra ID.
* **Requisito:** 2 cuentas de administración global (`cloud-only`).
* **Exclusión:** Excluidas explícitamente de las políticas de Acceso Condicional.
* **Custodia:** Credenciales de 128 caracteres almacenadas en bóveda física ignífuga.

---

## 3. Threat Protection (Exchange Online)
*Objective: Prevent Business Email Compromise (BEC) and Malware.*

### 3.1 Anti-Phishing & Impersonation
Configuración agresiva para detectar suplantación de identidad de directivos (CEO/CFO fraud).

* **PowerShell Implementation:**
```powershell
Set-AntiPhishingPolicy -Identity "Office365 AntiPhishing Default" `
    -EnableTargetedUserProtection $true `
    -TargetedUsersToProtect "CEO@client.com","CFO@client.com" `
    -EnableTargetedDomainsProtection $true `
    -EnableMailboxIntelligence $true `
    -PhishThresholdLevel 2 # (Aggressive)
```

### 3.2 Auto-Forwarding Block (Data Exfiltration)
**NIS2 Data Security** | Los atacantes suelen crear reglas de reenvío ocultas para robar información.
* **Mandato:** Bloqueo global de reenvío automático a dominios externos.
* **PowerShell Implementation:**
```powershell
Set-HostedOutboundSpamFilterPolicy -Identity Default `
    -AutoForwardingMode On -AutoForwardingBlockList $true
```

### 3.3 Safe Attachments (Sandbox)
Todos los adjuntos deben ser detonados en un entorno seguro antes de la entrega.
* **Policy:** `Dynamic Delivery` (El usuario recibe el correo inmediatamente y el adjunto se libera tras el análisis).

---

## 4. Data Governance & Collaboration
*Objective: Control data leakage in Teams and SharePoint.*

### 4.1 External Sharing Controls
**ISO 27001 A.8.12** | Prevención de exposición de datos confidenciales.
* **Default Link Type:** "Specific People" (Solo personas específicas).
* **Prohibido:** Enlaces "Anyone" (Anónimos) están deshabilitados a nivel de tenant.
* **Expiration:** Los accesos de invitados deben caducar automáticamente tras 90 días de inactividad.

### 4.2 Audit Log Retention
**NIS2 Incident Reporting** | Capacidad forense extendida.
* **Configuración:** Habilitar `Unified Audit Log`.
* **Retención:** Aumentar retención a 1 año (requiere licencia E5 o Add-on) o exportar a SIEM externo para cumplimiento legal.

---

## 5. Endpoint Protection (Intune)
* **BitLocker:** Cifrado de disco obligatorio con custodia de claves en Entra ID.
* **Attack Surface Reduction (ASR):**
    * *Block executable content from email client and webmail.*
    * *Block Office applications from creating child processes.*

---

### ⚠️ Disclaimer
*This document contains proprietary methodologies of Sesecpro. Unauthorized distribution outside the client organization is prohibited. Implementation of these controls requires testing in 'Report-Only' mode for 7 days.*

**© 2026 Sesecpro Engineering.**
