# Beschrijving batch XML

!!! tip XSD Schema
[Download](../schemas/tdm3prestatiebestand.xsd)

@startuml
node p as "pakketgegevens"
node s as "softwarehuis"
node pat as "patientgegevens"
node zorg as "uitgevoerdezorg"
node lez as "lezingidentiteit"
node tar as "tarificatiedienst"
node pr as "prijsberekening"
node ver as "vereniging"
node prof as "profielen"
node fac as "facturatieperiode"

p --> s
p --> pat
p --> zorg
p --> lez
p --> tar
p --> fac
p --> pr
p --> ver
p --> prof

@enduml
!!!


## Belangrijkste wijzigingen

| Versie | Omschrijving                                                                                                                                                               |
|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.11   | Bijkomende uitleg: algemene opmerkingen                                                                                                                                    |
| 1.12   | Toevoeging: waarde type facturering = 5 Toevoeging van locatie element op uitgevoerdezorg                                                                               |
| 1.20   | Toevoeging: voor bewijsstuk aan de patiënt: keuze kanaal, keuze adres                                                                                                     |
| 1.30   | Toevoeging: node “lezingenidentiteit”                                                                                                                                   |
| 1.31   | Toevoeging: type “metadata” en “metadataitem” Toevoeging: optionele node “metadata” op node patientgegeven Toevoeging van typebezoek element op node uitgevoerdezorg |
| 1.32   | Toevoeging: element “taalcodebewijsstuk”                                                                                                                                    |
| 1.33   | Toevoeging: element kb90omschrijving                                                                                                                                        |
| 1.34   | Toevoeging: element zorgminuten op node uitgevoerdezorg                                                                                                                     |
| 1.35   | Verduidelijking doorgeven afscoring forfait                                                                                                                                |
| 1.36   | Toevoeging: waarde eBox (3) bij kanaalbewijsstuk op node patientgegeven Verduidelijking startdatum profiel/akkoord vs palliatief akkoord                                    |
| 1.37   | Toevoeging: element identificatiebestemmelingbewijsstuk op node patientgegeven Toevoeging element identificatietypebestemmelingbewijsstuk op node patientgegeven            |
| 1.38	| Toevoeging: [facturatie wekelijks voorbereiding](#facturatie-424874-wekelijkse-voorbereiding-van-de-geneesmiddelen-per-os), [uitzondering dagelijks toilet bij een T2 profiel](#node-profielen) |
| 1.39 |Fix- Verduidelijking oplijsting type bestemmeling op node uitgevoerdezorg (medisch huis = 5) |
| 1.40 |Fix verduidelijking noodzaak derdebetalercode, -naam en -adres op node uitgevoerdezorg |
| 1.41 | Toevoeging: [instructies diabeteseducatie via videoconsultatie tijdens COVID19-crisis](#covid19-crisis-instructies) |
| 1.42 | Toevoeging: toevoeging 426576 bij pseudocodes die mag/moet meegegeven worden   |

## Algemene opmerkingen
Per praktijk (unieke verenigingcode)  en per “facturatiemaand” wordt er 1 xml bestand aangemaakt.

### Toevoegingen door TDM3

De uitgevoerde zorgen (als nomenclatuurode) worden doorgegeven in het xml bestand. 
TDM3 voegt zelf onderstaande zaken toe ikv tarificatie.
-	Basisverstrekkingen (eerste, tweede,derde)
-	Forfait A, B, C, PA, PB, PC, PP
	-   De nomenclatuurcodes ikv zorgen worden dan vervangen door pseudocodes
-	Supplementair forfait: PN
-	Opvolgingshonorarium diabetes (op basis van waarde “insuline= true” op node uitgevoerde zorg)
-	Verplaatsingskosten (op basis van waarde “ruraal=true” op node patientgegeven)

Dit betekent dat deze nomenclatuurcodes (basisverstrekking, forfaits, pseudocodes als vervanging van) niet meekomen in de xml. Een paliatief forfait wordt aangerekend op basis van 
-	de startdatum “Paliatief” op node patientgegeven. 
-	In combinatie met het doorgegeven patientprofiel. Ook al zou er in de node “patientprofiel” een paliatief forfait profiel zijn aangeduid, TDM3 kijkt altijd naar de startdatum “Paliatief” op node patientgegeven om te bepalen of een patiënt palliatief is of niet.


### pseudocodes die wel kunnen meegegeven worden in het xml bestand:
Pseudocodes die een bijkomende noodzakelijke registratie betekenen en dus op zichzelf noodzakelijk zijn ikv tarificatie, kunnen en moeten wel meekomen in de xml.

Bijvoorbeeld: 

- 426510:  	Thuiszorgen verpleegkunde, palliatieve patiënten : pseudocode te vermelden voor elk dringend bezoek overdag dat tijdens de verzorgingsdag is verleend voor              het forfait PA (427055 en 427136), voor het forfait PB (427033 en 427114), voor het forfait PC (427011 en 427092), voor het forfait PP (427173 en 427195)               en voor het forfait PN (427070 en 427151)
- 426893: 	Thuiszorgen verpleegkunde, palliatieve patiënten : pseudocode te vermelden voor elk overlegvergadering met de huisarts dat tijdens de verzorgingsdag is                 verleend voor het forfait PA (427055 en 427136), voor het forfait PB (427033 en 427114), voor het forfait PC (427011 en 427092), voor het forfait PP                    (427173 en 427195) en voor het forfait PN (427070 en 427151)
- 426576: Thuiszorgen verpleegkunde : pseudocode te vermelden voor elke voorbereiding medicatie dat tijdens de verzorgingsdag is verleend voor : a) niet-palliatieve patiënten : het forfait A (425272, 425670, 426075 en 429096), voor het forfait B (425294, 425692, 426090 en 429111) en voor het forfait C (425316, 425714, 426112 en 429133); b) palliatieve patiënten : het forfait PA (427055 en 427136), voor het forfait PB (427033 en 427114), voor het forfait PC (427011 en 427092) en voor het forfait PP (427173


### Remgeld

Indien er gefactureerd wordt aan een verzekeringsinstelling (type bestemmeling = 2), dan moet bij remgeld (=het deel dat aan de patient wordt gevraagd en dus de patient niet van de derdebetaler (verzekeringsinstelling) terugtrekt). Doorgaans wordt er geen remgeld gevraagd bij individuele verzekeringsinstellingen en is dit zinloos. Maar als dit veld is ingevuld dan zal TDM3 remgeld factureren aan de patient en niet aan de derdebetaler.


### Herindieningen
Aangezien de facturen en afrekeningenen worden opgemaakt ter hoogte van TDM3, kunnen en mogen herindieningen als gewone indieningen (type facturering = 0) of als expliciet herindiening (type facturering = 1) meegaan, TDM3 voegt de bijkomende gegevens waarover het END niet kan beschikken, toe.


### Zorgen zonder riziv-nomenclatuur
Deze kunnen en mogen in de xml meegaan en worden gefactureerd onder de KB90 nomenclatuurnr. In het veld KB90Bedrag wordt dan het te factureren bedrag aangeduid.


### Locatie veld bij node uitgevoerde zorg
De nomenclatuur specifieert enkel “praktijkkamer OF hersteloord”. Vanaf prestatiedatum 1 april 2016 kan men voor deze nomenclatuurnrs geen bedrag meer factureren, tenzij deze is uitgevoerd in een hersteloord. Om dit onderscheid te kunnen maken wordt de locatie gespescifieerd via een pseudocode (zie onderstaande tabel).
Wanneer de doorgegeven nomenclatuurnr een “praktijkkamer of hersteloord” betreft, moet een van de onderstaande pseudocodes worden vermeld. Op basis van deze pseudocode zal TDM3 dan al dan niet een bedrag factureren. 
| NomenclatuurCode | Omschrijving                                                                                                                                                                              |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 421131           | Verpleegkundigen: pseudo-code betrekkelijke verstrekking: alleenstaande praktijkkamer van verpleegkundige(n)                                                                              |
| 421212           | Verpleegkundigen: pseudo-code betrekkelijke verstrekking: hersteloord                                                                                                                     |
| 421175           | Verpleegkundigen: pseudo-code betrekkelijke verstrekking: praktijkkamer van verpleegkundige(n) in een ziekenhuis                                                                          |
| 421190           | Verpleegkundigen: pseudo-code betrekkelijke verstrekking: praktijkkamer van verpleegkundige(n) in een polikliniek buiten een ziekenhuiscampus bij geneesheer specialist(en)               |
| 421153           | Verpleegkundigen: pseudo-code betrekkelijke verstrekking: praktijkkamer van verpleegkundige(n) deel uit makend van een multidisciplinaire groepspraktijk van eerste lijns gezondheidszorg |


### Pijnpompcodes
Aangezien op basis van de pseudocode niet is op te maken welke betalende nomenclatuurcode TDM3 dient toe te voegen, moet in de xml de betalende nomenclatuurcode alsook de pseudocode meegaan, als aparte uitgevoerdezorg nodes:  1 met veld nomenclatuurummer  ingevuld, voor de betalende nomenclatuurcode, de andere met het veld Pseudocodenummer ingevuld, voor de pseudocode.

**Betalende codes**
| NomenclatuurCode | WWaarde | KorteOmschrijvingNL                                                     |
| ---------------- | ------- | ----------------------------------------------------------------------- |
| 429251           | 2.94600 | vervangen suprapubische sonde met ballon / verblijfplaats mindervaliden |
| 427571           | 2.94600 | toezicht/opvolging pompsystemen catheter / praktijkkamer                |
| 427534           | 2.94600 | toezicht/opvolging pompsystemen catheter / weekdag aan huis             |
| 427556           | 4.50400 | toezicht/opvolging pompsystemen catheter / weekend/feestdag aan huis    |

  **Pseudocodes**
| NomenclatuurCode | WWaarde | OmschrijvingNL                                                                                                                                                                                                                                          |
| ---------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 427615           | NULL    | Thuiszorgen verpleegkunde, specifieke technische verstrekkingen : Pseudocode te vermelden voor elke verbandwissel dat tijdens de verzorgingsdag is verleend bij de facturatie van de verstrekking 427534, 427556, 427571 of 429251                      |
| 427593           | NULL    | Thuiszorgen verpleegkunde, specifieke technische verstrekkingen : Pseudocode te vermelden voor elk bijkomend bezoek dat tijdens de verzorgingsdag is verleend bij de facturatie van de verstrekking 427534, 427556, 427571 of 429251                    |
| 427630           | NULL    | Thuiszorgen verpleegkunde, specifieke technische verstrekkingen : Pseudocode te vermelden voor elk heropstarten van de pomp dat tijdens de verzorgingsdag is verleend bij de facturatie van de verstrekking 427534, 427556, 427571 of 429251            |
| 427674           | NULL    | Thuiszorgen verpleegkunde, specifieke technische verstrekkingen : Pseudocode te vermelden voor elk wisselen van het medicatiereservoir dat tijdens de verzorgingsdag is verleend bij de facturatie van de verstrekking 427534, 427556, 427571 of 429251 |
| 427652           | NULL    | Thuiszorgen verpleegkunde, specifieke technische verstrekkingen : Pseudocode te vermelden voor elke wijziging van de onderhoudsdosis dat tijdens de verzorgingsdag is verleend bij de facturatie van de verstrekking 427534, 427556, 427571 of 429251   |

###  het doorgeven van een afscoring forfait
Het oorspronkelijke forfait en oorspronkelijke zorgen hoeven niet doorgegeven worden. De zorgen + nieuw profiel moeten doorgegeven worden. 
Bij de betrokken zorgen hoeft enkel type facturering = 5 meegegeven te worden. TDM3 kan dan een  correctie forfait factuur opstellen,  de originele factuurlijnen worden opgehaald, in min gezet en de nieuwe factuurlijnen worden in plus zetten.
Wat indien de oorspronkelijke facturatie niet door TDM3 of onder een ander groepsnr is uitgevoerd? Dit gebeurt niet via de xml en verijst manuele interventie van een TDM3 tarificateur.
    
Voorbeeld afscoring forfait/profiel van A naar T7
```xml
	<profielen>
		<externeid>2</externeid>
		<startdatum>2018-04-01T00:00:00</startdatum>
		<einddatum>2018-04-30T00:00:00</einddatum>
		<patientgegevens_id>341</patientgegevens_id>
		<profiel>T7</profiel>
	</profielen>
	<uitgevoerdezorg>
		<input_uitgevoerdezorg_id>7007</input_uitgevoerdezorg_id>
		<externeid>423</externeid>
		<datumuitvoering>2018-04-28T11:00:00</datumuitvoering>
		<datumvoorschrift>2018-04-01T00:00:00</datumvoorschrift>
		<rizivnummervoorschrijver/>
		<naamvoorschrijver> </naamvoorschrijver>
		<remgeld>0</remgeld>
		<nomenclatuurnummer>425515</nomenclatuurnummer>
		<pseudocodenummer>000000</pseudocodenummer>
		<kb90bedrag/>
		<kb90omschrijving/>
		<rizivnummerzorgkundige/>
		<naamzorgkundige/>
		<rizivnummerverpleegkundige>149488014401</rizivnummerverpleegkundige>
		<ziekenfondscode>322</ziekenfondscode>
		<patientgegevens_id>341</patientgegevens_id>
		<bezoeknummer>1</bezoeknummer>
		<rep_typebestemmeling_id>1</rep_typebestemmeling_id>
		<insuline>false</insuline>
		<derdebetalercode/>
		<derdebetalernaam/>
		<derdebetaleradres/>
		<polisnummer/>
		<dossiernummer/>
		<datumongeval/>
		<werkgever/>
		<ZHOpname/>
		<Locatie/>
		<TypeFacturering>5</TypeFacturering>
	</uitgevoerdezorg>
```
### COVID19-crisis instructies
#### Diabeteseducatie via videoconsultatie tijdens de COVID19-crisis
- Als tijdelijke aanpassing mag een individuele sessie tijdens de duur van de maatregelen, door middel van videocommunicatie plaatsvinden.
	Deze "locatie" kan via de xml aangeduid worden door de pseudocode **792433** te vermelden in het element **Locatie** ([Bron RIZIV](https://www.riziv.fgov.be/nl/covid19/Paginas/diabeteseducatie-1ste-lijn-videoconsultatie.aspx))
	```xml
	    <uitgevoerdezorg>
		     ...andere elementen...
			<nomenclatuurnummer>794312 </nomenclatuurnummer>
			<Locatie>792433</Locatie>
	         ...andere elementen...
		</uitgevoerdezorg>
	```

## XML Nodes
### Node Pakketgegevens
Deze node is de overkoepelende node waaronder alle andere nodes verzameld staan.

- Ze bevat eerst een aantal patientgegevens-nodes (zie 1.1)
- dan een aantal uitgevoerdezorg-nodes (zie 1.2),
- één tarificatiedienst-node (zie 1.3), één facturatieperiode-node (zie 1.4), 
- één vereniging-node (zie 1.5) en uiteindelijk een aantal profielen-nodes (zie 1.7). 
De nodes moeten in opgegeven volgorde voorkomen!

	```xml
	<pakketgegevens>
		<softwarehuis>
			…
		</softwarehuis >
		<patientgegevens>
			…
		</patientgegevens>
		<patientgegevens>
			…
		</patientgegevens>
		<uitgevoerdezorg>
			…
		</uitgevoerdezorg>
		<uitgevoerdezorg>
			…
		</uitgevoerdezorg>
		<tarificatiedienst>
			…
		</tarificatiedienst>
		<facturatieperiode>
			…
		</facturatieperiode>
	<vereniging>
		…
	</vereniging>
		<profielen>
			…
		</profielen>
		<profielen>
			…
		</profielen>
	</pakketgegevens>

	```

### Node softwarehuis
Deze node bevat de gegevens van het softwarehuis

-	Code (string): Dit is een code toegekend door TDM3.
-	Naam (string): De naam van het softwarehuis
-	Telefoonnummer (string): Het telefoonnummer van het softwarehuis
-	Email (string): Het emailadres van het softwarehuis


Voorbeeld:
```xml
	<softwarehuis>
		<code>…</code>
		<naam>…</naam>
		<telefoonnummer>…</telefoonnummer>
		<email>…</email>
	</softwarehuis>
```

### Node patiëntgegevens

Per patient is er één patientgegevens node met hierin de volgende gegevens (in volgorde zoals ze hieronder staan):

- **patientgegevens\_id** (integer): Dit is een uniek nummer binnen dit bestand waarmee de &#39;uitgevoerdezorgen&#39;-nodes en &#39;profielen&#39;-nodes gelinkt kunnen worden aan de &#39;patiënt&#39;. Dit wil zeggen dat voor elk &#39;profielen&#39;-node en elke &#39;uitgevoerdezorg&#39;-node van de patiënt deze patientgegevens\_id gebruikt moet worden.
- **externeid** (integer): De patientid van het versturende systeem; deze id wordt samen met de patientgegevens\_id gebruikt om de &#39;uitgevoerdezorgen&#39;-nodes en de &#39;profielen&#39;-nodes te linken aan de patiënt. Dit wil zeggen dat voor elke &#39;profielen&#39;-node en elke &#39;uitgevoerdezorg&#39;-node van de patiënt deze externeid gebruikt moet worden.
- **naam** (string): De naam van de patiënt.
- **voornaam** (string): De voornaam van de patiënt.
- **geslacht** (string): Het geslacht van de patiënt (mogelijke waardes: M, V).
- **geboortedatum** (dateTime): De geboortedatum van de patiënt.
- **straat** (string): De straat van de patiënt.
- **huisnummer** (string): De huisnummer van de patiënt. (kan maximum 10 karakters bevatten).
- **postcode** (string): De postcode van de patiënt.
- **gemeente** (string): De gemeente van de patiënt.
- **landcode** (string): De landcode van de patiënt (ISO)
- **ziekenfondscode** (string): Het ziekenfonds waar de patiënt is aangesloten.
- **stamnummer**(string): Enkel voor buitenlanders en boorlingen,**voor alle andere  gevallen moet men het rijksregisternummer gebruiken.** Bij facturatie van zorgen buiten het ZIV (verzekering, ocmw, patient) en het rijksregisternummer of stamnummer is niet gekend, kan in dit veld een unieke identificatienummer meegegeven worden.
- **risicocode1** (string): De risicocode 1 van de patiënt.
- **risicocode2** (string): De risicocode 2 van de patiënt.
- **ruraal** (boolean): Als dit veld true is dan wordt er voor elke uitgevoerde zorg een prestatie met nomenclatuur 418913, verplaatsingskosten, aangemaakt.
- **startdatumpal (dateTime):** De startdatum paliatieve verzorging, van het palliatief akkoord
- **rijksregisternummer** (string): Het rijksregisternummer van de patiënt.
- **kanaalBewijsstuk** (integer) 1= Per post, 2=per e-mail, 3 = eBox
- **bestemmelingBewijsstuk** (integer)1=patiënt, 2=vertegenwoordiger van de patiënt of bewindvoerder
- **naamBestemmelingBewijsstuk** (string): De naam van de bestemmelingbewijsstuk (zelfs als dit de patiënt is)
- **voornaamBestemmelingBewijsstuk** (string): De voornaam van de bestemmelingbewijsstuk (zelfs als dit de patiënt is)
- **straatBestemmelingBewijsstuk** (string): De straat van bestemmelingbewijsstuk (zelfs als dit de patiënt is)
- **huisnummerBestemmelingBewijsstuk** (string): De huisnummer van bestemmelingbewijsstuk (zelfs als dit de patiënt is). (kan maximum 10 karakters bevatten)
- **postcodeBestemmelingBewijsstuk** (string): De postcode van bestemmelingbewijsstuk (zelfs als dit de patiënt is).
- **gemeenteBestemmelingBewijsstuk** (string): De gemeente van van bestemmelingbewijsstuk (zelfs als dit de patiënt is)..
- **emailBestemmelingBewijsstuk** (string): e-mailadres van bestemmelingbewijsstuk (zelfs als dit de patiënt is).
- **taalcodebewijsstuk** (string): de ISO-639-1 taalcode waarin het bewijsstuk moet opgesteld worden.  [https://en.wikipedia.org/wiki/List\_of\_ISO\_639-1\_codes](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)
    - Nederlands = nl
    - Frans = fr
    - Tweetalig = nlfr
    Indien geen taalcode wordt meegegeven of aangeduid, wordt de overeenkomstige taal van de postcode (Gewest) gebruikt. Indien Brussels Hoofdstedelijk Gewest, dan wordt het bewijsstuk tweetalig opgesteld.
- **identificatiebestemmelingbewijsstuk** (string)
  - identificatie eigenaar ebox
  - indien patient, hier het insz nummer van de patient herhalen, indien bewindvoerder, de insz van de bewindvoerder plaatsen.
- **identificatietypebestemmelingbewijsstuk** (int)
  - type identificatie eigenaar eBox, 1 = INSS , 2 = NIHII, 3 = CBE (organization)
- **metadata**(type metadata, optioneel element)**:** een &quot;key-value&quot; pair  (naam-waarde) van items waarbij optioneel meta informatie kan meegegeven, die niet direct te maken heeft met de tarificatie van prestaties.

		Voorbeeld metadata item
	```xml
			<metadata>
			<item>
				<naam>venootschap</naam>
				<waarde>de zonnebloem</waarde>
			</item>
			<item>
				<naam>ronde</naam>
				<waarde>tour 1</waarde>
			</item>
			</metadata>
	```

Voorbeeld patientgegeven
```xml
	<patientgegevens>
		<patientgegevens_id>…</patientgegevens_id>
		<externeid>…</externeid>
		<naam>…</naam>
		<voornaam>…</voornaam>
		<geslacht>…</geslacht>
		<geboortedatum>…</geboortedatum>
		<straat>…</straat>
		<huisnummer>…</huisnummer>
		<postcode>…</postcode>
		<gemeente>…</gemeente>
		<landcode>…</landcode>
		<ziekenfondscode>…</ziekenfondscode>
		<stamnummer>…</stamnummer>
		<risicocode1>…</risicocode1>
		<risicocode2>…</risicocode2>
		<ruraal>…</ruraal>
		<startdatumpal>…</startdatumpal>
		<rijksregisternummer>…</rijksregisternummer>
	</patientgegevens>
```

### Node uitgevoerdezorg


Per zorgverstrekking is er een uitgevoerdezorg node met hierin de volgende gegevens (in volgorde zoals ze hieronder staan):

- **input\_uitgevoerdezorg\_id** (int): De uitgevoerdezorgid van het versturende systeem.
- **externeid** (int): Het patientid van het versturende systeem refererend naar externid van patientgegevens.
- **datumuitvoering** (dateTime): De datum waarop de prestatie is uitgevoerd.
- **zorgminuten:** (int): het aantal minuten de uitgevoerdezorg heeft geduurd.
- **datumvoorschrift** (dateTime): De datum waarop de prestatie is voorgeschreven.
- **rizivnummervoorschrijver** (string): De rizivnummer van de voorschrijvende geneesheer.
- **naamvoorschrijver** (string): De naam van de voorschrijvende geneesheer.
- **remgeld** (int): uitgedrukt als percentage van het persoonlijk aandeel dat wordt gevraagd, van 0 tem 100. 0 is geen remgeld tellen, 100 = het volledige persoonlijk aandeel wordt gevraagd.
- **nomenclatuurnummer** (string): De nomenclatuurnummer van de prestatie.
- **pseudocodenummer** (string): De pseudocode voor de nomenclatuur. Moet enkel ingevuld worden indien er geen nomenclatuurnummer bestaat (palliatieve zorgen en niet vergoedbare zorgen). Dan moet de nomenclatuurnummer 000000 zijn. Indien geen pseudocode veld opvullen met 000000. 
- **kb90bedrag** (double): Het bedrag van de uitgevoerde zorg die valt onder het KB van 1990. Voor deze zorg bestaat geen nomenclatuurnummer en moet 426856 als pseudocodenummer hebben.
- **kb90omschrijving:** een vrije omschrijving van de zorg die gefactureerd wordt onder de kb90 nomenclatuur.
  - Deze omschrijving zal verschijnen op het bewijsstuk naar de patiënt en op de factuur naar de patiënt of de verzekering
- **ziekenfondscode** (string): Het ziekenfonds van de patiënt.
- **rizivnummerverpleegkundige** (string): De rizivnummer van de verpleegkundige. Deze moet voorafgaan door 0 of 1 :
    - 0 : niet geconventioneerd
    - 1 : wel geconventioneerd
- **patientgegevens\_id** (int): De unieke nummer van de patiënt binnen dit bestand.
- **bezoeknummer** (int): Nummer van het bezoek.
- **rep\_typebestemmeling\_id** (int): Indicator die aangeeft voor wie de factuur is. Mogelijke waardes: 1=ziekenfonds, 2=verzekering, 3=patiënt, 4=ocmw, 5=medisch huis,…
- **insuline** (boolean): Dit zou op &#39;True&#39; moeten staan wanneer de verpleegkundige een dossier bijhoudt van een diabetes patiënt en de verpleegkundige insuline inspuitingen geeft.  De verpleegkundige krijgt dan een forfait honoraria (423231,423334).  Dit forfait wordt dan door TDM3 toegevoegd.
- **derdebetalercode** (string): Enkel voor niet-ziekenfonds facturen (patiënt, verzekering, medisch huis, ocmw, etc). Voor patiëntfacturen staat hier de patientnummer(dit mag een interne id zijn, eigen nummering,…), voor andere staat hierin de id van de verzekering in het versturende systeem. Dit veld mag maximum 10 posities lang zijn.
- **derdebetalernaam** (string): Enkel voor niet-ziekenfonds facturen (patiënt, verzekering, medisch huis, ocmw, etc). Voor patiëntfacturen staat hier de naam van de patiënt, voor andere staat hier de naam van de verzekeringsinstelling.
- **derdebetaleradres** (string): Enkel voor niet-ziekenfonds facturen (patiënt, verzekering medisch huis, ocmw, etc). Voor patiëntfacturen staat hier het adres van de patiënt, voor andere staat hier het adres van de verzekeringsinstelling.
- **polisnummer** (string): In geval van een arbeidsongeval staat hier het polisnummer.
- **dossiernummer** (string): In geval van arbeidsongeval staat hier het dossiernummer.
- **datumongeval** (dateTime): In geval van een arbeidsongeval staat hier de datum van het ongeval.
- **werkgever** (string): In geval van een arbeidsongeval staat hier de naam van de werkgever.
- **TypeFacturering** (string)
  - 0 = standaard factuur,
  - 3  = kredietnota
  - 5 = correctiefactuur voor forfaits verpleegkundige zorgen wegens declassering na bezoek adviserend geneesheer
- **rizivnummerzorgkundige** (string): De rizivnummer van de zorgkundige
- **naamzorgkundige** (string): De naam van de zorgkundige
- **ZHOpname (int):** optioneel veld om aan te duiden of het over een ziekenhuisopname gaat of niet (0 of 1).
- **Locatie:** Ikv Verordening van 28 juli 2003 bijlage 85, wordt hier de overeenkomstige pseudocode meegegeven die moeten vermeld worden bij de verstrekkingen van art. 8, §1, 3° van de nomenclatuur (&quot;verstrekkingen verleend hetzij in de praktijkkamer van de beoefenaar van de verpleegkunde, hetzij in een hersteloord&quot;). Deze pseudo-codes geven de plaats van verstrekking weer. Wordt ook gebruikt ikv diabeteseducatie via videoconsultatie tijdens COVID19-crisis. De betrekkelijke verstrekking wordt dan in dit element ingevuld.
- **typebezoek (int) – **optioneel om mee te geven. Laat toe om presaties zoals** 424874** ( _wekelijkse voorbereiding van geneesmiddelen per os_) die zijn uitgevoerd in een locatie waar geen vergoeding voor bestaat (&#39;instelling/ hersteloord/praktijkkamer&#39;) toch tegen te houden
  - 1 - aan huis van de patiënt op weekdag
  - 2 - aan huis van de patiënt in weekend en op feestdagen
  - 3 - Praktijkkamer van de verpleegkundige
  - 4 - Homes minder validen
  - 5 - Dagverzorgings centrum
- **metadata**(type metadata, optioneel element)**:** een &quot;key-value&quot; pair  (naam-waarde) van items waarbij optioneel meta informatie kan meegegeven, die niet direct te maken heeft met de tarificatie van prestaties.

	```xml
	<uitgevoerdezorg>
		<input_uitgevoerdezorg_id>…</input_uitgevoerdezorg_id>
		<externeid>…</externeid>
		<datumuitvoering>…</datumuitvoering>
		<datumvoorschrift>…</datumvoorschrift>
		<rizivnummervoorschrijver>…</rizivnummervoorschrijver>
		<naamvoorschrijver>…</naamvoorschrijver>
		<remgeld>…</remgeld>
		<nomenclatuurnummer>…</nomenclatuurnummer>
		<pseudocodenummer>…</pseudocodenummer>
		<kb90bedrag>…</kb90bedrag>
		<ziekenfondscode>…</ziekenfondscode>
		<rizivnummerverpleegkundige>…</rizivnummerverpleegkundige>
		<patientgegevens_id>…</patientgegevens_id>
		<bezoeknummer>…</bezoeknummer>
		<rep_typebestemmeling_id>…</rep_typebestemmeling_id>
		<insuline>…</insuline>
		<derdebetalercode>…</derdebetalercode>
		<derdebetalernaam>…</derdebetalernaam>
		<derdebetaleradres>…</derdebetaleradres>
		<polisnummer>…</polisnummer>
		<dossiernummer>…</dossiernummer>
		<datumongeval>…</datumongeval>
		<werkgever>…</werkgever>
	</uitgevoerdezorg>
	```

#### facturatie 424874 - Wekelijkse voorbereiding van de geneesmiddelen per os
- Voorafgaandelijk aan de facturatie van verstrekking **424874** moet er een verstrekking **424896** *"Verpleegkundig advies en overleg in functie van de wekelijkse voorbereiding van de geneesmiddelen per os met akkoord van de behandelend arts"* zijn gefactureerd. Deze verstrekking kan reeds gefactureerd zijn door een externe partij. Indien in het EMD gekend is wanneer deze verstrekkering 424896 is gefactureerd door deze externe partij, mag er een **metadata** item toegevoegd worden in de uitgevoerdezorg node van 424874, met volgende waarde
    - **naam** : "externf424896"
    - **waarde**: "datum" (geformateerd als yyyy-MM-dd)
- Deze informatie laat de facturatiedienst toe een betere controle uit te voeren

	```xml
	<uitgevoerdezorg>
		...
		<nomenclatuurnummer>424874</nomenclatuurnummer>
		<datumuitvoering>2019-10-18T01:00:00</datumuitvoering>
		...
		<metadata>
		<item>
			<naam>externf424896</naam>
			<waarde>2019-05-18</waarde>
		</item>
		</metadata>
	<uitgevoerdezorg>
	```
 

### Node tarificatiedienst

Deze node bevat de code en naam van de tarificatiedienst waar de prestaties verwerkt zullen worden.

-	**code** (string): De code van de tarificatiedienst. Dit moet TDM3 zijn.
-	**naam** (string): De naam van de tarificatiedienst. Dit moet TDM3 zijn.

Voorbeeld:
```xml
<tarificatiedienst>
	<code>TDM3</code>
	<naam>TDM3</naam>
</tarificatiedienst>

```

### Node facturatieperiode

Deze node bevat het jaar en de maand van de verwerking van de prestaties.

- **jaar** (int): Het jaar van de verwerking van de prestaties.
- **maand** (int): De maand van de verwerking van de prestaties.

**Voorbeeld:**

```xml
<facturatieperiode>
	<jaar>…</jaar>
	<maand>…</maand>
</facturatieperiode>
```

### Node vereniging
Node vereniging

Deze node bevat de code en naam van de vereniging waar de prestaties vandaan komen.

- **code** (string): De code van de vereniging (praktijk) waar de prestaties vandaan komen.  Via TDM3 aan te vragen.
- **naam** (string): De naam van de vereniging waar de prestaties vandaan komen.

**Voorbeeld:**
```xml
<vereniging>
	<code>…</code>
	<naam>…</naam>
</vereniging>
```

### Node prijsberekening

Deze node bevat een indicator die aangeeft of de prijsberekening een tijdelijke prijsberekening is of niet.

**- tijdelijk** (boolean): True als het een tijdelijke prijsberekening is.

```xml
<prijsberekening>
	<tijdelijk>false</tijdelijk>
</prijsberekening>
```

### Node profielen
Per patientprofiel is er een profielen-node met daarin volgende gegevens (in volgorde zoals ze hieronder staan).
De startdatum van het palliatief akkoord wordt op patientgegeven node meegegeven, in het veld &quot;startdatumpal&quot;.

- **externeid** (int): de patientid uit het versturende systeem refererend naar externid van patientgegevens.
- **startdatum** (dateTime): Startdatum van het mycarenet akkoord/profiel
- **einddatum** (dateTime): Einddatum van het mycarenet akkoord/profiel
- **patientgegevens\_id** (int): De unieke nummer van de patiënt binnen dit bestand.
- **profiel** (string): Het type profiel van de patiënt.
- **toiletten** (int): aantal toilletten *
  - wordt standaard niet ingevuld
  - wordt ingevuld met waarde '7' nav een van de uitzonderingen bij een T2 profiel, waardoor er wel een dagelijks toilet mag uitgevoerd worden
     - ‘desoriëntatie’, Matig / ernstig  
     - ‘nachtelijke incontinentie’ vanaf een bepaalde score


Mogelijke waardes zijn :
| **profiel** | Omschrijving       | Aantal toilleten |
| ----------- | ------------------ | ---------------- |
| A           | Profiel A          | 7                |
| B           | Profiel B          | 7                |
| C           | Profiel C          | 7                |
| P2          | Palliatief Profiel | 2 (7*)           |
| P7          | Palliatief Profiel | 7                |
| PA          | Forfait PA         | 7                |
| PB          | Forfait PB         | 7                |
| PC          | Forfait PC         | 7                |
| PN          | Forfait PN         | 7                |
| PP          | Forfait PP         | 7                |
| T2          | Profiel N          | 2 (7*)           |
| T7          | Profiel N          | 7                |

```xml
<profielen>
	<externeid>…</externeid>
	<startdatum>…</startdatum>
	<einddatum>…</einddatum>
	<patientgegevens_id>…<patientgegevens_id>
	<profiel>…</profiel>
</profielen>
```


### Node lezingidentiteit
Dit is enkel van toepassing bij facturatie via electronische derdebetaler! Maw, niet bij facturatie naar een Medisch huis, verzekering, patiënt,…
-	Vanaf 1/10/2017 is er verplichte verificatie van de identiteit van de patiënt, bij toepassing van facturatie via electronische derdebetaler
-	**De richtlijnen omtrent “lezing identiteit node” in deze xml volgen 1-op-1 de richtlijnen van de ‘groene bijbel’ (instructies electronische facturatie)**
-	Elk bezoek bij elke patiënt, dat via electronische derdebetaler wordt gefactureerd, geeft dus aanleiding tot een “lezingidentiteit” node (vanaf 1/10/2017).
-	Zorgen die geen bezoek vereisen en autonoom voorkomen, geven ook aanleiding tot een “lezingidentiteit” node, maar dan met “typevandrageridentiteitsdocument” = 7 en “redengebruikvignet” = 1
-	TDM3 zal ter hoogte van de facturatie rekening houden met enkel de lezingen identiteit die van toepassing zijn.

De volgende gegevens zijn vereist.

* **externeid** (int): de patientid uit het versturende systeem refererend naar externid van patientgegevens.	Cfr de uitgevoerdezorg node 
* **patientgegevens_id** (int): De unieke nummer van de patiënt binnen dit bestand. Cfr de uitgevoerdezorg node 
* **redenmanueleinvoering** (int):
    * Mogelijke waardes
    1.  Niet van toepassing
    2. Gebruik identiteitsdocument zonder chip
    3. Onbeschikbaarheid kaartlezer
    4. Panne informaticasysteem 
* **datumuitvoering** (dateTime): de datum van het bezoek, cfr datumuitvoering veld in uitgevoerdezorg node
* **bezoeknummer** (int): Nummer van het bezoek. Cfr node uitgevoerdezorg.
* **datumlezingidentiteitsdocument** (dateTime): datum waarop het identiteitsdocument is ingelezen. Dit element moet steeds ingevuld worden, behalve indien “typevanlezingidentiteitsdocument” = 4 en redenmanueleinvoering = 3.
*	**identificatiepatient** (string): de rijksregisternr, insz van de rechthebbende, cfr patientgegeven node.
*	**typevanlezingidentiteitsdocument** (int): Mogelijke waardes
      1. Lezing van de chipkaart
      2. Lezing van de streepjescode
      3. Lezing QR code
      4. Manuele invoering
        - Bij gebruik van deze waarde moet in “redenmanueleinvoering“ de reden vermeld worden.
*	**typevandrageridentiteitsdocument** (int):
      1. Belgische elektronische identiteitskaart (of Kids-id)
      2. Elektronische vreemdelingenkaart
      3. Kid-id kaart (*)
         - mogelijk tot en met gefactureerde maand december 2016; vanaf gefactureerde maand januari 2017 moet waarde 1 gebruikt worden ingeval van Kids-id.
      4. ISI+ kaart
      5. Elektronisch verblijfsdocument
      6. Een nog geldige sociale identiteitskaart (SIS-kaart) (**)
         - Niet meer geldig vanaf 01/01/2017
      7. Een vignet met streepjescode
          - Bij gebruik van deze waarde, moet in element “redengebruikvignet” de reden vermeld worden.
      8. Attest van sociaal verzekerde
      9. Attest van verlies of diefstal van Belgische elektronische

*	**redengebruikvignet** (int):
    -	Mogelijke waardes
    1. Niet van toepassing
    2. Rechthebbende niet aanwezig tijdens verstrekking en gelijktijdige aanwezigheid van rechthebbende en zorgverlener niet reglementair vereist
    3. Rechthebbende bezit geen identiteitsdocument
*	**uurvanlezingidentiteitsdocument** (string): formaat HHmm
    -  Voorbeeld: 1510, 0109, 1305, etc
    -  dit element moet steeds ingevuld worden, behalve indien **“typevanlezingidentiteitsdocument”** = 4 en **“redenmanueleinvoering”** = 3
*	**rizivnummerverstrekker** (string): de rizivnummer van de verstrekker
*	**serienummervandedrager** (string): Het serienummer van de drager (eID-kaart, …) moet in dit element vermeld worden. Deze zone moet steeds ingevuld worden, ook ingeval van manuele invoering, behalve indien typevandrageridentiteitsdocument  = 7, 8 of 9.
*	**nummerbewijsstuk** (string): Het nummer van het bewijsstuk afgeleverd aan de patiënt moet in deze zone vermeld worden. Dit hoeft niet ingevuld te worden wanneer de TDM3 Facturatiedienst het bewijsstuk opmaak, dit is dus de standaard situatie!

### Type “metadata” en optioneel element metadata op patientgegeven en uitgevoerdezorg
**metadata** (type metadata, optioneel element): een “key-value” pair  (naam-waarde) van items waarbij optioneel meta informatie kan meegegeven. Dit laat toe om op een flexibel manier informatie mee te geven die niet direct te maken heeft met de tarificatie van prestaties, zonder steeds het XSD schema aan te passen.
Het metadata is volledig optioneel voor de software ontwikkelaar van het END. Welke informatie die wordt meegegeven is, zoals steeds, nav concrete afspraken.
Zowel **naam** als **waarde** zijn van type “string”.

```xml
<metadata>
      <item>
        <naam>venootschap</naam>
        <waarde>de zonnebloem</waarde>
      </item>
      <item>
        <naam>ronde</naam>
        <waarde>tour 1</waarde>
      </item>
    </metadata>
```

## Opmerkingen
### Het dateTime formaat
Dit formaat wordt als volgt gedefinëerd: JJJJ-MM-DDTHH:MM:SS.
- JJJJ: jaar (4 posities)
- MM: maand (2 posities)
- DD: dag (2 posities)
- T: vaste waarde, duidt aan dat de tijd volgt.
- HH: uur (2 posities)
- MM: minuten (2 posities)
- SS: seconden (2 posities)

### Benaming van de velden
Aangezien XML case-sensitive is moeten alle veldnamen in de juiste case staan zoals in de xsd! De opgegeven benamingen moeten dus letterlijk gebruikt worden.

### Het veld patientgegevens_id
Per patientgegeven-node bestaat er een unieke patientgegevens_id. Deze id moet op elke uitgevoerdezorg-node en op elk profielen-node van de patiënt ingevuld worden. Op basis van dit veld, samen met het veld ‘externeid’, worden dan de uitgevoerde zorgen en profielen gelinkt aan de patiënt. 

In elk XML-bestand wordt er begonnen met patientgegevens_id 1 voor de eerste patiënt.

### Het veld externeid
Per patientgegeven-node bestaat er een unieke externeid, afkomstig uit het versturende systeem. Deze id moet op elke uitgevoerdezorg-node en op elke profielen-node van de patiënt ingevuld worden. Op basis van dit veld, samen met het veld ‘patientgegevens_id’, worden dan de uitgevoerde zorgen en profielen gelinkt aan de patiënt.


## Volledig voorbeeld
Deze XML heeft 2 patiënten, met per patiënt 1 uitgevoerde zorg en 1 profiel. De eerste patiënt is een patiënt die aangesloten is bij een ziekenfonds, de tweede patiënt is verzekerd bij Fortis AG.

```xml
<pakketgegevens>
	<softwarehuis>
		<code>A001</code>
		<naam>AAAAA</naam>
		<telefoonnummer>012/345678</telefoonnummer>
		<email>info@aaaaa.be</email>
	</softwarehuis>
	<patientgegevens>
		<patientgegevens_id>1</patientgegevens_id>
		<externeid>23123</externeid>
		<naam>PEETERS</naam>
		<voornaam>PETER</voornaam>
		<geslacht>M</geslacht>
		<geboortedatum>1920-12-31T00:00:00           </geboortedatum>
		<straat>Kerkstraat</straat>
		<huisnummer>1</huisnummer>
		<postcode>9000</postcode>
		<gemeente>Gent</gemeente>
		<landcode>BE</landcode>
		<ziekenfondscode>131</ziekenfondscode>
		<stamnummer/>
		<risicocode1>110</risicocode1>
		<risicocode2>110</risicocode2>
		<ruraal>false</ruraal>
		<startdatumpal/>
		<rijksregisternummer>20123112345</rijksregisternummer>
	</patientgegevens>
	<patientgegevens>
		<patientgegevens_id>2</patientgegevens_id>
		<externeid>23045</externeid>
		<naam>JANSSEN</naam>
		<voornaam>JAN</voornaam>
		<geslacht>M</geslacht>
		<geboortedatum/>
		<straat>Kerkstraat</straat>
		<huisnummer>2</huisnummer>
		<postcode>9000</postcode>
		<gemeente>Gent</gemeente>
		<landcode>BE</landcode>
		<ziekenfondscode/>
		<stamnummer/>
		<risicocode1/>
		<risicocode2/>
		<ruraal>false</ruraal>
		<startdatumpal/>
		<rijksregisternummer>13456724561</rijksregisternummer>
	</patientgegevens> 
	<uitgevoerdezorg>
		<input_uitgevoerdezorg_id>1234</input_uitgevoerdezorg_id>
		<externeid>23123</externeid>
		<datumuitvoering>2006-05-01T17:00:00           </datumuitvoering>
		<datumvoorschrift>2006-04-25T00:00:00           </datumvoorschrift>
		<rizivnummervoorschrijver>10765119340</rizivnummervoorschrijver>
		<naamvoorschrijver>WITTERS KATRIJN</naamvoorschrijver>
		<remgeld/>
		<nomenclatuurnummer>423275</nomenclatuurnummer>
		<pseudocodenummer>000000</pseudocodenummer>
		<kb90bedrag/>
		<ziekenfondscode/>
		<rizivnummerverpleegkundige>148594426401</rizivnummerverpleegkundige>
		<patientgegevens_id>1</patientgegevens_id>
		<bezoeknummer>1</bezoeknummer>
		<rep_typebestemmeling_id>1</rep_typebestemmeling_id>
		<insuline>false</insuline>
		<vorige_factuur_id/>
		<derdebetalercode/>
		<derdebetalernaam/>
		<derdebetaleradres/>
		<polisnummer/>
		<dossiernummer/>
		<datumongeval/>
		<werkgever/>
		<naamverwijzing/>
		<datumverwijzing/>
	</uitgevoerdezorg>
	<uitgevoerdezorg>
		<input_uitgevoerdezorg_id>37400</input_uitgevoerdezorg_id>
		<externeid>23045</externeid>
		<datumuitvoering>2006-05-25T18:00:00           </datumuitvoering>
		<datumvoorschrift>2006-05-24T00:00:00           </datumvoorschrift>
		<rizivnummervoorschrijver>17264911340</rizivnummervoorschrijver>
		<naamvoorschrijver>VOSSEN C</naamvoorschrijver>
		<remgeld/>
		<nomenclatuurnummer>423275</nomenclatuurnummer>
		<pseudocodenummer>000000</pseudocodenummer>
		<kb90bedrag/>
		<ziekenfondscode/>
		<rizivnummerverpleegkundige>148946002401</rizivnummerverpleegkundige>
		<patientgegevens_id>2</patientgegevens_id>
		<bezoeknummer>1</bezoeknummer>
		<rep_typebestemmeling_id>2</rep_typebestemmeling_id>
		<insuline>false</insuline>
		<vorige_factuur_id/>
		<derdebetalercode>6</derdebetalercode>
		<derdebetalernaam>Fortis AG</derdebetalernaam>
		<derdebetaleradres>E.Jacqmainlaan</derdebetaleradres>
		<polisnummer/>
		<dossiernummer/>
		<datumongeval/>
		<werkgever/>
		<naamverwijzing/>
		<datumverwijzing/>
	</uitgevoerdezorg>
	<tarificatiedienst>
		<code>TDM3</code>
		<naam>TDM3</naam>
	</tarificatiedienst>
	<facturatieperiode>
		<jaar>2006</jaar>
		<maand>5</maand>
	</facturatieperiode>
	<vereniging>
		<code>7139</code>
		<naam>Mederi NV</naam>
	</vereniging>
	<prijsberekening>
		<tijdelijk>false</tijdelijk>
	</prijsberekening>
	<profielen>
		<externeid>23123</externeid>
		<startdatum>2006-01-01T00:00:00           </startdatum>
		<einddatum>2006-12-31T00:00:00           </einddatum>
		<patientgegevens_id>1</patientgegevens_id>
		<profiel>A</profiel>
	</profielen>
	<profielen>
		<externeid>23045</externeid>
		<startdatum>2006-04-12T00:00:00           </startdatum>
		<einddatum>2007-04-11T00:00:00           </einddatum>
		<patientgegevens_id>2</patientgegevens_id>
		<profiel>T7</profiel>
	</profielen>
</pakketgegevens>

```
