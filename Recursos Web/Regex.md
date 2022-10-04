# REGEX
**Enlaces**
- [Curso](https://platzi.com/cursos/expresiones-regulares/)
## Teoria
### Que son las expresiones regulares?
Las **expresiones regulares** son patrones de caracteres que te permite ir seleccionando o descartando datos en un archivo de texto como por ejemplo csv, o en una línea o un input, según coincidan o no con este patrón.

Debes ser muy selectivo y especifico en ellas para encontrar lo que verdaderamente necesitas.

Las expresiones regulares están atadas a casi cualquier lenguaje!

### El carácter (.)

**Caracter.** Representación gráfica en bits de algún código, en mayor de los casos ASCII. Es la unidad mínima que se puede abstraer de una cadena de caracteres.

**Archivo de texto.** Serie de cadenas de caracteres. Una sucesión de líneas.

**Cadena de caracteres.** Un carácter seguido de otro carácter, seguido de otro carácter.

## Docs
Las búsquedas en las expresiones regulares funcionan en múltiplos de la cantidad de caracteres que explícitamente indicamos.
Regex

### Caracteres especiales
| Regex | Tipo | Que es? | Descripcion |
|:-:|:-:|-|-|
|`.`|Character| Predefinida| Cualquier carácter, selecciona cada uno de los caracteres|
|`\d`|Digit| Predefinida| Encuentra todos los dígitos (número) de 0 a 9, es equivalente a poner [0-9].|
|`\w`|Word| Predefinida| Encuentra todos los caracteres que son parte de una palabra, tanto letras (minúsculas o mayúsculas) como números y el guion bajo, es equivalente a poner [a-zA-Z0-9_]|
|`\s`|Space| Predefinida| Encuentra todos los espacios (los saltos de línea y tabuladores también son espacios).|
|`[0-9]`|Specific Digit| Construida| Encuentra todos los dígitos de 0 a 9.|
|`[a-z]`|Specific Word Character| Construida| Encontrara los caracteres de a-z. |
|`\`|Diagonal Invertida| Construida| Escapa caracteres especiales como `.` -> `\.`|
|`*`| Greedy - todo | Delimitador numérico | Haya o no haya, Cero o mas (Todo)|
|`+`| Uno o mas | Delimitador numérico | Uno o mas (Debe haber)|
|`?`| Cero o uno | Delimitador numérico| Cero o uno (Puede haber)|
|`{1,2}` |Contador {a,b} |Contador |Indica de manera precisa un rango de repeticion de un caracter |
| **`+?`** | Delimitador | Delimitador | Busca los grupos más pequeños posibles segun la condicion dada |
| **`\W o \D`** | No Word o No space| Predefinida | Negacion de `\w` o negacion de `\d` |
| **`^`** | Not | Predefinida | Negacion de lo que se le pida dentro de `[]` |
| **`^ $`** | Inicio y final de linea | Delimitadores | Hace si toda la linea coincide con la condicion |
| **`()`** | Agrupar | Agrupador | Agrupa una clase |
### Ejempos
#### Numeros telefonicos
```js
// encuentra un numero telefonico de 6 digitos
/(\d{2,2}[\-\.]?){3,3}/

\d{2,2} // Encuentra 2 digitos
[\-\.]? // Pueden tener o no un guion o un punto despues de cada 2 digitos

// 552384

// 84-75-48

// 56.38.83

```
```jsx
const array = [
  '+504 8394-8385',
  '+540 12439559',
  '+503 1993-4959',
  '+5048394-8385',
  '0384 9595',
  '9394-8584',
  '94-83-74-38',
  '+504-94-83-49-57',
  '123456789123',
  '1234z678',
]

console.log(
  array.filter(
    n => n.match(/^(\+504\s?\-?){0,1}(\d{2,2}\s?\-?){3,3}\d\d$/)
  )
)
```