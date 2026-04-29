## Páginas 

[regex101](https://regex101.com/)
[RegExr](https://regexr.com/)


## Ejercicios

[Ejercicios](https://github.com/FranciscoAmeri/Ejercicio_Lexicos/tree/ejercicio2)

### Fechas

#### (dd/mm/aa)

```
((0[1-9]|1[0-9]|2[0-8])/02/26)|((0[1-9]|[1-2][0-9]|3[0-1])/0[3-4]/26)|(0[1-5]/05/26)
```

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


Ejemplos válidos:

- 10/02/26 
- 05/05/26

####  AAAA-MM-DD (ISO)

```
(2026-02-(0[1-9]|1[0-9]|2[0-8]))|(2026-0[3-4]-(0[1-9]|[1-2][0-9]|3[0-1]))|(2026-05-(0[1-5]))
```

Ejemplos válidos:

- 2026-02-10
- 2026-05-05

#### Fechas con meses en texto

```
((0[1-9]|1[0-9]|2[0-8]) de febrero de 2026)|((0[1-9]|[1-2][0-9]|3[0-1]) de (marzo|abril) de 2026)|(0[1-5] de mayo de 2026)
```

Ejemplos válidos:

- 10 de febrero de 2026
- 25 de marzo de 2026
- 05 de mayo de 2026

### Correos Electrónicos 

```
/[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}/
```

Ejemplos válidos:

- [persona@mail.com](mailto:persona@mail.com)
- [usuario.123@empresa.org](mailto:usuario.123@empresa.org)

Inválidos:

- correo@com
- @sinusuario.com


### CUIL

```
/^((20|23|24|27|30|33|34)-?\d{8}-?\d)$/gm
```

Ejemplos válidos:

- 12345678
- 2345678

### Números de Teléfonos 

Formatos con separadores:

```
/\d{3}[\s.-]\d{3}[\s.-]\d{2}[\s.-]\d{2}/gm
```

Solo región 448 y 449:

```
/(448|449)[\s-]\d{3}[\s-]\d{2}[\s-]\d{2}/gm
```

Forma equivalente:

```
/44[89][\s-]\d{3}[\s-]\d{2}[\s-]\d{2}/gm
```

Usando repetición de grupo:

```
/(448|449)[\s-]\d{3}([\s-]\d{2}){2}/gm
```

Teléfono fijo:

```
/\d{3}-?\s?\d{4}/gm
```

Telefono

```
/\d{3}[ -]?\d{3}[ -]?\d{4}/gm
```


Ejemplos válidos:

- 3514587721
- 351 369 3463
- 351 549-2862

Formato internacional con +

```
/\+54[\s-]\d{3}([\s-]\d{3})([\s-]\d{4})/gm
```

Ejemplos válidos:

- +54 351 458 7721
- +54-351-369-3463

Formato nacional con 0 y 15

```
/0\d{3}[\s-]15[\s-]\d{3}[\s-]\d{4}/gm
```

Ejemplos válidos:

- 0351 15 4587721
- 011-15-369-3463

Formato internacional móvil correcto (con 9):

```
/\+54[\s-]?9[\s-]?\d{3}[\s-]?\d{7}/gm
```

Ejemplos válidos:

- +54 9 351 4587721


### Códigos postales

```
/\d{4}([A-Z]{3})?/gm
```

Ejemplos válidos:

- 1406
- 1406ABC

### Comentario de código

```
/^(\/\/.*|\/\*[\s\S]*?\*\/)$/gm
```

Ejemplos válidos:

- //Es un comentario
- /* */

### Contraseña segura

```
/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]).{8,}$/gm
```

Requisitos:

- Mínimo 8 caracteres
- Al menos una mayúscula
- Al menos una minúscula
- Al menos un número
- Al menos un símbolo

Ejemplos válidos:

- Aa1#abcd
- Segura9!
- XyZ123$%

inválidos:

- abcdefghi
- ABCDEFGH1
- Abcdefgh

### Nombres y Apellidos

```
/^\w+\s?\w+?\s?$/gim
/^[A-Za-zÁÉÍÓÚÜÑáéíóúüñ]+(?:\s[A-Za-zÁÉÍÓÚÜÑáéíóúüñ]+)+$/gm
```

Permite:

- Juan Pérez
- María José Gómez
- Ángel Núñez
- Ana Sofía Díaz

No permite:

- Juan123
- Pedro_Alvarez
- Luis! Pérez

### URL

```
/^(https?:\/\/)?(www\.)?[a-zA-Z0-9.-]+\.(com|com\.mx|net)$/gm
```

Ejemplos válidos:

- [https://www.google.com/](https://www.google.com/)
- [https://github.com/FranciscoAmeri](https://github.com/FranciscoAmeri)

### Dominios

```
w{3}\.[a-zA-Z]+\.[A-Za-z]{3}(\.[a-z]{2})?
```
  
Ejemplos válidos:

- [www.google.com](http://www.google.com)
- [www.ubp.edu.ar](http://www.ubp.edu.ar)

### IP

```
^((25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9]?[0-9])\.){3}(25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9]?[0-9])$
```

Como una IP tiene 4 números separados por puntos y cada uno debe estar entre 0 y 255, hay que dividir el rango en partes para cubrir todos los casos posibles.   
  
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

Ejemplos válidos:

- 192.168.16.20
- 0.0.0.0


## Matrículas de autos

```
/^([A-Z]{3}\s\d{3}|[A-Z]{2}\s\d{3}\s[A-Z]{2})$/gm
```

Ejemplos válidos:

- AAA 123
- AA 44A BB

## Números de tarjeta de crédito

```
/^\d{4}(\s\d{4}){3}$/gm
```
Ejemplos válidos:

- 1111 2222 3333 4444