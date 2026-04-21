

# Análisis Sintáctico Descendiente para Expresiones Aritméticas

El análisis descendente (top-down) construye el árbol sintáctico **desde el símbolo inicial** intentando generar la cadena de entrada.

A diferencia del ascendente, aquí el parser:

- Parte de la regla inicial
- Va expandiendo (derivando) no terminales
- Hace **match** cuando el tope de la pila coincide con el token de entrada

Este método **no puede trabajar con recursión izquierda**, por lo que la gramática debe transformarse antes de usarla.

Trabaja con una **pila** y dos acciones:

1. **Derivar**: reemplazar el no terminal superior por el lado derecho de una producción. Es a la inversa.
   Ejemplo:
    E -> E + T
    Derivo E -> T + E

3. **Match**: cuando el terminal en la pila coincide con el token de entrada.

## Cómo implementa esto ANTLR4

ANTLR4 permite escribir la gramática con recursión izquierda y el motor interno realiza este mismo proceso de reducciones.

```
// Reglas:

//  E  ->  E + T
//          | E - T  
//          | T
//          | ε

expr 
    : expr SUM term   # Addition
    | expr RES term   # Subtraction  
    | term            # JustTerm
    |                 # EmptyExpr
    ;
     
//  T  ->  T * F
//         | T / F  
//         | F
  
term 
    : term MUL factor # Multiplication  
    | term DIV factor # Division  
    | factor          # JustFactor  
    ;  

//  F -> (E)
//       | Num
//       | Dec
//       | ID

factor 
    : PA expr PC      # Parentheses
    | INTEGER         # Number
    | DECIMAL         # DecimalNumber
    | ID              # Identifier
    ;
```


### Características importantes

1. **Recursión por la izquierda**: ANTLR4 la maneja automáticamente, por eso se pueden escribir reglas como `E -> E + T` directamente.
2. **Etiquetas (#)**: cada alternativa tiene un nombre que facilita implementar visitors y recorrer el árbol.
3. **Precedencia de operadores**: no se programa explícitamente; queda definida por la estructura de las reglas.


## Ejemplo paso a paso


### Entrada: 

```
5 + 3 * 2
```


Partimos de la gramática original (con recursión izquierda):

```
E -> E + T | T 
T -> T * F | F
F -> (E) | num
```

### Eliminación de recursión izquierda

Para una regla de la forma:

```
A -> A α | β
```

Se transforma en:

```
A  -> β A'
A' -> α A' | ε
```

#### Aplicación paso a paso sobre **E**

Regla original:

```
E -> E + T | T
```


**1) Identificación**

Se busca la parte recursiva y la no recursiva:

- A = E
- α = + T
- β = T

**2) Aplicación de la transformación**

Usando la forma general:

```
E -> α  E'  
E' -> β E' | ε
```

**3) Reemplazo**

```
E -> T E'  
E' -> + T E' | ε
```


#### Aplicación paso a paso sobre **T**

Regla original:

```
T -> T * F | F
```


**1) Identificación**

- A = T
- α = * F
- β = F

**2) Transformación**

```
T  -> β T'  
T' -> α T' | ε
```

**3) Reemplazo**

```
T  -> F T'
T' -> * F T' | ε
```


#### Regla **F** (no tiene recursión izquierda)

```
F -> (E) | num
```


### Reglas Finales

```
E  -> T E'
E' -> + T E' | ε

T  -> F T'
T' -> * F T' | ε

F  -> (E) | num

```

### Resolución

| Pila        | Entrada     | Acción              |
| ----------- | ----------- | ------------------ |
| $ E         | 5 + 3 * 2 $ | Derivo E -> E' T    |
| $ E' T      | 5 + 3 * 2 $ | Derivo T -> T' F    |
| $ E' T' F   | 5 + 3 * 2 $ | Derivo F -> num     |
| $ E' T' num | 5 + 3 * 2 $ | Match 5 = num       |
| $ E' T'     | + 3 * 2 $   | Derivo T' -> ε      |
| $ E'        | + 3 * 2 $   | Derivo E' -> E' T + |
| $ E' T +    | + 3 * 2 $   | Match +             |
| $ E' T      | 3 * 2 $     | Derivo T -> T' F    |
| $ E' T' F   | 3 * 2 $     | Derivo F -> num     |
| $ E' T' num | 3 * 2 $     | Match 3             |
| $ E' T'     | * 2 $       | Derivo T' -> T' F * |
| $ E' T' F * | * 2 $       | Match *             |
| $ E' T' F   | 2 $         | Derivo F -> num     |
| $ E' T' num | 2 $         | Match 2             |
| $ E' T'     | $           | Derivo T' -> ε      |
| $ E'        | $           | Derivo E' -> ε      |
| $           | $           | Acepta              |

Árbol: arriba para abajo
Derivar: nodo
Match: hoja

Se escribe como en la regla, de izquierda a derecha, no hay que revertirla.

Por ejemplo:

Si primero derive  E -> E' T y la regla es E  -> T E' , entonces:

Raíz: E
Nodo: T  Nodo: E'

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


## Ejemplo 2:

### Reglas:

```
E -> T G
G -> + T G | ε
T -> F H
H -> * F H | ε
F -> (E) | id
```

### Entrada 
```
id + id * id
```

### Resolución

| Pila   | Entrada        | Acción          |
| ------ | -------------- | --------------- |
| $ E    | id + id * id $ | Derivo E -> GT  |
| $ GT   | id + id * id $ | Derivar T -> HF |
| $ GHF  | id + id * id $ | Derivo F -> id  |
| $ GHid | id + id * id $ | Match id        |
| $ GH   | + id * id $    | Derivo H -> ε   |
| $ G    | + id * id $    | Derivo G -> GT+ |
| $ GT+  | + id * id $    | Match +         |
| $ GT   | id * id $      | Derivo GT -> E  |
| $ E    | id * id $      | Derivo E -> GT  |
| $ GT   | id * id $      | Derivar T -> HF |
| $ GHF  | id * id $      | Derivo F -> id  |
| $ GHid | id * id $      | Match id        |
| $ GH   | * id $         | Derivo H -> HF* |
| $ GHF* | * id $         | Match *         |
| $ GHF  | id $           | Derivo F -> id  |
| $ GHid | id $           | Match id        |
| $GH    | $              | Derivo H -> ε   |
| $G     | $              | Derivo G -> ε   |
| $      | $              |                 |

### Pasamos las reglas a ANTLR4

```
// E -> T G

expr
    : term exprTail   # ExprWithTail
    ;

// G -> + T G | ε

exprTail
    : SUM term exprTail # AddTail
    |                   # EmptyExprTail
    ;

// T -> F H 

term
    : factor termTail   # TermWithTail
    ;
    
// H -> * F H | ε

termTail
    : MUL factor termTail # MulTail
    |                     # EmptyTermTail
    ;
    
// F -> (E) | ID

factor
    : PA expr PC      # Parentheses
    | ID              # Identifier
    ;
```


## Ejercicio 1

Partimos de la gramática original (con recursión izquierda):

```
E -> E + T | E - T | T
T -> T * F | F
F -> (E) | id
```

### Eliminación de recursión izquierda

Para una regla de la forma:

```
A -> A α | β
```

Se transforma en:

```
A  -> β A'
A' -> α A' | ε
```

#### Aplicación paso a paso sobre **E**

Regla original:

```
E -> E + T | E - T | T
```


**1) Identificación**

Se busca la parte recursiva y la no recursiva:

- A = E
- α₁ = + T
- α₂ = - T
- β = T

**2) Aplicación de la transformación**

Usando la forma general:

```
E  -> β E'
E' -> α E' | ε
```

**3) Reemplazo**

```
E  -> T E'  
E' -> + T E' | - T E' | ε
```


#### Aplicación paso a paso sobre **T**

Regla original:

```
T -> T * F | F
```


**1) Identificación**

- A = T
- α = * F
- β = F

**2) Transformación**

```
T  -> β T'  
T' -> α T' | ε
```

**3) Reemplazo**

```
T  -> F T'
T' -> * F T' | ε
```


#### Regla **F** (no tiene recursión izquierda)

```
F -> (E) | id
```


### Reglas Finales

```
E  -> T E'
E' -> + T E' | - T E' | ε

T  -> F T'
T' -> * F T' | ε

F  -> (E) | id

```

### Entrada: 

```
(A+B) * C-D
```

### Notas

- `id` puede ser A, B, C o D
- Siempre se deriva el no terminal del tope de la pila
- Se hace match cuando coincide con la entrada

### Resolución
| Pila              | Entrada           | Acción               |
| ----------------- | ----------------- | -------------------- |
| $ E               | (A+B) * C-D $     | Derivar E -> E' T     |
| $ E' T            | (A+B) * C-D $     | Derivar T -> T' F     |
| $ E' T' F         | (A+B) * C-D $     | Derivo F -> )E(       |
| $ E' T' ) E (     | (A+B) * C-D $     | Match (              |
| $ E' T' ) E       | A + B ) * C - D $ | Derivo E -> E' T      |
| $ E' T' ) E' T    | A + B ) * C - D $ | Derivo T -> T' F      |
| $ E' T' ) E' T' F | A + B ) * C - D $ | Derivo F -> A         |
| $ E' T' ) E' T' A | A + B ) * C - D $ | Match A              |
| $ E' T' ) E' T'   | + B ) * C - D $   | Derivo T' -> ε        |
| $ E' T' ) E'      | + B ) * C - D $   | Derivo E' -> E' T +   |
| $ E' T' ) E' T +  | + B ) * C - D $   | Match +              |
| $ E' T' ) E' T    | B ) * C - D $     | Derivo T ->  T' F     |
| $ E' T' ) E' T' F | B ) * C - D $     | Derivo F -> B         |
| $ E' T' ) E' T' B | B ) * C - D $     | Match B              |
| $ E' T' ) E' T'   | ) * C - D $       | Derivo T' -> ε        |
| $ E' T' ) E'      | ) * C - D $       | Derivo E' -> ε        |
| $ E' T' )         | ) * C - D $       | Match )              |
| $ E' T'           | * C - D $         | Derivo T' ->  T'  F * |
| $ E' T' F *       | * C - D $         | Match *              |
| $ E' T' F         | C - D $           | Derivo F -> C         |
| $ E' T' C         | C - D $           | Match C              |
| $ E' T'           | - D $             | Derivo T' -> ε        |
| $ E'              | - D $             | Derivo E' -> E' T -   |
| $ E' T -          | - D $             | Match -              |
| $ E' T            | D $               | Derivo T -> T' F      |
| $ E' T' F         | D $               | Derivo F -> D         |
| $ E' T' D         | D $               | Match D              |
| $ E' T'           | $                 | Derivo T' -> ε        |
| $ E'              | $                 | Derivo E' -> ε        |
| $                 | $                 | Acepta               |

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
    | ID              # Identifier
    ;
       
```


## Resumen 

En el descendente:

> Tengo la regla y trato de generar lo que veo en la entrada.

Si una expresión puede resolverse en ascendente, siempre puede adaptarse para descendente transformando la gramática.


