![logo de Zap](/assets/icons/zap.svg) 
# Zed Attack Proxy

(Nivel básico)

Sección de Seguridad de la información - Rodrigo Castro Díaz

## Agenda

- Introducción a ZAP
- Detalles de configuración
- Escaneos pasivos y activos
- Reportes

## ¿Qué es ZAP?

Zed Attack Proxy (ZAP) es una herramienta gratuita de pruebas de penetración de código abierto que hace parte del Open Web Application Security Project (OWASP). ZAP está diseñado específicamente para probar aplicaciones web y es flexible y extensible.

ZAP es un proxy de estilo hombre en el medio

![proxy in the middle](/assets/images/browser-no-proxy.png)

Si ya hay otro proxy de red en uso, como en muchos entornos corporativos, ZAP se puede configurar para conectarse a ese proxy.

![zap en entornos corporativos](/assets/images/browser-network-proxy.png)

###### Notas:

ZAP es un proxy de uso al estilo hombre en el medio, es decir que ZAP funciona poniéndose entre el navegador y la aplicación interceptando las peticiones y respuestas de la aplicación web incluso en entornos corporativos

## Detalles iniciales

### ¿Cómo obtener ZAP?

ZAP requiere Java 8+ para ejecutarse
  
[Descarga instalador ZAP](https://www.zaproxy.org/download/)
[kali linux](https://www.kali.org/)

###### Notas:
ZAP es una aplicación java, por lo tanto requiere que el sistema operativo tenga instalado java 8 o superior, por lo tanto podemos conseguir instaladores para los principales sistemas operativos e incluso en Docker

También viene incluida en diversas distribuciones de pentesting y seguridad informática como Kali Linux o Backbox

## Antes de usar

¿De qué manera voy a usar ZAP?¿Cómo proxy local, cómo servidor de pruebas?

###### Notas:

Debemos definir el uso que haremos de la herramienta para poder aprovechar al máximo sus capacidades, es posible que solo pensemos en probar la herramienta o realizar una prueba básica, en este caso las opciones y configuraciones por defecto serán más que suficientes

### Ajustes de la JVM antes de lanzar ZAP

- Ajustes de heap

~~~js
-Xmx1024m
~~~

[JVM options](https://www.zaproxy.org/docs/desktop/ui/dialogs/options/jvm/)

###### Notas:

Para sacarle el máximo provecho se permite establecer el tamaño máximo del grupo de asignación de memoria de Java para la ejecución de ZAP.

El heap es una estructura de datos especial de la JVM y es critica para las aplicaciones porque cuando se desborda el heap la aplicación falla, por defecto estos valores suelen ser pequeños, y dependiendo del uso que le demos puede ser necesario personalizar los valores.

Dependiendo del sistema operativo, esta configuración puede variar, por lo que es aconsejable consultar la documentación oficial

- Garbage Collector

~~~js
-XX:+UseSerialGC
-XX:+UseParallelGC
-XX:+UseConcMarkSweepGC //en desuso desde java 9
-XX:+UseG1GC
-XX:+UseZGC // recomendado en java 11
~~~

###### Notas:

Seleccionar correctamente el Garbage Collector dependiendo de la versión de JVM también impacta al funcionamiento de la herramienta

El Garbage Collector (o recolector de basura) es uno de los elementos fundamentales en la JVM se encarga de recolectar aquellos elementos que ya no son usados dentro del programa, es una procedimiento que se ejecuta de manera automática.

El algoritmo que escojamos dependerá del equipo. Si se tiene alguna restricción a nivel de hardware probablemente los primeros (serial y parallel) serán los más indicados, si la maquina es potente y el uso de la aplicación lo requiere es posible que la mejor opción sea usar los últimos (G1 o ZGC), en la versión 11 el recomendable es usar ZGC.

### Configurar proxy

Por defecto, ZAP usa la dirección de 'localhost' y el puerto '8080', pero estos se pueden cambiar

[Configuring proxies](https://www.zaproxy.org/docs/desktop/start/proxies/)

###### Notas:
Generalmente no hay problema pero en algunos navegadores actuales para Windows, se requiere de algunas configuraciones adicionales para permitir la captura del trafico de localhost.

## Primeros pasos con ZAP

![Ventana inicio de sesión](/assets/images/inicio_sesion.png)

###### Notas:
Cuando inicia ZAP, preguntará si desea mantener la sesión. De forma predeterminada, las sesiones se graban en el disco en una base de datos HSQLDB con un nombre y una ubicación predeterminados. Si no se desea permanecer en la sesión, esos archivos se eliminan cuando sale de ZAP.

![Actualización de inicio](/assets/images/Actualizar-complementos.png)

###### Notas:

Cuando inicia ZAP por primera vez, mostrara una ventana para la actualización de los componentes de la aplicación, se recomienda actualizar todos.

Tip: En esta ventana podemos ir a la pestaña de mercado para instalar complementos adicionales y expandir funcionalidades de la herramienta

### Addons

- Python Scripting y/o Ruby Scripting
- Tree Tools
- Community Scripts
- CustomReport
- Export Report
- Requester

###### Notas:

Estos son algunos de los complementos adicionales que podemos instalar cuya descripción encontraremos en la ventada de la aplicación

### Interfaz de usuario

![Interfaz de usuario de ZAP](/assets/images/ui-zap.png)

###### Notas:

La interfaz de usuario de ZAP se compone de los siguientes elementos:

1. **Menú:** brinda acceso a muchas de las herramientas automáticas y manuales.
2. **Barra de herramientas:** incluye botones que facilitan el acceso a las funciones más utilizadas.
3. **Ventana de árbol:** muestra el árbol de Sitios y el árbol de Scripts.
4. **Ventana del área de trabajo:** muestra solicitudes, respuestas y scripts y le permite editarlos.
5. **Ventana de información:** muestra detalles de las herramientas automáticas y manuales.
6. **Pie de página:** muestra un resumen de las alertas encontradas y el estado de las principales herramientas automatizadas.

### Escaneo automatizado

![Escaneo automatizado](/assets/images/automated-scan.png)

###### Notas:

Para ejecutar un escaneo automatizado de inicio rápido:

1. Inicie ZAP y haga clic en la pestaña **Inicio rápido** de la ventana del área de trabajo.
2. Haga clic en el botón grande de **Escaneo automatizado**.
3. En el cuadro de texto de **URL para atacar**, ingrese la URL completa de la aplicación web que desea atacar.
4. Haga clic en el botón de **ataque**

ZAP procederá a analizar la aplicación web y realizara un escaneo pasivo con un spider tradicional. Luego, ZAP utilizará el escáner activo para atacar todas las páginas, funcionalidades y parámetros descubiertos.

ZAP proporciona 2 spiders para rastrear aplicaciones web, puede usar una o ambas desde esta pantalla.

El spider tradicional de ZAP descubre enlaces examinando el HTML en las respuestas de la aplicación web. Es rápida, pero no siempre es efectiva cuando se explora una aplicación web AJAX que genera enlaces usando JavaScript.

Para las aplicaciones AJAX, es probable que el spider AJAX de ZAP sea más eficaz. Es más lenta que el spider tradicional.

ZAP primero escaneará pasivamente todas las solicitudes y respuestas enviadas a través de él, luego se realiza un análisis activo que intenta encontrar otras vulnerabilidades mediante ataques conocidos.

### Alertas

![Alertas](/assets/images/alertas.png)

###### Notas:

Para ver las alertas creadas durante su prueba:

1. Haga clic en la pestaña Alertas en la ventana de información.
2. Haga clic en cada alerta que se muestra en esa ventana para mostrar la URL y la vulnerabilidad detectada en el lado derecho de la Ventana de información.
3. En las ventanas del área de trabajo, haga clic en la pestaña Respuesta para ver el contenido del encabezado y el cuerpo de la respuesta. Se resaltará la parte de la respuesta que generó la alerta.

### Exploración manual

![Exploración manual](/assets/images/manual-explore.png)

###### Notas:

Para explorar manualmente una aplicación:

1. Inicie ZAP y haga clic en la pestaña Inicio rápido de la ventana del área de trabajo.
2. Haga clic en el botón grande Exploración manual.
3. En el cuadro de texto URL para explorar, ingrese la URL completa de la aplicación web que desea explorar.
4. Seleccione el navegador que le gustaría usar
5. Haga clic en Iniciar navegador

### ZAP HUD

![HUD, escaneo manual](/assets/images/hud.png)

###### Notas:

El Heads Up Display (HUD) es una nueva interfaz que brinda acceso a la funcionalidad ZAP directamente en el navegador. Es ideal para personas nuevas en la seguridad web y también permite que los probadores de penetración experimentados se centren en la funcionalidad de una aplicación.

## Reporte

![Reporte generado por ZAP](/assets/images/reporte.png)

###### Notas:

Para generar un reporte:

1. Abrir el menú Reporte
2. Escoger el tipo de informe a generar

Las opciones que maneja este menú son:

- **Generar informe HTML:** genera un nuevo informe HTML que contiene las alertas generadas.
- **Generar informe XML:** genera un nuevo informe XML que contiene las alertas generadas.
- **Generar informe Markdown:** genera un nuevo informe de Markdown que contiene las alertas generadas.
- **Exportar mensajes a archivo:** permite guardar solicitudes y respuestas en un archivo de texto. Seleccione los mensajes para guardar en la pestaña Historial; use la tecla Mayús para seleccionar varios mensajes.
- **Exportar respuesta a archivo:** Esto le permite guardar una respuesta específica en un archivo. Seleccione el mensaje relevante en la pestaña Historial; tenga en cuenta que las respuestas binarias (como imágenes) se pueden guardar, así como las respuestas de prueba.
- **Exportar todas las URL a archivo:** Esto le permite guardar todas las URL a las que se accede en un archivo de texto o HTML. Esto se puede utilizar, entre otras cosas, para comparar las URL disponibles para usuarios con diferentes roles o permisos en el mismo sistema.
- **Exportar las URL seleccionadas a un archivo:** Según la selección (incluida la selección múltiple) en el árbol de Sitios, se exportan todas las URL y las URL secundarias de los nodos seleccionados.
- **Exportar URL para contexto:** Se exportan todas las URL del árbol Sitios que se encuentran dentro del contexto seleccionado. Esta funcionalidad también está disponible en el menú contextual cuando se usa en un nodo Contexto en el panel del árbol Sitios.
- **Comparar con otra sesión:** Esto le solicita una sesión ZAP que haya guardado previamente. Luego le solicita un archivo de salida en el que se escriben todas las URL a las que accede la sesión actual y la sesión que ha seleccionado para compararla. El archivo contendrá una tabla que enumera las URL y las respuestas HTTP para las URL en las 2 sesiones. Los botones de JavaScript le permiten mostrar todas las URL, solo aquellas a las que se accede en la primera sesión, la segunda sesión y aquellas a las que se accede por ambas sesiones. Esto es particularmente útil para comparar 2 sesiones que acceden a la misma aplicación utilizando diferentes usuarios. Podrá ver qué URL son visibles para los usuarios y podrá intentar acceder a todas las URL cuando inicie sesión como cualquiera de los usuarios.

## Recursos

-  [Sitio oficial](https://www.zaproxy.org/)
- [Documentación oficial](https://www.zaproxy.org/docs/)
- [OWASP Latam](https://www.youtube.com/c/OWASPLATAM/featured)
