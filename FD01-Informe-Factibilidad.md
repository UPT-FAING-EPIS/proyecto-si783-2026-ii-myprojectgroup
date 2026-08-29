<center>

![Logo de la Universidad Privada de Tacna](./media/logo-upt.png)

**UNIVERSIDAD PRIVADA DE TACNA**

**FACULTAD DE INGENIERIA**

**Escuela Profesional de Ingeniería de Sistemas**

**Proyecto *SchemaSafe NoSQL – Sistema Multimotor de Análisis Preventivo de Cambios de Esquema***

Curso: *BASE DE DATOS II*

Docente: *PATRICK JOSE CUADROS QUIROGA*

Integrantes:

***Gabriela Estefania Cohaila Alvarado (2022075746)***  
***Victoria Isabel Lavarello Vidaurre (2000000000)***

**Tacna – Perú**

***2026***

</center>

<div style="page-break-after: always; visibility: hidden">\pagebreak</div>

<center>

**Sistema *SchemaSafe NoSQL***

**Informe de Factibilidad**

**Versión *{1.0}***

</center>

| CONTROL DE VERSIONES |  |  |  |  |  |
| :-: | :- | :- | :- | :- | :- |
| **Versión** | **Hecha por** | **Revisada por** | **Aprobada por** | **Fecha** | **Motivo** |
| 1.0 | GC Y VL | PJCQ | PJCQ | 28/08/2026 | Versión Original |
| 1.1 | GC | PJCQ | PJCQ | 29/08/2026 | Versión Original |

<div style="page-break-after: always; visibility: hidden">\pagebreak</div>

# **INDICE GENERAL**

[1. Descripción del Proyecto](#_Toc52661346)

[2. Riesgos](#_Toc52661347)

[3. Análisis de la Situación actual](#_Toc52661348)

[4. Estudio de Factibilidad](#_Toc52661349)

[4.1. Factibilidad Técnica](#_Toc52661350)

[4.2. Factibilidad Económica](#_Toc52661351)

[4.3. Factibilidad Operativa](#_Toc52661352)

[4.4. Factibilidad Legal](#_Toc52661353)

[4.5. Factibilidad Social](#_Toc52661354)

[4.6. Factibilidad Ambiental](#_Toc52661355)

[5. Análisis Financiero](#_Toc52661356)

[6. Conclusiones](#_Toc52661357)

<div style="page-break-after: always; visibility: hidden">\pagebreak</div>

# **<u>Informe de Factibilidad</u>**

## 1. <span id="_Toc52661346" class="anchor"></span>Descripción del Proyecto

### 1.1. Nombre del proyecto

Sistema multimotor para el análisis preventivo de riesgos, impacto y compatibilidad de cambios estructurales en bases de datos NoSQL antes de su despliegue.

Nombre referencial del sistema: SchemaSafe NoSQL – Sistema Multimotor de Análisis Preventivo de Cambios de Esquema.

### 1.2. Duración del proyecto

La duración del proyecto comprenderá el periodo académico establecido para el desarrollo del proyecto correspondiente al curso de Base de Datos II.

Duración específica: 3 meses

Durante este periodo se contemplarán progresivamente las actividades de análisis del problema, definición de requerimientos, diseño de la solución, implementación de los adaptadores para los gestores NoSQL seleccionados, desarrollo del motor de análisis preventivo, ejecución de pruebas controladas y elaboración de la documentación técnica correspondiente.

### 1.3. Descripción

*El proyecto propone el desarrollo de* ***SchemaSafe NoSQL****, una herramienta multimotor orientada al análisis preventivo de cambios estructurales en bases de datos NoSQL antes de que dichos cambios sean aplicados sobre la información almacenada. La finalidad del sistema es proporcionar al desarrollador o administrador de bases de datos información anticipada acerca de las posibles incompatibilidades, cantidad de datos afectados, porcentaje de impacto y nivel de riesgo asociado con una modificación propuesta.*
*La necesidad del proyecto surge debido a que la flexibilidad característica de las bases de datos NoSQL permite que las estructuras de información evolucionen con mayor libertad, pero no elimina los riesgos asociados con dichos cambios. Durante el mantenimiento de una aplicación pueden presentarse modificaciones como la incorporación o eliminación de atributos, renombramiento de campos, cambios de tipos de datos, establecimiento de atributos obligatorios, transformación de estructuras simples en estructuras anidadas, modificaciones de índices, incorporación de reglas de validación o cambios sobre restricciones particulares de cada gestor.*
*Estas modificaciones pueden generar incompatibilidades con información almacenada previamente. Por ejemplo, un mismo atributo puede aparecer como valor numérico en determinados documentos, como texto en otros o estar ausente en parte de los registros. Si posteriormente se establece una nueva estructura que exige un único tipo de dato o convierte dicho atributo en obligatorio, los datos existentes podrían no cumplir con la nueva condición. Situaciones similares pueden producirse al eliminar o renombrar atributos utilizados por una aplicación o al modificar estructuras consultadas por versiones anteriores del software.*
*La problemática adquiere mayor complejidad cuando se consideran distintos gestores NoSQL, debido a que no todos administran de la misma forma sus estructuras, validaciones, índices y restricciones. Por esta razón, SchemaSafe NoSQL trabajará inicialmente con tres tecnologías diferentes:* ***MongoDB****,* ***Apache CouchDB*** *y* ***Apache Cassandra****. MongoDB y CouchDB emplean modelos orientados a documentos, mientras que Cassandra trabaja mediante un modelo distribuido organizado en tablas y columnas. Estas diferencias hacen inviable asumir que una misma regla estructural puede aplicarse exactamente de la misma manera sobre todos los gestores.*
*Para afrontar esta heterogeneidad, el proyecto plantea una* ***arquitectura basada en adaptadores****. Cada gestor dispondrá de un adaptador responsable de conectarse a la base correspondiente, obtener la información estructural necesaria e interpretarla de acuerdo con las características propias del motor. Posteriormente, dicha información será transformada a un* ***modelo intermedio común****, permitiendo que el núcleo de SchemaSafe aplique reglas generales de análisis sin depender directamente de la implementación particular de MongoDB, CouchDB o Cassandra.*
*El funcionamiento de SchemaSafe se desarrollará principalmente bajo un enfoque preventivo o* ***dry-run****. El usuario podrá seleccionar una base de datos y la estructura que desea evaluar y, posteriormente, definir una modificación propuesta. El sistema analizará el estado actual de la información sin aplicar automáticamente el cambio sobre la base de datos. De esta manera, la evaluación permitirá conocer las posibles consecuencias antes de realizar una migración o modificación real.*
*Para cada análisis, SchemaSafe determinará la cantidad de elementos evaluados, los elementos potencialmente afectados y el porcentaje de impacto correspondiente. Asimismo, las modificaciones serán evaluadas mediante reglas previamente definidas y clasificadas dentro de los niveles de riesgo* ***BAJO, MEDIO, ALTO o CRÍTICO****.*
*La clasificación no se limitará a proporcionar un nivel numérico o categórico, sino que deberá ser explicable. El sistema presentará las causas que originaron la clasificación y generará recomendaciones preventivas que ayuden al usuario a decidir si debe corregir información existente, preparar un proceso de migración, realizar un respaldo o reconsiderar la modificación propuesta.*
*A partir de los resultados obtenidos, cada evaluación será clasificada de manera general como:*

- ***APROBADO:*** *no se identifican riesgos importantes que impidan continuar con el cambio propuesto.*
- ***REQUIERE REVISIÓN:*** *existen incompatibilidades o advertencias que deben evaluarse antes de realizar la modificación.*
- ***NO RECOMENDADO:*** *se identifican condiciones de riesgo alto o crítico que podrían afectar significativamente los datos existentes.*

*Cada evaluación generará un reporte que contendrá, como mínimo, información sobre el usuario que realizó el análisis, fecha y hora, gestor NoSQL empleado, base de datos y estructura analizadas, cambio propuesto, cantidad de elementos evaluados, cantidad de elementos potencialmente afectados, porcentaje de impacto, nivel de riesgo, causas identificadas, recomendaciones preventivas y resultado general.*
*El sistema mantendrá además un historial de evaluaciones realizadas. Esto permitirá consultar resultados anteriores y, cuando corresponda, comparar análisis efectuados sobre una misma estructura. De este modo, después de corregir datos incompatibles o realizar acciones preventivas, podrá ejecutarse nuevamente la evaluación para determinar si el nivel de riesgo o porcentaje de impacto se ha reducido.*
*Como medida complementaria de control, SchemaSafe contemplará usuarios y roles para diferenciar las funcionalidades administrativas de aquellas relacionadas directamente con el análisis. También mantendrá registros de auditoría asociados con las evaluaciones realizadas, facilitando la identificación del usuario, fecha, motor utilizado, estructura evaluada, modificación propuesta y resultado obtenido.*
*La validación del proyecto se desarrollará sobre bases de datos y conjuntos de información preparados específicamente para las pruebas, evitando utilizar información proveniente de ambientes productivos. Se ejecutarán como mínimo* ***50 escenarios controlados*** *distribuidos entre MongoDB, Apache CouchDB y Apache Cassandra, incluyendo al menos* ***10 escenarios considerados de riesgo alto o crítico****. Dentro de estos últimos se verificará que SchemaSafe identifique correctamente el* ***100 % de los casos críticos definidos previamente para las pruebas controladas****.*
*El producto obtenido tendrá carácter de prototipo funcional y no buscará reemplazar herramientas completas de migración ni ejecutar automáticamente transformaciones sobre los datos. Su aporte se concentrará en proporcionar una* ***capa preventiva, multimotor, no destructiva y explicable****, capaz de evaluar determinados cambios estructurales antes de que estos sean aplicados.*
*Finalmente, la arquitectura basada en adaptadores permitirá que el diseño pueda ampliarse posteriormente mediante la incorporación de nuevos gestores NoSQL, sin requerir la reconstrucción completa del núcleo de análisis.*
### 1.4. Objetivos

#### 1.4.1. Objetivo general

Desarrollar y validar una herramienta multimotor que permita analizar preventivamente cambios estructurales sobre diferentes bases de datos NoSQL antes de su aplicación, identificando incompatibilidades, datos potencialmente afectados, porcentaje de impacto y nivel de riesgo asociado con cada modificación.
#### 1.4.2. Objetivos específicos

1. Implementar una arquitectura modular basada en adaptadores para MongoDB, Apache CouchDB y Apache Cassandra, con la finalidad de aislar las características particulares de cada gestor y entregar al núcleo del sistema información estructural mediante un modelo intermedio común.
2. Obtener y analizar automáticamente información relevante de las estructuras almacenadas, considerando atributos, tipos de datos, estructuras anidadas, índices, validaciones y restricciones cuando estas características estén disponibles en el gestor evaluado.
3. Implementar como mínimo quince reglas de evaluación preventiva que permitan identificar modificaciones potencialmente incompatibles o peligrosas sobre los datos existentes.
4. Analizar al menos diez tipos de modificaciones estructurales, incluyendo cambios de tipos de datos, eliminación y renombramiento de atributos, establecimiento de obligatoriedad, modificación de estructuras anidadas y cambios sobre índices, validaciones o restricciones cuando sean soportados.
5. Calcular cuantitativamente el impacto de los cambios propuestos, determinando la cantidad total de elementos analizados, la cantidad de elementos potencialmente afectados y el porcentaje de impacto correspondiente.
6. Clasificar automáticamente las modificaciones evaluadas utilizando los niveles de riesgo BAJO, MEDIO, ALTO o CRÍTICO, mostrando las causas que justifican cada clasificación y generando un resultado general de APROBADO, REQUIERE REVISIÓN o NO RECOMENDADO.
7. Mantener las evaluaciones bajo un enfoque preventivo o dry-run, evitando que SchemaSafe ejecute automáticamente modificaciones destructivas sobre las bases de datos utilizadas durante el análisis.
8. Generar reportes e historial de análisis que permitan conservar información sobre la estructura evaluada, modificación propuesta, impacto calculado, riesgo detectado, causas, recomendaciones y resultado obtenido.
9. Implementar mecanismos básicos de usuarios, roles y auditoría, con el propósito de controlar las funciones administrativas y mantener trazabilidad sobre las evaluaciones realizadas.
10. Validar el funcionamiento de SchemaSafe mediante al menos 50 escenarios controlados, distribuidos entre los tres gestores implementados e incluyendo como mínimo 10 escenarios de riesgo alto o crítico previamente definidos.
11. Comprobar que el sistema identifique correctamente el 100 % de los escenarios críticos previamente establecidos para la validación controlada, utilizando evidencias reproducibles de los resultados obtenidos.
12. Verificar la extensibilidad de la arquitectura basada en adaptadores, demostrando que el núcleo de análisis permanece desacoplado de las particularidades de los gestores y puede admitir posteriormente nuevos motores NoSQL mediante nuevos adaptadores.

<div style="page-break-after: always; visibility: hidden">\pagebreak</div>

## 2. <span id="_Toc52661347" class="anchor"></span>Riesgos

*El desarrollo de SchemaSafe NoSQL involucra diferentes riesgos técnicos y de gestión derivados principalmente de la heterogeneidad existente entre los motores NoSQL seleccionados, el alcance del análisis propuesto y la necesidad de proporcionar resultados confiables sin modificar las bases de datos evaluadas.*
*La identificación temprana de estos riesgos permite establecer medidas preventivas que reduzcan su probabilidad de ocurrencia o disminuyan sus consecuencias sobre los objetivos del proyecto.*

| ***ID*** | ***Riesgo identificado***                                                                                                                    | ***Probabilidad*** | ***Impacto*** | ***Nivel***   | ***Medida preventiva***                                                                                                                                                                                                                      |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ | ------------- | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *R-01*   | *Las diferencias entre MongoDB, CouchDB y Cassandra pueden impedir representar determinadas características mediante un único modelo común.* | *Alta*             | *Alto*        | ***Alto***    | *Diseñar un modelo intermedio extensible que contenga características comunes y permita incorporar metadatos específicos de cada gestor.*                                                                                                    |
| *R-02*   | *Intentar aplicar exactamente las mismas reglas de análisis a todos los gestores puede generar resultados incorrectos.*                      | *Alta*             | *Alto*        | ***Alto***    | *Diferenciar reglas generales de reglas específicas y asignar cada evaluación únicamente a los gestores que soporten la característica analizada.*                                                                                           |
| *R-03*   | *Una regla de evaluación incorrectamente definida podría clasificar un cambio peligroso con un nivel de riesgo inferior al correspondiente.* | *Media*            | *Muy alto*    | ***Crítico*** | *Definir escenarios previamente clasificados y validar especialmente los casos de riesgo alto y crítico mediante pruebas controladas.*                                                                                                       |
| *R-04*   | *El sistema podría producir falsos positivos y considerar peligrosa una modificación cuyo impacto real sea reducido.*                        | *Media*            | *Medio*       | ***Medio***   | *Mostrar causas, métricas e información utilizada para calcular el riesgo, permitiendo que el resultado pueda ser interpretado por el usuario.*                                                                                              |
| *R-05*   | *Una falla en la implementación del modo dry-run podría permitir modificaciones accidentales sobre la información evaluada.*                 | *Baja*             | *Muy alto*    | ***Alto***    | *Utilizar conexiones con los permisos mínimos posibles y verificar mediante pruebas que las evaluaciones no alteren los datos originales.*                                                                                                   |
| *R-06*   | *El análisis de grandes volúmenes de documentos o registros puede incrementar significativamente el tiempo de evaluación.*                   | *Media*            | *Alto*        | ***Alto***    | *Diseñar los procesos de inspección de manera controlada y medir posteriormente su comportamiento mediante escenarios con distintos volúmenes de información.*                                                                               |
| *R-07*   | *El número de funcionalidades propuestas podría superar el tiempo disponible dentro del periodo académico.*                                  | *Media*            | *Alto*        | ***Alto***    | *Priorizar el núcleo funcional: conexión, extracción estructural, adaptadores, modelo intermedio, reglas, cálculo de impacto, riesgo y reportes. Las funcionalidades complementarias deberán desarrollarse sin comprometer las principales.* |
| *R-08*   | *Los datos utilizados en pruebas podrían no representar adecuadamente las incompatibilidades que se presentan en estructuras NoSQL.*         | *Media*            | *Alto*        | ***Alto***    | *Diseñar conjuntos de datos heterogéneos con tipos incompatibles, atributos ausentes, valores nulos, estructuras anidadas y modificaciones propias de cada gestor.*                                                                          |
| *R-09*   | *Una modificación particular de un gestor podría ser interpretada incorrectamente por el adaptador correspondiente.*                         | *Media*            | *Alto*        | ***Alto***    | *Desarrollar y probar cada adaptador de manera independiente antes de validar el funcionamiento conjunto mediante el modelo intermedio.*                                                                                                     |
| *R-10*   | *Una falla en la normalización de información entregada por los adaptadores puede ocasionar evaluaciones inconsistentes entre motores.*      | *Media*            | *Muy alto*    | ***Alto***    | *Definir contratos claros para la información que cada adaptador deberá entregar al núcleo y crear pruebas específicas sobre dicha normalización.*                                                                                           |
| *R-11*   | *El almacenamiento de credenciales de conexión podría representar un riesgo de seguridad durante el desarrollo.*                             | *Media*            | *Alto*        | ***Alto***    | *Evitar incorporar credenciales reales dentro del código fuente y trabajar únicamente con entornos controlados y configuraciones externas al repositorio.*                                                                                   |
| *R-12*   | *La indisponibilidad o configuración incorrecta de alguno de los tres gestores podría retrasar las pruebas.*                                 | *Media*            | *Medio*       | ***Medio***   | *Preparar ambientes controlados independientes y verificar la conexión de cada gestor antes de ejecutar los escenarios de prueba.*                                                                                                           |
| *R-13*   | *La arquitectura podría quedar demasiado acoplada a los tres gestores iniciales, dificultando su extensión futura.*                          | *Media*            | *Medio*       | ***Medio***   | *Mantener la lógica específica dentro de adaptadores y evitar dependencias directas entre el motor central de análisis y las APIs particulares de cada gestor.*                                                                              |
| *R-14*   | *La cantidad de escenarios de prueba podría ser insuficiente para verificar determinadas reglas o características particulares.*             | *Baja*             | *Alto*        | ***Medio***   | *Distribuir los 50 escenarios considerando los tres gestores, las reglas implementadas y los diferentes niveles de riesgo.*                                                                                                                  |
| *R-15*   | *Las métricas de impacto podrían interpretarse incorrectamente si se basan únicamente en porcentaje de elementos afectados.*                 | *Media*            | *Alto*        | ***Alto***    | *Utilizar el porcentaje de impacto como uno de varios criterios de evaluación, considerando además el tipo de modificación, incompatibilidad detectada y características propias del gestor.*                                                |

*Los riesgos que requieren mayor atención corresponden a la* ***clasificación incorrecta de modificaciones peligrosas****, las posibles diferencias no representables entre los tres gestores y cualquier posibilidad de modificación accidental de la información durante las evaluaciones. Debido a que SchemaSafe tendrá una finalidad preventiva, la confiabilidad de sus resultados y la conservación de los datos analizados constituyen aspectos prioritarios durante el desarrollo y validación del proyecto.*

<div style="page-break-after: always; visibility: hidden">\pagebreak</div>

## 3. <span id="_Toc52661348" class="anchor"></span>Análisis de la Situación actual

### 3.1. Planteamiento del problema

*Las bases de datos NoSQL constituyen una alternativa ampliamente utilizada en aplicaciones que requieren flexibilidad para representar información con estructuras variables y trabajar con diferentes modelos de almacenamiento. Sin embargo, la capacidad de modificar con mayor libertad la estructura de los datos no significa que dichas modificaciones sean inocuas para la información previamente almacenada.*
*Durante la evolución de un sistema pueden surgir nuevas necesidades que requieran agregar atributos, eliminar campos, modificar tipos de datos, renombrar propiedades, transformar estructuras simples en estructuras anidadas, incorporar nuevas validaciones, modificar índices o aplicar restricciones adicionales. Cuando una base de datos ya contiene información, estos cambios pueden entrar en conflicto con los datos existentes o con aplicaciones que todavía dependen de una estructura anterior.*
*Una de las principales dificultades se presenta cuando la información almacenada no posee una estructura completamente homogénea. Un atributo puede existir en determinados registros y encontrarse ausente en otros; puede contener valores nulos o utilizar diferentes tipos de datos dependiendo del momento en que fue almacenado. Como consecuencia, una modificación aparentemente sencilla puede afectar una cantidad significativa de elementos.*
*Por ejemplo, si un atributo denominado* *`edad`* *se encuentra almacenado como valor numérico en la mayoría de los documentos, pero una nueva versión de la aplicación pretende establecerlo como texto, será necesario conocer previamente qué cantidad de información utiliza la representación anterior. De forma equivalente, antes de establecer un atributo como obligatorio resulta necesario determinar cuántos elementos actualmente no contienen dicho atributo.*
*El problema no se limita a los modelos documentales. Las tecnologías NoSQL poseen diferencias importantes entre sí. MongoDB permite trabajar con colecciones de documentos y mecanismos de validación; CouchDB utiliza documentos JSON y dispone de mecanismos propios para gestionar y validar modificaciones; mientras que Cassandra organiza la información mediante tablas, columnas y tipos manejados a través de su propio modelo.*
*Estas diferencias provocan que el procedimiento requerido para analizar un cambio dependa habitualmente del gestor utilizado. Una comprobación válida para MongoDB no necesariamente puede ejecutarse de la misma forma sobre Cassandra y determinadas propiedades pueden no tener un equivalente directo entre motores.*
*Actualmente, la evaluación previa de estas modificaciones puede depender de consultas construidas específicamente para cada gestor, herramientas particulares del motor y verificaciones manuales realizadas por desarrolladores o administradores. Esto dificulta disponer de un procedimiento uniforme que permita responder, antes de ejecutar una modificación, preguntas como:*

- *¿Qué cantidad de información existente sería afectada?*
- *¿Qué porcentaje del conjunto analizado resultaría incompatible?*
- *¿Existen atributos ausentes o valores nulos?*
- *¿Se utilizan diferentes tipos de datos para representar un mismo atributo?*
- *¿La característica que se pretende modificar existe o es compatible con el gestor evaluado?*
- *¿La modificación debería considerarse de riesgo bajo, medio, alto o crítico?*
- *¿Es recomendable realizar el cambio directamente o deberían ejecutarse acciones preventivas?*

*Cuando estas comprobaciones no se realizan oportunamente, las incompatibilidades pueden descubrirse durante una migración, durante las pruebas de una nueva versión o después de haber aplicado el cambio estructural. En esos escenarios puede ser necesario corregir información, desarrollar transformaciones adicionales, modificar consultas, restaurar datos desde respaldos o revertir parcialmente la modificación realizada.*
*A ello se suma la ausencia de una representación común entre gestores. Aunque MongoDB, CouchDB y Cassandra pertenecen al conjunto general de tecnologías NoSQL, poseen diferencias suficientemente importantes como para impedir que el análisis pueda basarse únicamente en una implementación específica.*
*El* ***problema central*** *identificado consiste, por tanto, en la dificultad para evaluar de manera preventiva, uniforme y explicable el impacto que determinados cambios estructurales pueden producir sobre información existente cuando se trabaja con diferentes tecnologías NoSQL.*
*Esta situación genera la necesidad de una herramienta que actúe antes de la ejecución real del cambio, obtenga información sobre el estado actual de los datos, cuantifique los elementos potencialmente afectados, identifique incompatibilidades y proporcione una clasificación de riesgo suficientemente explicable para apoyar la toma de decisiones del desarrollador o administrador.*
*Frente a esta problemática se plantea* ***SchemaSafe NoSQL****, cuyo propósito es incorporar una capa de análisis preventivo entre la estructura actual de una base de datos y la modificación que se pretende realizar. La herramienta no ejecutará automáticamente la transformación propuesta, sino que evaluará sus posibles consecuencias mediante un procedimiento dry-run.*
*Mediante adaptadores específicos para MongoDB, Apache CouchDB y Apache Cassandra, el sistema obtendrá la información necesaria de cada tecnología y la transformará hacia un modelo intermedio. Sobre dicha representación, el motor central podrá aplicar reglas generales o específicas, calcular métricas de impacto y generar un resultado que facilite determinar si la modificación puede continuar, requiere revisión o no resulta recomendable.*
*En consecuencia, SchemaSafe NoSQL busca reducir la incertidumbre existente antes de efectuar modificaciones estructurales y proporcionar evidencia previa que permita adoptar medidas como normalizar información, preparar una migración, realizar respaldos o reconsiderar un cambio antes de afectar una base de datos.*
***Figura 01*** *Proceso actual de evaluación y aplicación de cambios estructurales en bases de datos NoSQL*
<p align="center">
  <img src="./media/NoSQL Structure-2026-08-29-103902.png" alt="Proceso actual de evaluación y aplicación de cambios estructurales en bases de datos NoSQL" width="430" />
</p>

***Nota:*** *La figura representa el procedimiento general identificado antes de disponer de SchemaSafe NoSQL, donde la evaluación depende de revisiones manuales y herramientas particulares de cada gestor. Esta situación puede ocasionar que determinadas incompatibilidades sean identificadas recién durante las pruebas o después de ejecutar el cambio estructural.*

### 3.2. Consideraciones de hardware y software

*Para el desarrollo de SchemaSafe NoSQL será necesario disponer de un entorno capaz de ejecutar de manera controlada los tres gestores seleccionados, el sistema de análisis y los conjuntos de datos utilizados durante las pruebas.*
*Debido a que el proyecto se desarrollará utilizando bases de datos preparadas específicamente para escenarios académicos y no sobre sistemas productivos, no será necesario disponer inicialmente de infraestructura empresarial dedicada. No obstante, el entorno deberá permitir ejecutar independientemente MongoDB, Apache CouchDB y Apache Cassandra y realizar conexiones desde el sistema SchemaSafe.*

### *Hardware*

| ***Recurso***           | ***Finalidad dentro del proyecto***                                                                                               | ***Situación***                                                                                            |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| *Equipo de desarrollo*  | *Implementación, ejecución de SchemaSafe, elaboración de documentación y ejecución de pruebas.*                                   | *Disponible por el equipo de desarrollo. Las especificaciones exactas deberán registrarse posteriormente.* |
| *Almacenamiento local*  | *Conservación de código fuente, configuraciones, bases de prueba, reportes y evidencias.*                                         | *Requerido.*                                                                                               |
| *Memoria RAM*           | *Ejecución simultánea o independiente de los gestores NoSQL y del entorno de desarrollo.*                                         | *Requerido. La capacidad exacta se determinará según el ambiente definitivo.*                              |
| *Conexión de red local* | *Comunicación entre SchemaSafe y las instancias de bases de datos cuando se ejecuten en servicios o contenedores independientes.* | *Requerido.*                                                                                               |
| *Acceso a Internet*     | *Descarga inicial de dependencias, gestores, documentación y herramientas necesarias para el desarrollo.*                         | *Requerido principalmente durante configuración y mantenimiento del entorno.*                              |

*Las características exactas de procesador, memoria RAM y almacenamiento del equipo utilizado deberán incorporarse cuando se documente el inventario definitivo del entorno, evitando establecer requisitos que no hayan sido comprobados experimentalmente.*

### *Software*

| ***Software / tecnología***                    | ***Función***                                                                                                                                                                                 |
| ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***MongoDB***                                  | *Gestor documental utilizado como uno de los motores sobre los cuales SchemaSafe realizará evaluaciones preventivas.*                                                                         |
| ***Apache CouchDB***                           | *Gestor documental utilizado para comprobar la capacidad multimotor y analizar características particulares de documentos JSON y sus mecanismos propios.*                                     |
| ***Apache Cassandra***                         | *Gestor NoSQL orientado a columnas utilizado para evaluar la capacidad de SchemaSafe de trabajar con un modelo estructural diferente a los gestores documentales.*                            |
| ***Sistema operativo de desarrollo***          | *Plataforma sobre la que se ejecutarán las herramientas de desarrollo y los entornos de prueba. Su especificación definitiva deberá registrarse de acuerdo con el equipo utilizado.*          |
| ***Entorno de desarrollo / editor de código*** | *Desarrollo y mantenimiento del código fuente de SchemaSafe. La herramienta concreta deberá registrarse cuando sea seleccionada definitivamente.*                                             |
| ***Sistema de control de versiones Git***      | *Gestión de versiones del código fuente y documentación durante el desarrollo.*                                                                                                               |
| ***GitHub***                                   | *Repositorio remoto para conservar el proyecto, documentación, control de cambios y evidencias asociadas.*                                                                                    |
| ***Controladores o bibliotecas de conexión***  | *Comunicación entre SchemaSafe y MongoDB, CouchDB y Cassandra. Las bibliotecas concretas dependerán de la tecnología de implementación seleccionada.*                                         |
| ***Herramientas de pruebas***                  | *Automatización y ejecución de las pruebas unitarias, integración y demás evaluaciones previstas. Las herramientas específicas deberán definirse de acuerdo con la tecnología de desarrollo.* |

*Una consideración importante para el proyecto será mantener separados los ambientes utilizados por cada gestor y trabajar exclusivamente con* ***bases de datos controladas****, preparadas para los escenarios de evaluación. Esto permitirá generar deliberadamente condiciones como valores nulos, atributos ausentes, tipos incompatibles, estructuras heterogéneas y otras situaciones necesarias para validar las reglas de SchemaSafe sin comprometer información real.*
*Asimismo, las conexiones utilizadas durante las evaluaciones deberán configurarse, siempre que sea técnicamente posible, con los permisos mínimos necesarios para consultar la información. Esta medida contribuirá a reforzar el principio de funcionamiento no destructivo del sistema y reducirá la posibilidad de que una falla durante la implementación provoque modificaciones accidentales.*
*En esta etapa todavía no se ha definido en el documento base la tecnología específica con la que se implementará el núcleo de SchemaSafe, por lo que no resulta metodológicamente correcto establecer todavía un lenguaje de programación, framework, controlador o herramienta de pruebas como tecnología definitiva. Estas decisiones deberán justificarse posteriormente de acuerdo con los requerimientos técnicos, compatibilidad con los tres gestores y arquitectura seleccionada.*

<div style="page-break-after: always; visibility: hidden">\pagebreak</div>

## 4. <span id="_Toc52661349" class="anchor"></span>Estudio de Factibilidad

*El estudio de factibilidad tiene como finalidad determinar si el desarrollo de* ***SchemaSafe NoSQL – Sistema Multimotor de Análisis Preventivo de Cambios de Esquema*** *puede llevarse a cabo considerando los recursos tecnológicos necesarios, los costos asociados, su posibilidad de operación, las condiciones legales, su aporte social y su impacto ambiental.*
*El proyecto plantea un prototipo funcional capaz de conectarse inicialmente con* ***MongoDB, Apache CouchDB y Apache Cassandra****, obtener información relevante de las estructuras y los datos existentes, normalizarla mediante adaptadores y analizar cambios propuestos antes de que sean aplicados. Asimismo, el sistema deberá calcular elementos potencialmente afectados, porcentaje de impacto, nivel de riesgo y recomendaciones, manteniendo las evaluaciones en modo preventivo o dry-run.*
*A partir de las características definidas para el proyecto, se evalúa su factibilidad en seis dimensiones.*

### 4.1. <span id="_Toc52661350" class="anchor"></span>Factibilidad Técnica

*La factibilidad técnica permite determinar si existen los recursos tecnológicos y conocimientos necesarios para implementar las funcionalidades propuestas para SchemaSafe NoSQL.*
*Desde el punto de vista tecnológico, el proyecto es viable debido a que su funcionamiento puede estructurarse mediante una arquitectura modular. En lugar de desarrollar un núcleo completamente diferente para cada tecnología NoSQL, SchemaSafe utilizará un* ***modelo intermedio común*** *y un conjunto de* ***adaptadores especializados*** *para MongoDB, Apache CouchDB y Apache Cassandra. Cada adaptador será responsable de obtener e interpretar la información correspondiente a su gestor y transformarla a una representación que pueda ser procesada por el motor central.*
*Esta separación representa una ventaja técnica importante porque reduce el acoplamiento entre el núcleo de análisis y las particularidades de cada motor. Al mismo tiempo, permite que determinadas reglas sean generales y otras se implementen únicamente para aquellos gestores que soporten las características evaluadas.*
*La propuesta contempla técnicamente los siguientes componentes principales:*

| ***Componente***               | ***Función dentro de SchemaSafe***                                                                                                 |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| *Adaptador MongoDB*            | *Obtener e interpretar estructuras, atributos, tipos, índices, validaciones y demás características disponibles en MongoDB.*       |
| *Adaptador Apache CouchDB*     | *Analizar las características disponibles sobre documentos JSON y mecanismos propios del gestor.*                                  |
| *Adaptador Apache Cassandra*   | *Obtener información correspondiente a tablas, columnas, tipos y demás estructuras administradas mediante el modelo de Cassandra.* |
| *Modelo intermedio común*      | *Normalizar la información obtenida desde los diferentes gestores.*                                                                |
| *Motor de análisis*            | *Ejecutar reglas preventivas y analizar el cambio estructural propuesto.*                                                          |
| *Motor de riesgo*              | *Determinar los niveles BAJO, MEDIO, ALTO o CRÍTICO.*                                                                              |
| *Cálculo de impacto*           | *Determinar cantidad y porcentaje de elementos potencialmente afectados.*                                                          |
| *Generador de recomendaciones* | *Explicar los problemas identificados y posibles medidas preventivas.*                                                             |
| *Reportes e historial*         | *Conservar los resultados obtenidos en cada análisis.*                                                                             |
| *Usuarios y roles*             | *Controlar el acceso a las funciones administrativas y de análisis.*                                                               |
| *Auditoría*                    | *Registrar usuario, fecha, motor, estructura, modificación y resultado de la evaluación.*                                          |

*El proyecto también resulta técnicamente factible por su enfoque* ***no destructivo****. SchemaSafe no tiene como finalidad ejecutar automáticamente los cambios propuestos, sino analizarlos antes de su aplicación. Además, se plantea que, siempre que sea posible, las conexiones utilizadas dispongan únicamente de los permisos necesarios para consultar la información.*
*La estrategia de validación también fortalece su factibilidad técnica. El proyecto establece la realización de al menos* ***50 escenarios controlados****, distribuidos entre los tres gestores, incluyendo un mínimo de* ***10 escenarios de riesgo alto o crítico*** *y la detección correcta del* ***100 % de los escenarios críticos previamente definidos*** *para dichas pruebas. Asimismo, deberá comprobarse que SchemaSafe no modifique las bases de datos durante los análisis y que los adaptadores entreguen información normalizada al núcleo.*
*No obstante, existe una dificultad técnica relevante: MongoDB, CouchDB y Cassandra no poseen exactamente las mismas características estructurales. Por esta razón, el proyecto no debe asumir que cada regla será universal. La solución planteada en el README diferencia las reglas comunes de aquellas específicas de cada motor, lo que permite manejar esta limitación sin invalidar la arquitectura general.*
*En consecuencia,* ***SchemaSafe NoSQL es técnicamente factible****, siempre que su implementación mantenga el desacoplamiento mediante adaptadores, delimite claramente qué reglas son compatibles con cada gestor y valide el motor de análisis mediante los escenarios controlados establecidos.*

### 4.2. <span id="_Toc52661351" class="anchor"></span>Factibilidad Económica

*La factibilidad económica permite determinar si los recursos requeridos para desarrollar el proyecto resultan razonables respecto de los beneficios que se espera obtener.*
*SchemaSafe se desarrolla inicialmente como un* ***proyecto académico y prototipo funcional****, por lo que no contempla en esta etapa la adquisición de infraestructura empresarial ni el procesamiento de bases de datos productivas. Las pruebas utilizarán bases de datos controladas y conjuntos de información generados específicamente para el proyecto.*
*El documento base no establece todavía los precios de los equipos, horas de desarrollo, consumo eléctrico, conexión a Internet u otros costos. Por ello, dichos conceptos deben valorizarse de manera explícita antes de cerrar el análisis financiero.*

#### 4.2.1. Costos Generales

*Los costos generales corresponden a los recursos complementarios necesarios durante el desarrollo y documentación del proyecto.*

| ***Concepto***                                      | ***Cantidad***  | ***Costo unitario*** | ***Costo total***   |
| --------------------------------------------------- | --------------- | -------------------- | ------------------- |
| *Materiales e impresiones para documentación final* | *[Por definir]* | *[Por definir]*      | *[Por definir]*     |
| *Conectividad a Internet atribuible al proyecto*    | *[Por definir]* | *[Por definir]*      | *[Por definir]*     |
| *Otros gastos generales comprobables*               | *[Por definir]* | *[Por definir]*      | *[Por definir]*     |
| ***Total costos generales***                        | <br>            | <br>                 | ***[Por definir]*** |

*No deben incorporarse gastos que no correspondan realmente al desarrollo. Si la documentación se entrega únicamente en formato digital, por ejemplo, el costo de impresión deberá considerarse como cero.*

#### 4.2.2. Costos operativos durante el desarrollo

*Dentro de los costos operativos pueden considerarse los recursos consumidos mientras se implementa y prueba SchemaSafe, principalmente energía eléctrica, conectividad y utilización de los equipos de desarrollo.*

| ***Concepto***                               | ***Periodo***   | ***Costo estimado*** |
| -------------------------------------------- | --------------- | -------------------- |
| *Energía eléctrica atribuible al desarrollo* | *[Por definir]* | *[Por definir]*      |
| *Internet y conectividad*                    | *[Por definir]* | *[Por definir]*      |
| *Otros costos operativos comprobables*       | *[Por definir]* | *[Por definir]*      |
| ***Total***                                  | <br>            | ***[Por definir]***  |

#### 4.2.3. Costos del ambiente

*El ambiente del proyecto estará compuesto por las herramientas necesarias para implementar y ejecutar SchemaSafe y por las instancias controladas de los tres gestores NoSQL.*
*El README no determina todavía si dichas instancias se ejecutarán localmente, mediante contenedores, máquinas virtuales o servicios externos, por lo que* ***no corresponde asignar aún un costo de infraestructura específico****.*

| ***Recurso***               | ***Uso***                                                    | ***Costo***                     |
| --------------------------- | ------------------------------------------------------------ | ------------------------------- |
| *MongoDB*                   | *Gestor NoSQL de pruebas*                                    | *[Según ambiente seleccionado]* |
| *Apache CouchDB*            | *Gestor NoSQL de pruebas*                                    | *[Según ambiente seleccionado]* |
| *Apache Cassandra*          | *Gestor NoSQL de pruebas*                                    | *[Según ambiente seleccionado]* |
| *Repositorio de código*     | *Control de versiones y documentación*                       | *[Según servicio seleccionado]* |
| *Infraestructura adicional* | *Servidor, máquina virtual o contenedor, si fuera necesario* | *[Por definir]*                 |
| ***Total del ambiente***    | <br>                                                         | ***[Por definir]***             |

#### 4.2.4. Costos de personal

*Aunque el proyecto sea desarrollado académicamente y no implique un pago real a sus integrantes, resulta recomendable considerar el* ***costo valorizado del trabajo humano****, debido a que representa uno de los principales recursos empleados en un proyecto de software.*
*El cálculo puede realizarse mediante:*
$$
Costo\ de\ personal = N.º\ integrantes \times Horas/semana \times N.º\ semanas \times Tarifa/hora
$$

| ***Rol***                  | ***Personas***  | ***Horas semanales*** | ***Semanas***   | ***Tarifa referencial*** | ***Total***         |
| -------------------------- | --------------- | --------------------- | --------------- | ------------------------ | ------------------- |
| *Analista / desarrollador* | *[Por definir]* | *[Por definir]*       | *[Por definir]* | *[Por definir]*          | *[Por definir]*     |
| ***Total de personal***    | <br>            | <br>                  | <br>            | <br>                     | ***[Por definir]*** |

*La tarifa utilizada deberá explicarse como una* ***valoración académica o costo de oportunidad****, evitando presentarla como un sueldo realmente pagado si no existe dicha contratación.*

#### 4.2.5. Costos totales del desarrollo del sistema

*Una vez definidos los valores anteriores, el costo total se determinará mediante:*
$$
CT = CG + CO + CA + CP
$$

*Donde:*

- ***CT:*** *costo total.*
- ***CG:*** *costos generales.*
- ***CO:*** *costos operativos.*
- *CA: costos del ambiente.*
- *CP: costos de personal.*

| *Categoría*                | *Costo*         |
| -------------------------- | --------------- |
| *Costos generales*         | *[Por definir]* |
| *Costos operativos*        | *[Por definir]* |
| *Costos del ambiente*      | *[Por definir]* |
| *Costos de personal*       | *[Por definir]* |
| *Costo total del proyecto* | *[Por definir]* |

*Por tanto, la factibilidad económica puede considerarse preliminarmente favorable, debido a que el proyecto no requiere en su planteamiento infraestructura empresarial ni tratamiento de datos productivos; no obstante, deberá confirmarse cuantitativamente una vez completado el presupuesto.*

### 4.3. <span id="_Toc52661352" class="anchor"></span>Factibilidad Operativa

*SchemaSafe NoSQL presenta factibilidad operativa debido a que responde a una actividad concreta dentro de la administración y evolución de bases de datos:* ***evaluar un cambio estructural antes de realizarlo****.*
*El flujo de operación planteado es comprensible para los usuarios técnicos. El desarrollador o administrador seleccionará el gestor y la base de datos correspondiente, identificará la estructura que desea analizar y definirá el cambio propuesto. SchemaSafe inspeccionará la información existente, ejecutará las reglas correspondientes y generará indicadores de impacto, nivel de riesgo, problemas encontrados y recomendaciones.*
*El resultado final podrá presentarse mediante tres estados:*

- ***APROBADO:*** *no se detectan riesgos importantes.*
- ***REQUIERE REVISIÓN:*** *existen incompatibilidades o advertencias que deberían verificarse.*
- ***NO RECOMENDADO:*** *se identifican modificaciones de riesgo alto o crítico que podrían afectar significativamente la información existente.*

*Esta simplificación facilita la interpretación del análisis y permite que los resultados no se limiten a información técnica difícil de comprender. El sistema deberá además explicar las causas del nivel asignado y proporcionar recomendaciones preventivas.*
*La operación también se apoya en un historial de evaluaciones. De esta manera, después de corregir datos o replantear una modificación, el usuario podrá ejecutar nuevamente el análisis y contrastar los resultados obtenidos.*
*Los principales actores operativos identificados son:*

| ***Actor***                      | ***Función***                                                              |
| -------------------------------- | -------------------------------------------------------------------------- |
| *Desarrollador*                  | *Analizar modificaciones estructurales antes de implementarlas.*           |
| *Administrador de base de datos* | *Revisar riesgos, incompatibilidades y posibles impactos sobre los datos.* |
| *Administrador de SchemaSafe*    | *Gestionar aspectos administrativos, usuarios y roles.*                    |
| *Usuario autorizado*             | *Consultar reportes e historial según los permisos asignados.*             |

*La existencia de usuarios, roles y auditoría también contribuye a la viabilidad operacional, ya que permitirá identificar quién ejecutó una evaluación, cuándo la realizó, qué base y gestor utilizó y cuál fue el resultado obtenido.*
*Por estas razones,* ***SchemaSafe es operativamente factible****, debido a que su flujo se integra a una tarea existente —la evaluación previa de modificaciones— y proporciona información orientada directamente a apoyar la decisión antes de ejecutar un cambio.*

### 4.4. <span id="_Toc52661353" class="anchor"></span>Factibilidad Legal

El planteamiento actual de SchemaSafe reduce diversos riesgos legales y de seguridad debido a que las pruebas serán ejecutadas utilizando **bases de datos controladas y conjuntos de información preparados específicamente para el proyecto**, evitando el uso de bases productivas o información institucional real.
Asimismo, SchemaSafe funcionará principalmente mediante evaluaciones preventivas y no ejecutará automáticamente las modificaciones propuestas. Cuando sea posible, las conexiones destinadas al análisis utilizarán únicamente los permisos necesarios para consultar la información.
Para la implementación definitiva deberán verificarse las condiciones de licencia correspondientes a:

- los gestores de bases de datos utilizados;
- bibliotecas y controladores de conexión;
- frameworks y dependencias del sistema;
- herramientas utilizadas durante desarrollo y pruebas.

También deberán evitarse credenciales incorporadas directamente en el código fuente y cualquier conjunto de datos de prueba que contenga información personal o confidencial.
Debido a que el proyecto aún se encuentra en etapa de diseño y el README no especifica todas las tecnologías de implementación, **no resulta correcto afirmar todavía el cumplimiento de licencias concretas que no han sido seleccionadas**.
Con las condiciones actualmente establecidas —datos controlados, finalidad académica, análisis preventivo y ausencia de modificación automática— no se identifica en el planteamiento del proyecto una restricción que impida continuar con su desarrollo. La verificación específica de licencias y tratamiento de datos deberá realizarse nuevamente cuando se defina el entorno tecnológico definitivo.

### 4.5. <span id="_Toc52661354" class="anchor"></span>Factibilidad Social

*El proyecto presenta una factibilidad social favorable debido a que busca proporcionar una herramienta de apoyo para desarrolladores y administradores que necesiten comprender las consecuencias de una modificación antes de aplicarla sobre la información almacenada.*
*El principal beneficio consiste en mejorar la capacidad de prevención. SchemaSafe no se limita a generar una advertencia, sino que pretende indicar* ***qué datos podrían verse afectados, en qué proporción, qué nivel de riesgo se identifica y por qué fue asignado****. Esto favorece una toma de decisiones más fundamentada.*
*En el ámbito académico, el proyecto también permite trabajar conjuntamente conceptos propios de bases de datos, como estructuras, tipos, índices, validaciones, restricciones, auditoría, seguridad, heterogeneidad de datos y evolución de esquemas.*
*Además, al contemplar tres tecnologías NoSQL diferentes, el sistema permite demostrar que los gestores NoSQL no constituyen un grupo completamente homogéneo y que una característica disponible en un motor puede requerir un tratamiento diferente en otro.*
*Por tanto, el impacto social y académico esperado es positivo, ya que el proyecto fomenta prácticas preventivas, documentación de cambios y análisis responsable antes de modificar información almacenada.*

### 4.6. <span id="_Toc52661355" class="anchor"></span>Factibilidad Ambiental

*El proyecto corresponde principalmente al desarrollo de un producto de software, por lo que no requiere procesos industriales, fabricación de componentes físicos ni utilización directa de materiales contaminantes.*
*Su impacto ambiental estará relacionado principalmente con el consumo energético producido por los equipos utilizados durante el desarrollo y por la ejecución de los gestores de bases de datos durante las pruebas.*
*Debido a que las pruebas se realizarán utilizando ambientes controlados y no se plantea inicialmente una infraestructura empresarial permanente, el consumo de recursos puede limitarse al periodo de desarrollo y validación.*
*También pueden adoptarse prácticas como:*

- *utilizar documentación digital;*
- *ejecutar únicamente los servicios necesarios durante las pruebas;*
- *evitar mantener instancias sin utilización;*
- *reutilizar los equipos informáticos disponibles;*
- *eliminar de forma controlada ambientes temporales una vez finalizadas las pruebas.*

*En consecuencia, se considera que* ***el impacto ambiental del prototipo es reducido y manejable****, por lo que esta dimensión no representa un impedimento para la ejecución del proyecto.*

<div style="page-break-after: always; visibility: hidden">\pagebreak</div>

## 5. <span id="_Toc52661356" class="anchor"></span>Análisis Financiero

*El análisis financiero busca determinar si la inversión requerida para desarrollar SchemaSafe se justifica en función de los beneficios que podría proporcionar.*

*Debe diferenciarse este apartado de la factibilidad económica. Mientras que la factibilidad económica identifica y organiza los costos necesarios para desarrollar el sistema, el análisis financiero relaciona dichos costos con los beneficios esperados y utiliza indicadores que permiten evaluar la conveniencia de la inversión.*

### 5.1. Justificación de la Inversión

La inversión en SchemaSafe se justifica por la necesidad de contar con un mecanismo preventivo que permita identificar problemas antes de ejecutar modificaciones estructurales sobre información existente.
Actualmente, según el planteamiento del proyecto, muchas comprobaciones dependen de herramientas específicas de cada gestor o de verificaciones realizadas manualmente. Esta situación dificulta contar con un procedimiento común y puede ocasionar que los problemas sean descubiertos durante una migración, durante las pruebas de una nueva versión o después de realizar el cambio.
SchemaSafe pretende trasladar parte de ese análisis a una etapa previa, permitiendo determinar:

- qué información podría resultar incompatible;
- cuántos elementos serían afectados;
- qué porcentaje representan respecto del total;
- qué nivel de riesgo posee el cambio;
- cuáles son las causas;
- qué medidas preventivas podrían adoptarse.

La inversión se orienta, por tanto, no solamente a la construcción de una aplicación, sino a desarrollar una herramienta que reduzca incertidumbre y facilite la detección temprana de incompatibilidades.

#### 5.1.1. Beneficios del Proyecto

### Beneficios tangibles potenciales

Los beneficios tangibles estarán relacionados principalmente con la reducción de trabajo asociado con comprobaciones manuales y con la detección anticipada de problemas.
Se consideran los siguientes:

1. Reducción del tiempo empleado en revisar manualmente grandes conjuntos de datos antes de una modificación.
2. Disminución del trabajo requerido para localizar información incompatible.
3. Automatización del cálculo de elementos afectados y porcentaje de impacto.
4. Reutilización de un mismo núcleo de análisis para distintos gestores mediante adaptadores.
5. Disponibilidad de reportes e historial sin necesidad de elaborar manualmente los resultados de cada evaluación.

Estos beneficios **no deben monetizarse todavía sin información real o una hipótesis económica claramente definida**.

### Beneficios intangibles

Entre los beneficios que no requieren necesariamente una valoración monetaria se encuentran:

- mayor visibilidad del riesgo antes de realizar modificaciones;
- mejor trazabilidad de los análisis;
- disponibilidad de una explicación para cada clasificación;
- reducción de incertidumbre durante la evolución de las estructuras;
- apoyo a la toma de decisiones;
- separación entre el análisis preventivo y la ejecución real;
- mejor control mediante usuarios, roles y auditoría;
- posibilidad de comparar evaluaciones anteriores;
- arquitectura extensible para incorporar nuevos gestores.

#### 5.1.2. Criterios de Inversión

Para evaluar financieramente SchemaSafe se utilizarán los indicadores **Relación Beneficio/Costo, Valor Actual Neto y Tasa Interna de Retorno**, conforme a la estructura establecida en el formato FD01.

##### 5.1.2.1. Relación Beneficio/Costo (B/C)

*La relación beneficio/costo permitirá comparar el valor monetario de los beneficios esperados con el costo total requerido para desarrollar el sistema.*

*La interpretación será:*

- ***B/C > 1:*** *los beneficios superan a los costos.*
- ***B/C = 1:*** *existe equilibrio.*
- ***B/C < 1:*** *los costos superan a los beneficios.*

*Para SchemaSafe:*

$$
B/C = \frac{Beneficios\ totales}{Costos\ totales}
$$

**Resultado:** [PENDIENTE DE VALORIZACIÓN].
*No recomiendo colocar un B/C inventado. Primero debemos establecer cuánto valoraremos el tiempo de desarrollo y de qué forma monetizaremos el beneficio del sistema.*

##### 5.1.2.2. Valor Actual Neto (VAN)

*El VAN permitirá determinar si los beneficios futuros estimados compensan la inversión inicial considerando el valor del dinero en el tiempo.*
*Su expresión general es:*

$$
VAN = -I_0 + \sum_{t=1}^{n}\frac{FC_t}{(1+r)^t}
$$

*Donde:*

- $I_0$: inversión inicial.
- $FC_t$: flujo neto correspondiente al periodo $t$.
- $r$: tasa de descuento.
- $n$: número de periodos analizados.

*Interpretación:*

- ***VAN > 0:*** *económicamente conveniente.*
- ***VAN = 0:*** *indiferente.*
- ***VAN < 0:*** *la inversión no recuperaría el rendimiento mínimo esperado.*

*Para SchemaSafe, los valores necesarios son actualmente:*
***Inversión inicial:*** *[PENDIENTE]*
 ***Flujos proyectados:*** *[PENDIENTE]*
 ***Tasa de descuento:*** *[PENDIENTE]*
 ***Periodo:*** *[PENDIENTE]*
*Por tanto, el VAN* ***no debe calcularse todavía hasta disponer de dichos valores****.*

##### 5.1.2.3. Tasa Interna de Retorno (TIR)

*La TIR representa la tasa que hace que el VAN sea igual a cero:*

$$
0 = -I_0 + \sum_{t=1}^{n}\frac{FC_t}{(1+TIR)^t}
$$

*Una vez obtenida, deberá compararse con la tasa mínima utilizada en el análisis:*

- *Si* ***TIR > tasa de referencia****, la inversión resulta financieramente favorable.*
- *Si* ***TIR = tasa de referencia****, el resultado se encuentra en equilibrio.*
- *Si* ***TIR < tasa de referencia****, la inversión no alcanza el rendimiento establecido.*

**TIR de SchemaSafe:** [PENDIENTE DE CÁLCULO].
*La TIR debe calcularse únicamente después de establecer los mismos flujos utilizados en el VAN.*

<div style="page-break-after: always; visibility: hidden">\pagebreak</div>

## 6. <span id="_Toc52661357" class="anchor"></span>Conclusiones

- *El análisis realizado permite determinar que* ***SchemaSafe NoSQL presenta factibilidad técnica****, debido a que su propuesta puede estructurarse mediante una arquitectura modular compuesta por un núcleo de análisis, un modelo intermedio y adaptadores específicos para MongoDB, Apache CouchDB y Apache Cassandra. Esta separación permite abordar las diferencias entre los gestores sin asumir que todos poseen las mismas características.*
- *El enfoque preventivo o dry-run representa un elemento fundamental para la viabilidad del sistema, debido a que permite analizar cambios propuestos sin ejecutarlos automáticamente sobre la base de datos. Esta característica se complementa con la utilización de ambientes controlados y, cuando sea posible, conexiones con privilegios limitados.*
- *La validación definida para el proyecto proporciona criterios objetivos para comprobar su funcionamiento. La ejecución de al menos* ***50 escenarios controlados****, incluyendo* ***10 escenarios de riesgo alto o crítico****, permitirá evaluar el comportamiento de los adaptadores y del motor de análisis. Particularmente, se plantea detectar correctamente el* ***100 % de los escenarios críticos previamente definidos*** *dentro de dicho conjunto de pruebas.*
- *Desde el punto de vista operativo, SchemaSafe resulta viable debido a que establece un procedimiento comprensible para desarrolladores y administradores: seleccionar una estructura, definir una modificación, analizar la información existente y obtener un resultado acompañado de impacto, riesgo, causas y recomendaciones. El historial, los reportes y la auditoría proporcionarán además trazabilidad sobre las evaluaciones efectuadas.*
- *La factibilidad legal se ve favorecida por el uso previsto de bases de datos y datos generados específicamente para las pruebas, evitando inicialmente el tratamiento de información productiva. Sin embargo, las licencias de las tecnologías, frameworks y dependencias que finalmente sean seleccionadas deberán verificarse antes de cerrar la documentación definitiva del proyecto.*
- *El proyecto presenta un impacto social y académico favorable, debido a que promueve una metodología preventiva para la evolución de estructuras de datos y permite aplicar conceptos relacionados con heterogeneidad, validaciones, índices, restricciones, seguridad, auditoría y análisis de datos en diferentes modelos NoSQL.*
- *El impacto ambiental esperado es reducido debido a que SchemaSafe corresponde principalmente a un producto de software que utilizará infraestructura informática durante su desarrollo y validación, sin requerir procesos productivos físicos adicionales.*
- *La factibilidad económica es* ***preliminarmente favorable****, pero todavía no puede establecerse cuantitativamente debido a que el documento base no especifica la duración definitiva, valorización de horas de desarrollo, costos operativos ni infraestructura final. Estos valores deberán incorporarse al presupuesto antes de emitir una conclusión financiera definitiva.*
- *Por la misma razón, los indicadores* ***B/C, VAN y TIR aún no deben presentar resultados numéricos****, puesto que hacerlo sin costos y beneficios monetarios previamente fundamentados produciría cifras artificiales. Una vez establecidos el costo total, los beneficios valorizados, los flujos y la tasa correspondiente, estos indicadores podrán calcularse y utilizarse para completar la evaluación financiera.*
- *Considerando las dimensiones técnica, operativa, legal, social y ambiental analizadas,* ***SchemaSafe NoSQL dispone de condiciones suficientes para continuar hacia las etapas de especificación de requerimientos, diseño arquitectónico, implementación y validación****, manteniendo como aspecto pendiente el cierre cuantitativo de su factibilidad económica y financiera.*
