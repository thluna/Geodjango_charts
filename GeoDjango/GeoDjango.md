# Introducción: #  
Django es un framework de aplicaciones web  de código abierto para el lenguaje  Python, dada la tendencia actual Python empieza a ser uno de los lenguajes mas usados hoy en día, la tendencia va la alza por su facilidad de entendimiento y la comunidad del lenguaje.  

## Objetivo ##  
El objetivo de éste curso es aprender a montar una aplicación web básica con Django, junto con las herramientas Leaflet y ChartJs.  
## Activación del proyecto ##  
**conda activate entorno**  
## Creación de proyecto ## 
**django-admin startproject prueba**  
.  
<p align="center"> 
<img src="../img/01.png">
</p>   
Se generará una carpeta con el nombres del proyecto y al abrirla encontraremos la siguiente estructura:  
.  

```
Geodjango_charts/
└── prueba/  
	└── manage.py   
	└── prueba/  
```  

De ésta forma tendremos iniciado nuestro proyecto, ahora deberemos crear nuestra app de la siguiente forma:  
**python manage.py startapp app**  
Así habremos creado nuestra primera app, entremos a la carpeta y crearemos una carpeta dentro llamada vistaPrincipal y moveremos todos los archivos dentro de app a ella.  
La estructura debería quedar de la siguiente forma:  
.  

```
Geodjango_charts/
└── prueba/
   ├── manage.py
   ├── app/
       └── vistaPrincipal/
       		└── migrations/
       		└── admin.py
       		└── app.py
       		└── models.py
       		└── tests.py
       		└── views.py
       └── __init__.py
   ├── prueba/
       └── __init__.py
       └── __pycache__
       └── settings.py
       └── urls.py
       └── wsgi.py
```    

## Configuración del proyecto ##  
Hasta éste punto habremos creado nuestra app ahora debemos realizar unas pequeñas configuraciones en el archivo **settings.py** con el fin de tener la configuración lista para nuestra app.  
Abrimos el archivo settings.py y buscamos la sección de installed apps  
<p align="center"> 
<img src="../img/installed_apps_prev.png">
</p> 

Dado que estaremos trabajando con datos espaciales, tendremos que usar la extensión espacial de **django** la cual se puede consultar en el siguiente link ** https://docs.djangoproject.com/en/3.0/ref/contrib/gis/ **  
<p align="center"> 
<img src="../img/installed_app_post.png">
</p>  
Con ésto estamos usando la extensión espacial y de paso incorporando la herramienta de leaflet para django, leaflet es una herramienta de **Javascript** para la creación de mapas interactivos, se puede encontrar su documentación en el siguiente link **https://leafletjs.com/reference-1.6.0.html**.
**Nota** Se pueden agregar tantas apps queramos crear, solo debemos agregarla dentro de installed apps como le hicimos con la de **app**.
Una vez hecho lo anterior procedemos a indicar la ubicación de la carpeta donde estará nuestros templates de html por lo que la parte de templates deberá quedar de la siguiente forma:  

<p align="center"> 
<img src="../img/prev_templates.png">
</p>  
<p align="center"> 
<img src="../img/post_templates.png">
</p> 
Como paso previo deberemos haber creado una base de datos en posgtres y crear la extensión espacial de postgis en ella, no importa que aun no tenga tablas.  
Procedemos a configurar la base de datos dado que por defecto django usa sqlite3 por lo que la configuración debe quedar de la siguiente forma:  

<p align="center"> 
<img src="../img/prev_db.png">
</p>
<p align="center"> 
<img src="../img/post_db.png">
</p> 
A nuestra base de datos la llamaremos accidentes, pueden usar el nombre que quieran pero debe coincidir con el nombre de la base que crearon al igual que la contraseña.  
Como requisito debemos instalar psycopg2 o tenerlo instalado, éste es el controlador para realizar la conexión a Postgres, recordemos que todo debe instalarse en el entorno de conda y através de **conda install package**.  
Ahora solo nos queda configurar para poder cargar archivos estáticos, es decir archivos js, img, css que vamos  a requerir más adelante.  

<p align="center"> 
<img src="../img/static_conf.png">
</p> 

Con ésto tenemos la configuración básica de nuestra aplicación hecha, aunque aún podemos modificarle cosas se dejará para más adelante.

Antes de continuar es importante mencionar el concepto de **json y geojson**  
**Json** por sus siglas JavaScript Object Notation (notación de objetos javascritp) y siendo ** Geojson ** un tipo de json particular para datos espaciales, para más información consultar **https://www.json.org/json-es.html**  

## ¿Qué son los modelos django ##
Introduciremos el concepto de ORM (Object Relational Mapping) Objecto Modelo Relación en español, es un patrón de diseño que nos permite manejar las tablas de la base de datos como clases en el lenguaje, en nuestro caso Python, django internamente maneja un mapeo entre nuestras tablas en la base de datos y clases en python, es importante que la manupulación hacia las tablas de la base de datos es mejor hacerlo através del ORM que con ejecución de querys directamente a la base de datos por temas de seguridad.
<p align="center"> 
<img src="../img/Django-Models.png">
</p> 
### Cómo definir modelos en  python ###

Debemos irnos al archivo models.py dentro de app y veremos lo siguiente:  
<p align="center"> 
<img src="../img/prev_models.png">
</p>   
Aquí debemos definir nuestros mapeos de la base de datos como se mencionó anteriormente, para crear un modelo que tenga soporte para datos espaciales debemos primero hacer el siguiente **import** hasta arriba de nuestro archivo

```python
from django.contrib.gis.db import models as geomodels
```  
con ello estaremos diciéndole a django que use la extensión espacial y podemos definir nuestros modelos de la siguiente forma, haremos una clase ejemplo  

```python
from django.db import models
from django.contrib.gis.db import models as geomodels

# Create your models here.

class Datos(models.Model):
    id = models.IntegerField(primary_key=True)    	
    geom = geomodels.MultiPointField()
    field_1	= models.IntegerField()
    id_ssc	= models.IntegerField()
    id_pgj = models.IntegerField()
    delito = models.CharField(max_length = 100)
    tipo_evento = models.CharField(max_length = 100)
    fecha = models.DateTimeField()	
    identidad = models.CharField(max_length = 100)	
```  
Como podremos observar nuestra variable id de tipo entero será nuestra llave en la tabla, posteriormente lo más importante a notar es la variable geom la cual funge como la representación de la geometría en nuestro caso es de tipo punto aunque podríamos tener otras como poligono o línea. Es **recomendable** más no obligatorio que se defina el modelo para cada app en su propio models.py
Aunque resulte un poco obvio que IntegerField() y CharField() sean para campos de tipo entero y cadena respectivamente, no está de más leer o tener a la mano la documentación **https://docs.djangoproject.com/en/3.0/ref/contrib/gis/model-api/**, **https://docs.djangoproject.com/en/3.0/topics/db/models/**.  
Dejaremos pendientes la parte de las relaciones entre tablas para más adelante, de momento no son necesarias..

## Rutas **URL** y vistas ##  
Para ésto es importante mencionar qué es HTTP (Protocolo de Transferencia de Hipertexto), es un protocolo de transferencia de información hipermedia para Internet, diseñado para la comunicación entre navegadores y servidores web, como lo que estaremos montando es un servidor web.  
Nos iremos al archivo **views.py** dentro de app por lo que tendremos el siguiente código:  
```python
from django.shortcuts import render

# Create your views here.
```  
Importaremos las siguientes bibliotecas:  
```python
from django.shortcuts import render
import json
from geojson import Point, Feature, FeatureCollection, dump


from django.contrib.gis.geos import Polygon, Point, MultiPoint, GeometryCollection
from shapely.geometry import Point, mapping, shape
from .models import * 
from django.views.decorators.csrf import csrf_exempt

from django.http import *

from django.core.serializers import serialize
from django.core  import *
#Create your views here
```  
Entonces definiremos nuestra ruta para el index, el cual siempre funge como la página principal para ésto mencionaremos brevemente los dos métodos **HTTP** más comunes:  
*   GET El método GET  solicita una representación de un recurso específico. Las peticiones que usan el método GET sólo deben recuperar datos.    
*   POST  El método POST se utiliza para enviar una entidad a un recurso en específico, causando a menudo un cambio en el estado o efectos secundarios en el servidor.    

Procedemos a definir el index (home) de la siguiente manera:
```python
@csrf_exempt

def index(request):
    if request.method == 'GET':
        return render(request,'primeraVista/home.html')
    elif request.method == 'POST':
        
        return HttpResponseForbidden()
```  
De momento no necesitamos definir un comportamiento para el método POST hacia nuestro index por lo que  usamos 403 de http para denegar la petición, solo nos interesa mandar peticiones de tipo GET al servidor para renderizar el html de la página principal, aunque la convención es a la primera vista llamarla **index.html**, posteriormente debemos ir a nuestro archivo **urls.py** dentro de la carpeta prueba y deberá verse de la siguiente forma:  
<p align="center"> 
<img src="../img/prev_urls.png">
</p>  

Como podremos observar solo tenemos la ruta para el admin propio de django, ahora en éste arreglo debemos definir la ruta para cada ruta que tengamos en nuestro servidor django, de 
momento solo tendremos la que contendrá el mapa en leaflet, por lo que deberemos tener el siguiente código en nuestro archivo **prueba/urls.py**:

```python
from django.contrib import admin
from django.urls import path
from django.conf.urls import url ,include
from app.vistaPrincipal.urls import *
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app.vistaPrincipal.urls')),
]
```
Y crearemos un nuevo archivo **urls.py** dentro de la carpeta vistaPrincipal con el siguiente contenido:

```python
from django.urls import path
from django.conf.urls import url,include
from app.vistaPrincipal.views import *
urlpatterns = [
    path('',index),
]
```  
Entre las comillas debemos escribir la ruta que deberá tomar, cuando dejamos las comillas en vacío le estamos indicando que la ruta por defecto es "http://127.0.0.1:8000".  

### Nota con las rutas  
Es posible definir varios archivos  de rutas, podríamos definir para cada módulo de nuestra aplicación un **urlpattern** para manejar las vistas correspondientes,  
Previo a iniciar por primera vez nuestro servidor debemos aplicar las **migrations** las cuales son la forma en la que django aplica los cambios a los modelos definidos y con el comando **makemigrations** se crean nuevas migraciones sobre la base de datos, es por ello que deberemos ejecutar los siguientes comandos:  
**python manage.py migrate**
<p align="center"> 
<img src="../img/prev-migrate.png">
</p>  

**python** **manage.py** **makemigrations**    

<p align="center"> 
<img src="../img/prev-makemigrations.png">
</p> 

Ahora si podremos inicar nuestra aplicación para comprobar si todo va correctamente, ejecutaremos el comando **python** **manage.py** **runserver** y si vamos a nuestro navegador con la ruta "http://127.0.0.1:8000" obtendremos lo siguiente:  
<p align="center"> 
<img src="../img/prev_django.png">
</p>  



## Creación de nuestro archivo home.html
Debemos crear las carpetas static y templates por convención se deben usar esos nombres, con lo cual nuestro proyecto debe tener la siguiente estructura:  
```
Geodjango_charts/
└── prueba/
   ├── manage.py
   ├── app/
       └── vistaPrincipal/
       		└── migrations/
       		└── admin.py
       		└── app.py
       		└── models.py
       		└── tests.py
       		└── views.py
       		└── urls.py
       └── __init__.py
   ├── prueba/
       └── __init__.py
       └── __pycache__
       └── settings.py
       └── urls.py
       └── wsgi.py
    ├── templates/
    	└── primeraVista
    		└──home.html
    ├── static/
    	└── img/
    	└── js/
    	└── css/
```
agregar lo de app.vistaPrincipal.url  
Ahora dentro de la carpeta templates/primeraVista creamos un archivo **home.html** con la siguiente estructura básica de html5  

<p align="center"> 
<img src="../img/prev_home_html.png">
</p>  

En donde **head** es donde haremos todos los imports de las bibliotecas externas y archivos css.
Con lo que ahora podremos visualizar lo siguiente en nuestro navegador:  

<p align="center"> 
<img src="../img/ejemplo-html.png">
</p>

Debemos escribir hasta arriba de nuestro html lo siguiente:  

<p align="center"> 
<img src="../img/static-tags.png">
</p> 

**Hasta éste punto ya tenemos nuestro template listo para montar nuestro mapa** por lo que procedemos a importar leaflet, pero antes haremos el import de las cosas necesarias externas y otros tags para leaflet:  

<p align="center"> 
<img src="../img/leaflet-tags.png">
</p>  

```html
    <link href="https://fonts.googleapis.com/css?family=PT+Sans&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Bebas+Neue&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Encode+Sans+Semi+Condensed|IBM+Plex+Mono|PT+Sans|Titillium+Web&display=swap" rel="stylesheet">
      
    <!--JQuery-->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
    
    <!--Fonts necesarios-->
    <link href="https://fonts.googleapis.com/css?family=PT+Sans&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Bebas+Neue&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Indie+Flower|Open+Sans|Open+Sans+Condensed:300|Raleway|Roboto+Mono|Roboto+Slab&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Encode+Sans+Semi+Condensed|IBM+Plex+Mono|PT+Sans|Titillium+Web&display=swap" rel="stylesheet">
    <!-- Scripts necesarios para chart js-->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.bundle.js" integrity="sha256-JG6hsuMjFnQ2spWq0UiaDRJBaarzhFbUxiUTxQDA9Lk=" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.bundle.min.js" integrity="sha256-XF29CBwU1MWLaGEnsELogU6Y6rcc5nCkhhx89nFMIDQ=" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.js" integrity="sha256-J2sc79NPV/osLcIpzL3K8uJyAD7T5gaEFKlLDM18oxY=" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.min.js" integrity="sha256-CfcERD4Ov4+lKbWbYqXD6aFM9M51gN4GUEtDhkWABMo=" crossorigin="anonymous"></script>
    
```  

Ahora necesitamos agregar nuestros propios archivos así que haremos los siguientes imports:  

<p align="center"> 
<img src="../img/staticfiles.png">
</p>  

De momento no nos meteremos con css por lo que tendremos una estructura básica para un mejor entendimiento  del curso, por lo que quedarían de la siguiente forma y explicaremos cada uno de ellos:  
```html  
<div class="Instrucciones">
            <h2>INSTRUCCIONES</h2>
            <p> En el mapa se observan de color rojo las zonas con mayor
              densidad de incidentes viales fatales para el año 2019 en la Ciudad de México. 
              Al seleccionar un mes es posible identificar su variación temporal. Además de la descripción del suceso.
            </p>     
        </div>
        <!--Un botón para -->

        <div class='select'>
          <select name="combo-box" id="combo-box">
            <option value="2018">AÑO 2018</option>
            <option value="Enero">Enero</option>
            <option value="Febrero">Febrero</option>
            <option value="Marzo">Marzo</option>
            <option value="Abril">Abril</option>
            <option value="Mayo">Mayo</option>
            <option value="Junio">Junio</option>
            <option value="Junio">Julio</option>
            <option value="Agosto">Agosto</option>
            <option value="Septiembre">Septiembre</option>
            <option value="Octubre">Octubre</option>
            <option value="Noviembre">Noviembre</option>
            <option value="Diciembre">Diciembre</option>
          </select> 
        </div>

      <div class ='button-div'> 
          <button class="button" type="submit" onclick="mapaCalor()" >ACEPTAR</button>
      </div>

      
      <div class ='mapa'>
            <div class="item1" id="map1" style="min-zoom: 3;"></div>    
      </div>
      <div class="chart-container" >
        <canvas id="myChartGraph" width="100" height="100" style="margin-left:100px;"></canvas> 
        <canvas id="myChartGraph1" width="100" height="100" style="margin-left:200px;"></canvas> 

       </div> 


```  
Ahora en nuestro archivo **mapa.css** solo debemos tener lo siguiente:  

```css
.item1{
  height:500px;
  width:50%;
}
```  

El tag **select** nos sirve para desplegar un **combo-box** al cual através de **id** le asignamos un identificador para trabajar con él más adelante y lo llenamos con los valores que en nuestro caso nos conviene alberguen los meses del año y opciones adicionales y através del tag **option** podemos meter las opciones al combo-box, **value** le asigna un valor de tipo cadena el cual es el que lograremos obtener desde JavaScript para saber cuál fue la selección del usuario.  
En el tag **button** le  estamos indicando que cuando sea apretado ejecute una llamada a función en nuestro caso le llamaremos mapaCalor(), así también le pondremos el mensaje de Aceptar para que el usuario después de seleccionar el mes que desea pueda filtrar los datos.  
Debemos crear un div exclusivo para nuestro mapa de leaflet, le asignaremos el **id="map1"** y la clase de css **class="item3"**, se podría decir que solo estamos definiendo el contenedor para el mapa de momento y dejaremos pendiente las gráficas para más adelante en el curso.  

Creamos un nuevo archivo llamado mapa.css y lo guardaremos dentro de la carpeta **static/css/** para más adelante poder acomodar nuestros elementos, así también copiaremos los archivos heatmat.js y leaflet-heatmap.js dentro de nuestra carpeta **static/js/** y también crearemos dentro de dicha carpeta un archivo **datos.js** con lo que nuestra estructura actualizada quedaría de la siguiente manera:  

```
Geodjango_charts/
└── prueba/
   ├── manage.py
   ├── app/
       └── vistaPrincipal/
          └── migrations/
          └── admin.py
          └── app.py
          └── models.py
          └── tests.py
          └── views.py
          └── urls.py
       └── __init__.py
   ├── prueba/
       └── __init__.py
       └── __pycache__
       └── settings.py
       └── urls.py
       └── wsgi.py
    ├── templates/
      └── primeraVista
        └──home.html
    ├── static/
      └── img/
      └── js/
        └──datos.js
        └──heatmap.js
        └──leaflet-heatmap.js
      └── css/
        └──mapa.css
```  
**Nota**, los archivos los puedes encontrar el la carpeta js del tutorial  
<p align="center"> 
<img src="../img/repo.png">
</p>  

Hasta el momento si revisamos nuestra página observaremos lo siguiente:
<p align="center"> 
<img src="../img/prev-mapa.png">
</p>  

Aun no lo hemos dado estilo pero tenemos los elementos que necesitamos, dejaremos un poco de lado el estilo de momento y procederemos a programar en JavaScript el mapa de leaflet.  
Daremos una breve introducción a **JQuery y Ajax** que son frameworks o marcos de trabajo de Javascritp que usaremos en breve.

## JQuery y Ajax

**JQuery** **https://jquery.com/** es una biblioteca que nos permite manipular el HTML, el manejo de eventos, manejo de AJAX, animaciones entre otras muchas cosas, al igual que muchos otros frameworks facilitan las formas de programar en javascript.

**Ajax**  significa JavaScript asíncrono y XML (AJAX significa JavaScript asíncrono y XML), es un conjunto de técnicas usadas en el desarrollo web que permiten a las aplicaciones funcionar de forma asíncrona, ésto es que no se tenga que esperar a recibir respuesta del servidor para continuar la ejecución, se procesan las solicitudes en segundo plano.

Ahora que al menos sabemos qué es cada cosa, podemos abrir el archivo **datos.js**, haremos una prueba para cargar los datos en cuanto nuestra página se cargue a su totalidad, para ello usaremos una función muy útil de Jquery la cual es:    

```text
$(document).ready(function{...});
```

Donde function es donde definiremos el comportamiento de esa función anónima, recordemos que JavaScript permite el paso de funciones como parámetros, lo cual nos será útil más adelante, ahora dentro de ésta función debemos definir lo siguiente:  

```javascript
var testData = {
        data: []
    };
```
La variable testData es un objeto el cual tiene un atributo data de tipo array que de momento dejaremos vacio, posteriormente deberemos crear e instanciar el primer layer del mapa, un layer es una capa que podemos agregar a nuestro mapa de leaflet, recordando que podemos tener varias capas en un mapa, entonces el código quedaría de la siguiente forma: 

```javascript
 var baseLayer = L.tileLayer('https://tiles.wmflabs.org/hikebike/{z}/{x}/{y}.png', {
        Zoom: 9,
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      });
```  
Donde zoom es una opción que le podemos cambiar al métodod TileLayer de leaflet **puede consultar el siguiente link para más información** **https://leafletjs.com/reference-1.6.0.html#tilelayer**, TileLayer por su parte nos permita para cargar y mostrar capas en nuestro mapa, el primero parámetro del método corresponde al template del mapa (los cuales puede cambiar a consideración dependiendo el uso) se pueden encontrar algunos en el siguiente link **https://wiki.openstreetmap.org/wiki/Tile_servers**, el siguiente parámetro es un objeto con opciones para nuestro mapa, puede consultar las opciones aquí o puede dirigirse a link que se puso previamente, de momento nos quedaremos solo con zoom y attribution para dar crédito a OpenStreetMaps

<p align="center"> 
<img src="../img/tilelayetoptions.png">
</p> 

Ahora procederemos a asignar el objeto de configuración a la variable **cfg** de la siguiente forma:   
```javascript
 var cfg = {
        // radius should be small ONLY if scaleRadius is true (or small radius is intended)
        // if scaleRadius is false it will be the constant radius used in pixels
        "radius": .008,
        "maxOpacity": .5,
        "gradient": {
          '0': 'Navy', '0.25': 'Navy',
          '0.26': 'Green',
          '0.5': 'Green',
          '0.51': 'Yellow',
          '0.75': 'Yellow',
          '0.76': 'Red',
          '1': 'Red'
        },
        // scales the radius based on map zoom
        "scaleRadius": true,
        // if set to false the heatmap uses the global maximum for colorization
        // if activated: uses the data maximum within the current map boundaries
        //   (there will always be a red spot with useLocalExtremas true)
        "useLocalExtrema": true,
        // which field name in your data represents the latitude - default "lat"
        latField: 'lat',
        // which field name in your data represents the longitude - default "lng"
        lngField: 'lng',
        // which field name in your data represents the data value - default "value"

      };
```  
Donde el campo **radius** indicará el radio de nuestro punto en el mapa, **maxOpacity** la opacidad del punto, en el objeto **gradient** la escala de colores, **scaleRadius** le indica que el punto se reescala conforme alejas el zoom en el mapa, **latfield** le indicará qué campo deberá tomar de los datos que le pasemos para la latitud, por otro lado **lngField** es para la longitud.  
Para crear nuestra capa de mapa de calor instanciamos e inicializamos la instancia con ayuda de la clase **HeatMapLayer** de la siguiente forma:  

```javascript
heatmapLayer = new HeatmapOverlay(cfg);  
```  
 
A continuación debemos inicilizar el mapa con ambas capas, **baseLayer** nos proporciona la capa que contiene el **tile server** y heatmap contiene la representación de los mapas de calor entonces procedemos a escribir el siguiente código:  

```javascript
map1 = new L.Map('map1', {
        zoomControl: true,
        minZoom: 11,
        maxZoom: 20,
        layers: [baseLayer, heatmapLayer],
        zoomAnimation: true,
        keyboard: true
      }).setView([19.40, -99.14], 11);
```

Donde el campo **zoomControl** recibe un valor booleano para indicar si se puede acercar o alejar el zoom con la rueda del ratón, **minZoom** es el mínimo zoom que puede tener el mapa y **maxZoom** es el máximo zoom que puede tener, **layers** es probablemente el campo de configuración más importante porque aquí le indicamos las capas que debe tener nuestro mapa, por lo que debe incluir la capa con los datos para el mapa de calor y el mapa base, existen otras opciones que podemos incluir en nuestro mapa las cuales se pueden consultar en el siguiente link https://leafletjs.com/reference-1.6.0.html#map-option  

Nuestra página debería verse de la siguiente forma:    
<p align="center"> 
<img src="../img/prev-mapa-datos.png">
</p> 

Ahora implementaré una solución (no única) para que por defecto, al cargar o recargar la página, se jalen todos los datos de la base y después através del combo-box podamos filtrar los datos, de momento la implementación para cargar por defecto todos los datos con python y js sería de la siguiente forma (explicaremos paso a paso):

```javascript
map1 = new L.Map('map1', {
        zoomControl: true,
        minZoom: 11,
        maxZoom: 20,
        layers: [baseLayer, heatmapLayer],
        zoomAnimation: true,
        keyboard: true
      }).setView([19.40, -99.14], 11);
```
Primero programaremos toda la parte de Javascript  

```javascript
$.ajax({
        type: "GET",
        url: 'data/',
        data: {
          'val': '2018'
        },
        dataType: 'json', 
        async: true,

        ...});
```
Iniciamos una petición de ajax con jquery, le definimos el método http que usaremos, en nuestro caso nos interesa obtener información del servidor, por lo que el método ideal es **GET**, le pasamos el url al que debe hacer la petición en nuestro caso **data/**, le indicamos que el tipo de dato que queremos es un **JSON** y la petición que sea asíncrona, es decir que si le es posible continue con la ejecución mientras espera la respuesta del servidor ésto es por temas de experiencia de usuario.  
Ahora que logramos hacer nuestra petición debemos usar lo que se conoce como **promesas** de js, ésto podemos verlo como un objeto que representa éxito o fracaso en la petición asíncrona que hemos realizado con ajax, siempre se ejecutan terminando las funciones anteriores, entonces si todo sale bien y recibimos los datos usaremos una promesa para manejar la respuesta del servidor que debiera ser un objeto JSON.  
La sintaxis es la siguiente:  
```javascript
$.ajax({
        ...

        success:function(respuesta){
          //Uso de respuesta  
        }
});
```

Solo para un mejor entendimiento voy a aclarar, directamente podríamos pasar el json como los datos para el mapa pero en particular nos interesa filtrar algunos campos por ende será un poco repetitivo y antes iremos a python a programar la parte del backend donde se procesará la petición, por lo que abriremos nuevamente el archivo **app/vistaPrincipal/views.py**

```python
    #código previo
@csrf_exempt
def data(request):
    if request.method == 'GET':
        val = request.GET.get('val')
        #podemos imprimir el valor para debug solamente, en producción por buenas prácticas de programación no hay que dejar print's 
        print(val)
        if(val != "2018"):    
            dataAux = serializers.serialize("geojson", Datos.objects.filter(fecha__month=val))
            return  HttpResponse(dataAux)
        else:
            dataAux = serializers.serialize("geojson", Datos.objects.all())
            return  HttpResponse(dataAux)
    elif request.method == 'POST':
        return HttpResponseForbidden()
```  

Como podremos observar, solo estaremos permitiendo peticiones de tipo **GET** dado que no tiene sentido recibir **POST**, el usuario solo quiere consultar datos, por tanto no modificar el estado del servidor, así que usamos 403 de HTTP forbidden como lo podemos ver en el código de arriba. Ahora bien, la lógica nos lleva a dos casos, si el valor buscado por el usuario corresponde a **2018** u otro caso, por ejemplo, **Enero** independientemente del valor, lo único que hay que hacer es un query a la base de datos pero por temas de seguridad se recomienda hacerlo através del ORM y no de ejecuciones directas, ésto con el fin de evitar **SQLInjection**. 

```python
            #... 
            dataAux = serializers.serialize("geojson", Datos.objects.filter(fecha__month=val))
            return  HttpResponse(dataAux)
            # else:
            dataAux = serializers.serialize("geojson", Datos.objects.all())
            return  HttpResponse(dataAux)
```

La sintaxis es Modelo.objects.metodo(parametros), para el primer caso como nos interesa consultar los datos de un determinado mes, debemos aplicar un filtro con .filter() y los parámetros deben corresponder a los atributos de la tabla en la base de datos, en nuestro caso es fecha (el cual es el atributo de tipo date en django) es un equivalente a hacer ésto en postgres:  


```sql
---dataAux = serializers.serialize("geojson", Datos.objects.filter(fecha__month=val))

select * from "vistaPrincipal_datos" where date_part('month', fecha) = mesFiltrado;
```
Y por otro lado el equivalente al caso 2 sería el siguiente:  

```sql
---dataAux = serializers.serialize("geojson", Datos.objects.all())
select * from "vistaPrincipal_datos";
```
Cuando obtenemos los datos ahora hay que serializarlos, para ésto nos axiliaremos de la clase serializer de django  **https://docs.djangoproject.com/en/3.0/ref/contrib/gis/serializers/** donde el primer parámetro indica a qué tipo de formato queremos serializar, en nuestro caso es json (o geojson en particular). Una vez teniendo serializando los datos debemos regresarlos a JavaScript para que pueda seguir con la ejecución.

```javascript
$.ajax({
        ...

        success:function(respuesta){
          responseFeatures = respuesta['features']
        }
});
```
Como nos interesa guardar los datos para las gráficas, aunque no es la única forma de hacerlo guardaremos en arreglos auxiliares, para recorrer el objeto json usaremos la estructura **for-each** de js  


```javascript
$.ajax({
        ...

        success:function(respuesta){
          responseFeatures = respuesta['features']
	  geoms = []
	  props = []
	  element = []
	  responseFeatures.forEach(element => geoms.push(element.geometry) && props.push(element.properties["delito"]) && 
	  entidad.push(element.properties["identidad"]));
        }
});
```
Lo que hicismo fue iterar sobre cada elemento dentro de las responseFeatures del geojson y guardamos en el respectivo arreglolos datos. Ahora, guardaremos las coordenadas de los puntos en otro arreglo
```javascript
$.ajax({
        ...

        success:function(respuesta){
          responseFeatures = respuesta['features']
	  geoms = []
	  props = []
	  element = []
	  responseFeatures.forEach(element => geoms.push(element.geometry) && props.push(element.properties["delito"]) && 
	  entidad.push(element.properties["identidad"]));
	  auxPuntosFinal = []
          geoms.forEach( function(element) {
            auxPuntos = []
            auxPuntos.push(element.coordinates[0][1])
            auxPuntos.push(element.coordinates[0][0])
            auxPuntosFinal.push(auxPuntos)
          });
	  
        }
});
```
Iteramos sobre las geometrías para las coorendas de cada punto y lo guardarmos como sub-arreglos de longitud 2 para posteriormente iterar sobre el arreglo de arreglos resultante y obtener la latitud y longitud  

```javascript
$.ajax({
        ...

        success:function(respuesta){
          responseFeatures = respuesta['features']
	  geoms = []
	  props = []
	  element = []
	  responseFeatures.forEach(element => geoms.push(element.geometry) && props.push(element.properties["delito"]) && 
	  entidad.push(element.properties["identidad"]));
	  auxPuntosFinal = []
          geoms.forEach( function(element) {
            auxPuntos = []
            auxPuntos.push(element.coordinates[0][1])
            auxPuntos.push(element.coordinates[0][0])
            auxPuntosFinal.push(auxPuntos)
          });
	  datos = []
          auxPuntosFinal.forEach( function(element) {
              dataAux = {
                         lat: element[0],
                         lng:element[1]
                       }
              datos.push(dataAux)
          });
	  
        }
});
```
Ahora que tenemos los datos para el mapa de calor (las coordenadas) se las asignaremos a nuestro objeto testData el arreglo datos de la siguiente forma:  


```javascript
$.ajax({
        ...
	varData = datos
        
});
```
Y le asignamos los puntos a nuestro mapa de calor a través del método setData()  
```javascript
$.ajax({
        ...
	heatmapLayer.setData(testData);
        
});
```

Ahora le pasaremos los datos para generar las gráficas, mandamos a llamar la función graficar(a,b) con los siguientes parámetros   

```javascript
$.ajax({
        ...
	 graficar(entidad, props)
        
});
```

Ahora debemos tener lo siguiente en nuestro mapa:  

<p align="center"> 
<img src="../img/mapa01.png">
</p>  

Notemos que en nuestro html tenemos  

```html

      <div class="chart-container" >
        <canvas id="myChartGraph" width="100" height="100" style="margin-left:100px;"></canvas> 
        <canvas id="myChartGraph1" width="100" height="100" style="margin-left:200px;"></canvas> 
       </div> 
```  
Tenemos dos canvas donde se dibujarán nuestras gráficas, ahora debemos inicializarlas en el código JS.  

```javascript
    var myChart;
    var myChart1;
    function graficar(datos, delito){
      var jsonData = JSON.stringify(datos)
      var delitoData = JSON.stringify(delito)


      var ctx = document.getElementById("myChartGraph").getContext("2d")
      var ctx1 = document.getElementById("myChartGraph1").getContext("2d")

      if(myChart != null && myChart1 != null){
        myChart.destroy();
        myChart1.destroy();
      }
    //... 
     }  
```    
Debemos crear la variable a la cual asignar los canvas, dado que estamos recibiendo dos arreglos usamos la función de JS JSON.stringify() para convertir el arreglo en una cadena, después usamos el método document.getElementById() para regresar un objeto de tipo **Element** que representa el elemento cuyo id coincide con la cadena y getContext es un método de canvas de html para devolver un "contexto de dibujo" y con 2d le estamos indicando que el tipo de contexto es de dos dimensiones. Con la estructura **if** checamos si ya estaba previamente inicializada y de ser así la destruimos para poder volver a usar el canvas y  que los datos se sobreescriban de forma correcta.  
```javascript
//...
//Indicamos que la letra en las gráficas sea blanca
Chart.defaults.global.defaultFontColor = 'white';
```  
Ahora vamos a filtrar los datos con ayuda de una estructura **switch-case** (No es la única forma de hacerlo pero es intuitiva)  

```javascript
      //...
      var numPeaton = 0;
      var numMoto = 0;
      var numPasajero = 0;
      var numCiclista = 0;
      var numConductor = 0;
      for (var i = 0; i <= datos.length - 1; i++) {
        console.log(datos[i])
        switch(datos[i]){
          case "PEATON":
              numPeaton +=1;
            break;
          case "MOTOCICLISTA":
              numMoto+=1;
              break;
          case "PASAJERO":
            numPasajero+=1;
            break;
          case "CICLISTA":
            numCiclista+=1;
            break;
          case "CONDUCTOR":
            numConductor+=1;
            break;
          default:
            break;
          

        }
      }
      var atropellado = 0
      var colision = 0
      var caida = 0
      var transito = 0
      for (var i = 0; i <= delitoData.length - 1; i++) {
        switch(delito[i]){
          case "HOMICIDIO CULPOSO POR TRÁNSITO VEHICULAR (CAIDA)":
              atropellado +=1;
            break;
          case "HOMICIDIO CULPOSO POR TRÁNSITO VEHICULAR (COLISION)":
              colision+=1;
              break;
          case "HOMICIDIO CULPOSO POR TRÁNSITO VEHICULAR (ATROPELLADO)":
              caida+=1;
            break;
          case "HOMICIDIO CULPOSO POR TRÁNSITO VEHICULAR":
              transito+=1;
            break;
          
          default:
            break;
          

        }
      }
	...
```  
Tenemos el número de ocurrencia de cada cadena con lo cual ya solo debemos crear nuestras gráficas con éstos datos de la siguiente forma:  

```javascript
    //... 
    myChart = new Chart(ctx, {
          type:"doughnut", //Indicamos el tipo de gráfica
          data: {
            labels:['PEATON','MOTOCICLISTA','PASAJERO','CICLISTA','CONDUCTOR'], //Damos las cabezeras a la gráfica
            datasets:[{
              label:'Tipo de Entidad',
	      //Aquí le pasamos los datos  numéricos que conseguimos previamente
              data:[numPeaton, numMoto, numPasajero, numCiclista, numConductor],
              //indicamos los colores para cada 
	      backgroundColor:[ 
                'rgb(219,34,52)',  
                'rgb(31,55,222)',
                'rgb(52,201,60)',
                'rgb(213,0,10)',
                'rgb(224,236,4)',
              ]
            }],
            },
	  //Aquí indicamos toda la configuración opcional de la gráfica
          options:{
            //Ajustamos la escala
	    scales:{
              yAxes:[{
                ticks:{
	          //indicamos la orientación
                  beginAtZero:true
                }
              }]
            },
	    //Le indicamos que el canvas sea responsivo 
            responsive:true
          }
      });
      //Con ésta función le decimos que se actualice el canvas con la nueva configuración
      myChart.update();
      
      //Éste canvas es análogo
      myChart1 = new Chart(ctx1, {
          type:"bar",
          data: {
            labels:['CAIDA','COLISION','ATROPELLADO','TRÁNSITO VEHICULAR'],
            datasets:[{
              label:'Tipo de delito',
              data:[caida, colision,atropellado,transito],
              backgroundColor:[
                'rgb(219,34,52)',
                'rgb(31,55,222)',
                'rgb(52,201,60)',
                'rgb(213,0,10)',
                'rgb(224,236,4)',
              ]
            }],
            },
          options:{
            scales:{
              yAxes:[{
                ticks:{
                  beginAtZero:true
                }
              }]
            },
            responsive:true
          }
      });
      myChart1.update();
      

     }
   }
   //...
```
Con ésto obtendriamos lo siguiente en nuestra página:   

<p align="center"> 
<img src="../img/charts.png">
</p> 

## Formularios para agregar datos a una tabla ##
Veremos cómo crear un formulario básico para cargar puntos a una tabla para ello nos iremos a nuestra clase **app/primeraVista/models.py**, crearemos un modelo llamado TablaPuntosEjemplo de la siguiente forma:  



```python
class TablaPuntosEjemplo(geomodels.Model):
	"""docstring for TablaPuntosEjemplo"""
	id_punto = models.AutoField(primary_key=True)
	descripcion_punto = models.CharField(max_length=200)
	coordenadas_punto = geomodels.PointField(srid=4326, default=None, null=True)

```  
Con lo que tenemos un **id_punto autoincrementable**, una **descripcion_punto** de nuestro punto donde podemos incluir alguna referencia
y por último un atributo llamado **coordenadas_punto** en el cual agregaremos una geometría de tipo punto la cual estará el sistema de referencia **4326** puede ser nula y por defecto es de tipo None.  
Recordemos que ésto nos hará el mapeo a una tabla en postgres, por lo cual debemos aplicar los comandos de **makemigration** y **migration**.  
<p align="center"> 
<img src="../img/modelo-puntos.png">
</p>  
<p align="center"> 
<img src="../img/migrations-point-model.png">
</p>

Antes de crear la vista debemos crear el formulario para agrear el punto, para ello crearemos un archivo dentro de la carpeta **app/primeraVista/** , con lo que tendremos la estructura:  
```
Geodjango_charts/
└── prueba/
   ├── manage.py
   ├── app/
       └── vistaPrincipal/
          └── migrations/
          └── admin.py
          └── app.py
          └── models.py
          └── tests.py
          └── views.py
          └── urls.py
	  └── forms.py
       └── __init__.py
   ├── prueba/
       └── __init__.py
       └── __pycache__
       └── settings.py
       └── urls.py
       └── wsgi.py
    ├── templates/
      └── primeraVista
        └──home.html
    ├── static/
      └── img/
      └── js/
        └──datos.js
        └──heatmap.js
        └──leaflet-heatmap.js
      └── css/
        └──mapa.css
```    
Dentro de nuestro archivo **forms.py** tendremos el siguiente código:  

```python   
from django.contrib.gis import forms
from .models import * 
from django.contrib.gis.geos import GEOSGeometry
import re
class AddPointForm(forms.Form): 
    descripcion = forms.CharField(max_length=200)
    punto = forms.CharField(max_length=200)
    def clean_punto(self):
        coordinates = self.cleaned_data['punto']
        latitude, longitude = coordinates.split(',', 1)
        coord_expr = re.compile('^[-]*[0-9]+.[0-9]+$')
        if((latitude == None or longitude == None) or not(coord_expr.match(latitude) and coord_expr.match(longitude))):
            print("entra al error")
            raise forms.ValidationError("No se puede generar un punto con longitud o latitud inválida")
        else:

        	return GEOSGeometry('POINT('+longitude+' '+latitude+')')
	

```

Donde cada atributo denotará un campo del formulario en el html, entonces por ejemplo descripción al igual que en el modelo, será un atributo del formulario de tipo caracter de longitud 200, por otro lado punto será un atributo de tipo cadena a diferencia del atributo correspondiente a punto en el modelo. En éste punto podrías preguntarte ¿no deben coincidir los tipos de dato del formulario con los del modelo?, en principio sí, pero en la realidad es que pueden no coincidir pero debemos hacer unos ajustes para que todo funcione correctamente.  

Como podemos observar tenemos un método **clean_punto** donde asignamos a **coordinates** el valor que reciba de el campo 'punto' del formulario, después parseamos la cadena a través de la coma ',' y con un max_split de 1, es decir solo debe dividir en dos el valor separado por coma; así una vez tengamos éstos valores asignanos haremos uso del API GEOSGeometry que se encargará de generar la geometría con los valores de latitud longitud que le estemos pasando.  


Ahora creemos la vista correspondiente, pero lo haremos de una forma equivalente para cubrir ambas en el curso, nos iremos al archivo **app/primeraVista/views.py**.  

Importamos las views de django **from django.views import View**, con ésto podemos hacer lo que se conoce **Clases como vistas** en lugar de lo que hicimos previamente que es conocido como **Funciones como vistas**, en otras palabras, ahora nuestras vistas pasan de ser funciones a clases, y al ser clases heredan todo lo de **programación orientada a objetos** de python (referencia ). Por otro lado también debemos importar el formulario que creamos previamente **from .forms import  AddPointForm** asignando eso al contexto, por último debemos renderizar el template crear_punto.html con el contexto que hemos definido previamente (el form).

```python   
#...
class AgregarPuntos(View):
    def get(self, request):
        form = AddPointForm()
        context = {"form": form}
        return render(request, 'primeraVista/crear_punto.html', context)  
    def post(self, request):
        form = AddPointForm(request.POST, request.FILES)
        print(form)
        if not form.is_valid():
            context = {"form": form}
            return render(request, 'primeraVista/create_point.html', context)
            
        TablaPuntosEjemplo.objects.create(
            descripcion_punto = form.cleaned_data["descripcion"],
            coordenadas_punto = form.cleaned_data["punto"],
            
        )
        return render(request, 'primeraVista/exito.html')
```  

Explicaremos paso a paso el código de arriba, primero al igual que en las funciones vistas previamente, necesitaremos definir las funciones get y post (al menos), empezando por **get** tenemos que instanciar el formulario que creamos previamente, asignarlo al contexto y aplicar **render** sobre el html y ese contexto para mostrar el formulario en el navegador.  
Por otro lado para el método **post** debemos instanciar el formulario con los datos del **request** y nos interesa verificar si contiene algún error en la entrada para controlar y mitigar potenciales errores, por tanto hacemos uso del método **form.is_valid()** el cual verificará si el formulario contiene errores o en otro caso es válido, pero como queremos cazar errores entonces usaremos la negación lógica de la condición qudeando de la siguiente forma **if not form.is_valid():** la cual nos garantiza que el código del interior de ese bloque se ejcutará en caso de que el form contega un error. Al volver a hacer render sobre el mismo html, con el contexto nuevo que ahora sabemos contiene un error, se desplegará un mensaje correspondiente al error, ésto surge de checar en la clase AddPointForm si la latitud o longitud es inválida, ejemplo:  

<p align="center"> 
<img src="../img/form-punto.png">
</p>  
<p align="center"> 
<img src="../img/form-punto-error.png">
</p> 
En otro caso, si ingresamos un punto válido obtendremos lo siguiente:  
<p align="center"> 

<img src="../img/correcto.png">
</p> 
.  
<p align="center"> 
<img src="../img/punto-correcto.png">
</p>  

**Nota, no funcionará todavía puesto que no hemos definido las rutas, pero lo cubriremos más adelante**  
Ahora bien por último una vez que el form sea válido habremos generado nuestra geometría, y debemos usar el ORM
para crear el objeto de la clase **TablaPuntosEjemplo** que corresponde a la parte dentro del método post  
```python
TablaPuntosEjemplo.objects.create(
            descripcion_punto = form.cleaned_data["descripcion"],
            coordenadas_punto = form.cleaned_data["punto"],
            
        )
        return render(request, 'primeraVista/exito.html')
```  
donde estaremos guardando el elemento en nuestra tabla y  renderizando un html con el mensaje de éxito.  

Por último para ésta parte solo nos queda **definir las rutas y los html para que funcione como las imágenes de ejemplo**, por lo cual abriremos el archivo **app/primeraVista/urls.py** el cual quedará de la siguiente forma:  

```python
from django.urls import path
from django.conf.urls import url,include
from app.vistaPrincipal.views import *
from django.views import View
from app.vistaPrincipal import views

app_name = "app"

urlpatterns = [
    path('',index),
    path('data/', data),
    path("puntos/crear-punto", views.AgregarPuntos.as_view(), name="crear_punto"),
]
```  

Como podremos observar tenemos dos imports nuevos, que corresponden a las bibliotecas o APIS que debemos llamar para poder trabar con nuestras clases como vistas, y es importante  notar  que agregamos un path a nuestro **urlpatterns**, distinto a los anteriores, en el primer parámetro estamos definiendo la ruta para la clase, es decir, la que debemos escribir en el navegador, el segundo parámetro nos ayuda a decirle a **djando** que queremos cargar una clase como vista con ayuda del método **as_view()** de views y el último parámetro es una etiqueta con la cual vamos a acceder a esa ruta desde el html.  
Para un mejor ahora veremos los html, con lo cual debemos crear 2 nuevos dentro de nuestra carpeta **templates/primeraVista**, uno lo llamaremos **crear_punto.html** y otro **exito.html**, primero analizemos **crear_punto.html**:  

<p align="center"> 
<img src="../img/crear_punto_template.png">
</p>  

Aquí solo definiremos un formulario a través de la etiqueta de html <form></form>, en la cual podremos observar que tiene action="./crear-punto" en el cual debemos indicarle la ruta que debe tomar el form para enviar la petición POST, en method="POST" le estamos indicando bajo qué método http enviará la petición como vimos previamente en el curso.  
Ahora con ayuda de un for iteramos el form que enviamos como context, en el cual indicamos el label_tag que corresponde al nombre del atributo dentro el form, el campo donde se introducirá y el mensaje de error en caso de existir. 

por otro lado solo necesitaremos crear un html llamado **exito.html**

```html

	<!DOCTYPE html>
	<html>
	<head>
		<title>Éxito</title>	
	</head>
	<body>
		<h1>Punto agregado correctamente</h1>
		
	</body>
	</html>
``` 

Así, tendremos nuestro formulario para agregar puntos a nuestra tabla de puntos.
Agregaremos un campo adicional para cargar imágenes por ejemplo, antes que nada, para hacer ésto debemos instalar a nuestro entorno la bibliotecta llamada **pillow** que sirve para el manejo y procesamiento de imágenes digitales en python.  
Para ello debemos escribir en nuestra terminal lo siguiente:  

<p align="center"> 
<img src="../img/pillow.png">
</p>   
Una vez instalado pillow, nos iremos a nuestro archivo **models.py** y agregaremos el campo de tipo imagen con lo que nuestro código quedaría de la siguiente forma:  

```python
from django.db import models
from django.contrib.gis.db import models as geomodels
from django.contrib.gis.db.models import PointField


class Datos(models.Model):
    id = models.IntegerField(primary_key=True)    	
    geom = geomodels.MultiPointField()
    field_1 = models.IntegerField()
    id_ssc = models.IntegerField()
    id_pgj = models.IntegerField()
    delito = models.CharField(max_length = 100)
    tipo_evento = models.CharField(max_length = 100)	
    fecha = models.DateTimeField()	
    identidad = models.CharField(max_length = 100)

def image_directory_path(instance, filename):
    """El directorio donde se guardará la imagen"""
    return f"sismo/{filename}"

class TablaPuntosEjemplo(geomodels.Model):
	"""docstring for TablaPuntosEjemplo"""
	id_puntos = models.AutoField(primary_key=True)
	descripcion_punto = models.CharField(max_length=200)
	coordenadas_punto = geomodels.PointField(srid=4326, default=None, null=True)
	imagen_punto = models.ImageField(upload_to=image_directory_path, default=None)
    
```  
Como podremos observar hay cosas nuevas que explicaremos a continuación, empezando por la definición de la función **directorio_imagenes** la cual es una función donde indicamos la carpeta donde se guardarán las imágenes de los puntos, en el **return** le estamos indicando como sufijo la cadena **sismo/imagenes/** la cual explicaremos más adelante y como nombre del archivo le dejaremos de  momento el nombre que traiga que por defecto la imagen al cargarse, aquí podriamos darle una regex para renombrarla.  
Por otro lado, agregamos un campo llamado imagen_punto el cual es una opción de los modelos de django para cargar imágenes como atributos, notemos que tiene un key_word llamado **upload_to** es ahí donde indicamos el directorio a donde guardar la imagen.  

Ahora debemos configurar nuestro proyecto para indicarle la ruta de los archivos dinámicos, tales como las imágenes, entonces vamos a abrir nuestro archivo **urls.py** pero de la carpeta prueba y agregaremos la siguiente línea:  
```python
from django.contrib import admin
from django.urls import path
from django.conf.urls import url ,include
from app.vistaPrincipal.urls import *
from django.conf.urls.static import static
from django.conf import settings

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app.vistaPrincipal.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```  
Ahora abrimos nuestro archivo **settings.py** y agregamos 

<p align="center"> 
<img src="../img/media-settings.png">
</p> 

por último crearemos una carpeta **media** a la altura del archivo **manage.py** y posteriormente dentro de **media** creamos una carpeta llamada **sismos**



<p align="center"> 
<img src="../img/carpeta-media.png">
</p> 

Ejecutamos el comando **python manage.py makemigrations**. Nos agregará el campo a nuestra tabla y solo nos quedará aplicar los cambios con **python manage.py migrate**.  

En nuestro formulario agregaremos el campo para la imagen, para ello iremos al archivo **forms.py**

```python
"""points forms."""
# Django
from django.contrib.gis import forms
from .models import * 
from django.contrib.gis.geos import GEOSGeometry
import re
class AddPointForm(forms.Form): 
    descripcion = forms.CharField(max_length=200)
    punto = forms.CharField(max_length=200)
    imagen = forms.ImageField()

    def clean_punto(self):
        coordinates = self.cleaned_data['punto']
        latitude, longitude = coordinates.split(',', 1)
        latitude = latitude.strip()
        longitude = longitude.strip()
        coord_expr = re.compile('^[-]*[0-9]+.[0-9]+$')
        if((latitude == None or longitude == None) or not(coord_expr.match(latitude) and coord_expr.match(longitude))):
            print("entra al error")
            raise forms.ValidationError("No se puede generar un punto con longitud o latitud inválida")
        else:

        	return GEOSGeometry('POINT('+longitude+' '+latitude+')')
```




<p align="center"> 
<img src="../img/Punto-imagen.png">
</p> 

## Breve introducción a la terminal shell 

### GEOSGeometry  
**¿Qué es GEOSGeometry?** Es una biblioteca de código abierto que implementa las funcionalidades de OpenGIS para el manejo de predicados espaciales **SQL** y operadores espaciales.  

Antes de pasar al manejo en django, probaremos crear unas instancias brevemente en la terminal interactiva de python, para ello con el comando **python manage.py shell** abriremos una terminal.
Nuestra terminal deberá verse de la siguiente forma: 

<p align="center"> 
<img src="../img/terminal-shell.png">
</p>  

Crearemos nuestra primer geometría con representación **wkt**  
<p align="center"> 
<img src="../img/wtk-shell.png">
</p>  

Así también podemos usar otras representaciones como la codificación **hexadecimal, geojson** , etc. Recordemos que el sistema hexadecimal es un sistema de numeración posicional base 16, es un sistema muy importante en cuanto al manejo interno de la memoria de la computadora aunque ese tema no será abordado a profundidad.  

<p align="center"> 
<img src="../img/hexa-shell.png">
</p>

Ahora con representación geojson  

<p align="center"> 
<img src="../img/geojson-shell.png">
</p>  

Otra forma de crear objetos es usar directamente los constructores (un constructor podemos pensarlo como un molde para crear objetos de cierto tipo) para crear objetos geométricos específicos, por ejemplo:  

<p align="center"> 
<img src="../img/punto-shell.png">
</p>  

Podemos pasar explicitamente el sistema de referencia en los parámetros, además, los objetos geométricos pueden ser tratados e iterados como cualquier otro objeto en python.  


<p align="center"> 
<img src="../img/listas-comprension-1.png">
</p>  

Las **listas por comprensión** es una forma abreviada que nos ofrece python para hacer algo con los objetos de una estructura de datos como listas o diccionarios.  
En el ejemplo estamos imprimiendo la coordenada por cada coordenada en el LineString, éste, es un objeto iterable, es decir que podemos recorrer sus elementos.

<p align="center"> 
<img src="../img/srid-dinamico.png">
</p>  

De igual forma podemos asignar como en la imagen el srid de forma dinámica, ésto nos puede servir para asignar posteriormente el sistema de referencia.  


## Agregando datos desde mapa leaflet

Como última sección del curso, veremos brevemente cómo cargar un mapa leaflet y meterle distintos tipos de geometrías desde JavaScript. 

Primero vamos a crear nuestra vista basada en clase **Mapa02** para ello abriremos nuestro archivo **primeraVista/views.py** y agregaremos la siguiente clase:
```python  
#código previo  
class Mapa02(View):
    def get(self, request):      
        return render(request, 'primeraVista/mapa_02.html')
```  

Una vez agregado, nos vamos a **urls.py** y agregaremos la ruta (**path**) con lo que nuestro **urlpatterns** queda:

```python
urlpatterns = [
    path('',index),
    path('data/', data),
	path("puntos/crear-punto/", views.AgregarPuntos.as_view(), name="crear_punto"),
	path("mapa2/", views.Mapa02.as_view(), name="mapa_02"),
]
```
Con ésto ya solo nos queda crear nuestro template para el nuevo mapa pero antes debemos cambiar algunas cosas en nuestro archivo **home.html**  

<p align="center"> 
<img src="../img/block-content.png">
</p>  

Notemos que debemos agregar las líneas sombreadas, ésto es algo que nos proporciona django para "agrupar" contenidos dentro de nuestro html, en éste caso estamos indicando que lo que está dentro de **body_content** sobreescribirá el body dentro de otros archivos html, es una forma de evitar estar definiendo mismo código en distintos templates, una vez agregada esa línea podemos pasar a nuestro template **mapa_02.html**.  


<p align="center"> 
<img src="../img/block-content.png">
</p>  

Para extender un archivo usaremos el tag sombreado, ahí indicamos el template que queremos extender y por consiguiente se nos cargarán todos los imports del **home** cuando hagamos ésto debemos tener cuidado de la redundancia y que probablemente extendamos algún archivo que tal vez ya no nos interese, sin embargo de momento omiteremos esos detalles.  

<p align="center"> 
<img src="../img/mapa_02-html.png">
</p>    

```html
<div class="content">

	<div class ='mapa-2' style="text-align: center;">
        <div  id="mapid" style="min-zoom: 3;  display: inline-block; height: 500px; width: 550px;"></div>    
    </div>
	
</div>

```
Omitiremos la explicación de la estructura html puesto que ya cubrimos esa parte previamente y nos enfocaremos al archivo **JS**.
Creamos nuestro archivo **JS**  **mapa02.js** y  ya no usaremos  **$(document).ready(function()...** de Jquery, simplemente definiremos nuestro mapa de la siguiente forma:  

```javascript
map2 = new L.Map('mapid', {
        center: new L.LatLng(19.27, -99),
        zoomControl: true,
        minZoom: 11,
        maxZoom:20,
        layers: []
      }).setView([19.40, -99.14], 11);
```  

Aquí solo estamos dándole la configuración inicial, lo centramos en la **CDMX**, le damos un zoom máximo y mínimo, le damos la capacidad de controlar el zoom, no le damos capas y centramos la vista. Ésto lo habíamos hecho previamente, ahora agreguemos una capa con un tile personalizado **crétidos a maxbox por el tile**   

[] **https://www.mapbox.com/?utm_medium=sem&utm_source=google&utm_campaign=sem|google|brand|chko-googlesearch-pr01-mapboxbrand-br.exact-intl-landingpage-search&utm_term=brand&utm_content=chko-googlesearch-pr01-mapboxbrand-br.exact-intl-landingpage-search&gclid=EAIaIQobChMIi_CetKGF6QIVg__jBx0uvgkoEAAYASAAEgIMH_D_BwE**   

```javascript
L.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw', {
		maxZoom: 18,
		attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, ' +
			'<a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, ' +
			'Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
		id: 'mapbox/streets-v11',
		tileSize: 512,
		zoomOffset: -1
	}).addTo(map2);
```  
Nuestro mapa deberá verse de la siguiente forma:  

<p align="center"> 
<img src="../img/mapa_02_inicial.png">
</p>  

Agregaremos unos pocos datos al mapa: 
1. Un punto   
	Para ello agregamos lo siguiente al código:  
	```javascript  
	//código previo
	L.marker([19.40, -99.14]).addTo(map2)
		.bindPopup("<b>Aquí podemos poner elementos html</b><br />Soy un popup.").openPopup();
	```   
	Aquí estamos añadiendo un marcador con sus respectivas **Lat**, **Long**, un popUp con contenido html y
	a través de la función openPopup() indicamos que se abra en el mapa en cuando se agregue obteniendo lo siguiente:  
	
	<p align="center"> 
	<img src="../img/marcador_mapa02.png">
	</p>
2. Un círculo  
	Agregamos lo siguiente al código:  
	```javascript  
	//Código previo
	L.circle([19.36, -99.14], 500, {
		color: 'red',
		fillColor: '#f03',
		fillOpacity: 0.5
	}).addTo(map2).bindPopup("<b>Click sobre el círculo</b>");
	```     
	Aquí estamos indicando la opacidad, el color del círculo y del relleno del mismo. Deberíamos poder visualizarlo de la  
	siguiente forma: 
	
	<p align="center"> 
	<img src="../img/circulo_mapa02.png">
	</p>
	
	
3. Un polígono  
	Agregamos lo siguiente al código:  
	```javascript  
	//Código previo
	var polygon = L.polygon([
	    [19.39, -99.14],
	    [19.41, -99.15],
	    [19.41, -99.13]
	]).addTo(map2).bindPopup("<b>Click sobre el polígono</b>");
	```   
	Aquí indicamos cada una de las esquinas que conforman el polígono obteniendo el siguiente resultado:  
	 <p align="center"> 
	<img src="../img/poligono_mapa02.png">
	</p>
	
4. Datos desde geojson  
	Éste es un punto interesante, leaflet nos permite cargar geojson directamente, para ello primero vamos a bajar los datos  
	del siguiente link: **https://datos.cdmx.gob.mx/explore/dataset/alcaldias/table/**  
	
	<p align="center"> 
	<img src="../img/geojson_mapa02_ini.png">
	</p>  
	
	Filtramos por delegación **Tlalpan** 
	
	<p align="center"> 
	<img src="../img/circulo_mapa02.png">
	</p>  
	
	Exportamos los datos a **geojson**  
	
	<p align="center"> 
	<img src="../img/geojson_mapa02_ini.png">
	</p>  
	Ahora solo copiaremos a la carpeta media el archivo geojson generado  
	
	<p align="center"> 
	<img src="../img/alcaldias.png">
	</p>  
	Una vez hecho ésto agregaremos el siguiente código:   
	
	```javascript    
	//código previo
	$.getJSON("../../../media/alcaldias.geojson", function (data) {
    		var datalayer = L.geoJson(data, { 
        		pointToLayer: function (feature, latlng) {
            			return L.circleMarker(latlng, geojsonMarkerOptions)
        		}	
    		}).addTo(map2).bindPopup("<b>Delegación tlalpan</b><br/>").openPopup();
	})
	```
	Notemos que con **/../../../** subimos hasta la raiz que correspondería a donde está el archivo **manage.py** es  
	por ello que después escribimos **/media/alcaldias.geojson**, le estámos diciendo que todo el polígono muestre un popup  
	con la leyenda delegación tlalpan si hacemos click sobre él:  
	
	<p align="center"> 
	<img src="../img/tlalpan.png">
	</p>  
	Si lo queremos quitar solo debemos dejarlo de la siguiente forma:  
	
	```javascript  
	$.getJSON("../../../media/alcaldias.geojson", function (data) {
	    var datalayer = L.geoJson(data, {

		pointToLayer: function (feature, latlng) {
		    return L.circleMarker(latlng, geojsonMarkerOptions)
		}
	    }).addTo(map2);
	})
	```  
5. Puntos al mapa  
	Ahora con ayuda de los métodos de leaflet, podemos agregar puntos de forma dinámica al mapa haciendo lo siguiente:  
	```javascript  
		//código previo
		var popup = L.popup();
		function onMapClick(e) {
			L.marker(e.latlng).addTo(map2)
			.bindPopup("<b>Punto agregado</b> " + e.latlng.toString() ).openPopup();
		}
		map2.on('click', onMapClick);
	```   
	
	No es muy dificil intuir el potencial que tiene ésto, **onMapClick()** se manda a llamar cada que hacemos click sobre el mapa  
	de leaflet, en éste caso estamos agregando simplemente un punto al mapa y mostrando las coordenadas en un popup  
	
	
	<p align="center"> 
	<img src="../img/final.png">
	</p>   
	
	Lo que hagamos en la función **onMapClick()** ya dependerá del uso que se le de a la aplicación, podríamos enviarlo a   
	la base de datos para guardarlos a través de ajax, guardarlo en un formulario y enviarlo por **post** .   
	Realmente su uso es muy escalable.


