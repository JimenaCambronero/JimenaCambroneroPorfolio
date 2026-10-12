---
title: "Transformación vs Orquestación en Fabric: no es lo mismo y vale la pena entender por qué 🔄"
description: "Dataflows, Notebooks, Pipelines… cuál uso y cuándo? La diferencia entre transformar y orquestar en Microsoft Fabric, explicada con ejemplos concretos."
pubDate: 2026-05-28
author: "Jimena Cambronero"
image: "https://jimenacambronero.netlify.app/foto/me.jpg"
tags: ["Microsoft Fabric", "Dataflows Gen2", "Pipelines", "Notebooks", "Orquestación", "Data Engineering"]
---

Si vienen siguiendo esta serie, que se ha demorado un poquito en salir esta vez!, perdón! ya nos cruzamos con varias herramientas de Microsoft Fabric: Dataflows Gen2, Pipelines, Notebooks… y probablemente apareció una duda bastante común:

> *"Pero pará… si todas trabajan con datos, cuál uso y cuándo?"*

Y honestamente, es normal confundirse al principio. Porque sí: todas participan del flujo de datos. Pero cada una tiene un rol completamente distinto.

Y entender esa diferencia cambia muchísimo cómo diseñamos soluciones en Fabric.

Acá viene la confesión: es uno de los conceptos que más confusión genera cuando empezamos. Y no es para menos — todas estas herramientas "mueven datos" de alguna forma. Entonces cuál uso? Cuándo uso un Dataflow y cuándo una Pipeline? Y los Notebooks dónde entran?

Este artículo existe para responder exactamente eso. Sin vueltas, con ejemplos concretos, y con la esperanza de que después de leerlo nunca más mezclen los conceptos. 🤞

## La diferencia fundamental

Antes de entrar en detalle, la distinción clave:

**Transformar** es trabajar sobre los datos: limpiarlos, darles forma, combinarlos, enriquecerlos. El resultado es un dato mejor que el que entró.

**Orquestar** es coordinar cuándo y cómo se ejecutan las cosas: en qué orden, a qué hora, qué pasa si algo falla, qué proceso viene después. El resultado es un flujo que funciona solo.

Una analogía que me gusta: en una cocina, **transformar** es cocinar — cortar, mezclar, hervir, condimentar. **Orquestar** es ser el jefe de cocina — decidir en qué orden salen los platos, coordinar a cada cocinero, y asegurarse de que si algo se quema haya un plan B. 🍳

Son dos trabajos distintos. Y en Fabric, hay herramientas distintas para cada uno.

## Las herramientas de transformación

### Dataflows Gen2: transformación visual, sin código

El Dataflow Gen2 es la herramienta de transformación más accesible de Fabric. Basado en Power Query, permite conectarse a una fuente, aplicar pasos de limpieza y transformación de forma visual, y escribir el resultado en un destino dentro de OneLake.

**¿Qué hace bien?**

- Limpiar columnas, cambiar tipos de dato, eliminar duplicados
- Combinar datos de múltiples fuentes
- Ingesta y transformación de volúmenes bajos a medios
- Todo desde una interfaz visual, sin escribir código

**¿Qué no hace?**

- No orquesta. No sabe qué pasa antes ni después de él.
- No maneja errores complejos ni lógica condicional avanzada.
- No es la herramienta ideal para grandes volúmenes o transformaciones muy complejas.

**Perfil ideal**: analistas de datos, perfiles low-code, equipos que vienen de Power BI.

### Notebooks: transformación con código

Los Notebooks son el entorno de transformación para cuando necesitamos más poder: Python, PySpark, Scala o SQL sobre Apache Spark. Permiten procesar grandes volúmenes de datos, aplicar lógica compleja, y trabajar directamente sobre las tablas Delta del Lakehouse.

**¿Qué hace bien?**

- Transformaciones complejas que Power Query no puede manejar
- Procesamiento de grandes volúmenes con Spark
- Lógica de negocio avanzada: merges, upserts, deduplicación compleja
- Data Science: exploración, entrenamiento de modelos, feature engineering

**¿Qué no hace?**

- Tampoco orquesta. Un Notebook se ejecuta y listo — no sabe qué viene después.
- Tiene mayor curva de aprendizaje que los Dataflows.

**Perfil ideal**: ingenieros de datos, científicos de datos, perfiles técnicos.

## La herramienta de orquestación

### Pipelines: el director de orquesta

Las Pipelines no transforman datos — coordinan las herramientas que sí lo hacen. Permiten encadenar actividades, definir el orden de ejecución, manejar errores, y programar cuándo corre todo.

**¿Qué hace bien?**

- Ejecutar un Dataflow Gen2, después un Notebook, después refrescar un modelo de Power BI — en ese orden
- Manejar errores: si algo falla, ejecutar una actividad de notificación
- Lógica condicional: si el Dataflow procesó 0 filas, no continuar
- Programar ejecuciones automáticas: todos los días a las 5 AM
- Ejecutar actividades en paralelo para ahorrar tiempo

**¿Qué no hace?**

- No transforma datos por sí sola. Sin Dataflows o Notebooks adentro, una Pipeline vacía no sirve de mucho.

**Perfil ideal**: ingenieros de datos, arquitectos de datos, cualquiera que necesite automatizar flujos de trabajo.

## Se pueden usar juntos? Sí, y es lo más habitual

Acá está el punto que más importa: estas herramientas no compiten entre sí — se complementan.

El flujo más habitual en proyectos reales es exactamente este:

**Pipeline** (orquesta)

- **Dataflow Gen2** → transforma Bronze → Silver
- **Notebook** → transforma Silver → Gold
- **Semantic Model Refresh** → actualiza Power BI
- **Teams Notification** → avisa que todo terminó bien ✅

La Pipeline es el contenedor que coordina todo. Cada herramienta adentro hace lo que mejor sabe hacer.

## Cuál uso en cada situación?

### Situación 1: Tengo un CSV en SharePoint y quiero limpiarlo y cargarlo en el Lakehouse

→ **Dataflow Gen2**. Fuente conocida, transformación visual, volumen moderado. No necesito código ni orquestación compleja.

### Situación 2: Tengo 500 millones de filas en Bronze y necesito aplicar lógica de deduplicación compleja para pasar a Silver

→ **Notebook con Spark**. El volumen y la complejidad requieren el poder de Spark. Power Query no va a alcanzar.

### Situación 3: Quiero que todos los días a las 6 AM se ejecute el Dataflow, después el Notebook, y si todo sale bien se refresque el modelo de Power BI

→ **Pipeline**. Es un flujo de múltiples pasos, con dependencias y schedule. Exactamente para lo que está diseñada.

### Situación 4: Quiero limpiar datos de un ERP y también de una API externa, y combinarlos en una sola tabla

→ **Dataflow Gen2**. Múltiples fuentes, transformación visual, merge de datos. Power Query maneja esto muy bien.

### Situación 5: Si el proceso nocturno falla, quiero recibir un mensaje en Teams con el detalle del error

→ **Pipeline**. El manejo de errores y las notificaciones son responsabilidad de la orquestación.

## La regla práctica para no confundirse más

Cuando tengamos que elegir una herramienta, hacemos estas tres preguntas:

**¿Necesito transformar datos?**

- Poca complejidad, sin código → **Dataflow Gen2**
- Alta complejidad o gran volumen → **Notebook**

**¿Necesito coordinar múltiples pasos, manejar errores o programar ejecuciones?**

- Sí → **Pipeline** (que puede incluir un Dataflow o Notebook adentro)

**¿Necesito las dos cosas?**

- **Pipeline que orquesta un Dataflow Gen2 y/o un Notebook**. La combinación más habitual en producción.

## Un apunte para quienes están estudiando para la DP-600 o DP-700

Este es uno de los conceptos que más aparece en los exámenes. La distinción entre transformación y orquestación no es solo teórica — los escenarios del examen te piden elegir la herramienta correcta para cada situación.

La regla de oro que siempre funciona: si el escenario habla de **limpiar, combinar o preparar datos** → transformación (Dataflow o Notebook). Si habla de **automatizar, programar, encadenar o manejar errores** → orquestación (Pipeline).

---

¿Todavía mezclan Dataflows con Pipelines? No se preocupen, nos pasó a todos 😄 Cuéntenme en LinkedIn cómo están aplicando estas herramientas en sus proyectos. 🧉
