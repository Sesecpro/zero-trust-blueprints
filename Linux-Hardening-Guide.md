# 🐧 Linux Server Hardening Standard (v2.0)

**Classification:** Internal / Client Confidential
**Compliance Mapping:** CIS Linux Benchmark Level 2 | NIST SP 800-53 (CM-6, AC-6)
**Target OS:** RHEL 9 / Ubuntu 24.04 LTS / Debian 12
**Owner:** Sesecpro Engineering Team
**Last Updated:** February 2026

---

## 1. Executive Summary
Este estándar define la configuración de seguridad obligatoria para servidores Linux en entornos de producción.

El objetivo es garantizar la **inmutabilidad del sistema**, la **minimización de privilegios** y la **trazabilidad forense** de todas las acciones administrativas, cumpliendo con los requisitos de resiliencia operativa de la directiva **NIS2**.

---

## 2. Secure Shell (SSH) Defense
*Objective: Prevent brute force, credential theft, and lateral movement.*

### 2.1 SSH Daemon Configuration
**File:** `/etc/ssh/sshd_config`
**Compliance:** CIS 5.2 | NIST AC-3

La configuración por defecto es insegura. Se debe aplicar el siguiente bloque de configuración estricta:

```bash
# --- Sesecpro Hardening Start ---
Protocol 2
PermitRootLogin no               # CRITICAL: Bloqueo total de acceso root directo
PasswordAuthentication no        # CRITICAL: Prohibido uso de passwords (solo llaves)
PermitEmptyPasswords no
PubkeyAuthentication yes
ChallengeResponseAuthentication no
UsePAM yes

# Attack Surface Reduction
X11Forwarding no                 # Previene ataques de canal lateral
AllowAgentForwarding no          # Previene secuestro de agente SSH
MaxAuthTries 3                   # Mitigación estricta de fuerza bruta
LoginGraceTime 60                # Desconectar sesiones no autenticadas rápidamente
ClientAliveInterval 300          # Timeout de sesión inactiva (5 min)
ClientAliveCountMax 0

# User Whitelisting (Principle of Least Privilege)
# AllowUsers admin_sysdeploy
# --- Sesecpro Hardening End ---
```

> **Remediation Action:** `systemctl reload sshd`

---

## 3. Kernel Hardening (Network Stack)
*Objective: Prevent network-based attacks (IP Spoofing, MITM, DDoS).*

### 3.1 Sysctl Parameters
**File:** `/etc/sysctl.d/99-security.conf`
**Compliance:** CIS 3.1 - 3.3 | NIST SC-5

Protección a nivel de núcleo contra paquetes malformados y manipulación de rutas.

```ini
# IP Spoofing Protection (RFC 3704)
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

# Disable ICMP Redirects (Prevent MITM / Routing Table Poisoning)
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.accept_source_route = 0

# Log Martians (Detect suspicious packets from impossible addresses)
net.ipv4.conf.all.log_martians = 1

# Ignore ICMP Broadcasts (Smurf Attack Mitigation)
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Randomize Virtual Memory Layout (ASLR)
kernel.randomize_va_space = 2
```

> **Remediation Action:** `sysctl --system`

---

## 4. Audit & Forensics (Auditd)
*Objective: Who did what, where, and when.*

### 4.1 Audit Rules
**Compliance:** NIS2 Incident Reporting | PCI-DSS 10.2
**File:** `/etc/audit/rules.d/audit.rules`

El sistema debe registrar cualquier cambio en archivos críticos de identidad o intentos de escalada de privilegios.

```bash
# Identity Database Changes
-w /etc/passwd -p wa -k identity_modification
-w /etc/shadow -p wa -k identity_modification
-w /etc/group -p wa -k identity_modification

# Privilege Escalation (Sudo/Su usage)
-w /etc/sudoers -p wa -k scope_change
-w /etc/sudoers.d/ -p wa -k scope_change
-w /bin/su -p x -k priv_escalation
-w /usr/bin/sudo -p x -k priv_escalation

# Suspicious Command Execution (Root)
-a always,exit -F arch=b64 -F euid=0 -S execve -k root_cmd
```

> **Remediation Action:** `service auditd restart`

---

## 5. File System Integrity
*Objective: Prevent execution of unauthorized binaries in temporary folders.*

### 5.1 Critical Mount Points
**Compliance:** CIS 1.1
Montar particiones temporales con flags restrictivos en `/etc/fstab` para evitar la ejecución de malware o scripts descargados.

| Mount Point | Options | Description |
| :--- | :--- | :--- |
| `/tmp` | `nodev,nosuid,noexec` | Prevent execution of binaries |
| `/var/tmp` | `nodev,nosuid,noexec` | Prevent persistent malware |
| `/dev/shm` | `nodev,nosuid,noexec` | Secure shared memory |

### 5.2 Umask Setting
**File:** `/etc/profile`
Establecer `umask 027` asegura que los nuevos archivos creados solo sean legibles por el propietario y su grupo, nunca por "otros".

---

### ⚠️ Disclaimer
*This configuration is strict ("Hardened by Design") and may break legacy applications requiring root access or weak authentication. Thorough testing in Staging is mandatory before Production rollout.*

**© 2026 Sesecpro Engineering.**
