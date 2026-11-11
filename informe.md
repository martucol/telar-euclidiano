# **Telar euclidiano**

Laboratorio de Creación Algorítmica 2026 - UNTREF 

*María Martina Colmenero*

---

## **Informe**

Telar euclidiano es un instrumento digital que plasma estructuras rítmicas en patrones visuales de píxeles. El sistema trabaja con dos principios: la secuencia rítmica euclidiana, y el tejido tafetán, el más simple del telar, donde cada hilo pasa una vez por arriba y una vez por abajo de aquellos con los que se cruza.

Las secuencias euclidianas reparten k pulsos en n pasos de la forma más pareja posible. Para realizar esto, el sistema usa una fórmula modular que evalúa un paso a la vez: si el resto de dividir (i * k) por n es menor que k, va 1; sino, va 0:

```(i * k) % n < k ```

Para pasar de la secuencia euclidiana al color, el sistema genera una secuencia E(k,n) de unos y ceros y, por cada paso, asigna un color a cada "*hilo*" (fila/columna de píxeles) uno por uno dentro de un bucle. Cada vez que aparece un 1, se le asigna al hilo un nuevo color, y los 0 que vienen después mantienen el color de ese 1. La secuencia de unos y ceros se transforma en una secuencia de franjas de colores:

```
E(3,8) = 10010010 → 100 color A , 100 color B , 10 color A 
```

El desarrollo se hizo en etapas. La primera versión es unidimensional, con *hilos* en una sola dirección que forman franjas de color. La segunda versión es bidimensional, y la misma secuencia se aplica a la *urdimbre*, que son los *hilos* verticales o columnas, y a la *trama*, que son los horizontales o filas. Los *hilos* se entrelazan en *tafetán* y en cada cruce se alterna qué *hilo* queda adelante.

```
        col0 col1 col2 col3
fila0    U    T    U    T
fila1    T    U    T    U
fila2    U    T    U    T
fila3    T    U    T    U
```

El patrón se muestra animado, como en un telar: primero crecen las urdimbres, una por una, de arriba hacia abajo, y después las tramas, de izquierda a derecha, cruzándose en tafetán. Cada *hilo* tarda un tiempo fijo, definido en la configuración.

En la última versión, se suma además un acento a la secuencia, que se calcula aleatoriamente entre 1 y k-1, generando una sub-secuencia euclidiana de E(a,k). 

```
E(3,8) = 10010010 
acento E(1,3) = 100 → 20010010 → 200 color C (acento) , 100 color A , 10 color B
```

Esta es la versión descripta en los diagramas y pseudocódigo en los anexos, que funciona en loop continuo a modo de los antiguos "protectores de pantalla" ([ver online](https://martucol.github.io/telar-euclidiano/auto)).

A partir de este algoritmo se creó la herramienta online, que suma parámetros ajustables, la opción de usar acentos o no, y la de que urdimbre y trama tengan la misma secuencia o secuencias distintas. Tiene un modo determinista, donde el usuario define cada parámetro, y uno exploratorio, donde elige algunos valores iniciales y el sistema genera patrones aleatorios a partir de ellos. Cada patrón se puede exportar como imagen del canvas o como tile, el módulo mínimo que se repite sin cortes, para usarlo en otros soportes, como fondos, impresiones o mosaicos.


## **Antecedentes**

El punto de partida de este proyecto fue el trabajo de Godfried Toussaint, *The Euclidean Algorithm Generates Traditional Musical Rhythms* (2005), que plantea que las secuencias generadas por el algoritmo de Euclides coinciden con muchos ritmos tradicionales de distintas culturas, como el tresillo cubano. Este proyecto traslada esa idea al ámbito visual, para descubrir qué patrones se generan al aplicar el mismo algoritmo a lógicas textiles. Para calcular las secuencias se tomó la fórmula modular que Ezequiel Abregú usa en el secuenciador euclidiano de *Code that sounds* (2025), además de sumar también de este libro el concepto de los acentos.

Del aspecto textil, las artistas del taller textil de la Bauhaus, como Anni Albers en *On Weaving* (1965), pensaron el tejido como estructura, con el hilo como unidad mínima y el patrón como resultado de reglas de cruce y colores de los hilos.

Diego Alberti, en *Lliklla* (2023), reconstruye con luces LED y código visualizaciones inspiradas en textiles andinos, donde un tejido aleatorio electrónico repite la acción manual del telar. En su obra el tejido avanza por aleatoriedad, mientras que en este trabajo se utiliza la aleatoriedad para seleccionar los parámetros pero la estructura la define Euclides.

Otro antecedente relevante es el trabajo de Vera van de Seyp, creative coder que trabaja con el traslado de imágenes generadas por código a producciones textiles. Ella desarrolló en el MIT Media Lab el *Overshot Pattern Tool* (2023), una herramienta web que genera patrones de overshot, una técnica de telar con base en tafetán y una trama suplementaria que flota sobre ella. A partir de este proyecto surge la idea de que el producto final de esta investigación sea una herramienta online y no únicamente una obra. 


