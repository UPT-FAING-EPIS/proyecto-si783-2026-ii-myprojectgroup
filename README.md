# proyecto-formatos-01

# Título del proyecto

**Sistema multimotor para el análisis preventivo de riesgos, impacto y compatibilidad de cambios estructurales en bases de datos NoSQL antes de su despliegue**

# Nombre referencial del sistema

**SchemaSafe NoSQL – Sistema Multimotor de Análisis Preventivo de Cambios de Esquema**

# Planteamiento del problema

Las bases de datos NoSQL son utilizadas actualmente en sistemas que requieren flexibilidad, escalabilidad y manejo de grandes volúmenes de información. A diferencia de las bases de datos relacionales tradicionales, existen diferentes modelos NoSQL, como bases documentales, clave-valor, columnares y orientadas a grafos, cada uno con características particulares para organizar, validar y modificar sus datos.

Durante la evolución de una aplicación es frecuente que la estructura utilizada para almacenar información necesite modificarse. Estos cambios pueden incluir agregar o eliminar atributos, modificar tipos de datos, renombrar campos, cambiar estructuras anidadas, incorporar nuevas restricciones, modificar índices o establecer nuevas reglas de validación.

Aunque las bases de datos NoSQL proporcionan una mayor flexibilidad para realizar este tipo de modificaciones, dicha flexibilidad no elimina los riesgos asociados a la evolución de las estructuras de datos. Un cambio puede ocasionar incompatibilidades con información almacenada previamente, afectar consultas existentes, producir errores en nuevas versiones de una aplicación o requerir procesos de migración sobre grandes cantidades de información.

Por ejemplo, una colección documental puede contener los siguientes registros:

```json
{ "nombre": "Ana", "edad": 24 }
{ "nombre": "Luis", "edad": "31" }
{ "nombre": "Carla" }
```

En este caso, el atributo `edad` se encuentra almacenado como número en un documento, como texto en otro y está ausente en un tercero. Si una nueva versión de la aplicación establece que `edad` debe ser obligatoriamente de tipo texto, parte de la información existente sería incompatible con la nueva estructura.

Un problema similar puede presentarse cuando se elimina o renombra un atributo utilizado por una aplicación, cuando se modifica una estructura simple por un objeto anidado, cuando se incorpora una nueva validación o cuando se crea una restricción que los datos existentes no cumplen.

También pueden presentarse situaciones en las que diferentes documentos de una misma colección utilicen distintos tipos de datos para representar un mismo atributo. Por ejemplo, un campo denominado `telefono` podría encontrarse almacenado como texto en algunos documentos, como número en otros o incluso no existir en determinados registros. Antes de establecer una nueva estructura sería necesario conocer cuántos datos utilizan cada representación y cuáles resultarían afectados.

Otro caso puede ocurrir al modificar una estructura simple por una estructura anidada. Por ejemplo:

```json
{
  "nombre": "Ana",
  "telefono": "999999999"
}
```

puede modificarse posteriormente a:

```json
{
  "nombre": "Ana",
  "contacto": {
    "telefono": "999999999"
  }
}
```

Aunque el cambio puede mejorar la organización de la información, las aplicaciones o consultas que todavía busquen directamente el atributo `telefono` podrían dejar de funcionar correctamente.

La dificultad aumenta debido a que no todas las bases de datos NoSQL funcionan de la misma manera. MongoDB utiliza un modelo documental con esquema flexible y permite definir reglas de validación sobre los documentos. Apache CouchDB también utiliza documentos JSON, pero posee mecanismos propios para almacenar, consultar y validar modificaciones. Apache Cassandra, por otro lado, utiliza un modelo NoSQL orientado a columnas y define estructuras mediante tablas, columnas y tipos administrados mediante CQL.

Por esta razón, una modificación que puede resultar válida y sencilla en un motor NoSQL puede requerir un tratamiento diferente en otro.

MongoDB, por ejemplo, permite que los documentos pertenecientes a una misma colección puedan presentar estructuras diferentes si no se han establecido reglas de validación. Esto proporciona flexibilidad, pero también puede provocar que una colección acumule diferentes versiones de una misma estructura a medida que la aplicación evoluciona.

Apache CouchDB almacena información utilizando documentos JSON y permite trabajar con estructuras flexibles. Sin embargo, los mecanismos utilizados para validar, consultar y modificar documentos presentan diferencias respecto a MongoDB.

Apache Cassandra presenta una situación diferente debido a que utiliza un modelo basado en tablas y columnas. Aunque pertenece a la categoría de bases de datos NoSQL, su estructura es más explícita y las modificaciones deben adaptarse a las características disponibles mediante CQL.

Estas diferencias dificultan la creación de una única estrategia de evaluación aplicable directamente a todos los motores NoSQL. No obstante, existen conceptos que pueden ser analizados de manera común, como identificar la estructura existente, determinar qué cambio se desea realizar, calcular qué datos podrían resultar afectados y clasificar el nivel de riesgo antes de ejecutar la modificación.

Actualmente, el análisis de estos cambios suele depender de las herramientas específicas proporcionadas por cada gestor o de verificaciones realizadas manualmente por desarrolladores y administradores. Esto dificulta disponer de un procedimiento común que permita conocer anticipadamente el impacto que una modificación puede producir sobre diferentes tecnologías NoSQL.

En muchos casos, los problemas son detectados recién durante la ejecución de una migración, durante las pruebas de una nueva versión de la aplicación o después de que el cambio estructural ya fue realizado. En estas situaciones puede ser necesario corregir documentos, restaurar información, modificar consultas o desarrollar procesos adicionales de migración.

Ante esta problemática se propone desarrollar **SchemaSafe NoSQL**, una herramienta multimotor que permita analizar preventivamente cambios estructurales sobre diferentes bases de datos NoSQL antes de ejecutarlos.

Inicialmente, el sistema trabajará con tres motores:

* **MongoDB**, como base de datos documental.
* **Apache CouchDB**, como base de datos documental distribuida.
* **Apache Cassandra**, como base de datos NoSQL orientada a columnas.

SchemaSafe utilizará una arquitectura basada en adaptadores. Cada adaptador será responsable de interpretar las características particulares del motor correspondiente y transformar la información obtenida a una representación común que pueda ser procesada por el sistema.

De esta manera, el núcleo de SchemaSafe podrá aplicar criterios generales de análisis sin asumir que todos los gestores NoSQL funcionan de la misma forma.

La arquitectura general será similar a la siguiente:

```text
                    SCHEMASAFE NoSQL
                          |
                  MOTOR DE ANÁLISIS
                          |
               MODELO INTERMEDIO COMÚN
                          |
          ---------------------------------
          |               |               |
          v               v               v
       MongoDB          CouchDB         Cassandra
       Adapter          Adapter          Adapter
```

Cada adaptador tendrá la responsabilidad de conectarse con el gestor correspondiente, obtener información relevante sobre sus estructuras, convertirla a un formato común y proporcionar los datos necesarios al motor de análisis.

SchemaSafe permitirá conectarse a una base de datos de prueba o desarrollo, seleccionar la estructura que se desea analizar y obtener información sobre los datos existentes.

Posteriormente, el usuario podrá definir un cambio estructural propuesto sin aplicarlo directamente sobre la base de datos.

El sistema identificará modificaciones como:

* incorporación de nuevos atributos o campos;
* eliminación de atributos;
* renombramiento de atributos;
* modificación de tipos de datos;
* establecimiento de atributos obligatorios;
* modificación de estructuras anidadas;
* cambios relacionados con índices;
* incorporación o modificación de validaciones;
* cambios sobre restricciones soportadas por el motor;
* modificaciones estructurales específicas de cada gestor NoSQL.

Cada modificación será evaluada mediante reglas y clasificada en niveles de riesgo **BAJO, MEDIO, ALTO o CRÍTICO**.

La clasificación se realizará utilizando reglas explícitas y explicables. Esto significa que SchemaSafe no solamente indicará que un cambio posee determinado nivel de riesgo, sino que también mostrará las razones que originaron dicha clasificación.

Por ejemplo, un cambio podría ser clasificado como riesgo alto cuando afecte a un porcentaje elevado de los datos existentes, cuando implique modificar tipos incompatibles o cuando requiera transformar estructuras utilizadas por una cantidad significativa de registros.

El sistema también realizará comprobaciones sobre los datos existentes. Por ejemplo, antes de establecer que un atributo debe utilizar un determinado tipo de dato podrá verificar cuántos registros utilizan actualmente un tipo diferente; antes de convertir un atributo en obligatorio comprobará cuántos elementos no lo contienen; y antes de aplicar determinadas restricciones podrá analizar si existen datos incompatibles.

SchemaSafe calculará la cantidad y porcentaje de elementos potencialmente afectados por cada cambio.

Por ejemplo:

```text
Motor: MongoDB
Base de datos: ventas
Colección: clientes

Cambio propuesto:
edad: Number → String

Documentos analizados: 10 000
Documentos potencialmente afectados: 8 420
Impacto estimado: 84.20 %

Nivel de riesgo: ALTO

Problemas encontrados:
- 8 420 documentos utilizan actualmente Number.
- 210 documentos no contienen el atributo edad.
- 35 documentos contienen valores nulos.

Recomendación:
Normalizar la información existente antes de aplicar
la nueva estructura.
```

En otro escenario, si el usuario propone convertir un atributo en obligatorio, SchemaSafe podrá identificar cuántos documentos no contienen dicho atributo.

Por ejemplo:

```text
Cambio propuesto:
correo → obligatorio

Documentos analizados: 5 000
Documentos sin correo: 1 120
Impacto estimado: 22.40 %

Nivel de riesgo: MEDIO
```

Asimismo, cuando el gestor lo permita, SchemaSafe podrá evaluar cambios relacionados con índices, validaciones o restricciones antes de su aplicación.

Debido a las diferencias existentes entre los gestores NoSQL, determinadas comprobaciones estarán disponibles únicamente cuando el motor seleccionado soporte la característica evaluada. SchemaSafe identificará estas diferencias mediante los adaptadores correspondientes.

Por ejemplo, una regla relacionada con determinada característica de MongoDB no necesariamente será aplicable de la misma forma sobre Cassandra. En estos casos, el adaptador correspondiente determinará qué análisis pueden realizarse y qué información puede obtenerse.

El sistema trabajará principalmente bajo un enfoque de **análisis preventivo o dry-run**, por lo que los cambios propuestos no serán ejecutados automáticamente sobre la base analizada.

El objetivo será proporcionar información al desarrollador o administrador antes de realizar una modificación, permitiéndole determinar si debe corregir datos, preparar una migración, realizar una copia de seguridad o reconsiderar el cambio propuesto.

Cada análisis generará un reporte que contendrá el motor NoSQL utilizado, estructura analizada, cambio propuesto, cantidad de elementos evaluados, elementos potencialmente afectados, porcentaje de impacto, nivel de riesgo, problemas encontrados y recomendaciones.

El resultado general del análisis será clasificado como:

**APROBADO:** no se detectan riesgos importantes.

**REQUIERE REVISIÓN:** existen incompatibilidades o advertencias que deben analizarse antes de realizar el cambio.

**NO RECOMENDADO:** existen modificaciones de riesgo alto o crítico que podrían afectar significativamente los datos existentes.

SchemaSafe almacenará además un historial de análisis para permitir consultar evaluaciones realizadas anteriormente y comparar diferentes versiones de una misma estructura.

Por ejemplo, después de corregir los datos incompatibles, un usuario podría ejecutar nuevamente el análisis y comparar ambos resultados para verificar si el porcentaje de riesgo disminuyó.

Las pruebas serán realizadas utilizando bases de datos controladas y conjuntos de información generados específicamente para el proyecto, evitando afectar bases de datos reales o productivas.

Se utilizarán diferentes escenarios preparados previamente para comprobar que SchemaSafe sea capaz de detectar correctamente situaciones de riesgo conocidas.

Entre estos escenarios podrán incluirse:

* documentos con diferentes tipos para un mismo atributo;
* documentos donde determinados atributos no existan;
* estructuras anidadas diferentes;
* campos con valores nulos;
* modificaciones de tipos de datos;
* eliminación de atributos;
* renombramiento de atributos;
* incorporación de nuevas reglas de validación;
* cambios relacionados con índices;
* modificaciones propias del modelo utilizado por Cassandra.

También se implementarán usuarios, roles y auditoría de las principales operaciones realizadas dentro del sistema.

Los usuarios podrán disponer de diferentes niveles de acceso, permitiendo separar las funciones administrativas de las funciones relacionadas con el análisis de bases de datos.

La auditoría permitirá registrar información como:

* usuario que realizó el análisis;
* fecha y hora;
* motor NoSQL seleccionado;
* base de datos evaluada;
* estructura analizada;
* tipo de cambio;
* resultado obtenido;
* nivel de riesgo generado.

Siempre que sea posible, las conexiones utilizadas durante los análisis dispondrán únicamente de los permisos necesarios para consultar la información, reduciendo el riesgo de modificaciones accidentales.

De esta manera, **SchemaSafe NoSQL** permitirá analizar modificaciones potencialmente peligrosas antes de su aplicación sobre diferentes tecnologías NoSQL.

Su finalidad no será reemplazar herramientas de migración, ejecutar automáticamente transformaciones ni limitarse a comprobar sintaxis, sino proporcionar una evaluación preventiva, uniforme y explicable del posible impacto de los cambios sobre información existente.

El principal aporte del proyecto será proporcionar una capa de abstracción capaz de trabajar con múltiples motores NoSQL mediante adaptadores específicos y un núcleo común de análisis de riesgos.

Esto permitirá que SchemaSafe pueda ampliarse posteriormente para soportar nuevos gestores NoSQL sin necesidad de rediseñar completamente el sistema.

# Objetivos del Proyecto

# Objetivo general

Analizar los riesgos asociados con la aplicación de cambios estructurales sobre diferentes bases de datos NoSQL con información existente, identificando incompatibilidades, modificaciones potencialmente destructivas y condiciones que puedan ocasionar problemas durante la evolución o despliegue de una aplicación.

# Objetivos específicos

1. Analizar las características estructurales de al menos tres gestores NoSQL: MongoDB, Apache CouchDB y Apache Cassandra.

2. Identificar como mínimo diez tipos de modificaciones estructurales que puedan afectar los datos almacenados en bases de datos NoSQL.

3. Clasificar los cambios según su nivel de riesgo sobre la integridad, compatibilidad, disponibilidad y conservación de la información.

4. Definir al menos quince reglas para identificar cambios potencialmente incompatibles o peligrosos.

5. Analizar las diferencias existentes entre los mecanismos de estructura, validación, índices y restricciones de los motores NoSQL seleccionados.

6. Identificar qué tipos de modificaciones pueden analizarse utilizando criterios comunes entre diferentes gestores NoSQL.

7. Determinar qué modificaciones requieren reglas específicas de acuerdo con las características de cada motor.

8. Analizar situaciones en las que los datos existentes resulten incompatibles con una nueva estructura propuesta.

9. Identificar casos donde un mismo atributo se encuentre representado mediante diferentes tipos de datos dentro de un mismo conjunto de información.

10. Analizar problemas derivados de atributos ausentes, valores nulos y estructuras documentales heterogéneas.

11. Definir métricas para cuantificar el impacto de una modificación mediante cantidad de elementos analizados, cantidad de elementos afectados y porcentaje de incompatibilidad.

12. Diseñar un modelo intermedio que permita representar de manera común la información estructural obtenida desde diferentes gestores NoSQL.

13. Definir criterios para clasificar automáticamente los cambios en niveles de riesgo **BAJO, MEDIO, ALTO o CRÍTICO**.

14. Definir criterios para generar un resultado general como **APROBADO, REQUIERE REVISIÓN o NO RECOMENDADO**.

15. Analizar mecanismos que permitan realizar evaluaciones preventivas sin modificar directamente la base de datos estudiada.

16. Analizar la posibilidad de utilizar adaptadores para incorporar nuevos motores NoSQL posteriormente.

17. Identificar los requerimientos funcionales y no funcionales necesarios para desarrollar SchemaSafe NoSQL.

# Objetivo Solución

# Objetivo general de solución

Desarrollar y validar una herramienta multimotor que permita analizar preventivamente cambios estructurales sobre diferentes bases de datos NoSQL antes de su aplicación, identificando incompatibilidades, datos potencialmente afectados, nivel de impacto y riesgo asociado a cada modificación.

# Objetivos específicos de solución

1. Implementar una arquitectura modular basada en adaptadores que permita trabajar con diferentes gestores NoSQL.

2. Implementar inicialmente compatibilidad con **MongoDB, Apache CouchDB y Apache Cassandra**.

3. Implementar conexiones configurables con los tres motores NoSQL seleccionados.

4. Permitir comprobar el estado de conexión con cada gestor antes de realizar un análisis.

5. Permitir seleccionar y explorar las bases de datos, colecciones, documentos, tablas y demás estructuras disponibles según el gestor utilizado.

6. Obtener automáticamente información sobre la estructura actual de los datos.

7. Identificar atributos, tipos de datos, estructuras anidadas, índices, validaciones y restricciones disponibles según las características de cada motor.

8. Identificar la frecuencia de aparición de los atributos cuando el modelo de base de datos lo permita.

9. Identificar diferentes tipos de datos utilizados para representar un mismo atributo.

10. Implementar un modelo intermedio común para representar la información obtenida desde los diferentes gestores NoSQL.

11. Permitir definir cambios estructurales propuestos sin ejecutarlos directamente sobre la base de datos.

12. Analizar como mínimo diez tipos de modificaciones estructurales compatibles con uno o más de los motores implementados.

13. Detectar cambios de tipo de dato que puedan resultar incompatibles con información existente.

14. Detectar atributos ausentes antes de establecer nuevas condiciones de obligatoriedad o validación.

15. Detectar la eliminación de atributos existentes.

16. Detectar el renombramiento de atributos existentes.

17. Analizar modificaciones de estructuras simples hacia objetos, documentos anidados o colecciones de valores cuando corresponda.

18. Analizar cambios relacionados con índices cuando sean soportados por el gestor seleccionado.

19. Analizar cambios relacionados con validaciones cuando sean soportados por el gestor seleccionado.

20. Analizar cambios relacionados con restricciones cuando sean soportados por el gestor seleccionado.

21. Incorporar reglas específicas para operaciones propias de cada gestor NoSQL cuando no puedan evaluarse mediante reglas comunes.

22. Calcular automáticamente la cantidad de elementos potencialmente afectados por cada modificación.

23. Calcular el porcentaje de impacto de cada cambio respecto al conjunto de datos analizado.

24. Implementar al menos quince reglas de evaluación preventiva de riesgos.

25. Clasificar automáticamente cada modificación como **BAJO, MEDIO, ALTO o CRÍTICO**.

26. Mostrar las causas que originaron cada nivel de riesgo.

27. Generar recomendaciones preventivas relacionadas con los problemas encontrados.

28. Generar un resultado general como **APROBADO, REQUIERE REVISIÓN o NO RECOMENDADO**.

29. Mantener los análisis en modo **dry-run**, evitando ejecutar automáticamente los cambios propuestos.

30. Evitar operaciones destructivas durante el proceso de análisis.

31. Generar reportes con el motor utilizado, estructura analizada, cambio propuesto, elementos afectados, porcentaje de impacto, nivel de riesgo, problemas encontrados y recomendaciones.

32. Permitir consultar los reportes generados anteriormente.

33. Mantener un historial de los análisis realizados y sus respectivos resultados.

34. Permitir comparar diferentes análisis correspondientes a una misma estructura cuando sea posible.

35. Implementar usuarios y roles para controlar las funciones administrativas del sistema.

36. Registrar mediante auditoría el usuario, fecha, motor NoSQL, base analizada y resultado de cada evaluación.

37. Registrar el tipo de modificación evaluada dentro del historial de auditoría.

38. Implementar una interfaz que permita visualizar de manera comprensible los resultados del análisis.

39. Mostrar mediante indicadores la cantidad de elementos compatibles e incompatibles encontrados.

40. Mostrar porcentajes de impacto asociados con los cambios evaluados.

41. Evaluar el sistema mediante al menos **50 escenarios controlados**, distribuidos entre los gestores NoSQL implementados.

42. Incluir escenarios específicos para MongoDB.

43. Incluir escenarios específicos para Apache CouchDB.

44. Incluir escenarios específicos para Apache Cassandra.

45. Incluir como mínimo **10 escenarios considerados de riesgo alto o crítico** para validar el funcionamiento del motor de análisis.

46. Detectar correctamente el **100 % de los escenarios críticos previamente definidos** para las pruebas controladas.

47. Comprobar que SchemaSafe no ejecute modificaciones sobre las bases utilizadas durante las evaluaciones preventivas.

48. Verificar que los adaptadores entreguen al núcleo del sistema información normalizada independientemente del gestor utilizado.

49. Documentar las funcionalidades disponibles y las limitaciones existentes para cada uno de los motores implementados.

50. Diseñar la arquitectura de manera que posteriormente puedan incorporarse nuevos gestores NoSQL mediante nuevos adaptadores sin modificar completamente el núcleo de SchemaSafe.

# Resultado esperado del proyecto

Al finalizar el proyecto se espera contar con un prototipo funcional de **SchemaSafe NoSQL**, capaz de conectarse a diferentes bases de datos NoSQL, obtener información sobre la estructura y características de sus datos y analizar modificaciones antes de que sean ejecutadas.

El sistema deberá funcionar inicialmente con **MongoDB, Apache CouchDB y Apache Cassandra**, utilizando un adaptador especializado para cada gestor y un motor común encargado de realizar la evaluación preventiva.

Cada adaptador permitirá obtener las características relevantes del gestor correspondiente y convertirlas a un modelo intermedio utilizado por el motor principal de SchemaSafe.

El sistema permitirá seleccionar una base de datos y determinar la estructura que será evaluada.

Posteriormente, el usuario podrá indicar una modificación estructural que desea realizar.

SchemaSafe analizará los datos existentes y determinará si estos resultan compatibles con la modificación propuesta.

Por ejemplo, si en una colección MongoDB se propone cambiar el atributo `edad` de tipo numérico a texto, SchemaSafe analizará previamente los documentos existentes para determinar cuántos utilizan actualmente el tipo anterior.

Supóngase que existen 10 000 documentos:

```text
Number: 8 420
String: 1 100
Null: 270
Ausente: 210
```

SchemaSafe podrá generar el siguiente resultado:

```text
Motor: MongoDB
Base de datos: ventas
Colección: clientes

CAMBIO PROPUESTO
edad: Number → String

Documentos analizados: 10 000
Documentos potencialmente afectados: 8 420

Porcentaje de impacto: 84.20 %

RIESGO: ALTO

Causas:
- La mayoría de los documentos utiliza Number.
- Existen documentos sin el atributo.
- Existen documentos con valores nulos.

Recomendación:
Normalizar previamente los valores existentes
antes de establecer la nueva estructura.

RESULTADO GENERAL:
NO RECOMENDADO
```

De manera similar, si se pretende establecer un atributo como obligatorio, SchemaSafe verificará cuántos elementos no contienen actualmente dicho atributo.

Por ejemplo:

```text
CAMBIO PROPUESTO
correo → obligatorio

Documentos analizados: 5 000
Documentos sin correo: 1 120

Impacto: 22.40 %

RIESGO: MEDIO

RESULTADO GENERAL:
REQUIERE REVISIÓN
```

También podrá analizar situaciones donde se proponga eliminar un atributo.

Por ejemplo:

```text
CAMBIO PROPUESTO
Eliminar atributo: telefono

Documentos analizados: 8 000
Documentos que contienen telefono: 7 830

Impacto: 97.88 %

RIESGO: CRÍTICO

Recomendación:
Verificar que la información almacenada en el atributo
ya no sea utilizada y realizar una copia de seguridad
antes de cualquier proceso de migración.

RESULTADO GENERAL:
NO RECOMENDADO
```

En CouchDB podrá analizar cambios equivalentes relacionados con documentos JSON y las características disponibles en dicho gestor.

En Cassandra se evaluarán modificaciones estructurales compatibles con su modelo de tablas, columnas y tipos de datos.

Debido a que las tecnologías seleccionadas poseen características diferentes, SchemaSafe no aplicará exactamente las mismas reglas sobre todos los gestores.

Cada adaptador interpretará las capacidades del motor correspondiente y entregará la información necesaria al núcleo común de análisis.

El sistema permitirá distinguir entre reglas generales y reglas particulares.

Las reglas generales podrán aplicarse cuando diferentes motores permitan evaluar conceptos equivalentes.

Las reglas específicas serán utilizadas únicamente cuando una característica dependa directamente del gestor seleccionado.

El sistema mostrará los riesgos encontrados, explicará sus causas, calculará el impacto sobre los datos existentes y proporcionará recomendaciones antes de que el cambio sea realizado.

Asimismo, conservará un historial de análisis y permitirá generar reportes con los resultados obtenidos.

Los reportes incluirán como mínimo:

* usuario que realizó el análisis;
* fecha y hora;
* gestor NoSQL utilizado;
* base de datos analizada;
* estructura seleccionada;
* cambio propuesto;
* cantidad de elementos analizados;
* cantidad de elementos afectados;
* porcentaje de impacto;
* nivel de riesgo;
* causas detectadas;
* recomendaciones;
* resultado general.

Los análisis serán realizados principalmente bajo el enfoque **dry-run**, por lo que el sistema no modificará automáticamente los datos evaluados.

Esta característica permitirá utilizar SchemaSafe como una herramienta preventiva antes de realizar cambios reales.

Las pruebas del sistema se realizarán utilizando escenarios controlados y bases de datos preparadas específicamente para el proyecto.

No será necesario utilizar información de sistemas reales o productivos.

Se crearán diferentes conjuntos de datos que permitan comprobar el funcionamiento de las reglas implementadas.

Por ejemplo, se prepararán conjuntos donde existan:

* tipos de datos incompatibles;
* campos ausentes;
* valores nulos;
* estructuras anidadas;
* atributos duplicados cuando corresponda;
* modificaciones de índices;
* reglas de validación;
* cambios de columnas;
* modificaciones consideradas de riesgo crítico.

Como parte de la validación se ejecutarán al menos 50 escenarios controlados distribuidos entre los tres gestores implementados.

Dentro de estos escenarios se definirán previamente casos considerados críticos y se verificará que SchemaSafe sea capaz de identificarlos correctamente.

El proyecto también deberá demostrar que la arquitectura basada en adaptadores permite separar la lógica específica de cada gestor del núcleo general del sistema.

Esto permitirá que en versiones posteriores puedan incorporarse otros motores como Redis, Neo4j u otras tecnologías NoSQL sin necesidad de reconstruir completamente SchemaSafe.

De esta manera, **SchemaSafe NoSQL** permitirá demostrar que la flexibilidad proporcionada por las bases de datos NoSQL no elimina los riesgos asociados con la evolución de las estructuras de datos.

El sistema ofrecerá una evaluación preventiva antes de implementar determinados cambios, permitiendo conocer qué información podría resultar afectada, cuánto representa respecto al conjunto analizado y qué nivel de riesgo posee la modificación.

El aporte principal del proyecto será proporcionar una herramienta multimotor orientada específicamente al análisis preventivo y no destructivo de cambios estructurales sobre diferentes tecnologías NoSQL.

# Referencias

MongoDB, Inc. (2026). *MongoDB Manual: Schema Validation.*

MongoDB, Inc. (2026). *MongoDB Manual: Specify JSON Schema Validation.*

MongoDB, Inc. (2026). *MongoDB Manual: Indexes.*

MongoDB, Inc. (2026). *MongoDB Manual: Data Modeling.*

Apache Software Foundation. (2026). *Apache CouchDB Documentation.*

Apache Software Foundation. (2026). *Apache CouchDB Documentation: Design Documents and Validation.*

Apache Software Foundation. (2026). *Apache Cassandra Documentation.*

Apache Software Foundation. (2026). *Apache Cassandra Documentation: Data Modeling.*

Apache Software Foundation. (2026). *Apache Cassandra CQL Documentation.*

Universidad Privada de Tacna. (2026). *Sílabo de Base de Datos II, SI-783.*
