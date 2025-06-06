# 🌐 Usar un túnel SSH para navegar como si estuvieras en otro equipo (con VPN)

Esta guía permite redirigir el tráfico de tu navegador (Firefox) desde una Mac al equipo Ubuntu, el cual tiene acceso a una VPN.

---

## ✅ Requisitos

- Equipo Ubuntu con VPN activa y conexión a internet.
- Acceso SSH desde Mac al equipo Ubuntu.
- Firefox instalado en Mac.

---

## 🧩 1. Activar el servidor SSH en Ubuntu

En el equipo **Ubuntu**:

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

Verifica que el servicio esté corriendo:

```bash
sudo systemctl status ssh
```

---

## 🔐 2. Verifica conexión SSH desde tu Mac

Desde la **MacBook**, asegúrate de poder conectarte por SSH:

```bash
ssh usuario@IP_UBUNTU
```

> Reemplaza `usuario` e `IP_UBUNTU` con tu usuario y la IP del Ubuntu (por ejemplo, `eperez@192.168.100.6`).

---

## 🔄 3. Crear un túnel SOCKS con SSH

En tu Mac, abre Terminal y ejecuta:

```bash
ssh -D 1080 -q -C -N usuario@IP_UBUNTU
```

- `-D 1080`: abre un proxy SOCKS en el puerto 1080 local.
- `-q`: silencioso.
- `-C`: habilita compresión.
- `-N`: no ejecuta comandos remotos.

> Esto redirige el tráfico desde tu Mac a través del Ubuntu.

---

## 🌍 4. Configurar Firefox para usar el túnel

1. Abre Firefox.
2. Visita: `about:preferences`
3. Baja hasta **Network Settings** → click en **Settings...**
4. Configura así:

```
✔ Manual proxy configuration:
   SOCKS Host: 127.0.0.1      Port: 1080
   ✔ SOCKS v5
   ✔ Proxy DNS when using SOCKS v5
```
<strong>NOTA:</strong> Toma en cuenta que el Host es 127.0.0.1 y no la `IP_UBUNTU` (por ejemplo, `192.168.100.6`).

5. Deja todos los demás campos vacíos.
6. Guarda y cierra.

---

## 🧪 5. Verifica que estás navegando desde Ubuntu

Abre Firefox y visita:

```text
https://ipinfo.io
```

Deberías ver la IP pública del equipo Ubuntu (o la de la VPN a la que está conectado).

---

## 🧰 6. (Opcional) Automatizar

### Crear un alias en tu `.zshrc` o `.bashrc`:

```bash
alias vpn-ubuntu='ssh -D 1080 -q -C -N usuario@IP_UBUNTU'
```

### Usar autenticación sin contraseña:

En tu Mac:

```bash
ssh-keygen -t ed25519 -C "tu-email@example.com"
ssh-copy-id usuario@IP_UBUNTU
```

---

## 🧼 7. Cierre del túnel

Para cerrar el túnel, presiona `Ctrl + C` en la terminal donde hiciste el SSH.

---

## 🧯 Solución de problemas

- ❌ **Firefox no tiene internet**:
  - Asegúrate de que el SOCKS Host sea `127.0.0.1`, **no la IP del Ubuntu**.
  - Revisa si el puerto 1080 está activo: `lsof -i :1080`
  - El equipo Ubuntu debe tener acceso a internet o VPN activa.

- ❌ **No puedes conectar por SSH**:
  - Verifica que `openssh-server` esté instalado y corriendo.
  - Asegúrate de estar en la misma red o que el puerto 22 esté abierto.

---

## 📌 Notas adicionales

- Este método redirige solo aplicaciones que tú configures manualmente.
- Para redirigir todo el tráfico de tu Mac (no solo Firefox), puedes usar herramientas como:
  - [Proxifier](https://www.proxifier.com/)
  - `proxychains` o `tsocks` para apps CLI.
