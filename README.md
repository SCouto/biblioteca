# 📚 Luceroteka

Biblioteca y préstamos de libros para el AFA Gloria Fuertes · CEIP Ntra. Sra. del Lucero.

Es **un único archivo** (`library.html`): se abre con doble clic en el ordenador o en el móvil. No necesita instalación, servidor ni conexión a internet, y los datos no salen de tu equipo.

## Qué hace

- **Libros:** añadir, editar (pulsa sobre el libro) y borrar (dentro de la ficha). Se pueden repetir libros (varios ejemplares); aparece una etiqueta *duplicado*.
- **Préstamos** con estado **Prestado / Devuelto / Retrasado**, fecha de préstamo, devolución prevista (por defecto, 2 semanas) y fecha real de devolución. El estado "retrasado" se calcula solo con la fecha.
- **Buscador** de libros (título, autor, notas) y de préstamos (libro, autor, persona), sin tildes ni mayúsculas.
- Al crear un préstamo se busca el libro escribiendo: salen los más parecidos con su autor, y los que ya están prestados aparecen marcados como **PRESTADO**.
- **PC y móvil:** en el ordenador, tablas ordenables; en el móvil, tarjetas con botón **+**. Botón para cambiar de vista.
- **Tema** claro, oscuro o automático.

## Cómo usarla

1. Descarga `library.html` (botón *Raw* → *Guardar como*, o clona el repositorio).
2. Ábrela con doble clic. Para guardado automático en un archivo, usa **Chrome o Edge** en el ordenador.

## Dónde se guardan los datos

| Dónde | Cómo |
|---|---|
| **En el navegador** (`localStorage`) | Siempre, en cada cambio. Es propio de cada navegador y de la ruta del archivo: otro navegador no ve esos datos. |
| **En un archivo CSV** (solo Chrome/Edge en el ordenador) | En **⚙ Datos → Nuevo archivo de datos** (o *Abrir*) eliges un `.csv` una vez. Desde entonces cada cambio se escribe en él, sin pulsar nada. Al abrir la app se **fusiona** el archivo con lo que hay en el navegador y se reescribe el resultado. Tras reiniciar el navegador puede salir un aviso **Reconectar** (un clic). |
| **Copia manual** | **⚙ Datos → Exportar todo** descarga un CSV. **Importar CSV** lo vuelve a cargar. |

En Firefox, Safari y los visores de archivos del móvil no se puede escribir en un archivo desde una página web, así que solo se guarda en el navegador. Si no hay archivo vinculado, la app avisa cuando pasan 7 días sin exportar.

> La fusión no detecta borrados ni ediciones: si borras un libro en la app pero sigue en el archivo CSV, volverá a aparecer al fusionar.

## Formato del CSV

Un solo archivo para todo. Una fila por préstamo; un libro sin préstamos ocupa una fila con las columnas de préstamo vacías.

```
Título,Autor,Notas,Prestado a,Fecha préstamo,Devolución prevista,Fecha devolución,Estado,ID
```

- Se abre en Excel con cualquier configuración regional (lleva `sep=,` y BOM).
- La columna **ID** identifica cada ejemplar, así los libros repetidos no se mezclan al importar. Si editas el archivo a mano o lo haces en Excel, puedes dejarla vacía: las filas con el mismo título se tratarán como ejemplares distintos.
- Al importar se detecta el separador (`,` `;` o tabulador), se aceptan cabeceras en español e inglés (con o sin tildes) y fechas `AAAA-MM-DD` o `DD/MM/AAAA`.
- El **Estado** se ignora salvo para marcar como devuelto un préstamo sin fecha de devolución; "retrasado" se deduce de la fecha.

## Datos de ejemplo

**⚙ Datos → Cargar libros y préstamos de ejemplo** añade 72 libros y 20 préstamos para probar. *Quitar los datos de ejemplo* los elimina (solo mientras no hayas recargado desde un archivo CSV, que no guarda la marca de ejemplo).

## Desarrollo

Un solo archivo HTML con JavaScript plano, sin dependencias ni compilación. Más detalles de arquitectura y decisiones en [`CLAUDE.md`](CLAUDE.md).
