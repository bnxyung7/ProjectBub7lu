# Project Bub7lu$ — carpeta de descarga

Esta carpeta guarda el archivo exacto que la web sirve en **Descargar IPA**. No es un
backup del proyecto: es el punto de entrega. Lo que hay aquí dentro es lo que se publica.

## Última versión

| Campo | Valor |
| --- | --- |
| Archivo | `ProjectBub7lu-unsigned.ipa` |
| Versión | 1.3.0 |
| Build | 9 |
| Tamaño | 6 828 638 bytes (6.8 MB) |
| SHA-256 | `325524965e61172e46d77994a1651cee23b5a5e07666595f5d35ccb62688f8b6` |
| Nombre visible | Project Bub7lu$ |
| Identificador | `com.apple.mobile.MobileHouseArrest` |
| Requiere | iOS 16.0 o posterior, arm64, iPhone y iPad |
| SDK con el que se compiló | iphoneos26.5 |
| Idiomas incluidos | inglés, español, portugués (Brasil), vietnamita, chino simplificado |
| Fecha del artifact | 24/10/2026 22:38 |

El ejecutable de este archivo ya lleva los cambios hasta el commit `3fad525` del repositorio
del IPA. Se comprobó leyendo el binario, no suponiéndolo: contiene los símbolos de las
categorías horizontales (`catalog.pick_hint`, `paintbrush.pointed.fill`, `shippingbox.fill`)
y ya no contiene la clave que se eliminó con ese commit (`activation.server_required`). O sea
que este `.ipa` es la última compilación publicada, no una vieja.

## Firma: sin firmar, a propósito

El archivo **no** se puede instalar tal cual. No lleva `embedded.mobileprovision` ni
carpeta `_CodeSignature`, porque el proyecto no tiene certificado de desarrollador: las dos
pipelines de compilación (`codemagic.yaml` y `.github/workflows/build.yml`) producen
deliberadamente un `.ipa` sin firmar y así lo declaran.

Antes de instalar hay que re-firmarlo con el flujo que ya se usa para repartirlo (AltStore,
Sideloadly, firma propia…). Nada de esto lo decide la web: la web solo entrega el archivo.

## Cómo se actualiza esta carpeta

1. Compilar en CI (Codemagic o GitHub Actions) el commit que se quiera publicar.
2. Descargar el artifact `ProjectBub7lu-unsigned.ipa` y sustituir el que está aquí.
3. Volver a medir: `sha256sum ProjectBub7lu-unsigned.ipa` y el tamaño en bytes.
4. Actualizar la tabla de arriba con la versión, el build, el tamaño y el hash nuevos.
5. Subirlo al panel (**Versiones de la IPA**), que es lo que de verdad publica el archivo.
   Mientras no se suba, la web sigue diciendo «Próximamente» por muy nuevo que sea lo que
   hay en esta carpeta.

El nombre del archivo se mantiene fijo a propósito: es el nombre que suelta CI y el que la
web escribe en `Content-Disposition` cuando lo entrega.

## Para que el botón de la web descargue de verdad

Medido en producción, no deducido: `https://project-bub7lu.onrender.com/api/v1/download`
responde `available: false` y `version: null`, así que la portada y `/download` anuncian
«Próximamente» y no enlazan nada. Falta solo una fila en el panel; el archivo ya está aquí.

La web ahora **sí** sirve el archivo ella misma. `AppRelease` guardaba antes solo una
dirección HTTPS que había que alojar en otro sitio; desde este cambio la fila admite
también los bytes del `.ipa` (`download_data` y `download_filename`) y la propia web los
entrega en `/dl/ipa`. No hace falta ningún host externo, ni publicar un repositorio, ni
tocar código: se sube el archivo en el panel y la página cambia sola, porque
`services.download_info()` lee la fila en cada petición.

### Lo que hay que escribir, paso a paso

Entra en `https://project-bub7lu.onrender.com/admin` → **Versiones de la IPA** →
formulario **Crear versión**:

| Campo del panel | Valor para este artifact |
| --- | --- |
| Número de versión | `1.3.0` |
| Build | `9` |
| Versión mínima que sigue funcionando | `1.0.0` |
| Subir el archivo IPA | `ProjectBub7lu-unsigned.ipa` (el de esta carpeta) |
| Dirección del archivo IPA (HTTPS) | **déjalo vacío** |
| Tamaño en MB (opcional) | **déjalo vacío**: con archivo subido la web mide el tamaño real |
| Changelog de esta versión | lo que quieras que lea el cliente |

Pulsa **Crear versión**. El panel responde «Versión publicada como actual.» y, si el
archivo llegó, su guardado dice «Archivo publicado: … bytes. La web lo sirve ella misma.».
La sección pasa de «Próximamente» a «Versión 1.3.0 lista para descargar», con el nombre del
archivo, su tamaño medido y el botón apuntando a `/dl/ipa`.

Para cambiar el archivo sin mover el número de versión existe el formulario aparte,
**Archivo de la versión actual**. Ahí se sube el `.ipa` nuevo y el mensaje es «Archivo
publicado: …».

Dos reglas del formulario, para que no sorprendan:

- **Archivo o dirección, no las dos en el mismo guardado.** Se rechaza con «Sube el archivo
  o escribe una dirección externa, pero no las dos.» Lo último que se guarde es lo que la
  web ofrece: si se escribe una dirección encima de un archivo guardado, los bytes se van.
- **Techo de `MAX_UPLOAD_MB`, 8 MB si nadie lo pone en Render.** Este `.ipa` pesa
  6 828,638 bytes (6.51 MiB) y cabe. Un archivo más pesado se niega entero —no se guarda a
  medias— y entonces queda la opción de la dirección externa.

En **Versión mínima que sigue funcionando** va una versión de la app, nunca un número de iOS:
el IPA compara ese campo con su propia versión (`AppInfo.releaseVersion`). Escribir `16.0` ahí
haría que la app creyera estar por debajo del mínimo y avisara a todas las instalaciones.

### Qué queda por comprobar en producción

El recorrido está probado aquí, con el archivo real de esta carpeta: servidor local, subida
por HTTP desde el panel, `GET /dl/ipa` devolviendo 200 con 6 828,638 bytes,
`Content-Disposition: attachment; filename="ProjectBub7lu-unsigned.ipa"` y un SHA-256 que
coincide con el de la tabla de arriba; y en el navegador la página pública enlaza
`/dl/ipa` y el botón responde «Descarga en curso (7 MB).». También está probado con las
pruebas del repo (`tests/test_release_upload.py`, 15 casos).

Lo que aquí no se puede comprobar y hay que mirar al desplegar:

- Que Render deje pasar un cuerpo de ~6,8 MB en el POST del panel. El límite del propio
  Flask está puesto para eso; el techo del proxy de Render no se ha medido nunca. Si la
  subida falla en producción, la salida inmediata es `MAX_UPLOAD_MB` más alto o la
  dirección externa.
- Que `ensure_schema()` añada `download_data` y `download_filename` a la base en el primer
  arranque tras el despliegue. Es lo que hace con cada columna nueva y nullable, y hay una
  prueba que la borra y la recupera; pero ocurre en el arranque real de Render, no aquí.
- Que baje de verdad en un iPhone. Aquí se midió la cabecera que lo hace posible
  (`attachment`, no `inline`); instalarlo lo comprueba un teléfono.

Los bytes viven en la base de datos, así que cada `.ipa` que se publique ocupa ~6,8 MB del
almacenamiento de esa base hasta que se sustituya.
