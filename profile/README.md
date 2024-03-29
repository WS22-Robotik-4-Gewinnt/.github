## Projektstruktur
In jedem Repo liegt eine 'requirements.txt' mit einer Liste der notwendigen Python Bibliotheken. Diese werden im Docker Image automatisch installiert.
Zudem ist der Eintrittspunkt jeweils ./src/main.py
Etwaige Änderungen an Systemdateien (@Adrian) sollen als shell-script hinterlegt und in der Dockerfile aufgerufen werden, damit möglichst keine händischen Eingriffe zur Installation notwendig sind.

## Ports
- REST Bildverarbeitung: 8090
- REST Spielalgorithmus: 8093
- REST Hardwaresteuerung: 8096

## Datenstruktur
### Spielfeld 7x6
```
6 0  0  0  0  0  0  0
5 0  0  0  0  0  0  0
4 0  0  0  0  0  0  0
3 h  0  0  r  0  0  0
2 h  r  r  h  r  0  r
1 h  h  h  r  h  r  r
--1--2--3--4--5--6--7

// h = Human Player | r = Robot Player | 0 = empty space
```

## Schnittstellen / Kommunikation
###  Bildverarbeitung -> Spielalgorithmus
POST http://localhost:8093/updateBoard 
```json
{ "Column1": {"Row1":"h", "Row2":"0", "Row3":"0", "Row4":"0", "Row5":"0", "Row6":"0"},
"Column2": {"Row1":"0", "Row2":"0", "Row3":"0", "Row4":"0", "Row5":"0", "Row6":"0"},
"Column3": {"Row1":"0", "Row2":"0", "Row3":"0", "Row4":"0", "Row5":"0", "Row6":"0"},
"Column4": {"Row1":"0", "Row2":"0", "Row3":"0", "Row4":"0", "Row5":"0", "Row6":"0"},
"Column5": {"Row1":"0", "Row2":"0", "Row3":"0", "Row4":"0", "Row5":"0", "Row6":"0"},
"Column6": {"Row1":"h", "Row2":"h", "Row3":"h", "Row4":"0", "Row5":"0", "Row6":"0"},
"Column7": {"Row1":"r", "Row2":"r", "Row3":"0", "Row4":"0", "Row5":"0", "Row6":"0"},
"Difficulty": 4
}
```
wobei 'Difficulty' die Suchtiefe für den nächsten Zug ist. 2 <= Difficulty <= 5. Normalerweise suchen wir mit 4, damit ist der Algorithmus recht schlau.

### Spielalgorithmus -> Hardwaresteuerung
Roboter soll Zug ausführen
POST http://localhost:8096/move
```json
{
 "col": 7,
 "row": 1
}
```
Spiel-Ende
POST http://localhost:8096/end
```json
{"winner": "DRAW"}
{"winner": "ROBOT"}
{"winner": "HUMAN"}
```

### Hardwaresteuerung -> Bildverarbeitung
Button Spieler ist fertig und gibt Spiel für Roboter frei
POST http://localhost:8090/ready
```json
{"difficulty": 4}
```
