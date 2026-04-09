---
title: "Pipelines en Microsoft Fabric: el director de orquesta que hace que todo funcione junto"
description: "Si los Dataflows Gen2 transforman datos, las Pipelines coordinan cuándo, cómo y en qué orden pasa todo. Son el motor de orquestación de Fabric — y acá te explico cómo funcionan."
pubDate: 2026-04-09
author: "Jimena Cambronero"
image: "https://jimenacambronero.netlify.app/foto/me.png"
tags: ["Microsoft Fabric", "Pipelines", "Data Engineering", "Orquestación", "Data Factory", "Data Analytics"]
---

Si llegaron hasta acá conmigo en esta serie, ya sabemos qué es Fabric, entendemos la diferencia entre OneLake, Lakehouse y Data Warehouse, y conocemos los Dataflows Gen2. Eso es una base muy sólida . . . pero esto recién esta comenzando, el universo de Fabric es enorme! y seguro hay una pregunta que como me paso a mí ya te estas haciendo vos. . . "¿y quién coordina todo esto?"

La respuesta es las maravillosas Pipelines!!! con redoble de tambores! 🥁

Si los Dataflows Gen2 son los que limpian y transforman los datos, las Pipelines son las que se aseguran de que todo eso pase en el orden correcto, a la hora correcta, y que alguien se entere si algo sale mal. Son el director de orquesta de nuestra arquitectura de datos — y como todo buen director, su trabajo es que los demás brillen sin que nadie note lo que hacen ellos. 🎻

---

## ¿Qué es una Pipeline en Fabric?

Una Pipeline es un **flujo de trabajo automatizado** que agrupa y coordina un conjunto de actividades para completar una tarea. Esas actividades pueden ser muy variadas: copiar datos de un origen a un destino, ejecutar un Dataflow Gen2, correr un Notebook de Spark, refrescar un modelo semántico de Power BI, o incluso mandar un mensaje de Teams cuando algo falla o cuando algo sale bien, que también merece celebración 🎉 porque no!?

Lo que hace especial a las Pipelines no es ninguna actividad en particular, es la capacidad de **encadenarlas, condicionarlas y orquestarlas** como un flujo coherente. Podemos definir que la actividad B se ejecute solo si la actividad A terminó con éxito, o que si algo falla se ejecute una actividad de error que notifique al equipo. Podemos hacer que varias actividades corran en paralelo para ahorrar tiempo, o en secuencia cuando una depende de la otra.

Y todo eso desde una interfaz visual, con un canvas donde arrastramos y conectamos las actividades como si estuviéramos armando un diagrama de flujo. Sin código obligatorio — aunque si queremos agregar lógica más avanzada, podemos hacerlo con expresiones.

---

## ¿En qué se diferencia de un Dataflow Gen2?

Esta es *la* pregunta. En el artículo anterior hablamos de Dataflows Gen2 y mencionamos que las Pipelines son su complemento natural. Pero vale la pena dejarlo bien claro porque es una de las confusiones más comunes al empezar:

**Un Dataflow Gen2 transforma datos.** Se conecta a una fuente, aplica transformaciones con Power Query, y escribe el resultado en un destino. Hace una cosa y la hace muy bien.

**Una Pipeline orquesta tareas.** No transforma datos por sí sola, lo que hace es coordinar las actividades que sí lo hacen. Puede ejecutar un Dataflow Gen2, después correr un Notebook, después refrescar un modelo de Power BI, todo en el orden correcto y con manejo de errores.

Una analogía que me ayudó a entenderlo: el Dataflow Gen2 es el cocinero que prepara el plato. La Pipeline es el jefe de cocina que decide en qué orden salen los platos, qué pasa si uno se quema, y quién avisa a la mesa si hay una demora. 🍳

| | **Dataflow Gen2** | **Pipeline** |
|---|---|---|
| **¿Qué hace?** | Ingesta y transforma datos | Orquesta y coordina actividades |
| **¿Transforma datos?** | Sí, con Power Query | No directamente |
| **¿Maneja errores?** | Limitado | Sí, con rutas de éxito/falla |
| **¿Ejecuta lógica condicional?** | No | Sí (If/Else, loops) |
| **¿Corre en paralelo?** | No | Sí |
| **Perfil ideal** | Analistas, bajo/medio código | Ingenieros de datos |

¿Son excluyentes? Para nada. De hecho, lo más habitual en proyectos reales es que una Pipeline ejecute un Dataflow Gen2 como una de sus actividades. Son piezas que trabajan juntas.

---

## Las actividades principales: el toolbox de una Pipeline

Una Pipeline puede tener hasta 120 actividades. No vamos a ver todas . . . tranquilos, no hay quiz al final 😄, pero sí las más importantes para arrancar:

### Actividades de movimiento de datos

**Copy Data** es probablemente la actividad más usada. Copia datos desde un origen a un destino — puede ser de una base de datos SQL a un Lakehouse, de un archivo CSV a un Warehouse, de Amazon S3 a OneLake, entre cientos de combinaciones posibles. Es rápida, simple, y no aplica transformaciones: solo mueve los datos tal como están.

Es el punto de entrada más directo cuando necesitamos traer datos a Fabric desde un sistema externo sin transformaciones complejas.

### Actividades de transformación

**Dataflow Gen2** — sí, podemos incluir un Dataflow Gen2 como actividad dentro de una Pipeline. Esto es muy común: la Pipeline orquesta cuándo se ejecuta el dataflow, qué pasa si falla, y qué actividad sigue después.

**Notebook** — ejecuta un Notebook de Spark (Python, Scala o SQL). Ideal cuando necesitamos transformaciones complejas que van más allá de lo que Power Query puede hacer.

**Stored Procedure / SQL Script** — ejecuta lógica SQL directamente en el Data Warehouse. Útil para quienes prefieren encapsular la lógica de negocio en procedimientos almacenados.

### Actividades de control de flujo

Estas son las que le dan inteligencia a la Pipeline:

**If Condition** — evalúa una condición y ejecuta distintas actividades según si es verdadera o falsa. Por ejemplo: si el Dataflow terminó con más de 0 filas procesadas, continuamos; si procesó 0 filas, mandamos una alerta.

**For Each** — itera sobre una lista de elementos y ejecuta una actividad por cada uno. Muy útil cuando necesitamos procesar múltiples tablas o archivos con la misma lógica.

**Until** — repite una actividad hasta que se cumpla una condición. El equivalente de un *while loop* en código.

**Wait** — pausa la ejecución por un tiempo definido. Más útil de lo que parece cuando necesitamos esperar que un proceso externo termine antes de continuar.

### Actividades de notificación

**Teams Activity / Email Activity** — mandan un mensaje de Teams o un email cuando se completa una actividad o cuando algo falla. Porque enterarse de que un pipeline explotó a las 3 AM por un email que nadie lee hasta el mediodía no es una estrategia de monitoreo, es una fuente de estrés. 📧😅

**Semantic Model Refresh** — refresca un modelo semántico de Power BI después de que los datos fueron actualizados. Así los reportes siempre muestran los datos más recientes sin que nadie tenga que acordarse de hacer clic en "actualizar".

---

## Orquestación y schedules: cuándo y cómo se ejecuta todo

Una Pipeline puede ejecutarse de tres formas:

**Manual** — la lanzamos desde el editor cuando queremos. Útil para pruebas y para ejecuciones puntuales.

**Programada (Schedule)** — se ejecuta automáticamente según un calendario que definimos: todos los días a las 6 AM, cada hora, cada lunes a las 8 PM. Podemos tener hasta **20 schedules distintos** sobre la misma Pipeline, lo que da mucha flexibilidad para escenarios donde el mismo flujo necesita correr con distintas frecuencias según el día o el contexto.

**Por evento** — se ejecuta cuando ocurre algo específico: llega un archivo nuevo a una carpeta de OneLake, se completa otra Pipeline, cambia un dato en una base de datos. Ideal para arquitecturas reactivas donde no queremos esperar al próximo schedule sino actuar en el momento en que los datos están disponibles.

### El canvas y las conexiones

En el editor de Pipelines, cada actividad tiene tres salidas posibles:

- **On Success** (verde) → lo que se ejecuta si la actividad terminó bien
- **On Failure** (rojo) → lo que se ejecuta si la actividad falló
- **On Completion** (azul) → lo que se ejecuta siempre, sin importar el resultado

Usar bien estas conexiones es lo que convierte una Pipeline frágil en una robusta. Una Pipeline bien diseñada no solo sabe qué hacer cuando todo va bien, sabe qué hacer cuando algo sale mal. Y algo siempre sale mal en algún momento. La ley de Murphy no discrimina tecnologías. 🙃

---

## Un ejemplo concreto: la Pipeline de ventas diarias

Volvamos al ejemplo de la empresa que quiere tener sus ventas actualizadas cada mañana. Con todo lo que sabemos ahora, una Pipeline típica para ese escenario podría verse así:

1. **Copy Data** → copia los datos de ventas del ERP hacia la capa Bronze del Lakehouse (datos en crudo, tal como vienen)
2. **Dataflow Gen2** → limpia y transforma los datos de Bronze a Silver (elimina duplicados, estandariza formatos, etc.)
3. **Notebook** → agrega los datos de Silver a la capa Gold (ventas por día, región y producto)
4. **Semantic Model Refresh** → refresca el modelo de Power BI para que los reportes muestren los datos del día
5. **Teams Activity** *(On Success)* → manda un mensaje al canal del equipo: "✅ Datos de ventas actualizados correctamente"
6. **Teams Activity** *(On Failure)* → manda una alerta si algo falló en cualquiera de los pasos anteriores

Todo esto se ejecuta automáticamente a las 5 AM, antes de que el equipo comercial entre a trabajar. Nadie tiene que hacer nada manualmente, y si algo falla, el equipo se entera de inmediato en lugar de descubrirlo cuando ya están en una reunión con el cliente. 🙌

---

## Buenas prácticas para empezar

**Empecemos simple.** Una Pipeline con dos o tres actividades que funciona es infinitamente mejor que una Pipeline de veinte actividades que nunca termina de probarse. Agregamos complejidad de a poco, a medida que entendemos cómo se comporta.

**Siempre conectemos las salidas de error.** Una actividad sin manejo de falla es un problema silencioso esperando suceder. Como mínimo, conectemos la salida roja a una notificación.

**Usemos parámetros.** Las Pipelines admiten parámetros que podemos definir al momento de ejecutar o pasar desde otra Pipeline. Esto las hace reutilizables — en lugar de tener diez Pipelines casi idénticas para diez tablas distintas, podemos tener una Pipeline parametrizada que se ejecuta diez veces con distintos valores.

**Nombremos bien las actividades.** El nombre por defecto "Copy data1", "Copy data2", "Copy data3" es una receta para el caos cuando tenemos que debuggear algo a las 2 de la mañana. Usemos nombres descriptivos desde el principio.

**Monitoreemos desde el día uno.** Fabric tiene un Monitoring Hub donde podemos ver el historial de ejecuciones, el tiempo que tardó cada actividad y los errores. Revisarlo regularmente nos permite detectar problemas antes de que se conviertan en incidentes.

---

## Recursos para seguir aprendiendo

**Microsoft Learn — Pipeline Overview** → la documentación oficial cubre todos los tipos de actividades con ejemplos y referencias. El punto de partida más completo.
→ [Pipeline Overview en Microsoft Learn](https://learn.microsoft.com/en-us/fabric/data-factory/pipeline-overview)

**ADN Fabric (YouTube)** → tiene videos en español sobre Pipelines con casos prácticos. Muy útil para ver el canvas en acción y entender cómo se conectan las actividades visualmente.
→ [Canal ADN Fabric](https://www.youtube.com/@adnfabric)

**"Introducción a Microsoft Fabric" de Diana y Nelson** → una referencia muy completa para entender los componentes de Fabric en contexto, incluyendo las Pipelines dentro de la arquitectura general.
→ [Disponible en Amazon](https://www.amazon.com)

---

*¿Ya están usando Pipelines en sus proyectos con Fabric? ¿O todavía están en la etapa de "tengo todo en un Dataflow Gen2 y rezo para que funcione"? 😄 Cuéntenme en LinkedIn — me encanta saber en qué punto del camino está cada uno.* 🧉
