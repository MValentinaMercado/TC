
[Proyecto](https://github.com/FranciscoAmeri/Ejercicios_Sintacticos/tree/lexico/Ejercicio/demo)

## Nivel 1: Fundamentos Básicos

### Ejercicio 1.1: Identificar Números Enteros
**Objetivo:** Reconocer números enteros positivos.


**Casos de prueba:**
```
123
0
999
42
```

**Casos que NO deben reconocer:**
```
12.5
-10
abc

```
#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;  
  
programa : .*? EOF ;  
  
fragment DIGITO : [0-9];  
  
// Invalido  
DECIMAL : DIGITO+ '.' DIGITO+ ;  
NEGATIVO : '-' DIGITO+ ;  
ID : [a-zA-Z]+ ;  
  
//Valido  
INTEGER : DIGITO+ ;  
  
WS : [ \r\n\t]+ -> skip ;  
  
OTRO : . ;
```

##### Ejecución
```
echo "123 0 999 42 12.5 -10 abc" > Ej1-1.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar ejemplo.txt
```

---

### Ejercicio 1.2: Identificar Palabras Simples
**Objetivo:** Reconocer palabras formadas solo por letras.


**Casos de prueba:**
```
hola
Mundo
variable
```

**Casos que NO deben reconocer:**
```
var123
_nombre
123abc
```
#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

fragment LETRA : [a-zA-Z];
fragment DIGITO : [0-9];

// Invalido
ALFANUMERICO : LETRA+ DIGITO+ | DIGITO+ LETRA+ ;
GUION_BAJO   : '_' LETRA+ ;

// Valido
PALABRA : LETRA+ ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "hola Mundo variable var123 _nombre 123abc" > Ej1-2.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej1-2.txt
```

---

### Ejercicio 1.3: Identificar Operadores Simples
**Objetivo:** Reconocer los operadores aritméticos básicos.


**Casos de prueba:**
```
+
-
*
/
```

**Archivo de prueba (ejercicio1_3.txt):**
```
5 + 3
10 - 2
4 * 7
9 / 3
```

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

SUMA : '+' ;
RESTA : '-' ;
MULT : '*' ;
DIV  : '/' ;

INTEGER : [0-9]+ ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "5 + 3
10 - 2
4 * 7
9 / 3" > Ej1-3.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej1-3.txt
```

---

## Nivel 2: Patrones Combinados

### Ejercicio 2.1: Números Enteros con Signo
**Objetivo:** Reconocer números enteros positivos y negativos.


**Casos de prueba:**
```
123
-45
+67
0
-999
```

**Casos que NO deben reconocer:**
```
12.5
--10
+-5
```

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;  
  
programa : .*? EOF ;  
  
fragment DIGITO : [0-9];  
  
DECIMAL : DIGITO+ '.' DIGITO+ ;  
DOBLE_SIGNO : ('+'|'-')('+'|'-') DIGITO+ ;  
  
ENTERO : ('+'|'-')? DIGITO+ ;  
  
WS : [ \r\n\t]+ -> skip ;  
  
OTRO : . ;
```

##### Ejecución
```
echo "123 -45 +67 0 -999 12.5 --10 +-5" > Ej2-1.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej2-1.txt
```


---

### Ejercicio 2.2: Números Decimales
**Objetivo:** Reconocer números con punto decimal.


**Casos de prueba:**
```
3.14
0.5
123.456
1.0
```

**Casos que NO deben reconocer:**
```
123
.5
5.
1.2.3
```

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;  
  
programa : .*? EOF ;  
  
fragment DIGITO : [0-9];  
  
ENTERO : DIGITO+ ;  
MAL_DECIMAL_1 : '.' DIGITO+ ;  
MAL_DECIMAL_2 : DIGITO+ '.' ;  
MAL_DECIMAL_3 : DIGITO+ '.' DIGITO+ '.' DIGITO+ ;  
  
DECIMAL : DIGITO+ '.' DIGITO+ ;  
  
WS : [ \r\n\t]+ -> skip ;  
  
OTRO : . ;
```

##### Ejecución
```
echo "3.14 0.5 123.456 1.0 123 .5 5. 1.2.3" > Ej2-2.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej2-2.txt
```


---

### Ejercicio 2.3: Identificadores Válidos
**Objetivo:** Reconocer identificadores que comienzan con letra o guión bajo, seguidos de letras, dígitos o guiones bajos.


**Casos de prueba:**
```
variable
_temp
contador1
MI_CONSTANTE
camelCase
snake_case
var_123_test
```

**Casos que NO deben reconocer:**
```
123abc
-variable
var-name
```

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

fragment LETRA : [a-zA-Z];
fragment DIGITO : [0-9];

EMPIEZA_NUMERO : DIGITO+ LETRA+ (LETRA|DIGITO|'_')* ;
GUION_INICIAL : '-' (LETRA|'_')+ ;
GUION_MEDIO : (LETRA|'_')+ '-' (LETRA|'_')+ ;

ID : (LETRA | '_') (LETRA | DIGITO | '_')* ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "variable _temp contador1 MI_CONSTANTE camelCase snake_case var_123_test 123abc -variable var-name" > Ej2-3.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej2-3.txt
```


---

## Nivel 3: Tokens de Lenguajes de Programación

### Ejercicio 3.1: Palabras Reservadas
**Objetivo:** Distinguir entre palabras reservadas e identificadores.

**Expresión Regular (palabras reservadas):**
```
if | while | for | else | return | int | char | double
```

**Expresión Regular (identificador):**
```
[A-Za-z_][A-Za-z0-9_]*
```

**Casos de prueba:**
```
if          -> IF (palabra reservada)
while       -> WHILE (palabra reservada)
miVar       -> ID (identificador)
contador    -> ID (identificador)
return      -> RETURN (palabra reservada)
```

**Archivo de prueba (ejercicio3_1.txt):**
```
if (x > 0) {
    return x;
} else {
    while (y < 10) {
        y = y + 1;
    }
}
```


#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;  
  
programa : .*? EOF ;  

IF : 'if' ;  
WHILE : 'while' ;  
FOR : 'for' ;  
ELSE : 'else' ;  
RETURN : 'return' ;  
INT : 'int' ;  
CHAR : 'char' ;  
DOUBLE : 'double' ;  
  
ID : [a-zA-Z_][a-zA-Z0-9_]* ;  
  
NUM : [0-9]+ ;  
IGUAL : '=' ;  
SUMA : '+' ;  
LLAVE_ABRE : '{' ;  
LLAVE_CIERRA : '}' ;  
PAR_ABRE : '(' ;  
PAR_CIERRA : ')' ;  
PUNTO_COMA : ';' ;  
MENOR : '<' ;  
MAYOR : '>' ;  
  
WS : [ \r\n\t]+ -> skip ;  
  
OTRO : . ;
```

##### Ejecución
```
echo "if (x > 0) {
    return x;
} else {
    while (y < 10) {
        y = y + 1;
    }
}" > Ej3-1.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej3-1.txt
```


---

### Ejercicio 3.2: Operadores de Comparación
**Objetivo:** Reconocer operadores relacionales.

**Expresiones Regulares:**
```
==  -> EQL
!=  -> DISTINTO
>=  -> MAYOR_IGUAL
<=  -> MENOR_IGUAL
>   -> MAYOR
<   -> MENOR
```

**Casos de prueba:**
```
x == y
a != b
num >= 10
val <= 5
i > 0
j < 100
```

**Nota:** El orden importa (primero `>=` y `<=`, luego `>` y `<`)


#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

EQL          : '==' ;
DISTINTO     : '!=' ;
MAYOR_IGUAL  : '>=' ;
MENOR_IGUAL  : '<=' ;
MAYOR        : '>' ;
MENOR        : '<' ;

ID  : [a-zA-Z_][a-zA-Z0-9_]* ;
NUM : [0-9]+ ;
IGUAL : '=' ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "x == y
a != b
num >= 10
val <= 5
i > 0
j < 100" > Ej3-2.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej3-2.txt
```


---

### Ejercicio 3.3: Cadenas de Texto
**Objetivo:** Reconocer cadenas entre comillas dobles.


**Casos de prueba:**
```
"Hola Mundo"
"123"
"Variable: x"
""
"Texto con espacios"
```

**Casos especiales:**
```
"Comillas \"escapadas\""  -> Nivel avanzado
```

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

STRING : '"' ( ~["\\\r\n] | '\\' . )* '"' ;

ID  : [a-zA-Z_][a-zA-Z0-9_]* ;
NUM : [0-9]+ ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
ni Ej3-3.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej3-3.txt
```


---

## Nivel 4: Patrones Avanzados

### Ejercicio 4.1: Caracteres Literales
**Objetivo:** Reconocer caracteres entre comillas simples.


**Casos de prueba:**
```
'a'
'Z'
'1'
'$'
'\n'   -> carácter de escape
'\t'   -> tabulación
```

**Casos que NO deben reconocer:**
```
''
'ab'
'
```


#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

CARACTER 
    : '\'' ( ~['\\\r\n] | '\\' . ) '\''
    ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "'a' 'Z' '1' '$' '\n' '\t' '' 'ab'" > Ej4-1.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej4-1.txt
```


---

### Ejercicio 4.2: Comentarios de Línea
**Objetivo:** Reconocer y omitir comentarios de una línea.


**Casos de prueba:**
```
// Esto es un comentario
int x = 5;  // comentario al final de línea
// TODO: implementar función
```

**Acción:** `-> skip` (no generar token)


#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

COMENTARIO_LINEA : '//' ~[\r\n]* -> skip ;

ID  : [a-zA-Z_][a-zA-Z0-9_]* ;
NUM : [0-9]+ ;
IGUAL : '=' ;
PUNTO_COMA : ';' ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "// Esto es un comentario
int x = 5;  // comentario al final de línea
// TODO: implementar función" > Ej4-2.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej4-2.txt
```


---

### Ejercicio 4.3: Comentarios de Bloque
**Objetivo:** Reconocer comentarios multilínea.

**Casos de prueba:**
```
/* Comentario simple */

/*
 * Comentario
 * de varias
 * líneas
 */

int x = 5; /* inline */ int y = 10;
```

**Acción:** `-> skip`


#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

COMENTARIO_BLOQUE : '/*' .*? '*/' -> skip ;

COMENTARIO_LINEA : '//' ~[\r\n]* -> skip ;

ID  : [a-zA-Z_][a-zA-Z0-9_]* ;
NUM : [0-9]+ ;
IGUAL : '=' ;
PUNTO_COMA : ';' ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "/* Comentario simple */

/*
 * Comentario
 * de varias
 * líneas
 */

int x = 5; /* inline */ int y = 10;" > Ej4-3.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej4-3.txt
```


---

## Nivel 5: Casos Complejos

### Ejercicio 5.1: Números Científicos
**Objetivo:** Reconocer notación científica.

**Expresión Regular:** `[0-9]+\.?[0-9]*[eE][+-]?[0-9]+`

**Casos de prueba:**
```
1.5e10
3E-5
2.0e+3
1e10
```

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;  
  
programa : .*? EOF ;  
  
fragment DIGITO : [0-9];  
  
CIENTIFICO : DIGITO+ ('.' DIGITO*)? [eE] [+-]? DIGITO+ ;  
  
DECIMAL : DIGITO+ '.' DIGITO+ ;  
ENTERO : DIGITO+ ;  
  
WS : [ \r\n\t]+ -> skip ;  
  
OTRO : . ;
```

##### Ejecución
```
echo "1.5e10 3E-5 2.0e+3 1e10" > Ej5-1.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej5-1.txt
```


---

### Ejercicio 5.2: Números Hexadecimales
**Objetivo:** Reconocer números en base 16.

**Expresión Regular:** `0[xX][0-9A-Fa-f]+`

**Casos de prueba:**
```
0xFF
0x1A2B
0X00
0xDEADBEEF
```

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

HEX : '0' [xX] [0-9A-Fa-f]+ ;

ENTERO : [0-9]+ ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "0xFF 0x1A2B 0X00 0xDEADBEEF" > Ej5-2.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej5-2.txt
```


---

### Ejercicio 5.3: Operadores Compuestos
**Objetivo:** Reconocer operadores de asignación compuesta.

**Expresiones Regulares:**
```
+=  -> SUM_ASIG
-=  -> RES_ASIG
*=  -> MUL_ASIG
/=  -> DIV_ASIG
++  -> INC
--  -> DEC
```

**Casos de prueba:**
```
x += 5
y -= 3
z *= 2
contador++
--indice
```

**Nota:** El orden importa (primero `++`, `--`, `+=`, etc., luego `+`, `-`)

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

INC      : '++' ;
DEC      : '--' ;
SUM_ASIG : '+=' ;
RES_ASIG : '-=' ;
MUL_ASIG : '*=' ;
DIV_ASIG : '/=' ;

SUMA : '+' ;
RESTA : '-' ;
MULT : '*' ;
DIV  : '/' ;

ID  : [a-zA-Z_][a-zA-Z0-9_]* ;
NUM : [0-9]+ ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "x += 5
y -= 3
z *= 2
contador++
--indice" > Ej5-3.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Ej5-3.txt
```


---

## Nivel 6: Proyecto Integrador

### Ejercicio 6.1: Analizador Léxico Completo
**Objetivo:** Crear un lexer completo para un mini-lenguaje.

**Archivo de entrada (programa.txt):**
```c
int main() {
    int x = 10;
    double y = 3.14;
    char c = 'A';

    // Calcular suma
    if (x > 5) {
        y += 2.5;
        x++;
    } else {
        while (x < 100) {
            x *= 2;
        }
    }

    /* Retornar resultado */
    return x + y;
}
```

**Tokens esperados:**
- Palabras reservadas: `int`, `double`, `char`, `if`, `else`, `while`, `return`
- Identificadores: `main`, `x`, `y`, `c`
- Números: `10`, `3.14`, `5`, `2.5`, `100`, `2`
- Caracteres: `'A'`
- Operadores: `=`, `>`, `+=`, `++`, `*=`, `+`
- Delimitadores: `(`, `)`, `{`, `}`, `;`
- Comentarios: (ignorados)

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

INT    : 'int' ;
DOUBLE : 'double' ;
CHAR   : 'char' ;
IF     : 'if' ;
ELSE   : 'else' ;
WHILE  : 'while' ;
RETURN : 'return' ;

INC      : '++' ;
SUM_ASIG : '+=' ;
MUL_ASIG : '*=' ;

IGUAL : '=' ;
SUMA  : '+' ;
MAYOR : '>' ;

PAR_ABRE     : '(' ;
PAR_CIERRA   : ')' ;
LLAVE_ABRE   : '{' ;
LLAVE_CIERRA : '}' ;
PUNTO_COMA   : ';' ;


DECIMAL : [0-9]+ '.' [0-9]+ ;
ENTERO : [0-9]+ ;
CARACTER : '\'' ( ~['\\\r\n] | '\\' . ) '\'' ;

ID : [a-zA-Z_][a-zA-Z0-9_]* ;

COMENTARIO_LINEA  : '//' ~[\r\n]* -> skip ;
COMENTARIO_BLOQUE : '/*' .*? '*/' -> skip ;


WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "int main() {
    int x = 10;
    double y = 3.14;
    char c = 'A';

    // Calcular suma
    if (x > 5) {
        y += 2.5;
        x++;
    } else {
        while (x < 100) {
            x *= 2;
        }
    }

    /* Retornar resultado */
    return x + y;
}" > programa.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar programa.txt
```


---

## Ejercicios de Desafío

### Desafío 1: Ambigüedad Léxica
**Problema:** ¿Qué reconoce primero: palabra reservada o identificador?

```
forvar
for
forma
```

**Solución:** Usar la regla más específica primero (palabra reservada antes que ID).

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

FOR : 'for' ;

ID : [a-zA-Z_][a-zA-Z0-9_]* ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "forvar for forma" > Des1.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Des1.txt
```


---

### Desafío 2: Cadenas con Escapes
**Objetivo:** Reconocer cadenas que contienen caracteres escapados.

**Expresión Regular:** `"([^"\\]|\\.)*"`

**Casos de prueba:**
```
"Hola \"Mundo\""
"Línea 1\nLínea 2"
"Ruta: C:\\Users\\nombre"
```

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

STRING : '"' ( ~["\\\r\n] | '\\' . )* '"' ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
ni > Des2.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Des2.txt
```


---

### Desafío 3: Detectar Errores Léxicos
**Objetivo:** Identificar tokens inválidos y reportar errores.

**Casos de error:**
```
@#$         -> Carácter inválido
'abc'       -> Carácter con más de un símbolo
12.34.56    -> Número mal formado
```

**Implementación:** Token `OTRO : .` para capturar errores.

#### Resolución

##### MiLenguaje.g4
```
grammar MiLenguaje;

programa : .*? EOF ;

fragment DIGITO : [0-9];

CARACTER_INVALIDO : '\'' .+ '\'' ;
NUM_MAL : DIGITO+ '.' DIGITO+ '.' DIGITO+ ;

DECIMAL : DIGITO+ '.' DIGITO+ ;
ENTERO  : DIGITO+ ;

ID : [a-zA-Z_][a-zA-Z0-9_]* ;

WS : [ \r\n\t]+ -> skip ;

OTRO : . ;
```

##### Ejecución
```
echo "@#$ 'abc' 12.34.56" > Des3.txt

mvn clean package

java -jar target/demo-1.0-jar-with-dependencies.jar Des3.txt
```


---
