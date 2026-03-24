## Ejercicios
[regex101](https://regex101.com/)

### Dominios

```
w{3}.[a-zA-Z]+.[A-z]{3}(.[a-z]{2}){,1}
```
 
- `w{3}` dominio fijo  
- `.` cualquier carácter 
- `[a-zA-Z]+` nombre del sitio  
- `.` separador  
- `[A-Z]{3}` dominio principal
- `.[a-z]{2}){,1}` dominio país opcional (ar, es)  
  
Valido:  
- www.google.com  
- www.ubp.edu.ar


### IP

```
^((25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9]?[0-9])\.){3}(25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9]?[0-9])$
```

- `^` inicio de línea  
- `(...)` grupo para un bloque de la IP  
- `\.` separador (punto)  
- `{3}` repite los primeros 3 bloques  
- `$` fin de línea

Como una IP tiene 4 números separados por puntos y cada uno debe estar entre 0 y 255, hay que dividir el rango en partes para cubrir todos los casos posibles.  
Las expresiones regulares no entienden números como tal, sino **patrones de caracteres**, por lo que el rango se divide según la cantidad de dígitos:  
  
0–9 → 1 dígito  
10–99 → 2 dígitos  
100–255 → 3 dígitos

Entonces:

Del 0 al 99: 
```
[1-9]?[0-9]
```

Del 100 al 199 
```
1[0-9]{2}
```

Del 200 al 249 
```
2[0-4][0-9]
```

Del 250 al 255 
```
25[0-5]
```

Valido:  
- 192.168.16.20
- 0.0.0.0

### Fecha
```
((0[1-9]|1[0-9]|2[0-8])/02/26)|((0[1-9]|[1-2][0-9]|3[0-1])/0[3-4]/26)|(0[1-5]/05/26)
```

- `( ... )` separa los distintos casos de meses  
- `\ /` separador de fecha  
- `|` alternativa (OR)

Como cada mes tiene distinta cantidad de días, se divide el problema por meses.  
  
Febrero (01 a 28):
```
(0[1-9]|1[0-9]|2[0-8])
```
  
Marzo y Abril (01 a 31):  
```
(0[1-9]|[1-2][0-9]|3[0-1])
```
  
Mayo (01 a 05):  
```
(0[1-5])
```

Válido:  
- 10/02/26 
- 05/05/26

### Mail
```
[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}
```

- `[a-zA-Z0-9._%+-]+` usuario
- `@` Separador(arroba)
- `[a-zA-Z0-9.-]+` dominio  
- `\.` separador (punto) 
- `[a-zA-Z]{2,}` extensión 
  
Valido:  
- momercado@ubp.edu.ar

### Teléfono
```
\d{10}
```

- `\d` dígito  
- `{10}` exactamente 10 dígitos

Valido:
- 3514587721

### CUIL
```
\d{11}
```

- `\d` dígito  
- `{11}` exactamente 11 dígitos

Valido:
- 27450918992
- 27448975601

### Comentario de código
```
<!--[\s\S]+?-->
```

- `<!--` inicio de comentario  
- `[\s\S]*?` cualquier contenido 
- `-->` fin de comentario

Valido:
- <!--comentario-->