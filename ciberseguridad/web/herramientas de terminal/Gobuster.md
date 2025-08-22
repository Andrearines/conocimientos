# Documentación Completa de Gobuster

## 🚀 Descripción General

Gobuster es una herramienta escrita en Go, utilizada para realizar ataques de fuerza bruta en servicios web con el fin de descubrir:

- Directorios y archivos ocultos
    
- Subdominios DNS
    
- Virtual hosts
    
- Buckets de Amazon S3 (modo s3)
    
- URIs de Azure
    

---

## 🔧 Instalación

### En Debian/Kali:

```bash
sudo apt install gobuster
```

### Desde el código fuente (Go debe estar instalado):

```bash
go install github.com/OJ/gobuster/v3@latest
```

El ejecutable estará en `~/go/bin/gobuster`, asegúrate de agregarlo a tu `PATH`.

---

## 🔎 Modos de Uso

### 1. Directorios y Archivos (`dir`)

Busca directorios y archivos ocultos en un sitio web.

```bash
gobuster dir -u http://example.com -w /ruta/wordlist.txt
```

**Parámetros comunes:**

- `-u`: URL objetivo
    
- `-w`: Wordlist
    
- `-x`: Extensiones (ej: php, html, txt)
    
- `-t`: Cantidad de hilos (default: 10)
    
- `-o`: Archivo de salida
    
- `--wildcard`: Detección de respuestas falsas (wildcard DNS)
    
- `-s`: Códigos de estado HTTP a mostrar (ej. 200,204,301)
    

**Ejemplo completo:**

```bash
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt -x php,html -t 50 -o resultado.txt
```

---

### 2. Subdominios DNS (`dns`)

Descubre subdominios usando una wordlist.

```bash
gobuster dns -d example.com -w /ruta/wordlist.txt
```

**Opciones comunes:**

- `-d`: Dominio objetivo
    
- `-w`: Wordlist
    
- `-t`: Hilos
    
- `-o`: Salida
    
- `--wildcard`: Detectar DNS wildcard
    

**Ejemplo:**

```bash
gobuster dns -d target.com -w /usr/share/wordlists/dns/subdomains-top1million-110000.txt -t 30
```

---

### 3. Virtual Hosts (`vhost`)

Descubre virtual hosts ocultos que responden a la misma IP.

```bash
gobuster vhost -u http://example.com -w /ruta/wordlist.txt
```

**Ejemplo:**

```bash
gobuster vhost -u http://target.com -w /usr/share/wordlists/vhosts.txt -t 50
```

---

### 4. Buckets S3 (`s3`)

Fuerza nombres de buckets en Amazon S3.

```bash
gobuster s3 -w /ruta/wordlist.txt
```

---

### 5. URIs de Azure (`azure`)

Descubre endpoints de Azure.

```bash
gobuster azure -w /ruta/wordlist.txt
```

---

## 📄 Opciones Generales

|Opcion|Descripción|
|---|---|
|`-u`|URL objetivo|
|`-w`|Wordlist a usar|
|`-t`|Hilos de ejecución (concurrencia)|
|`-o`|Guardar resultados en archivo|
|`-q`|Modo silencioso|
|`--no-error`|No mostrar errores HTTP|
|`-s`|Mostrar solo ciertos códigos de estado|
|`-k`|No verificar el certificado SSL|

---

### Instalar `dirb` y sus wordlists

`sudo apt update sudo apt install dirb`

ubicacion de las listas:   `/usr/share/dirb/wordlists`
    

---

## 🎓 Consejos para Ciberseguridad

- Usa `-s 200,204,301,302` para filtrar resultados útiles
    
- Usa `--wildcard` si el servidor responde lo mismo a todo
    
- Siempre prueba primero con listas pequeñas
    
- No olvides analizar los resultados con herramientas como Burp Suite
    

---

## 🔒 Ejemplo Final

```bash
gobuster dir -u http://192.168.1.10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt -t 50 -s 200,204,301,302 -o resultado.txt
```

---

## ✉️ Recursos

- Repositorio oficial: [https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)
    
- Wordlists: [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)