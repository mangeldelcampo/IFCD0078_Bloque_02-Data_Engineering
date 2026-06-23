# DP-900 - Lab 01 - Azure Database for PostgreSQL
## Ejercicio: Creación y uso de una base de datos en Azure Database for PostgreSql

## Resumen 
Este laboratorio documenta el procedimiento técnico para el aprovisionamiento y la puesta en marcha de una solución de base de datos relacional de código abierto utilizando el servicio Azure Database for PostgreSQL. La práctica detalla el despliegue bajo el modelo de Servidor Flexible (Flexible Server), una arquitectura de Microsoft Azure diseñada para proporcionar un control granular sobre las variables del motor, optimización financiera en cargas de trabajo de desarrollo y mecanismos avanzados de conectividad híbrida.

El documento abarca el flujo completo desde el plano de control: validación de requisitos de suscripción, asignación de identidades lógicas y contenedores de recursos, parametrización de red perimetral mediante el puerto nativo de PostgreSQL (5432) y el establecimiento de políticas de acceso seguro e integridad en la nube de Azure.


## Puntos Clave del Laboratorio

### 1. Configuración del Entorno y Modelo de Despliegue

* **Arquitectura de Servidor Flexible:** Se selecciona específicamente la opción de Azure Database for PostgreSQL Flexible Server. Esta modalidad PaaS (Plataforma como Servicio) ofrece a los desarrolladores un control simplificado, compatibilidad nativa con las extensiones de la comunidad y un aislamiento completo de la infraestructura.
* **Contenedores de Gobernanza:** La infraestructura lógica se asocia a una suscripción activa (Azure for Students) y se encapsula dentro de un Grupo de Recursos dedicado (LaboratorioAdnan) para unificar la monitorización y el ciclo de vida de los componentes.
* **Parámetros de Ubicación:** El servidor se despliega en la región geográfica UK South, asociándose a una versión de motor estable (PostgreSQL 17).

### 2. Optimización Financiera y de Carga

* **Perfil Dev/Test:** El tipo de carga de trabajo se clasifica explícitamente como Desarrollo. Esta opción restringe el uso de recursos masivos para evitar costes innecesarios en fases preliminares de diseño.
* **Disponibilidad Estándar:** Con el fin de minimizar el consumo de créditos, la alta disponibilidad con redundancia de zona se mantiene desactivada (Disabled), operando bajo un Acuerdo de Nivel de Servicio (SLA) básico del 99.9%.

### 3. Seguridad Perimetral y Autenticación

* **Firewall y Restricción de Puerto:** Se habilita la conectividad por Acceso público protegiendo el endpoint mediante el cortafuegos de Azure. El tráfico entrante se canaliza estrictamente a través del puerto estándar 5432.
* **Control por Dirección IP:** Se restringe el perímetro de red añadiendo de forma exclusiva la dirección IP pública del cliente actual (79.116.248.142) a las reglas de firewall. Cualquier conexión externa ajena a este origen es bloqueada por defecto.
* **Identidad y Acceso:** La plataforma ofrece un modelo híbrido que permite seleccionar entre la Autenticación nativa de PostgreSQL (mediante un usuario administrador local como adnan) y la integración centralizada con Microsoft Entra ID mediante tokens temporales.

### 4. Ciclo de Vida y Despliegue Automatizado

* **Aprovisionamiento Atómico:** Tras la validación de la pestaña Revisar y crear, el motor de Azure despliega en cascada todos los recursos de red y discos físicos requeridos.
* **Acceso Inmediato al Recurso:** Una vez que la interfaz confirma que Se completó la implementación, el plano de control habilita el botón Ir al recurso, permitiendo al administrador el acceso directo al panel centralizado para gestionar las bases de datos lógicas individuales.


## Ejecución Ejercicio 
### Paso 1: Acceso al portal de Azure
Ingresa al portal de Azure. Portal de Azure e iniciamos sesión con nuestras credenciales.

### Paso 2: Crear recurso de Azure Database for PostgreSql
En el portal, seleccionamos Crear un recurso.
Buscamos Azure Database for PostgreSQL y seleccionamos la opción Azure Database for PostgreSQL.
* Hacemos clic en Crear.

![Acceso al Marketplace y selección de Azure Database for PostgreSQL](imagenes/E3Imagen01.png)

### Paso 3: Configuración del recurso
* Rellenamos los campos con las opciones que deseemos, como:
  * Nombre del servidor.
  * Tipo de suscripción.
  * Grupo de recursos (puede ser uno nuevo o existente).
  * Ubicación (región).
  * Versión de PostgreSQL.
* Establecemos el tipo de autenticación que queramos (por ejemplo, autenticación SQL).
* En la sección de redes, agregamos nuestra IP actual para permitir el acceso desde nuestra ubicación.
* Finalmente, revisamos toda la configuración y hacemos clic en Revisar y crear.

#### Configurar la base de datos
En la página Crear base de datos SQL, introduce los siguientes valores:
* **Suscripción:** selecciona tu suscripción activa de Azure.
* **Grupo de recursos:** crea un nuevo grupo `RGbdPostgreSQL`.
* **Nombre del servidor:** escribe un nombre único para el servidor `srvdbsqldatabase`.
* **Región:** Spain Central.
* **Versión de PostgreSQL:** déjala sin cambios. 16.
* **Tipo de carga de trabajo:** selecciona para proyectos de desarrollo o hobby. Dev/Test.
* **Computación + almacenamiento:** déjalo sin cambios. Burstable, B2s, 2 vCores, 4 GiB RAM, 32 GiB storage, P4 (120 IOPS). Storage autogrow: Not enabled. Geo-redundancy: Not enabled.
* **Zona de disponibilidad:** déjala sin cambios. No preference.
* **Habilitar alta disponibilidad:** déjala sin cambios. Not enabled.
* **Nombre de usuario del administrador:** ingresa el nombre de usuario `mgueldba` que quieras usar.
* **Contraseña y Confirmar contraseña:** establece una contraseña segura. Luego, selecciona Siguiente: Redes.

![Configuración de los parámetros básicos e identificación del servidor](imagenes/E3Imagen02.png)
![Configuración de los métodos de autenticación del administrador](imagenes/E3Imagen03.png)

### Paso 4: Configurar reglas de firewall
En la sección Reglas de firewall, selecciona **+ Agregar dirección IP del cliente actual** para permitir el acceso desde tu equipo (IP detectada: `213.0.69.114`). Esto generará una regla automática denominada `ClientIPAddress_2026-6-19_19-59-54`.

![Configuración de la seguridad de red y reglas de firewall](imagenes/E3Imagen04.png)

### Paso 5: Revisar y crear
Selecciona **Revisar + Crear** para validar la configuración.
Luego, haz clic en **Crear** para iniciar la implementación.

![Resumen general de la configuración para validación](imagenes/E3Imagen05.png)

### Paso 6: Esperar a la implementación
Espera a que la implementación se complete. Cuando termine, selecciona **Ir al recurso** para acceder al servidor PostgreSQL recién creado.

![Progreso en curso del despliegue del recurso](imagenes/E3Imagen06.png)
![Confirmación de implementación completada con éxito](imagenes/E3Imagen07.png)

### Paso 7: Explorar y administrar el recurso
Desde la página del recurso, puedes revisar todas las opciones avanzadas en la barra lateral izquierda para administrar tu Azure Database for PostgreSQL Flexible Server:

#### 1. Configuración de Infraestructura y Rendimiento (Settings)
* **Compute + storage:** Permite escalar la base de datos verticalmente (modificando núcleos virtuales, memoria RAM, almacenamiento en disco o IOPS aprovisionados) adaptándose al volumen transaccional demandado.
* **Databases:** Sección para la manipulación y gestión directa de las bases de datos lógicas individuales alojadas dentro de la instancia del servidor.

#### 2. Seguridad, Redes y Conectividad
* **Networking:** Permite reconfigurar las directivas de filtrado IP público por Firewall y habilitar accesos privados herméticos mediante endpoints o redes virtuales de Azure (VNet Integration).
* **Connect:** Proporciona las directrices de conexión listas para su inserción en diferentes entornos lógicos de desarrollo (Java, .NET, Python, Node.js) y los certificados SSL necesarios para cifrar las comunicaciones.

#### 3. Ajustes del Motor y Comportamiento de Datos
* **Server parameters:** Panel técnico para la manipulación de variables del motor (`postgresql.conf`), tales como configuraciones del planificador de consultas, buffers de memoria, zonas horarias predeterminadas y codificaciones locales.
* **Replication:** Provee herramientas para la orquestación de réplicas de lectura secundarias para distribuir cargas analíticas intensivas sin mermar la instancia primaria.

#### 4. Resiliencia, Mantenimiento y Continuidad de Negocio
* **Maintenance:** Agenda ventanas operativas para la aplicación automática e invisible de parches de seguridad a nivel de sistema operativo y kernel de base de datos.
* **High availability:** Permite activar configuraciones robustas de conmutación por error ante desastres en zonas de disponibilidad redundantes.
* **Backup and restore:** Panel especializado en la gobernanza de las copias de seguridad de retención automática (Point-in-Time Restore) para restaurar el estado íntegro de la instancia a cualquier minuto deseado del histórico almacenado.

![Panel de control principal del servidor PostgreSQL aprovisionado](imagenes/E3Imagen08.png)