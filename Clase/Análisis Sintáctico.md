
# Análisis Sintáctico

El análisis sintáctico es la etapa del compilador que, a partir de los tokens entregados por el analizador léxico, verifica si la secuencia cumple las reglas de una gramática formal y construye el árbol sintáctico.

Ese árbol representa la estructura jerárquica de la expresión y respeta reglas como precedencia y asociatividad de operadores.

Una gramática libre de contexto está formada por:

- No terminales: símbolos que se pueden expandir (E, T, F)

- Terminales: símbolos del lenguaje (id, +, * , ( , ))

- Producciones: reglas que indican cómo se reemplazan los no terminales

- Símbolo inicial: desde donde comienza el análisis


Ejemplo clásico de gramática para expresiones aritméticas:

```
E -> E + T | E - T | T  
T -> T * F | F  
F -> (E) | id
```

Esta gramática ya codifica:

- `*` tiene mayor precedencia que `+` y `-`

- Las operaciones son asociativas a izquierda

- Los paréntesis alteran la precedencia


Existen dos grandes estrategias para analizar:

- **Análisis descendente (top-down)**: parte del símbolo inicial e intenta generar la cadena de entrada.

- **Análisis ascendente (bottom-up)**: parte de la cadena de entrada e intenta reducirla hasta llegar al símbolo inicial.


Si una gramática puede analizarse de forma ascendente, siempre puede transformarse para analizarse de forma descendente eliminando la recursión izquierda.


## Qué hace realmente el analizador sintáctico

El parser no “entiende cuentas”. Lo único que hace es:

1. Leer tokens.
2. Ver si esos tokens pueden generarse con las reglas.
3. Construir el árbol que muestra **cómo** se generaron.

Por ejemplo, para:

```
5 + 2 * 3
```

El árbol resultante fuerza primero `2 * 3` y después la suma. Eso no lo decide el parser “porque sabe matemáticas”, sino porque la gramática obliga a que pase por `T -> T * F` antes que por `E -> E + T`.

La precedencia está en la **forma de la gramática**, no en el parser.


## Relación entre gramática y árbol sintáctico

Cada reducción (en ascendente) o derivación (en descendente) corresponde a un nodo del árbol.

- Reducir `id -> F` crea un nodo F
- Reducir `F -> T` crea un nodo T
- Reducir `T -> E` crea un nodo E

El árbol no es otra cosa que el historial de esas operaciones.


## Análisis sintáctico con ANTLR4

ANTLR4 implementa análisis **ascendente con soporte directo para recursión izquierda**, lo que permite escribir la gramática exactamente como se define en teoría:

```
expr : expr '+' term
     | expr '-' term
     | term
     ;

term : term '*' factor
     | factor
     ;

factor : '(' expr ')'
       | ID
       ;
```

No hace falta transformar la gramática para que funcione. ANTLR se encarga internamente.

Lo que en teoría distingue ascendente y descendente, en ANTLR queda “oculto”, pero el mecanismo que usa corresponde al enfoque ascendente.

## Proyecto

[Codigo](https://github.com/FranciscoAmeri/Ejercicios_Sintacticos/tree/ejercicio1)

#### Archivos del proyecto

- **MiLenguaje.g4**: Gramática ANTLR4 que incluye:
    
    - Tokens léxicos para un lenguaje similar a C
    - Reglas sintácticas para análisis de expresiones
    
- **ExpressionValidator.java**: Programa que:
    
    - Analiza expresiones aritméticas
    - Muestra el árbol sintáctico generado
    - Proporciona una descripción estructurada de la expresión
    
- **expresion_aritmetica.txt**: Ejemplo simple (5 + 2 * 3)
    
- **expresiones_complejas.txt**: Múltiples ejemplos de expresiones
    
- **compilar_expresiones.sh**: Script para compilar todo el proyecto


## Cómo usar este proyecto

### Compilación

```
chmod +x compilar_expresiones.sh
./compilar_expresiones.sh
```

### Análisis de expresiones

```
java -cp '.:/ruta/a/antlr-4.12.0-complete.jar' ExpressionValidator expresion_aritmetica.txt
```

### Visualización

El programa abrirá una ventana que muestra el árbol sintáctico y también imprimirá:

- La representación textual del árbol
- Una descripción estructurada de la expresión

## Qué observar al ejecutar

Cuando ejecutes el ejemplo `5 + 2 * 3`, observá que:

- El nodo raíz es una suma
- El lado derecho de la suma es un subárbol de multiplicación
- Ese orden coincide exactamente con las reglas `E`, `T` y `F`

Eso demuestra visualmente cómo la gramática controla la precedencia sin necesidad de reglas extra.

