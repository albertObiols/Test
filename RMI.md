#RMI
##1. Paradigma
- Dona abstracció sobre els sistemes **Client-Servidor**.
- Basat en **Programació Orientada a Objectes**.
- Orientat a funcionalitats i no a l'intercanvi d'informació.
- Simplificació Protocol i gestió de trames.
- Simplificació de tractament d'errors

##2. Conceptes
- **Objecte Remot:** objecte que té algun métode el qual podem invocar des d'un altre procés corrent en una màquina remota.
- **Objecte Local:** objecte que només pot ser invocat per un procés local.

##3. Esquema conceptual
Per sol·licitar un servei d'un recurs de la xarxa, un procés invoca algun dels seus métodes. Aquest métode s'executarà a la màquina remota i la resposta s'enviará de nou al procés que l'ha sol·licitat.
[insertar imatge pagina 4]

##4. Framework Genèric d'Objects Distribuïts
- Un objecte distribuït és proporcionat o exportat per un **Servidor d'objectes**.
- Per tal de que l'objecte remot pugui ser distribuït, s'ha de **registrar**.
- Per accedir a un objecte remot, un procés busca una referència remota en el registre de l'objecte que es vol fer servir. (Aquesta referència es utilitzada per fer les crides als seus métodes).
[insertar imatge pagina 5]

##5. Frameworks d'objectes distribuïts
- Java Remote Method Invocation (RMI).
- Common Object Request Broker Architecture (CORBA).
- Distributed Component Object Model (DCOM).
- Mecanismes que ofereixin el Simple Object Acces Protocol (SOAP).

##6. Java RMI
- L'objectiu és que els programadors desenvolupin aplicacions distribuïdes amb Java usant la mateixa sintaxi i semàntica que les aplicacions Java tradicionals.
- L'aplicació distribuïda s'executarà en vàries màquines virtuals simultàniament.
- L'arquitectura RMI defineix:
	- com es comporten els objectes.
	- quan i com ocorren les excepcions.
	- com es gestiona la memòria.
	- com es passen els paràmetres a mètodes remots i com es reben els resultats.

##6.1 En quins aspectes ens ajuda Java RMI
En la majoria d'aplicacions apareixen 5 aspectes a codificar:
1. Lògica de l'aplicació.
2. Interfície d'usuari.
3. Preparació de dades per a l'enviament a altres processos.
4. Codi que llança l'aplicació.
5. Codi que intenta fer una aplicació distribuïda més robusta i escalable: caches en client, serveis de noms, balanceig de càrrega...

RMI ens ajuda en la generació automàtica de la part 3 i 4.

##6.2 Conceptes fonamentals RMI
- **Objecte Remot:** objecte que té algun métode el qual podem invocar des d'un altre procés corrent en una màquina remota.
- **Objecte Local:** objecte que només pot ser invocat per un procés local.
- **Servidor d'objectes: ** la màquina virtual té instanciats els objectes remots.
- **Interfície Remota: ** interfície que declara els métodes d'un objecte remot.
- **Invocació de métode remot: ** acció d'invocar un métode d'una interfície en un objecte remot.
- **RMIregistry: ** servidor de noms o registre d'objectes remots. Permet localitzar objectes remots a partir del seu nom.

#7. Utilitzant objectes remots
- Per utilitzar un objecte remot, hem de saber com:
	- Obtenir una referència remota.
	- Crear un objecte remot i deixar-lo accessible.
	- Invocar un mètode remot.

##7.1 Com obtenir una referència remota
- Quan una màquina virtual s'inicia no té cap referència a l'abast. Per poder comunicar-se a l'exterior ha de buscar la primera referència remota utilitzant un servei de directori per localitzar un objecte remot a partir del seu nom. Un cop haguem localitzat el primer objecte remot és possible que aquest ens envïi noves referències remotes com a valors de retorn d'algun dels seus métodes remots(callback).

##7.2 El servei de directori: rmiregistry
- L'API de RMI ens permet fer servir diferents serveis de directori per registrar un objecte distribuït. Particularment utilitzarem un servei de directori anomenat rmiregistry que es proporciona amb el SDK de Java.
- **RMIregistry** és un servidor que s'ha d'executar a la màquina servidora d'objectes, per defecte al port 1099.
> /$rmiregistry
- S'ha d'engegar en el mateix path que el servidor d'objectes, o l'intent d'utilitzar-lo fracasarà.
- El registre estarà actiu fins que es mati el procés.

##7.3 Accés al registre de noms des d'un programa Java

- Tambè podem engegar-lo de forma dinàmica dintre de la classe servidora d'objectes:

>import java.rmi.registry.LocateRegistry;
LocateRegistry.createRegistry(1099);

##7.4 Métodes de rmi.Naming
- El servei de noms ens dona fonamentalment un directori, de tipus guia de telefons amb les següents funcions:
	- **bind:** afegir i associar un nou objecte remot a un nom.
	- **unbind:** eliminar un objecte remot existent.
	- **rebind:** modificar l'associació d'un nom a un altre objecte remot.
	- **lookup:** cercar un objecte remot per nom.
	- **list:** obtenir totes les associacions.

##7.5 API del RMI JAVA

- Un cop ja tenim solucionat com obtenir referències remotes, hem de saber com:
	1. crear objectes remots i deixar-los accessibles.
	2. invocar un mètode remot.

- Necessitarem:
	- Interfície remota tant al costat del client com del servidor.
	- L'aplicació del costat del servidor:
		- Implementació de l'interfície remota.
        - Instància de l'objecte remot i el deixa disponible al servei de directori.
	- L'aplicació del costat del client, invoca métodes remots.

##7.6 La interfície remota
- Una interfície remota és una interfície que hereta de la interfície marcador **Remote** de JAVA(no implementa cap mètode) i que especifica quins mètodes remots oferirà.
- L'ús d'interfícies facilita la crida de mètodes d'objectes remots, ja que el client sols necessita l'interfície remota i no pas tota la implementació.

- Tot mètode pot llençar una excepció **java.rmi.RemoteException** ja que pot haver-hi causes com:
	- Error en les comunicacions.
	- Error al empaquetar / Desempaquetar paràmetres.
	- Error de protocol.

##7.7 Exemple Interfície remota
```java
package es.ub.gei.sd.dateserver;
import java.rmi.Remote;
import java.rmi.RemoteException;
import java.util.Date;

public interface DateServer extends Remote {
	public Date getDate() throws RemoteException;
}
```

##7.8 Aplicació del costat del servidor
- Un servidor d'objecte distribuït és un objecte que proporciona els mètodes i la interfície d'un objecte remot. Cada servidor ha de:
	- Implementar cadascún dels mètodes remots especificats a l'interfície.
	- Registrar l'objecte que conté la implementació dins del servei de directori.


##7.9 Implementació Interfície remota
```java
package es.ub.gei.sd.dateserver;
import java.rmi.RemoteException;
import java.rmi.server.UnicastRemoteObject;
import java.util.Date;

public class DateServerImpl extends UnicastRemoteObject implements DateServer {
	public DateServerImpl throws RemoteException{
    	public Date getDate() throws RemoteException {
        	return new Date();
        }
    }
}
```

##7.10 Servidor d'objecte remot
```java
public class Server{
	public static void main(String [] args) throw Exception {
    	int portNum = 1099;
        DareServerImpl dateServer = new DateServerImpl();
        startRegistry(portNum);
        String registryUrl = "rmi://localhost" + portNum + "/DateServer";
        Naming.rebind(registryURL, dateServer);
        System.out.println("Some server ready!");
    }
}
```
## Stubs & Skeletons
Recull les crides locals de mètodes remots i gestiona el seu enviament a l'objecte remot. Gestiona també la recepció per part de l'objecte remot de la crida al mètode i retorn de l'informació.

### Stub
Un Stub d'un objecte remot actua com un representant local a la part del client de client l'objecte remot. L'stub recull la petició localment i l'envia a l'objecte remot. Quan s'invoca un mètode en un stub aquest:
1. Inicia una connexió amb la JVM remota que conté l'objecte remot.
2. Empaqueta i transmet els paràmetres a la JVM remota.
3. Espera el resultat de la invocació del mètode.
4. LLegeix i desempaqueta el valor de retorn o l'excepció retornada.
5. Retorna el valor a l'objecte local que va fer l'invocació.

### Skeleton
A la JVM remota, cada objecte remot té un **skeleton** associat. L'Skeleton és el responsable d'enviar la crida rebuda des del client a l'implementació de l'objecte remot. Quan l'Skeleton rep l'entrada d'una invocació d'un mètode:
1. Pot crear un nou thread per atendre la petició.
2. Llegeix i desempaqueta els paràmetres.
3. Invoca el mètode en l'objecte local.
4. Empaqueta i transmet el resultat de la invocació.

### Generació de Stubs i Skeletons
Així doncs, stubs i skeletons són classes de java que inclouen codi que:
- empaqueta i desempaqueta paràmetres.
- obre i tanca sockets.
- realitza operacións d'E/S...

Històricament es generaven usant l'executable rmic sobre el class de la classe remota.
A partir de la versió 1.2 de java els Skeletons es generen automàticament en exportar els objectes.
A partir de la versió 1.5 de java els Stubs no cal generar-los ni distribuir-los:
- En el servidor: en instanciar-se un objecte UnicastRemoteObject una instància de java.lang.Proxy és construïda, que implementa totes les interfícies remotes de l'objecte remot i té com a controlador d'invocacions java.rmi.server.RemoteObjectInvocationHandler.java.
- En el client: a partir de l'invocació d'un mètode remot i l'instància de java.lang.reflect.Proxy la funcionalitat d'invocar el métode remot de l'stub es fa de forma automàtica.

###Capa de Referències Remotes
![alt text][logo]
[logo]: http://www-01.ibm.com/support/knowledgecenter/api/content/nl/es/SSYKE2_7.0.0/com.ibm.java.win.70.doc/diag/understanding/rmi_implementations.gif
Defineix les semàntiques d'invocació d'un servei remot. Aquesta capa proporciona un objecte de tipus **RemoteRef** que facilita l'implementació del stubs i els skeletons, que farán ús de les referències remotes.

###Mètodes de java.rmi.server.RemoteRef
- Permet invocar un mètode en un objecte remot:
```java
Object invoke(Remote obj, Method method, Object[] params, long opnum)
```
- Compara dos objectes remots per veure si son iguals:
```java
boolean remoteEquals(RemoteRef obj)
```
- Retorna un codi hash per un objecte remot.
```java
int remoteHashCode()
```
- Retorna un string que representa l'objecte remot.
```java
String remoteToString()
```

###Garbage Collector Distribuït
L'RMI implementa un garbage collector per tal de:
1. Assegurar si existeix una referència local o remota d'un objecte distribuït, aquest continuará existint.
2. Tan bon punt no quedi cap referència remota o local sobre l'objecte remot, serà completament esborrat de la memòria del servidor.

El GCD està basat en un comptador de referències igual que el GC normal de JAVA, però les referències en els clients han de ser reportades al servidor de l'objecte mitjançant crides remotes. El GCD i GC treballen en cooperació.

###Distribució automàtica de fitxers
Com hem vist RMI no permet enviar els .class sinò unicament els objectes. Només viatgen les dades. Però els clients i els servidors han de compartir els .class d'objectes que intercanvien com a paràmetres o valors de retorn.

- **Problema:** si en un determinat moment el servidor crea una certa subclasse d'una classe de retorn, el client l'haurà d'incloure tambè.
- RMI ofereix la possibilitat que les classes es descarreguin d'un o diversos repositoris de codi. És habitual que hi hagi un únic repositori de codi gestionat per qui gestioni el servidor.

Quan el client o el servidor troben una classe que no coneixen l'intenten carregar:
1. del CLASSPATH
2. o d'una URL, si la referència remota du internament una propietat amb l'URL d'on es troben els .class necessaris.

Per motius de seguretat per a que es realitzi aquesta descàrrega de codi s'haurà d'instal·lar un SecurityManager, en cas contrari saltarà un SecurityException.
- Client: `java -Djava.security.policy=java.policy SomeClient`
- Server: `java -Djava.rmi.codebase=<URL>-D-Djava.security.policy=java.policy Server`

###Fitxer java.policy
```java
grant{
permission java.net.SocketPermission "*:1024-65535", "connect, accept, resolve";
permission java.net.SocketPermission "*:80", "connect";
};

###Objectes Activables

```




