#Tic-Tac-Toe like a Pro

**Vortrag zum Thema Spielbäume**

##Agenda

- Motivation
- Terminologie
- MinMax
- AlphaBeta

## Motivation

### Donald Michies "Menace"

- 1963
- …

### Ziel dieses Vortrages

- Verständnis für formale Betrachtung von Spielen
- Schritte des MinMax-Algorithmus nachvollziehen können (in der Lage sein es zu implementieren)
- Notwendigkeit der Alpha-Beta-Kürzung verstehen und anpassung des Algorithmusses verstehen
- weitere?

## Terminologie

### Einordnung "Spiel"

- [Grafik Spiele]
- Definition: Spiel S ≔ (R, a, F )
  - R Menge legaler Spielzüge
  - k0 ∈ K Anfangskonfiguration
  - F ⊂ K Menge der Endkonfigurationen
  - zwei Spieler: der Max und der Min
- K Menge aller zulässigen Konfigurationen

### Spielzüge, Folgekonfiguration

- R ≔ {r ∶ u → v ∣ u, v ∈ K, r legal }
  - wenn v = r(u) , dann heißt v Folgekonfiguration von u (bezüglich r )
  - analog: u Elternkonfiguration von v (bezüglich r )
- R k ≔ {r ∈ R ∣ r anwendbar auf k}
- Menge der Folgekonfigurationen N (k) ≔ {r(k) ∣ r ∈ R k } ⊆ K
  - N(k f ) = ∅, k f ∈ F
- K induktiv über Folgekonfigurationen definierbar:
  (1) a ∈ K
  (2) ∀k ∈ K ∶ N(k) ⊂ K
- [Grafik Spielzüge]

### Spielbaum (ehemals Spielgraph)

- Spielgraph ist gerichteter Graph G S ≔ (V , E)
  - Knoten V = K
  - Kanten E = ⋃ {(u, v) ∣ v ∈ N(u)}
- G ist Baum mit Wurzel a , Blättern F
- Spielverlauf mit n Zügen ist Pfad p subsequenter Folgekonfigurationen
  - p = (k 0 = a, k 1 , ... , k n ∈ F )
- theoretisches Hilfskonstrukt
- [Grafik: TicTacToe Spielgraph]

### Suchbaum (ehemals Spielbaum)

- Spielbaum ist gerichteter Graph B S ≔ (V , E, l, k 0 )
  - Tiefe l ∈ N, l ≥ 1
  - Wurzelknoten k 0 ∈ K
  - Knoten V = {v = (k 0 , ... , k n ) ∣ ∀k i ∈ v ∶ ∃r ∈ R k ∶ k i = r(k i−1), k i ∈ K, n ≤ l}
    - entspricht (Teil-)Pfaden der maximalen Länge l , ausgehend von k 0 im Spielgraph
  - Kanten E = {((k 0 , ... , k i ), (k 0 , ... , k i , k i+1 )) ∣ ∃r ∈ R k ∶ r(k i) = k i+1 }
- praktisch in Implementierung genutzt

## MinMax

### Geschichte

- populär als Suchalgorithmus für Spielstrategie in Schachprogrammen
- 1912 erstmals von Ernst Zermelo erwähnt
- 1940er Bedeutung betont (Shannon, Turing)

### wir brauchen…

- Nutzenfunktion f: F → N, die Endkonfigurationen mit einer Zahl bewertet (der Einfachheut natürliche Zahlen)
- zwei Typen von Knoten: Min- und Max- je nach dem wer am Zug ist, je nach dem ob Minimiert oder Maximiert werden soll
  - [Grafik: Bildsprache Min-/ Max-Knoten]
  - bei uns: alterniert mit jedem Halbzug

### Algo

- jedem Knoten wird ein MinMax-Wert zugeordnet
  - entspricht Nutzen dieser Konfiguration für Max
  - der MinMax-Wert eines Knoten ergibt sich immer aus seinen Kindern je nach dem, ob es ein Min- oder Max-Knoten gibt, bzw. entspricht f(k), wenn k Endkonf.
    - Min-Knoten: Annahme: Min ist an der Reihe und spielt optimal, d.h. Annahme 2: bester Zug für Min ist schlechtester für Max → kleinster Wert für Max anzunehmen (so zu sagen worst case)
    - Max-Knoten: Max ist dran und nimmt besten Zug → höchster Wert
  - schließlich: es wird Zug gewählt, der Max' Nutzen maximiert, bzw, der Zug zum Kindknoten mit dem selben Wert, wie dem aktuellen Wurzelknoten
- [Formel: MinMax, S. 209]
- [Grafik: MinMax TicTacToe]
- [Grafik: abstrakter: dreieckige Min-/Max-Knoten]
- Rekursion
  - steigt Spielpfade entlang des Baumes bis zu Blättern ab
  - Knoten werden quasi inialisiert: 
    -∞ für Max- und ∞ für Min-Knoten
  - entspricht vollständiger Tiefensuche
  - Aufstieg: tatsächliche Werte werden, basierend auf Kindern gespeichert

### Komplexität

- m maximale Tiefe des Baumes
- b Verzweigungsgrad an jedem Knoten (mögliche Züge)
- Zeit: O(b^m)
- Speicher: O(bm) wenn alles gleiczeitig gehalten wird, O(m), wenn nacheinander, d.h. für einen Pfad
- Problem: Baumsuche: Anzahl möglicher Spielstände wächst exponentiell zur Anzahl Züge, real nicht brauchbar

## Alpha-Beta

- Pruning = Kürzung, um weniger Konfigurationen zu durchsuchen
  - gibt das selbe Ergebnis wie std-Min-Max zurück
  - Konfigurationen, die Entscheidung nicht beeinflussen, werden nicht betrachtet

### Geschichte

- Optimierung von Minmax
- 1956 in Dartmouth vorgestellt (McCarthy)
- 1961 erstmals in Schachprogramm eingesetzt
- 1975 Korrektheit bewiesen (Knuth, Moore)
- weitere Verbesserungen u. Varianten
  folgten

### Wie brauchen…

- da tiefensuche, stats pfadweise Betrachtung, pro Pfad muss alpha/beta-Par gehalten werden
  - praktisch bekommet jeder Knoten einen alpha/ beta Wert
  - alpha: bisher bester wert im pfad
  - beta: bisher schlechtester wert im pfad

### Algo

- alpha und beta werden nach jeder untersuchten konfiguration aktualisert
  - initial wird alpha = -ue und beta = +ue gesetzt iwe minmax
  - alpha wird max-knoten maximiert: max(a-1, v)
  - beta wird an minknoten minimiert: min(b-1,v)
- kinder werden gekürzt = rekursion vor erreichen der endkonf. terminiert
  - an Max-Knoten k: α-Cut: es wird der größte Wert der Kinder gesucht
    - sobald β eines Kindes kleiner gleich als α des k
  - an Min-Noten k: β-Cut: es wird der kleinste Wert der Kinder gesucht
    - sobald α eines Kindes größer gleich als β des k

### Komplexität

- Minmax: O(b^m)
- AlphaBetta O(b^(m/2) bei optimaler Sortierung (Heuristik)
  - d.h. in gleicher zeit doppelt so tiefen baum
  - theoretisches limit, wenn immer sofort gekürzt werden kann
- Zufällige Sortierung der Kinde: etwa O(b^(3/4m))

## unvollständige Echtzeitentscheidung

### verbesserung

- kriteirum, wann nicht mehr weiter expandieren
- zusätzlich zu Nutzenfunktion für Endkonf.: Bewertungsfunktion für Konfs, die nicht Endkonf sind (Heuristik)
  - vergleichbar mit Kostenschätzung bei A* Suche
- mit alphabeta kombinierbar
- stärke des agenten hängt von genauigkeit der Heuristik und bewertung des nutztens einer knotenexpansion ab

### Heuristik

- schätzt den nutzen für spieler in bestimmter konfiguration ab
- anforderungen
  - muss mindestens gleiche ordnung, wie nutzenfunktion erzeugen
  - muss schnell sein
  - sollte bei nicht endzuständen nah an tatsächlichen chancen sein (chance, auch wenn kein zufallsspiel da information durch tiefenbeschränkung unvollständig wird)
- setzt sich aus verschiedenen merkmalen des zustands zusammen, die sich numerisch beschreiben lassen
- [bsp tictactoe]

### wann abbrechen

- immer wenn endkonf erreicht ist natürlich
- bspw. feste tiefe → problematisch (bsp. schach)
  - mindest suchtiefe, dann abbrechen, wenn bewertung ruht
- metaschließen: wann lohnt es sich eine berechnung anzustellen?

## Ausblick

- Min spielt nicht optimal

  - MinMax nur beste Strategie, wenn Min wirklich optimal spielt
  - wenn nicht: dann noch besser für Max
  - aber: es gibt dann ggf. noch bessere Strategien

- Nicht-nullsummenspiel

  - jeder spieler hat eigene nutzenfunktion (die beiden bekannt ist)

- mehr als 2 spieler

  - nicht Min-/Max-Halbzüge, sondern A,B,C,…
  - nicht ein MinMaxVal sondern Vektor mit Max-Werte jedes Spielers
  - warum kein vektor bei 2? 
    - nullsummenspiel gewinn ist verlust des anderen
    - negamax
  - Problem: Spielstrategie mit Allianzen

- zugreihenfolge

  - vorsortieren und Killerzüge finden ("Killerzugheuristik")
  - dynamisch: zuerst züge probieren, die vorher gut waren
    - situationen können wieder auftreten → hashtabelle (allerdings für alle knoten wiederum zu aufwendig) für interessante konf.s

- verbesserung mit iterativer tiefensuche

  - hilft bei sortierung der zugreihenfolge
  - begrenzte zeit möglichst gut ausnutzen

- Spiele mit Zufall

  - dritte art knoten: zufallsknoten
  - erwartungswert statt minmax-wert

- "Maximin" in der Philosophie

  - US-amerikanischer Philosoph: John Rawles: "A Theory of Justice", 1971, belebte politische Philosophie wieder

  - Gedankenexperiment zur Schaffung eines neuen Gesellschaftsvertrages

    - Vertragspartner  befinden sich in hypotetischem Urzustand hinter Schleier des Nichtwissens und entscheiden pber Gerechtigkeitsprinzipien
    - wissen nicht, wo/wer sie sein werden

  - Grundsätze der Gerechtigkeit

  - 1) Jeder Mensch hat das gleiche Recht auf das umfangreichste Gesamtsystem gleicher Grundfreiheiten, das für alle möglich ist.

  - 2) Soziale und wirtschaftliche Ungleichheiten müssen folgendermaßen beschaffen sein:

    - (a) sie müssen unter der Einschränkung des gerechten Spargrundsatzes den am wenigsten Begünstigten den größtmöglichen Vorteil bieten, und

      (b) sie müssen mit Ämtern und Positionen verbunden sein, die allen gemäß fairer Chancengleichheit offen stehen.

  - Vorrang von 1 vor 2 sowie ein Vorrang des Prinzips fairer Chancengleichheit (b) vor dem Differenzprinzip (a)

  - MaxiMin bedeutet nun, dass die entscheidende Person sich für die Alternative entscheidet, die das minimale denkbare Ergebnis maximiert. Rawls wählt damit das konservative Prinzip der Risikominimierung. (Risikoaversion)

## Anhang

- Github-Link
- Quellen