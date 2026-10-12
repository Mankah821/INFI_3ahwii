# Hausübung KM5-02

## 1 Vorhersage

Die Spalte ort verletzt die 3. Normalform, weil ort nicht direkt von bestell_nr,
sondern von plz abhängt. Es gilt: bestell_nr → plz → ort.

## 2 Aufgabe

`bestellung_denorm` erfüllt die 1NF, weil alle Werte atomar sind.
 Die 2NF ist ebenfalls erfüllt, weil `bestell_nr` ein einfacher Primärschlüssel ist und daher keine partielle Abhängigkeit möglich ist.

In der 3NF gibt es jedoch eine transitive Abhängigkeit:

`bestell_nr → plz → ort`

Deshalb wird `ort` zusammen mit `plz` in eine eigene Tabelle ausgelagert.

Tabelle `bestellung`:
- `bestell_nr`(PK)
- `kunde`
- `plz`(FK)

Tabelle `plz`:
- `plz`(PK)
- `ort`

## 3 Aufgabe

### Beispiel 1: `song_playlist_denorm`

`song_titel` hängt nur von `song_id` ab und nicht vom gesamten zusammengesetzten Primärschlüssel `(song_id, playlist_id)`.

Abhängigkeit:

`song_id → song_titel`

Deshalb wird `song_titel` in die Tabelle `song` ausgelagert.
```sql
CREATE TABLE song(
  song_id INTEGER PRIMARY KEY,
  titel TEXT NOT NULL
);
CREATE TABLE song_playlist(
  song_id INTEGER NOT NULL,
  playlist_id INTEGER NOT NULL,
  PRIMARY KEY(song_id, playlist_id)
);
INSERT INTO song(song_id, titel) VALUES
  (1, 'Silent Lines'),
  (2, 'Night Ferry'),
  (3, 'Dust Choir');
INSERT INTO song_playlist(song_id, playlist_id) VALUES
  (1, 10),
  (1, 20),
  (2, 20);
```

### Beispiel 2: `kunde_hobby_denorm`

Die Spalte `hobbys` verletzt die 1NF, weil mehrere Werte in einer Zelle gespeichert werden.

Deshalb wird die Tabelle in `kunde` und `hobby` aufgeteilt, sodass jedes Hobby in einer eigenen Zeile steht.

```sql
CREATE TABLE kunde(
  kunde_id INTEGER PRIMARY KEY,
  name TEXT NOT NULL
);
CREATE TABLE hobby(
  kunde_id INTEGER NOT NULL,
  hobby TEXT NOT NULL,
  PRIMARY KEY(kunde_id, hobby)
);
INSERT INTO kunde(kunde_id, name) VALUES
  (1, 'Auer'),
  (2, 'Beck'),
  (3, 'Cevik');
INSERT INTO hobby(kunde_id, hobby) VALUES
  (1, 'Lesen'),
  (1, 'Schwimmen'),
  (2, 'Schach');
```
## 4 Aufgabe

`deno task demo` wurde erfolgreich ausgeführt

Der Screenshot der Konsolenausgabe befindet sich im Ordner `KM5-02-normalisierung`.

# Zusatz: 8. Jetzt du!

## 1. Vorhersage

`tracks` verletzt die 1NF, weil mehrere Track-Werte in einer Zelle gespeichert werden können.

Die 2NF ist hier kein Thema, weil `bestell_nr` ein einfacher Primärschlüssel ist und es daher keine partielle Abhängigkeit geben kann.

`ort` verletzt die 3NF, weil `ort` von `plz` abhängt:

`bestell_nr → plz → ort`

## 2. Zerlegung bis 3NF

Für die 1NF wird `tracks` ausgelagert, weil mehrere Track-Werte nicht in einer Zelle stehen sollen.

Tabelle `bestellung`:
- `bestell_nr` (PK)
- `kunde`
- `plz` (FK → `plz.plz`)

Tabelle `bestellposition`:
- `bestell_nr` (FK → `bestellung.bestell_nr`)
- `position`
- `track_id`
- Primärschlüssel: (`bestell_nr`, `position`)

Die 2NF ist erfüllt: In `bestellung` gibt es nur den einfachen Primärschlüssel
`bestell_nr`, und in `bestellposition` hängt `track_id` vom gesamten
Primärschlüssel (`bestell_nr`, `position`) ab.

Für die 3NF wird `ort` ausgelagert, weil gilt:

`bestell_nr → plz → ort`

Tabelle `plz`:
- `plz` (PK)
- `ort`

## 3. Zurück zur Abfrage (Interleaving)
Für die Ausgabe des Track-Titels wird zusätzlich eine Tabelle
`track(track_id, titel)` benötigt, weil `bestellposition` nur die `track_id`
speichert.

```sql
SELECT
  bestellung.bestell_nr,
  bestellung.kunde,
  bestellung.plz,
  plz.ort,
  track.titel
FROM bestellung
JOIN bestellposition
  ON bestellung.bestell_nr = bestellposition.bestell_nr
JOIN track
  ON bestellposition.track_id = track.track_id
JOIN plz
  ON bestellung.plz = plz.plz;
```