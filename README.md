# Project Bub7lu$ — el `.ipa`

`ProjectBub7lu-unsigned.ipa` — sin firmar, `1.3.0` build `9`, 7.126.204 bytes.
`SHA-256 48d39276a260d657c23c0c833462a7db8597f69eb06bca2c7005ade0a30e54ea`

## Enlace crudo (este repo es público)

```
https://raw.githubusercontent.com/bnxyung7/ProjectBub7lu/main/ProjectBub7lu-unsigned.ipa
```

Medido sin sesión desde fuera: `200`, `Content-Type: application/octet-stream`,
`Access-Control-Allow-Origin: *` y `X-Content-Type-Options: nosniff`, con `Content-Length`
igual al peso de arriba. No trae `Content-Disposition: attachment`, pero con ese tipo y
`nosniff` ningún navegador lo intenta renderizar: baja como archivo. GitHub cachea el
archivo unos 5 minutos, así que tras empujar uno nuevo la dirección tarda eso en cambiar.

Ponerla en el panel: **Versiones de la IPA** → *dirección* = el enlace de arriba, *tamaño* =
7.126.204 (lo que escriba el operador; la web muestra lo que hay en la fila). O dejar que la
web lo sirva ella misma con la otra forma, que es la que sigue.

## La forma que no depende de GitHub

Panel (`https://project-bub7lu.onrender.com/admin/`) → **Versiones de la IPA** → *Crear versión*:

1. `version` y `build` — las dos, obligatorias.
2. **Archivo**: elegir este `.ipa`. Deja vacíos *dirección* y *tamaño*: se solos.
3. Guardar.

Con archivo subido la versión **nace actual** y el botón de la web (`/dl/ipa`) ya la sirve.
Sin archivo, la fila queda registrada pero sigue descargándose la anterior. Y si algún día
sustituyes el archivo por una dirección, la fila servida por esta web se borra: solo se
conserva la última forma que se guardó.

Un `.ipa` firmado o con otro nombre: mismo formulario, *Crear versión* con la versión y el
build que traiga dentro (`CFBundleShortVersionString` y `CFBundleVersion`).

## Lo que significa que sea público

Cualquiera con el enlace baja el binario, y `strings` sobre él ve lo que contenga. Un `.ipa`
sin firmar no da acceso a nada que la API no controle —las keys se comprueban en el
servidor, no dentro del archivo—, pero deja el código y los textos a la vista de quien los
quiera leer. Si alguna vez quieres cerrar esto, el repo se cambia a privado en *Settings →
Danger Zone* y la descarga vuelve a salir solo de `/dl/ipa`.

## Cuando Codemagic termine una build nueva

1. Bajar el `.ipa` del CI y sustituir el de esta carpeta.
2. `git add -A && git commit && git push` (`origin` = `github.com/bnxyung7/ProjectBub7lu`).
3. Subirla al panel con los tres pasos de arriba, o actualizar la dirección si eliges el
   enlace crudo.
