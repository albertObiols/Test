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

##6.3 Utilitzant objectes remots
- Per utilitzar un objecte remot, hem de saber com:
	- Obtenir una referència remota.
	- Crear un objecte remot i deixar-lo accessible.
	- Invocar un mètode remot.

##6.4 Com obtenir una referència remota
- Quan una màquina virtual s'inicia no té cap referència a l'abast. Per poder comunicar-se a l'exterior ha de buscar la primera referència remota utilitzant un servei de directori per localitzar un objecte remot a partir del seu nom. Un cop haguem localitzat el primer objecte remot és possible que aquest ens envïi noves referències remotes com a valors de retorn d'algun dels seus métodes remots(callback).
- 

