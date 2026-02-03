# ☁️ AWS Security Foundations: La Línea Base

Configuración esencial para evitar el compromiso de cuentas AWS. Basado en **AWS CIS Foundations Benchmark**.

## 1. Gestión de Identidad (IAM)
- [ ] **Desactivar Root:** No usar nunca la cuenta raíz (Root) para tareas diarias. Activar MFA físico en ella y borrar sus Access Keys.
- [ ] **IAM Roles:** No usar usuarios IAM para aplicaciones. Utilizar Roles asignados a instancias EC2 o funciones Lambda.
- [ ] **Password Policy:** Mínimo 14 caracteres, rotación cada 90 días y bloqueo tras 5 intentos fallidos.

## 2. Almacenamiento (S3)
- [ ] **Block Public Access:** Activar la configuración "Block Public Access" a nivel de cuenta (Account Level) para evitar fugas de datos accidentales.
- [ ] **Cifrado:** Forzar cifrado en reposo (SSE-S3 o KMS) mediante Bucket Policies para todos los objetos.

## 3. Monitorización y Auditoría
- [ ] **CloudTrail:** Activar CloudTrail en todas las regiones y proteger los logs de modificación.
- [ ] **GuardDuty:** Habilitar detección de amenazas inteligente en la región principal.
