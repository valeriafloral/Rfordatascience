# Capítulo 5: Análisis de datos exploratorios
## Jesús Rauda
### 9/25/2021

En este capítulo vamos a visualizar y transformar nuestros datos de una forma sistemática en un proceso iterativo conocido como Análisis Exploratorio de Datos o (EDA).

El ciclo iterativo se compone de 3 pasos:

* 1.- **Generar preguntas** iniciales.
* 2.- **Buscar respuestas** mediante visualización, transformación y modelado.
* 3.- **Obtener conclusiones** que sirvan de base para generar nuevas preguntas.

## Prerrequisitos

Combinaremos lo aprendido acerca de **dplyr** y **ggplot2**.
 
 ```{r}
library(tidyverse)

library(nycflights13)

 ```
 
## Preguntas

### Cantidad antes de calidad

EDA es unn estado mental, un proceso creativo que requiere trabajo, por lo que la manera más sencilla de hacer preguntas de calidad tiene que ver con generar ingentes cantidades de preguntas cuantitativas. Experimentar en este primer paso es la clave para empezar a afinar nuestros sentidos a preguntarnos cosas de mayor relevancia.

Sin embargo, un par de preguntas iniciales que pueden ayudar son:

* 1.- ¿Qué tipo de varianza ocurre en mis variables?
* 2.- ¿Qué tipo de covarianza ocurre entre mis variables?

Finalmente, para facilitar el capítulo es necesario definir claramente los siguientes conceptos: 

* **Variable**: Cantidad, cualidad o propiedad medible.
* **Valor**: Estado de una variable al ser medida.
* **Observación**: Conjunto de medidas realizadas bajo condiciones similares, simultánemente o sobre el mismo objeto.
* **Datos tabulares**: Conjunto de valores asociados a variables y observaciones.


## Variación

Es la tendencia de los valores de una variable para cambiar entre mediciones. Cada medición conlleva un error y cada variable tiene su propio patrón variación que nos puede revelar características importantes del fenómeno.

### Visualizando las distribuciones 

Depende de la naturaleza de las variables, una u otra representación disponible en ggplot2 será más adecuada. Algunos ejemplos para observar la variación de una sola variable son: 

* **1 Variable categórica**: Gráficos de barras (*geom_bar*)


 ```{r}

ggplot(data = diamonds) +
  geom_bar(mapping = aes(x = cut))

 ```

Allternativamente podemos realizar los conteos de las categorías *manualmente* con la función dplyr::count()

 ```{r}

diamonds %>% 
  count(cut)

 ```
 
* **1 Variable continua**: Histogramas (geom_histogram)

 ```{r}

ggplot(data = diamonds) +
  geom_histogram(mapping = aes(x = carat), binwidth = 0.5)
  
 ``` 
 
Donde binwidth define el tamaño de los subconjuntos de la variable y es muy aconsejable utilizar distintos rangos para el tamaño de los subconjuntos.
 
De la misma forma, podemos obtener la tabla con las funciones dyplr::count() y ggplot2::cutwidth()

 ```{r}

diamonds %>% 
  count(cut_width(carat, 0.5))
  
 ``` 

 A veces nos será útil superponer histogramas, ya sea porque queremos separar una variable continua a partir de una categórica o porque queremos observar más de una variable de manera individual. Para esos casos se recomienda la función geom_freqpoly() que a diferencia de geom_histogram() utiliza lineas en lugar de barras.
 
 ```{r}
ggplot(data = diamonds, mapping = aes(x = carat, colour = cut)) +
  geom_freqpoly(binwidth = 0.1)
  
 ``` 

### Valores típicos e inusuales

El tamaño de los subconjuntos pueden ayudarnos a revelar la distribución de los valores dentro de los subconjuntos más grandes.
 
 ```{r}
  
  ggplot(data = diamonds, mapping = aes(x = carat)) +
  geom_histogram(binwidth = 0.01)

 ``` 
 
En cambio los valores más aislados dificilmente son vistos en histogramas o gráficas de barra sin la ampliación necesaria en esa zona. Por ejemplo comparemos estas dos gráficas

 
 ```{r}
  
ggplot(diamonds) + 
  geom_histogram(mapping = aes(x = y), binwidth = 0.5)
  
 ``` 
  
 ```{r}

ggplot(diamonds) + 
  geom_histogram(mapping = aes(x = y), binwidth = 0.5) +
  coord_cartesian(ylim = c(0, 50))
  
 ``` 
 
Las funciones **ggplot::xlim()** y **ggplot::ylim()** ayudan a establecer los límites que queremos en nuestro gráfico.

De manera similar con las funciones de **dplyr** podemos obtener una tabla con los outliers:

 ```{r}
unusual <- diamonds %>% 
  filter(y < 3 | y > 20) %>% 
  select(price, x, y, z) %>%
  arrange(y)
unusual

 ``` 
  
### Ejercicios

1. Explora la distribución de cada una de las variables x, y, y z en el set de datos diamantes. ¿Qué aprendiste? Piensa en un diamante y cómo decidirías qué dimensiones corresponden a la longitud, ancho y profundidad.

 
<details>
<summary><b>Respuesta</b></summary>

 ```{r}

ggplot(data = diamonds) + geom_freqpoly(mapping = aes(x = x, y =  ..density..), color = "blue", binwidth = 0.1) + geom_freqpoly(mapping = aes(x = y, y =  ..density..), color = "red", binwidth = 0.1) + geom_freqpoly(mapping = aes(x = z, y =  ..density..), color = "green", binwidth = 0.1) + xlim(0,10)


 ``` 

</details>

2. Explora la distribución de precio. ¿Ves algo inusual o sorprendente? (Sugerencia: Piensa detenidamente en binwidth y asegúrate de usar un rango largo de valores.)


<details>
<summary><b>Respuesta</b></summary>

 ```{r}
 
ggplot(data = diamonds) + geom_histogram(mapping = aes(x = price), binwidth = 10) 

ggplot(data = diamonds) + geom_histogram(mapping = aes(x = price), binwidth = 100) 

ggplot(data = diamonds) + geom_histogram(mapping = aes(x = price), binwidth = 500) 

ggplot(data = diamonds) + geom_histogram(mapping = aes(x = price), binwidth = 1000) 

 
 ```
</details>

3. ¿Cuántos diamantes tienen 0.99 quilates? ¿Cuántos son de 1 quilate? ¿Qué piensas que puede ser la causa de dicha diferencia?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

ggplot(data = diamonds) + geom_histogram(mapping = aes(x = carat), binwidth = 0.01) + xlim(0,1.5) 

diamonds %>%
  count(carat) %>%
  filter(carat<=1.0) %>%
  arrange(desc(carat))
 
 ```

</details>

4. Compara y contrasta coord_cartesian() contra xlim() o ylim() en cuanto a acercar la imagen en un histograma. ¿Qué pasa si no modificas el valor de binwidth? ¿Qué pasa si intentas acercar la imagen de manera que solo aparezca la mitad de una barra?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}
 
ggplot(data = diamonds) + geom_histogram(mapping = aes(x = price)) 


ggplot(data = diamonds) + geom_histogram(mapping = aes(x = price)) + xlim(0,5000)


ggplot(data = diamonds) + geom_histogram(mapping = aes(x = price)) + coord_cartesian(xlim = c(0,5000))

 ```
</details>


## Valores faltantes

¿Tienes valores inusuales? ¿No quieres trabajar con ellos? ¿Qué hacer con ellos?

* Borralos, olvídate de su existencia y nunca le cuentes a nadie lo que viste ahí:

  
 ```{r}

diamonds2 <- diamonds %>% 
  filter(between(y, 3, 20))
  
 ``` 

* Si crees que borrarlos de la faz de la Tierra es un poco extremo, trata convirtiéndolos a NA. Para ello utilizamos la función **mutate()** con una condicional provista por **ifelse()**:


 ```{r}

diamonds2 <- diamonds %>% 
  mutate(y = ifelse(y < 3 | y > 20, NA, y))
  
 ``` 
 
Esto provocará que ggplot te haga ser consciente de cuantos datos estás ignorando por tener una desviación muy grande. 

 ```{r}

ggplot(data = diamonds2, mapping = aes(x = x, y = y)) + 
  geom_point()
  
 ``` 

Puedes callar esa voz llamada ggplot, llamada consciencia, con un simple argumento **na.rm = TRUE**


 ```{r}

ggplot(data = diamonds2, mapping = aes(x = x, y = y)) + 
  geom_point(na.rm = TRUE)
  
 ``` 
 
También es posible que quieras observar esos datos inusuales, no sea que nos digan cosas interesantes. A continuación se muestra una propuesta de como obtener un gráfico de los datos usuales y los outliers. 

 ```{r}

nycflights13::flights %>% 
  mutate(
    cancelled = is.na(dep_time),
    sched_hour = sched_dep_time %/% 100,
    sched_min = sched_dep_time %% 100,
    sched_dep_time = sched_hour + sched_min / 60
  ) %>% 
  ggplot(mapping = aes(sched_dep_time)) + 
    geom_freqpoly(mapping = aes(colour = cancelled), binwidth = 1/4)
    
 ``` 
### Ejercicios



1. ¿Qué sucede con los valores faltantes en un histograma? ¿Qué pasa con los valores faltantes en una gráfica de barras? ¿Cuál es la razón detrás de esta diferencia?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

diamonds2 <- diamonds %>% 
  mutate(y = ifelse(y < 3 | y > 20, NA, y))

flights2 <- flights %>%
  mutate(carrier = ifelse(day == 1, NA, carrier))

ggplot(data = flights2, mapping = aes( x = carrier)) + 
  geom_bar()

ggplot(data = diamonds2, mapping = aes( x = y)) + 
  geom_histogram()


 ```

</details>

2. ¿Qué efecto tiene usar na.rm = TRUE en mean() (media) y sum()(suma)?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}
sum( c (10, 20, 30, NA), na.rm = TRUE)

sum( c (10, 20, 30, NA))

mean( c (10, 20, 30, NA), na.rm = TRUE)

mean( c (10, 20, 30, NA))

 ```
</details>

## Covarianza

Tendencia de los valores de 2 o más variables a desviarse de una manera relacionada.

### Una variable categórica y otra continua 
 
Una de las desventajas de geom_freqpoly() es que dificulta la comparación respecto a la variable categórica porque depende de la población del subconjunto. Por ello podemos evaluar graficas normalizadas para observar proporción mediante el argumento **density**. Para ello comparemos estas 2 graficas.

 ```{r}
 
ggplot(data = diamonds, mapping = aes(x = price)) + 
  geom_freqpoly(mapping = aes(colour = cut), binwidth = 500)

 ``` 
 
 ```{r}

ggplot(data = diamonds, mapping = aes(x = price, y = ..density..)) + 
  geom_freqpoly(mapping = aes(colour = cut), binwidth = 500)

 ``` 

Otra manera de representar es un gráfico de caja y bigotes, donde podremos ver claramente donde se ubica el grueso de nuestros datos y los valores inusuales:
 
 ```{r}

ggplot(data = diamonds, mapping = aes(x = cut, y = price)) +
  geom_boxplot()

 ``` 
 
 ```{r}

ggplot(data = diamonds, mapping = aes(x = reorder(cut, price, FUN = median), y = price)) +
  geom_boxplot()

 ``` 
  
 ```{r}

ggplot(data = diamonds, mapping = aes(x = reorder(cut, price, FUN = median), y = price)) +
  geom_boxplot() + coord_flip()

 ``` 
#### Ejercicios 


1. Usa lo que has aprendido para mejorar la visualización de los tiempos de salida de los vuelos cancelados versus los no cancelados.
 
 

<details>
<summary><b>Respuesta</b></summary>

 ```{r}
 
nycflights13::flights %>% 
  mutate(
    cancelled = is.na(dep_time),
    sched_hour = sched_dep_time %/% 100,
    sched_min = sched_dep_time %% 100,
    sched_dep_time = sched_hour + sched_min / 60
  ) %>% 
  ggplot(mapping = aes(x = cancelled, y = sched_dep_time)) + 
  geom_boxplot()

 ```

</details>


2. ¿Qué variable del conjunto de datos de diamantes es más importante para predecir el precio de un diamante? ¿Cómo está correlacionada esta variable con el corte? ¿Por qué la combinación de estas dos relaciones conlleva que los diamantes de menor calidad sean más costosos?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

ggplot(data = diamonds, mapping = aes(x = cut, y = price)) +
  geom_boxplot()

ggplot(data = diamonds, mapping = aes(x = carat, y = price)) +
  geom_boxplot(mapping = aes(group = cut_width(carat, 0.5)))

ggplot(data = diamonds, mapping = aes(x = color, y = price)) +
  geom_boxplot()

ggplot(data = diamonds, mapping = aes(x = clarity, y = price)) +
  geom_boxplot()

 ```

</details>

3. Intercambia las variables x e y en un diagrama de caja vertical y crear un diagrama de cajas horizontal. ¿Cómo se compara esto a usar coord_flip()?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

ggplot(data = diamonds, mapping = aes(x = reorder(cut, price, FUN = median), y = price)) +
  geom_boxplot() + coord_flip()

ggplot(data = diamonds, mapping = aes(y = reorder(cut, price, FUN = median), x = price)) +
  geom_boxplot()


 ```

</details>

4. Un problema con los diagramas de caja es que fueron desarrollados en un tiempo en que los set de datos eran más pequeños y por ende tienden a mostrar un número muy grande de “valores atípicos”. Una estrategia para remediar este problema es el diagrama letter value. Instala el paquete lvplot, e intenta usar geom_lv() para mostrar la distribución de precio vs corte. ¿Qué observas? ¿Cómo interpretas los gráficos?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}
 
install.packages("lvplot")

library(lvplot)

ggplot(data = diamonds, mapping = aes(x = cut, y = price)) + geom_lv()

 ```

</details>

5. Compara y contrasta geom_violin() con un geom_histogram() dividido en facetas, o un geom_freqpoly() codificado por colores. ¿Cuáles son las ventajas y desventajas de cada método?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}
ggplot(data = diamonds, mapping = aes(x = price, y = ..density..)) +
  geom_freqpoly(mapping = aes(color = cut), binwidth = 500)
  
ggplot(data = diamonds, mapping = aes(x = price)) +
  geom_histogram() +
  facet_wrap(~cut, ncol = 1, scales = "free_y")
  
ggplot(data = diamonds, mapping = aes(x = cut, y = price)) +
  geom_violin() +
  coord_flip()


 ```

</details>


### Dos variables categóricas

La visualización de dos variables categóricas requiere el conteo de cada combinación existente entre esas dos. geom_count() ofrece una forma automática de hacerlo:

 ```{r}
 
ggplot(data = diamonds) +
  geom_count(mapping = aes(x = cut, y = color))
  
 ``` 

Otra aproximación es utilizar **dplyr::count()** y otro método de visualización como geom_tile()
 
 ```{r}

diamonds %>% 
  count(color, cut) %>%  
  ggplot(mapping = aes(x = color, y = cut)) +
    geom_tile(mapping = aes(fill = n))
    
 ``` 
#### Ejercicios



1. ¿Cómo podrías cambiar la escala del conjunto de datos anterior para mostrar de manera más clara la distribución del corte dentro del color, o del color dentro de la variable corte?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

diamonds %>%
  count(color, cut) %>%
  group_by(color) %>%
  mutate(prop = n / sum(n)) %>%
  ggplot(mapping = aes(x = color, y = cut)) +
  geom_tile(mapping = aes(fill = prop))

 ```

</details>

2. Usa geom_tile() junto con dplyr para explorar la variación del retraso promedio de los vuelos en relación al destino y mes del año. ¿Qué hace que este gráfico sea difícil de leer? ¿Cómo podrías mejorarlo?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

flights %>%
  group_by(month, dest) %>%                       
  summarise(dep_delay = mean(dep_delay, na.rm = TRUE)) %>%
  group_by(dest) %>%                              
  filter(n() == 12) %>%                           
  ungroup() %>%
  mutate(dest = reorder(dest, dep_delay)) %>%
  ggplot(aes(x = factor(month), y = dest, fill = dep_delay)) +
  geom_tile() +
  labs(x = "Month", y = "Destination", fill = "Departure Delay")

 ```

</details>

3. ¿Por qué es un poco mejor usar aes(x = color, y = corte) en lugar de aes(x = corte, y = color) en el ejemplo anterior?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

diamonds %>%
  count(color, cut) %>%
  group_by(color) %>%
  mutate(prop = n / sum(n)) %>%
  ggplot(mapping = aes(x = color, y = cut)) +
  geom_tile(mapping = aes(fill = prop))
  
  diamonds %>%
  count(color, cut) %>%
  group_by(color) %>%
  mutate(prop = n / sum(n)) %>%
  ggplot(mapping = aes(x = cut, y = color)) +
  geom_tile(mapping = aes(fill = prop))

 ```

</details>

### Dos variables continuas

Los diagramas de dispersión son muy utiles para visualizar dos variables continuas, para ello utilizamos la función geom_point().

 ```{r}
 
ggplot(data = diamonds) +
  geom_point(mapping = aes(x = carat, y = price))

 ``` 

En bases de datos muy grandes un poco de transparencia puede hacer más clara la visualización:
 
 ```{r}

ggplot(data = diamonds) + 
  geom_point(mapping = aes(x = carat, y = price), alpha = 1 / 100)

 ``` 

Agrupando los datos en subconjuntos también podemos obtener gráficos propios de variables categóricas como los provistos por geom_bin2d o geom_hex (requiere la instalación del paquete "hexbin"):
 
 ```{r}

ggplot(data = diamonds) +
  geom_bin2d(mapping = aes(x = carat, y = price))

# install.packages("hexbin")
ggplot(data = diamonds) +
  geom_hex(mapping = aes(x = carat, y = price))

 ``` 
 
O agrupando los datos en función a su distribución:
 
 ```{r}
 
ggplot(data = diamonds, mapping = aes(x = carat, y = price)) + 
  geom_boxplot(mapping = aes(group = cut_width(carat, 0.1)))

ggplot(data = diamonds, mapping = aes(x = carat, y = price)) + 
  geom_boxplot(mapping = aes(group = cut_number(carat, 20)))
   
 ``` 

#### Ejercicios

1. En lugar de resumir la distribución condicional con un diagrama de caja, podrías usar un polígono de frecuencia. ¿Qué deberías considerar cuando usas cut_width() en comparación con cut_number()? ¿Qué impacto tiene este parámetro en la visualización bidimensional de quilate y precio?


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

ggplot(data = diamonds, mapping = aes(color = cut_number(carat,5), x = price)) + 
  geom_freqpoly()

ggplot(data = diamonds, mapping = aes(color = cut_width(carat,1), x = price)) + 
  geom_freqpoly()


 ```

</details>

2. Visualiza la distribución de quilate, segmentada según la variable precio.


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

ggplot(data = diamonds, mapping = aes(y = carat, x = cut_number(price, 10))) + 
  geom_boxplot() + coord_flip()
  
 ```

</details>

4. Combina dos de las técnicas que has aprendido para visualizar la distribución combinada de las variables corte, quilate y precio.


<details>
<summary><b>Respuesta</b></summary>

 ```{r}

ggplot(diamonds, aes(color = cut_number(carat, 5), y = price, x = cut)) +
  geom_boxplot()

 ```
 </details>
 
5. Los gráficos bidimensionales revelan observaciones atípicas que podrían no ser visibles en gráficos unidimensionales. Por ejemplo, algunos puntos en la gráfica a continuación tienen una combinación inusual de valores x y y, que hace que algunos puntos sean valores atípicos aún cuando sus valores x e y parecen normales cuando son examinados de manera individual.



 ```{r}

ggplot(data = diamonds) +
  geom_point(mapping = aes(x = x, y = y)) +
  coord_cartesian(xlim = c(4, 11), ylim = c(4, 11))

 ``` 

¿Por qué es mejor usar un diagrama de dispersión que un diagrama basado en rangos en este caso?


## Patrones y modelos

Si existe una relación sistemática entre dos variables, aparecerá como un patrón en los datos. Posteriormente tendrán que surgir una serie de preguntas como: 

* ¿Es un patrón real o aleatorio?
* ¿Cómo describir la relación que implica ese patrón?
* ¿Que tan fuerte es la relación?
* ¿Hay otras variables affectando la relación?
* ¿La relación es dependiente de subconjuntos?

Los patrones revelan covarianza, lo que reduce la incertidumbre del sistema y permiten mejorar las predicciones de valores de una variable a partir de otra.

Los modelos son las herramientas predilectas para extraer los patrones de las variables, lo que compone la cuarta parte de este libro.

