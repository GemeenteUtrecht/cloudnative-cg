# Cloudnative Common Ground

## Index
* [Inleiding](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#inleiding)
* [Architectuur principes](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#architectuur-principes)
* [Richtlijnen voor laag 5 componenten](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#richtlijnen-voor-laag-5-componenten)
* [Richtlijnen voor laag 4 componenten](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#richtlijnen-voor-laag-4-componenten)
* [API richtlijnen](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#api-richtlijnen)
* [Monitoring en logging richtlijnen](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#monitoring-en-logging-richtlijnen)
* [Component documentatie](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#component-documentatie)
* [Database architectuur](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#database-architectuur)
* [CI/CD](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#cicd)
* [Midden team functionaliteit](https://github.com/GemeenteUtrecht/cloudnative-cg/blob/main/README.md#midden-team-functionaliteit)

## Inleiding
Dit document beschrijft de architectuur richtlijnen voor in eerste instantie KiK (Klantgerichtwerken in Ketens), maar kan ook door andere Organistatie Onderdelen (OOs) worden toegepast.

De richtlijnen gelden voor programmatuur waarbij de gemeente controle heeft over de bron code, danwel in de toekomst mogelijk zelf het onderhoud van de programmatuur in eigen hand wil nemen.

De nadruk in dit document ligt op laag 4 en 5 van het Common Ground vijf lagen model.

Naast Common Ground is gekozen voor een Cloud Native benadering.
Cloud Native houdt in dit geval in dat gebruik wordt gemaakt van Azure Kubernetes (AKS) met Otomi voor het beheer.
Het streven is om zoveel mogelijk leverancier agnostisch te zijn, zodat bijvoorbeeld van Azure overgestapt kan worden naar een andere cloud provider.

Een ander uitgangspunt voor de architectuur is om te komen tot synergie en hergebruik tussen verschillende OOs.

Het platform bestaande uit Kubernetes op Azure (AKS) en Otomi is gevalideerd op basis van de Baseline Informatiebeveiliging Overheid (BIO) en ISO 27001.
In dit document wordt niet uitputtend ingegaan op beveiliging vraagstukken voor applicaties. Dit onderwerp rechtvaardigt een eigen document.

## Architectuur principes
* Common Ground principes.
* Cloud Native principes.
* Leverancier agnostisch waar mogelijk.
* Vereenvoudiging van taken voor ontwikkelaars en beheerders.
* Wens tot samenwerking met andere OOs.

### Common Ground principes
De Common Ground principes worden beschreven in: [Gemeentelijk gegevenslandschap Informatiearchitectuurprincipes](https://componentencatalogus.commonground.nl/20190130_-_Common_Ground_-_Informatiearchitectuurprincipes.pdf) van de VNG.

De belangrijkste punten zijn:
1. Autonomie - Componenten zijn zelfstandig inzetbaar, schaalbaar en vervangbaar.
1. Losse koppeling - Componenten zijn onderling onafhankelijk en zelfstandig door te ontwikkelen en gebruiken. Wanneer één component niet beschikbaar is, blijven andere componenten functioneren.
1. Herbruikbaar - Componenten zijn vaker in te zetten door ze in verschillende omgevingen te gebruiken of door een component vaker te instantiëren binnen één omgeving.
1. Samenstelbaar - Componenten kunnen worden gecombineerd tot nieuwe componenten.
1. Abstractie - Afnemers hoeven alleen de functionaliteit, niet de interne werking, van een component te kennen.
1. Servicecontract - Een component kent een gestandaardiseerd api contract.
1. Vindbaar - Services zijn voorzien van metadata en zijn goed vindbaar.
1. Toestandsloos - Componenten bewaren geen statusinformatie om goed schaalbaar te zijn.

### Cloud Native architectuur principes
1. Gebaseerd op een collectie van autonome componenten, waarbij componenten zich onafhankelijk van elkaar kunnen ontwikkelen en los van elkaar kunnen worden uitgerold.
1. Ondersteuning van een geautomatiseerd proces voor CI-CD (Continious Integration – Continious Delivery) voor frequente uitrol van componenten.
1. Gebaseerd op container technologie, waarbij alle code afhankelijkheden in de container zijn ingesloten. Container images worden gedistribueerd via een centraal registry.
1. Gebruik van volledig geautomatiseerde cloud native processen voor:
   1. Vervanging van componenten die falen door bijvoorbeeld en software crash.
   1. Vervanging van onderliggende hardware als die faalt.
   1. Schaling van componenten, bijvoorbeeld op basis van de actuele belasting.
   1. Active-active redundantie.
1. Componenten zijn ‘state-less’ ten behoeve van horizontale schaalbaarheid en active-active redundantie.
1. Decentrale opslag van gegevens zoals ‘state’. Om ongewenste koppeling te voorkomen, wordt de opslag wordt uitsluitend aangeroepen door de componenten die de gegevens beheerd.

### Leverancier agnostisch
Het streven is om zoveel mogelijk leverancier agnostisch te zijn en daarmee ‘vendor lock-in’ te beperken.

Belangrijk is met name om de afhankelijkheid van één bepaalde cloud provider te beperken.
Er is nu gekozen voor het Azure Kubernetes platform AKS, maar het moet mogelijk zijn om soortgelijke diensten van andere cloud providers zoals AWS, Google en IBM te gebruiken. Ook moet het in principe mogelijk om Kubernetes in een eigen datacenter uit te gebruiken.

### Vereenvoudiging van taken
Ontwikkelaars en beheerders hebben vaak een zware taak aan activiteiten zoals installatie, configuratie en monitoring die niet direct bijdragen aan de eigenlijke missie van de organisatie.
Dit verschijnsel werd door de CTO van Amazon omschreven als ‘undifferentiated heavy lifting’.

Het doel van de architectuur is ook om het mogelijk te maken dat ontwikkelaars en beheerders zich maximaal richten op de eigenlijke missie: het stroomlijnen van gemeentelijke processen.

### Samenwerking tussen OOs
In lijn met Common Ground principes moet er worden gestreefd naar herbruikbaarheid van oplossingen in de samenwerking tussen OOs.

Door afstemming op het gebied van andere andere platform infrastructuur, technologie keuzes, beheer en standaarden op het gebied van APIs en documentatie kan schaalvoordeel worden behaald.

Wel is het van belang om de budgettaire en bestuurlijke autonomie van OOs in acht te nemen.

## Richtlijnen voor laag 5 componenten
De UI is gebaseerd op ‘micro frontend’ architectuur waarbij de filosofie achter microservices naar  frontend development wordt gebracht.
Een traditionele SPA frontend applicatie is monolithisch, met alle bekende nadelen van een monolithische backend applicatie.
Met micro frontend architectuur kunnen verschillende teams van ontwikkelaars onafhankelijk van elkaar aan micro frontends ontwikkelen en uitrollen.

Laag 5 componenten hebben interactie met componenten op laag 4 en nooit direct interactie met laag 2/3, onder andere omdat het autorisatie model van laag 2/3 APIs hier niet voor ontworpen is. <-- hier lijkt overigens verandering in te komen. Er ligt een voorstel vanuit Den Haag om de ZGW API standaard aan te passen om een ander autorisatiemodel te volgen

### Technologie keuzes
Voor de ontwikkeling van __externe__ frontend applicaties (bedoeld voor burgers en bedrijven) wordt gebruik gemaakt van het van NL design systems afgeleide Utrecht Design System: https://github.com/nl-design-system/utrecht. Totdat NL design de 1.0 status heeft bereikt wordt gebruik gemaakt van Material Design voor de ontwikkeling van publiekelijk toegangkelijke frontends.

Voor de ontwikkeling van __interne__ Gemeente Utrecht frontends wordt gebruik gemaakt van Typescript, Angular Elements, Material Design en NX.

Material Design is de de-facto standaard component bibliotheek van Angular, met een groot aantal UI elementen zoals: drop-down box, datapicker, menu, tabel met pagination en sorteer opties.

Het gebruik van Material Design verkort de ontwikkeltijd, hetgeen in overeenstemming is met het uitgangspunt om ‘Undifferentiated heavy lifting’ te beperken.

## Richtlijnen voor laag 4 componenten
Ten behoeve van de onderhoudbaarheid en potentieel hergebruik is het wenselijk dat alle componenten in laag 4 voldoen aan een aantal gemeenschappelijke uitgangspunten.

### Algemene uitgangspunten
De specifiek invulling van een aantal van de algemene Common Ground principes voor componenten in laag 4 wordt in deze paragraaf beschreven.

Het Common Ground principe van ‘eenmalige vastlegging’  betekend dat als een component gegevens ophaalt bij een laag 2 API, zoals de Open Zaak API, deze niet lokaal in het component worden opgeslagen. Kortstondige caching in het component van eerder gebruikte data is wel toegestaan, echter het proactief laden van data is niet in overeenstemming met het principe van ‘eenmalige vastlegging’.

Data die door een component is gewijzigd, zoals bijvoorbeeld een zaak eigenschap, dient onmiddellijk te worden weggeschreven naar de laag 2 APIs. Dit betreft niet alleen de uiteindelijke status, nadat alle bewerkingen door de applicatie zijn afgerond, maar ook tussentijdse updates.

Er is gekozen voor een Cloud Native infrastructuur. Concreet betekent dit dat gebruik wordt gemaakt van Azure Kubernetes (AKS) met Otomi voor het beheer.

Componenten die worden uitgerold in de infrastructuur van de Gemeente Utrecht dienen aan de volgende eisen te voldoen:
* De uitrol van een component moet gebaseerd zijn op container technologie, waarbij alle code afhankelijkheden in de container zijn ingesloten. Container images worden gedistribueerd via een centraal Harbor registry.
* Indien de applicatie bestaat uit meerdere componenten moeten deze los van elkaar in containers worden uitgerold.
* Containers zijn vergankelijk en slaan geen data op.
Het component kan gebruik maken van een externe database voor de opslag van data.
* Redundantie en schaling wordt door het platform geïmplementeerd door middel van een laag 7 (http) load-balancer waarbij inkomend API verkeer wordt verdeeld over meerdere actieve kopieën van het component.
* Container images moeten veilig zijn en worden door de container registry gescand (met behulp van het open-source tool Trivi) op bekende kwetsbaarheden.

### Technologie keuzes
#### Knative
Componenten worden in principe met behulp van Knative uitgerold.

Kubernetes geeft veel flexibiliteit maar is ook complex in het gebruik en beheer.
Knative is een existentie op Kubernetes die de complexe taken voor ontwikkelaars weg abstraheert.
Met Knative kunnen ontwikkelaars zich richten op de applicatie zelf in plaats van ‘bijzaken‘ zoals complexe Kubernetes configuratie met yaml files.
Hiermee wordt invulling gegeven aan het ‘Vereenvoudiging van taken’ architectuur principe.

Knative omvat de ‘Serving’ en ‘Eventing’ sub-projecten. Otomi ondersteund enkel Knative ‘Serving’.
Knative Serving biedt een serverless platform voor de uitrol van containers on Kubernetes.
Out-of-the-box ondersteund Knative Serving de volgende functionaliteit:
* Automatisch schalen van services afhankelijk van de ‘load’; desgewenst tot nul.
Kosten kunnen worden bespaart door services buiten kantooruren automatisch af te schalen.
* Eenvoudig beheer van service versies, voor eenvoudige integratie met CI/CD.
* Automatische ‘active-active’ redundantie.

#### Helm charts
Helm charts zijn een verzameling yaml files die definiëren hoe een component op een kubernetes cluster wordt uitgerold.
Voor bestaande componenten waarvoor al Helm charts zijn gedefinieerd, kan deze methode worden gebruikt in plaats van Knative.
De definitie en het testen van Helm charts is erg complex, in het bizonder de configuratie van automatische schaling en redundantie. Om deze rede wordt voor nieuwe componenten Knative gebruikt.

#### Component ontwikkel platform
De componenten in laag 2 en 4 worden in bij voorkeur met Python ontwikkeld, maar een ander gangbaar ontwikkel eco-systeem zoals Java is ook acceptabel.

Om de hergebruik en onderhoudbaarheid te bevorderen is het van belang om de afhankelijkheid van externe Python libraries zo veel mogelijk te beperken. Dit is in het bijzonder van belang als als er wordt gewerkt met verschillende ontwikkelaars en teams.
Django wordt niet gebruikt voor GUI ontwikkeling.

### API richtlijnen
In een op componenten gebaseerd architectuur zijn naast de externe APIs, interne APIs tussen de componenten van groot belang.
De richtlijnen in deze paragraaf zijn zowel van toepassing op externe APIs (bijvoorbeeld tussen taal 4 en 5) alsmede interne APIs (bijvoorbeeld tussen componenten in laag 4).

Goed ontworpen en gedocumenteerde interne APIs zijn een vereiste voor hergebruik van componenten en voor de onderhoudbaarheid.

Het document “API Strategie Algemeen”, dat een API strategie van de Nederlandse overheid beschrijft is van toepassing; zie: https://docs.geostandaarden.nl/api/API-Strategie/.
Daarnaast moet worden voldaan aan de richtlijnen in sub paragrafen over het contract first principe en REST principes.

#### Contract first principe
De APIs worden gespecificeerd met behulp van de OpenAPI 3.x standaard.

De ‘Contract first’ benadering wordt toegepast, waarbij de ontwikkelaar of architect eerst de API specificeert in een OpenAPI yaml document dat zowel door ontwikkelaars als door software leesbaar is.

Het OpenAPI generator tool kan op basis van een OpenAPI yaml file de implementatie van de API genereren, die vervolgens eenvoudig vanuit de applicatie code kan worden aangeroepen.
De OpenAPI generator ondersteund vele programmeertalen, inclusief Python en Typescript.
De aanroep van de OpenAPI generator moet onderdeel zijn van het automatische (CI/CD) build proces.

Voordelen van de ‘contract first’ benadering en het gebruik van de OpenAPI generator zijn:
* Goede, consistente communicatie tussen teams
* De OpenAPI yaml file kan in git worden bijgehouden; wijzigingen in de API zijn gemakkelijk traceerbaar en direct voor alle teams zichtbaar
* Verschillende teams kunnen gelijktijdig met dezelfde API werken zonder elkaar in de weg te zitten
* ‘Separation of concerns’  design principe
* Geen implementatie fouten, dus consistent hoge kwaliteit
* Snellere werkwijze

#### REST principes
##### Algemene principes
* RESTful principes zijn van toepassing, zie: https://en.wikipedia.org/wiki/Representational_state_transfer.
* Data objecten worden gespecificeerd in JSON format.
* Http gzip voor de message payload wordt gebruikt.
##### Logische resources
* De basis voor de API zijn logische resources.
* Een logische resource is een zelfstandig naamwoord, geen werkwoord. Voorbeelden zijn: "klant", "zaak".
* Meervoud moet worden gebuikt als de respons kan bestaan uit meerdere resources; enkelvoud als er altijd een enkele resource wordt geretourneerd.
De URL kan worden opgebouwd uit een hiërarchie van resources. In het volgende voorbeeld URL zijn `land`, `klant` en `clips` resources: `/land/poland/klant/123456/clips/654321`.
##### Methodes
Resources worden gemanipuleerd met de volgende http methodes: GET, POST, PUT, DELETE:
* GET - retourneert een resource; veranderd de state van de resource niet.
* PUT - creëert of wijzigt een resource - idempotent.
* POST – creëert of wijzigt een resource - niet idempotent.
* DELETE - wist een resource.
##### Parameter plaatsing
REST ondersteund pad parameters, query parameters, body parameters en form data:
* Pad parameters.
  * Gebruikt voor resources.
  * De call `GET http:///example.com/klant/567/orders/123` retourneert een 404 code indien order met ID 123 niet bestaat.
* Query parameters
  * Wordt gebruikt voor additionele filters en het veranderen van de prestatie van de resource.
    Voorbeelden: paginatie, sorteer volgende, taal.
  * The call GET `http:///example.com/klant/567/?order=123` geeft geen resultaten in de response terug indien order met ID 123 niet bestaat.
  * Meerdere query parameters worden gescheiden door een `&` karakter: `http:///example.com/klant/567/?order=123&offset=300`.
* Body parameter
  * Wordt gebruikt als een pad of query parameter niet gebruikt kan worden, bijvoorbeeld voor een groot JSON object of een binair object.
  * De POST methode ondersteund ook form data.
* Header parameters
  * Wordt gebruikt voor protocol gerelateerde parameters zoals cache control en tokens.

##### Naam conventie
* De eerste deel van de URL (het deel dat door de DNS wordt geresolved) is niet hoofdletter gevoelig. Alle parameters in de API zijn wel hoofdletter gevoelig.

##### Mime type
* Voor  REST met JSON: `application/json`

##### User agent
De `user agent` is een string die in de http request door de client wordt toegevoegd om het client type te identificeren. De server kan op basis van de `user agent` de response aanpassen

##### API versies
Om compatibiliteit met oudere API versies te waarborgen moet de REST URL een versie nummer bevatten.

##### URL template
Een typische template voor een service call is als volgt: `https://<baseUrl>/<serviceName>/<version>/<pathParameters>?<queryParameters>`,
waarbij:
* `<baseUrl>` - the url die wordt geresolved door de DNS.
* `<serviceName>` - de naam van de service, bijvoorbeeld: searchService.
* `<version>` - de versie van de API, beginnende met de letter "v" .
* `<pathParameters>` - zie sectie 'Parameter plaatsing' en ‘Logische resources’.
* `<queryParameters>` - zie sectie 'Parameter plaatsing'.
* Voorbeeld: `GET https://api.utrecht.nl/clipService/v3/outlet/1234/clips/?offset=0&page=9`.

### Monitoring en logging richtlijnen
Om de ‘health’ van een component te kunnen bewaken met behulp van Prometheus dienen componenten een /metrics endpoint te ondersteunen. Via dit eindpont moeten alle voor de applicatie relevante numerieke status data worden gepresenteerd.

Voor de meeste applicatie platforms zoals Java en Django zijn libraries beschikbaar waarmee een /metrics endpoint voor het gebruik met Prometheus wordt ondersteund.

Component in een container dienen ongebufferd te loggen naar stdout en stderr zodat de logs kunnen worden verwerkt door Loki.

Voor het overdragen van de component/container logs naar Loki kan de Promtail agent worden gebruikt. Promtail is open-source software afkomstig van dezelfde ontwikkelaars als Loki en Grafana.

### Component documentatie
Voor de onderhoudbaarheid en bevordering van hergebruik dienen componenten op op een uniforme manier worden gedocumenteerd.

De documentatie moet bestaan uit:
* Een tekstuele beschrijving van de functionaliteit van het component.
* De API definitie met behulp van OpenAPI.
* Indien van toepassing, een overzicht van derde componenten waarvan het component afhankelijk is. Opmerking: afhankelijkheid van andere componenten dient zoveel mogelijk worden beperkt.
* UML sequence diagram dat de beoogde integratie tussen het component, laag 2 APIs en andere componenten beschrijft; zie: https://plantuml.com/sequence-diagram.
* Indien van toepassing, een lijst van te gebruiken laag 2 API calls.

## Database architectuur

### PostgreSQL
Er is gekozen voor de open-source PostgreSQL database voor zowel de laag 1 als applicatie databases in laag 4.

In overeenstemming met best-practices wordt de database wordt buiten het Kubernetes cluster geïmplementeerd. 

Op Azure kan gebruik worden gemaakt van een van de volgende twee managed PostgreSQL opties:
* Enkele Server
  * Max 100 GB
  * SLA 99.99% uptime (~1 uur/jaar down)
  * Geen auto-scaling
  * Hyperscale (Citus) server group
* Maakt gebruik van meerdere server nodes
  * Opslag en prestaties schalen horizontaal
  * Hoge beschikbaarheid, afhankelijk van de configuratie
  * Hogere kosten

De keuze tussen een enkele server en een server group wordt bepaald door de afweging tussen beschikbaarheid/schaalbaarheid en kosten.

###  5.2  Database alternatieven
Als met PostgreSQL niet meer voldaan kan worden aan de eisen op het gebied van prestatie en schaalbaarheid kan als alternatief een NoSQL database worden overwogen.
Veelgebruikte NoSQL databases zijn MongoDB en Cassandra.
Welke database keuze er ook wordt gemaakt voor laag 1, het uitgangspunt is dat de Common Ground API van laag 2 en de componenten op de lagen daarboven niet wijzigen.
De migratie naar een NoSQL heeft echter een zeer grote impact op de lagen 1 en 2.

## CI/CD
CI/CD is gebaseerd op de volgende principes:
* Elk component kan afzonderlijk worden uitgerold
* In lijn met het losse koppeling principe, heeft elk component zijn eigen git repository
* Het ontwikkel proces [GitFlow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) danwel [GitHub Flow](https://scottchacon.com/2011/08/31/github-flow.html) moet worden doorlopen voordat code kan worden ge-commit naar de git main branch
* De uitrol naar productie en acceptatie is volledig geaautomitiseerd zonder handmatige stapppen.
   * Fouten worden vermeden: de uitrol is volledig reproduceerbaar.
   * Mindere werk: maakt frequente uitrol mogelijk.
* Indien er gekozen is voor een acceptatie omgeving, dient er volledige pariteit tussen acceptatie en productie te zijn.
   * Als iets in acceptatie werkt weet je zeker dat het precies zo werkt in productie.
* De geautomatiseerde CI/CD pijplijn wordt getriggerd door een commit op de git main branch
* Geautomatiseerde tests worden uitgevoerd als onderdeel van de CI/CD pijplijn
* De output van een CI/CD straat is een (docker) image in het Harbor registry
* Harbor scant het image op kwetsbaarheden
* CD kan in principe staan voor Continious Deployment of voor Continious Delivery. Er is gekozen voor deze laatste definitie; hierbij is er een altijd een persoon (product owner of  beheerder) die beslist of een nieuwe versie in productie gaat.
* De product owner of beheerder kan kiezen voor een ‘canary deployment’ waarbij de nieuwe versie bijvoorbeeld 10% van het verkeer krijgt. Op die manier worden eventuele problemen met de nieuwe versie zichtbaar zonder dat de hele organisatie hiervan te veel last heeft.

## Midden team functionaliteit
Het uitgangspunt is dat verschillende OOs samenwerken waar mogelijk.
Gemeenschappelijke functionaliteit wordt ondergebracht in het “Midden Team”.

Om efficiënt samen te werken dient er overeenstemming te zijn over:
* Gemeenschappelijke architectuurprincipes
* Gemeenschappelijke infrastructuur/platform
* Gemeenschappelijke API standaarden
* Gemeenschappelijke documentatie standaarden
* Gemeenschappelijke monitoring en logging standaard
* Gemeenschappelijke CI/CD benadering
* Gemeenschappelijk ontwikkelproces, inclusief synchronisatie van sprints

In een component architectuur is het strikt genomen niet nodig dat alle services/componenten met dezelfde technologie worden ontwikkeld. Echter, als ontwikkelaars van verschillende OOs aan dezelfde midden componenten moeten kunnen werken, moet er ook overeenstemming zijn over:
* Gemeenschappelijke applicatie technologie stack
* Gemeenschappelijke code standaarden

### Modus operandum
In het volgende figuur zijn met Otomi drie Teams gedefinieerd in een cluster: W&I, 'x' en Midden. Een Team in Otomi is een Namespace in Kubernetes en kan optioneel geïsoleerd worden van andere Otomi Teams.

De OOs W&I en 'x' hebben ieder hun ontwikkelaars die functionaliteit voor het eigen Team ontwikkelen en daarnaast bijdragen aan de gemeenschappelijke functionaliteit van het Otomi Team Midden.

Services op het 'x' Team en het W&I Team zijn volledig van elkaar geïsoleerd: een service in het Otomi Team 'x' heeft geen toegang tot een service op het Team W&I en vis versa.
Service op het Team 'x' en ket Team W&I hebben wel volledig toegang tot services op het midden Team.

![](https://github.com/GemeenteUtrecht/cloudnative-cg/raw/main/WIKiK.png)

Voordelen:
* Schaalvoordeel door gedeelde midden functionaliteit

Nadelen:
* Gedeelde verantwoordelijkheid voor functionaliteit van het midden platform
* Hogere operationeel beheer last: drie platforms om te beheren
