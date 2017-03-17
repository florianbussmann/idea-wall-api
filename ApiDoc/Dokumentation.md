# Einführung
Für den Entwurf des Backend APIs wurde das Framework Swagger eingesetzt. Die Design-Dokumentation kann unter [editor.swagger.io](https://editor.swagger.io/) eingesehen werden, indem die Datei **/ApiDoc/swagger.yaml** in den Editor importiert wird. Für weiterführende Übersicht mit Beispielen zu den möglichen Requests muss die Datei **swagger.yaml** auf der Plattform [Swaggerhub](https://app.swaggerhub.com) hochgeladen werden.

# Dokumentation der Design-Entscheidungen
## Authentifizierung und Autorisierung
Zum Einsehen von Ideen ist keine Autorisierung erforderlich. Daher muss bei der entsprechenden Methode (**GET**-Request auf **idea/{id}** keine Autorisierung erfolgen. Für weitere Operationen, etwa das Hinzufügen einer Idee, muss der Benutzer angemeldet sein. An den Stellen, an denen ein Passwort des Nutzers übertragen werden muss, erfolgt dies gehasht, damit keine Passwörter im Klartext abgehört werden können.
Zur Authentifizierung bei den Ressourcen, die diese benötigen, wird Basic Authentication genutzt. Existiert noch keine authorisierte Session, antworten die entsprechenden Methoden der API mit dem HTTP-Statuscode 401 („Unauthorized“) und verlangen somit vom Client zunächst eine Authentifizerung. Werden diese in der Folge korrekt übergeben oder waren von Beginn an in den Request-Daten vorhanden, wird die ursprünglich angefragte Operation durchgeführt.

## Responses
Um die Anzahl der Requests gegen die Idea-Wall-API zu verringern, wird beim Verändern oder Erstellen einer Idee stets direkt das erstellte bzw. geänderte Objekt in der Response an das Frontend zurückgegeben. Dadurch muss nach dem Anlegen einer Idee nicht erneut ein Request abgesendet werden, um diese dem Benutzer anzeigen zu können.

## POST vs. PUT
Die Methoden **POST** und **PUT** werden innerhalb der API einheitlich gemäß der [W3C-Definition](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) verwendet. Das Hinzufügen einer Ressource (Idee, Kommentar, Bewertung) erfolgt über eine andere URL als das spätere Bearbeiten. Dabei wird über die erste URL, die nicht spezifisch auf eine bestimmte Ressource hindeutet, nur die **POST**-Operation zum Hinzufügen verwendet. Die Ressource wird dann unter einer eigenen URL angelegt, die die im Backend generierte ID der Ressource enthält. Über diese kann sie später mit **PUT**-Requests geändert werden. **POST**-Requests auf die Ressourcen-spezifischen URLs sind nicht möglich.
Da jede Idee jederzeit genau einen Status hat, wird dieser nicht über eine eigene ID identifiziert, sondern über die ID der Idee, zu der der Status gehört. Der Status wird initial beim Anlegen der Idee im Backend gesetzt. Daher kann er über die API lediglich über **PUT**-Requests auf die durch die ID der Idee definierte URL angepasst, nicht aber angelegt (**POST**) werden.
Wird eine nicht definierte Methode aufgerufen, wird grundsätzlich mit dem HTTP-Statuscode 405 (Method not allowed) geantwortet (siehe auch Tabelle 1: Verwendung der HTTP-Verben).

## Bewerten einer Idee
Ratings werden anonym abgegeben. Daher wird beim Anzeigen der Ratings kein Benutzer mit zurückgegeben. Beim Erstellen muss dagegen der Nutzer übergeben werden, damit im Backend sichergestellt werden kann, dass ein Nutzer jede Idee jeweils nur einmal bewerten kann. Hat der angemeldete Nutzer eine Idee bereits bewertet, wird diese bei einer erneuten Bewertung überschrieben.

## Status einer Idee
Der Status einer Idee wird als String zurückgegeben, der den Status repräsentiert. Dies wurde entschieden, damit der Status den Benutzern ohne weitere Konvertierung seitens der Frontend-Developer sinnvollerweise als Text statt als ID oder sonstiger Code angezeigt werden kann.

## Versionierung des Backend API
Für zukünftige Änderungen an dem API wurde ein Paramter für die Identifizierung des verwendeten Versionsstatus in die URI aufgenommen. Hierdurch wird sichergestellt, dass eine zuvor definierte Funktionsweise sich nicht automatisch durch Änderungen an dem Backend API auf das Frontend auswirkt. Erst bei einem Versionssprung der aufgerufenen API-Endpunkte an eine neuere Version darf sich das grundsätzliche Verhalten des Backend API ändern. Dies schließt nicht aus, dass bei einem kritischen Fehler ein Hotfix die interne Funktionslogik einer bereits definierten Version noch verändern kann.

Beispiel für einen API-Endpunkt: /`{version}`/**ideas**?`lang=de`

# Unterstützung unterschiedlicher Sprachen (User-Story 10)
Die Formate der übergebenen Daten usw. richten sich nach der Sprache, in der die Idee angezeigt werden soll. Texte, etwa die Beschreibung der Idee werden dabei nicht automatisch übersetzt. Die verwendeten Formate richten sich dabei automatisch nach der vom Browser bei jedem Request übergebenen Sprachkennung. Soll die Ausgabe in einer anderen Sprache als dieser erfolgen, kann der optionale Sprach-Parameter in der Request-URL gesetzt werden.

Wird die in der URL übergebene Sprache vom Server nicht unterstützt, wird die im Browser standardmäßig genutzte Sprache genutzt. Ist diese nicht gesetzt oder wird nicht unterstützt und in der URL ist ebenfalls keine bzw. keine unterstützte Sprache gesetzt, wird die Standard-Sprache des Servers genutzt.

Bei der Antwort wird im HTTP Response Header der Parameter **Content-Language** gesetzt, damit im Frontend die Sprache der Antwort angezeigt werden kann.

# Weiterführende Ideen
Neben den explizit geforderten Anforderungen in den User Stories, sind im Rahmen des Entwurfes eines Backend APIs auch weiterführende Ideen für mögliche sinnvolle Schnittstellenmethoden dokumentiert worden. So ließe sich beispielsweise bei einem **GET** auf den Endpunkt **/ideas** eine Liste der publizierten Ideen zurückgeben, um in der Startansicht der UI eine Übersicht der bisherigen Ideen anzuzeigen.
Eine umfassende Gegenüberstellung der verschiedenen HTTP-Verben und deren Verwendung für die definierten Endpunkte ist in der untenstehenden Tabelle vorgenommen worden.

### Tabelle 1: Verwendung der HTTP-Verben
|Request-URI                              |  GET                            | POST  	          | PUT  	           | DELETE  	         |
|---	                                  | ---	                            | ---	              | ---	               | ---    	         |
|{version}/idea                           | 405                             |  `siehe Doku (1)`   |  405               |  405                |
|{version}/idea/{id} 	                  | `siehe Doku (3)`                | 405                 | `siehe Doku (4)`   | `siehe Doku (2)`    |
|{version}/idea/{id}/comment	          | 405                             | `siehe Doku (5)`    |  405               | 405                 |
|{version}/idea/{id}/comment/{commentId}  | 405                             | 405     	          | *Kommentar ändern* | *Kommentar löschen* |
|{version}/idea/{id}/rating 	          | 405                             | `siehe Doku (6)`    | 405                | 405                 |
|{version}/idea/{id}/status 	          | 405                             | 405                 | `siehe Doku (9)`   | 405                 |
|{version}/ideas                          | *Liste aller Ideen*             | 405                 | 405                | 405                 |
|{version}/ideas/{userId}	              | `siehe Doku (8)`                | 405                 | 405                | 405                 |
|{version}/signin 	                      | 405                             | *Anmeldung*         | 405                | 405                 |
|{version}/signup 	                      | 405                             | `siehe Doku (7)`    | 405                | 405                 |

### Legende
|Eintrag                | Beschreibung                                                                                |
|---	                | ---                                                                                         |
|HTTP-Statuscode 405    | Method not allowed : Die angefragte Methode ist nicht definiert und wird nicht unterstützt. |
|*Kursiver Text*        | Sinnvolle Methode, da jedoch nicht in den User-Stories nicht weiter definiert.              |
|`Hervorgehobener Text` | Methode gemäß User-Stories (Nummer in Klammern), siehe Dokumentation der API in Swagger	  |
