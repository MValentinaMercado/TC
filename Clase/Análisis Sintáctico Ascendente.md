
# Análisis Sintáctico Ascendente para Expresiones Aritméticas

El análisis ascendente (bottom-up) construye el árbol sintáctico comenzando desde los tokens de entrada hasta llegar al símbolo inicial de la gramática.

A diferencia del descendente, este método no intenta generar la cadena, sino descubrir **qué reglas pudieron haberla generado**.

Por eso se dice que **el análisis ascendente es más potente que el descendente**, ya que puede trabajar directamente con gramáticas que tienen recursión izquierda y no requiere transformaciones.

Trabaja con una **pila** y tres acciones fundamentales:

1. **Desplazamiento (shift)**: mover el símbolo de la entrada a la pila.
2. **Reducción (reduce)**: reemplazar elementos de la pila por un no terminal según una producción.
3. **Aceptación**: cuando toda la entrada se procesa y la pila contiene únicamente el símbolo inicial.

## Cómo implementa esto ANTLR4

ANTLR4 permite escribir la gramática con recursión izquierda y el motor interno realiza este mismo proceso de reducciones.

```
// Reglas:

//  E  ->  E + T
//          | E - T  
//          | T
//          | ε

expr : expr SUM term            # Addition
    | expr RES term             # Subtraction  
    | term                            # JustTerm
    |                                     # EmptyExpr
     ;
     
//  T  ->  T * F
//         | T / F  
//         | F
  
term : term MUL factor       # Multiplication  
     | term DIV factor       # Division  
     | factor                       # JustFactor  
     ;  


//  F -> (E)
//       | Num
//       | Dec
//       | ID


factor : PA expr PC                 # Parentheses
       | INTEGER                     # Number
       | DECIMAL                   # DecimalNumber
       | ID                               # Identifier
       ;
       
       
```


### Características importantes

1. **Recursión por la izquierda**: ANTLR4 la maneja automáticamente, por eso se pueden escribir reglas como `E -> E + T` directamente.
2. **Etiquetas (#)**: cada alternativa tiene un nombre que facilita implementar visitors y recorrer el árbol.
3. **Precedencia de operadores**: no se programa explícitamente; queda definida por la estructura de las reglas.

## Ejemplo paso a paso

Para la expresión:

```
5 + 2 * 3
```

Con la gramática:

```
E -> E + T | T  
T -> T * F | F  
F -> (E) | id
```

El proceso es:

1. Desplazar `5` -> Reducir a F -> Reducir a T -> Reducir a E
2. Desplazar `+` -> Desplazar `2` -> Reducir a F -> Reducir a T
3. Desplazar `*` -> Desplazar `3` -> Reducir a F
4. Reducir `2 * 3` a T (por T -> T * F)
5. Reducir `5 + T` a E (por E -> E + T)
6. Aceptar

Este proceso genera un árbol sintáctico que respeta automáticamente la precedencia de operadores.


### Resolución

| Pila    | Entrada   | Acción                 |
| ------- | --------- | ---------------------- |
| $       | + 2 * 3 $ | Desplazar 5            |
| $ 5     | + 2 * 3 $ | Reducir 5 -> F         |
| $ F     | + 2 * 3 $ | Reducir F -> T         |
| $ T     | + 2 * 3 $ | Reducir T -> E         |
| $ E     | + 2 * 3 $ | Desplazar +            |
| $ E +   | 2 * 3 $   | Desplazar 2            |
| $ E + 2 | * 3 $     | Reducir 2 -> F         |
| $ E + F | * 3 $     | Reducir F -> T         |
| $ E + T | * 3 $     | Reducir E + T -> E     |
| $ E     | *  3 $    | Reducir E -> T         |
| $ T     | * 3 $     | Desplazar *            |
| $ T *   | 3 $       | Desplazar 3            |
| $ T * 3 | $         | Reducir 3 -> F         |
| $ T * F | $         | Reducir T * F -> T     |
| $ T     | $         | Reducir T -> E         |
| $ E     | $         | Reducir E -> Vacio (ε) |
| $       | $         |                        |

Árbol: abajo para arriba
Desplazar: hoja
Reducir: nodo

### Pasamos las reglas a ANTLR4

```
// E → E + T  
//      | T  

expr : expr SUM term    
     | term  
     ;  
  
// T → T * F  
//     | F  

term : term MUL factor  
     | factor   
     ;  
  
// F → (E)
     | id  
     
factor : PA expr PC  
      | ID    
       ;   
```



## Ejemplo 2

### Reglas

```
E -> E + T | T  
T -> T * F | F  
F -> (E) | id
```

### Entrada

```
id + id * id
```

### Notas

El parser desplazará y reducirá hasta descubrir que primero debe formar `id * id` como `T`, y luego sumarlo con el primer `id` para formar `E`.

### Resolución 
| Pila         | Entrada   | Acción          |
| ------------ | --------- | --------------- |
| $            | id+id*id$ | Desplazar id    |
| $ id         | +id*id$   | Reducir id -> F |
| $ F          | +id*id$   | Reducir F -> T   |
| $ T          | +id*id$   | Reducir T -> E   |
| $ E          | +id*id$   | Desplazar +     |
| $ E +        | id*id$    | Desplazar id    |
| $ E + id     | *id$      | Reducir id -> F  |
| $ E + F      | *id$      | Reducir F -> T   |
| $ E + T      | *id$      | Desplazar *     |
| $ E + T *    | id$       | Desplazar id    |
| $ E + T * id | $         | Reducir id -> F  |
| $ E + T * F  | $         | Reducir T*F -> T |
| $ E + T      | $         | Reducir E+T -> E |
| $ E          | $         | Reducir E -> ε   |
| $            | $         | Aceptar         |

### Pasamos las reglas a ANTLR4

```
// E → E + T  
//      | T  

expr 
    : expr SUM term   # Addition
    | term            # JustTerm
    ;  
  
// T → T * F  
//     | F  

term 
    : term MUL factor # Multiplication
    | factor          # JustFactor
    ;  
  
// F → (E)
//     | id  
     
factor 
    : PA expr PC      # Parentheses
    | ID              # Identifier
    ;
       
```



## Ejercicio 1

### Reglas

```
E -> E + T  | T  | E - T
T -> T * F | F  
F -> (E) | id
```

### Entrada

```
(A+B) * C - D
```

### Notas

- id puede ser A, B, C o D
- Los paréntesis también cuentan
- Primero se resuelve lo que está entre paréntesis
- Luego la multiplicación
- Finalmente la resta

### Resolución 

| Pila      | Entrada       | Acción              |
| --------- | ------------- | ------------------- |
| $         | (A+B) * C-D $ | Desplazar (         |
| $ (       | A+B) * C-D $  | Desplazar A         |
| $ ( A     | +B) * C-D $   | Reducir A -> F      |
| $ ( F     | +B) * C-D $   | Reducir  F -> T     |
| $ ( T     | +B) * C-D $   | Reducir T -> E      |
| $ ( E<br> | +B) * C-D $   | Desplazar +         |
| $ ( E +   | B) * C-D $    | Desplazar B         |
| $ ( E + B | ) * C-D $     | Reducir B-> F       |
| $ ( E + F | ) * C-D $     | Reducir F -> T      |
| $ ( E + T | ) * C-D $     | Reducir E + T -> E  |
| $ ( E     | * C-D $       | Desplazar )         |
| $ ( E )   | * C-D $       | Reducir  ( E ) -> F |
| $ F       | * C-D $       | Reducir  F -> T     |
| $ T       | * C-D $       | Desplazar *         |
| $ T *     | C-D $         | Desplazar C         |
| $ T * C   | - D$          | Reducir C -> F      |
| $ T * F   | - D$          | Reducir T * F -> T  |
| $  T      | - D$          | Reducir T -> E      |
| $ E       | - D$          | Desplazar -         |
| $ E -     | D$            | Desplazar D         |
| $ E - D   | $             | Reducir D -> F      |
| $ E - F   | $             | Reducir F -> T      |
| $ E - T   | $             | Reducir E - T -> E  |
| $ E       | $             | Reducir E -> ε      |
| $         | $             | Aceptar             |

### Pasamos las reglas a ANTLR4

```
// E  -> E + T  
//      | E - T  
//      | T  
  
expr  
    : expr SUM term   # Addition  
    | expr RES term   # Subtraction  
    | term            # JustTerm  
    ;  
  
// T  -> T * F  
//     | F  
  
term  
    : term MUL factor # Multiplication  
    | factor          # JustFactor  
    ;  
  
// F  -> (E)  
//     | id  
  
factor  
    : PA expr PC      # Parentheses  
    | INTEGER         # Number  
    | DECIMAL         # DecimalNumber  
    | ID              # Identifier  
    ;
       
```


## Resumen 

En el ascendente:

> Tengo lo que veo en la entrada y descubro qué regla lo generó.

Si una expresión puede resolverse en ascendente, siempre puede adaptarse para descendente transformando la gramática.


