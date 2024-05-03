<!--
author:   Steffen Rörtgen

email:    kontakt@steffen-roertgen.de

version:  0.0.1

language: de

comment:  Anlegen eines kontrollierten Wertebereiches mit SKOS und Veröffentlichung mit SkoHub

-->

# Kontrollierte Wertebereiche mit SKOS und SkoHub

Wir wollen heute lernen, wie Du einen kontrollierten Wertebereich mit SKOS anlegen und mit SkoHub Vocabs veröffentlichen kannst.

Dabei gehen wir ganz praktisch vor und behandeln Hintergrundinformationen zu den Themen Linked Data und Resource Description Framework nur (Technologien, auf denen SKOS aufbaut), wenn sie relevant sind.

SKOS, das Simple Knowledge Organization System, ist ein W3C-Standard, um kontrollierte Wertebereiche (auch "Vokabulare" genannt), abzubilden.
SKOS bietet verschiedene Möglichkeiten, um diese Wertebereiche mit anderen Wertebereichen zu verlinken und in Beziehung zu setzen.
Durch diese Möglichkeiten der standardkonformen Abbildung sowie der Verlinkungsmöglichkeiten sind SKOS-Vokabulare hoch interoperabel.
Sie lassen sich leicht in einer Vielzahl von Anwendungen nachnutzen und gewährleisten damit auch eine nachhaltige Nutzung.

Falls du dich mit dem Thema Vokabulare noch etwas vertraut machen möchtest, bevor du praktisch einsteigst, lies dich gerne im ["Kompendium: Didaktische Metadaten"](w3id.org/kim/kompendium/#/3_technische_grundlagen?id=_33-was-sind-vokabulare) noch etwas in das Thema ein.

Schau dir gerne auch diese [Einführung in SKOS](https://dini-ag-kim.github.io/skos-einfuehrung/#/) an, die im Rahmen der DINI-AG-KIM entstanden ist.

---

Beispiel Vokabulare:

- [Hochschulfächersystematik](https://w3id.org/kim/hochschulfaechersystematik/scheme)
- [Interdisziplinäre Forschungsfeldklassifikation](https://w3id.org/kdsf-ffk/)


# Deine Erste Werteliste

Öffnet einen Text-Editor eurer Wahl und erstellt eine Werteliste

- Maximal fünf Deskriptoren
- Nehmt ein Beispiel aus eurer täglichen Arbeit oder erstellt eine Tier-Taxonomie, Fahrzeuge, Getränkesorten,...

Beispiel:

```
- blau
- grün
- gelb
- orange
- lila
```

## Ergänzt Definition

```
- blau
  Definition: Blau ist der Farbreiz, der wahrgenommen wird, wenn Licht mit einer spektralen Verteilung ins Auge fällt, bei der Wellenlängen im Intervall zwischen 460 und 490 nm dominieren.
  Beispiel: ...
- grün
- gelb
- orange
- lila
```

# URIs und Namespaces

"SKOS", das "Simple Knowledge Organization System" baut auf "RDF", dem "Resource Description Framework" auf. Diese Technologien nutzen URIs [Uniform Resource Identifier](https://de.wikipedia.org/wiki/Uniform_Resource_Identifier), um Dinge eindeutig zu referenzieren.

Diese URIs lösen im besten Fall später auf, sodass sowohl Menschen, als auch Maschinen, diese URIs besuchen und Informationen zu dem Vokabular abrufen können.

Eine URI sieht wie eine URL aus:

`https://example.org/`

Im Gegensatz zu einer URI sollte eine URL immer auflösen. Das ist für SKOS nicht zwingend erforderlich, wird aber empfohlen.

Später werden wir sehen, wie auch deine URIs korrekt auflösen werden.

## RDF besteht aus Triples

Aussagen über eine Ressource werden in RDF in der Form von Triples getroffen: Subjekt - Prädikat - Objekt.

- **Subjekt**: Ressource über die eine Aussage getroffen wird
- **Prädikat**: Beziehung zwischen Subjekt und Objekt
- **Objekt**: Literal oder eine andere ressource

💡 Merken:

- **Subjekt** und **Prädikat** werden *immer* über eine URI identifiziert
- **Objekt** ist eine URI *oder* ein Literal (String mit einem Datentyp oder einem Language Tag)

Hier seht ihr zwei exemplarische Aussagen mit RDF-Triples.
In der ersten Aussage wird ein "Literal" mit einem "Language Tag verwendet, um auszusagen, dass der Name des Hundes im Deutschen "Lica" ist.
In der zweiten Aussage wird eine URI genutzt, die auf einen Eintrag bei Wikidata verweist.
Rufe den Eintrag gerne einmal auf und schaue ihn dir an.

| Subjekt | Prädikat | Objekt |
| -------- | -------- | -------- |
| Mein Hund     | heißt     | Lica.     |
| `<http://example.org/myDog>` | `<https://schema.org/name>`  | `"Lica"@de` . |
| Mein Hund | ist interessiert an | Apportieren. |
| `<http://example.org/myDog>` | `<http://xmlns.com/foaf/0.1/interest>`  | `<http://www.wikidata.org/entity/Q621694>` . |

## Namespaces und Präfixe

Zur Darstellung der Triples können verschiedene Formate genutzt werden.
Wir nutzen hier die ["Turtle"-Syntax](https://www.w3.org/TR/rdf12-turtle/), da sie sich gut lesen und schreiben lässt.

Damit wir nicht lange URIs schreiben müssen, können wir Präfixe benutzen und die URI etwas abkürzen:

`<http://example.org/myDog>` wird zu

```
@prefix ex: <http://example.org/> .

ex:myDog
```

## Unser erstes Triple

Auch die anderen URIs lassen sich abkürzen.
Jetzt definieren wir `sdo` als Präfix für `https://schema.org/`.

```turtle
@prefix ex: <http://example.org/> .
@prefix sdo: <https://schema.org/> .

ex:myDog sdo:name
```

Fehlt nur noch der eigentliche Name "Lica" zu unserem ersten Triple.
Diesen können wir als Literal an die dritte Stelle setzen:

```turtle
@prefix ex: <http://example.org/> .
@prefix sdo: <https://schema.org/> .

ex:myDog sdo:name "Lica"@de .
```

Wie bei einem regulären Satz steht hier am Ende ein `.`, um zu kennzeichnen, dass die Aussage beendet ist.

Wollten wir ein zweites Literal ergänzen, möglicherweise in einer anderen Sprache, können wir dies mit einem Komma tun:

```turtle
@prefix ex: <http://example.org/> .
@prefix sdo: <https://schema.org/> .

ex:myDog sdo:name "Lica"@de, "Lica"@en .
```

## Mehrere Aussagen über ein Subjekt treffen

Wenn wir mehrere Aussagen über ein Subjekt, also bspw `ex:myDog` treffen wollen, nutzen wir das Semikolon `;`.
Aus dem vorherigen Beispiel wollen wir nun ergänzen, dass `ex:myDog` Interesse am Apportieren besitzt:

```turtle
@prefix ex: <http://example.org/> .
@prefix sdo: <https://schema.org/> .

ex:myDog sdo:name "Lica"@de, "Lica"@en ;
  <http://xmlns.com/foaf/0.1/interest> <http://www.wikidata.org/entity/Q621694> .
```

Wie ihr seht, haben wir jetzt direkt die URIs benutzt.
URIs werden, wie bei den Präfixen auch, in Angle Brackets gesetzt `<meine_uri>`.
Das ist völlig valide, aber bisweilen schwierig zu lesen oder auch viel zu schreiben.

Nutze `foaf` für http://xmlns.com/foaf/0.1/ und `wikidata` für `http://www.wikidata.org/entity/`

Bevor du dir die Lösung anschaust, probiere einmal die Präfixe hier einzutippen:

foaf:

[[@prefix foaf: <http://xmlns.com/foaf/0.1/> .]]

wikidata:

[[@prefix wikidata: <http://www.wikidata.org/entity/> .]]
***
```turtle
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix wikidata: <http://www.wikidata.org/entity/> .
```
***

## SKOS-Präfix

Auch für SKOS können wir ein entsprechendes Präfix setzen:

- `@prefix skos: <http://www.w3.org/2004/02/skos/core#> .`
- Alle Properties und Klassen des SKOS-Modells sind in diesem Namespace definiert


## SKOS Attribute

SKOS-Properties für die bisher erstellten Informationen:

- `prefLabel`: Vorzugsbezeichnung. Nur einmal pro Sprache
- `altLabel`: Alternativbezeichnung. Mehrere Angaben pro Sprache möglich
- `definition`: Definition des Begriffs
- `example`: Angabe von Beispielen

```turtle
@prefix ex: <http://example.org/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .

ex:blau skos:prefLabel "blau" ;
  skos:definition "Meine Definition"@de ;
  skos:example "..."@de ;
  .
- grün
- gelb
- orange
- lila
```

```turtle
@prefix ex: <http://example.org/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .

ex:blau skos:prefLabel "blau" ;
  skos:definition "Meine Definition"@de ;
  skos:example "..."@de ;
  .
- grün
- gelb
- orange
- lila
```

Ergänze nun die SKOS-Attribute für deine Werteliste.


# Bestandteile eines SKOS-Vokabulars

Ein SKOS Vokabular besteht aus zwei wesentlichen Bestandteilen:

- `skos:ConceptScheme`:

  - allgemeine Informationen wie Titel, Lizenz, Beschreibung des Gesamt-Vokabulars
  - gedankliche Klammer, um das Vokabular

- `skos:Concept`:

  - *Ein* Eintrag im Vokabular
  - Vorzugbezeichnung, Alternativbezeichnung, Definition, Beziehungen usw.

## skos:ConceptScheme

```turtle
@prefix ex: <http://example.org/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix dct: <http://purl.org/dc/terms/> .

ex: a skos:ConceptScheme ;
  dct:title "Meine Farben"@de ;
  dct:license <http://creativecommons.org/publicdomain/zero/1.0/> ;
  .

ex:blau a skos:Concept ;
  skos:prefLabel "blau"@de ;
  skos:definition "Meine Definition"@de ;
  .
```

## skos:Concept

jeder Vokabulareintrag muss als `skos:Concept` deklariert werden:

```turtle
@prefix ex: ...
@prefix skos: ...

ex:blau a skos:Concept ;
  skos:prefLabel "blau"@de ;
  skos:definition "Meine Definition"@de ;
  skos:example: "Ein Beispiel"@de ;
  skos:narrower ex:hellblau .
```

## Verbindung von `skos:ConceptScheme` und `skos:Concept`

Nun müssen wir noch Verlinkungen zwischen den einzelnen SKOS-Konzepten und dem SKOS-Konzept-Schema herstellen.
Dabei gibt es eine Best-Practice zu beachten:

Die Top-Level-Einträge eines Vokabulars werden mit `skos:topConceptOf` verlinkt.
Darunter liegende Einträge werden mit `skos:inScheme` verlinkt.

```turtle
@prefix ex: <http://example.org/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix dct: <http://purl.org/dc/terms/> .

ex: a skos:ConceptScheme ;
  dct:title "Meine Farben"@de ;
  dct:license <http://creativecommons.org/publicdomain/zero/1.0/> ;
  skos:hasTopConcept ex:blau ;
  .

ex:blau a skos:Concept ;
  skos:prefLabel "blau"@de ;
  skos:definition "Meine Definition"@de ;
  skos:example "Mein Beispiel"@de ;
  skos:narrower ex:hellblau ;
  skos:topConceptOf ex: ;
  .
  
ex:hellblau a skos:Concept ;
  skos:broader ex:blau ; # Inversion von `skos:narrower`
  skos:inScheme ex: .
```

# Validierung

Bevor wir nun das Vokabular publizieren, können wir es validieren, um zu überprüfen, ob unsere Syntax korrekt ist.
Dazu eignet sich der Webeditor von Felix Lohmeier:

- https://felixlohmeier.github.io/turtle-web-editor/

Es gibt auch Möglichkeiten zu überprüfen, ob das SKOS valide ist.
Die technisch versierten unter euch, können sich gerne dieses Repo anschauen und den dort beschreibenen Schritten folgen, um ihr Vokabular zu validieren: https://github.com/skohub-io/skohub-shapes/

Wenn das allerdings nichts für dich ist: Fear no more.
Es wird auch eine automatische Validierung im Rahmen der Publikation deines Vokabulars vorgenommen.

Und diesen Schritt schauen wir uns als nächstes an.

# Publikation

Um aus SKOS-Wertelisten schöne HTML-Seiten zu bauen, wird [SkoHub Vocabs](https://github.com/skohub-io/skohub-vocabs/) genutzt.
Für die leichtgewichtige Publikation von Vokabularen haben wir außerdem einen Workflow entwickelt, der es dir mit wenigen Klicks ermöglicht, dein Vokabular ohne eigene Infrastruktur zu publizieren.
Dazu nutzen wir GitHub und GitHub Pages.
Du benötigst für die folgenden Schritte einen Account auf https://www.github.com

Mit [SkoHub Pages](https://github.com/skohub-io/skohub-pages) bieten wir eine sehr einfache Möglichkeit, dein SKOS-Vokabular aus einem GitHub-Repository zu veröffentlichen.
Es umfasst nur 5-6 Schritte:

## 1. Forke das skohub-pages Repository

Klicke auf die Schaltfläche „Fork“ in der oberen rechten Ecke des [SkoHub Pages Repositories](https://github.com/skohub-io/skohub-pages). 
Du kannst den Namen des Forks nach deinen Wünschen ändern, z.B. `my-shiny-vocab`.
Siehe auch die [GitHub Fork Dokumentation](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo#forking-a-repository).

⚠️ **Wichtig**: Deaktiviere das Kästchen "Copy the main branch only"

![Fork erstellen, Kästchen ⚠️ deaktivieren, um auch den `gh-pages`-Zweig zu forken](https://blog.skohub.io/static/b5beefdabbe43f74578e4af6474e861c/eb390/create_fork.png)

## 2. Aktiviere GitHub-Actions

Klicke nun auf den "Actions" Tab und aktiviere GitHub Actions, falls sie noch nicht aktiviert sind.

![Gehe zum Reiter „Aktionen“ und aktiviere GitHub-Actions, falls noch nicht aktiviert](https://blog.skohub.io/static/4bae802167f80dcb59a36f4d3ec553ed/ecf19/activate_action.png)

## 3. Konfiguriere den GitHub Pages Branch

Gehe zu „Einstellungen“, navigiere zu den „Pages“-Einstellungen und wählen Sie „gh-pages“ als den Branch, von dem aus die Webseite für dein Vokabular erstellt wird.

![gh-pages Branch einstellen](https://blog.skohub.io/static/ffc58a76b23bb9ae2b5cd91d14bfe5e6/302a4/set_gh_pages.png)

## 4. Seiten-URL aktualisieren

Gehe zurück zur Hauptseite deines Repos und klicke auf das kleine Zahnradsymbol oben rechts im Abschnitt „Über“. Aktiviere das Kästchen bei „Use your GitHub Pages website“.

![Über"-Abschnitt des Repositorys bearbeiten](https://blog.skohub.io/static/b6a6a99907ce6e939473565adc2a5be3/0098c/click_gear_icon.png)

![URL des Repositorys festlegen](https://blog.skohub.io/static/34c8419d626f86c2e8a6f15ce75998a4/f0685/use_gh_pages_website.png)

## 5. Commit

Jetzt kannst du einen Commit im main-Branch vornehmen, indem du dein Beispielvokabular anpasst oder eine neue Turtle-Datei hinzufügst.
Die Änderungen werden automatisch auf Deiner GitHub-Webseite veröffentlicht, die nun oben rechts in deinem GitHub-Repository verlinkt ist (manchmal dauert es ein wenig, bis die Änderungen sichtbar sind, denk daran, regelmäßig zu refreshen).
Als Beispiel kannst du dir auch die beiden kleinen Beispielvokabulare im skohub-pages Repo ansehen, die unter https://skohub-io.github.io/skohub-pages/ erstellt wurden.

## 6. Setzen Sie Ihre GitHub Pages URL als Namespace (optional)

Siehe Abschnitt „Auflösen von benutzerdefinierten Domains“ unten ⬇️

Obwohl mit dem vorgestellten Ansatz das benutzerdefinierte Vokabular ohne eigene Infrastruktur bereitgestellt werden konnte, lösen die Domains nicht zu den GitHub-Seiten auf.
Das bedeutet, dass ein `skos:ConceptScheme`, das URIs verwendet, die auf der GitHub Pages Domain basieren (z.B. `https://myhandle.github.io/skohub-pages/`), bisher nicht aufgelöst werden konnte.
In der Vergangenheit haben wir empfohlen, einen Redirect über [w3id](https://w3id.org/) oder [purl.org](https://purl.archive.org/) einzurichten, um eine Auflösung zu ermöglichen.
Natürlich ist es nach wie vor sinnvoll, eine Weiterleitung einzurichten (für den Fall, dass das Vokabular woanders hingeht).
Es ist nun jedoch auch möglich, die über GitHub Pages zugewiesene Domain zu verwenden und schnell ein voll funktionsfähiges SKOS-Vokabular mit auflösenden Konzept-URIs einzurichten, was für die Prototypenerstellung sehr nützlich sein kann.

Dazu muss im Repo eine [`config.yaml`](https://github.com/skohub-io/skohub-pages/blob/main/config.yaml) angelegt werden.
Unter `custom_domain` muss dann die entsprechende Domain eingetragen werden.
Beispiel: Deine GitHub Pages Domain ist https://myhandle.github.io/skohub-pages/. Dann gib `https://myhandle.github.io/skohub-pages/` als `custom_domain` in der config.yaml an.

```yaml
#config.yaml
---
# [...]
custom_domain: "https://myhandle.github.io/skohub-pages/"
#[...]
```

```turtle
# colors.ttl
@prefix colour: <https://myhandle.github.io/skohub-pages/myColourVocab/> .
@prefix dct: <http://purl.org/dc/terms/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

colour: a skos:ConceptScheme ;
    dct:title "Colour Vocabulary"@en, "Farbvokabular"@de ;
    dct:creator "Hans Dampf"@de ;
    dct:created "2021-11-02"^^xsd:date ;
    dct:license <https://creativecommons.org/publicdomain/zero/1.0/> ;
    skos:hasTopConcept colour:violet, colour:blue .

colour:violet a skos:Concept ;
    skos:prefLabel "Violett"@de, "violet"@en;
    skos:altLabel "Lila"@de, "purple"@en ;
    skos:topConceptOf colour: .

colour:blue a skos:Concept ;
    skos:prefLabel "Blau"@de, "blue"@en ;
    skos:topConceptOf colour: .
```

# Links

- [SKOS Einführung DINI AG KIM](https://dini-ag-kim.github.io/skos-einfuehrung/#/)
- [Workshop: Einführung in SKOS](https://pad.gwdg.de/s/OCbQBibi2#)
- [SWIB22](https://pad.gwdg.de/2022-11-30-swib22-skos-workshop-slides?both#)
- [SkoHub-Slides eines SKOS-Workshops von 30. November 2022](https://pad.gwdg.de/p/2022-11-30-swib22-skos-workshop-slides)
- Anleitung zur Konfiguration von Perma-URIs mit W3ID oder purl.org: https://git.io/JPWsI
