# proyecto-formatos-01

# Título del proyecto

Sistema para el análisis preventivo de riesgos, impacto y compatibilidad de cambios estructurales en bases de datos antes de su despliegue


# Nombre referencial del sistema

SchemaSafe – Sistema de Evaluación Preventiva de Cambios en Bases de Datos


# Planteamiento del problema

Las bases de datos constituyen uno de los componentes principales de los sistemas de información, debido a que almacenan información necesaria para el funcionamiento de aplicaciones, procesos administrativos y servicios digitales. A medida que un sistema evoluciona, también es necesario realizar modificaciones sobre la estructura de su base de datos, como crear o eliminar tablas, agregar columnas, modificar tipos de datos, incorporar restricciones, crear índices o establecer nuevas relaciones entre entidades.

Estas modificaciones suelen ejecutarse mediante sentencias de definición de datos, scripts SQL o procesos de migración. Sin embargo, que una sentencia sea sintácticamente correcta no significa necesariamente que pueda aplicarse de manera segura sobre una base de datos que ya contiene información.

Una modificación aparentemente sencilla puede generar consecuencias no previstas. Por ejemplo, eliminar una columna puede provocar pérdida permanente de información; reducir el tamaño de un campo de texto puede afectar registros cuyo contenido exceda la nueva longitud; cambiar un tipo de dato puede ocasionar pérdida de precisión; agregar una restricción UNIQUE puede fallar si existen valores duplicados; incorporar una restricción NOT NULL puede resultar incompatible con registros existentes que contienen valores nulos; y eliminar un índice puede afectar el rendimiento de consultas que dependían de él.

La documentación oficial de motores como PostgreSQL y MySQL evidencia que las operaciones ALTER TABLE permiten realizar cambios significativos sobre la estructura de las tablas, incluyendo incorporación y eliminación de columnas, modificación de tipos, creación o eliminación de índices y administración de restricciones. Algunas de estas operaciones también pueden afectar objetos dependientes o requerir privilegios específicos para su ejecución.

En PostgreSQL, por ejemplo, la eliminación de una columna puede provocar también la eliminación de índices y restricciones asociadas a ella, mientras que otras dependencias externas, como determinadas vistas o claves foráneas, requieren tratamiento adicional. Esto demuestra que una modificación estructural puede tener consecuencias sobre otros elementos de la base de datos y no únicamente sobre el objeto directamente modificado.

En entornos de desarrollo es frecuente utilizar scripts de actualización para evolucionar progresivamente el esquema de una base de datos. Cuando estos cambios se aplican sin una evaluación previa suficiente, un error puede descubrirse recién durante el despliegue o después de que la modificación haya sido ejecutada.

Además, cuando una base de datos contiene información previa, la seguridad de una modificación depende tanto de la estructura propuesta como del contenido existente. Por ejemplo, la instrucción para agregar una restricción UNIQUE puede ser técnicamente correcta, pero no podrá aplicarse correctamente si la columna contiene información duplicada.

De forma similar, modificar un campo VARCHAR(100) a VARCHAR(20) puede representar un riesgo cuando existen valores cuya longitud supera el nuevo límite. Por ello, evaluar únicamente la sintaxis de una modificación resulta insuficiente para determinar si esta puede aplicarse sin afectar la información almacenada.

Otro aspecto importante corresponde a la compatibilidad entre diferentes sistemas gestores de bases de datos. Los motores relacionales utilizan dialectos, tipos de datos y características que pueden presentar diferencias entre sí. Una instrucción diseñada para MySQL puede requerir modificaciones para ejecutarse sobre PostgreSQL, SQL Server u otro gestor.

Asimismo, frente a una modificación problemática resulta importante determinar si existe una estrategia para revertir los cambios. Herramientas de gestión de migraciones como Liquibase incorporan el concepto de rollback precisamente para regresar una base de datos a un estado previo cuando una actualización debe ser revertida. Sin embargo, no todos los cambios pueden deshacerse sin riesgo de pérdida de información, especialmente cuando han eliminado datos.

Ante esta problemática se propone desarrollar SchemaSafe, un sistema orientado a analizar de manera preventiva los cambios estructurales propuestos para una base de datos antes de ejecutarlos sobre el entorno objetivo.

SchemaSafe permitirá conectarse a una base de datos de prueba o desarrollo y obtener información de su esquema actual, incluyendo tablas, columnas, tipos de datos, claves primarias, claves foráneas, índices, restricciones y otros objetos seleccionados.

Posteriormente, el usuario podrá proporcionar un script de modificación o una segunda versión del esquema que represente el estado que desea obtener después de la actualización.

El sistema analizará las diferencias entre la estructura actual y la estructura propuesta e identificará automáticamente las operaciones que se producirían.

Por ejemplo:

ESQUEMA ACTUAL
        ↓
SCHEMASAFE
        ↑
SCRIPT / ESQUEMA PROPUESTO

El sistema podrá detectar cambios como:

- creación de tablas;
- eliminación de tablas;
- incorporación de columnas;
- eliminación de columnas;
- modificación de tipos de datos;
- reducción de longitud;
- incorporación o eliminación de claves;
- incorporación o eliminación de restricciones;
- creación o eliminación de índices;
- modificación de relaciones;
- renombrado de objetos.

Posteriormente, cada cambio será analizado utilizando un conjunto de reglas previamente definidas.

SchemaSafe clasificará los cambios según un nivel de riesgo configurable, utilizando categorías como:

RIESGO BAJO
RIESGO MEDIO
RIESGO ALTO
RIESGO CRÍTICO

Una operación de creación de una columna opcional, por ejemplo, podría representar un riesgo bajo, mientras que la eliminación de una columna que contiene información podría clasificarse como riesgo crítico.

La herramienta no se limitará a revisar la estructura. Cuando sea técnicamente posible, también realizará comprobaciones sobre los datos existentes antes de determinar el riesgo.

Por ejemplo, frente al cambio:

VARCHAR(100)
     ↓
VARCHAR(20)

SchemaSafe podrá consultar la longitud máxima de la información actualmente almacenada.

Si encuentra:

Longitud máxima existente: 47
Nueva longitud permitida: 20
Registros afectados: 148

el sistema mostrará:

RIESGO ALTO

Existen registros cuyo contenido supera el nuevo tamaño permitido. La modificación podría provocar pérdida, truncamiento o rechazo de información dependiendo del gestor utilizado.

Otro escenario corresponde a la incorporación de una restricción UNIQUE.

Antes de aprobarla, SchemaSafe podrá comprobar si existen valores duplicados.

Por ejemplo:

ALTER TABLE usuarios
ADD CONSTRAINT uq_email UNIQUE(email);

ANÁLISIS PREVIO:

Registros analizados: 10 000
Valores duplicados: 34

RESULTADO:

CAMBIO NO RECOMENDADO

Existen registros que incumplen la nueva restricción.

De manera similar, cuando se proponga una restricción NOT NULL, SchemaSafe podrá comprobar previamente la existencia de valores nulos y advertir al usuario cuando la modificación pueda fallar.

El sistema también analizará dependencias estructurales.

Si se intenta eliminar una columna utilizada por claves foráneas, índices, vistas u otros objetos identificables, SchemaSafe mostrará las dependencias que podrían resultar afectadas antes de permitir considerar el cambio como seguro.

Por ejemplo:

CAMBIO PROPUESTO:

Eliminar clientes.id

DEPENDENCIAS DETECTADAS:

ventas.cliente_id
pagos.cliente_id
credito.cliente_id

RIESGO:

CRÍTICO

La modificación puede afectar la integridad referencial de la base de datos.

Otra funcionalidad será el análisis de índices. Cuando un script elimine un índice, SchemaSafe registrará qué tabla y columnas se encuentran involucradas y advertirá que la eliminación puede modificar el comportamiento o rendimiento de las consultas relacionadas.

SchemaSafe también incluirá un módulo de compatibilidad entre gestores. Para determinadas instrucciones y tipos de datos seleccionados, podrá identificar diferencias entre motores soportados y advertir al usuario cuando un script haya sido creado utilizando características específicas de un gestor.

Por ejemplo:

INSTRUCCIÓN:
AUTO_INCREMENT

MYSQL: COMPATIBLE
POSTGRESQL: REQUIERE ADAPTACIÓN

El propósito no será reemplazar completamente los motores de migración existentes, sino proporcionar una evaluación previa orientada a explicar los riesgos que podría producir un cambio determinado.

El sistema incorporará además un mecanismo de generación o sugerencia de rollback para aquellas operaciones que puedan revertirse razonablemente.

Por ejemplo:

CAMBIO PROPUESTO:

ALTER TABLE cliente
ADD COLUMN observacion VARCHAR(200);

ROLLBACK PROPUESTO:

ALTER TABLE cliente
DROP COLUMN observacion;

Sin embargo, SchemaSafe diferenciará entre operaciones reversibles y operaciones potencialmente destructivas. Si una modificación elimina información, el sistema no afirmará que puede restaurarla únicamente mediante una sentencia inversa y deberá advertir que la recuperación puede requerir una copia de seguridad.

Cada análisis realizado generará un informe que contendrá como mínimo la base de datos evaluada, gestor utilizado, fecha, script analizado, cambios detectados, dependencias encontradas, nivel de riesgo, problemas identificados y recomendaciones.

El resultado general podrá clasificarse como:

APROBADO
La modificación no presenta riesgos críticos según las reglas configuradas.

REQUIERE REVISIÓN
Se identificaron advertencias que deben ser revisadas antes del despliegue.

NO RECOMENDADO
Se encontraron uno o más cambios de riesgo alto o crítico que podrían provocar pérdida de datos, incompatibilidades o fallos.

Por ejemplo:

SCHEMASAFE

Versión actual: V1.8
Versión propuesta: V1.9

Cambios detectados: 17

5 cambios de riesgo bajo
6 advertencias
4 cambios de riesgo alto
2 cambios críticos

Principales problemas:

- Columna con datos será eliminada.
- Clave foránea afectada.
- Reducción de longitud incompatible con información existente.
- Índice eliminado.
- Restricción UNIQUE incompatible con datos actuales.

RESULTADO:

NO RECOMENDADO PARA DESPLIEGUE

SchemaSafe almacenará un historial de los análisis realizados para permitir comparar diferentes versiones de un mismo cambio y verificar si los problemas detectados han sido corregidos.

Por ejemplo, una primera versión puede producir:

VERSIÓN 1
Riesgo: CRÍTICO
Problemas: 5

Después de modificar el script:

VERSIÓN 2
Riesgo: MEDIO
Problemas: 2

Finalmente:

VERSIÓN 3
Riesgo: BAJO
Problemas críticos: 0

De esta forma, la herramienta permitirá observar la evolución de una modificación antes de ejecutarla.

El proyecto utilizará bases de datos controladas y datos generados específicamente para las pruebas. De esta manera podrán simularse diferentes escenarios de riesgo sin afectar bases de datos reales ni información institucional.

El sistema podrá trabajar inicialmente con MySQL y PostgreSQL para las funciones principales, manteniendo una arquitectura que permita incorporar posteriormente otros gestores según el avance del proyecto.

Asimismo, el sistema aplicará principios de seguridad mediante usuarios y roles con privilegios limitados. Para realizar análisis preventivos se priorizarán conexiones de lectura cuando la operación no requiera realizar modificaciones, reduciendo el riesgo de que la propia herramienta altere accidentalmente la base de datos evaluada.

SchemaSafe también mantendrá auditoría sobre sus operaciones principales, registrando los análisis ejecutados, usuario responsable, conexión utilizada, resultados obtenidos y fecha de ejecución.

De esta manera, el proyecto buscará solucionar el problema de evaluar cambios estructurales antes de su despliegue, proporcionando información que permita identificar riesgos de pérdida de datos, incompatibilidad, ruptura de dependencias o imposibilidad de aplicar nuevas restricciones.

A diferencia de un validador de sintaxis, SchemaSafe no se limitará a determinar si una sentencia SQL está correctamente escrita. Una sentencia puede ser sintácticamente válida y, aun así, representar un cambio peligroso para una base de datos existente.

Tampoco tendrá como objetivo principal migrar información entre diferentes motores. Su finalidad será analizar el impacto potencial de una modificación antes de que esta sea aplicada.

El proyecto permitirá aplicar conocimientos de arquitectura y gestión de bases de datos relacionales, índices, restricciones, integridad, seguridad, roles, auditoría, respaldo, recuperación y análisis avanzado de información, manteniendo además la posibilidad de extender el análisis hacia diferentes tecnologías de almacenamiento estudiadas durante el curso.


# Objetivos del Proyecto


# Objetivo general

Analizar los riesgos asociados con la aplicación de cambios estructurales sobre bases de datos que contienen información previa, identificando modificaciones potencialmente destructivas, incompatibilidades, dependencias y condiciones de los datos que puedan ocasionar fallos durante un despliegue.


# Objetivos específicos

1. Identificar y documentar como mínimo diez tipos de modificaciones estructurales que puedan afectar una base de datos, incluyendo creación y eliminación de tablas o columnas, cambios de tipos, restricciones, índices y relaciones.

2. Clasificar los cambios estructurales identificados según su nivel potencial de riesgo sobre disponibilidad, integridad, compatibilidad y conservación de datos.

3. Definir al menos quince reglas de análisis preventivo que permitan identificar situaciones potencialmente peligrosas antes de ejecutar una modificación.

4. Analizar las principales dependencias existentes entre tablas, columnas, claves, índices y restricciones que puedan resultar afectadas durante un cambio estructural.

5. Identificar situaciones en las que una modificación sintácticamente válida pueda fallar debido a las características de los datos existentes.

6. Analizar diferencias relevantes entre los dialectos y características estructurales de al menos dos gestores de bases de datos relacionales seleccionados para el proyecto.

7. Definir criterios para clasificar los cambios como reversibles, parcialmente reversibles o potencialmente destructivos.

8. Identificar los requerimientos funcionales y no funcionales necesarios para desarrollar una herramienta de evaluación preventiva de cambios de bases de datos.


# Objetivo Solución


# Objetivo general de solución

Desarrollar y validar una herramienta que permita analizar preventivamente scripts y cambios estructurales de bases de datos antes de su despliegue, identificando riesgos relacionados con pérdida de datos, restricciones, dependencias, compatibilidad, índices y posibilidades de reversión.


# Objetivos específicos de solución

1. Implementar un módulo de conexión que permita obtener la estructura de bases de datos controladas utilizando como mínimo MySQL y PostgreSQL.

2. Implementar un extractor de metadatos capaz de identificar tablas, columnas, tipos de datos, claves primarias, claves foráneas, índices y restricciones de los esquemas seleccionados.

3. Implementar un analizador que permita identificar las operaciones estructurales contenidas en los scripts SQL incluidos en el alcance del proyecto.

4. Implementar un comparador de esquemas que detecte automáticamente diferencias entre una versión actual y una versión propuesta.

5. Implementar un motor configurable con al menos quince reglas de análisis de riesgo para evaluar los cambios detectados.

6. Detectar el 100 % de las eliminaciones de tablas y columnas incluidas en los escenarios controlados de prueba y verificar si los objetos contienen información antes de clasificarlos.

7. Implementar verificaciones de datos antes de operaciones seleccionadas, incluyendo detección de valores duplicados antes de incorporar restricciones UNIQUE, valores nulos antes de NOT NULL y longitudes incompatibles antes de reducir campos de texto.

8. Implementar un analizador de dependencias capaz de identificar relaciones mediante claves foráneas, índices y restricciones asociadas con los objetos seleccionados.

9. Implementar un análisis de índices que permita detectar su creación y eliminación y registrar las columnas involucradas.

10. Implementar un módulo de compatibilidad que identifique diferencias seleccionadas entre los dialectos de MySQL y PostgreSQL utilizados durante las pruebas.

11. Implementar una clasificación automática de cada modificación utilizando los niveles BAJO, MEDIO, ALTO y CRÍTICO según las reglas configuradas.

12. Generar un resultado general por análisis utilizando las categorías APROBADO, REQUIERE REVISIÓN y NO RECOMENDADO.

13. Implementar un mecanismo que genere o sugiera sentencias de rollback para las operaciones reversibles incluidas en el alcance del proyecto.

14. Identificar explícitamente aquellas modificaciones para las cuales una sentencia inversa no garantiza la recuperación de la información eliminada y recomendar la existencia de una copia de seguridad previa.

15. Implementar un historial de análisis que permita comparar diferentes versiones de un mismo script y visualizar la evolución del nivel de riesgo.

16. Implementar controles de acceso basados en roles para restringir las operaciones administrativas de configuración y conexión.

17. Implementar una bitácora de auditoría que registre el 100 % de los análisis definidos como operaciones críticas, incluyendo usuario, fecha, base evaluada y resultado.

18. Generar reportes que resuman los cambios encontrados, riesgos, dependencias, compatibilidad y recomendaciones obtenidas.

19. Evaluar SchemaSafe mediante como mínimo 50 escenarios controlados que incluyan modificaciones seguras, advertencias, cambios incompatibles y operaciones destructivas.

20. Validar que el sistema detecte correctamente el 100 % de los escenarios críticos previamente definidos para el conjunto controlado de pruebas.


# Resultado esperado del proyecto

Al finalizar el proyecto se espera disponer de un prototipo funcional de SchemaSafe capaz de conectarse con una base de datos de prueba, obtener su estructura actual y analizar un conjunto de modificaciones antes de que sean aplicadas.

Por ejemplo, el usuario podrá seleccionar una base de datos y proporcionar el siguiente cambio:

ALTER TABLE clientes
ALTER COLUMN nombre TYPE VARCHAR(20);

SchemaSafe consultará previamente la información almacenada y podrá encontrar:

Longitud máxima encontrada: 48 caracteres.
Nueva longitud propuesta: 20 caracteres.
Registros potencialmente afectados: 126.

El sistema generará:

RIESGO: ALTO

MOTIVO:
Existen datos que superan la longitud propuesta.

RESULTADO:
NO RECOMENDADO.

Otro escenario podrá evaluar:

ALTER TABLE usuarios
ADD CONSTRAINT uq_email UNIQUE(email);

SchemaSafe comprobará los datos actuales antes del cambio:

Correos duplicados encontrados: 18.

RESULTADO:
EL CAMBIO NO PUEDE SER RECOMENDADO HASTA CORREGIR LOS REGISTROS DUPLICADOS.

En un tercer escenario:

DROP COLUMN cliente.telefono;

SchemaSafe podrá determinar:

Registros con información: 4 852.
Dependencias encontradas: 2.
Tipo de operación: destructiva.

RIESGO: CRÍTICO.

Además, el sistema podrá generar un reporte consolidado con todos los problemas y conservar un historial para comparar diferentes versiones del script.

De esta manera, SchemaSafe permitirá demostrar que una sentencia SQL puede ser sintácticamente correcta pero operacionalmente riesgosa, proporcionando una herramienta preventiva para evaluar modificaciones antes de su despliegue.


# Referencias

PostgreSQL Global Development Group. (2026). PostgreSQL 18 Documentation: ALTER TABLE.

Oracle Corporation. (2026). MySQL 8.4 Reference Manual: ALTER TABLE Statement.

Liquibase. (2026). What is a rollback?

Liquibase. (2026). Rollback command documentation.

Universidad Privada de Tacna. (2026). Sílabo de Base de Datos II, SI-783.
