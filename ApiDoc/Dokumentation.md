# Einführung
Für den Entwurf des Backend APIs wurde das Framework Swagger eingesetzt. Die Design-Dokumentation kann unter https://app.swaggerhub.com/ eingesehen werden, indem die Datei /ApiDoc/swagger.yaml auf der Plattform hochgeladen wird.

# Dokumentation der Design-Entscheidungen
Zum Einsehen von Ideen ist keine Autorisierung erforderlich. Daher muss bei der entsprechenden Methode (GET-Request auf „idea/{id}“ keine Autorisierung erfolgen. Für weiter Operationen, etwa das Hinzufügen einer Idee, muss der Benutzer angemeldet sein. An den Stellen, an denen ein Passwort des Nutzers übertragen werden muss, erfolgt dies gehasht, damit keine Passwörter im Klartext abgehört werden können.

Um die Anzahl der Requests gegen die Idea-Wall-API zu verringern, wird beim Verändern oder Erstellen einer Idee stets direkt das erstellte bzw. geänderte Objekt in der Response an das Frontend zurückgegeben. Dadurch muss nach dem Anlegen einer Idee nicht erneut ein Request abgesendet werden, um diese dem Benutzer anzeigen zu können. 

Ratings werden anonym abgegeben. Daher wird beim Anzeigen der Ratings kein Benutzer mit zurückgegeben. Beim Erstellen muss dagegen ein Nutzer übergeben werden, damit im Backend sichergestellt werden kann, dass ein Nutzer jede Idee jeweils nur einmal bewerten kann. Beim Versuch, eine Idee zu bewerten, die der angemeldete Nutzer bereits bewertet hat, wird als HTTP-Statuscode ein Fehler zurückgegeben und die Bewertung nicht gespeichert.

Die Methoden „post“ und „put“ werden innerhalb der API einheitlich gemäß der W3C-Definition (https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) verwendet. Das Hinzufügen einer Ressource (Idee, Kommentar, Bewertung) erfolgt über eine andere URL als das spätere Bearbeiten. Dabei wird über die erste URL, die nicht spezifisch auf eine bestimmte Ressource hindeutet, nur die „post“-Operation zum Hinzufügen verwendet. Die Ressource wird dann unter einer eigenen URL angelegt, die die im Backend generierte ID der Ressource enthält. Über diese kann sie später mit „put“-Requests geändert werden. „Post“-Requests auf die Ressourcen-spezifischen URLs sind nicht möglich.
Da jede Idee jederzeit genau einen Status hat, wird dieser nicht über eine eigene ID identifiziert, sondern über die ID der Idee, zu der der Status gehört. Der Status wird initial beim Anlegen der Idee im Backend gesetzt. Daher kann er über die API lediglich über „put“-Requests auf die durch die ID der Idee definierte URL angepasst, nicht aber angelegt („post“) werden.
Wird eine nicht definierte Methode aufgerufen, wird grundsätzlich mit dem http-Statuscode 405 (Method not allowed) geantwortet (siehe auch Tabelle unten).


# Unterstützung unterschiedlicher Sprachen
[Moritz:Language Text]

# Weiterführende Ideen
Neben den explizit geforderten Anforderungen in den User Stories, sind im Rahmen des Entwurfes eines Backend APIs auch weiterführende Ideen für mögliche sinnvolle Schnittstellenmethoden dokumentiert worden. So ließe sich beispielsweise bei einem GET auf den Endpunkt /ideas eine Liste der publizierten Ideen zurückgeben, um in der Startansicht der UI eine Übersicht der bisherigen Ideen anzuzeigen.
Eine Gegenüberstellung der verschiedenen HTTP-Verben und deren Verwendung für die zunächst geforderten Endpunkte ist in der untenstehenden Tabelle vorgenommen worden.

|Request-URI                    |  GET                            | POST  	          | PUT  	           | DELETE  	         |
|---	                        |---	                          |---	              |---	               |---    	             |
|/idea  	                    | *Liste aller Ideen*             |  `siehe Doku (1)` |  405               |  405                |
|/idea/{id} 	                | `siehe Doku (3)`                | 405               | `siehe Doku (4)`   | `siehe Doku (2)`    |
|/idea/{id}/comment	            | *Alle Kommentare zu einer Idee* | `siehe Doku (5)`  |  405               | 405                 |
|/idea/{id}/comment/{commentId}	| *Ein bestimmter Kommentar*      | 405     	      | *Kommentar ändern* | *Kommentar löschen* |
|/idea/{id}/rating 	            | 405                             | `siehe Doku (6)`  | 405                | 405                 |
|/idea/{id}/rating/{ratingId}   | 405                             | 405               | *Bewertung ändern* | 405                 |
|/idea/{id}/status 	            | 405                             | `siehe Doku (9)`  | 405                | 405                 |
|/ideas/{userId}	            | `siehe Doku (8)`                | 405               | 405                | 405                 |
|/signup 	                    | 405                             | `siehe Doku (7)`  | 405                | 405                 |

### Legende
|Eintrag                | Beschreibung                                                                                |
|---	                | ---                                                                                         |
|HTTP-Code 405          | Method not allowed : Die angefragte Methode ist nicht definiert und wird nicht unterstützt. |
|*Kursiver Text*        | Sinnvolle Methode, da jedoch nicht in den User-Stories nicht weiter definiert.              |
|`Hervorgehobener Text` | Methode gemäß User-Stories (Nummer in Klammern), siehe Dokumentation der API in Swagger	  |

[Florian:Text für überschüssige HTTP-Verben]
