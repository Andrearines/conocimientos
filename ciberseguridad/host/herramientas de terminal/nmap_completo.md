# Nmap Completo para Pentesting, Red Team y Blue Team

## 1. Introducción a Nmap

Nmap (Network Mapper) es una herramienta gratuita, poderosa y ampliamente usada para reconocimiento de red y auditorías de seguridad. Con ella puedes descubrir hosts activos, identificar puertos abiertos, obtener versiones de servicios, detectar sistemas operativos y encontrar vulnerabilidades. Es utilizada tanto por equipos Red Team (ataque) como por Blue Team (defensa).

## 2. Instalación y actualización

### En Debian/Ubuntu:

```bash
sudo apt update
sudo apt install nmap
```

### Desde código fuente (última versión estable):

```bash
wget https://nmap.org/dist/nmap-7.94.tar.bz2
bunzip2 nmap-7.94.tar.bz2
tar -xvf nmap-7.94.tar
cd nmap-7.94
./configure
make
sudo make install
```

### Verificar la versión:

```bash
nmap --version
```

## 3. Sintaxis básica y modos de escaneo

```bash
nmap [opciones] [objetivo]
```

### Ejemplos:

```bash
nmap 192.168.1.1
nmap -sS -p 22,80,443 192.168.1.1
nmap -iL targets.txt
```

## 4. Escaneos más comunes para Red Team

- **SYN scan (sigiloso):**
    

```bash
nmap -sS 192.168.1.1
```

- **TCP connect (normal):**
    

```bash
nmap -sT 192.168.1.1
```

- **UDP scan:**
    

```bash
nmap -sU 192.168.1.1
```

- **Escaneo agresivo:**
    

```bash
nmap -A 192.168.1.1
```

- **Todos los puertos:**
    

```bash
nmap -p- 192.168.1.1
```

## 5. Detección de sistemas, servicios y versiones

- **Sistema operativo:**
    

```bash
nmap -O 192.168.1.1
```

- **Servicios y versiones:**
    

```bash
nmap -sV 192.168.1.1
```

- **Resolución DNS inversa:**
    

```bash
nmap -R 192.168.1.1
```

## 6. Evasión de firewalls, IDS e IPS

- **Cambiar puerto origen:**
    

```bash
nmap -g 53 192.168.1.1
```

- **Fragmentar paquetes:**
    

```bash
nmap -f 192.168.1.1
```

- **Decoys:**
    

```bash
nmap -D RND:10 192.168.1.1
```

- **MAC spoofing:**
    

```bash
nmap --spoof-mac Cisco 192.168.1.1
```

## 7. Automatización con NSE (Nmap Scripting Engine)

- **Scripts predeterminados:**
    

```bash
nmap --script default 192.168.1.1
```

- **Vulnerabilidades conocidas:**
    

```bash
nmap --script vuln 192.168.1.1
```

- **Buscar scripts específicos:**
    

```bash
nmap --script http-title,ftp-anon 192.168.1.1
```

## 8. Blue Team: monitoreo y detección

- **Descubrir hosts activos:**
    

```bash
nmap -sn 192.168.1.0/24
```

- **Detección de puertos en red interna:**
    

```bash
nmap -sS -p- -T3 192.168.1.0/24
```

- **Escaneo regular para inventario:**  
    Programar con cron y guardar resultados con `-oN` o `-oX`.
    

## 9. Casos reales y simulaciones

### Escaneo interno:

```bash
nmap -A -T4 10.0.0.0/24
```

### Reconocimiento externo:

```bash
nmap -Pn -sS -T3 example.com
```

## 10. Ejemplos prácticos paso a paso

1. Identificar hosts:
    

```bash
nmap -sn 192.168.1.0/24
```

2. Escanear puertos:
    

```bash
nmap -sS -p- 192.168.1.100
```

3. Identificar servicios:
    

```bash
nmap -sV -p 22,80,443 192.168.1.100
```

4. Buscar vulnerabilidades:
    

```bash
nmap --script vuln 192.168.1.100
```

## 11. Formatos de salida y parsing

- **Normal:** `-oN output.txt`
    
- **XML:** `-oX report.xml`
    
- **JSON:** `-oJ report.json`
    
- **Grepable:** `-oG grepable.txt`
    
- **Comparar escaneos:** `ndiff scan1.xml scan2.xml`
    

## 12. NSE avanzado: escritura y edición

- Directorio: `/usr/share/nmap/scripts/`
    
- Crear script nuevo (Lua): `mymodule.nse`
    
- Usar `nmap --script-updatedb` para actualizar índice
    

## 13. Escaneo por topología y redes complejas

- **Target múltiples subredes:**
    

```bash
nmap -sS 10.0.0.0/24 192.168.1.0/24
```

- **Uso de proxychains:**
    

```bash
proxychains nmap -sT target.com
```

## 14. Fingerprinting oculto

- **TTL tweaking:** `--ttl 128`
    
- **Desactivar TCP timestamp:** usar firewalls o `--data-length`
    
- **MTU personalizado:** `--mtu 8`
    

## 15. Detección de escaneos con Snort y Zeek

- **Snort:** Reglas para detectar SYN, NULL, FIN, Xmas scan
    
- **Zeek:** Logs de conexiones sospechosas
    
- **Honeypots:** como Honeyd o Cowrie
    

## 16. Integración con otras herramientas

- **Metasploit:**
    

```bash
db_nmap -sS -A 192.168.1.100
```

- **Nikto:**
    

```bash
nikto -host 192.168.1.100
```

- **Masscan + Nmap:**
    

```bash
masscan -p1-65535 192.168.1.100 --rate=1000
nmap -sV -p <puertos_abiertos> 192.168.1.100
```

## 17. Buenas prácticas

- Tener permiso explícito para escanear
    
- Evitar redes de producción
    
- Mantener logs y reportes
    
- Combinar con otras fases de pentesting
    

## 18. Recursos y referencias

- Sitio oficial: [https://nmap.org](https://nmap.org/)
    
- NSE Docs: [https://nmap.org/nsedoc/](https://nmap.org/nsedoc/)
    
- Cheat sheet: [https://hackertarget.com/nmap-cheatsheet/](https://hackertarget.com/nmap-cheatsheet/)
    
- GitHub NSE Scripts: [https://github.com/nmap/nmap/tree/master/scripts](https://github.com/nmap/nmap/tree/master/scripts)
    

## 19. Apéndice: Scripts NSE por categoría

- **auth:** autenticación
    
- **brute:** ataques de fuerza bruta
    
- **default:** scripts comunes
    
- **discovery:** detección de hosts y servicios
    
- **dos:** denegación de servicio
    
- **exploit:** explotación
    
- **malware:** detección de malware
    
- **safe:** no destructivos
    
- **vuln:** vulnerabilidades
    

## 20. Apéndice: Comandos útiles

```bash
nmap -sS -T4 -p 1-1000 192.168.1.10
nmap -A -T3 example.com
nmap -sV --script=http-enum 192.168.1.1
nmap -Pn -sS -p- -T4 --open 10.10.10.10
```

---

Este documento está listo para usarse en plataformas como Obsidian para documentación personal, conocimiento ofensivo/defensivo, o como base para entrenamientos de seguridad informática.
