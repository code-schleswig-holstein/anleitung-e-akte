# Anbindung der E-Akte an CKAN

Für die Kommunikation zum Transparenzportal wird das [CKAN API](http://docs.ckan.org/en/ckan-2.7.3/api/) verwendet.

Als Metadatenmodell wird [DCAT-AP.de 1.0.2](https://www.dcat-ap.de/def/) verwendet. 
Ein Eintrag im Transparenzportal entspricht dabei einem *Dataset*. Ein Dataset 
kann aus mehreren *Distribution* (Dateien) bestehen. Dabei wird es sich in der 
Regel um PDF-Dateien handeln.

Dokumente (*Datasets*), die eine zeitliche Reihe bilden (jährliche Berichte, sich ändernde
Geschäftsverteilungspläne), werden in einer *Collection* zusammengefasst.

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

## Übertragung der Datei(en) und Metadaten an das Transparenzportal

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

Soll das Dokument in eine zeitliche Reihe eingeordnet werden, ist dies nun durch
einen weiteren API-Aufruf zu erledigen.

Nun kann die PDF-Datei hochgeladen werden:



### Metadaten

* Titel
* Informationsgegenstand
* Veröffentlichungsdatum 
* Beschreibung (optional) 
* Lizenz
* Kategorie (optional)
* Zeitraum Beginn
* Zeitraum Ende (optional)

Was muss hier wie übertragen werden? Wie erfolgt die Verbindung? Wie die Authentifizierung? Dateiformate etc.

Was meldet das Transparenzportal in welcher Form zurück?

## Löschen der Datei(en) und Metadaten im Transparenzportal> 
Wie soll dieser Löschvorgang angestossen werden?

Was meldet das Transparenzportal in welcher Form zurück?