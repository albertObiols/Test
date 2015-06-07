#RMI

##1. Objectes Distribuits
###1.1 Paradigma
- Dona abstracció sobre els sistemes **Client-Servidor**.
- Basat en **Programació Orientada a Objectes**.
- Orientat a funcionalitats i no a l'intercanvi d'informació.
- Simplificació Protocol i gestió de trames.
- Simplificació de tractament d'errors

###1.2 Conceptes
- **Objecte Remot:** objecte que té algun métode el qual podem invocar des d'un altre procés corrent en una màquina remota.
- **Objecte Local:** objecte que només pot ser invocat per un procés local.

###1.3 Esquema conceptual
Per sol·licitar un servei d'un recurs de la xarxa, un procés invoca algun dels seus métodes. Aquest métode s'executarà a la màquina remota i la resposta s'enviará de nou al procés que l'ha sol·licitat.
[insertar imatge pagina 4]

###1.4 Framework Genèric d'Objects Distribuïts
- Un objecte distribuït és proporcionat o exportat per un **Servidor d'objectes**.
- Per tal de que l'objecte remot pugui ser distribuït, s'ha de **registrar**.
- Per accedir a un objecte remot, un procés busca una referència remota en el registre de l'objecte que es vol fer servir. (Aquesta referència es utilitzada per fer les crides als seus métodes).
[insertar imatge pagina 5]

###1.5 Frameworks d'objectes distribuïts
- Java Remote Method Invocation (RMI).
- Common Object Request Broker Architecture (CORBA).
- Distributed Component Object Model (DCOM).
- Mecanismes que ofereixin el Simple Object Acces Protocol (SOAP).

##2. Java RMI
- L'objectiu és que els programadors desenvolupin aplicacions distribuïdes amb Java usant la mateixa sintaxi i semàntica que les aplicacions Java tradicionals.
- L'aplicació distribuïda s'executarà en vàries màquines virtuals simultàniament.
- L'arquitectura RMI defineix:
	- com es comporten els objectes.
	- quan i com ocorren les excepcions.
	- com es gestiona la memòria.
	- com es passen els paràmetres a mètodes remots i com es reben els resultats.

###2.1 En quins aspectes ens ajuda Java RMI
En la majoria d'aplicacions apareixen 5 aspectes a codificar:

1. Lògica de l'aplicació.
2. Interfície d'usuari.
3. Preparació de dades per a l'enviament a altres processos.
4. Codi que llança l'aplicació.
5. Codi que intenta fer una aplicació distribuïda més robusta i escalable: caches en client, serveis de noms, balanceig de càrrega...

RMI ens ajuda en la generació automàtica de la part 3 i 4.

###2.2 Conceptes fonamentals RMI
- **Objecte Remot:** objecte que té algun métode el qual podem invocar des d'un altre procés corrent en una màquina remota.
- **Objecte Local:** objecte que només pot ser invocat per un procés local.
- **Servidor d'objectes:** la màquina virtual té instanciats els objectes remots.
- **Interfície Remota:** interfície que declara els métodes d'un objecte remot.
- **Invocació de métode remot:** acció d'invocar un métode d'una interfície en un objecte remot.
- **RMIregistry:** servidor de noms o registre d'objectes remots. Permet localitzar objectes remots a partir del seu nom.

##3. Utilitzant objectes remots
- Per utilitzar un objecte remot, hem de saber com:
	- Obtenir una referència remota.
	- Crear un objecte remot i deixar-lo accessible.
	- Invocar un mètode remot.

###3.1 Com obtenir una referència remota
- Quan una màquina virtual s'inicia no té cap referència a l'abast. Per poder comunicar-se a l'exterior ha de buscar la primera referència remota utilitzant un servei de directori per localitzar un objecte remot a partir del seu nom. Un cop haguem localitzat el primer objecte remot és possible que aquest ens envïi noves referències remotes com a valors de retorn d'algun dels seus métodes remots(callback).

###3.2 El servei de directori: rmiregistry
- L'API de RMI ens permet fer servir diferents serveis de directori per registrar un objecte distribuït. Particularment utilitzarem un servei de directori anomenat rmiregistry que es proporciona amb el SDK de Java.
- **RMIregistry** és un servidor que s'ha d'executar a la màquina servidora d'objectes, per defecte al port 1099.
> /$rmiregistry
- S'ha d'engegar en el mateix path que el servidor d'objectes, o l'intent d'utilitzar-lo fracasarà.
- El registre estarà actiu fins que es mati el procés.

###3.3 Accés al registre de noms des d'un programa Java

- Tambè podem engegar-lo de forma dinàmica dintre de la classe servidora d'objectes:

>import java.rmi.registry.LocateRegistry;
LocateRegistry.createRegistry(1099);

###3.4 Métodes de rmi.Naming
- El servei de noms ens dona fonamentalment un directori, de tipus guia de telefons amb les següents funcions:
	- **bind:** afegir i associar un nou objecte remot a un nom.
	- **unbind:** eliminar un objecte remot existent.
	- **rebind:** modificar l'associació d'un nom a un altre objecte remot.
	- **lookup:** cercar un objecte remot per nom.
	- **list:** obtenir totes les associacions.

###3.5 API del RMI JAVA

- Un cop ja tenim solucionat com obtenir referències remotes, hem de saber com:
	1. crear objectes remots i deixar-los accessibles.
	2. invocar un mètode remot.

- Necessitarem:
	- Interfície remota tant al costat del client com del servidor.
	- L'aplicació del costat del servidor:
		- Implementació de l'interfície remota.
        - Instància de l'objecte remot i el deixa disponible al servei de directori.
	- L'aplicació del costat del client, invoca métodes remots.

###3.6 La interfície remota
- Una interfície remota és una interfície que hereta de la interfície marcador **Remote** de JAVA(no implementa cap mètode) i que especifica quins mètodes remots oferirà.
- L'ús d'interfícies facilita la crida de mètodes d'objectes remots, ja que el client sols necessita l'interfície remota i no pas tota la implementació.

- Tot mètode pot llençar una excepció **java.rmi.RemoteException** ja que pot haver-hi causes com:
	- Error en les comunicacions.
	- Error al empaquetar / Desempaquetar paràmetres.
	- Error de protocol.

###3.7 Exemple Interfície remota
```java
package es.ub.gei.sd.dateserver;
import java.rmi.Remote;
import java.rmi.RemoteException;
import java.util.Date;

public interface DateServer extends Remote {
	public Date getDate() throws RemoteException;
}
```

###3.8 Aplicació del costat del servidor
- Un servidor d'objecte distribuït és un objecte que proporciona els mètodes i la interfície d'un objecte remot. Cada servidor ha de:
	- Implementar cadascún dels mètodes remots especificats a l'interfície.
	- Registrar l'objecte que conté la implementació dins del servei de directori.


###3.9 Implementació Interfície remota
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

###3.10 Servidor d'objecte remot
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
- Quan un servidor d'objectes s'executa, l'exportació d'objecte distribuït fa que el procés del servidor comenci a escoltar i esperi que els clients es connectin i sol·licitin el servei d'objecte.
- Un servidor d'objectes RMI és un servidor concurrent: cada sol·licitud d'un client a un dels métodes es pot fer en un fil separat. S'ha de tenir en compte que si un client crida a diversos métodes, aquestes crides podrien executar-se simultàniament. Igualment en el servidor cal tenir en compte que diferents clients poden estar executant el mateix servei i caldrà posar sistemes de sincronització allà on calgui.


###3.11 Aplicació del costat del client
L'aplicació client és com qualsevol altra classe de Java. La sintaxi RMI necessària fa referència a:
- Localitzar el registre RMI del servidor.
- Buscar la referència remota de l'objecte remot.
- Convertir a la classe d'interfície remota i invocar els seus métodes remots.

```java
public class DateClient {
	public static void main(Strinng [] args) {
    	if(args.length != 1) {
        	throw new IlleglArgumentException("Syntax: DateClient <hostname:port>");
        }
        DateServer dateServer = () Naming.lookup("rmi://" + args[0] + "/DateServer");
        Date when = dateServer.getDate();
        System.out.println(when);
    }
}
```
- La referència de la interfície remota s'ha d'utilitzar per invocar qualsevol dels métodes remots.
- La sintaxi d'invocació dels métodes remots és la mateixa que en els locals.
- **ALERTA!** és un error comú convertir la referència obtinguda des del registre de la classe a la classe d'implementació de la interfície en comptes de la classe d'interfície.

###3.12 Passos per crear una aplicació RMI client/servidor
**En l'aplicació del client**
- Interfícies d'Objectes Remots.
- Codi Aplicació Client.
	- Main.
	- GUI.
	- Crides a métodes remots.

**En l'aplicació del servidor**
- Interfícies d'Objectes Remots.
- Implementacions d'Objectes Remots.
- Codi Aplicació Servidor.
	- Main.
	- Instanciació d'objectes Remots.
	- *(Engegar RMIRegistry des de l'aplicació)
	- Publicitar Objectes Remots al servidor de directori.

###3.13 Passos per arrencar una aplicació RMI client/servidor
**En la màquina servidor.**
- *(Engegar rmiregistry).
- Executar l'aplicació del servidor.

**En les màquines clients.**
- Executar l'aplicació del client.

**ALERTA!** Cal vigilar que el rmiregistry estigui executant-se en el mateix path que el servidor, en cas contrari cal tenir-ho en compte quan especifiqueu la URL rmi://...

**ALERT!** Les interfícies dels objectes remots a client i a servidor han de ser exactament iguals, vigileu si feu servir packages.

###3.14 Diferències entre invocació local i remota.
- El clients d'objectes remots sempre interactuen  amb interfícies remotes, mai amb les classes d'implementació.
- Els arguments no-remots, i els resultats retornats són passats per còpia enlloc de per referència. Es necessari que implementin **Serializable**.
- Els objectes remots es pasen sempre per referència, no copiant les implementacions remotes.
- El significat d'alguns métodes definits en java.lang.Object s'especialitzen en el cas d'objectes remots.
- Una invocació remota pot fallar per raons adicionals a les que ho fa una invocació local. S'han de tractar excepcions adicionals.

- **Invocació local**
```java
public class Cliente {}
public class IdentificadorCliente {}
public Cliente obtenerCliente(IdentificadorCliente id);
```
*S'envia una referència local i es rep una referència local*
- **Invocació remota amb paràmetre local i retorn remot**
```java
public Interficie ICliente extends Remote {}
public class Cliente implements ICliente {}
public class IdentificadorCliente implements Serializable {}
public ICliente obtenerCliente (IIdentificadorCliente id) throws RemoteException;
```
*S'envia una còpia de l'objecte **id** i es rep una referència remota.*

- **Invocació remota amb paràmetre remot i retorn remot**
```java
public Interficie ICliente extends Remote {}
public Interficie IIdentificadorCliente extends Remote {}
public class Cliente implements ICliente {}
public class IdentificadorCliente implements IIdentificadorCliente {}
public ICliente obtenerCliente (IdentificadorCliente id) throws RemoteException;
```
*S'envia una referència remota **id** i es rep una referència remota d'**idCliente**.*


##4. RMI Avançat
###4.1 Més enllà del C/S
En el model Client-Servidor, el servidor és passiu: el client inicia l'IPC, el servidor espera l'arribada de les sol·licituds i proporciona respostes. Però algunes aplicacions requereixen que el servidor iniciï la comunicació en determinades incidències **Callback**. Exemples:
- Monitorització.
- Jocs.
- Subhastes.
- Votacions/Enquestes.
- Sales de xat.
- Tauler d'anuncis/Servei de missatges.
- Treball en grup.

###4.2 Polling vs Callback.
Si no hi ha callback, en cas de que el client hagi de ser notificat sobre un esdeveniment asíncron que ha ocorregut en el costat del servidor, el client l'hauria de sondejar (*polling*) diverses vegades.

###4.3 Comunicació ambdos sentits
Algunes aplicacions requereixen que les dues parts puguin iniciar l'IPC. Utilitzant sockets, una comunicació dúplex es pot aconseguir mitjançant l'ús de dos sockets a cada costat. Amb sockets orientats a connexió, cada part actua com a client i servidor.

###4.4 RMI Callbacks
Elements necessaris per realitzar el callback:
- Un client amb Callback es registra a un servidor RMI.
- El servidor fa callbacks a cada client registrat davant l'ocurrència d'un esdeveniment determinat.

###4.5 Interfícies RMI Callbacks
- El servidor proporciona un mètode remot que permet a un client que es registri 	per a fer-li callback.
- Es necessita una interfície remota pel callback a la banda del client, a més de la interfície de l'objecte remot del costat del servidor.
- Aquesta interfície especifica un mètode per rebre un callback des del servidor.
- El programa client és una subclasse de RemoteObject i implementa la interfícien de callback, incloent el mètode per rebre'l.
- El client abans de res, s'ha de registrar al servidor per rebre un callback.
- El servidor invoca el métode remot del client en cas de produir-se l'esdeveniment esperat.

###4.6 Estructura aplicació distribuïda RMI amb callbacks
**Client:**
- Interfície Callback client
- Implementació Interfície Callback client
- Aplicació client

**Servidor:**
- Interfície Callback servidor
- Implementació Interfície Callback servidor
- Aplicació servidor
- RMIRegistry

###4.7 Interfície Callback Servidor
```java
public interface CallbackServerInterface extends Remote {
	public void registerForCallback(CallbackClientInterface callbackClientObject) throws RemoteException;
    public void unregisterForCallback(CallbackClientInterface callbackClientObject) throws RemoteException;
}
```

###4.8 Implementació Interfície Callback Servidor
```java
public synchronized void registerForCallback(CallbackClientInterface callbackClientObject) throws RemoteException {
	if(!(clientList.contains(callbackClientObject))) {
    	clientList.add(callbackClientObject);
        System.out.println("Registered new client");
    }
}

public synchronized void unregisterForCallback(CallbackClientInterface callbackClientObject) throws RemoteException {
	if(!(clientList.removeElement(callbackClientObject))) {
        System.out.println("Unregistered client");
    }else{
    	System.out.println("Client wasn't registered");
    }
}
```
###4.9 Aplicació Callback Servidor
```java
public static void main(String args[]) {
	startRegistry(RMIportNum);
    CallbackServerImpl exportedObj = new CallbackServerImpl();
    registryURL = "rmi://localhost:" + portNum + "/callback";
    Naming.rebind(registryURL, exportedObj);
    System.out.println("Callback server ready");
}
```

###4.10 Interfície Callback Client
```java
public interface CallbackClientInterface extends Remote {
	public String notifyMe(String message) throws RemoteException;
}
```

###4.11 Implementació Interfície Callback Client
```java
public class CallbackClientImpl extends UnicastRemoteObject implements CallbackClientInterface {
	public CallbackClientImpl() throws RemoteException {
    	super();
    }
    public String notifyMe(String message){
    	String returnMessage = "Call back received" + message;
        System.out.println(returnMessage);
        return returnMessage;
    }
}
```

###4.12 Aplicació Callback Client
```java
public static void main(String args[]) {
	String URL = "rmi://localhost:" + portNum + "/callback" ;
    CallbackServerInterface h = (CallbackServerInterface)Naming.lookup(registryURL);
	System.out.println("Lookup Completed");
    System.out.println("Server said " + h.sayHello());
    CallbackClientInterface callbackObj = new CallbackClientImpl();
    h.registerForCallback(callbackObj);
    System.out.println("Registered for callback");
}
```

###4.13 Objectes Remots amb múltiples interfícies
Un mateix objecte remot pot donar serveis a diferents tipus de clients. Si totes les funcionalitats que dóna es troben en una única Interfície Remota, qualsevol client pot invocar mètodes que no li pertocarien.

**Exemple:**
>Aplicació de gestió de notes per alumnes i professors.

**Problema**
>Com fem que professors puguin modificar notes però els alumnes no?

**Solució**
>Objecte remot que implementa dos interfícies: una per l'alumne i una altre pel professor. Cadascuna amb els métodes corresponents.

###4.14 RMI i Threads
- Les crides a métodes remotes poden esdevenir en diferents threads en la banda de l'objecte remot.
- Cal assegurar-se que tot el codi dintre dels métodes sigui thread-safe
- Cal sincronitzar només les zones d'exclusió mútua (zones de dades compartides)
- Cal anar en compte amb les collections de java, ja que no son thread-safe. Utilitzeu les collections que hi ha a **java.utils.concurrent**

###4.15 Broadcast en RMI
Enviar un missatge a tots els usuaris registrats:
```java
for(IClient client: registredClients)
	client.notifyResult("OK");
```
**Problemes:**
- No és just (s'envia de forma seqüèncial).
- No és robust (si un client cau o no respon, els demés es veuen afectats).

**Solució:**
- Aproximació Multi-thread
```java
for(IClient client: registredClients)
	new ClientNotifyThread(client, "OK").start();
```

###4.16 P2P fent servir RMI
No existeix diferència entre rols, cada peer és igual que els demés.
- Cada peer pot comunicar-se amb qualsevol peer, sense necessitat d'un server.
- Cada peer ha de poder localitzar els demés peers.
	- Una **taula hash distribuïda** és necessària per tenir totalment descentralitzada la localització dels peers.
	- Cada peer pot mantenir una part de la taula hash actualitzada amb els seus peers propers.
- Ens hem d'assegurar que el peer es realment qui diu ser.
	- En registrar-se al servidor, aquest li ha de proporcionar una clau i el peer presentar-la a cada iteració.
	- En cas de comunicació entre peers, s'ha de presentar una clau pública tipus RSA i verificar en el servidor que la referència remota és realment del mateix usuari que es va registrar.


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




