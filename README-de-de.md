# Einleitung

Das Ziel dieses Style Guides ist, eine Sammlung von Best Practices und Gestaltungsrichtlinien für AngularJS-Anwendungen aufzuzeigen.
Sie wurden aus den folgenden Quellen zusammengestellt:

0. AngularJS-Quelltext
0. Quelltexte oder Artikel, die ich gelesen habe
0. Meine eigene Erfahrung

**Hinweis:** Hierbei handelt es sich noch um einen Entwurf des Style Guides, dessen vorrangiges Ziel es ist, gemeinschaftlich von der Community entwickelt zu werden. Die gesamte Community wird es daher begrüßen, wenn Lücken gefüllt werden.

Du wirst in diesem Style Guide keine allgemeinen Richtlinien für die JavaScript-Entwicklung finden. Solche finden sich unter:

0. [Googles JavaScript-Style-Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
0. [Mozillas JavaScript-Style-Guide](https://developer.mozilla.org/en-US/docs/Developer_Guide/Coding_Style)
0. [GitHubs JavaScript-Style-Guide](https://github.com/styleguide/javascript)
0. [Douglas Crockfords JavaScript-Style-Guide](http://javascript.crockford.com/code.html)
0. [Airbnb JavaScript-Style-Guide](https://github.com/airbnb/javascript)

Für die AngularJS-Entwicklung ist [Googles JavaScript-Style-Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml) empfehlenswert.

Im GitHub-Wiki von AngularJS gibt es einen ähnlichen Abschnitt von [ProLoser](https://github.com/ProLoser), den du dir [hier](https://github.com/angular/angular.js/wiki) ansehen kannst.

# Inhaltsverzeichnis
* [Allgemein](#allgemein)
    * [Verzeichnisstruktur](#verzeichnisstruktur)
    * [Optimieren des Digest-Zyklus](#optimieren-des-digest-zyklus)
    * [Sonstiges](#sonstiges)
* [Module](#module)
* [Controller](#controller)
* [Direktiven](#direktiven)
* [Filter](#filter)
* [Services](#services)
* [Templates](#templates)
* [Routing](#routing)
* [Testen](#testen)
* [Mitmachen](#mitmachen)

# Allgemein

## Verzeichnisstruktur

Da eine große AngularJS-Anwendung viele Komponenten hat, sollten diese mit Hilfe einer Verzeichnishierarchie strukturiert werden.
Es gibt zwei Basis-Herangehensweisen:

* Auf einer oberen Ebene eine Aufteilung nach Art der Komponenten und auf einer tieferen Ebene eine Aufteilung nach Funktionalität.

Die Verzeichnisstruktur wird in diesem Fall folgendermaßen aussehen:

```
.
├── app
│   ├── app.js
│   ├── controllers
│   │   ├── page1
│   │   │   ├── FirstCtrl.js
│   │   │   └── SecondCtrl.js
│   │   └── page2
│   │       └── ThirdCtrl.js
│   ├── directives
│   │   ├── page1
│   │   │   └── directive1.js
│   │   └── page2
│   │       ├── directive2.js
│   │       └── directive3.js
│   ├── filters
│   │   ├── page1
│   │   └── page2
│   └── services
│       ├── CommonService.js
│       ├── cache
│       │   ├── Cache1.js
│       │   └── Cache2.js
│       └── models
│           ├── Model1.js
│           └── Model2.js
├── lib
└── test
```

* Auf einer oberen Ebene eine Aufteilung nach Funktionalität und auf einer tieferen Ebene eine Aufteilung nach Art der Komponenten.

Hier ist das entsprechende Layout:

```
.
├── app
│   ├── app.js
│   ├── common
│   │   ├── controllers
│   │   ├── directives
│   │   ├── filters
│   │   └── services
│   ├── page1
│   │   ├── controllers
│   │   │   ├── FirstCtrl.js
│   │   │   └── SecondCtrl.js
│   │   ├── directives
│   │   │   └── directive1.js
│   │   ├── filters
│   │   │   ├── filter1.js
│   │   │   └── filter2.js
│   │   └── services
│   │       ├── service1.js
│   │       └── service2.js
│   └── page2
│       ├── controllers
│       │   └── ThirdCtrl.js
│       ├── directives
│       │   ├── directive2.js
│       │   └── directive3.js
│       ├── filters
│       │   └── filter3.js
│       └── services
│           └── service3.js
├── lib
└── test
```

* Wenn eine Direktive erstellt wird, kann es sinnvoll sein, alle der Direktive zugehörigen Dateien (d. h. Templates, CSS/SASS-Dateien, JavaScript) in das selbe Verzeichnis zu legen. Wenn du dich für diesen Stil entscheidest, setze ihn konsequent im gesamten Projekt um.

```
app
└── directives
    ├── directive1
    │   ├── directive1.html
    │   ├── directive1.js
    │   └── directive1.sass
    └── directive2
        ├── directive2.html
        ├── directive2.js
            └── directive2.sass
```

Dieser Ansatz kann mit beiden der oben genannten Verzeichnisstrukturen kombiniert werden.
* Eine weitere kleine Variation der beiden Verzeichnisstrukturen wird in [ng-boilerplate](http://joshdmiller.github.io/ng-boilerplate/#/home) eingesetzt. In dieser liegen die Unit Tests zu einer Komponente direkt im Verzeichnis der jeweiligen Komponente. Werden Änderungen an einer Komponente vorgenommen, ist es auf diese Weise einfacher, ihre Tests zu finden. Gleichzeitig dienen die Tests als Dokumentation und zeigen Anwendungsfälle auf.

```
services
├── cache
│   ├── cache1.js
│   └── cache1.spec.js
└── models
    ├── model1.js
    └── model1.spec.js
```

* Die Datei `app.js` enthält die Routendefinitionen, die Konfiguration und/oder das manuelle Bootstrapping (falls benötigt).
* Jede JavaScript-Datei sollte nur eine einzige Komponente enthalten. Die Datei sollte nach dem Namen der Komponente benannt sein.
* Verwende Angular-Projektstrukturvorlagen wie [Yeoman](http://yeoman.io) oder [ng-boilerplate](http://joshdmiller.github.io/ng-boilerplate/#/home).

Ich bevorzuge die erste Struktur, weil bei ihr die üblichen Komponenten einfacher gefunden werden können.

Konventionen über die Benennung der Komponenten stehen in jedem Abschnitt über die jeweilige Komponente.

## Optimieren des Digest-Zyklus

* Watche nur auf die vitalsten Variablen (Beispiel: Wenn du eine Echtzeitkommunikation einsetzt, sollte nicht bei jeder eingehenden Nachricht ein Digest-Loop ausgelöst werden).
* Vereinfache Berechnungen in `$watch` so weit wie möglich. Komplexe und langsame Berechnungen in einem einzigen `$watch` verlangsamen die gesamte Applikation (der $digest-Loop wird in einem einzelnen Thread ausgeführt, weil JavaScript single-threaded ist).
* Falls in der Callback-Funktion von `$timeout` keine gewatchten Variablen geändert werden, setze den dritten Parameter der `$timeout`-Funktion auf `false`, um nicht automatisch einen Digest-Zyklus durch den Aufruf des Callbacks auszulösen.

## Sonstiges

* Verwende:
    * `$timeout` statt `setTimeout`
    * `$interval` statt `setInterval`
    * `$window` statt `window`
    * `$document` statt `document`
    * `$http` statt `$.ajax`

Dadurch werden deine Tests einfacher und in manchen Fällen wird einem unerwarteten Verhalten vorgebeugt (zum Beispiel wenn du ein `$scope.$apply()` in `setTimeout` vergessen hast).

* Automatisiere deinen Workflow mit Tools wie:
    * [Yeoman](http://yeoman.io)
    * [Grunt](http://gruntjs.com)
    * [Bower](http://bower.io)

* Verwende Promises (`$q`) statt Callbacks. Dadurch sieht dein Code eleganter und sauberer aus und du wirst nicht in der Callback-Hölle landen.
* Verwende, wenn möglich, `$resource` statt `$http`. Das höhere Abstraktionslevel schützt dich vor Redundanz.
* Verwende einen Angular Pre-Minifier (wie [ngmin](https://github.com/btford/ngmin) oder [ng-annotate](https://github.com/olov/ng-annotate)), um Probleme nach einer Minification zu vermeiden.
* Verwende keine Globalen. Löse alle Abhängigkeiten durch Dependency Injection auf.
* Mülle deinen `$scope` nicht zu. Füge ihm nur Funktionen und Variablen hinzu, die in den Templates verwendet werden.
* Bevorzuge [Controller gegenüber `ngInit`](https://github.com/angular/angular.js/pull/4366/files). `ngInit` eignet sich nur, um Aliase für spezielle Eigenschaften von `ngRepeat` zu erstellen. Hiervon abgesehen solltest du immer Controller statt `ngInit` verwenden, um Werte in einem Scope zu initialisieren.
* Verwende kein `$` als Präfix für die Namen von Variablen, Eigenschaften oder Methoden. Dieser Präfix ist für AngularJS reserviert.

# Module

Es gibt zwei verbreitete Wege, nach denen Module strukturiert werden können:

0. Nach Funktionalität
0. Nach Typ der Komponente

Derzeit gibt es keinen großen Unterschied, aber die erste Variante sieht sauberer aus. Außerdem wird - wenn lazy-loading für die Module implementiert ist (momentan nicht auf der AngularJS-Roadmap) - die Performance der App verbessert.

# Controller

* Du solltest das DOM nicht aus deinen Controllern heraus manipulieren, dadurch wird das Testen der Controller erschwert und du verstößt gegen das [Prinzip der Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns). Verwende stattdessen Direktiven.
* Controller sollen nach ihrer Funktion (zum Beispiel shopping cart, homepage, admin panel) und dem Suffix `Ctrl` benannt werden. Controller werden in UpperCamelCase benannt (`HomePageCtrl`, `ShoppingCartCtrl`, `AdminPanelCtrl` usw.).
* Controller sollten nicht als Globale definiert werden (AngularJS erlaubt das zwar, es ist jedoch schlechte Praxis den globalen Namensraum zu verschmutzen).
* Verwende für Controller-Definitionen die Array-Syntax:

```JavaScript
module.controller('MyCtrl', ['dependency1', 'dependency2', ..., 'dependencyn', function(dependency1, dependency2, ..., dependencyn) {
  // body
}]);
```

Durch die Verwendung dieses Definitionstyps werden Probleme bei der Minification vermieden. Die Array-Definition kann aus der Standardnotation automatisch generiert werden, indem Werkzeuge wie [ng-annotate](https://github.com/olov/ng-annotate) (und der Grunt-Task [grunt-ng-annotate](https://github.com/mzgol/grunt-ng-annotate)) verwendet werden.
* Verwende die Originalnamen der im Controller verwendeten Abhängigkeiten. Dies hilft dir dabei, lesbareren Code zu schreiben:

```JavaScript
module.controller('MyCtrl', ['$scope', function(s) {
  // body
}]);
```

ist schlechter lesbar als:

```JavaScript
module.controller('MyCtrl', ['$scope', function($scope) {
  // body
}]);
```

Das gilt insbesondere für Dateien, die so viel Code enthalten, dass gescrollt werden muss. Dadurch vergisst du möglicherweise, welche Variable zu welcher Abhängigkeit gehört.

* Halte Controller so schlank wie möglich und lagere mehrfach verwendete Funktionen in Services aus.
* Kommuniziere zwischen verschiedenen Controllern, indem du Method Invocation nutzt (das ist möglich, wenn ein Kindcontroller mit seinem Elterncontroller kommunizieren möchte) oder die `$emit`-, `$broadcast`- und `$on`-Methoden verwendest. Über `$emit` und `$broadcast` gesendete Nachrichten sollten auf ein Minimum reduziert werden.
* Erstelle eine Liste aller Nachrichten, die über `$emit` und `$broadcast` verschickt werden. Pflege diese Liste, um Kollisionen und Bugs zu vermeiden.
* Wenn du Daten formatieren musst, kapsle die Formatierungslogik in einem [Filter](#filter) und gebe diesen als Abhängigkeit an:

```JavaScript
module.filter('myFormat', function() {
  return function() {
    // body
  };
});

module.controller('MyCtrl', ['$scope', 'myFormatFilter', function($scope, myFormatFilter) {
  // body
}]);
```

# Direktiven

* Benenne deine Direktiven in lowerCamelCase.
* Verwende `scope` statt `$scope` in deiner Link-Funktion. In den Compile- und Post-/Pre-Link-Funktionen hast du bereits Argumente angegeben, die verwendet werden sobald die Funktion aufgerufen wird. Diese kannst du nicht über eine Dependency Injection ändern. Dieser Stil wird auch im AngularJS-Sourcecode verwendet.
* Verwende eigene Präfixe für deine Direktiven, um Namenskollisionen mit Bibliotheken von Drittanbietern zu vermeiden.
* Die Präfixe `ng` und `ui` solltest du nicht verwenden, da diese für AngularJS und AngularUI reserviert sind.
* DOM-Manipulationen dürfen ausschließlich über Direktiven vorgenommen werden.
* Verwende einen Isolated Scope, wenn du wiederverwendbare Komponenten entwickelst.
* Binde Direktiven über Attribute oder Elemente ein statt über Kommentare oder Klassen. Das macht deinen Code lesbarer.
* Verwende zum Aufräumen `$scope.$on('$destroy', fn)`. Dies ist besonders nützlich wenn du Wrapper-Direktiven für Drittanbieter-Plug-ins entwickelst.
* Vergiss nicht, `$sce` zu verwenden, wenn du mit Inhalten arbeitest, die nicht vertrauenswürdig sind.

# Filter

* Benenne deine Filter in lowerCamelCase.
* Halte deine Filter so schlank wie möglich. Durch die `$digest`-Schleife werden sie häufig aufgerufen, so dass langsame Filter die gesamte Anwendung verlangsamen.

# Services

* Benenne deine Services in (lower oder upper) camelCase.
* Kapsle Anwendungslogik in Services.
* Services, die eine bestimmte Domäne abbilden, sollten bevorzugt als `service` statt als `factory` geschrieben werden. Auf diese Weise können die Vorteile der klassischen Vererbung einfacher genutzt werden:

```JavaScript
function Human() {
  // body
}
Human.prototype.talk = function() {
  return "I'm talking";
};

function Developer() {
  // body
}
Developer.prototype = Object.create(Human.prototype);
Developer.prototype.code = function() {
  return "I'm coding";
};

myModule.service('Human', Human);
myModule.service('Developer', Developer);
```

* Für einen sitzungsbezogenen Cache kannst du `$cacheFactory` verwenden. Diesen solltest du nutzen, um die Ergebnisse von Anfragen oder aufwändigen Berechnungen zwischenzuspeichern.

# Templates

* Verwende `ng-bind` oder `ng-cloak` statt einfachen `{{ }}`, um flackernde Inhalte zu vermeiden.
* Vermeide es, komplexen Code in ein Template zu schreiben.
* Wenn das `src`-Attribut eines Bilds dynamisch gesetzt werden soll, verwende `ng-src` statt `src` mit einem `{{ }}`-Template.
* Statt in Scopevariablen Strings anzugeben und diese mit `{{ }}` in ein `style`-Attribut zu schreiben, benutze die `ng-style`-Direktive, der als Parameter objektartige Strings und Scopevariablen übergeben werden können:

```JavaScript
$scope.divStyle = {
  width: 200,
  position: 'relative'
};
```
```HTML
<div ng-style="divStyle">Mein wunderschön gestyltes div, das auch im IE funktioniert</div>;
```

# Routing

* Verwende `resolve`, um Abhängigkeiten aufzulösen bevor die View angezeigt wird.

#Testen

TBD

# Mitmachen

Da dieser Style Guide gemeinschaftlich durch die Community erstellt werden soll, sind Beiträge willkommen.
Zum Beispiel kannst du etwas beitragen, indem du den Abschnitt über Tests erweiterst oder den Style Guide in deine Sprache übersetzt.
