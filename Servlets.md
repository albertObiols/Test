## 1. Java Servlets
Características de un JAVA SERVLET:

- Corre dentro de un **Servlet Container** (Servidor WEB: Apache.. ).
- Es un objeto de JAVA que responde a peticiones HTTP.
	- 	Request -> Response
- Es solo una parte de una web-application
- Dentro de un Servlet Container pueden ejecutarse múltiples aplicaciones web, cada una con sus servlets.
- Una java web application puede contener también:
	- JSP
	- JSF
	- Web Services
![alt text][logo]
[logo]: http://tutorials.jenkov.com/images/java-servlets/overview-2.png


### HTTP Request and Response

1. El navegador envia una request HTTP a el **Java Web Server**
2. Java Web Server mira si la request va dirigida a un **Servlet**
3. Si ese es el caso Java Web Server envia la request a el **Servlet Container**.
4. El Servlet Container busca el Servlet al que pertenece y lo activa con **Servlet.service()**
5. El Servlet una vez activado procesa la request y genera una response que es enviada de vuelta al navegador.

![alt text][logo2]
[logo2]:http://tutorials.jenkov.com/images/java-servlets/overview.png

### Servlet Containers
Es una parte de un servidor de web(o aplicaciones) que:
- Contiene y gestiona el ciclo de vida de los **Servlets**.
- Decodifica peticiones y formatea respuestas.
- Ofrece servicios de red sobre los cuales se envían peticiones y respuestas.
- Puede fijar restricciones de seguridad entorno los servlets que se ejecutan. (Ej: nº de threads máx.)

Todo servlet container debe soportar como mínimo **HTTP 1.0** aunque puede soportar opcionalmente protocolos adicionales.

Se ejecutan normalmente dentro de Java Web Servers como:
- Jetty
- TomCat

##2. Servlet Life Cycle
La ejecución de un servlet esta gestionada por el Servlet Container:
1. Load Servlet Class
2. Create Servlet Instance
3. Se llama al método **init()**
4. Se llama al método **service()**
5. Se llama al método **destroy()**

![alt text][logo3]
[logo3]:http://tutorials.jenkov.com/images/java-servlets/life-cycle.png

Los 3 primeros pasos solo se ejecutan una vez, ya que los servlets estan desactivados por defecto hasta reciben la primera request.
El 4 se hace por cada request recibida
El 5 cuando el servlet container cierra el servlet

## 3. Java Servlet Example

```java
public class SimpleServlet extends GenericServlet {

  public void service(ServletRequest request, ServletResponse response)
        throws ServletException, IOException {

        String yesOrNoParam = request.getParameter("param");

        if("yes".equals(yesOrNoParam) ){
          response.getWriter().write(
            "<html><body>You said yes!</body></html>");
        }

        if("no".equals(yesOrNoParam) ){
          response.getWriter().write(
            "<html><body>You said no!</body></html>");
        }
  }
}
```
El método **service()** lee el parámetro param enviado con el request y dependiendo de si es 'yes' o 'no' genera una response diferente.

##4. HttpServlet
La classe HttpServlet és un poco más avanzada, ya que a diferència de GenericServlet permite si el método con el que se envia la request es:
-	POST
-	GET
-	PUT
-	DELETE
-	HEAD

Por ejemplo, para generar una response a HTTP GET deberemos extender de la clase HttpServlet y hacer override del método **doGet**.

```JAVA
public class SimpleHttpServlet extends HttpServlet {
  protected void doGet( HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {

      response.getWriter().write("<html><body>GET response</body></html>");
  }
}
```

La clase HttpServlet tiene para cada método HTTP, métodos de los que podemos hacer override:
-	doGet()
-	doPost()
-	doHead()
-	doPut()
-	doDelete()
-	doOptions()
-	doTrace()

Lo más frecuente és responder a peticiones HTTP GET o POST, así que simplemente haremos override de estos dos.
Si queremos tratar peticiones GET o POST haremos override y haremos que una llame a la otra.

```java
public class SimpleHttpServlet extends HttpServlet {
  protected void doGet( HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {

      doPost(request, response);
  }

  protected void doPost( HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {

      response.getWriter().write("GET/POST response");
    }
}
```
##5. HttpRequest (Petición)
```java
protected void doGet(HttpServletRequest request, HttpServletResponse response)
```
HttpRequest es un objeto de java que representa la petición HTTP que nos llega des del navegador y nos llega a nuestra aplicación web.

###Parameters
Juntamente con la petición nos pueden llegar una serie de parámetros enviados normalmente como parte del URL(query string) o como parte del 'body' de la petición HTTP.

- HTTP POST request: los parámetros estan incluidos en el cuerpo de la petición HTTP.
- HTTP GET request: los parámetros están incluidos en la query string de la URL.

Ejemplo GET:

`http://jenkov.com/somePage.html?param1=hello&param2=world`

Donde la query string es:

`?param1=hello&param2=world`

Y contiene dos parámetros:
`param1=hello`
`param2=world`

A los que podemos acceder des del objeto HttpRequest:
```java
protected void doGet(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {

    String param1 = request.getParameter("param1");
    String param2 = request.getParameter("param2");

}
```
Usaremos el mismo código java en caso de que los parámetros nos lleguen por el 'body' de la petición HTTP. (POST)

Podemos usar métodos como:
`String getParameter(String name)`: retorna el valor del parámetro cuyo nombre es name.
`Map getParameterMap()`: retorna una tabla hash con todos los parámetros de la petición.
`Enumeration getParameterNames()`: retorna una enumeration de objetos String conteniendo los nombres de los parámetros de la petición.
`String[] getParameterValues(String name)`: retorna un array de Strings con los valores del parámetro name.

###Attributes
Los atributos permite la comunicación entre servlets que se pasen una misma petición o entre filtros y servlets.

`Object getAttribute(String name)`: retorna el valor de un atributo name.
`Enumeration getAttributeNames()`: retorna una Enumeration con los nombres en formato String de todos los atributos de la petición.
`void removeAttribute(String name)`: Elimina el atributo name de la petición.
`void setAttribute(String name, Object o)`: Fija el valor del atributo name al objeto o.

###Headers
Las cabeceras de la request son pares de valores enviados por el navegador juntamente con la petición HTTP, que contienen información relativa a el tipo de navegador, que tipo de ficheros se pueden recibir y metadatos de la petición.

Para acceder a esta información (ejemplo):
```java
String contentLength = request.getHeader("Content-Length");
```
Donde la cabecera Content-Length contiene el numero de bytes enviados en el cuerpo de la petición HTTP.

Otros métodos:
```java
String getHeader(String name)// retorna el valor de la primera cabecera de la petición con el nombre name.
Enumeration getHeaderNames()// retorna los nombres de todas las cabeceras de la petición.
Enumeration getHeaders(String name)// retorna todas las cabeceras de la petición con nombre name.

```

###InputStream
Cuando recibimos una petición HTTP por POST, la información recibida en el cuerpo no tiene por ser siempre parámetros, puede ser cualquier tipo de información como un fichero...
Para acceder al cuerpo de una petición HTTP por POST, podemos obtener un InputStream que apunta directamente al cuerpo HTTP de la petición:
```java
InputStream requestBodyInput = request.getInputStream();
```
En caso de que también necesitemos recibir parámetros, deberá ser siempre despues de hacer al método anterior.

###Session
También podemos obtener una sessión de la petición HTTP. El objeto **HttpSession** nos almacena información sobre un usuario. Si assignamos un objeto HttpSession durante una petición, para las subsiguientes peticiones que nos lleguen podremos leer información del usuario.
```java
HttpSession session = request.getSession();
```

###ServletContext
Contiene metadatos relacionados con la aplicación web. Por ejemplo los valores en el fichero web.xml
```java
ServletContext context = request.getSession().getServletContext();
```

##6. HttpResponse(Respuesta)
El objeto HttpResponse representa la respuesta que nuestra aplicación web devuelve al navegador, en respuesta de la pretición HTTP recibida préviamente.

###Writing HTML
Para enviar HTML de vuelta al navegador hemos de obtener el objeto PrintWriter del HttpResponse:
```java
PrintWriter writer = response.getWriter();
writer.write("<html><body>GET/POST response</body></html>");
```
###Headers
Al igual que los podíamos leer como en el HttpRequest podemos crearlos de la siguiente manera:
```java
response.setHeader("Header-Name", "Header Value");
```
Aunque siempre debemos hacerlo antes de escribir datos en la respuesta.

###Content-Type
Es un header de respuesta que informa al navegador que tipo de contenido se esta enviando de vuelta. Por ejemplo si es HTML será text/html. Si enviamos texto plano será text/plain. Así es como lo indicamos:
```java
response.setHeader("Content-Type", "text/html");
```

###Writing text
Podemos pues enviar texto plano en vez de html de la siguiente forma:
```java
response.setHeader("Content-Type", "text/plain");

PrintWriter writer = response.getWriter();
writer.write("This is just plain text");
```

###Content-Length
En caso de que queramos enviar datos en binario necesitaremos informar al navegador de cuantos datos (bytes). Para ello lo haremos añadiendo la siguiente cabecera:
```java
response.setHeader("Content-Length", "31642");
```

###Writing Binary Data
También podemos enviar datos binarios de vuelta al navegador, por ejemplo una imagen, un PDF, un archivo FLASH ... Como hemos visto anteriormente deberemos definir el tipo de contenido que enviamos en la cabecera **Content-Type**.

Por parte de el envío en vez de usar el **PrintWriter** que hemos visto anteriormente, usaremos el objeto **OutputStream** que se obtiene de la siguiente manera:
```java
OutputStream outputStream = response.getOutputStream();
outputStream.write(...);
```

###Redirecting to a Different URL
También podemos redirigir a otra URL desde nuestro Servlet, aunque en este caso no podremos enviar datos de vuelta. Para hacerlo usamos el siguiente código:
```java
response.sendRedirect("http://jenkov.com");
```
Métodos:
```java

```
##7. HttpSession
El objeto HttpSession representa la sesión de un usuario. Esta sesión contiene información de este a lo largo de sus múltiples peticiones HTTP.
Cuando un usuario entra por primera vez en nuestra aplicación web, se le da un identificador único de sesión. Esta ID normalmente se guarda como Cookie o un parametro de request.
Así es como accedemos al objeto de sesion:
```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    HttpSession session = request.getSession();
}
```
También podemos associar valores al objeto de sesión:
```java
session.setAttribute("userName", "theUserName");
```
Y recuperarlos posteriormente:
```java
String userName = (String) session.getAttribute("userName");
```
Las sesiones existen a nivel de aplicación web, y se comparten entre los diferentes servlets de una misma aplicación.

Métodos:
```java
//OBTENER SESIÓN
httpSession getSession()// retorna la sesión asociada con la petición. Si no existe, crea una.
httpSession getSession(boolean create)// retorna la sesión asociada con la petición. Si no existe, y create es cierto, crea una.
//ATRIBUTOS DE SESIÓN
Object getAttribute(String name)// retorna el valor del atributo de nombre name.
Enumeration getAttributeNames()// retorna una enumeración con los nombres de todos los atributos.
void setAttribute(String name, Object value)// fija el valor del atributo de nombre name asignandole value.
void removeAttribute(String name)// elimina el atributo de nombre name.
//FINALIZAR SESIÓN
void setMaxInactiveInterval(int interval)// especifica el tiempo en segundos entre peticiones de un mismo cliente antes de que el contenedor de servlets invalide la sesión.
```

##8. RequestDispatcher
Es una clase que permite llamar a otros servlets o jsp. Para obtenerlo:
```java
protected void doPost(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {

  RequestDispatcher requestDispatcher = request.getRequestDispatcher("/anotherURL.simple");
}
```
El código de arriba obtiene el objeto RequestDispatcher mapeado en la URL anotherURL.simple

Llamando al método forward o include el servlet container activa el servlet mapeado a la url del RequestDispatcher
```java
requestDispatcher.forward(request, response);
```
```java
requestDispatcher.include(request, response)
```

##9. ServletContext
Es un objeto que contiene metadatos sobre nuestra aplicación web. Y se puede acceder a él mediante el objeto HttpRequest:
```java
ServletContext context = request.getSession().getServletContext();
```

### Context Attributes
Justo como los objetos de sesión podemos almacenar atributos en el objeto ServletContext:
```java
context.setAttribute("someValue", "aValue");
```
Y acceder a ellos con:
```java
Object attribute = context.getAttribute("someValue");
```
Los atributos guardados en el ServletContext serán accesibles en todos los servlets de la aplicación y entre todas las peticiones y sesiones.

##10. web.xml configuration
Para que un servlet pueda ser accesible desde el navegador, hemos de decir al servlet container que servlets desplegar y que URLs irán associadas a estos servlets(mapping).

Esto se hace en el fichero web.xml de nuestra aplicación web.

###Configuring and Mapping a Servlet
Para configurar un servlet en el fichero web.xml escribimos esto:
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

<!DOCTYPE web-app
    PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
    "http://java.sun.com/dtd/web-app_2_3.dtd">

<web-app>
  <servlet>
    <servlet-name>controlServlet</servlet-name>
    <servlet-class>com.jenkov.butterfly.ControlServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>controlServlet</servlet-name>
    <url-pattern>*.html</url-pattern>
  </servlet-mapping>
</web-app>
```
Primer configuramos el servlet usando el tag <**servlet**>, aquí escribimos su nombre
y la clase a la que pertenece.
En segundo lugar, mapeamos todas las URL acabadas en .html a nuestro servlet.

###Servlet init parameters
Podemos pasar parámetros al servlet des de el fichero web.xml. Estos parametros solo seran accesibles por ese servlet.
```xml
<servlet>
    <servlet-name>controlServlet</servlet-name>
    <servlet-class>com.jenkov.butterfly.ControlServlet</servlet-class>

        <init-param>
        <param-name>myParam</param-name>
        <param-value>paramValue</param-value>
        </init-param>
</servlet>
```
Y así es como los leeríamos en nuestro servlet
```java
public void init(ServletConfig servletConfig) throws ServletException{
    this.myParam = servletConfig.getInitParameter("myParam");
}
```

###Server Load-On-StartUp
El tag <**servlet**> tiene un sub-elemento llamado <**load-on-startup**> que se puede usar para inidicar al servlet container cuando queremos que se cargue nuestro servlet. Cuanto menor sea el numero más rápido lo cargará, si es negativo lo cargará cuando se haga una request por primera vez.
```xml
<servlet>
    <servlet-name>controlServlet</servlet-name>
    <servlet-class>com.jenkov.webui.ControlServlet</servlet-class>
    <init-param><param-name>container.script.static</param-name>
                <param-value>/WEB-INF/container.script</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
```

###Context Parameters
También podemos definir algunos parámetros de contexto que pueden ser leídos por todos los servlets de nuestra aplicación web.
```xml
<context-param>
    <param-name>myParam</param-name>
    <param-value>the value</param-value>
</context-param>
```
Y así es como accederíamos desde nuestro servlet:
```java
String myContextParam = request.getSession().getServletContext().getInitParameter("myParam");
```



##13. Cookies
Las **cookies** HTTP són pequeñas piezas de datos(hasta 4kB) que la aplicación web puede almacenar en la máquina cliente de los usuarios que visitan nuestra aplicación.

###Java Cookie Example
Podemos escribir cookies usando el objeto **HttpResponse**:
```java
Cookie cookie = new Cookie("myCookie", "myCookieValue");
response.addCookie(cookie);
```
Como podemos ver hemos creado una cookie con el identificador *myCookie* y con el valor *myCookieValue*. Podemos crear tantas cookies como queramos, parecido a una *Hashtable*.
Cuando el navegador accede a nuestra aplicación web, este le entrega las cookies almacenadas en la máquina cliente a la aplicación web. Pero únicamente las cookies pertenecientes a esa aplicación.

###Reading Cookies Sent From the Browser
Podemos leer las cookies mediante el objeto **HttpRequest** y luego iterando:
```java
Cookie[] cookies = request.getCookies();
String userId = null;
for(Cookie cookie : cookies){
    if("uid".equals(cookie.getName())){
        userId = cookie.getValue();
    }
}
```
También podemos hacer uso de una **hashmap**:
```java
Map cookieMap = new HashMap();
Cookie[] cookies = request.getCookies();

for(Cookie cookie : cookies){
    cookieMap.put(cookie.getName(), cookie);
}
```
Y luego podriamos acceder mediante **cookieMap.get("cookieName")**

###Cookie Expiration
Una opción importante és el tiempo de caducidad de la cookie. Este tiempo le dice al navegador hasta cuando ha de mantener la cookie. Esto se hace mediante el método **setMaxAge()**:
```java
Cookie cookie = new Cookie("uid", "123");
cookie.setMaxAge(24 * 60 * 60);  // 24 hours.
response.addCookie(cookie);
```

###Removing Cookies
A veces es necesario eliminar una cookie. Para hacerlo solo tenemos que configurar el tiempo de expiración a 0 si queremos eliminarla de immediato, o bien a -1 si queremos que se elimine en el momento que el cliente cierre el navegador.
```java
Cookie cookie = new Cookie("uid", "");
cookie.setMaxAge(0);
response.addCookie(cookie);
```

###Cookie Cases
Las cookies normalmente son usadas para guardar información específica que no queremos que ocupen espacio en la parte del servidor, como puede ser una sesión anónima ...

##14. Servlet Concurrency
Un **Servlet Container/WebServer** és por defecto multithread. Esto significa que múltiples requests al mismo servlet podrian ser ejecutadas al mismo tiempo. Así pues deberemos tenerlo en cuenta en el momento de implementar nuestro servlet.

Para asegurarnos que nuestro servlet es **thread-safe** debemos seguir estas reglas:
1. En el **service()**, no acceder a variables de clase a no ser que sean **thread-safe**.
2. En el **service()**, no reassignar variables de clase. En caso de que lo necesitemos hacerlo dentro de un bloque **synchronize**.
3. Aplicar la regla **1** y **2** para las variables **estáticas**.
4. Las variables locales siempre son thread-safe. Aunque quiza el objecto al que apunta una variable local puede que no lo sea. Si un objeto es instanciado dentro del método, y nunca escapa entonces no habrá problema. Por otro lado si una variable local apunta a un objeto compartido podría seguir causando problemas. El hecho de assignar un objeto compartido a una variable local no hace convierte el objeto en **thread-safe**.

Los objetos **request** y **response** so	n **thread-safe** ya que se crea una nueva instancia de estos por cada petición o thread ejecutado en nuestro servlet.

En la siguiente imagen se muestra en una caja roja, las variables que tendremos que tener cuidado al trabajar con ellas.

![alt text][logo4]
[logo4]:http://tutorials.jenkov.com/images/java-servlets/servlet-concurrency.png

##Other Shared Resources
Por supuesto no solo las variables **estáticas** y de **clase** dentro de la clase servlet hemos de tener en cuenta. Sino que también las variables estáticas y de clase de otras clases deben ser **thread-safe**.

##Code Example
```java
public class SimpleHttpServlet extends HttpServlet {
  // Not thread safe, static.
  protected static List list = new ArrayList();
  // Not thread safe
  protected Map map = new HashMap();
  // Thread safe to access object, not thread safe to reassign variable.
  protected Map map = new ConcurrentHashMap();
  // Thread safe to access object (immutable), not thread safe to reassign variable.
  protected String aString = "a string value";

  protected void doGet( HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
    // Not thread safe, unless the singleton is 100% thread safe.
    SomeClass.getSomeStaticSingleton();
    // Thread safe, locally instantiated, and never escapes method.
    Set set = new HashSet();
  }
}
```

##15.Web Applications
Una **aplicación web** és una colección de servlets, páginas HTML, clases y otros recursos que comprenden una aplicación completa dentro de un servidor web.
Una aplicación web tiene una ruta específica dentro de un servidor web. Ejemplo:
>http://www.miempresa.com/catalog

##Elements
- Servlets.
- Páginas JSP.
- Clases Java de utilidad.
- Documentos estáticos (html, imagenes, sonidos, etc).
- Applets, beans y clases de la parte de la aplicación.
- Información descriptiva que enlaza los elementos anteriores.

##Folder Structure
- Una **aplicación web** existe como una estructura jerárquica de directorios.
- Una aplicación enraizada en el servidor en **/catalog**, el fichero index.html que se encuentre en la raiz del sistema de directorios de la aplicación web será retornado cuando el usuario solicite **/catalog/index.html**.
- Existe un directorio especial dentro de la jerarquía llamado **WEB-INF**. Este directorio no es visible desde el exterior, pero si para los servlets de la aplicación usando **getResource**. Los contenidos de WEB-INF son:
	- **/WEB-INF/web.xml**: és el descriptor de despliegue. Explica al servlet container lo que contiene la aplicación.
	- **/WEB-INF/classes/**: és el directorio donde se encuentran los .class de los servlets y el resto de clases Java que utilize la aplicación.
	- **/WEB-INF/lib/*.jar**: és el área predefinida para los archivos Java Archive.
- Las aplicaciones web se empaquetan en archivos **.war** (web archive).
Ejemplo:
>/index.html
>/howto.jsp
>/images/banner.gif
>/WEB-INF/web.xml
>/WEB-INF/lib/jspbean.jar
>/WEB-INF/classes/com/miempresa/servlets/MiServlet.class
>/WEB-INF/classes/com/miempresa/util/MiClaseUtilidad.class

##Deploy Descriptor (web.xml)
Incluye información relativa a:
- Datos generales de la aplicación web.
- Parámetros de inicialización de la aplicación web.
- Configuración de la gesyión de sesiones (timeout).
- Declaración de servlets.
- Mapeos de servlets.
- ...

És el fichero web.xml que hemos explicado en el apartado 10.

##Servlet Mapping. Matching.
Tipos de matching:
- Matching de prefijos
- Matching exacto
- Matching de extensión
- Matching para definir un servlet por defecto

Algoritmo de mapeo:
1. Si existe un matching exacto entre el path solicitado y el servlet, se envia la petición al servlet.
2. Se intenta encontrar de forma recursiva, el mayor prefijo que haga matching y se envia la petición al servlet.
3. Si el último segmento del URL contiene una extensión, se busca un servlet que gestione esa extensión.
4. Si se ha definido, se envía la petición al servlet por defecto.






