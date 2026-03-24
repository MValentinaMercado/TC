# Compilación vs Interpretación  
  
## Compilador
Es un programa que traduce código fuente escrito en un lenguaje de alto nivel a un lenguaje destino, generalmente código de maquina o código intermedio. La traducción se realiza **antes de la ejecución**, generando un archivo ejecutable u otro tipo de código que luego puede ejecutarse directamente por el sistema.

## Intérprete
Es un programa que ejecuta el código fuente directamente, **sin generar un archivo ejecutable previo**.  Analiza y ejecuta el código **instrucción por instrucción** en tiempo de ejecución.

## Lenguajes Híbridos  
Combinan compilación e interpretación. Primero el código se compila a un **código intermedio** (bytecode), y luego se ejecuta mediante una **máquina virtual**.

# Fases del Proceso de Compilación  
  
## 1. Análisis Léxico  
Lee el código fuente y lo divide en unidades llamadas **tokens**(unidades básicas con significado), como palabras clave, identificadores, números, operadores y símbolos.

## 2. Análisis Sintáctico
Toma los tokens generados en la fase anterior y verifica que estén organizados según las **reglas gramaticales del lenguaje**. Construye una estructura jerárquica, normalmente un **árbol sintáctico**.

## 3. Análisis Semántico
 Comprueba que las estructuras tengan **sentido dentro del lenguaje**. Verifica tipos de datos, declaraciones de variables, uso correcto de funciones, compatibilidad de operaciones y reglas de alcance.

## 4. Generación de Código Intermedio (IR)
 Convierte el programa en una representación intermedia independiente de la máquina, que facilita posteriores optimizaciones y la generación de código para distintas arquitecturas.
 
## 5. Optimización de Código  
Mejora el código intermedio para que el programa resultante sea **más eficiente**, reduciendo tiempo de ejecución o uso de memoria sin cambiar el comportamiento del programa.
