# 🏗️ Zero Trust Architecture & Hardening Blueprints

Repositorio de estándares de configuración segura (Hardening) y arquitecturas de referencia para entornos Multi-Cloud e Híbridos. 

Este material refleja la **"Doctrina de Defensa en Profundidad"** de Sesecpro, alineada con CIS Benchmarks y NIST SP 800-53.

## 📂 Catálogo de Estándares

### ☁️ Cloud Security (Multi-Cloud)
* [**AWS Security Foundations**](./AWS-Secure-Baseline.md): Línea base de seguridad para cuentas AWS (IAM, S3, CloudTrail).
* [**M365 Enterprise Hardening**](./M365-Hardening-Standard.md): Protección de tenants Microsoft 365 y Azure AD bajo NIS2.
* **Azure Landing Zone:** (En revisión) Arquitectura Hub-Spoke segura.

### 🐧 Infrastructure & OS
* [**Linux Server Hardening**](./Linux-Hardening-Guide.md): Blindaje de servidores críticos (Ubuntu/RHEL) expuestos a internet.
* **Windows Server 2025:** (En revisión) GPOs de seguridad para controladores de dominio.

### 🐳 Cloud Native & Containers
* **Kubernetes Security:** Pod Security Standards y configuración de Network Policies.

---
### ⚠️ Nota de Implementación
Estos "Blueprints" priorizan la seguridad sobre la usabilidad. Se recomienda aplicar en entornos de **Staging** antes de pasar a Producción.

**Architected by Sesecpro Engineering Team**
