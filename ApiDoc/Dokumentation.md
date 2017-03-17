# Einführung
Für den Entwurf des Backend APIs wurde das Framework Swagger eingesetzt. Die Design-Dokumentation kann unter [editor.swagger.io](https://editor.swagger.io/) eingesehen werden, indem die Datei **/ApiDoc/swagger.yaml** in den Editor importiert wird. Für weiterführende Übersicht mit Beispielen zu den möglichen Requests muss die Datei **swagger.yaml** auf der Plattform [Swaggerhub](https://app.swaggerhub.com) hochgeladen werden.

# Dokumentation der Design-Entscheidungen
[Arthur:Design-Entscheidungstext]

# Unterstützung unterschiedlicher Sprachen
[Moritz:Language Text]

# Weiterführende Ideen
Neben den explizit geforderten Anforderungen in den User Stories, sind im Rahmen des Entwurfes eines Backend APIs auch weiterführende Ideen für mögliche sinnvolle Schnittstellenmethoden dokumentiert worden. So ließe sich beispielsweise bei einem **GET** auf den Endpunkt **/ideas** eine Liste der publizierten Ideen zurückgeben, um in der Startansicht der UI eine Übersicht der bisherigen Ideen anzuzeigen.
Eine umfassende Gegenüberstellung der verschiedenen HTTP-Verben und deren Verwendung für die definierten Endpunkte ist in der untenstehenden Tabelle vorgenommen worden.

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
|HTTP-Statuscode 405    | Method not allowed : Die angefragte Methode ist nicht definiert und wird nicht unterstützt. |
|*Kursiver Text*        | Sinnvolle Methode, da jedoch nicht in den User-Stories nicht weiter definiert.              |
|`Hervorgehobener Text` | Methode gemäß User-Stories (Nummer in Klammern), siehe Dokumentation der API in Swagger	  |

