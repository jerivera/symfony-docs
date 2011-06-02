En que difiere Symfony2 de symfony1
==================================

El framework Symfony2 plasma una significante evoluci�n cuando se compara con la primera versi�n del framework. Afortunadamente con la arquitectura MVC como n�cleo, las habilidades  y conocimientos usados para hacer buenos proyectos en symfony1 siguen siendo muy relevantes cuando desarrollamos en Symfony2.
En este cap�tulo, vamos a caminar a trav�s de las diferencias entre symfony1 y Symfony2. Como podremos ver,   muchas tareas son abordadas de una manera ligeramente diferente.  Usted llegar� a apreciar estas diferencias de menor importancia ya que promueven el c�digo estable, predecible, verificable y disociada en sus aplicaciones Symfony2.
Entonces, si�ntese y rel�jese,  mientras lo llevamos del antes al ahora.

Estructura del directorio
-------------------------

Cuando le damos un vistazo a un proyecto de Symfony2  � por ejemplo the Symfony2 Sandbox (No sirve ese link), usted se dar� cuenta que la estructura de directorios de symfony es muy diferente. Las diferencias, sin embargo, son algo superficiales.

El directorio ``app/`` 
~~~~~~~~~~~~~~~~~~~~~~

En symfony1, un proyecto tiene una o mas aplicaciones y cada una esta dentro del directorio  apps/ (Por ejemplo. apps/frontend).  Por defecto en Symfony2, se tiene una sola aplicaci�n representada por el directorio app/.  Como en symfony1, el directorio app/  contiene configuraci�n especifica de la aplicaci�n. �ste Tambi�n contiene cach� espec�fico de la aplicaci�n, registro y directorios de plantillas,  as� como la clase Kernel (AppKernel),  el cual es el objeto base que representa a la aplicaci�n. 
A diferencia de symfony1, casi no hay codigo PHP en el directorio app/.  Este directorio no se usa como casa de m�dulos o librer�as de archivos como lo hacia en symfony1. En cambio, es simplemente el �hogar� de la configuraci�n y otros recursos (plantillas, archivos de traducci�n).


El directorio ``src/`` 
~~~~~~~~~~~~~~~~~~~~~~

En pocas palabras, su verdadero c�digo va aqu�. En Symfony2, todo el c�digo de la aplicaci�n   est� dentro de un bundle (aproximadamente equivalente a un plugin en symfony1) y, por defecto, cada bundle est� dentro del directorio src.  De esta manera, el directorio src es un poco como el directorio de plugins en symfony1, pero mucho m�s flexible. Adem�s, mientras sus bundles est�n  en el directorio src /, los bundles de otros fabricantes pueden estar en el directorio vendor /.

Para obtener una mejor imagen de el directorio src /, primero piense en una aplicaci�n symfony1. En primer lugar, parte de su c�digo habita dentro de una o m�s aplicaciones. Lo m�s com�n son los m�dulos, pero tambi�n podr�a incluir otras clases de PHP que usted tiene en su aplicaci�n. Es posible que tambi�n haya creado un archivo schema.yml en el directorio de configuraci�n de su proyecto. Por �ltimo, para ayudar con algunas funcionalidades comunes, usted est� usando varios plugins de terceros que est�n en el directorio  plugins /. En otras palabras, el c�digo que impulsa su aplicaci�n a la vida habita en muchos lugares diferentes.

En Symfony2, la vida es mucho m�s simple porque todo el c�digo Symfony2 debe estar en un bundle. En nuestro posible proyecto symfony1, todo el c�digo podr�a ser trasladado entre uno o m�s plugins (que es una pr�ctica muy buena, de hecho). Suponiendo que todos los m�dulos, clases de PHP, el esquema, configuraci�n de enrutamiento, etc fueron movidos a un plugin, el  directorio plugins/ symfony1  debe ser muy similar al directorio src/ en Symfony2.
 
En pocas palabras otra vez, el directorio src / es donde el c�digo, los assets, las plantillas y la mayor�a de todas las cosas especificas habitan.


El directorio ``vendor/`` 
~~~~~~~~~~~~~~~~~~~~~~~~~

El directorio  vendor/ es b�sicamente equivalente al directorio lib/vendor/ en symfony1, que fue el directorio para todas las librer�as de proveedores. Por defecto, usted encontrar� en symfony2  los archivos de librer�a en este directorio, junto con varias bibliotecas dependientes, como Doctrine2, Twig y SwiftMailer.


El directorio ``web/`` 
~~~~~~~~~~~~~~~~~~~~~~

El directorio web/ no ha cambiado mucho. La diferencia m�s notable es la ausencia de los directorios CSS/, js/  e images/. Esto es intencional. Al igual que con su c�digo PHP, todos los assets deben vivir tambi�n dentro de un paquete. Con la ayuda de una consola de comandos, el directorio Resources/public/ de cada bundle se copia o se vincula simb�licamente al directorio web/bundles/. Esto le permite mantener organizados los assets dentro de su bundle, pero siguen estando disponibles al p�blico. Para asegurarse de que todos los paquetes est�n disponibles, ejecute el siguiente comando:

    ./app/console_dev assets:install web --symlink

.. note::

   This command is the Symfony2 equivalent to the symfony1 ``plugin:publish-assets``
   command.

Autoloading
-----------

Una de las ventajas de los frameworks modernos es no tener que preocuparse nunca sobre que requieren los archivos. Al hacer uso de un autoloader, puede referirse a cualquier clase en su proyecto y confiamos en que est� disponible. Esto es posible a trav�s de un autoloader. El autoloader ha cambiado en Symfony2, para ser m�s universal, m�s r�pido  e independiente de la necesidad de vaciar la cach�.

En symfony1, el autoloader se llev� a cabo mediante la b�squeda en todo el proyecto de la presencia de archivos de clases PHP y atrapando esa informaci�n en un arreglo bastante grande. Ese arreglo le dec�a a symfony1 exactamente qu� archivo contiene cada clase. En el entorno de producci�n, esto caus� que usted necesitara borrar la cach� cuando las clases se a�adieran o se movieran.

En Symfony2, una nueva clase - UniversalClassLoader - se encarga de este proceso. La idea detr�s del autoloader  es simple: el nombre de su clase (incluyendo el namespace) debe coincidir con la ruta al archivo que contiene esa clase. Tome HelloController del sandbox Symfony2 como ejemplo:

    namespace Acme\HelloBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class HelloController extends Controller
    {
        // ...

El archivo como tal est� en src/Acme/HelloBundle/Controller/HelloController.php.  Como puede ver, el lugar donde est� el archivo sigue el namespace de la clase. Espec�ficamente, el namespace Acme\HelloBundle\Controller, especifica el directorio donde el archive debe estar (src\Acme\HelloController\Controller). Esto es porque en el archivo app/autoload.php, usted podr� configurar Symfony para ver el namespace Acme en el directorio src. 


.. code-block:: php

    // app/autoload.php

    // ...
    $loader->registerNamespaces(array(
        // ...
        'Acme'             => __DIR__.'/../src',
    ));

 Si el archivo  no est� en esta ubicaci�n exacta, usted ver� el siguiente error: "Acme\HelloBundle\Controller\HelloController" does not exist. En Simfony2 un "class does not exist" significa que el namespace de la clase no concuerda con la ubicaci�n f�sica. B�sicamente, Symfony2 est� buscando en una ubicaci�n exacta de dicha clase, pero ese lugar no existe (o contiene una clase diferente). Cuando una clase se carga autom�ticamente en Symfony2, no ser� necesario vaciar la cach�. 
Como se menciono anteriormente, para que el autoloader funcione, este debe saber donde est� el namespace Acme en el directorio /src.  Por ejempo el namespace de Doctrine est� en el directorio vendor/doctrine/lib/. T Este mapeo esta controlado enteramente por usted mediante el archivo app/autoload.php


Usando la consola
-----------------

En symfony1 la consola esta en la ra�z del proyecto y es llamada symfony:
php symfony
En Symfony2 la consola esta ahora en el subdirectorio app y es llamada  console:
php app/console


Aplicaciones
------------

En un proyecto symfony1, es com�n tener varias aplicaciones: una para el frontend y otra para el back-end por ejemplo.

En un proyecto Symfony2, s�lo tiene que crear una aplicaci�n (una aplicaci�n de blog, una aplicaci�n de intranet, ...). La mayor�a de las veces, si desea crear una segunda aplicaci�n, en su lugar podr�a crear otro proyecto y comparten algunos paquetes entre ellos.

Y si tiene que separar la interfaz y las funciones de back-end de algunos bundles , puede crear sub-namespaces para los controladores, sub-directorios de plantillas, diferentes configuraciones de sem�ntica, configuraciones de enrutamiento por separado, y as� sucesivamente.

Por supuesto, no hay nada malo en tener varias aplicaciones en el proyecto. Una segunda aplicaci�n significar�a un nuevo directorio, por ejemplo, my_app/, con la misma configuraci�n b�sica que el directorio app/,

Lea la definici�n de un proyecto, una aplicaci�n y un bundle en el glosario.


Bundles y Plugins
-------------------

En un proyecto de symfony1, un plugin puede contener configuraci�n, modulos, librer�as PHP, assets y cualquier cosa relacionada con el proyecto. En Symfony2, la idea de un plugin es reemplazada por el �bundle�. Un bundle es mucho mas poderoso que un plugin por que el n�cleo del framework Symfony2 es tra�do atraves de una serie de bundles. En Symfony2, los bundles son ciudadanos de primera clase,  por lo tanto son mas flexible que inclusive cualquier c�digo del n�cleo. 
En symfony1, un plugin debe ser habilitado dentro de la clase ProjectConfiguration

class::

    // config/ProjectConfiguration.class.php
    public function setup()
    {
        $this->enableAllPluginsExcept(array(/* some plugins here */));
    }

En Symfony2,  los bundles son activados dentro del kernel de la aplicaci�n (AppKernel.php)

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

Tambi�n hay que asegurarse que el namespace Acme est� escrito en el archivo para ser autocargado::

    // app/autoload.php
    $loader = new UniversalClassLoader();
    $loader->registerNamespaces(array(
        'Symfony'                        => __DIR__.'/../vendor/symfony/src',
        'Acme'                           => __DIR__.'/../src',
        // ...
    ));

En symfony1 los archivos de configuraci�n  routing.yml y app.yml  eran cargados autom�ticamente dentro de cualquier plugin. En symfony2 la configuraci�n de la aplicaci�n y el enrutamiento dentro de un bundle debe ser incluida manualmente. Por ejemplo, para incluir un recurso de enrutamiento a un bundle, debe hacer lo siguiente::


    # app/config/routing.yml
    hello:
        resource: @HelloBundle/Resources/config/routing.yml

Para traer la configuraci�n del bundle, necesitar� importar esa configuraci�n de la configuraci�n de su aplicaci�n.


.. _`Symfony2 Sandbox`: https://github.com/symfony/symfony-sandbox