# proyecto-formatos-01

# Título del proyecto

**Sistema para el análisis preventivo de riesgos, impacto y compatibilidad de cambios estructurales en bases de datos antes de su despliegue**

# Nombre referencial del sistema

**SchemaSafe – Sistema de Evaluación Preventiva de Cambios en Bases de Datos**

# Planteamiento del problema

Las bases de datos son componentes fundamentales de los sistemas de información y, durante su evolución, requieren modificaciones como agregar o eliminar tablas y columnas, cambiar tipos de datos, incorporar restricciones, modificar índices o establecer nuevas relaciones.

Aunque una sentencia SQL sea sintácticamente correcta, esto no garantiza que pueda ejecutarse de manera segura sobre una base de datos que ya contiene información. Algunos cambios pueden ocasionar pérdida de datos, errores de integridad, incompatibilidades o problemas de rendimiento.

Por ejemplo, eliminar una columna puede provocar pérdida permanente de información; reducir el tamaño de un campo puede afectar registros existentes; agregar una restricción UNIQUE puede fallar si existen valores duplicados; y establecer NOT NULL puede resultar incompatible si existen registros con valores nulos.

Además, una modificación puede afectar otros elementos relacionados. Una columna puede estar asociada con claves foráneas, índices, restricciones o vistas, por lo que eliminarla o modificarla puede producir consecuencias sobre diferentes partes de la base de datos.

Otro problema es la compatibilidad entre gestores. MySQL y PostgreSQL, por ejemplo, presentan diferencias en determinados tipos de datos, instrucciones y características, por lo que un script diseñado para un gestor puede necesitar modificaciones para funcionar correctamente en otro.

También resulta importante determinar si un cambio puede revertirse. Algunas operaciones permiten generar una sentencia de rollback, mientras que aquellas que eliminan información pueden requerir una copia de seguridad para recuperar los datos.

Ante esta problemática se propone desarrollar **SchemaSafe**, una herramienta que permita analizar los cambios estructurales antes de aplicarlos. El sistema podrá conectarse a una base de datos de prueba o desarrollo, obtener su estructura actual y analizar un script SQL o una nueva versión del esquema.

SchemaSafe identificará cambios como:

* creación o eliminación de tablas;
* incorporación o eliminación de columnas;
* modificación de tipos y longitudes;
* creación o eliminación de claves y restricciones;
* creación o eliminación de índices;
* modificación de relaciones;
* renombrado de objetos.

Cada modificación será evaluada mediante reglas y clasificada en niveles de riesgo **BAJO, MEDIO, ALTO o CRÍTICO**.

El sistema también realizará comprobaciones sobre los datos existentes. Por ejemplo, antes de reducir un VARCHAR podrá verificar si existen valores que superen la nueva longitud; antes de agregar UNIQUE comprobará duplicados; y antes de aplicar NOT NULL verificará la existencia de valores nulos.

Asimismo, analizará dependencias entre tablas, claves, índices y restricciones, permitiendo advertir cuando un objeto que se desea modificar afecta otros componentes.

SchemaSafe incluirá un módulo básico de compatibilidad entre **MySQL y PostgreSQL**, capaz de advertir cuando determinadas instrucciones o características requieren adaptación.

También podrá generar o sugerir rollback para operaciones reversibles. En cambios destructivos advertirá que una sentencia inversa no garantiza la recuperación de los datos y recomendará disponer de una copia de seguridad.

Cada análisis generará un reporte con los cambios detectados, nivel de riesgo, dependencias, problemas encontrados y recomendaciones. El resultado general será clasificado como:

**APROBADO:** no se detectan riesgos importantes.

**REQUIERE REVISIÓN:** existen advertencias que deben evaluarse.

**NO RECOMENDADO:** existen cambios de riesgo alto o crítico.

El sistema almacenará además un historial de análisis para comparar diferentes versiones de un mismo script y observar si los riesgos detectados fueron corregidos.

Las pruebas se realizarán utilizando bases de datos controladas y datos generados específicamente para el proyecto, evitando afectar información real. Inicialmente se trabajará con MySQL y PostgreSQL.

También se implementarán usuarios, roles y auditoría de las operaciones principales. Siempre que sea posible, las conexiones utilizadas para el análisis tendrán permisos de lectura para reducir el riesgo de modificar accidentalmente la base evaluada.

De esta manera, SchemaSafe permitirá detectar modificaciones potencialmente peligrosas antes de su despliegue. Su finalidad no será reemplazar herramientas de migración ni limitarse a comprobar sintaxis SQL, sino evaluar el impacto que un cambio puede producir sobre una base de datos existente.

# Objetivos del Proyecto

# Objetivo general

Analizar los riesgos asociados con la aplicación de cambios estructurales sobre bases de datos con información existente, identificando modificaciones destructivas, incompatibilidades, dependencias y condiciones que puedan ocasionar fallos durante un despliegue.

# Objetivos específicos

1. Identificar como mínimo diez tipos de modificaciones estructurales que puedan afectar una base de datos.

2. Clasificar los cambios según su nivel de riesgo sobre la integridad, disponibilidad, compatibilidad y conservación de los datos.

3. Definir al menos quince reglas para detectar cambios potencialmente peligrosos.

4. Analizar dependencias entre tablas, columnas, claves, índices y restricciones.

5. Identificar modificaciones válidas sintácticamente que puedan fallar debido a los datos existentes.

6. Analizar diferencias relevantes entre MySQL y PostgreSQL.

7. Definir criterios para clasificar los cambios como reversibles, parcialmente reversibles o destructivos.

8. Identificar los requerimientos funcionales y no funcionales necesarios para desarrollar SchemaSafe.

# Objetivo Solución

# Objetivo general de solución

Desarrollar y validar una herramienta que permita analizar preventivamente scripts y cambios estructurales antes de su despliegue, identificando riesgos relacionados con datos, restricciones, dependencias, compatibilidad, índices y posibilidades de reversión.

# Objetivos específicos de solución

1. Implementar la conexión con bases de datos MySQL y PostgreSQL.

2. Obtener metadatos de tablas, columnas, tipos, claves, índices y restricciones.

3. Analizar las operaciones estructurales presentes en scripts SQL.

4. Comparar automáticamente el esquema actual con una versión propuesta.

5. Implementar al menos quince reglas para evaluar riesgos.

6. Detectar el 100 % de las eliminaciones de tablas y columnas incluidas en los escenarios controlados.

7. Verificar duplicados antes de UNIQUE, valores nulos antes de NOT NULL y longitudes incompatibles antes de reducir campos.

8. Detectar dependencias mediante claves foráneas, índices y restricciones.

9. Identificar la creación y eliminación de índices y las columnas relacionadas.

10. Detectar diferencias seleccionadas de compatibilidad entre MySQL y PostgreSQL.

11. Clasificar automáticamente los cambios como **BAJO, MEDIO, ALTO o CRÍTICO**.

12. Generar un resultado general como **APROBADO, REQUIERE REVISIÓN o NO RECOMENDADO**.

13. Generar o sugerir rollback para operaciones reversibles.

14. Advertir cuando un cambio destructivo requiera una copia de seguridad para recuperar información.

15. Mantener un historial para comparar diferentes versiones de un análisis.

16. Implementar usuarios y roles para controlar las funciones administrativas.

17. Registrar mediante auditoría los análisis realizados, usuario, fecha, base evaluada y resultado.

18. Generar reportes con cambios, riesgos, dependencias, compatibilidad y recomendaciones.

19. Evaluar el sistema mediante al menos 50 escenarios controlados.

20. Detectar correctamente el 100 % de los escenarios críticos previamente definidos para las pruebas.

# Resultado esperado del proyecto

Al finalizar el proyecto se espera contar con un prototipo funcional de **SchemaSafe**, capaz de conectarse a una base de datos de prueba, obtener su estructura y analizar modificaciones antes de que sean ejecutadas.

Por ejemplo, si se propone reducir un campo de **VARCHAR(100) a VARCHAR(20)**, SchemaSafe verificará previamente la longitud de los datos existentes. Si encuentra registros mayores al nuevo límite, clasificará el cambio como riesgo alto y recomendará no ejecutarlo hasta solucionar el problema.

De manera similar, antes de agregar una restricción UNIQUE podrá comprobar si existen valores duplicados, y antes de eliminar una columna podrá determinar si contiene información o presenta dependencias.

El sistema mostrará los riesgos encontrados, explicará sus causas, proporcionará recomendaciones y conservará un historial de los análisis realizados.

De esta manera, SchemaSafe permitirá demostrar que una sentencia SQL puede ser correcta sintácticamente pero representar un riesgo para una base de datos existente, ofreciendo una evaluación preventiva antes de su despliegue.

# Referencias

PostgreSQL Global Development Group. (2026). *PostgreSQL 18 Documentation: ALTER TABLE.*

Oracle Corporation. (2026). *MySQL 8.4 Reference Manual: ALTER TABLE Statement.*

Liquibase. (2026). *What is a rollback?*

Liquibase. (2026). *Rollback command documentation.*

Universidad Privada de Tacna. (2026). *Sílabo de Base de Datos II, SI-783.*

