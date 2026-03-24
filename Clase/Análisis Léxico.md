# Análisis Léxico  
  
El análisis léxico es la primera fase del compilador. Su objetivo es leer el código fuente y dividirlo en unidades llamadas **tokens**.  
  
Un token representa una unidad con significado, como:  
- palabras reservadas (`if`, `while`)  
- identificadores (`x`, `contador`)  
- números (`10`, `3.14`)  
- operadores (`+`, `=`, `==`)


## Expresiones Regulares
Las expresiones regulares (regex) se utilizan para definir patrones que permiten reconocer tokens.  
Cada tipo de token se describe mediante una expresión regular.

### Símbolos básicos

| Símbolo  | Significado                               | Ejemplo       | Coincide con        |
| -------- | ----------------------------------------- | ------------- | ------------------- |
| `a`      | carácter literal                          | `cat`         | `cat`               |
| `.`      | cualquier carácter excepto salto de línea | `a.c`         | `abc`, `a1c`, `axc` |
| `[abc]`  | uno de esos caracteres                    | `c[ao]sa`     | `casa`, `cosa`      |
| `[^abc]` | cualquier carácter excepto esos           | `[^0-9]`      | `a`, `B`, `#`       |
| `[a-z]`  | rango de caracteres                       | `[a-z]+`      | `hola`, `regex`     |
| `[A-Z]`  | letras mayúsculas                         | `[A-Z]{2}`    | `AR`, `US`          |
| `[0-9]`  | dígitos                                   | `[0-9]{3}`    | `123`, `456`        |
| `\d`     | dígito                                    | `\d\d`        | `10`, `25`          |
| `\D`     | no dígito                                 | `\D+`         | `abc`, `#@`         |
| `\w`     | letra, número o `_`                       | `\w+`         | `abc123`            |
| `\W`     | carácter que no es letra, número ni `_`   | `\W+`         | `@#!`               |
| `\s`     | espacio en blanco                         | `a\sb`        | `a b`               |
| `\S`     | carácter que no es espacio                | `\S+`         | `abc`, `123`        |
| `*`      | 0 o más repeticiones                      | `a*`          | `""`, `a`, `aaa`    |
| `+`      | 1 o más repeticiones                      | `a+`          | `a`, `aa`, `aaa`    |
| `?`      | 0 o 1 repetición                          | `colou?r`     | `color`, `colour`   |
| `{n}`    | exactamente n veces                       | `\d{3}`       | `123`               |
| `{n,m}`  | entre n y m veces                         | `\d{2,4}`     | `12`, `123`, `1234` |
| `{n,}`   | al menos n veces                          | `a{2,}`       | `aa`, `aaa`, `aaaa` |
| `()`     | agrupar expresiones                       | `(ab)+`       | `ab`, `abab`        |
| `(?: )`  | grupo sin captura                         | `(?:ab)+`     | `ab`, `abab`        |
| `\|`     | alternativa (OR)                          | `gato\|perro` | `gato`, `perro`     |
| `^`      | inicio de línea                           | `^Hola`       | `Hola mundo`        |
| `$`      | fin de línea                              | `mundo$`      | `hola mundo`        |
| `\b`     | límite de palabra                         | `\bsol\b`     | `sol`               |
| `\B`     | no límite de palabra                      | `\Bsol\B`     | `consolador`        |
| `\.`     | carácter especial escapado                | `www\.com`    | `www.com`           |
| `\\`     | barra invertida literal                   | `C:\\temp`    | `C:\temp`           |

## Rangos numéricos  

Estas expresiones permiten validar números dentro de ciertos límites.

| Rango     | División lógica      | Expresión regular                                    |
| --------- | -------------------- | ---------------------------------------------------- |
| 0–9       | un dígito            | `[0-9]`                                              |
| 1–9       | un dígito sin 0      | `[1-9]`                                              |
| 10–99     | dos dígitos          | `[1-9][0-9]`                                         |
| 0–99      | uno o dos dígitos    | `[1-9]?[0-9]`                                        |
| 100–999   | tres dígitos         | `[1-9][0-9]{2}`                                      |
| 0–255     | dividir por centenas | `25[0-5] \| 2[0-4][0-9] \| 1[0-9]{2} \| [1-9]?[0-9]` |
| 0–59      | dividir por decenas  | `[0-5][0-9]`                                         |
| 1–31      | dividir por decenas  | `3[0-1] \| [1-2][0-9] \| [1-9]`                      |
| 1900–2099 | dividir por miles    | `19[0-9]{2} \| 20[0-9]{2}`                           |
| 00-23     |                      | [0-1][0-9]\|2[0-3]                                   |

## Uso en análisis léxico
Estas expresiones son utilizadas por herramientas como ANTLR para construir analizadores léxicos automáticamente.

# Analizador Léxico con ANTLR
https://github.com/FranciscoAmeri/Ejercicios_Sintacticos/tree/lexico/Ejercicio/demo

Script de ejecución rápida:

```bash
echo "Analizador Léxico con ANTLR"

echo "Limpiando proyecto con Maven..."
mvn clean

echo "Compilando proyecto con Maven..."
mvn package

echo "Compilación completada. Ejecutando análisis léxico..."
java -jar target/demo-1.0-jar-with-dependencies.jar ejemplo.txt
```

## Requisitos

Verificar que estén instalados:

```bash
mvn -v
java -version
```

Como:
- Apache Maven 3.9.14
- OpenJDK 24

---- 

## Instalación

1. Clonar el repositorio:
```bash
git clone https://github.com/FranciscoAmeri/Ejercicios_Sintacticos.git
```
2. Entrar al proyecto:

```bash
cd Ejercicios_Sintacticos/Ejercicio/demo
```

## Instrucciones de uso

### 1. Crear archivo de prueba

Crear un archivo `.txt` con código a analizar:

```bash
echo "int x = 10;" > test.txt
```

### 2. Compilar el proyecto

```bash
mvn clean package
```

### 3. Ejecutar el análisis

```bash
java -jar target/demo-1.0-jar-with-dependencies.jar test.txt
```

### 4. Interpretar resultados

El programa muestra una tabla con:

- **TIPO**: nombre del token
- **LEXEMA**: texto reconocido
- **LÍNEA**: número de línea
- **COLUMNA**: posición

## Prioridad de tokens en ANTLR

El orden de las reglas léxicas es importante:
1. Reglas más específicas primero
2. Palabras reservadas antes que identificadores
3. Operadores compuestos antes que simples

### Ejemplo

```antlr
// Correcto
MAYOR_IGUAL : '>=' ;
MAYOR       : '>'  ;

// Incorrecto
MAYOR       : '>'  ;
MAYOR_IGUAL : '>=' ;
```

## Estructura del proyecto

- `src/main/antlr4/.../MiLenguaje.g4` → definición de tokens
- `target/` → archivos compilados
- `README.md` → documentación

## Modificar el analizador

1. Editar el archivo:
```
MiLenguaje.g4

grammar MiLenguaje;  
  
// Regla parser mínima (requerida por ANTLR)  
programa : .*? EOF ;  
  
fragment LETRA : [A-Za-z];  
fragment DIGITO : [0-9];  
  
// TOKENS  
  
  
SUM  : '+' ;  
RES  : '-' ;  
MUL  : '*' ;  
DIV  : '/' ;  
MOD  : '%' ;  
  
  
  
  
  
ID : (LETRA | '_') (LETRA | DIGITO | '_')*;  
  
INTEGER : DIGITO+;  
DECIMAL : INTEGER'.'INTEGER;  
CHARACTER: '\'' (~['\r\n] | '\\' .) '\'' ;  
  
// Comentarios - Se ignoran durante el análisis  
//COMENTARIO_LINEA : '//' ~[\r\n]*; sin ocultar  
COMENTARIO_LINEA : '//' ~[\r\n]* -> skip;  
COMENTARIO_BLOQUE : '/*' .*? '*/' -> skip;  
  
WS : [ \r\n\t] -> skip ;  
OTRO : . ;
```

2. Recompilar:

```bash
mvn clean package
```

3. Ejecutar con nuevos inputs

