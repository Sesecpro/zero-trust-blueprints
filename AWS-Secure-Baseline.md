# ☁️ AWS Security Foundations: Baseline Standard (v2.0)

**Classification:** Internal / Client Confidential
**Compliance Mapping:** CIS AWS Benchmark v3.0 | NIST SP 800-53 (Rev. 5)
**Owner:** Sesecpro Engineering Team
**Last Updated:** February 2026

---

## 1. Executive Summary
Este estándar define la **Configuración Base de Seguridad (Secure Baseline)** obligatoria para cualquier cuenta de AWS gestionada bajo la supervisión de Sesecpro.

El objetivo es establecer un perímetro de identidad "Zero Trust" y garantizar la **auditabilidad forense** y la **inmutabilidad de los datos** antes del despliegue de cualquier carga de trabajo productiva.

---

## 2. Identity & Access Management (IAM) Governance
*Objective: Ensure only authorized identities can access resources.*

### 2.1 Root Account Hardening (Critical)
**CIS 1.5 - 1.7** | La cuenta raíz posee privilegios ilimitados. Su uso operativo está estrictamente prohibido.
* **MFA Mandate:** MFA de hardware (YubiKey/Token) obligatorio para el usuario Root.
* **Key Deletion:** Eliminar todas las `Access Keys` asociadas a Root.
* **Alerting:** Configurar alarma en CloudWatch para cualquier uso de Root.
  * *Metric Filter:* `{ $.userIdentity.type = "Root" }`

### 2.2 Password Policy Specification
**CIS 1.8** | Cumplimiento estricto con estándares SOC2 y PCI-DSS.
* **Implementation (AWS CLI):**
```bash
aws iam update-account-password-policy \
    --minimum-password-length 14 \
    --require-symbols \
    --require-numbers \
    --require-uppercase-characters \
    --allow-users-to-change-password \
    --max-password-age 90 \
    --password-reuse-prevention 24
```

### 2.3 Roles & Federation (No IAM Users)
**CIS 1.16** | Se prohíbe la creación de usuarios IAM para personas físicas.
* **Standard:** El acceso a la consola debe realizarse mediante **AWS IAM Identity Center** (anteriormente SSO) federado con el IdP corporativo (Azure AD / Okta).
* **Workloads:** Las aplicaciones deben usar **IAM Roles** adjuntos a recursos (EC2/Lambda), nunca credenciales estáticas (`AKIA...`).

---

## 3. Data Protection & Encryption
*Objective: Prevent data exfiltration and ensure confidentiality.*

### 3.1 S3 Block Public Access (Account Level)
**CIS 2.1.1** | Prevención de fugas de datos por error humano o mala configuración de Terraform.
* **Mandato:** Aplicar bloqueo a nivel de cuenta para anular configuraciones de buckets individuales.
* **Implementation (AWS CLI):**
```bash
aws s3control put-public-access-block \
    --account-id <ACCOUNT_ID> \
    --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

### 3.2 Encryption at Rest (Enforcement)
**NIST SC-28** | Rechazar cualquier escritura de objetos que no solicite cifrado.
* **Bucket Policy Snippet (JSON):**
```json
{
  "Sid": "DenyIncorrectEncryptionHeader",
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::<bucket_name>/*",
  "Condition": {
    "StringNotEquals": {
      "s3:x-amz-server-side-encryption": "AES256"
    }
  }
}
```

---

## 4. Detective Controls & Forensics
*Objective: Detect incidents in real-time and preserve evidence.*

### 4.1 CloudTrail Integrity
**CIS 3.1 - 3.7** | Trazabilidad inmutable de todas las llamadas a la API.
* **Configuración:** `Multi-Region Trail` habilitado.
* **Validation:** Activar `Log File Validation` para garantizar la no-repudiación de los logs (Hash chain).
* **Storage:** Los logs deben enviarse a un Bucket S3 centralizado (Log Archive Account) con cifrado KMS-CMK.

### 4.2 AWS Config
**CIS 2.5** | Grabación continua de cambios de configuración para auditoría.
* **Scope:** Grabar todos los recursos soportados en todas las regiones activas.
* **Retention:** Mínimo 7 años (requerimiento regulatorio financiero/NIS2).

---

## 5. Network Security (VPC)
*Objective: Reduce attack surface.*

### 5.1 Default VPC Deletion
**Best Practice** | Eliminar la VPC por defecto (`172.31.0.0/16`) en todas las regiones. Esto evita despliegues accidentales en redes públicas no seguras.

### 5.2 Flow Logs
**NIST AU-2** | Habilitar VPC Flow Logs con destino a CloudWatch Logs para análisis de tráfico este-oeste y detección de balizamiento (C2 traffic).

---

## 6. Threat Detection
* **GuardDuty:** Habilitar en todas las regiones. Centralizar hallazgos en la cuenta de "Seguridad".
* **Security Hub:** Activar estándares "AWS Foundational Security Best Practices" y "CIS Benchmark".

---

### ⚠️ Disclaimer
*This document contains proprietary methodologies of Sesecpro. Unauthorized distribution outside the client organization is prohibited. Implementation of these controls requires testing in a non-production environment.*

**© 2026 Sesecpro Engineering.**
