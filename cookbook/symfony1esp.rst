En que difiere Symfony2 de symfony1
==================================

El framework Symfony2 plasma una significante evolución cuando se compara con la primera versión del framework. Afortunadamente con la arquitectura MVC como núcleo, las habilidades  y conocimientos usados para hacer buenos proyectos en symfony1 siguen siendo muy relevantes cuando desarrollamos en Symfony2.
En este capítulo, vamos a caminar a través de las diferencias entre symfony1 y Symfony2. Como podremos ver,   muchas tareas son abordadas de una manera ligeramente diferente.  Usted llegará a apreciar estas diferencias de menor importancia ya que promueven el código estable, predecible, verificable y disociada en sus aplicaciones Symfony2.
Entonces, siéntese y relájese,  mientras lo llevamos del antes al ahora.

Estructura del directorio
-------------------------

Cuando le damos un vistazo a un proyecto de Symfony2  – por ejemplo the Symfony2 Sandbox (No sirve ese link), usted se dará cuenta que la estructura de directorios de symfony es muy diferente. Las diferencias, sin embargo, son algo superficiales.

El directorio ``app/`` 
~~~~~~~~~~~~~~~~~~~~~~

En symfony1, un proyecto tiene una o mas aplicaciones y cada una esta dentro del directorio  apps/ (Por ejemplo. apps/frontend).  Por defecto en Symfony2, se tiene una sola aplicación representada por el directorio app/.  Como en symfony1, el directorio app/  contiene configuración especifica de la aplicación. Éste También contiene caché específico de la aplicación, registro y directorios de plantillas,  así como la clase Kernel (AppKernel),  el cual es el objeto base que representa a la aplicación. 
A diferencia de symfony1, casi no hay codigo PHP en el directorio app/.  Este directorio no se usa como casa de módulos o librerías de archivos como lo hacia en symfony1. En cambio, es simplemente el “hogar” de la configuración y otros recursos (plantillas, archivos de traducción).


El directorio ``src/`` 
~~~~~~~~~~~~~~~~~~~~~~

En pocas palabras, su verdadero código va aquí. En Symfony2, todo el código de la aplicación   está dentro de un bundle (aproximadamente equivalente a un plugin en symfony1) y, por defecto, cada bundle está dentro del directorio src.  De esta manera, el directorio src es un poco como el directorio de plugins en symfony1, pero mucho más flexible. Además, mientras sus bundles estén  en el directorio src /, los bundles de otros fabricantes pueden estar en el directorio vendor /.

Para obtener una mejor imagen de el directorio src /, primero piense en una aplicación symfony1. En primer lugar, parte de su código habita dentro de una o más aplicaciones. Lo más común son los módulos, pero también podría incluir otras clases de PHP que usted tiene en su aplicación. Es posible que también haya creado un archivo schema.yml en el directorio de configuración de su proyecto. Por último, para ayudar con algunas funcionalidades comunes, usted está usando varios plugins de terceros que están en el directorio  plugins /. En otras palabras, el código que impulsa su aplicación a la vida habita en muchos lugares diferentes.

En Symfony2, la vida es mucho más simple porque todo el código Symfony2 debe estar en un bundle. En nuestro posible proyecto symfony1, todo el código podría ser trasladado entre uno o más plugins (que es una práctica muy buena, de hecho). Suponiendo que todos los módulos, clases de PHP, el esquema, configuración de enrutamiento, etc fueron movidos a un plugin, el  directorio plugins/ symfony1  debe ser muy similar al directorio src/ en Symfony2.
 
En pocas palabras otra vez, el directorio src / es donde el código, los assets, las plantillas y la mayoría de todas las cosas especificas habitan.


El directorio ``vendor/`` 
~~~~~~~~~~~~~~~~~~~~~~~~~

El directorio  vendor/ es básicamente equivalente al directorio lib/vendor/ en symfony1, que fue el directorio para todas las librerías de proveedores. Por defecto, usted encontrará en symfony2  los archivos de librería en este directorio, junto con varias bibliotecas dependientes, como Doctrine2, Twig y SwiftMailer.


El directorio ``web/`` 
~~~~~~~~~~~~~~~~~~~~~~

El directorio web/ no ha cambiado mucho. La diferencia más notable es la ausencia de los directorios CSS/, js/  e images/. Esto es intencional. Al igual que con su código PHP, todos los assets deben vivir también dentro de un paquete. Con la ayuda de una consola de comandos, el directorio Resources/public/ de cada bundle se copia o se vincula simbólicamente al directorio web/bundles/. Esto le permite mantener organizados los assets dentro de su bundle, pero siguen estando disponibles al público. Para asegurarse de que todos los paquetes estén disponibles, ejecute el siguiente comando:

    ./app/console_dev assets:install web --symlink

.. note::

   This command is the Symfony2 equivalent to the symfony1 ``plugin:publish-assets``
   command.

Autoloading
-----------

Una de las ventajas de los frameworks modernos es no tener que preocuparse nunca sobre que requieren los archivos. Al hacer uso de un autoloader, puede referirse a cualquier clase en su proyecto y confiamos en que esté disponible. Esto es posible a través de un autoloader. El autoloader ha cambiado en Symfony2, para ser más universal, más rápido  e independiente de la necesidad de vaciar la caché.

En symfony1, el autoloader se llevó a cabo mediante la búsqueda en todo el proyecto de la presencia de archivos de clases PHP y atrapando esa información en un arreglo bastante grande. Ese arreglo le decía a symfony1 exactamente qué archivo contiene cada clase. En el entorno de producción, esto causó que usted necesitara borrar la caché cuando las clases se añadieran o se movieran.

En Symfony2, una nueva clase - UniversalClassLoader - se encarga de este proceso. La idea detrás del autoloader  es simple: el nombre de su clase (incluyendo el namespace) debe coincidir con la ruta al archivo que contiene esa clase. Tome HelloController del sandbox Symfony2 como ejemplo:

    namespace Acme\HelloBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class HelloController extends Controller
    {
        // ...

El archivo como tal está en src/Acme/HelloBundle/Controller/HelloController.php.  Como puede ver, el lugar donde está el archivo sigue el namespace de la clase. Específicamente, el namespace Acme\HelloBundle\Controller, especifica el directorio donde el archive debe estar (src\Acme\HelloController\Controller). Esto es porque en el archivo app/autoload.php, usted podrá configurar Symfony para ver el namespace Acme en el directorio src. 


.. code-block:: php

    // app/autoload.php

    // ...
    $loader->registerNamespaces(array(
        // ...
        'Acme'             => __DIR__.'/../src',
    ));

 Si el archivo  no está en esta ubicación exacta, usted verá el siguiente error: "Acme\HelloBundle\Controller\HelloController" does not exist. En Simfony2 un "class does not exist" significa que el namespace de la clase no concuerda con la ubicación física. Básicamente, Symfony2 está buscando en una ubicación exacta de dicha clase, pero ese lugar no existe (o contiene una clase diferente). Cuando una clase se carga automáticamente en Symfony2, no será necesario vaciar la caché. 
Como se menciono anteriormente, para que el autoloader funcione, este debe saber donde está el namespace Acme en el directorio /src.  Por ejempo el namespace de Doctrine está en el directorio vendor/doctrine/lib/. T Este mapeo esta controlado enteramente por usted mediante el archivo app/autoload.php


Usando la consola
-----------------

En symfony1 la consola esta en la raíz del proyecto y es llamada symfony:
php symfony
En Symfony2 la consola esta ahora en el subdirectorio app y es llamada  console:
php app/console


Aplicaciones
------------

En un proyecto symfony1, es común tener varias aplicaciones: una para el frontend y otra para el back-end por ejemplo.

En un proyecto Symfony2, sólo tiene que crear una aplicación (una aplicación de blog, una aplicación de intranet, ...). La mayoría de las veces, si desea crear una segunda aplicación, en su lugar podría crear otro proyecto y comparten algunos paquetes entre ellos.

Y si tiene que separar la interfaz y las funciones de back-end de algunos bundles , puede crear sub-namespaces para los controladores, sub-directorios de plantillas, diferentes configuraciones de semántica, configuraciones de enrutamiento por separado, y así sucesivamente.

Por supuesto, no hay nada malo en tener varias aplicaciones en el proyecto. Una segunda aplicación significaría un nuevo directorio, por ejemplo, my_app/, con la misma configuración básica que el directorio app/,

Lea la definición de un proyecto, una aplicación y un bundle en el glosario.


Bundles y Plugins
-------------------

En un proyecto de symfony1, un plugin puede contener configuración, modulos, librerías PHP, assets y cualquier cosa relacionada con el proyecto. En Symfony2, la idea de un plugin es reemplazada por el “bundle”. Un bundle es mucho mas poderoso que un plugin por que el núcleo del framework Symfony2 es traído atraves de una serie de bundles. En Symfony2, los bundles son ciudadanos de primera clase,  por lo tanto son mas flexible que inclusive cualquier código del núcleo. 
En symfony1, un plugin debe ser habilitado dentro de la clase ProjectConfiguration

class::

    // config/ProjectConfiguration.class.php
    public function setup()
    {
        $this->enableAllPluginsExcept(array(/* some plugins here */));
    }

En Symfony2,  los bundles son activados dentro del kernel de la aplicación (AppKernel.php)

    // app/AppKernel.php
    public function registerBundles()
    {
        $bundles = array(
            new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
            new Symfony\Bundle\TwigBundle\TwigBundle(),
            // ...
            new Acme\HelloBundle\AcmeHelloBundle(),
        );
        
        return $bundles;
    }

También hay que asegurarse que el namespace Acme está escrito en el archivo para ser autocargado::

    // app/autoload.php
    $loader = new UniversalClassLoader();
    $loader->registerNamespaces(array(
        'Symfony'                        => __DIR__.'/../vendor/symfony/src',
        'Acme'                           => __DIR__.'/../src',
        // ...
    ));

En symfony1 los archivos de configuración  routing.yml y app.yml  eran cargados automáticamente dentro de cualquier plugin. En symfony2 la configuración de la aplicación y el enrutamiento dentro de un bundle debe ser incluida manualmente. Por ejemplo, para incluir un recurso de enrutamiento a un bundle, debe hacer lo siguiente::


    # app/config/routing.yml
    hello:
        resource: @HelloBundle/Resources/config/routing.yml

Para traer la configuración del bundle, necesitará importar esa configuración de la configuración de su aplicación.


.. _`Symfony2 Sandbox`: https://github.com/symfony/symfony-sandbox