---
title: "Dataflow vs Notebook: ¿quién gana por knockout? 🥊"
description: "Para quienes venimos del mundo Power BI, los Dataflows son zona de confort. Pero cuándo conviene saltar al Notebook? Un recorrido por los cuatro rounds que definen la pelea."
pubDate: 2026-09-21
author: "Jimena Cambronero"
image: "https://jimenacambronero.netlify.app/foto/me.jpg"
tags: ["Microsoft Fabric", "Dataflows Gen2", "Notebooks", "PySpark", "Spark SQL", "Data Engineering"]
---

Seamos honestos: para quienes venimos del mundo Power BI, los Dataflows son muchas veces nuestra zona de confort. Los entendemos, los controlamos, y sabemos exactamente dónde hacer clic y qué va a pasar cuando apretamos "publicar". Son confiables. Son nuestros amigos.

Pero que pasa cuando llega el día en que alguien nos dice. . . *"para esto mejor usá un Notebook"*.

Y nos quedamos con esa cara de ¿perdón?

Porque la primera vez que abrímos un Notebook en Fabric nos encontramos con una pantalla llena de celdas, un motor que se llama Spark, y código. Sin botones de colores. Sin menús desplegables. Solo nosotros, el cursor parpadeando, y la sensación de no saber que hacer.

Respirá. Porque en este artículo te voy a contar qué es un Notebook, en qué situaciones le gana por knockout al Dataflow, y por qué — spoiler — no tenés que aprender casi nada nuevo para empezar. 🥊

## Primero . . . qué es exactamente un Notebook?

Antes de meternos en la pelea, aclaremos qué hay en cada esquina.

Un **Notebook** es un bloc de notas interactivo donde combinás tres cosas: **código**, **texto explicativo** y **resultados**. El código son las instrucciones que le das a la máquina. El texto son tus notas y explicaciones. Y los resultados aparecen justo debajo de cada celda, apenas ejecutás.

¿Cómo imaginártelo si nunca usaste uno? Pensá en un Excel donde cada celda, en vez de una fórmula, puede ejecutar código de verdad y mostrarte el resultado al instante. Sin salir de la pantalla. Sin exportar a ningún lado. Sin el clásico *"actualizando... 0%... 0%... 0%..."* que todos conocemos. 😅

Y lo más importante: hace exactamente lo mismo que un Dataflow. Te conectás a una fuente, aplicás transformaciones, y guardás el resultado. La diferencia está en **cómo** lo hacés y en **cuánto** podés hacer.

## Y para qué sirve el Dataflow entonces?

Para muchas cosas. Bastantes, de hecho. Y no, no estamos acá para jubilarlo.

El **Dataflow Gen2** es la herramienta ideal cuando:

- Las transformaciones son visuales y manejables con Power Query
- El volumen de datos es bajo o medio
- El equipo mezcla perfiles técnicos y no tan técnicos
- No querés ni necesitás escribir código

Si eso describe tu día a día, el Dataflow es tu mejor aliado y no hace falta que lo reemplaces con nada.

El problema aparece cuando los datos crecen, la lógica se complica, o necesitás hacer algo que Power Query sencillamente no tiene. Ahí es cuando el Notebook entra al ring.

## Las cuatro situaciones donde el Notebook gana por puntos . . . o por knockout

### 🥊 Round 1: el volumen

Te acordás cuando tu Dataflow se ponía lento con demasiados datos, o directamente se quejaba y tiraba un error que no sabías muy bien cómo resolver?

El Notebook, con **Apache Spark** por detrás, procesa millones de filas sin despeinarse. Spark está diseñado para trabajar en paralelo, distribuyendo el procesamiento en múltiples máquinas al mismo tiempo. No le importa si tenés 10 filas o 10 millones. No se queja. No tira errores raros a las 3 AM.

**Ganador del round: Notebook 🏆**

### 🥊 Round 2: el control

En un Dataflow, hay cosas que pasan por dentro y vos no ves. Similar a una caja negra de avión. Pasos que se ejecutan solos y que cuando algo falla... buena suerte encontrando dónde.

En el Notebook ves exactamente qué pasa con tus datos en cada celda. Podés ejecutar paso a paso, ver el resultado intermedio, inspeccionar lo que tenés antes de seguir. Sin sorpresas. Sin ese *"que sea lo que Dios quiera"* que todos conocemos demasiado bien.

**Ganador del round: Notebook 🏆**

### 🥊 Round 3: las transformaciones complejas

Cuántas veces necesitaste hacer algo que Power Query simplemente no tenía? Una lógica de deduplicación que dependía de múltiples condiciones? Un cálculo que requería mirar filas anteriores? Una transformación que en Power Query requería cinco pasos y una oración?

En el Notebook, si lo podés pensar, lo podés escribir. No estás limitada a lo que la herramienta trae de fábrica. Tenés toda la potencia de Python y Spark a disposición.

**Ganador del round: Notebook 🏆**

### 🥊 Round 4: el costo

Este round se lo lleva el Dataflow... pero solo si el volumen es pequeño.

Para grandes volúmenes de datos, los Notebooks bien usados suelen consumir menos recursos de capacidad (**CUs**) que un Dataflow haciendo el mismo trabajo. Y en Fabric, el consumo de CUs se traduce directamente en costo. Así que no es solo una cuestión de poder — también es de eficiencia.

**Ganador del round: depende del volumen 🤝**

## Pero ... y el código?

Sí, ya sé lo que estás pensando. *"Todo muy lindo, pero yo no sé escribir código"*.

Primero: no hace falta saber Python para empezar. En Fabric podés escribir en **Spark SQL** — que es casi calcado al SQL que ya conocés de Power BI, SQL Server o donde sea que lo hayas aprendido.

- Un `SELECT` para leer datos → igual.
- Un `WHERE` para filtrar → igual.
- Un `GROUP BY` para agrupar → igual.

Cambian cuatro cositas del dialecto y nada más.

Así que si te da cosa el código, empezá por Spark SQL. Y cuando te sientas cómoda, das el salto a **PySpark** — que es Python. Sin apuro, sin presión.

Y segundo... aunque te trabés, tenés dos comodines buenísimos.

**Data Wrangler** es una herramienta visual dentro del propio Notebook que te permite aplicar transformaciones con clics — filtrar, renombrar, eliminar nulos — y te genera el código PySpark automáticamente. Es como tener Power Query adentro del Notebook, pero que además te enseña a escribir código mientras lo usás.

**La IA** — Copilot si tu empresa lo tiene activado, o Claude, ChatGPT o Gemini si no — te sacan de casi cualquier atasco. Le explicás en lenguaje natural lo que querés hacer y te proponen el código. No lo hacen todo por vos, pero te sacan del bloqueo de la pantalla en blanco. Y eso, cuando estás aprendiendo, vale oro.

## El patrón que se repite en todos los proyectos

Una vez que empezás a usar Notebooks, vas a notar que el flujo de trabajo siempre es el mismo. Da igual el proyecto, da igual el cliente. Son cinco pasos que se repiten:

1. **Ingestar** — leer los datos de la fuente. El mismo paso de conexión de tu Dataflow.
2. **Explorar** — ver las primeras filas, entender qué tenés. Como la vista previa de Power Query pero con más control.
3. **Entender la estructura** — los tipos de datos, los nulos, las columnas que alguien nombró mal hace tres años y que ahora son tu problema. Los tenemos todos.
4. **Transformar** — limpiar, renombrar, calcular, unir. Tu zona de confort... pero en código.
5. **Guardar en Delta** — el equivalente al "aplicar y cerrar" de Power Query, pero guardando en tu Lakehouse en formato Delta con todas las ventajas que eso trae: versionado, trazabilidad, ACID.

Lo estas viendo no?? Es el mismo proceso de siempre. Lo único que cambia es la herramienta.

## Y dónde encaja esto en la arquitectura Medallion?

Ya hablamos de la **Arquitectura Medallion** — Bronze, Silver y Gold. El Notebook es el que hace el trabajo pesado entre capas cuando el Dataflow se queda corto:

- **Bronze → Silver**: el Notebook limpia, estandariza, elimina duplicados y une fuentes. Todo lo que Power Query haría... pero con millones de filas y sin quejarse.
- **Silver → Gold**: el Notebook agrega, calcula métricas, construye el modelo que Power BI va a consumir.

Y al final del flujo, Power BI se conecta al Gold con **Direct Lake** y muestra los reportes actualizados. El Notebook no reemplaza a Power BI ni al Dataflow — trabaja con ellos.

## Un tip que tu yo del futuro te va a agradecer

**Documentá tu Notebook.** Sí, ya sé, nadie lo hace. Pero hacelo igual.

En un Notebook tenés dos tipos de celdas: las de código y las de **Markdown** — donde escribís texto libre. Títulos, explicaciones, notas. Usá esas celdas para dejar claro qué hace cada sección, por qué tomaste tal decisión, qué significa ese campo que nadie sabe de dónde viene.

Cuando pasen tres meses y tengas que volver a ese Notebook — o cuando alguien de tu equipo lo tenga que mantener — van a agradecer que esté ordenado y explicado. Documentar no es perder tiempo. Es ganarlo después. Te lo firmo. ✍️

## Quién gana la pelea?

**Ninguno. Y los dos.**

El Dataflow y el Notebook no son rivales — son herramientas distintas para momentos distintos. El Dataflow es tu punto de entrada natural, visual y accesible. El Notebook es el paso siguiente cuando el volumen crece, la lógica se complica o necesitás más control.

La clave está en saber **cuándo usar cada uno**. Y con este artículo, ya tenés el criterio para decidir.

No le tengas miedo al Notebook. En cuanto creás el primero... ya no hay vuelta atrás 😊

📺 [Miralo en mi canal de YouTube](https://www.youtube.com/@jimenacambronero)

## Fuentes consultadas

- Microsoft Learn — How to use Microsoft Fabric Notebooks
- Microsoft Learn — Explore and transform Spark data with Data Wrangler
- Microsoft Fabric Blog — April 2025 Feature Summary — Data Wrangler

---

¿Ya diste el salto del Dataflow al Notebook? O todavía no te animaste? 🧉
