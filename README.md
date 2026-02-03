<p align="center">
  <img src="https://img.shields.io/badge/Architecture-Zero%20Trust-00557f?style=for-the-badge" alt="Zero Trust">
  <img src="https://img.shields.io/badge/Standard-CIS%20Benchmark-00557f?style=for-the-badge" alt="CIS Benchmark">
  <img src="https://img.shields.io/badge/Status-Production%20Ready-00557f?style=for-the-badge" alt="Status">
</p>

# 🏗️ Zero Trust Engineering Blueprints

**Estándares de configuración segura (Hardening) y arquitecturas de referencia.**

Este repositorio contiene la "infraestructura como especificación" de **Sesecpro**. No son sugerencias; son los estándares de ingeniería obligatorios para garantizar la resiliencia en entornos hostiles.

> *"Trust nothing. Verify everything. Assume breach."*

---

## 📐 Principios de Diseño
Nuestros blueprints se basan en tres pilares innegociables:
1.  **Inmutabilidad:** Los servidores no se parchean en caliente; se redespliegan.
2.  **Privilegio Mínimo (PoLP):** Cada identidad tiene CERO accesos hasta que se justifique lo contrario.
3.  **Defensa en Profundidad:** Si el perímetro cae (Firewall), el host debe resistir (Hardening).

---

## 📂 Catálogo de Estándares (Library)

### ☁️ Cloud Governance & SaaS
| Estándar | Alcance | Compliance Mapping | Estado |
| :--- | :--- | :--- | :--- |
| **[AWS Security Foundations](./AWS-Secure-Baseline.md)** | `AWS Accounts` | CIS AWS v3.0 / NIST | ✅ **Stable** |
| **[M365 Enterprise Defense](./M365-Hardening-Standard.md)** | `Entra ID` / `Exchange` | NIS2 / ISO 27001 | ✅ **Stable** |

### 🐧 Infrastructure & Host Security
| Estándar | Alcance | Compliance Mapping | Estado |
| :--- | :--- | :--- | :--- |
| **[Linux Server Hardening](./Linux-Hardening-Guide.md)** | `RHEL` / `Ubuntu` / `Debian` | CIS Level 2 | ✅ **Stable** |
| **Kubernetes Guardrails** | `EKS` / `AKS` | NSA / CISA | 🚧 *Roadmap* |

---

### 🛠️ Instrucciones de Uso
Estos documentos están diseñados para ser consumidos por equipos de **DevSecOps** y **Administradores de Sistemas**.
1.  **Audit:** Ejecute los comandos de verificación incluidos en cada guía.
2.  **Remediate:** Aplique los scripts de configuración en un entorno de **Staging**.
3.  **Monitor:** Integre los logs generados (Auditd/CloudTrail) en su SIEM.

### ⚠️ Disclaimer de Ingeniería
*Estas configuraciones son estrictas ("Hardened by Default"). La aplicación directa en producción sin pruebas previas puede causar interrupciones de servicio. La seguridad requiere validación.*

**Architected by Sesecpro Engineering Team.**
