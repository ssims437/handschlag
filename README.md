# Handschlag

**Ein Geheimnis über eine öffentliche Leitung.** Elliptische Kurven zum Anfassen: erst so klein,
dass jeder Punkt einzeln zu sehen ist — und am Ende einmal in der Größe, die tatsächlich jede
HTTPS-Verbindung trägt.

→ **[Blatt öffnen](https://ssims437.github.io/handschlag/)**

Vier Kurven über endlichen Körpern (GF(23) bis GF(1009)), dazu:

- **Punktaddition sichtbar** — die Verbindungsgerade durch P und Q ist über einem endlichen Körper
  keine Gerade, sondern eine Punktwolke, die am Rand hinausläuft und links wieder hereinkommt
- **Untergruppen** — die Kette P, 2P, 3P … als Linienzug; bei GF(23) sind die Ordnungen abzählbar
- **Der Handschlag** — zwei Geheimzahlen, drei öffentliche Punkte, ein gemeinsames Ergebnis
- **Rückwärtsrechnen** — stumpfes Durchzählen gegen Baby-Step-Giant-Step, beide mit gezählten
  Schritten, dazu die Hochrechnung auf secp256k1 mit der gemessenen Geschwindigkeit dieses Browsers
- **Einmal in echter Größe** — secp256k1 mit 256-Bit-Zahlen, inklusive der Probe `[n]G = O`
- **Prüflauf** — 182 373 Einzelrechnungen, darunter die Assoziativität über alle 21 952 Punkt-Tripel

## Was die Punktaddition ist

Über den reellen Zahlen: Gerade durch **P** und **Q**, dritter Schnittpunkt mit der Kurve, an der
x-Achse gespiegelt. Über GF(p) gilt dieselbe Formel — nur ist „Spiegeln" jetzt `y → p − y`, und die
Gerade besteht aus p einzelnen Punkten. Der unendlich ferne Punkt `O` ist das neutrale Element.

Das Vielfache `[k]P` entsteht durch Verdoppeln und Addieren: so viele Schritte, wie `k` Bits hat.
Rückwärts — aus `P` und `[k]P` das `k` — kennt niemand etwas Besseres als Wurzel aus der
Gruppenordnung. Darauf beruht die Sicherheit, nicht auf der Formel.

## Was der Prüflauf zeigt

| Behauptung | Ergebnis |
|---|---|
| Punktmenge vollständig | 529 Gitterplätze abgesucht, 27 Lösungen + O = 28 Punkte |
| neutrales Element und Inverse | 28 Punkte, ausnahmslos |
| Addition ist kommutativ | 784 Paare |
| **Addition ist assoziativ** | **21 952 Tripel** — der Teil, den man sonst glaubt |
| schnelle Multiplikation stimmt | 3747 Fälle über GF(97): 26 501 statt 83 730 Additionen |
| Ordnung teilt Gruppenordnung | alle Punkte aller vier Kurven (Lagrange) |
| Punktzahl im Hasse-Intervall | GF(23): 28 · GF(97): 100 · GF(223): 252 · GF(1009): 1056 |
| Handschlag trifft immer | 2401 Geheimzahl-Paare über GF(97) |
| zwei Rechenwerke, ein Ergebnis | 784 Additionen und 868 Multiplikationen, Zahlen gegen BigInt |
| secp256k1: `[n]G = O` | die Ordnung aus dem Standard trifft auf das letzte Bit |
| secp256k1: `[n−1]G = −G` | trifft |
| secp256k1: Handschlag und Rechenregel | `[a]([b]G) = [b]([a]G) = [a·b mod n]G` |

Die letzten drei Zeilen sind die scharfen: die Zahlen `p`, `G` und `n` stammen aus dem Standard,
gerechnet wird mit eigenem Code. Wäre in der Punktaddition ein Bit falsch, käme `[n]G` nicht auf
den unendlich fernen Punkt.

## Was mich das gekostet hat

**Die Geschwindigkeitsmessung war eine Messung von nichts.** Der erste Entwurf hat die
Hochrechnung auf secp256k1 aus der Laufzeit des Baby-Step-Giant-Step-Laufs gebildet. Auf GF(23)
sind das **neun Schritte** — weit unter der Auflösung der Uhr. Ergebnis: „180 000 Schritte/s" und
daraus **6,0·10²⁵ Jahre**. Mit einem eigenen Messlauf über 150 ms (874 000 Punktadditionen am
Stück) sind es **5 822 785 Additionen/s** und **1,9·10²⁴ Jahre** — Faktor 30 Unterschied, allein
weil vorher der Messboden gemessen wurde. Die Zahl klang beide Male beeindruckend; nur eine davon
war eine Messung.

**GF(97) hat keinen Punkt der Ordnung 100.** Die Kurve hat 100 Punkte, aber die Gruppe ist nicht
zyklisch (sie ist Z/2 × Z/50), und der größte Punkt hat Ordnung **50**. Die Regler für die
Geheimzahlen standen zunächst auf `1 … |E|−1` und erzeugten damit Schlüssel, die auf denselben
Punkt führen — das fällt beim Handschlag nicht auf, weil beide Seiten trotzdem übereinstimmen.
Der Prüflauf hat es sichtbar gemacht, weil die Ordnung des Basispunkts mit ausgegeben wird.
Jetzt ist der Regler auf die Ordnung des Basispunkts begrenzt.

**Zwei Rechenwerke, weil eins nichts beweist.** Für secp256k1 braucht es BigInt, für die
erschöpfenden Läufe wären BigInt-Zahlen zu langsam. Beide Implementierungen getrennt zu schreiben
war zuerst Verdopplung — bis auffiel, dass genau darin die Absicherung liegt: die BigInt-Variante
muss auf der kleinen Kurve Punkt für Punkt dieselben Ergebnisse liefern wie die Zahlen-Variante
(784 Additionen, 868 Multiplikationen). Erst dann hat die Rechnung auf der großen Kurve Gewicht.

**Die Gerade zu zeichnen war die eigentliche Erkenntnis.** Solange man die Formel schreibt, wirkt
die Punktaddition wie Geometrie mit Zahlen. Zeichnet man die Verbindungsgerade über GF(23) wirklich
— alle 23 Punkte, die auf ihr liegen —, sieht man Streuung statt Linie. Die Konstruktion
„dritter Schnittpunkt, gespiegelt" bleibt richtig, aber die Anschauung, die sie erklärt, ist über
einem endlichen Körper einfach weg. Bei GF(1009) ist auch von der Struktur nichts mehr zu erkennen —
und trotzdem ist sie in Millisekunden zu knacken. Sicherheit ist hier nicht Unordnung, sondern Größe.

**Was das Blatt nicht kann:** Es rechnet mit Doubles, solange `p² < 2⁵³` bleibt (p ≤ 1009 · sicher),
und mit BigInt erst bei secp256k1. Es gibt keinen Schutz gegen Seitenkanäle — die Multiplikation
verzweigt über die Bits des Schlüssels, was in echtem Code eine Lücke wäre. Für ein Blatt, das
zeigen soll, *was* gerechnet wird, ist das der richtige Kompromiss; für Krypto im Einsatz ist es
genau der falsche.

## Technik

Eine einzelne HTML-Datei. Kein Build, keine Bibliothek, nichts verlässt den Browser.
Canvas 2D, reines JavaScript, `BigInt` für die 256-Bit-Rechnung, hell und dunkel.

## Die ganze Sammlung

Fünfzehn Blätter nach Feld geordnet, jedes mit eigenem Repo:
**[ssims437.github.io](https://ssims437.github.io/)**

## Lizenz

MIT
