# Capítulo 6: Workflow: Proyectos

## Jesús Rauda

### 25/09/21

¿Qué pasa cuando quieres volver a correr un análisis? La mejor forma de recrear un ambiente de trabajo es a partir de los datos y los scripts. 

Un consejo útil a largo plazo es no guardar tus espacios de trabajo entre sesiones. Para ello es recomendable desactivar las opciones de *"Restaurar .Rdata en el espacio de trabajo al iniciar"* y seleccionar la opción de *nunca guardar .RData al salir*. Esto se consigue en **Tools -> Global options**. Esto te obligará a guardar el código >:D

Para corrobar que el código que has guardado es correcto, existen dos atajos del teclado muy útiles:

* Cmd/Ctrl-Shift-F10 : Reiniciar Rstudio.
* Cmd/Ctrl-Shift-S : Correr el script actual.

Los análisis viven y se alimentan en el directorio de trabajo, para obtener el directorio de trabajo actual utilizamos el comando **getwd()**.

No es recomendable, pero podemos definir el directorio de trabajo con el comando **setwd("ruta/del/directorio")**

**De la misma forma no es aconsejable utilizar rutas absolutas**

Finalmente, la mejor forma de mantener nuestros análisis ordenados es crear un proyecto de RStudio en cada análisis (**File -> New Project**).

Al guardar un código también estará en el archivo .Rproj, así como los archivos que crees a partir de ellos se mantendrán en el directorio del proyecto. De esta manera podrás reproducir facilmente los análisis que hayas realizado previamente.

## Resumen

1. Crea un proyecto de Rstudio para cada proyecto de análisis de datos.
2. Manten los archivos de datos y códigos ahí.
3. Guarda tus archivos de salida en el directorio del proyecto.
4. Sólo utiliza rutas relativas.
