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


## Anlegen eines Dokumentes und Metadaten an das Transparenzportal

Für jedes Dokument muss zunächst ein *Dataset* angelegt werden. Zur Beschreibung des *Dataset* werden mindestens folgende Metadaten benötigt (Pflichtangaben):

| Name des Metadatums   | Beschreibung      |
|-----------------------|-------------------|
| `title`       | Titel, des *Dataset*, wie er später in CKAN angezeigt wird |
| `license_id`  | Identifikator der Lizenz für das *Dataset* (siehe Hinweise) |
| `subject`     | URI des *Informationsgegenstands* des *Dataset* |
| `owner_org`   | `id` der Organisation, die das *Dataset* erzeugt (siehe Hinweise) |
| `issued`      | Veröffentlichungsdatum des *Dataset*, Format siehe Beispiel |
| `notes`       | Beschreibung des *Dataset*, siehe Hinweis |

Zusätzlich können folgende optionale Metadaten gesetzt werden:
| Name des Metadatums   | Beschreibung      |
|-----------------------|-------------------|
| `temporal_start`  | Beginn des Bezugszeitraums des *Dataset*, Format siehe Beispiel |
| `temporal_end`    | Ende des Bezugszeitraums des *Dataset*, Format siehe Beispiel |
| `tags`            | Schlagworte, Format siehe Beispiel |
| `groups`          | Kategorien denen das *Dataset* zugeordnet ist (siehe Hinweise), Format siehe Beispiel |

Die folgenden API-Zugriffe werden über den url-Client cURL (https://de.wikipedia.org/wiki/CURL) durchgeführt. Es kann aber ebenso ein anderer Client verwendet werden.


### Beispiel für Anlegen eines *Dataset*
```bash
curl -X POST \
    'http://transparenz-test.zitsh.de/api/3/action/package_create' \
    -H'Authorization: 1dc4ab92-5475-43a9-ae91-3b4aa8ab6aa0' \
    -H 'Content-Type: application/json' \
    -d '{
        "title": "Testdatensatz",
        "license_id": "http://dcat-ap.de/def/licenses/dl-zero-de/2.0",
        "subject": "http://d-nb.info/gnd/4128022-2",
        "owner_org": "2a6d6241-fdfd-4d9a-9106-8c658be43a27",
        "groups": [
            {"name": "soci"},
            {"name": "educ"}
        ],
        "extras": [
            {"key": "issued", "value": "2019-07-07T00:00:00"},
            {"key": "temporal_start", "value": "2017-01-01T00:00:00"},
            {"key": "temporal_end", "value": "2017-03-31T00:00:00"}
        ],
        "notes": "Dies ist ein Testdatensatz, der über die CKAN-API erstellt wurde.",
        "tags": [
            {"name": "Test"},
            {"name": "API"}
        ]
    }'
    
```

Auf die obige Anfrage antwortet CKAN folgendermaßen:
```json
{
    "help": "http://185.223.104.9/api/3/action/help_show?name=package_create",
    "success": true,
    "result": {
        "license_title": "Datenlizenz Deutschland \u2013 Zero \u2013 Version 2.0",
        "maintainer": null,
        "relationships_as_object": [],
        "is_new": false,
        "private": false,
        "maintainer_email": null,
        "num_tags": 2,
        "id": "637875e5-fccf-4eab-ae2d-0cffee601725",
        "metadata_created": "2019-08-12T08:07:21.760424",
        "subject": "http://d-nb.info/gnd/4128022-2",
        "metadata_modified": "2019-08-12T08:07:21.760432",
        "author": null,
        "author_email": null,
        "successor_url": null,
        "state": "active",
        "version": null,
        "creator_user_id": "cbc8c4a0-276f-43e9-a557-36468940ea65",
        "type": "dataset",
        "resources": [],
        "num_resources": 0,
        "tags": [
            {
                "vocabulary_id": null,
                "state": "active",
                "display_name": "API",
                "id": "ea8ca1f3-7737-4058-9cf5-f1298e0a7bdd",
                "name": "API"
            },
            {
                "vocabulary_id": null,
                "state": "active",
                "display_name": "Test",
                "id": "11bf96ec-a635-47c5-b052-befbc20cd03e",
                "name": "Test"
            }
        ],
        "daterange_prettified": "1. Quartal 2017",
        "groups": [
            {
                "display_name": "Bev\u00f6lkerung und Gesellschaft",
                "description": "",
                "image_display_url": "",
                "title": "Bev\u00f6lkerung und Gesellschaft",
                "id": "soci",
                "name": "soci"
            },
            {
                "display_name": "Bildung, Kultur und Sport",
                "description": "",
                "image_display_url": "",
                "title": "Bildung, Kultur und Sport",
                "id": "educ",
                "name": "educ"
            }
        ],
        "license_id": "http://dcat-ap.de/def/licenses/dl-zero-de/2.0",
        "relationships_as_subject": [],
        "organization": {
            "description": "",
            "created": "2019-08-06T10:56:19.169508",
            "title": "Test-Organisation",
            "name": "test-organisation",
            "is_organization": true,
            "state": "active",
            "image_url": "",
            "revision_id": "e2db2344-707b-4481-9a23-e25e044fc600",
            "type": "organization",
            "id": "2a6d6241-fdfd-4d9a-9106-8c658be43a27",
            "approval_status": "approved"
        },
        "name": "testdatensatz1",
        "isopen": true,
        "url": null,
        "notes": "Dies ist ein Testdatensatz, der \u00fcber die CKAN-API erstellt wurde.",
        "owner_org": "2a6d6241-fdfd-4d9a-9106-8c658be43a27",
        "extras": [
            {
                "key": "issued",
                "value": "2019-07-07T00:00:00"
            },
            {
                "key": "licenseAttributionByText",
                "value": ""
            },
            {
                "key": "subject_text",
                "value": "T\u00e4tigkeitsbericht"
            },
            {
                "key": "temporal_end",
                "value": "2017-03-31T00:00:00"
            },
            {
                "key": "temporal_start",
                "value": "2017-01-01T00:00:00"
            }
        ],
        "license_url": "https://www.govdata.de/dl-de/zero-2-0",
        "title": "Testdatensatz",
        "revision_id": "bff35d90-a0a4-4911-add3-55d35e122433",
        "predecessor_url": null
    }
}
```

### Hinweise:
+ Eine Liste der möglichen Lizenzen kann über folgende API-Anfrage bezogen werden:
    ```bash
    curl -X GET \
        http://transparenz-test.zitsh.de/api/3/action/license_list
    ```
+ Eine Liste der Organisationen inklusive der zugehörigen Identifikatoren (Feld `id`) kann über folgende API-Anfrage bezogen werden:
    ```
    curl -X POST \
        http://transparenz-test.zitsh.de/api/3/action/organization_list \
        -H 'Content-Type: application/json' \
        -d '{
            "all_fields": true
        }'
    ```
+ Für das Feld notes muss mindestens ein leerer String ("") übergeben werden.
+ Für das spätere Hinzufügen von *Resourcen* zum soeben erstellten *Dataset* muss sich die `id` des Datasets gemerkt werden. Sie befindet sich im Feld `result['id']` der Antwort.
+ Eine Liste der vorhandenen Kategorien kann über folgende API-Anfrage bezogen werden:
    ```bash
    curl -X POST \
        http://transparenz-test.zitsh.de/api/3/action/group_list \
        -H 'Content-Type: application/json' \
        -d '{
            "all_fields": true
        }'
    ```


## Anlegen einer *Resource* an ein bestehendes Dataset

Zum Anlegen einer *Resource* an ein bestehendes *Dataset* werden folgende Metadaten benötigt:

| Name des Metadatums   | Beschreibung      |
|-----------------------|-------------------|
| `package_id`  | `id` des übergeordneten Dataset |
| `format`      | Dateiformat der Resource |
| `name`        | Name der Resource, wie er in CKAN angezeigt wird |


### Beispiel für Upload einer Datei als Resource
Über folgendes Kommando kann ein Client eine Datei hochladen:
```bash
curl -H'Authorization: 1dc4ab92-5475-43a9-ae91-3b4aa8ab6aa0' \
    'http://transparenz-test.zitsh.de/api/3/action/resource_create' \
    --form upload=@Checkliste-Barrierefreies-PDF.pdf \
    --form package_id=637875e5-fccf-4eab-ae2d-0cffee601725 \
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

Falls zusätzliche Dateien zu dem gleichen Dokument gehören, so muss der Befehl mit der entsprechenden *Resource* und Namen noch einmal ausgeführt werden. Hierbei ist zu beachten, dass die zeitliche Reihenfolge des Uploads in CKAN abgebildet wird und das zuletzt hochgeladene Dokument des Dateiformats unter der url 

```http://transparenz-test.zitsh.decollection/<Name der Collection>/aktuell.<format>```

angezeigt wird.
Die Sichtbarkeit der *Resource* wird durch die Sichtbarkeit des *Dataset* festgelegt. Das heißt, *Resourcen*, die einem privaten *Dataset* zugeordnet sind, sind für nicht-berechtigte Nutzer nicht sichtbar.


## Anlegen von *Collections*

Gehören mehre Dateien zu einer Sammlung, so muss zusätzlich zu den *Datasets* eine *Collection* angelegt werden. Anschließend muss eine Relationship zwischen der *Collection* und den *Datasets* angelegt werden. 


### Anlegen der Collection

Die Collection ist ein Package ähnlich wie das Dataset. Eine Collection ist nicht sichtbar für Dritte, aber kann durch die API aufgerufen werden. 

```bash
curl -v http://transparenz-test.zitsh.de/api/action/package_create \
    -H "Authorization: API-KEY " \
    -d \
    '{  "name":"COLLECTION_NAME",
        "type": "collection",
        "owner_org": "ORGANIZATION"
    }' 
```

name : COLLECTION_NAME kann frei gewählt werden. Der Name kann nur aus Zahlen, kleinen Buchstaben und _ oder –  bestehen.
Leerzeichen sind in einem Collectionanme nicht erlaubt.

"owner_org": ORGANIZATION muss eine in CKAN bestehende Organisation sein (siehe Hinweis bei Anlegen *Dataset*).

Auf diese Anfrage schickt CKAN folgende Antwort:

```json
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
      "name":"collection_name",
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

Im Falle dass die Collection schon existiert, wird folgende Anwort geliefert:

```json
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


### Anlegen der Verknüpfung (*Relationship*)

Um eine Datensatz einer Sammlung zuzufügen, muss eine *Relationship* zwischen der *Collection* und dem *Dataset* erstellt werden. Dies geschieht mit diesem Befehl. 

```bash
curl -v 
http://192.168.126.128/api/action/package_relationship_create \
    -H "Authorization:e3cdb731-496f-407f-9304-e20642075a1a" \
    -d '{     
        "subject":"test_collection",
        "object": "test-title51",
        "type": "parent_of"
    }' 
```

Dabei sind:
+ `subject`: Name der *Collection*
+ `object`: Name des *Dataset*

Auf diese Anfrage schickt CKAN folgende Antwort:
```json
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
```