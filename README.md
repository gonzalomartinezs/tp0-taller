# Trabajo Práctico 0
### Taller de Programación I (75.42)

#### Martinez Sastre, Gonzalo Gabriel - 102321

Link al repositorio de Github: https://github.com/gonzalomartinezs/tp0-taller

## Introducción

EL objetivo del presente trabajo consiste en comprender el funcionamiento del sistema de entregas SERCOM para lograr hacer un correcto uso del mismo, así como también se busca afianzar conceptos básicos de la programación tales como el proceso de compilación y el de depuración.

Para lograr esto, se parte de la entrega de un código que no logra finalizar la generación de un ejecutable en SERCOM, el cual se irá corrigiendo progresivamente a lo largo de distintas etapas o pasos hasta poder obtener un programa capaz de concretar exitosamente todas las pruebas del sistema.

## Desarrollo paso por paso
### Paso 0

**a.** A continuación se muestran los outputs obtenidos al ejecutar el programa "Hola mundo" con y sin Valgrind:

![alt text][valgrind_paso0]

**b.** Valgrind es un framework de código abierto de herramientas dedicadas a hallar problemas de memoria, facilitar la depuración (o “debugging”) y favorecer la mejora de la performance de un programa. Entre las opciones más utilizadas del mismo se encuentran:

- `--leak-check`: puede tomar los valores `no`, `yes`, `summary` y `full`. Con las opciones `yes` y `full` muestra en detalle información de cada pérdida de memoria de manera individual. Si la opción escogida es `summary` (por defecto), muestra la cantidad de pérdidas de memoria. La utilización de esta opción aumenta el tiempo de ejecución del programa.
- `--show_reachable`: cuando se setea en `yes` se muestran todos los tipos posibles de pérdidas de memoria: aún alcanzable (*still reachable*), definitivamente perdido (*definitely lost*), indirectamente perdido (*indirectly lost*) y posiblemente perdido (*possibly lost*).
- `--track-origins`: si se activa, informa el origen de una variable no inicializada cada vez que surja un error como consecuencia de la misma.

**c.** `sizeof()` es un operador que devuelve un entero (`size_t`, en realidad) el cual representa el tamaño en bytes que ocupa el tipo de dato que recibe como parámetro. De esta manera, por un lado, `sizeof(char)` retornará 1 y, por el otro, `sizeof(int)` devolverá 4 siempre y cuando no se trate de una arquitectura de 16 bits (en dicho caso el valor obtenido es 2).

**d.** El `sizeof()` de un `struct` no siempre es igual a la suma del `sizeof()` de cada uno de sus elementos. Esto se debe a la presencia del `padding`, un espacio que se agrega luego de cada dato para evitar problemas de alineación. Un claro ejemplo de esto es el siguiente registro:

```c
typedef struct {

    char inicial;
    int longitud;

}nombre_t;
```
Si se realiza la operación `sizeof(nombre_t)` en una arquitectura de 64 bits, el valor obtenido **no** será `sizeof(char) + sizeof(int) = 5`, sino que **será 8** debido a que tras el `char` se colocan 3 bytes de padding para que coincida con los 4 bytes correspondientes al `sizeof(int)`.

**e.** STDIN (*standard input*), STDOUT (*standard output*) y STDERR (*standard error*) son tres streams utilizados en los sistemas operativos Unix. El uso de cada uno de ellos se describe a continuación:

- **STDIN** : almacena el texto de entrada.
- **STDOUT** : almacena el texto de salida producto de la ejecución de un comando.
- **STDERR** : almacena los mensajes de error surgidos durante la ejecución del comando.

Además, existen los caracteres de redireccionamiento (**<**, **>**)utilizados, por ejemplo, cuando se desea almacenar el output de un comando en un archivo o cuando se quiere enviar como input el contenido de un archivo en lugar de tipearlo. De esta manera, se utiliza **<** para redireccionar a STDIN, **>** para STDOUT y **2>** para STDERROR. A continuación, se muestran algunos ejemplo de posibles usos de estos en terminal:

```
echo "Texto de prueba" > prueba.txt						# almacena el output del comando echo en prueba.txt
cat < prueba.txt															# cat toma como input prueba.txt
comandoInexistente 2> error.text							# almaceno el mensaje de error obtenido al utilizar un comando inexistente en error.txt
```
Por último, también es posible lograr que la salida de un comando sea la entrada de otro mediante la utilización del caracter **|**. Así, la salida del comando ubicado a la derecha de dicho caracter, será la entrada de aquel ubicado a la izquierda.

### Paso 1

**a.** Los errores de estilo obtenidos con esta entrega son los siguientes:
```
/task/student//source_unsafe/paso1/paso1_wordscounter.c:27:  Missing space before ( in while(  [whitespace/parens] [5]
/task/student//source_unsafe/paso1/paso1_wordscounter.c:41:  Mismatching spaces inside () in if  [whitespace/parens] [5]
/task/student//source_unsafe/paso1/paso1_wordscounter.c:41:  Should have zero or one spaces inside ( and ) in if  [whitespace/parens] [5]
/task/student//source_unsafe/paso1/paso1_wordscounter.c:47:  An else should appear on the same line as the preceding }  [whitespace/newline] [4]
/task/student//source_unsafe/paso1/paso1_wordscounter.c:47:  If an else has a brace on one side, it should have it on both  [readability/braces] [5]
/task/student//source_unsafe/paso1/paso1_wordscounter.c:48:  Missing space before ( in if(  [whitespace/parens] [5]
/task/student//source_unsafe/paso1/paso1_wordscounter.c:53:  Extra space before last semicolon. If this should be an empty statement, use {} instead.  [whitespace/semicolon] [5]
/task/student//source_unsafe/paso1/paso1_wordscounter.h:5:  Lines should be <= 80 characters long  [whitespace/line_length] [2]
/task/student//source_unsafe/paso1/paso1_main.c:12:  Almost always, snprintf is better than strcpy  [runtime/printf] [4]
/task/student//source_unsafe/paso1/paso1_main.c:15:  An else should appear on the same line as the preceding }  [whitespace/newline] [4]
/task/student//source_unsafe/paso1/paso1_main.c:15:  If an else has a brace on one side, it should have it on both  [readability/braces] [5]
Done processing /task/student//source_unsafe/paso1/paso1_wordscounter.c
Done processing /task/student//source_unsafe/paso1/paso1_wordscounter.h
Done processing /task/student//source_unsafe/paso1/paso1_main.c
Total errors found: 11
```
Donde el significado de cada uno de ellos se describe a continuación:

- **Missing space before ( in while(** : falta un espacio que separe al `while` de la condición de corte del mismo.

- **Mismatching spaces inside () in if** : la cantidad de espacios a cada lado de la expresión del `if` es dispareja.

- **Should have zero or one spaces inside ( and ) in if** : dentro de la expresión del *if* no debe haber más de un espacio (' ') contiguo.

- **An else should appear on the same line as the preceding }** : El `else` debe estar en la misma línea que la llave que lo antecede.

- **If an else has a brace on one side, it should have it on both** : Si el `else`
 tiene una llave de un lado también debería tener una al otro lado. La forma correcta sería `} else if (state == STATE_IN_WORD) {`

- **Missing space before ( in if(** : Análogo al primer error descripto, pero en este caso aplicado a un `if`.

- **Extra space before last semicolon. If this should be an empty statement, use {} instead** : hay un espacio innecesario antes del último punto y coma. Corregido quedaría `return next_state;`

- **Lines should be <= 80 characters long** : una línea no debería superar los 80 caracteres (por más que sea un comentario).

- **Almost always, snprintf is better than strcpy** : en lugar de `strcpy`, en la mayoría de los casos, es mejor utilizar `snprintf`. Esto se debe a que la primera no establece un límite al tamaño del buffer que usa, lo cual puede resultar peligroso, mientras que la segunda sí lo hace.


**b.** Los errores de la generación del ejecutable obtenidos son:

```
Descomprimiendo el codigo 'source_unsafe.zip'...
Archive:  source_unsafe.zip
  inflating: source_unsafe/paso1_main.c
  inflating: source_unsafe/paso1_wordscounter.c
  inflating: source_unsafe/paso1_wordscounter.h
Compilando el codigo...
  CC  paso1_main.o
paso1_main.c: In function ‘main’:
paso1_main.c:22:9: error: unknown type name ‘wordscounter_t’
   22 |         wordscounter_t counter;
      |         ^~~~~~~~~~~~~~
paso1_main.c:23:9: error: implicit declaration of function ‘wordscounter_create’ [-Wimplicit-function-declaration]
   23 |         wordscounter_create(&counter);
      |         ^~~~~~~~~~~~~~~~~~~
paso1_main.c:24:9: error: implicit declaration of function ‘wordscounter_process’ [-Wimplicit-function-declaration]
   24 |         wordscounter_process(&counter, input);
      |         ^~~~~~~~~~~~~~~~~~~~
paso1_main.c:25:24: error: implicit declaration of function ‘wordscounter_get_words’ [-Wimplicit-function-declaration]
   25 |         size_t words = wordscounter_get_words(&counter);
      |                        ^~~~~~~~~~~~~~~~~~~~~~
paso1_main.c:27:9: error: implicit declaration of function ‘wordscounter_destroy’ [-Wimplicit-function-declaration]
   27 |         wordscounter_destroy(&counter);
      |         ^~~~~~~~~~~~~~~~~~~~
make: *** [<builtin>: paso1_main.o] Error 1

real    0m0.164s
user    0m0.032s
sys     0m0.012s
[Error] Fallo la compilacion del codigo en 'source_unsafe.zip'. Codigo de error 2
```
Donde el significado de cada uno de ellos se describe a continuación:

- **unknown type name ‘wordscounter_t’** : se trata de un error del compilador el cual indica que no se reconoce el tipo `wordscounter_t` puesto que no se encuentra definido ni declarado en `paso1_main.c` y la biblioteca en la que sí se halla definido no fue incluida.

- **implicit declaration of function ‘wordscounter_...’** : error similar al caso anterior, el compilador se encuentra con estas funciones que no se hallan definidas ni declaradas en `paso1_main.c`.

**c.** El sistema no reporta ningún WARNING puesto que se utiliza el flag `-Werror` para la compilación, el mismo produce que todos los WARNINGS emitidos por el compilador sean tomados como errores. De no estar este flag, los errores mencionados en el item anterior hubiesen sido reportados como WARNINGS.

### Paso 2

**a.** Las diferencias del código en comparación con el paso anterior yacen en que se corrigieron todos los errores de estilo y, además, en este caso sí se incluye la biblioteca en la que se encuentran definidos `wordscounter_t` y algunas las funciones que aparecían en los errores de compilación del paso 1.

**b.** En este caso, el output obtenido en cuanto a las normas de programación es:

```
Done processing /task/student//source_unsafe/paso2_wordscounter.c
Done processing /task/student//source_unsafe/paso2_main.c
Done processing /task/student//source_unsafe/paso2_wordscounter.h
```

**c.** Por otro lado, los errores que surgieron durante la generación del ejecutable son:

```
Descomprimiendo el codigo 'source_unsafe.zip'...
Archive:  source_unsafe.zip
  inflating: source_unsafe/paso2_main.c
  inflating: source_unsafe/paso2_wordscounter.c
  inflating: source_unsafe/paso2_wordscounter.h
Compilando el codigo...
  CC  paso2_wordscounter.o
In file included from paso2_wordscounter.c:1:
paso2_wordscounter.h:7:5: error: unknown type name ‘size_t’
    7 |     size_t words;
      |     ^~~~~~
paso2_wordscounter.h:20:1: error: unknown type name ‘size_t’
   20 | size_t wordscounter_get_words(wordscounter_t *self);
      | ^~~~~~
paso2_wordscounter.h:1:1: note: ‘size_t’ is defined in header ‘<stddef.h>’; did you forget to ‘#include <stddef.h>’?
  +++ |+#include <stddef.h>
    1 | #ifndef __WORDSCOUNTER_H__
paso2_wordscounter.h:25:49: error: unknown type name ‘FILE’
   25 | void wordscounter_process(wordscounter_t *self, FILE *text_file);
      |                                                 ^~~~
paso2_wordscounter.h:1:1: note: ‘FILE’ is defined in header ‘<stdio.h>’; did you forget to ‘#include <stdio.h>’?
  +++ |+#include <stdio.h>
    1 | #ifndef __WORDSCOUNTER_H__
paso2_wordscounter.c:17:8: error: conflicting types for ‘wordscounter_get_words’
   17 | size_t wordscounter_get_words(wordscounter_t *self) {
      |        ^~~~~~~~~~~~~~~~~~~~~~
In file included from paso2_wordscounter.c:1:
paso2_wordscounter.h:20:8: note: previous declaration of ‘wordscounter_get_words’ was here
   20 | size_t wordscounter_get_words(wordscounter_t *self);
      |        ^~~~~~~~~~~~~~~~~~~~~~
paso2_wordscounter.c: In function ‘wordscounter_next_state’:
paso2_wordscounter.c:30:25: error: implicit declaration of function ‘malloc’ [-Wimplicit-function-declaration]
   30 |     char* delim_words = malloc(7 * sizeof(char));
      |                         ^~~~~~
paso2_wordscounter.c:30:25: error: incompatible implicit declaration of built-in function ‘malloc’ [-Werror]
paso2_wordscounter.c:5:1: note: include ‘<stdlib.h>’ or provide a declaration of ‘malloc’
    4 | #include <stdbool.h>
  +++ |+#include <stdlib.h>
    5 |
cc1: all warnings being treated as errors
make: *** [<builtin>: paso2_wordscounter.o] Error 1

real    0m0.023s
user    0m0.018s
sys     0m0.005s
[Error] Fallo la compilacion del codigo en 'source_unsafe.zip'. Codigo de error 2
```
Donde el significado de cada uno de ellos se describe a continuación:

- **unknown type name ‘size_t’** : en este caso, el compilador no reconoce el tipo `size_t`. Este error surge puesto que `paso2_wordscounter.h` no incluye ninguno de los headers en los que se encuentra la definición de dicho tipo.
- **unknown type name ‘FILE’** : similar al caso anterior, el compilador no reconoce el tipo `FILE`. El mismo se encuentra definido en `stdio.h`, biblioteca no incluída por `paso2_wordscounter.h`.
- **implicit declaration of function ‘malloc’** : este error se produce debido a que el compilador se encuentra con esta función la cual no se halla definida ni declarada en `paso2_wordscounter.c` ni en su respectivo header.
- **incompatible implicit declaration of built-in function ‘malloc’** : este error surge porque al tratarse de una declaración implícita, el compilador establece que su valor de retorno es `int` lo cual difiere del retorno de tipo 'void*' de la función built-in `malloc`.
- **conflicting types for ‘wordscounter_get_words’** : este error indica que la firma de la declaración de la función es distinta a la de la definición de la misma. Esto se produce porque en `paso2_wordscounter.c` se incluye `stdio.h` y el compilador reconoce que el tipo de retorno de la función es `size_t`, sin embargo esto no sucede en `paso2_wordscounter.h` puesto que para el compilador en este entorno el tipo `size_t` es desconocido. Al igual que los errores anteriores, se trata de un error del compilador.

### Paso 3

**a.** A diferencia del paso 2, en este sí se incluyen todas las bibliotecas faltantes en el anterior (`string.h` y `stdio.h` en `paso3_wordscounter.h`, y `stdlib.h` en `paso3_wordscounter.c`)

**b.** En este paso, el único error durante la generación del ejecutable se muestra a continuación:

```
Descomprimiendo el codigo 'source_unsafe.zip'...
Archive:  source_unsafe.zip
  inflating: source_unsafe/paso3_main.c
  inflating: source_unsafe/paso3_wordscounter.c
  inflating: source_unsafe/paso3_wordscounter.h
Compilando el codigo...
  CC  paso3_wordscounter.o
  CC  paso3_main.o
  LD  tp
/usr/bin/ld: paso3_main.o: in function `main':
/task/student/source_unsafe/paso3_main.c:27: undefined reference to `wordscounter_destroy'
collect2: error: ld returned 1 exit status
make: *** [/task/student/MakefileTP0:142: tp] Error 1

real    0m0.158s
user    0m0.110s
sys     0m0.043s
[Error] Fallo la compilacion del codigo en 'source_unsafe.zip'. Codigo de error 2
```
En este caso, **undefined reference to `wordscounter_destroy'** es un error el cual indica que si bien la función se encuentra declarada, el *linker* no halló la definición de la misma. Es por esto que a diferencia de todos los anteriores, este es un error del *linker*.

### Paso 4

**a.** En este caso, a diferencia del paso anterior, se define la función `wordscounter_destroy` en `paso4_wordscounter.c`.

**b.** El output de Valgrind para la prueba 'TDA' es el siguiente:
```
==00:00:00:00.000 59== Memcheck, a memory error detector
==00:00:00:00.000 59== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==00:00:00:00.000 59== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==00:00:00:00.000 59== Command: ./tp input_tda.txt
==00:00:00:00.000 59== Parent PID: 58
==00:00:00:00.000 59==
==00:00:00:00.889 59==
==00:00:00:00.889 59== FILE DESCRIPTORS: 5 open at exit.
==00:00:00:00.889 59== Open file descriptor 4: input_tda.txt
==00:00:00:00.889 59==    at 0x495FEAB: open (open64.c:48)
==00:00:00:00.889 59==    by 0x48E2195: _IO_file_open (fileops.c:189)
==00:00:00:00.889 59==    by 0x48E2459: _IO_file_fopen@@GLIBC_2.2.5 (fileops.c:281)
==00:00:00:00.889 59==    by 0x48D4B0D: __fopen_internal (iofopen.c:75)
==00:00:00:00.889 59==    by 0x48D4B0D: fopen@@GLIBC_2.2.5 (iofopen.c:86)
==00:00:00:00.889 59==    by 0x109177: main (paso4_main.c:14)
==00:00:00:00.889 59==
==00:00:00:00.889 59== Open file descriptor 3: /task/student/cases/tda/__valgrind__
==00:00:00:00.889 59==    <inherited from parent>
==00:00:00:00.889 59==
==00:00:00:00.889 59== Open file descriptor 2: /task/student/cases/tda/__stderr__
==00:00:00:00.889 59==    <inherited from parent>
==00:00:00:00.889 59==
==00:00:00:00.889 59== Open file descriptor 1: /task/student/cases/tda/__stdout__
==00:00:00:00.889 59==    <inherited from parent>
==00:00:00:00.889 59==
==00:00:00:00.889 59== Open file descriptor 0: /task/student/cases/tda/__stdin__
==00:00:00:00.889 59==    <inherited from parent>
==00:00:00:00.889 59==
==00:00:00:00.889 59==
==00:00:00:00.889 59== HEAP SUMMARY:
==00:00:00:00.889 59==     in use at exit: 1,977 bytes in 216 blocks
==00:00:00:00.889 59==   total heap usage: 218 allocs, 2 frees, 10,169 bytes allocated
==00:00:00:00.889 59==
==00:00:00:00.889 59== 472 bytes in 1 blocks are still reachable in loss record 1 of 2
==00:00:00:00.889 59==    at 0x483B7F3: malloc (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
==00:00:00:00.889 59==    by 0x48D4AAD: __fopen_internal (iofopen.c:65)
==00:00:00:00.889 59==    by 0x48D4AAD: fopen@@GLIBC_2.2.5 (iofopen.c:86)
==00:00:00:00.889 59==    by 0x109177: main (paso4_main.c:14)
==00:00:00:00.889 59==
==00:00:00:00.889 59== 1,505 bytes in 215 blocks are definitely lost in loss record 2 of 2
==00:00:00:00.889 59==    at 0x483B7F3: malloc (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
==00:00:00:00.889 59==    by 0x109301: wordscounter_next_state (paso4_wordscounter.c:35)
==00:00:00:00.889 59==    by 0x1093B5: wordscounter_process (paso4_wordscounter.c:30)
==00:00:00:00.890 59==    by 0x109197: main (paso4_main.c:24)
==00:00:00:00.890 59==
==00:00:00:00.890 59== LEAK SUMMARY:
==00:00:00:00.890 59==    definitely lost: 1,505 bytes in 215 blocks
==00:00:00:00.890 59==    indirectly lost: 0 bytes in 0 blocks
==00:00:00:00.890 59==      possibly lost: 0 bytes in 0 blocks
==00:00:00:00.890 59==    still reachable: 472 bytes in 1 blocks
==00:00:00:00.890 59==         suppressed: 0 bytes in 0 blocks
==00:00:00:00.890 59==
==00:00:00:00.890 59== For lists of detected and suppressed errors, rerun with: -s
==00:00:00:00.890 59== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```
Los errores arrojados por Valgrind para este caso de prueba son dos. Por un lado, hay 472 bytes en un único bloque que no fueron liberados aunque pudieron haberlo sido (*still reachable*) producto de que en ningún momento se cierra el archivo (*stream*) `input`. Por el otro, se pierden definitvamente 1505 bytes en 215 bloques como resultado de que en ningún momento se libera la memoria reservada para cada uno de los arreglos `delim_words` que se definen por cada vez que se accede a la función. `wordscounter_next_state` dentro de `paso4_wordscounter.h`.

**c.** Para el caso de prueba 'Long Filename' el output de Valgrind obtenido fue:

```
**00:00:00:00.939 47** *** memcpy_chk: buffer overflow detected ***: program terminated
==00:00:00:00.000 47== Memcheck, a memory error detector
==00:00:00:00.000 47== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==00:00:00:00.000 47== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==00:00:00:00.000 47== Command: ./tp input_extremely_long_filename.txt
==00:00:00:00.000 47== Parent PID: 46
==00:00:00:00.000 47==
**00:00:00:00.939 47** *** memcpy_chk: buffer overflow detected ***: program terminated
==00:00:00:00.939 47==    at 0x483E9CC: ??? (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
==00:00:00:00.939 47==    by 0x4843C0A: __memcpy_chk (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
==00:00:00:00.939 47==    by 0x109168: memcpy (string_fortified.h:34)
==00:00:00:00.939 47==    by 0x109168: main (paso4_main.c:13)
==00:00:00:00.959 47==
==00:00:00:00.959 47== FILE DESCRIPTORS: 4 open at exit.
==00:00:00:00.959 47== Open file descriptor 3: /task/student/cases/nombre_largo/__valgrind__
==00:00:00:00.959 47==    <inherited from parent>
==00:00:00:00.959 47==
==00:00:00:00.959 47== Open file descriptor 2: /task/student/cases/nombre_largo/__stderr__
==00:00:00:00.959 47==    <inherited from parent>
==00:00:00:00.959 47==
==00:00:00:00.959 47== Open file descriptor 1: /task/student/cases/nombre_largo/__stdout__
==00:00:00:00.959 47==    <inherited from parent>
==00:00:00:00.959 47==
==00:00:00:00.959 47== Open file descriptor 0: /task/student/cases/nombre_largo/__stdin__
==00:00:00:00.959 47==    <inherited from parent>
==00:00:00:00.959 47==
==00:00:00:00.959 47==
==00:00:00:00.959 47== HEAP SUMMARY:
==00:00:00:00.959 47==     in use at exit: 0 bytes in 0 blocks
==00:00:00:00.959 47==   total heap usage: 0 allocs, 0 frees, 0 bytes allocated
==00:00:00:00.959 47==
==00:00:00:00.959 47== All heap blocks were freed -- no leaks are possible
==00:00:00:00.959 47==
==00:00:00:00.959 47== For lists of detected and suppressed errors, rerun with: -s
==00:00:00:00.959 47== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

En este caso, Valgrind no puede realizar un análisis completo debido a que como consecuencia de un *buffer overflow* la ejecución del programa termina antes de tiempo. Este error es producto de que en `paso4_main.c` se intenta copiar en `filepath` una ruta cuya longitud excede la capacidad máxima de dicha variable.

**d.** Si se utiliza `strncopy()` en lugar de `memcpy()`, se puede solucionar el problema del *buffer overflow* al imponer la capacidad máxima de `filepath` como límite de caracteres a copiar. Sin embargo, el programa continuaría sin funcionar correctamente, pues al recortar la ruta del archivo a n caracteres, la misma no estará completa y `fopen()` devolverá `NULL` al no haber podido encontrar un archivo que corresponda al path recibido.

**e.** Un *segmentation fault* es un error lanzado por el hardware como mecanismo de protección producto de que se intentó acceder a un área de memoria para la cual no se cuenta con autorización de hacerlo.

Por su parte, un *buffer overflow* es un error que se produce cuando durante la escritura en un buffer, el tamaño de la información que se está escribiendo es mayor a la capacidad del mismo, produciendo que se sobrescriban los datos almacenados en las direcciones contiguas de memoria.

### Paso 5
**a.** En primer lugar, uno de los cambios efectuados fue remover la función `memcpy()`, haciendo que en su lugar se le pase directamente como parámetro el path almacenado en `argv[1]` a la función `fopen()`. Luego, se agregó el `fclose()` cuando se termina de usar un archivo distinto al `STDIN`. Además, se eliminó el `malloc()` innecesario en la función `wordscounter_next_state()`. Con estas modificaciones, el programa deja de tener problemas con el uso memoria.

**b.** Las pruebas 'Invalid File' y 'Single Word' fallan porque la cantidad de palabras contadas por el programa son distintas a los resultados esperados. En ambos casos se esperaba como output el valor 1 y se obtuvo 255, en la primera, y 0, en la segunda.

El código de SERCOM que permite visualizar esto se muestra a continuación:

```
[=>] Comparando archivo_invalido/__return_code__...
1c1
< 255
---
> 1

[=>] Comparando una_palabra/__return_code__...
[=>] Comparando una_palabra/__stdout__...
1c1
< 0
---
> 1
```
Donde el símbolo `<` indica el resultado obtenido y `>`, el esperado.

**c.** El output obtenido al ejecutar el comando `hexdump` es el siguiente:

![alt text][hexdump]

Aquí se puede observar que el valor hexadecimal del último caracter del archivo es 64, el cual pasado a base decimal es 100, valor correspondiente al caracter *d* en el còdigo ASCII.

**d.** El resultado obtenido tras ejecutar `GDB` es:

```
GNU gdb (Ubuntu 8.1-0ubuntu3.2) 8.1.0.20180409-git
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
Para las instrucciones de informe de errores, vea:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Leyendo símbolos desde ./tp...hecho.
(gdb) info functions
All defined functions:

File paso5_main.c:
int main(int, char **);

File paso5_wordscounter.c:
void wordscounter_create(wordscounter_t *);
void wordscounter_destroy(wordscounter_t *);
size_t wordscounter_get_words(wordscounter_t *);
void wordscounter_process(wordscounter_t *, FILE *);
static char wordscounter_next_state(wordscounter_t *, char, char);

Non-debugging symbols:
0x0000000000000690  _init
0x00000000000006c0  fclose@plt
0x00000000000006d0  __stack_chk_fail@plt
0x00000000000006e0  strchr@plt
0x00000000000006f0  _IO_getc@plt
0x0000000000000700  __printf_chk@plt
0x0000000000000710  fopen@plt
0x0000000000000720  __cxa_finalize@plt
0x00000000000007e0  _start
0x0000000000000810  deregister_tm_clones
0x0000000000000850  register_tm_clones
0x00000000000008a0  __do_global_dtors_aux
0x00000000000008e0  frame_dummy
0x00000000000009f0  __libc_csu_init
0x0000000000000a60  __libc_csu_fini
0x0000000000000a64  _fini
(gdb) list wordscounter_next_state
29	        int c = getc(text_file);
30	        state = wordscounter_next_state(self, state, c);
31	    } while (state != STATE_FINISHED);
32	}
33
34	static char wordscounter_next_state(wordscounter_t *self, char state, char c) {
35	    const char* delim_words = " ,.;:\n";
36
37	    char next_state = state;
38	    if (c == EOF) {
(gdb) list
39	        next_state = STATE_FINISHED;
40	    } else if (state == STATE_WAITING_WORD) {
41	        if (strchr(delim_words, c) == NULL)
42	            next_state = STATE_IN_WORD;
43	    } else if (state == STATE_IN_WORD) {
44	        if (strchr(delim_words, c) != NULL) {
45	            self->words++;
46	            next_state = STATE_WAITING_WORD;
47	        }
48	    }
(gdb) break 45
Punto de interrupción 1 at 0x964: file paso5_wordscounter.c, line 45.
(gdb) run input_single_state.txt
Starting program: /home/gonzams/Escritorio/Taller/TP0/paso5/tp input_single_state.txt
[Inferior 1 (process 11978) exited with code 0377]
(gdb) quit

```
Los comandos utilizados son los siguientes:

- **gdb ./tp** : se carga el ejecutable en `GDB`.
- **info functions** : lista firmas de las funciones definidas en cada archivo archivo compilado.
- **list wordscounter_next_state** : muestra las líneas que rodean a la función `wordscounter_next_state()`, centradas en la misma.
- **list** : muestra las líneas posteriores a las últimas mostradas.
- **break 45** : coloca un *breakpoint* en la línea 45.
- **run input_single_word.txt** : se ejecuta el programa con el nombre del archivo como parámetro
- **quit** : se sale de `GDB`.

Como se puede observar, el debugger no se detuvo en el breakpoint de la línea 45, esto se debe a que el flujo del programa en ningún momento pasa por dicha línea, por ende el contador de palabras queda en 0. La razón de esto es que entre el último caracter de la palabra y el EOF no hay ninguno de los delimitadores definidos en `words_delim`, por lo cual no se ingresa al `if` que contiene la operación `self->words++;`, dejando sin contabilizar la palabra.

### Paso 6

**a.** En primer lugar, en este paso se cambió el valor de retorno de -1 a 1 en caso de que no se haya podido definir un archivo de entrada para el programa. Luego, los caracteres delimitadores pasaron a ser una constante y, por último, se modificó la lógica de la función `wordscounter_next_state()` para que también se contabilice una palabra cuando esta es seguida por el EOF.

**b.** Las entregas realizadas se muestran a continuación:

![alt text][entregas1]

![alt text][entregas2]

**c.** Finalmente, se muestran los resultados de la ejecución local de la prueba `Single Word` con las distintas variantes indicadas:

![alt text][variantes]

## Conclusión

El desarrollo de este trabajo resultó enriquecedor y de gran utilidad, tanto a nivel materia como personal puesto que se trata de conceptos básicos que resulta fundamental tener en claro.

[valgrind_paso0]: https://github.com/gonzalomartinezs/tp0-taller/blob/master/img/valgrind_paso0.png
[hexdump]: https://github.com/gonzalomartinezs/tp0-taller/blob/master/img/hexdump.png
[entregas1]: https://github.com/gonzalomartinezs/tp0-taller/blob/master/img/entregas1.png
[entregas2]: https://github.com/gonzalomartinezs/tp0-taller/blob/master/img/entregas2.png
[variantes]: https://github.com/gonzalomartinezs/tp0-taller/blob/master/img/variantes.png
