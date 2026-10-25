# Project Bub7lu$ — el `.ipa`

`ProjectBub7lu-unsigned.ipa` — build sin firmar, 6.828.638 bytes.
`SHA-256 325524965e61172e46d77994a1651cee23b5a5e07666595f5d35ccb62688f8b6`

## Para que la web lo descargue

Panel (`https://project-bub7lu.onrender.com/admin/`) → **Versiones de la IPA** → *Crear versión*:

1. `version` y `build` — las dos, obligatorias.
2. **Archivo**: elegir este `.ipa`. Deja vacíos *dirección* y *tamaño*: se solos.
3. Guardar.

Con archivo subido la versión **nace actual** y el botón de la web (`/dl/ipa`) ya la sirve.
Sin archivo, la fila queda registrada pero sigue descargándose la anterior.

Un `.ipa` firmado o con otro nombre: mismo formulario, *Crear versión* con la versión y el
build que traiga dentro (`CFBundleShortVersionString` y `CFBundleVersion`).

## La dirección de este repo NO es un enlace de descarga

Es privado: desde un teléfono sin tu acceso da 404, y el `.ipa` pesa lo mismo firmando o sin
firmar. La descarga sale siempre de la web. Aquí solo vive el artifact.

## Cuando Codemagic termine una build nueva

1. Bajar el `.ipa` del CI y sustituir el de esta carpeta.
2. `git add -A && git commit && git push` (`origin` = `github.com/bnxyung7/ProjectBub7lu`).
3. Subirla al panel con los tres pasos de arriba.
