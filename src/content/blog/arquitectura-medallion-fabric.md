---
title: "🏅 Arquitectura Medallion en Fabric: Bronze, Silver y Gold — o cómo ordenar la casa antes de que todo explote"
description: "La Arquitectura Medallion no es una herramienta nueva ni algo exclusivo de Fabric, pero sí una de esas ideas que cambian completamente la forma de trabajar con datos. Acá te cuento cómo funcionan las capas Bronze, Silver y Gold."
pubDate: 2026-05-05
author: "Jimena Cambronero"
image: "https://jimenacambronero.netlify.app/foto/me.png"
tags: ["Microsoft Fabric", "Medallion Architecture", "Bronze Silver Gold", "Data Engineering", "Delta Lake", "Lakehouse", "Data Analytics"]
---

Si llegaron hasta acá en esta serie, ya tenemos lo que podríamos llamar los cimientos y creo yo que hasta bastante bien puestos. Ya vimos qué es Fabric, entendimos OneLake, el Lakehouse y el Data Warehouse, y también nos metimos con Dataflows Gen2 y Pipelines.

Hasta ahora hablamos mucho de herramientas y o artefactos. Pero ahora viene el momento en el que todo eso empieza a tener sentido de verdad.

Porque la pregunta deja de ser "¿qué herramienta uso?" y pasa a ser:

> ¿cómo organizo todo esto sin que se convierta en un caos? 😅

Y ahí aparece la **Arquitectura Medallion**.

No es una herramienta nueva, no hay ningún botón mágico, y tampoco es algo exclusivo de Fabric. Pero sí es una de esas ideas que, cuando la entendemos, nos cambia completamente la forma de trabajar con datos y te aseguro que también te lo facilitara.

---

## ¿Qué es la Arquitectura Medallion?

La Arquitectura Medallion es, básicamente, un **patrón de diseño para organizar datos en capas con distintos niveles de calidad**.

Y la clave está en esa palabra: **calidad**.

Porque los datos no nacen limpios. Llegan como llegan, y creo que esto ya lo hemos padecido un poco todos! A veces llegan bastante bien y otras tantas no tanto, con duplicados, con formatos distintos, con campos que nadie documentó y con valores que te hacen levantar una ceja 🤨

Entonces, en lugar de intentar arreglar todo en un solo paso, lo que siempre implica cruzar los dedos para que funcione, lo que propone Medallion es **separar ese proceso en capas**.

Tres capas, concretamente:

- **Bronze** → datos tal como llegan
- **Silver** → datos limpios y consistentes
- **Gold** → datos listos para análisis

De ahí el nombre, si! como las medallas, cada capa representa un nivel superior 🥉🥈🥇

Una analogía que me gusta mucho es pensar en una cocina. **Bronze** es cuando volvés del súper y dejás todo en la mesada: bolsas, envases, cosas mezcladas. **Silver** es cuando lavás, cortás y organizás los ingredientes. **Gold** es el plato terminado que realmente vas a servir 🍽️, las mejores analogías son comida, no me digan que no?

y ya sabemos que nadie quiere cocinar directamente desde la bolsa del súper y bueno con los datos pasa exactamente lo mismo.

---

## La base técnica: Delta Lake . . . por qué esto no es solo teoría!

Para que todo esto funcione en la práctica — y no sea solo un diagrama lindo, porque si, a mi me gusta hacerme un diagrama en papel para bajar las ideas! — hay una pieza clave: **Delta Lake**.

En Fabric, especialmente en Lakehouse, los datos no son simplemente archivos sueltos. Están organizados en **tablas Delta**, que combinan:

- archivos **Parquet** (eficientes y comprimidos)
- un **transaction log** que registra todos los cambios

Esto permite algo fundamental que es tener **comportamiento tipo base de datos sobre un Data Lake**. Es decir, propiedades ACID y no es que hace falta memorizar la sigla, lo importante es esto:

> podés trabajar con los datos sin miedo a que una escritura a medias, un proceso concurrente o un error te deje todo inconsistente 😌

Y hay un extra que, en la vida real, vale oro, el **Time Travel**, porque a quien no le gustaría viajar un poco en el tiempo! Bueno, acá vas a poder!

Si un pipeline sobreescribe datos por error, si una transformación salió mal o si alguien "probó algo en producción"… podemos volver a una versión anterior de la tabla. Es literalmente tener un "deshacer" en los datos 🙏

En Fabric, las tablas dentro de un Lakehouse usan este formato. Y aunque en el Data Warehouse trabajamos con tablas relacionales más tradicionales, el motor también está optimizado para analítica a gran escala.

---

## Las capas, llevadas a la realidad

### 🥉 Bronze: donde todo empieza (y nada se toca)

Bronze es la puerta de entrada. Acá llega todo, exactamente como viene del sistema origen. Sin filtros, sin limpieza, sin transformaciones.

Y esto, aunque al principio suene contraintuitivo, es clave. Porque **Bronze no es una capa "sucia"**. Es una capa **fiel al origen**.

Si algo falla más adelante — y segura te digo que va a pasar, porque nuestro amigo Murphy no falla nunca 😄 — Bronze es lo que te permite reconstruir todo sin depender del sistema original otra vez.

En Fabric, muchas veces esta capa se implementa en la sección **Files** del Lakehouse, con una estructura organizada por fuente y por fecha de carga. Esto facilita muchísimo la ingesta incremental y la trazabilidad.

Pero no es la única opción. En algunos escenarios, Bronze también se guarda como tablas Delta si se necesita control de esquema o consultas tempranas.

Lo importante no es tanto el formato… sino la intención. Y esa intención es clara:

> guardar todo tal como llegó

Acá hay un detalle que marca la diferencia en proyectos reales: la famosa **metadata**. Columnas como:

- cuándo llegó el dato
- de qué sistema vino
- en qué archivo estaba

son las que nos van a permitir entender qué pasó cuando un número no cuadra. Y sí, incluso ese dato que claramente está mal… se guarda igual. Porque incluso los errores cuentan una historia.

---

### 🥈 Silver: donde los datos empiezan a ser confiables

Si Bronze es "lo que llegó", Silver es "lo que ya podemos usar sin miedo".

Acá empieza el trabajo de verdad. Tomamos los datos crudos y los transformamos en algo coherente:

- eliminamos duplicados
- normalizamos formatos
- corregimos tipos de datos
- cruzamos fuentes
- aplicamos reglas básicas

Es la capa donde los datos dejan de ser caóticos y pasan a ser **consistentes**. En Fabric, esto normalmente se implementa como **tablas Delta en la sección Tables del Lakehouse**, lo que permite consultarlas con SQL, reutilizarlas y usarlas como base para múltiples procesos.

Pero hay algo más importante que cualquier transformación técnica: la **trazabilidad** 🔍

Cada registro en Silver debería poder rastrearse hacia atrás hasta Bronze. Porque cuando algo está mal, no alcanza con saber que está mal. Necesitás saber **por qué**.

Y Silver es lo que hace que esa respuesta exista. Además, esta capa tiene otro beneficio clave: la **reutilización**. Una buena capa Silver permite que múltiples modelos Gold usen la misma base sin tener que repetir lógica. Es la diferencia entre cocinar una vez bien… o rehacer todo cada vez 🔁

---

### 🥇 Gold: donde los datos generan valor

Gold es la capa que ve el negocio. Acá ya no estamos limpiando datos. Estamos **modelándolos para responder preguntas**. Y esto es un cambio importante de mentalidad.

En Gold, los datos no están organizados como vienen . . . están organizados **como se necesitan**. La estructura más habitual es el **modelo estrella**, tablas de hechos con métricas y tablas de dimensiones que aportan contexto.

Esto no es casualidad ni moda. Está optimizado para análisis. En Fabric, esta capa puede implementarse tanto en **Lakehouse** como en **Data Warehouse**, dependiendo del enfoque del equipo.

Y acá aparece uno de los puntos fuertes del ecosistema: **Direct Lake** ⚡ Power BI puede conectarse directamente a las tablas sin necesidad de importar datos ni ejecutar procesos de refresh tradicionales y esto decide si no cambia bastante el juego?

---

## Por qué no saltear capas?

Porque todos lo pensamos en algún momento. "¿Y si voy directo de Bronze a Gold y listo?" Funciona… al principio. Pero en cuanto algo no cuadra, empieza el problema.

Sin Silver:

- no hay separación de responsabilidades
- no hay trazabilidad clara
- la lógica se mezcla con los datos

Y aparece ese clásico:

> "los datos están… pero no confiamos en ellos" 😬

Silver no es burocracia. Es lo que evita que todo dependa de magia.

---

## Cómo encaja todo esto en Fabric

Si miramos todo lo que vimos hasta ahora, la foto completa empieza a cerrarse.

Las **Pipelines** se encargan de mover e ingerir datos hacia **Bronze**. Los **Dataflows Gen2** o **Notebooks** transforman esos datos hacia **Silver**. Y a partir de ahí se construye **Gold**, con Spark o con SQL.

No son herramientas sueltas. Están pensadas para encajar en este flujo. Y cuando lo empezamos a ver asi, decide si todo no empieza a tener mucho más sentido 😊

---

## Para cerrar . . .

La Arquitectura Medallion no va de herramientas. Va de **orden**.

Porque en datos, el problema no es que algo no funcione… el problema es cuando funciona más o menos y nadie sabe por qué. Y ahí es donde empieza el caos.

Medallion no es que te salva de todo. Pero al menos te da un mapa 🧭

Y en este mundo… eso ya es muchísimo.

---

También vas a poder encontrar contenido en formato video en mi canal de YouTube, videos cortos donde te voy a estar explicando de manera muy sencilla todos los conceptos que tenes que conocer de Microsoft Fabric.

📌 Suscribite a mi canal de YouTube: [https://www.youtube.com/@jimenacambronero](https://www.youtube.com/@jimenacambronero)

¿Ya estás trabajando con este enfoque o seguís en la fase de "tengo todo en una sola tabla y sorprendentemente funciona"? Te leo en LinkedIn 🧉
