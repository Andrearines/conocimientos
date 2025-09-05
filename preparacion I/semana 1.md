# Linux – Usuarios, Grupos, Permisos y Enlaces (Documentación Completa)

---

## 1️⃣ Usuarios y Grupos

### whoami

- **Descripción:** Muestra el usuario actual.
    
- **Parámetros:** ninguno.
    
- **Ejemplo:**
    

```
$ whoami
steven
```

### groups [usuario]

- **Descripción:** Lista los grupos a los que pertenece un usuario.
    
- **Parámetros:**
    
    - `[usuario]` → opcional, por defecto muestra grupos del usuario actual.
        
- **Ejemplo:**
    

```
$ groups hola
hola : hola sudo users
```

### cat /etc/group

- **Descripción:** Muestra todos los grupos y sus miembros.
    
- **Parámetros importantes:**
    
    - `-n` → muestra IDs numéricos en lugar de nombres
        
    - `-s` → silencioso (no mostrar info redundante)
        
- **Ejemplo:**
    

```
$ cat /etc/group
sudo:x:27:steven,hola
users:x:100:steven,hola
```

### adduser usuario

- **Descripción:** Crea un usuario nuevo.
    
- **Parámetros importantes:**
    
    - `--disabled-password` → crea usuario sin contraseña
        
    - `--gecos` → proporciona info como nombre, teléfono
        
- **Ejemplo:**
    

```
$ sudo adduser hola
```

### usermod

- **Descripción:** Modifica un usuario existente.
    
- **Parámetros importantes:**
    
    - `-aG grupo usuario` → agrega el usuario a un grupo
        
    - `-L` → bloquea usuario
        
    - `-U` → desbloquea usuario
        
    - `-G grupo1,grupo2` → asigna grupos (reemplaza los actuales)
        
- **Ejemplo agregar a grupo:**
    

```
$ sudo usermod -aG sudo hola
```

- **Ejemplo eliminar de un grupo:**
    

```
$ sudo gpasswd -d hola sudo
```

---

## 2️⃣ Permisos de Archivos y Directorios

### ls -l [archivo/directorio]

- **Descripción:** Lista archivos con detalles.
    
- **Parámetros importantes:**
    
    - `-l` → listado largo
        
    - `-a` → incluir archivos ocultos
        
    - `-h` → tamaño legible (`K`, `M`)
        
- **Ejemplo:**
    

```
$ ls -lh
-rwxr-xr-x 1 steven steven 0 sep  5 12:30 hola.txt
```

### chmod

- **Descripción:** Cambia permisos de archivos o directorios.
    
- **Formas de usar:**
    
    - **Simbólica:** `u/g/o/a +|-|= rwx`
        
    

sudo chmod u+r archivo.txt  
sudo chmod g+w archivo.txt  
sudo chmod o-x archivo.txt

```
  - **Octal:** `0-7`
```

sudo chmod 755 archivo.txt

````
- **Parámetros importantes:**
  - `-R` → recursivo (aplica a directorios y subdirectorios)
  - `--reference=archivo` → copia permisos de otro archivo
- **Ejemplos prácticos:**
```bash
sudo chmod 700 solo_el_owner.txt
sudo chmod 744 todo_pueden_leer.txt
sudo chmod 777 todo.txt
sudo chmod 775 ownes_grups.txt
````

### chown

- **Descripción:** Cambia propietario y grupo.
    
- **Parámetros importantes:**
    
    - `usuario:grupo archivo` → define propietario y grupo
        
    - `-R` → recursivo
        
    - `--reference=archivo` → copia propietario/grupo de otro archivo
        
- **Ejemplo:**
    

```bash
sudo chown steven:users hola.txt
```

### umask

- **Descripción:** Muestra o define la máscara de permisos por defecto.
    
- **Parámetros:**
    
    - `-S` → muestra en forma simbólica (`u=rwx,g=rw,o=r`)
        
- **Ejemplo:**
    

```bash
$ umask
002
$ umask -S
u=rwx,g=rw,o=r
```

---

## 3️⃣ Permisos especiales

|Permiso|Comando|Descripción|
|---|---|---|
|setuid|`chmod u+s archivo`|Ejecuta archivo como propietario|
|setgid|`chmod g+s archivo/directorio`|Ejecuta archivo como grupo o hereda grupo en directorios|
|sticky bit|`chmod +t directorio`|Solo el dueño puede borrar archivos del directorio|

- **Ejemplos prácticos:**
    

```bash
sudo chmod u+s archivo_permisos_onwer
sudo chmod g+s archivo_permisos_g
sudo chmod +t solo_onwer_eliminar
```

- **Notas de `ls -l`**
    
    - `s` minúscula → permiso especial + ejecutable
        
    - `S` mayúscula → permiso especial sin ejecutable
        
    - `t` → sticky bit activo
        

---

## 4️⃣ Enlaces

### Hard Link

- **Descripción:** Dos nombres apuntan al mismo inode.
    
- **Comando:** `ln archivo_original enlace_duro`
    
- **Ejemplo:**
    

```bash
ln hola.txt hola_hard.txt
ls -l
# -rw-rw-r-- 2 steven steven 0 hola.txt
# -rw-rw-r-- 2 steven steven 0 hola_hard.txt
```

### Soft Link (Simbólico)

- **Descripción:** Archivo que apunta a otro archivo por nombre.
    
- **Comando:** `ln -s archivo_original enlace_simbólico`
    
- **Ejemplo:**
    

```bash
ln -s hola.txt hola_soft.txt
ls -l
# lrwxrwxrwx 1 steven steven 8 sep 5 12:50 hola_soft.txt -> hola.txt
```

---

## 5️⃣ Resumen permisos octales

|Octal|Propietario|Grupo|Otros|Significado|
|---|---|---|---|---|
|700|rwx|---|---|Solo propietario accede|
|744|rwx|r--|r--|Otros solo leen|
|775|rwx|rwx|r-x|Grupo puede escribir, otros leer|
|777|rwx|rwx|rwx|Todos tienen todo|

---

## 6️⃣ Ejemplos prácticos finales

```bash
# Crear archivo solo accesible por propietario
touch solo_el_owner.txt
sudo chmod 700 solo_el_owner.txt

# Crear archivo que otros solo leen
touch todo_pueden_leer.txt
sudo chmod 744 todo_pueden_leer.txt

# Crear archivo accesible para todos
touch todo.txt
sudo chmod 777 todo.txt

# Crear archivo con permisos especiales
touch archivo_permisos_onwer
sudo chmod u+s archivo_permisos_onwer

# Crear directorio con sticky bit
mkdir solo_onwer_eliminar
sudo chmod +t solo_onwer_eliminar

# Crear enlaces duros y simbólicos
touch origen.txt
ln origen.txt hard.txt
ln -s origen.txt soft.txt
```

---

## 7️⃣ Comandos de administración rápida

- **Eliminar usuario de un grupo:**
    

```bash
sudo gpasswd -d usuario grupo
# Ejemplo:
sudo gpasswd -d hola sudo
```

- **Ver procesos activos:**
    

```bash
ps aux | head -10
```

- **Ver estado de un servicio (ej. SSH):**
    

```bash
systemctl status ssh
```

---

**Esta documentación cubre todos los comandos, permisos, usuarios, grupos, bits especiales y enlaces, lista para estudiar y practicar **