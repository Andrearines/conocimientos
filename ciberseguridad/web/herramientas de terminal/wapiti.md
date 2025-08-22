**Documentación de Uso: Wapiti para Pentesting Web**

---

### 📊 Descripción General

Wapiti es una herramienta de escaneo de vulnerabilidades web que permite auditar la seguridad de aplicaciones web de manera automatizada. Se centra en detectar vulnerabilidades comunes como:

- Inyecciones SQL (SQLi)
    
- Cross-Site Scripting (XSS)
    
- Inyección de comandos
    
- Inclusión de archivos locales/remotos (LFI/RFI)
    
- Archivos de respaldo accesibles
    
- Inyección de cabeceras (CRLF)
    

---

### 🎨 Uso Básico

#### 1. Escanear un objetivo:

```bash
wapiti -u http://127.0.0.1/mutillidae/
```

#### 2. Escanear con todos los módulos:

```bash
wapiti -u http://127.0.0.1/mutillidae/ --module all
```

#### 3. Escanear módulos específicos:

```bash
wapiti -u http://127.0.0.1/mutillidae/ --module sql,xss,file
```

#### 4. Guardar reporte en HTML:

```bash
wapiti -u http://127.0.0.1/mutillidae/ -f html -o ~/reporte_mutillidae
```

---

### 🌐 Módulos disponibles

|Módulo|Ataque realizado|
|---|---|
|`sql`|Inyección SQL|
|`blindsql`|SQLi ciega por tiempo|
|`xss`|Cross-Site Scripting|
|`file`|Inclusión de archivos (LFI/RFI)|
|`backup`|Archivos de respaldo (`.bak`, `.old`, etc.)|
|`crlf`|Inyección de cabeceras HTTP|
|`exec`|Ejecución remota de comandos|
|`commands`|Command Injection|
|`redirect`|Redirección abierta|

---

### ⚙️ Opciones adicionales

|Opcion|Descripción|
|---|---|
|`-c cookie.txt`|Usa cookies de sesión|
|`--proxy http://127.0.0.1:8080`|Canaliza peticiones a través de Burp Suite|
|`--timeout 5`|Tiempo máximo de espera por respuesta (seg)|
|`--scope folder|domain|
|`--continue`|Reanuda escaneo interrumpido|
|`--verbose 2`|Modo verboso para ver detalle de cada ataque|

---

### ⚠️ Recomendaciones

- **No usar contra objetivos sin autorización.**
    
- Verifica si el sitio usa JavaScript intensivo, ya que Wapiti no lo analiza.
    
- Complementa con otras herramientas como ZAP o Burp Suite para mayor cobertura.
    
- Usa en un entorno controlado como Mutillidae, DVWA o WebGoat.
    

## ⚠️ Consejos

- Usa `--verbose 2` si quieres ver más detalles durante el ataque.
    
- Puedes combinarlo con **Burp Suite en modo proxy** para ver las peticiones.
    
- Wapiti **no interactúa con JavaScript dinámico** como hace OWASP ZAP.
---
