# Anbindung der E-Akte an CKAN

Für die Kommunikation zum Transparenzportal wird das [CKAN API](http://docs.ckan.org/en/ckan-2.7.3/api/) verwendet.

Als Metadatenmodell wird [DCAT-AP.de 1.0.2](https://www.dcat-ap.de/def/) verwendet. 
Ein Eintrag im Transparenzportal entspricht dabei einem *Dataset*. Ein Dataset 
kann aus mehreren *Distribution* (Dateien) bestehen. Dabei wird es sich in der 
Regel um PDF-Dateien handeln.

Dokumente (*Datasets*), die eine zeitliche Reihe bilden (jährliche Berichte, sich ändernde
Geschäftsverteilungspläne), werden in einer Sammlung (*Collection*) zusammengefasst.

## Authentifizierung

Für die Authentifizierung verwendet CKAN einen sogenannten `api_key`. 

Im Testsystem wurden zwei Nutzer angelegt:

| organization  | api_key                              |
| ------------- | ------------------------------------ |
| ministerium_a | 4ef9e986-8437-4ce7-a9cc-f486fba86757 |
| ministerium_b | a08ebb75-329a-435b-af08-f322a7da41bd | 

## Verbindung zum CKAN

Zunächst muss (hier in Python-Code) eine Verbindung zum CKAN-Server aufgebaut werden:

```python
r_ckan = RemoteCKAN('http://transparenz-test.zitsh.de', apikey=api_key)
```

## Anlegen eines Dokumentes und Metadaten an das Transparenzportal

Für jedes Dokument muss zunächst ein *Dataset* angelegt werden:

```python
r_ckan.action.package_create(
             title='Titel (Pflichtangabe)',
             notes='Beschreibung (optional)',
             license_id='Lizenz',   # unsere Juristen klären noch, was hier angegeben wird
             extras={
                 'issued': 'Veröffentlichungsdatum (Pflichtangabe)',
                 'temporal_start': 'Zeitraum Beginn (Pflichtangabe)',
                 'temporal_end': 'Zeitraum Ende (optional)',
                 'subject': 'Informationsgegenstand'
             },
             tags=['Schlagwort 1', Schlagwort 2'],
             groups='Kategorie')
```

CKAN wird auf diesen Aufruf den Identifikator des *Dataset* zurückliefern.


Nun kann die dazugehörige Datei hochgeladen werden:

Die folgenden API-Zugriffe werden über den url-Client cURL (https://de.wikipedia.org/wiki/CURL) durchgeführt. Es kann aber ebenso ein anderer Client verwendet werden.

Über folgendes Kommando kann ein Client eine Datei hochladen:
```bash
curl -H'Authorization: e3cdb731-496f-407f-9304-e20642075a1a' \
    'http://192.168.152.133:5000/api/3/action/resource_create' \
    --form upload=@Checkliste-Barrierefreies-PDF.pdf \
    --form package_id=test-ohne-startdatum \
    --form format=PDF \
    --form name='Checkliste barrierefreies PDF'
```

Daraufhin erhält der Client folgende Antwort von CKAN:
```json
{
    "help": "http://192.168.152.133:5000/api/3/action/help_show?name=resource_create",
    "success": true,
    "result": {
        "cache_last_updated": null,
        "cache_url": null,
        "mimetype_inner": null,
        "hash": "",
        "description": "",
        "format": "PDF",
        "url": "http://192.168.152.133:5000/dataset/b66f2ea4-3a25-42a5-88f8-bffbbdbb46be/resource/0929f4f5-d14c-4a0e-aea7-f293587d5b48/download/checkliste-barrierefreies-pdf.pdf",
        "created": "2019-08-09T08:28:03.467267",
        "state": "active",
        "package_id": "b66f2ea4-3a25-42a5-88f8-bffbbdbb46be",
        "last_modified": "2019-08-09T08:28:03.410142",
        "mimetype": "application/pdf",
        "url_type": "upload",
        "position": 5,
        "revision_id": "d1788a89-dbec-4b36-8711-5178bb63f6a5",
        "size": 112437,
        "datastore_active": false,
        "id": "0929f4f5-d14c-4a0e-aea7-f293587d5b48",
        "resource_type": null,
        "name": "Checkliste barrierefreies PDF"
    }
}
```

Falls zusätzliche Dateien zu dem gleichen Dokument gehören, so muss der Befehl mit der entsprechenden Resource und Namen noch einmal ausgeführt werden. Hierbei ist zu beachten, dass die zeitliche Reihenfolge des Uploads in CKAN abgebildet wird und das zu letzt Hochgeladene Dokument des Dateiformats mit dem Aufruf collection/<Name der Collection> /aktuell. <Type> angezeigt wird.
Die Sichtbarkeit wird auf dem Dataset Level festgelegt.

## Anlegen von Sammlungen

Gehören mehre Dateien zu einer Sammlung, so muss zusätzlich zu den Datasets eine Collection angelegt werden. Und anschließend muss eine Relationship zwischen der Collection und den Datasets angelegt werden. 


### Anlegen der Collection

Die Collection ist ein Package ähnlich wie das Dataset. Eine Collection ist nicht sichtbar für Dritte, aber kann durch die API aufgerufen werden. 

```
curl -v http:// IP-Adresse /api/action/package_create -d 
    '{  "name":"COLLECTION_NAME",
        "type": "collection",
        "owner_org": "ORGANIZATION"
    }' 
-H "Authorization: API-KEY "
```

name : COLLECTION_NAME kann frei gewählt werden. Der Name kann nur aus Zahlen, kleinen Buchstaben und _ oder –  bestehen.
Leerzeichen sind in einem Collectionanme nicht erlaubt.

"owner_org": ORGANIZATION muss einer im CKAN bestehnden Organisation entsprechen.

```
{
   "help":"http://10.0.2.15/api/3/action/help_show?name=package_create",
   "success":true,
   "result":{
      "license_title":null,
      "maintainer":null,
      "relationships_as_object":[

      ],
      "private":false,
      "maintainer_email":null,
      "num_tags":0,
      "id":"e34ce524-9513-4856-9c75-0b43f0f7ca1d",
      "metadata_created":"2019-08-12T05:54:41.811738",
      "metadata_modified":"2019-08-12T05:54:41.811755",
      "author":null,
      "author_email":null,
      "state":"active",
      "version":null,
      "creator_user_id":"ff8d002d-3908-45e5-ba7b-445381860957",
      "type":"collection",
      "resources":[

      ],
      "num_resources":0,
      "tags":[

      ],
      "groups":[

      ],
      "license_id":null,
      "relationships_as_subject":[

      ],
      "organization":{
         "description":"",
         "created":"2019-06-05T06:22:36.040980",
         "title":"test",
         "name":"test",
         "is_organization":true,
         "state":"active",
         "image_url":"2019-07-10-102908.870936100.jpeg",
         "revision_id":"12e9e347-cca3-49bd-be21-b29c457cd356",
         "type":"organization",
         "id":"61263ac6-64cd-4048-90fc-767ddf45f3b7",
         "approval_status":"approved"
      },
      "name":"colle* Connection #0 to host 192.168.126.128 left intact
ction_name",
      "isopen":false,
      "url":null,
      "notes":null,
      "owner_org":"61263ac6-64cd-4048-90fc-767ddf45f3b7",
      "extras":[

      ],
      "title":"collection_name",
      "revision_id":"5d07b2b5-5779-4a10-918b-d760837b6b69"
   }
}
```

Im Falle dass die Collection schon existiert, wird folgende Anwort geliefert.

```
{
   "help":"http://10.0.2.15/api/3/action/help_show?name=package_create",
   "success":false,
   "error":{
      "__type":"Validation Error",
      "name":[
         "That URL is already in use."
      ]
   }
}
```

### Anlegen der Verknüpfung (Relationship)

Um eine Datensatz einer Sammlung zuzufügen, muss eine Relationship zwischen dem Collection Objekt und dem Datensatz erstellt werden. Dies geschieht mit diesem Befehl. 

```
curl -v 
http://192.168.126.128/api/action/package_relationship_create -d '{     
        "subject":"test_collection", "object": "test-title51", "type": "parent_of"
    }' -H "Authorization:e3cdb731-496f-407f-9304-e20642075a1a"
```

subject: Name der Collection
object: Name des Datasets

type: "parent_of"

Der Type wird zum jetztigen Zeitpunkt noch nicht Benutzt aber sollte konsistet gewählt werden.  

Antwort:

{
   "help":"http://10.0.2.15/api/3/action/help_show?name=package_relationship_create",
   "success":true,
   "result":{
      "comment":"",
      "object":"test_collection",
      "type":"child_of",
      "subject":"test-title51"
   }
}



### Metadaten

* Titel
* Informationsgegenstand
* Veröffentlichungsdatum 
* Beschreibung (optional) 
* Lizenz
* Kategorie (optional)
* Zeitraum Beginn
* Zeitraum Ende (optional)

Was muss hier wie übertragen werden? Wie erfolgt die Verbindung? 

Wie soll ein User Account erstellt werden, welche Rechte soll dieser User haben?

Wie die Authentifizierung? Dateiformate etc.

Was meldet das Transparenzportal in welcher Form zurück?



## Löschen der Datei(en) und Metadaten im Transparenzportal> 
Wie soll dieser Löschvorgang angestossen werden?

Was meldet das Transparenzportal in welcher Form zurück?