---
title: "Dataflows Gen2 en Microsoft Fabric: tu primer pipeline sin escribir una línea de código"
description: "Dataflows Gen2 es el motor de ingesta y transformación de datos de Microsoft Fabric. Si conocés Power Query, ya sabés usarlo. Acá te explico cómo funciona y cuándo usarlo."
pubDate: 2026-03-31
author: "Jimena Cambronero"
image: "https://jimenacambronero.netlify.app/foto/me.png"
tags: ["Microsoft Fabric", "Dataflows Gen2", "Power Query", "Data Engineering", "OneLake", "Data Analytics"]
---

Si leíste el artículo anterior sobre Microsoft Fabric, ya tenés claro qué es OneLake y por qué la plataforma es un cambio de paradigma. Ahora viene la pregunta que todos nos hemos hecho y que si estas empezando seguro te vas a hacer vos también: ¿y cómo meto mis datos ahí adentro?

La respuesta, al menos para empezar, tiene nombre: Dataflows Gen2.

## Qué es un Dataflow Gen2 ... y qué no es

Un Dataflow Gen2 es el motor de ingesta y transformación de datos de Microsoft Fabric. Permite conectarse a más de 150 fuentes de datos distintas, aplicar transformaciones visuales sobre esos datos, y depositarlos directamente en OneLake, todo desde una interfaz sin código, o con muy poco código.

La forma más directa de entenderlo: es Power Query, pero integrado nativamente en Fabric.

Si alguna vez abriste Power Query en Excel o en Power BI Desktop para limpiar una tabla, filtrar columnas o combinar dos fuentes, ya sabés usar Dataflows Gen2. La interfaz es prácticamente la misma. La diferencia está en lo que pasa después: en lugar de cargar los datos en un modelo de Power BI, los escribís directamente como tablas Delta en OneLake, disponibles para cualquier otro servicio de Fabric.

Lo que no es un Dataflow Gen2 es un orquestador de procesos complejos. Si necesitás ejecutar un notebook de Python, disparar un procedimiento almacenado, copiar archivos en paralelo y enviar un email al final, eso le corresponde a las Pipelines. Los Dataflows son la herramienta de ingesta y transformación; las Pipelines son las que coordinan el flujo completo. Esta distinción importa, y volvemos a ella más adelante.

## Por qué los Dataflows Gen2 son el mejor punto de entrada a Fabric

Cuando empezás con Fabric, tenés varias opciones para mover datos hacia OneLake: Pipelines con la actividad Copy Data, Notebooks de Spark, o Dataflows Gen2. Cada una tiene su lugar, pero para alguien que viene del mundo Power BI, los Dataflows son la curva de aprendizaje más suave por varias razones:

**La interfaz ya la conocés.** El editor de Power Query en Dataflows Gen2 es el mismo que usás en Power BI Desktop. Mismos menús, misma lógica de pasos aplicados, misma forma de conectar fuentes. No hay que aprender nada nuevo para dar los primeros pasos.

**No necesitás saber programar.** Todas las transformaciones básicas — filtrar filas, cambiar tipos de datos, renombrar columnas, combinar tablas, calcular columnas nuevas — se hacen de forma visual. El editor genera el código M por debajo, pero no hace falta tocarlo a menos que quieras algo muy específico.

**El destino está integrado.** Una de las mejoras clave respecto a los Dataflows de Power BI (Gen1) es que Gen2 escribe directamente en destinos dentro de Fabric: Lakehouse, Warehouse o KQL Database. No hace falta configurar conexiones adicionales ni preocuparse por dónde van los datos. Los definís una vez y Fabric se encarga del resto.

**Guarda el trabajo automáticamente.** Mientras trabajás en el editor, Fabric guarda los cambios en tiempo real. Podés cerrar el navegador, volver al día siguiente, y encontrar todo exactamente donde lo dejaste. Para quien viene de perder trabajo por cerrar Power BI sin guardar, esto es un alivio que no se pondera lo suficiente.

## Las mejoras respecto a Gen1 que importan

Dataflows Gen2 no es solo un cambio de nombre. Hay diferencias técnicas concretas que lo hacen más poderoso:

**Staging intermedio.** Gen2 usa internamente un Lakehouse y un Warehouse de staging (llamados DataflowsStaging) para procesar los datos antes de escribirlos al destino final. Esto permite manejar volúmenes más grandes sin que la transformación explote en memoria. No lo ves directamente en la interfaz, pero notás la diferencia cuando procesás tablas grandes.

**Salida en Delta.** Los datos que Gen2 escribe en el Lakehouse van en formato Delta Parquet, no en tablas de Power BI propietarias. Esto significa que las tablas resultantes son accesibles desde Spark, desde SQL, desde Power BI con Direct Lake, y desde cualquier herramienta que entienda Delta. Los datos que producís con un Dataflow son ciudadanos de primera clase en OneLake.

**Motor de cómputo mejorado.** Gen2 incorpora el Modern Evaluator, un motor más eficiente que reduce significativamente el tiempo de ejecución y el consumo de CUs comparado con Gen1. Según pruebas de la comunidad (Data Mozart, 2025), Gen2 con Modern Evaluator puede consumir menos de la mitad de capacidad que Gen1 para el mismo volumen de datos. No es un detalle menor cuando estás pagando por CUs.

**Más de 150 conectores.** SQL Server, Azure SQL, Oracle, Salesforce, Dynamics 365, SharePoint, Excel, APIs REST, JSON, CSV, y muchos más. Si el dato existe en algún lado, es probable que haya un conector para traerlo.

## Cuándo usar Dataflows Gen2 ... y cuándo no

Esta es la pregunta que más confunde cuando empezás. La guía de la comunidad y la documentación oficial coinciden en lo mismo:

**Usá Dataflows Gen2 cuando:**

- Necesitás conectarte a una fuente externa y traer datos a OneLake sin escribir código.
- Los datos necesitan transformaciones de limpieza antes de llegar al destino: cambios de tipo, deduplicación, merges de tablas.
- Tu equipo tiene más perfil analítico que de ingeniería, y Power Query ya es una herramienta conocida.
- El volumen de datos es de bajo a medio.

**Preferí otra herramienta cuando:**

- Necesitás orquestar múltiples pasos: ingestar, transformar, ejecutar un script SQL, notificar. Eso es trabajo de Pipelines.
- Los volúmenes son muy grandes y las transformaciones son complejas. Los Notebooks de Spark escalan mejor.
- Necesitás lógica de actualización incremental avanzada o manejo de cambios tipo SCD Tipo 2. Gen2 soporta append y replace, pero no update nativo — una limitación real que vale conocer antes de diseñar la arquitectura.

Una práctica muy habitual en proyectos reales: usar Dataflows Gen2 para la ingesta y transformación, y envolverlos dentro de una Pipeline que controla el orden de ejecución, maneja los errores y programa la frecuencia de actualización. Los dos se complementan, no se excluyen.

## Cómo funciona en la práctica: el flujo paso a paso

Para hacerlo concreto, imaginá este escenario: tenés un archivo CSV en SharePoint con datos de ventas mensuales, y querés tenerlo como una tabla limpia en tu Lakehouse para que Power BI lo consuma.

**Paso 1:** Crear el Dataflow Gen2 en tu workspace de Fabric. Desde + New item seleccionás Dataflow Gen2. Se abre el editor de Power Query Online.

**Paso 2:** Conectar la fuente. Seleccionás Get data, buscás SharePoint, ingresás la URL del sitio y elegís el archivo. En segundos ves la vista previa de los datos. Hasta acá, igual que en Power BI Desktop.

**Paso 3:** Transformar. Cambiás los tipos de datos de las columnas, filtrás filas con valores nulos en campos críticos, renombrás columnas con nombres que tienen sentido para el negocio, calculás una columna de margen si la necesitás. Cada paso queda registrado en el panel de Applied Steps.

**Paso 4:** Definir el destino. Desde el menú Add data destination seleccionás Lakehouse. Elegís el workspace, el Lakehouse destino, y el nombre de la tabla. Configurás si querés que cada ejecución reemplace la tabla completa o agregue los nuevos registros (replace o append). Guardás.

**Paso 5:** Publicar y programar. Publicás el dataflow. Desde las opciones del workspace configurás el scheduled refresh: todos los días a las 6 AM, por ejemplo. Fabric ejecuta el dataflow, transforma los datos y los escribe en el Lakehouse automáticamente.

A partir de ese momento, la tabla está disponible en tu Lakehouse, en formato Delta, lista para ser consultada con SQL, leída con Spark, o consumida desde Power BI en modo Direct Lake sin necesidad de importar nada.

## Por dónde empezar hoy

Si querés poner en práctica lo que leíste, el punto de entrada más rápido es el lab oficial de Microsoft Learn: "Create and use Dataflows Gen2 in Microsoft Fabric". Está disponible de forma gratuita, y usa el trial gratuito de Fabric. No necesitás tener nada configurado previamente.

El flujo del lab es exactamente el que describí antes: conectar una fuente OData, aplicar transformaciones básicas, configurar el destino en un Lakehouse, y luego usar el Dataflow como actividad dentro de una Pipeline. Es el circuito completo en un entorno controlado.

## Lo que viene: Pipelines y orquestación

En el próximo artículo vamos a ver cómo usar los Dataflows dentro de Pipelines para construir flujos de datos completos y automatizados. Si Dataflows Gen2 es la pieza que limpia y transforma, las Pipelines son las que coordinan cuándo, en qué orden, y qué pasa si algo falla.

Es el siguiente paso natural en la curva de aprendizaje de Fabric, y una vez que entendés cómo encajan las dos piezas, el resto de la plataforma empieza a cobrar mucho más sentido. 🧉

---

*¿Ya usaste Dataflows Gen2 o estás evaluando cómo arrancar con la ingesta en Fabric? Escribime en [LinkedIn](https://www.linkedin.com/in/jimenacambronero/) y charlamos. Me encanta hablar de estos temas — especialmente de los workarounds que descubrís cuando algo no funciona como esperabas.*
