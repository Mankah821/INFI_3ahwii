# Hausübung KM5-01

## 1. Vorhersage

Ich erwarte bei Query 1, dass Nova mit 3 Songs an erster Stelle steht,
Pixel mit 2 Songs danach folgt und Solveig sowie „Ohne Label“
jeweils 1 Song haben.

COUNT(*) ergibt 4, weil alle Künstler gezählt werden, egal ob sie eine
labelId haben oder nicht. COUNT(labelId) ergibt 3, weil nur die Zeilen
gezählt werden, in denen labelId einen Wert hat. „Ohne Label“ hat
labelId = null und wird deshalb nicht mitgezählt.

## 2.Setup wiederholen
Screenshots im KM5-01-nodejs-prisma

## 4.Reflexion
Prisma hat einige SQL-Befehle verkürzt. Zum Beispiel musste man nicht selbst SELECT ... FROM ... GROUP BY ... schreiben
sondern konnte direkt prisma.song.groupBy() verwenden, was übersichtlicher und kürzer ist. Für komplexere Abfragen musste man
aber trotzdem auf $queryRaw zurückgreifen und SQL direkt schreiben. Das war zum Beispiel beim Self-Join der Künstlerpaare der
Fall, weil sich diese Abfrage mit Prisma nicht so einfach darstellen ließ. Ich würde Prisma vorerst weiterverwenden, weil man
damit bei vielen Standardabfragen schneller arbeiten kann. SQL bleibt trotzdem sehr wichtig, weil man es für komplexere
Abfragen weiterhin braucht.