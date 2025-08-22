# Documentación de Apache2 en Debian 12

---

## 🔹 ¿Qué es Apache?

Apache es un **servidor web** de código abierto que permite **servir páginas web** a través del protocolo HTTP/HTTPS. Es uno de los servidores más usados en el mundo.

---

## 🔹 Instalación de Apache en Debian 12

### 📥 Paso 1: Actualiza tu sistema

bash

CopiarEditar

`sudo apt update && sudo apt upgrade`

### 🧰 Paso 2: Instala Apache

bash

CopiarEditar

`sudo apt install apache2`

### 🟢 Paso 3: Verifica que esté funcionando

Abre tu navegador y visita:

arduino

CopiarEditar

`http://localhost`

O en otra máquina de la red:

cpp

CopiarEditar

`http://IP_DE_TU_SERVIDOR`

Deberías ver la **página de bienvenida de Apache**.

---

## 🔹 Comandos básicos de Apache

|Acción|Comando|
|---|---|
|Iniciar Apache|`sudo systemctl start apache2`|
|Detener Apache|`sudo systemctl stop apache2`|
|Reiniciar Apache|`sudo systemctl restart apache2`|
|Recargar config (sin reiniciar)|`sudo systemctl reload apache2`|
|Estado del servicio|`sudo systemctl status apache2`|
|Habilitar en el arranque|`sudo systemctl enable apache2`|
|Deshabilitar en arranque|`sudo systemctl disable apache2`|

---

## 🔹 Estructura de directorios en Debian

|Ruta|Descripción|
|---|---|
|`/etc/apache2/`|Configuración principal de Apache|
|`/var/www/html/`|Directorio raíz por defecto (DocumentRoot)|
|`/etc/apache2/sites-available/`|Archivos de configuración de sitios|
|`/etc/apache2/sites-enabled/`|Enlaces simbólicos a los sitios activos|
|`/etc/apache2/mods-available/`|Módulos disponibles|
|`/etc/apache2/mods-enabled/`|Módulos activados|
|`/var/log/apache2/`|Archivos de log|

---

## 🔹 Configurar un sitio web (Virtual Host)

### 1. Crea el directorio del sitio

bash

CopiarEditar

`sudo mkdir -p /var/www/miweb.com/public_html`

### 2. Asigna permisos

bash

CopiarEditar

`sudo chown -R $USER:$USER /var/www/miweb.com/public_html`

### 3. Crea un archivo HTML

bash

CopiarEditar

`echo "<h1>Hola desde Apache</h1>" > /var/www/miweb.com/public_html/index.html`

### 4. Crea el archivo de configuración del sitio

bash

CopiarEditar

`sudo nano /etc/apache2/sites-available/miweb.com.conf`

Pega lo siguiente:

apacheconf

CopiarEditar

`<VirtualHost *:80>     ServerAdmin webmaster@localhost     ServerName miweb.com     ServerAlias www.miweb.com     DocumentRoot /var/www/miweb.com/public_html     ErrorLog ${APACHE_LOG_DIR}/miweb_error.log     CustomLog ${APACHE_LOG_DIR}/miweb_access.log combined </VirtualHost>`

### 5. Habilita el sitio

bash

CopiarEditar

`sudo a2ensite miweb.com.conf`

### 6. Reinicia Apache

bash

CopiarEditar

`sudo systemctl reload apache2`

### 7. Agrega al archivo `/etc/hosts` para pruebas locales:

bash

CopiarEditar

`sudo nano /etc/hosts`

Agrega:

CopiarEditar

`127.0.0.1    miweb.com`

---

## 🔹 Activar HTTPS con Certbot y Let's Encrypt

### 1. Instala Certbot

bash

CopiarEditar

`sudo apt install certbot python3-certbot-apache`

### 2. Ejecuta Certbot

bash

CopiarEditar

`sudo certbot --apache`

Sigue las instrucciones para generar certificados SSL gratuitos y habilitar HTTPS.

---

## 🔹 Logs de Apache

| Tipo de Log       | Ruta                          |
| ----------------- | ----------------------------- |
| Accesos (visitas) | `/var/log/apache2/access.log` |
| Errores           | `/var/log/apache2/error.log`  |

Ver logs en tiempo real:

bash

CopiarEditar

`tail -f /var/log/apache2/access.log`

---

## 🔹 Habilitar / deshabilitar módulos

|Acción|Comando|
|---|---|
|Habilitar módulo|`sudo a2enmod nombre_modulo`|
|Deshabilitar módulo|`sudo a2dismod nombre_modulo`|
|Ejemplo: activar rewrite|`sudo a2enmod rewrite`|

Luego de cualquier cambio:

bash

CopiarEditar

`sudo systemctl restart apache2`

---

## 🔹 Seguridad básica

1. **Desactiva listado de directorios**:  
    En el VirtualHost, asegúrate de no tener `Indexes`:
    
    apacheconf
    
    CopiarEditar
    
    `<Directory /var/www/html>     Options -Indexes </Directory>`
    
2. **Oculta versión de Apache**:  
    Edita `/etc/apache2/conf-available/security.conf` y cambia:
    
    apacheconf
    
    CopiarEditar
    
    `ServerTokens Prod ServerSignature Off`
    
3. **Activa firewall (ufw)**:
    
    bash
    
    CopiarEditar
    
    `sudo apt install ufw sudo ufw allow 'Apache Full' sudo ufw enable`
    

---

## 🔹 Otros comandos útiles

- Verificar errores de configuración:
    
    bash
    
    CopiarEditar
    
    `sudo apache2ctl configtest`
    
- Mostrar módulos cargados:
    
    bash
    
    CopiarEditar
    
    `apache2ctl -M`
    
- Mostrar versión de Apache:
    
    bash
    
    CopiarEditar
    
    `apache2 -v`
    

---

## 🔹 Recursos adicionales

- Documentación oficial: [https://httpd.apache.org/docs/](https://httpd.apache.org/docs/)
    
- Certbot (SSL gratuito): https://certbot.eff.org/instructions
    
- Apache para Debian: https://wiki.debian.org/Apache