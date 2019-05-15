## INGENIERÍA DE SISTEMAS
### Trabajo grupal 1
| Asignatura        | Valor           | Fecha  |
| ------------- |:-------------:| -----:|
| Adminitración de base de datos avanzada      | 10% |  **Abril/25/2019** |

El propósito de este trabajo es implementar un modelo de base de datos con su respectiva normalización, de acuerdo a los requerimientos del cliente.
**Importante:**
1. Hacerle [fork a este repositorio](https://help.github.com/en/articles/fork-a-repo#fork-an-example-repository)
2. Después de haber hecho _fork_, clonar el repositorio que se creó localmente, hacer el repositorio privado, agregar a los compañeros y mi usuario "amartinezg".
3. Clonar el proyecto en sus respectivas máquinas.
4. Crear una carpeta llamada **Assignment 1** y dentro de ella colocar todo lo requerido para la entrega del trabajo.
5. El repositorio deberá tener:
6. Cada integrante **deberá tener al menos un commit** resolviendo diferentes partes del taller. En caso de que alguno de los integrantes no lo tenga, se rebajarán 2 unidades sobre la nota definitiva del taller de dicho integrante.
---
### To do
1. Design (normalize) the problem below, add an Entity diagram class (Image).
2. Create 3 tablespaces
    * First one with 3Gb and 1 datafile, the name of the tablespace should be **"coordinadora"**
    * Undo tablespace with 100Mb and 1 datafile (Set this tablespace to be used in the system)
    * Bigfile tablespace of 4Gb
3. Create an user with the username *amartinezg*.
    * The user should be assigned to the tablespace.
    * There are no restrictions of space for this user.
    * The role of the user should be _dba_
    * The user should be able to connect to the system.
4. Create 3 profiles:
    * Profile 1: **clerk** with a password life of 40 days, one session per user, 15 minutes of idle, 3 failed login attempts.
    * Profile 2: **development** with a password life of 100 days, two sessions per user, 30 minutes of idle and no failed login attempts.
    * Profile 3: **operative** with a password life of 30 days, one session per user, 5 minutes of idle, 4 failed login attemps. This profile can reuse the password after 30 days if the password has already been changed 3 times.
5. Create 3 users:
   * You are free to pick the username
   * One user should user the profile **clerk**, the second one **development** and the last one **operative**
   * All of them should be able to connect to the database.
   * The user with the profile **operative** should be locked.
6. Create all tables shown in the ERD (Entity relationship diagram) using the user **amartinez**:
    * Create primary and foreign keys according to the naming conventions.
    * Insert data
    * Export data using Sql developer.
7. Create a RMAN backup and take a screenshot of the list of backups in the console.
8. Read the following articles and write an essay of each of them:
    * [Who Needs Stored Procedures, Anyways?](https://blog.codinghorror.com/who-needs-stored-procedures-anyways/)
    * [Maybe Normalizing Isn't Normal](https://blog.codinghorror.com/maybe-normalizing-isnt-normal/)
    * [10 Reasons To Consider A Multi-Model Database](http://highscalability.com/blog/2015/3/4/10-reasons-to-consider-a-multi-model-database.html)
---
### Problema
Coordinadora ha decidido contratar a su equipo para la modernización de sus sistemas y poder integrar en un solo software, diferentes aplicaciones que tienen en su arquitectura actual, la idea es migrar algunas aplicaciones _legacy_ para tener una interfaz más moderna y que soporte las transacciones que desean. Por esta razón requieren diseñar una base de datos que soporte lo descrito a continuación.

Todas las operaciones son asociadas a un código postal, cada municipio puede tener diferentes códigos postales y es bien sabido que un municipio pertenece a un departamento. Los datos de códigos postales se encuentran [acá](data/C_digos_Postales_Nacionales.csv). Los [centros de recibo](http://www.coordinadora.com/centros-de-recibo/) están estratégicamente ubicados y pueden ser de dos tipos, convencionales o terminales de carga que son aquellas donde llega toda la mercancía para luego ser distribuida a los centros de recibo más cercanos a las direcciones de destino. Para cada centro de carga se guarda la ciudad donde está ubicada, el nombre del centro de recibo, la dirección, el teléfono y el empleado encargado.

Para los empleados se desea almacenar la siguiente información: nombre del empleado, cédula de ciudadanía, código del empleado, dirección residencia, teléfono, salario, jefe directo, cargo y el centro de recibo asociado a dicho empleado. El personal de la carga administrativa está asociado a la terminal de carga de la ciudad de Medellín que es donde quedan las instalaciones principales de la compañía.

Coordinadora tiene el inventario de sus vehículos, la información que se almacena de cada vehículo es: marca, modelo, línea, tipo de combustible (diesel, gasolina), kilometraje, capacidad (2.5 TON, 2.8 TON, 3.3 TON, 3.5 TON, 5 TON, 6 TON, 11 TON y 700Kg). Cada vehículo está asociado a una terminal de carga.

Las directivas han decidido utilizar la base de datos para llevar el control y los planes de mantenimiento de los vehículos, este plan de mantenimiento se realiza por kilometraje y va de la siguiente manera, es decir; cada plan de mantenimiento tiene varios items o varios elementos a ser revisados. El modelo debe ser lo suficientemente dinámico para soportar nuevos planes que sean necesarios, los siguientes son unos posibles ejemplos de escenarios que podrían presentarse:

* Cada 5.000 Kilómetros:
    - Cambiar el aceite y el filtro del mismo y el del aire
    - Revisar el aire de las llantas y el nivel de deterioro de la banda de rodamiento
    - Comprobar la tensión del embrague o freno y de la correa del motor
    - Verificar que las luces y el funcionamiento de los comandos del tablero.
    - Mirar que el líquido de frenos esté en el nivel adecuado.

* Cada 10.000 Kilómetros
    - Funcionamiento de las luces y del tablero de mando.
    - Estado de alternador y arranque
    - Desgaste de articulaciones y rotulas y el estado del guardapolvos
    - Sistema de admisión, mangueras y tuberías
    - Revisión del nivel de los líquidos (Frenos, agua o refrigerante, aceite, líquido de motor, etc.
    - Realizar lavado del motor
    - Revisar tensión de freno de pie y de mano
    - Alineación, balanceo y rotación de las llantas para garantizar un desgaste parejo.
    - Inspección del sistema eléctrico.

* Cada 20.000 Kilómetros
    - Cambiar aceite y su filtro
    - Reposición del Filtro de aire y de combustible
    - Sustitución de las pastillas de frenos y de bandas traseras
    - Cambio de aceite de dirección, transmisión y caja de velocidades
    - Calibrar las llantas y rotarlas nuevamente
    - Calibrar los frenos
    - Verificar la tensión del embrague y las correas de motor
    - Alinear y balancear el auto
    - Engrasar los rodamientos de las llantas de atrás

* Cada 40.000 kilómetros
    - Debes repetir el mantenimiento de los 30.000 kilómetros y adicionalmente hacer el mantenimiento y sondeo del radiador

* Cada 50.000 Kilómetros
    - Repetir el mantenimiento de los 30.000 kilómetros y adicionalmente sincronizar el motor de inyección o de  carburador y ajustar la suspensión.

* Cada 100.000 Kilómetros
    - Alinear el vehículo
    - Sincronizar el motor
    - Ajustar la suspensión
    - Cambiar las pastillas y el líquido de frenos, bujías de encendido, cables de alta y correas
    - Sustituir el líquido refrigerante, de radiador, aceite y su filtro.
    - Realizar peritaje especializado para prevención de fallas.

La idea es que cuando se le haga el mantenimiento a un vehículo, se debe almacenar: fecha del mantenimiento, hora de entrada, hora de salida, empleado encargado. Cuando se realiza el mantenimiento a un vehículo, se debe asociar con el plan de mantenimiento requerido. Posteriormente se deben relacionar las revisiones hechas de acuerdo al plan de mantenimiento (Alineación, Sincronización, Ajuste Suspensión), para cada una deberá guardarse el estado *(por defecto tendrá "No realizado")* y una columna de observaciones. Así, cuando se realice el mantenimiento, esta información será enviada a la base de datos. Ejemplo:

| Vehículo | Fecha Mantenimiento | Estado  | Empleado Encargado | Mantenimiento |
| ------------- |:-------------:| :-----:| :----: | :---: |
| RGH-345 | **Abril/25/2019** | Realizado | Andres Martinez | Revisión 5000 km |

*Detalle*

| Revisión | Estado | Observaciones |
|--------|:----:|:---:|
| Cambio aceite | Realizado ||
| Cambio Filtro aceite | Realizado ||
| Cambio Filtro Aire | Pendiente ||
| Revisión presión llantas | Pendiente ||
| Revisión Banda Rodamiento | Realizado | Se realiza cambio de la banda por desgaste prematuro |


Cuando se envía la mercancía, se genera un número de guía que identifica cada despacho, se debe almacenar: peso real, ancho, largo y alto del paquete en centímetros, peso volumen (se obtiene multiplicando el ancho x el alto x el largo y luego se multiplica por 400 que es el factor de equivalencia por cada metro cúbico), tipo de servicio (carga aérea, mercancía, mensajería, firma de documentos, radicación de documentos), nombre, cédula o NIT, teléfono y dirección (tanto del remitente como del destinatario), fecha y hora del despacho, fecha y hora de la entrega, observaciones, ciudad origen, ciudad destino, código postal origen, código postal destino, unidades, flete fijo, flete variable, otros valores, valor del servicio, estado.

Cada guía es posible rastrearla, por eso se debe almacenar los diferentes estados que ha tenido, los posibles estados son: A recibir, en terminal origen, en transporte, en terminal destino, en reparto, entregada. También se debe almacenar la fecha y la hora en la cual se crea un nuevo estado.

---
### Observaciones

* Debe usarse la opción "Default value" en aquellas columnas que tengan valores por defecto.
* _Mi recomendación personal_ es que cuando las tablas queden con 2 columnas o 1 columna sin contar la clave primaria, es posible desnormalizar siempre y cuando en la tabla que vaya a contener esa columna, tenga una restricción de tipo CHECK que solo acepte los valores requeridos https://www.techonthenet.com/oracle/check.php
* No olvidar las buenas prácticas para el nombramiento de las cosas, tablas en plural, todas las tablas con una columna ID de tipo entero. Claves foráneas bien nombradas, entre otros.
* Si la tabla se llama "CLIENTES", no es necesario que las columnas tengan algún sufijo que relacione la tabla, tales como: teléfono_cliente, nombre_cliente, apellido_cliente. Ya se asume que estos campos son de un cliente en específico. Si la columna no coincide con lo referido en la tabla, es probable que se encuentre en la tabla incorrecta o el modelo se encuentre mal normalizado.


### Trabajo grupal 2
| Asignatura        | Valor           | Fecha  |
| ------------- |:-------------:| -----:|
| Adminitración de base de datos avanzada      | 20% |  **Junio/2/2019 12:00 (Mediodía)** |

El propósito de esta entrega es practica la creación de las vistas, funciones, procedimientos almacenados, manipulación de datos y crear un modelo de base de datos no relacional con una base de datos documental; utilizando el modelo entidad relación de la pasada práctica.

**Importante:**
1. Crear una carpeta llamada **Assignment 2** y dentro de ella colocar todo lo requerido para la entrega del trabajo.
2. El repositorio deberá tener:
   - Archivo con extensión .sql el cual tendrá toda la lógica.
   - Video exponiendo el (Más detalles abajo)
   - Diagrama entidad relación en extensión .pdf o .jpg .png si ha tenido cambios con respecto a la entrega pasada.
3. Cada integrante **deberá tener al menos un commit** resolviendo diferentes partes del taller. En caso de que alguno de los integrantes no lo tenga, se rebajarán **2 unidades** sobre la nota definitiva del taller de dicho integrante.
---
### To do

(Realizar las correcciones pertinentes en caso de que hubiere lugar al modelo entidad relación presentado en el trabajo anterior. Hay cosas opciones y hay cosas obligatorias, en caso de no seguir las recomendaciones es probable que tengan dificultad para construir los queries necesarios para extraer los datos.)
1. Crear datos para la solución de problemas, para esto pueden utilizar [mockaroo.com](https://mockaroo.com/), lo mínimo requerido es:
   * 50 empleados
   * 100 clientes
   * 10 vehículos
   * Los centros de recibo reales de Coordinadora https://www.coordinadora.com/centros-de-recibo/
   * Los planes de mantenimiento expuestos en el problema pasado.
   * 500 envíos o guías entre diferentes ciudades y en diferentes estados (La mayoría que estén en estado "pendiente" o "por enviar")
2. Crear una vista llamada "plan_mantenimiento_detallado" el cuál deberá mostrar que cosas se realizan en ese plan de mantenimiento. La idea es invocarlo de esta manera "select * from plan_mantenimiento_detallado where kilometraje = 5000" (Ya usted decide si quiere crear una columna numérica o si quiere manejarlo usando LIKE en el campo del nombre del mantenimiento)


| Kilometraje   | Item          |
| ------------- |-------------| 
| 5000          | Cambio aceite |
| 5000          | Cambio filtro aceite |
| 5000          | Cambio filtro aire |
| 5000          | Aire llantas |
| 5000          | Nivel deterioro banda rodamiento |
| 5000          | Tensión embrage |
| 5000          | Tensión freno |
| 5000          | Tensión correa motor |
| 5000          | Verificación luces |
| 5000          | Verificación comandos tablero |
| 5000          | Verificación niveles líquido freno |
