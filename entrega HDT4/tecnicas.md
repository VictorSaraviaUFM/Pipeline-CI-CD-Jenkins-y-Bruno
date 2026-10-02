# Técnicas de prueba — regresión

La colección `bruno/regresion/` ejecuta las siguientes requests en orden. Cada una contiene su aserción y un bloque `docs` con el caso que cubre.

| N.º | Request | Técnica | Valor probado | Resultado esperado |
|---:|---|---|---|---|
| 01 | POST `/reset` | Transición de estados | Restablecer el catálogo al estado inicial | 200; quedan tres libros |
| 02 | GET `/books?author=wilson` | Partición de equivalencia | Subcadena válida en minúsculas | 200; solo el libro de Christie Wilson |
| 03 | POST `/books` | Valores frontera | `year=1450`, mínimo válido | 201; se crea el libro 4 y `Location: /books/4` |
| 04 | PATCH `/books/4` | Valores frontera | `copies=0`, mínimo válido | 200; solo cambia `copies` |
| 05 | PATCH `/books/4` | Valores frontera | `copies=999`, máximo válido | 200; `copies=999` |
| 06 | PATCH `/books/4` | Valores frontera | `copies=1000`, máximo más uno | 422; se rechaza el cambio |
| 07 | GET `/books/4` | Transición de estados | Consultar después del PATCH inválido | 200; se conservan las 999 copias |
| 08 | PUT `/books/4` | Valores frontera y transición de estados | `year=2100`, máximo válido; reemplazo sin enviar `copies` | 200; libro reemplazado y `copies=1` por defecto |
| 09 | GET `/books/4` | Transición de estados | Consultar después del PUT | 200; persisten los valores nuevos y `copies=1` |
| 10 | POST `/books` | Valores frontera | `year=1449`, mínimo menos uno | 422; no se crea el libro |
| 11 | POST `/books` | Partición de equivalencia | `title=""`, longitud inválida de cero | 422; no se crea el libro |
| 12 | DELETE `/books/4` | Transición de estados | Libro existente → eliminado | 204 |
| 13 | GET `/books/4` | Transición de estados | Consultar el libro eliminado | 404 |
| 14 | HEAD `/books/4` | Transición de estados | Verificar el libro eliminado | 404 |
| 15 | OPTIONS `/books` | Partición de equivalencia | Solicitud OPTIONS válida sobre `/books` | 204; `Allow: GET, POST, OPTIONS` |

**Discrepancias con la especificación:** ninguna observada en las corridas de Bruno indicadas por el estudiante. Los reportes JUnit del ZIP documentan la verificación mediante la CLI.
