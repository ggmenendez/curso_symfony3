# Twig

## Twig Template Caching

Twig es rápido porque cada plantilla se compila a código nativo PHP y se cachea. 
Esto ocurre de forma automática. Sin embargo, durante el desarrollo, Twig no cachea 
sino que recompila las plantillas después de cada cambio. Esto hace que Twig sea
rápido en producción pero cómodo durante el desarrollo.


## Sintaxis de Twig

Twig define tres tipos de sintaxis:

{{ ... }}
Renderiza en la plantilla el valor de una variable o el resultado de una expresión.

{% ... %}
Ejecuta instrucciones lógicas (condiciones, bucles, etc).

{# ... #}
Comentarios. Los comentarios de Twig no se incluyen en las páginas renderizadas.

Twig también tiene filtros, que modifican el contenido que va a ser renderizado.
{{ title|upper }}

Twig viene con una larga lista de etiquetas, filtros y funciones disponibles por defecto. 
También se pueden añadir filtros y funciones personalizados con extensiones de Twig.

https://twig.symfony.com/doc/2.x/tags/index.html
https://twig.symfony.com/doc/2.x/filters/index.html
https://twig.symfony.com/doc/2.x/functions/index.html

### Tipos de datos

#### Cadenas de texto

Van entre comillas. 

- Con comillas simples o dobles: "Hello World"
- El caracter de escape es la barra invertida (\): 'It\\'s good'.
- La barra invertida se escapa con otra barra invertida: 'c:\\\\Program Files'

#### Números

Simplemente se escriben tal cual.

- Si hay un punto, el número será un *float*. Si no lo hay, será un *integer*: 43 / 19.99

#### Arrays

Se definen entre corchetes cuadrados, separando los elementos con comas: ["foo", "bar"]

#### Hashes

Son listas de parejas clave/valor, separadas por comas y encerradas entre llaves: {"foo": "bar"}

```twig
{# keys as string #}
{ 'foo': 'foo', 'bar': 'bar' }
```

```twig
{# keys as names (equivalent to the previous hash) #}
{ foo: 'foo', bar: 'bar' }
```

```twig
{# keys as integer #}
{ 2: 'foo', 4: 'bar' }
```

```twig
{# keys as expressions (the expression must be enclosed into parentheses) #}
{% set foo = 'foo' %}
{ (foo): 'foo', (1 + 1): 'bar', (foo ~ 'b'): 'baz' }
```

#### Booleanos

Se escriben tal cual:
- true
- false

#### Null

Se escribe tal cual
- null
- none (es un alias de null)


Los arrays y los  hashes se pueden anidar:


{% set foo = [1, {"foo": "bar"}] %}

### Operadores matemáticos

+: Suma: {{ 1 + 1 }}

-: Resta: {{ 3 - 2 }}

/: División. El resultado es un float: {{ 1 / 2 }} es {{ 0.5 }}.

%: Resto de una división: {{ 11 % 7 }} is 4.

//: División más redondeo a la baja: {{ 20 // 7 }} is 2, {{ -20 // 7 }} is -3

*: Multiplicación: {{ 2 * 2 }}

**: Potencia: {{ 2 ** 3 }} es 8.

Twig intenta hacer cast de los operandos antes de realizar la operación

### Operadores lógicos

- and
- or
- not

### Comparadores

La lista de comparadores es la siguiente: ==, !=, <, >, >=, and <=.

Además existen los comparadores start, end y matches

{% if 'Fabien' starts with 'F' %}
{% endif %}

{% if 'Fabien' ends with 'n' %}
{% endif %}

{% if phone matches '/^[\\d\\.]+$/' %}
{% endif %}

### Operador in

{{ 1 in [1, 2, 3] }}
{{ 'cd' in 'abcde' }}

{% if 1 not in [1, 2, 3] %}
{# is equivalent to #}
{% if not (1 in [1, 2, 3]) %}

### Operador de testeo: is

{# find out if a variable is odd #}
{{ name is odd }}

{% if post.status is constant('Post::PUBLISHED') %}

{% if post.status is not constant('Post::PUBLISHED') %}
{# is equivalent to #}
{% if not (post.status is constant('Post::PUBLISHED')) %}

https://twig.symfony.com/doc/2.x/tests/index.html

### Otros operadores

#### Operador |

Aplica un filtro

#### Operador ..

Crea una secuencia (equivalente a la función range()

```twig
{{ 1..5 }}

{# equivalent to #}
{{ range(1, 5) }}
```

#### Operador ~

Convierte los operadores en strings y los concatena

{{ "Hello " ~ name ~ "!" }} => Hello John!.

#### Operador .

```
{{ user.edad }}
```

Cuando twig se encuentra un . realiza las siguientes operaciones en la capa PHP:

1. Comprueba si user es un array y *edad* un elemento de dicho array
2. Si no, si user es un objeto, comprueba que *edad* es una propiedad de de dicho objeto
3. Si no, si user es un objeto, comprueba que *edad()* es un método público de dicho objeto
4. Si no, si user es un objeto, comprueba que *getEdad()* es un método público de dicho objeto
5. Si no, si user es un objeto, comprueba que *isEdad()* es un método público de dicho objeto
6. Si no, si user es un objeto, comprueba que *hasEdad()* es un método público de dicho objeto
7. Si no, devuelve el valor *null*.

#### Operador []

```
{{ user['edad'] }}
```

1. Comprueba si user es un array y edad un elemento de dicho array
2. Si no, devuelve el valor *null*.

#### El método attribute()

No es un operador, pero es una función auxiliar para el operador .

```
{{ attribute(user, 'fecha-nacimiento') }}
```

Esta función es útil para acceder atributos con caracteres especiales que pueden dar lugar a confusión.

También para acceder a atributos dinámicamente.



#### El operador ? y ?\:

{{ foo ? 'yes' : 'no' }}

{{ foo ?\: 'no' }} equivale a {{ foo ? foo : 'no' }}

{{ foo ? 'yes' }} equivale a {{ foo ? 'yes' : '' }}

#### El operador ??

{# Devuelve el valor de *foo* si está definido y no es nulo. En cualquier otro caso, devuelve el valor 'no' #}

{{ foo ?? 'no' }}


### String interpolation: (#{expression})

Permite evaluar extresiones dentro de strings. Solamente funciona en strings con comillas dobles.

{{ "foo #{bar} baz" }}

{{ "foo #{1 + 2} baz" }}


### Variables globales

Estas variables están siempre disponibles en las plantillas:

_self: Hace referencia al nombre de la plantilla actual
_context: Hace referencia al contexto actual
_charset: Hace referencia al charset actual


## La etiqueta set

Se utilizar para declarar y establecer valor de variables

```
{% set foo = 'foo' %}
{% set foo = [1, 2] %}
{% set foo = {'foo': 'bar'} %}
```

## La etiqueta if

```
{% if kenny.sick %}
    //---
{% elseif kenny.dead %}
    //---
{% else %}
    //---
{% endif %}


{% if user.edad > 18 and user.active %}
...
{% if not user.active %}
...

```


## La etiqueta for

```
<h1>Members</h1>
<ul>
    {% for user in users %}
        <li>{{ user.username|e }}</li>
    {% endfor %}
</ul>
```

Se utiliza para recorrer arrays o objetos que implementen la interfaz *Traversable*.
http://php.net/manual/es/class.traversable.php


```
{% for i in 0..10 %}
    * {{ i }}
{% endfor %}
```

```
{% for letter in 'a'..'z' %}
    * {{ letter }}
{% endfor %}
```

### La variable loop

```
{% for user in users %}
    {{ loop.index }} - {{ user.username }}
{% endfor %}
```

Dentro de un bucle for, se puede acceder a una variable especial *loop* con los siguientes atributos:

- loop.index	The current iteration of the loop. (1 indexed)
- loop.index0	The current iteration of the loop. (0 indexed)
- loop.revindex	The number of iterations from the end of the loop (1 indexed)
- loop.revindex0	The number of iterations from the end of the loop (0 indexed)
- loop.first	True if first iteration
- loop.last	True if last iteration
- loop.length	The number of items in the sequence
- loop.parent	The parent context

Nota: Las variables loop.length, loop.revindex, loop.revindex0, y loop.last solamente 
están disponibles para arrays PHP u objetos que implementan la interfaz *Countable*.
Tampoco están disponibles si se recorre el bucle con una condición.

### Añadiendo una condición en el bucle

```
<ul>
    {% for user in users if user.active %}
        <li>{{ user.username|e }}</li>
    {% endfor %}
</ul>
```

### La cláusula else

Si no se produce ninguna iteración porque la secuencia estaba vacía, se puede renderizar
un bloque utilizando la cláusula *else*.

```
<ul>
    {% for user in users %}
        <li>{{ user.username|e }}</li>
    {% else %}
        <li><em>no user found</em></li>
    {% endfor %}
</ul>
```


### Iterar con acceso a las claves


```
<h1>Members</h1>
<ul>
    {% for key, user in users %}
        <li>{{ key }}: {{ user.username|e }}</li>
    {% endfor %}
</ul>
```



## Filtros

Se aplican con el operador |

```
{{ name|upper }}
```

Son concatenables

```
{{ name|striptags|title }}
```

Admiten parámetros

```
{{ list|join(', ') }}
```

Se pueden aplicar a bloques completos

```
{% filter upper %}
    Este texto se transformará en mayúsculas
{% endfilter %}
```



### Filtro slice

```
<h1>Top Ten Members</h1>
<ul>
    {% for user in users|slice(0, 10) %}
        <li>{{ user.username|e }}</li>
    {% endfor %}
</ul>
```

## Funciones

Se utilizan de forma similar a la mayoría de lenguajes de programación

```
{% for i in range(0, 3) %}
    {{ i }},
{% endfor %}
```


### Función path()

Extensión de symfony.

```php
// src/AppBundle/Controller/WelcomeController.php

// ...
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;

class WelcomeController extends Controller
{
    /**
     * @Route("/", name="welcome")
     */
    public function indexAction()
    {
        // ...
    }
}
```


```twig
<a href="{{ path('welcome') }}">Home</a>
```



#### Función path() con parámetros

```php
// src/AppBundle/Controller/ArticleController.php

// ...
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;

class ArticleController extends Controller
{
    /**
     * @Route("/article/{slug}", name="article_show")
     */
    public function showAction($slug)
    {
        // ...
    }
}
```

```twig
{# app/Resources/views/article/recent_list.html.twig #}
{% for article in articles %}
    <a href="{{ path('article_show', {'slug': article.slug}) }}">
        {{ article.title }}
    </a>
{% endfor %}
```


### Función url()

Es una extensión de symfony.

<a href="{{ url('welcome') }}">Home</a>


### Función asset()

Es una extensión de symfony.

```twig
<img src="{{ asset('images/logo.png') }}" alt="Symfony!" />

<link href="{{ asset('css/blog.css') }}" rel="stylesheet" />
```

Genera rutas relativas al directorio webroot del proyecto.

http://example.com => /images/logo.png

http://example.com/my_app => /my_app/images/logo.png


### Función absolute_url()

Extensión de symfony.

```twig
<img src="{{ absolute_url(asset('images/logo.png')) }}" alt="Symfony!" />
```


### Argumentos nombrados

```
{% for i in range(low=1, high=10, step=2) %}
    {{ i }},
{% endfor %}
```

Usar argumentos nobrados hace que las plantillas sean más explícitas sobre el 
significado de los valores que se pasan como argumentos.

```
{{ data|convert_encoding('UTF-8', 'iso-2022-jp') }}

{# versus #}

{{ data|convert_encoding(from='iso-2022-jp', to='UTF-8') }}
```

Los argumentos nombrados permiten además omitir argumentos opcionales

```
{# the first argument is the date format, which defaults to the global date format if null is passed #}
{{ "now"|date(null, "Europe/Paris") }}

{# or skip the format value by using a named argument for the time zone #}
{{ "now"|date(timezone="Europe/Paris") }}
```

Se pueden mezclar argumentos posicionales con argumentos nombrados, en ese caso
los argumentos posicionales deben ser siempre los primeros

```
{{ "now"|date('d/m/Y H:i', timezone="Europe/Paris") }}
```


## Template Inheritance and Layouts - Las etiquetas extends y block

La clave para la herencia de plantillas es la etiqueta *{% extends %}*. 

Esta etiqueta le dice a Twig que primero evalúe la plantilla base, en la que se 
definirá el layout y uno o más bloques mediante la etiqueta *{% block %}*.

Después se renderiza la plantilla *hija*. En la plantilla hija, se redefinen los 
bloques que se deseen. Los bloques que no redefinan en la plantilla hija, se 
renderizarán tal como los haya definido la plantilla base.

Se pueden utilizar tantos niveles de herencia como se quieran. Al trabajar con 
herencia de plantillas, hay que tener en cuenta unas cuantas cosas:

La etiqueta *{% extends %}* debe ser la primera etiqueta de dicha plantilla.


## Función parent()

```
{# app/Resources/views/contact/contact.html.twig #}
{% extends 'base.html.twig' %}

{% block stylesheets %}
    {{ parent() }}

    <link href="{{ asset('css/contact.css') }}" rel="stylesheet" />
{% endblock %}
```

## Función include()

Se utiliza para incluir el contenido de una plantilla en otra plantilla. Se pueden pasar
variables a la plantilla incluida. Además, la plantilla incluida tiene acceso a las variables
de la plantilla inclusora, pero no es una buena práctica.

```twig
{# app/Resources/views/article/article_details.html.twig #}
<h2>{{ article.title }}</h2>
<h3 class="byline">by {{ article.authorName }}</h3>

<p>
    {{ article.body }}
</p>
```

```twig
{# app/Resources/views/article/list.html.twig #}
{% extends 'layout.html.twig' %}

{% block body %}
    <h1>Recent Articles<h1>

    {% for article in articles %}
        {{ include('article/article_details.html.twig', { 'article': article }) }}
    {% endfor %}
{% endblock %}
```


## Acceso a variables globales del framework

- app.user: Objeto *user* de symfony o *null* si no existe.

- app.request: El objeto Request

- app.session: El objeto Session o null si no existe.

- app.environment: El nombre del entorno actual (dev, prod, etc).

- app.debug: True si estamos en modo debug. False si no.

```html
<p>Username: {{ app.user.username }}</p>
{% if app.debug %}
    <p>Request method: {{ app.request.method }}</p>
    <p>Application Environment: {{ app.environment }}</p>
{% endif %}
```

## Cómo inyectar variables globales en las plantillas

https://symfony.com/doc/current/templating/global_variables.html

A veces queremos que una variables sea accesible a todas las plantillas. Se pueden
definir variables globales para twig en el fichero config.yml

```yml
// app/config/config.yml
twig:
    # ...
    globals:
        ga_tracking: UA-xxxxx-x
```

 
Este ejemplo hace que la variable ga_tracking esté disponible en todas las plantillas twig

<p>The google tracking code is: {{ ga_tracking }}</p>


O de forma equivalente:

```yml
// app/config/parameters.yml
parameters:
    ga_tracking: UA-xxxxx-x

// app/config/config.yml
twig:
    globals:
        ga_tracking: '%ga_tracking%'
```

El valor de la variable puede ser un valor en sí, o un servicio.

```yml
// app/config/config.yml
twig:
    # ...
    globals:
        # the value is the service's id
        user_management: '@AppBundle\Service\UserManagement'
```

NOTA
El servicio no se carga de forma *lazy*. Es decir, tan pronto como se carga twig, 
se instancia el servico, incluso aunque esa variable global no se utilice nunca.

Para definir un servicio como variable globa de twig, se utiliza el prefijo @.


## El filtro |raw

Twig realiza "output escaping" automáticamente cuando renderiza cualquier contenido
con el propósito de protegernos contra ataques Cross Site Scripting (XSS)

Supongamos que el contenido de la vairable *description* es "I <3 this product":

```twig
<!-- output escaping is on automatically -->
{{ description }} <!-- I &lt;3 this product -->

<!-- disable output escaping with the raw filter -->
{{ description|raw }} <!-- I <3 this product -->
```


## Whitespace Control

Se pueden eliminar los espacios en blanco con la etiqueta {% spaceless %}

```twig
{% spaceless %}
    <div>
        <strong>foo bar</strong>
    </div>
{% endspaceless %}

{# output will be <div><strong>foo bar</strong></div> #}
```

o bien con el modificador -

```twig
{% set value = 'no spaces' %}
{#- No leading/trailing whitespace -#}
{%- if true -%}
    {{- value -}}
{%- endif -%}

{# output 'no spaces' #}
```


```twig
{% set value = 'no spaces' %}
<li>    {{- value }}    </li>

{# outputs '<li>no spaces    </li>' #}
```



## Cómo escribir una extensión de Twig

Se puede extender twig con etiquetas filtros o funciones personalizados.

https://symfony.com/doc/current/templating/twig_extension.html


## Nomenclatura de las plantillas y localizaciones

Las plantillas twig pueden localizarse en tres lugares diferentes:

- app/Resources/views/
Aquí están las plantillas de nuestra aplicación.

- vendor/path/to/CoolBundle/Resources/views/
Dentro de cada bundle de terceros se alojan sus propias plantillas en su directorio Resources/views/.

src/MyBundle/Resources/views/
Puedes alojar las plantillas de tu proyecto en tu propio bundle dentro de src. Esta localización se suele utilizar 
si se está desarrollando un bundle que será distribuido a terceros.


### Referenciar plantillas de un bundle

Para hacer referencia a una plantilla de un bundle, symfony utiliza la siguiente sintaxis:

@BundleName/directory/filename.html.twig

Por ejemplo:

@AcmeBlog/Blog/index.html.twig

@AcmeBlog: Es el nombre del Bundle sin el sufijo "Bundle". Esta plantilla estál localizada en AcmeBlogBundle (e.g. src/Acme/BlogBundle);

Blog: (directorio) indica que la plantilla está en el subdirectorio *Blog* dentro de Resources/views/;

index.html.twig: (archivo) el nombre del archivo es index.html.twig.

Suponiendo que el bundle AcmeBlogBundle esté en src/Acme/BlogBundle, entonces la localización de la plantilla sería src/Acme/BlogBundle/Resources/views/Blog/index.html.twig.

Si la plantilla está directamente en el directorio Resources/views/ entonces se omite la parte del directorio
@AcmeBlog/layout.html.twig: 

### Sobreescribir plantillas

Se puede sobreescribir una plantilla localizada en un bundle creando otra plantilla en app/Resources/nombreBundle/views

Por ejemplo, la siguiente plantilla 

app/Resources/AcmeBlogBundle/views/Blog/index.html.twig

sobreescribe a la plantilla 

src/AcmeBlogBundle/Resources/views/Blog/index.html.twig





https://twig.symfony.com/doc/2.x/templates.html