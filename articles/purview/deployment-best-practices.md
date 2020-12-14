---
title: Aanbevolen best practices voor implementatie
description: Dit artikel bevat aanbevolen procedures voor het implementeren van Azure controle sfeer liggen. Met Azure controle sfeer liggen kunnen gebruikers gegevens bronnen registreren, detecteren, begrijpen en gebruiken.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 1b2841f69ebe91dac748a4b2e24dc0c33756b1da
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400689"
---
# <a name="azure-purview-deployment-best-practices"></a>Aanbevolen procedures voor de implementatie van Azure controle sfeer liggen

In dit artikel worden algemene taken geïdentificeerd die u kunnen helpen bij het implementeren van controle sfeer liggen in productie. Deze taken kunnen in fasen worden uitgevoerd, in de loop van een maand of meer. Zelfs organisaties die al controle sfeer liggen hebben geïmplementeerd, kunnen deze hand leiding gebruiken om ervoor te zorgen dat ze het meeste uit hun investering halen.

Een goed geplande implementatie van een Data governance-platform (zoals Azure controle sfeer liggen) kan de volgende voor delen bieden:

- Betere gegevens detectie
- Verbeterde samen werking in analytic
- Gemaximaliseerd rendement op investeringen.

## <a name="prerequisites"></a>Vereisten

* Toegang tot Microsoft Azure met een ontwikkelings-of productie abonnement
* Mogelijkheid om Azure-resources te maken, waaronder controle sfeer liggen
* Toegang tot gegevens bronnen, zoals Azure Data Lake Storage of Azure SQL in test-, ontwikkelings-of productie omgevingen
  * Voor Data Lake Storage is de vereiste rol die moet worden gescand, de rol lezer
  * Voor SQL moet de identiteit query's kunnen uitvoeren in tabellen voor het bemonsteren van classificaties
* Toegang tot Azure Security Center of de mogelijkheid om samen te werken met Security Center beheerder voor het labelen van gegevens

## <a name="identify-objectives-and-goals"></a>Doel stellingen en doel stellingen identificeren

Veel organisaties hebben hun data governance-traject gestart door afzonderlijke oplossingen te ontwikkelen die voldoen aan specifieke vereisten van geïsoleerde groepen en gegevens domeinen binnen de hele organisatie. Hoewel ervaring kan variëren afhankelijk van de branche, het product en de cultuur, vinden de meeste organisaties het lastig om consistente besturings elementen en beleids regels voor deze typen oplossingen te onderhouden.

Enkele van de common data governance-doel stellingen die u in de vroege fasen wilt identificeren, zijn onder andere:

* De bedrijfs waarde van uw gegevens maximaliseren
* Het inschakelen van een gegevens cultuur waarbij gegevens gebruikers eenvoudig gegevens kunnen vinden, interpreteren en vertrouwen
* De samen werking tussen verschillende bedrijfs eenheden verhogen om een consistente gegevens ervaring te bieden
* Innovatie bevorderen door de gegevens analyse te versnellen om de voor delen van de cloud te benutten
* Minder tijd om gegevens te detecteren via self-service opties voor verschillende vaardigheden groepen
* De time-to-Market beperken voor het leveren van analyse oplossingen waarmee de service naar hun klanten wordt verbeterd
* Het verminderen van de operationele Risico's die worden veroorzaakt door het gebruik van toepassingsspecifieke hulpprogram ma's en niet-ondersteunde technologieën

De algemene benadering is het opsplitsen van deze overkoepelend-doel stellingen in verschillende categorieën en doel stellingen. Een aantal voorbeelden:

|Categorie|Doel|
|---------|---------|
|Detectie|Gebruikers met beheerders rechten moeten Azure en niet-Azure-gegevens bronnen (met inbegrip van on-premises bronnen) kunnen scannen om automatisch informatie over de gegevensassets te verzamelen.|
|Classificatie|Het platform moet gegevens automatisch classificeren op basis van een steek proef van de gegevens en hand matige onderdrukking toestaan met aangepaste classificaties.|
|Verbruik|De zakelijke gebruikers moeten informatie kunnen vinden over elk activum voor zowel zakelijke als technische meta gegevens.|
|Herkomst|Elk activum moet een grafische weer gave van de onderliggende gegevens sets tonen, zodat de gebruikers inzicht hebben in de oorspronkelijke bronnen en welke wijzigingen zijn aangebracht.|
|Samenwerking|Het platform moet gebruikers in staat stellen om samen te werken door aanvullende informatie over elke gegevens Asset te verstrekken.|
|Rapporten|De gebruikers moeten rapportages kunnen weer geven over de gegevens die ze nodig hebben, inclusief gevoelige gegevens en gegevens die extra verrijkend moeten zijn.|
|Gegevens-governance|Het platform moet de beheerder toestaan beleid voor toegangs beheer te definiëren en de gegevens toegang automatisch af te dwingen op basis van elke gebruiker.|
|Werkstroom|Het platform moet de mogelijkheid hebben om werk stromen te maken en te wijzigen, zodat het eenvoudig kan worden uitgeschaald en verschillende taken binnen het platform kunnen worden geautomatiseerd.|
|Integratie|Andere technologieën van derden, zoals ticketing of indeling, moeten kunnen worden geïntegreerd in het platform via script-of REST-Api's.|

## <a name="top-questions-to-ask"></a>Beste vragen om te vragen

Zodra uw organisatie akkoord gaat met de doel stellingen op hoog niveau en de doel stellingen, is er een groot aantal vragen van meerdere groepen. Het is essentieel dat u deze vragen kunt verzamelen om een abonnement te kunnen opstellen om alle problemen op te lossen. Enkele voor beelden van vragen die u tijdens de eerste fase kunt uitvoeren:

1. Wat zijn de belangrijkste gegevens bronnen en gegevens systemen van de organisatie?
2. Wat zijn de opties voor gegevens bronnen die nog niet worden ondersteund door controle sfeer liggen?
3. Hoeveel controle sfeer liggen-exemplaren hebt u nodig?
4. Wie zijn de gebruikers?
5. Wie kan nieuwe gegevens bronnen scannen?
6. Wie kan inhoud in controle sfeer liggen wijzigen?
7. Welk proces kan ik gebruiken om de kwaliteit van de gegevens in controle sfeer liggen te verbeteren?
8. Hoe kan ik een Boots trap van het platform doen met bestaande kritieke assets, woordenlijst termen en contact personen?
9. Hoe kan ik integreren met bestaande systemen?
10. Hoe kunt u feedback verzamelen en een duurzaam proces bouwen?

Hoewel u het antwoord op de meeste van deze vragen mogelijk niet meteen kunt beantwoorden, kan uw organisatie het project op dit moment kaderen en ervoor zorgen dat aan alle vereisten van ' behoeften ' kan worden voldaan.

## <a name="include-the-right-stakeholders"></a>Neem de juiste belanghebbenden op

Om ervoor te zorgen dat de implementatie van controle sfeer liggen voor de hele onderneming is geslaagd, is het belang rijk dat de juiste belanghebbenden betrokken zijn. Er zijn slechts enkele mensen betrokken bij de eerste fase. Aangezien de scope groter wordt, hebt u extra personen nodig om bij het project bij te dragen en feedback te geven.

Enkele belang rijke belanghebbenden die u mogelijk wilt gebruiken:

|Persona|Rollen|
|---------|---------|
|**Chief data Officer**|De CDO heeft een scala aan functies die gegevens beheer, gegevens kwaliteit, hoofd gegevens beheer, gegevens wetenschappen, business intelligence en het maken van gegevens strategie mogelijk achten. Ze kunnen de sponsor zijn van het controle sfeer liggen-implementatie project.|
|**Domein/eigenaar van het bedrijf**|Een zakelijke persoon die van invloed is op het gebruik van hulpprogram ma's en budget beheer heeft|
|**Gegevensanalist**|Kan een bedrijfs probleem melden en gegevens analyseren zodat leidinggevenden zakelijke beslissingen kunnen nemen|
|**Data architect**|Data bases ontwerpen voor bedrijfskritische line-of-Business-Apps, samen met het ontwerpen en implementeren van gegevens beveiliging|
|**Data Engineer**|De gegevens stack actief maken en onderhouden, gegevens uit verschillende bronnen ophalen, gegevens integreren en voorbereiden, gegevens pijplijnen instellen|
|**Data Scientist**|Bouw analytische modellen en stel gegevens producten in die moeten worden geopend door Api's|
|**DB-beheerder**|Aan data base gerelateerde incidenten en aanvragen binnen Service Level Agreements (Sla's) vastleggen en oplossen; Kan gegevens pijplijnen instellen|
|**DevOps**|De ontwikkeling en implementatie van line-of-business-toepassingen; kan schrijf scripts en indelings mogelijkheden bevatten|
|**Specialist gegevens beveiliging**|Evalueer de algehele netwerk-en gegevens beveiliging, die betrekking heeft op gegevens die beschikbaar zijn in en uit controle sfeer liggen|

## <a name="identify-key-scenarios"></a>Belang rijke scenario's identificeren

Controle sfeer liggen kan worden gebruikt voor het centraal beheren van gegevens beheer over de data-en Cloud-en on-premises omgeving van een organisatie. Als u een succes volle implementatie wilt uitvoeren, moet u belang rijke scenario's identificeren die essentieel zijn voor het bedrijf. Deze scenario's kunnen cross-Business Unit-grenzen hebben of invloed hebben op meerdere gebruikers personen upstream of downstream.

Deze scenario's kunnen op verschillende manieren worden geschreven, maar u moet ten minste de volgende vijf dimensies gebruiken:

1. Wie zijn de gebruikers?
2. Bron systeem: wat zijn de gegevens bronnen, zoals Azure Data Lake Storage Gen2 of Azure SQL Database?
3. Impact gebied: wat is de categorie van dit scenario?
4. Gedetailleerde scenario's: hoe de gebruikers controle sfeer liggen gebruiken om problemen op te lossen?
5. Verwachte uitkomst: wat zijn de succes criteria?

De scenario's moeten specifiek, actiebaar en uitvoerbaar zijn met meet bare resultaten. Enkele voorbeeld scenario's die u kunt gebruiken:

|Scenario|Detail|Persona|
|---------|---------|---------|
|Bedrijfs kritieke assets catalogiseren|Ik moet informatie over elke gegevens sets hebben om een goed inzicht te hebben in wat het is. Dit scenario bevat zowel zakelijke als technische meta gegevens over de gegevensset in de catalogus. De gegevens bronnen omvatten Azure Data Lake Storage Gen2, Azure Synapse DW en/of Power BI. Dit scenario omvat ook een on-premises resource, zoals SQL Server.|Bedrijfs analist, data wetenschapper, data Engineer|
|Bedrijfs kritieke assets detecteren|Ik moet een zoek programma hebben dat alle meta gegevens in de catalogus kan doorzoeken. Ik moet in staat zijn om met een technische term te zoeken, met een eenvoudige of complexe zoek opdracht met behulp van joker tekens.|Bedrijfs analist, data wetenschapper, data Engineer, gegevens beheerder|
|Gegevens bijhouden om de oorsprong te begrijpen en problemen met gegevens oplossen|Ik moet gegevens afkomst hebben om gegevens in rapporten, voor spellingen of modellen weer te kunnen volgen op de oorspronkelijke bron en inzicht te krijgen in de wijzigingen en waar de gegevens zich bevinden tijdens de levens cyclus van de gegevens. Dit scenario moet gegevens pijplijnen met prioriteiten Azure Data Factory en Databricks ondersteunen.|Data Engineer, data wetenschapper|
|Verrijkende meta gegevens op essentiële gegevensassets|Ik moet de gegevensset in de catalogus verrijken met technische meta gegevens die automatisch worden gegenereerd. Classificatie en labeling zijn enkele voor beelden.|Data Engineer, domein/bedrijfs eigenaar|
|Beheerst data assets met een gebruiks vriendelijke gebruikers ervaring|Ik moet een zakelijke woorden lijst hebben voor bedrijfsspecifieke meta gegevens. Zakelijke gebruikers kunnen controle sfeer liggen voor selfservice scenario's gebruiken om aantekeningen te maken op hun gegevens en de gegevens gemakkelijk te kunnen detecteren via een zoek opdracht.|Domein/bedrijfs eigenaar, bedrijfs analist, data wetenschapper, data Engineer|

## <a name="deployment-models"></a>Implementatiemodellen

Als u slechts één kleine groep hebt die gebruikmaakt van controle sfeer liggen met gebruik van elementaire verbruiks cases, kan de aanpak zo eenvoudig zijn dat er één controle sfeer liggen-exemplaar is om de hele groep te onderhouden. U kunt echter ook afvragen of uw organisatie meer dan één controle sfeer liggen-exemplaar nodig heeft. En als u meerdere controle sfeer liggen-instanties gebruikt, hoe kunnen werk nemers de activa van de ene fase naar de andere promo veren.

### <a name="determine-the-number-of-purview-instances"></a>Het aantal controle sfeer liggen-instanties bepalen

In de meeste gevallen mag er slechts één controle sfeer liggen-account zijn voor de hele organisatie. Deze aanpak heeft Maxi maal voor deel van de ' netwerk effecten ', waarbij de waarde van het platform exponentieel toeneemt als een functie van de gegevens die zich in het platform bevinden.

Er zijn echter uitzonde ringen op dit patroon:

1. **Nieuwe configuraties testen** : organisaties willen mogelijk meerdere instanties maken voor het testen van scan configuraties of-classificaties in geïsoleerde omgevingen. Hoewel er sprake is van een functie voor het maken van versies in sommige gebieden van het platform, zoals de verklarende woorden lijst, is het eenvoudiger om een ' beschik bare ' exemplaar te hebben om vrij te testen.
2. Het **scheiden van test, pre-productie en productie** : organisaties willen verschillende platformen maken voor verschillende soorten gegevens die zijn opgeslagen in de verschillende omgevingen. Het wordt niet aanbevolen omdat dit soort gegevens verschillende inhouds typen zijn. U kunt de woordenlijst term gebruiken op het hoogste hiërarchie niveau of de categorie om inhouds typen te scheiden.
3. **Conglomeraat en federatief model** – conglomeraten hebben vaak veel Business Units (BUs) die afzonderlijk werken, en, in sommige gevallen, delen ze zelfs geen facturering met elkaar. In dergelijke gevallen wordt het maken van een controle sfeer liggen-exemplaar voor elke BU door de organisatie beëindigd. Dit model is niet ideaal, maar kan nood zakelijk zijn, met name omdat BUs vaak niet bereid is om facturering te delen.
4. **Naleving** : er zijn een aantal strikte nalevings regelingen waarbij zelfs de meta gegevens worden behandeld als gevoelig en deze moeten zich in een specifieke geografie bevinden. Als een bedrijf meerdere geographs heeft, heeft de enige oplossing meerdere controle sfeer liggen-instanties, een voor elke geografie.

### <a name="create-a-process-to-move-to-production"></a>Een proces maken om over te stappen op productie

Sommige organisaties kunnen ervoor kiezen om dingen eenvoudig te blijven door te werken met één productie versie van controle sfeer liggen. Ze hoeven waarschijnlijk niet verder te gaan dan detectie-, zoek-en blader scenario's. Als sommige assets onjuiste woordenlijst termen hebben, is het erg gezinder om uw eigen gebruikers te laten corrigeren. De meeste organisaties die controle sfeer liggen willen implementeren in verschillende bedrijfs eenheden, willen echter een vorm van proces en controle hebben.

Een ander belang rijk aspect dat u in uw productie proces moet meenemen, is de manier waarop classificaties en labels kunnen worden gemigreerd. Controle sfeer liggen heeft meer dan 90 systeem classificaties. U kunt systeem-of aangepaste classificaties Toep assen op bestands-, tabel-of kolom assets. Classificaties zijn vergelijkbaar met-labels en worden gebruikt om de inhoud te markeren en te identificeren van een specifiek type dat is gevonden in uw data-erfgoed tijdens het scannen. Gevoeligheids labels worden gebruikt voor het identificeren van de categorieën van classificatie typen in uw bedrijfs gegevens en vervolgens groeperen van de beleids regels die u op elke categorie wilt Toep assen. Er wordt gebruikgemaakt van dezelfde gevoelige informatie typen als Microsoft 365, zodat u uw bestaande beveiligings beleid en-beveiliging kunt uitrekken in uw hele inhoud en gegevens. Het kan documenten scannen en automatisch classificeren. Als u bijvoorbeeld een bestand met de naam multiple.docx hebt en dit een nationaal ID-nummer in de inhoud heeft, zal controle sfeer liggen de classificatie, zoals het nationale identificatie nummer van de EU, toevoegen op de pagina Asset detail.

In controle sfeer liggen zijn er verschillende gebieden waar de catalogus beheerders moeten zorgen voor consistentie en best practices voor onderhoud tijdens de levens cyclus:

* Gegevensassets **: gegevens** bronnen moeten opnieuw worden gescand tussen omgevingen. Het is niet raadzaam om alleen in ontwikkeling te scannen en ze vervolgens opnieuw te genereren met behulp van Api's in productie. De belangrijkste reden is dat de controle sfeer liggen-scanners veel meer ' bedrading ' achter de schermen van de gegevensassets, wat complex kan zijn om ze te verplaatsen naar een ander controle sfeer liggen-exemplaar. Het is veel eenvoudiger om simpelweg dezelfde gegevens bron toe te voegen aan de productie en de bronnen opnieuw te scannen. Het algemene best practice is een documentatie te hebben over alle scans, verbindingen en verificatie mechanismen die worden gebruikt.
* **Regel sets scannen** : dit is uw verzameling regels die zijn toegewezen aan een specifieke scan, zoals het bestands type en de classificaties die moeten worden gedetecteerd. Als u niet veel scan regel sets hebt, is het mogelijk om ze opnieuw hand matig opnieuw te maken via de productie. Hiervoor is een intern proces en een goede documentatie vereist. Als u regel matig wijzigingen instelt, kan dit echter worden opgelost door de REST API route te verkennen.
* **Aangepaste classificaties** : uw classificaties worden mogelijk niet regel matig gewijzigd. Tijdens de eerste fase van de implementatie kan het enige tijd duren om inzicht te krijgen in verschillende vereisten voor aangepaste classificaties. Maar als dit eenmaal is vereffend, is dit weinig te wijzigen. Daarom is de aanbeveling hier om aangepaste classificaties hand matig te migreren of de REST API te gebruiken.
* **Verklarende** woorden lijst: het is mogelijk om termen van een woorden lijst te exporteren en te importeren via de UX. Voor automatiserings scenario's kunt u ook de REST API gebruiken.
* **Beleids regels** voor het instellen van patronen: deze functionaliteit is zeer geavanceerd voor typische organisaties die u kunt Toep assen. In sommige gevallen heeft uw Azure Data Lake Storage naamgevings conventies voor mappen en een specifieke structuur die problemen kan veroorzaken voor controle sfeer liggen om de resourceset te genereren. Mogelijk wilt u ook de bouw van de resourceset met aanvullende aanpassingen aanpassen aan de bedrijfs behoeften. Voor dit scenario is het het beste om alle wijzigingen bij te houden via REST API, en de wijzigingen te documenteren via extern platform voor versie beheer.
* **Roltoewijzing** : dit is de locatie waar u bepaalt wie toegang heeft tot controle sfeer liggen en welke machtigingen ze hebben. Controle sfeer liggen heeft ook REST API voor het ondersteunen van het exporteren en importeren van gebruikers en rollen, maar dit is niet de Atlas API-compatibel. De aanbeveling is een Azure-beveiligings groep toe te wijzen en in plaats daarvan het groepslid maatschap te beheren.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Plannen en implementeren van verschillende integratie punten met controle sfeer liggen

Waarschijnlijk heeft een rijpe organisatie al een bestaande Data Catalog. De belang rijke vraag is of u de bestaande technologie wilt blijven gebruiken en met controle sfeer liggen wilt synchroniseren. Met controle sfeer liggen kunt u informatie publiceren via de Atlas-Api's, maar deze zijn niet bedoeld om dit soort scenario te ondersteunen. Sommige organisaties kunnen het gebruik van controle sfeer liggen in eerste instantie zelf bepalen door te migreren over de bestaande gegevensassets van andere oplossingen voor gegevens catalogi. Dit kan worden gedaan via de Atlas Api's als een eenrichtings benadering. Voor synchronisatie tussen verschillende catalogus technologieën mag niet worden overwogen in het ontwerp voor de lange termijn. Wat er meestal gebeurt, is dat elke bedrijfs eenheid de bestaande oplossingen voor oudere gegevensassets blijft gebruiken terwijl controle sfeer liggen wordt gebruikt om te scannen op nieuwere gegevens bronnen.

Voor andere integratie scenario's zoals tickets, aangepaste gebruikers interface en indeling kunt u gebruikmaken van Atlas Api's en Kafka-eind punten. Over het algemeen zijn er vier integratie punten met controle sfeer liggen:

* **Gegevens Asset** : Hiermee kan controle sfeer liggen de assets van een archief scannen om te inventariseren wat deze assets zijn en alle direct beschik bare meta gegevens over hen verzamelen. Voor SQL kan dit bijvoorbeeld een lijst zijn met Db's, tabellen, opgeslagen procedures, weer gaven en configuratie gegevens over hen op locatie als `sys.tables` . Voor iets als Azure Data Factory (ADF) kan dit alle pijp lijnen opsommen en gegevens ophalen op het moment dat ze werden gemaakt, de laatste uitvoering, de huidige status.
* **Afkomst** : Hiermee kan controle sfeer liggen informatie verzamelen van een analyse/gegevensmutatiesysteem over hoe gegevens worden verplaatst. In sommige gevallen bijvoorbeeld Spark kan er informatie worden verzameld van de uitvoering van een notitie blok om te zien welke gegevens de notitie blok heeft opgenomen, hoe deze is getransformeerd en waar deze wordt gegenereerd. In het geval van SQL kan het de query logboeken analyseren naar reverse-engineering waarmee mutatie bewerkingen werden uitgevoerd en wat ze hebben gedaan. We ondersteunen zowel push-als pull-based afkomst afhankelijk van de behoeften.
* **Classificatie** : Hiermee kunnen controle sfeer liggen fysieke voor beelden van gegevens bronnen nemen en deze uitvoeren via ons classificatie systeem. In het classificatie systeem worden de semantiek van een stukje gegevens genoteerd. Het is bijvoorbeeld mogelijk dat een bestand een Parquet-bestand is dat drie kolommen bevat en dat het derde een teken reeks is. Maar de classificaties die we uitvoeren op de voor beelden, geven ons aan dat de teken reeks een naam, adres of telefoon nummer is. Als u dit integratie punt verlicht, hebben we gedefinieerd hoe controle sfeer liggen objecten zoals notebooks, pijp lijnen, Parquet bestanden, tabellen en containers kan openen.
* **Inge sloten ervaring** : producten die een studio-ervaring hebben (zoals ADF, Synapse, SQL Studio, aan pbi en Dynamics), willen gebruikers doorgaans de mogelijkheid bieden om gegevens te ontdekken waarmee ze willen communiceren en ook locaties vinden voor het uitvoeren van gegevens. De catalogus van controle sfeer liggen kan helpen bij het versnellen van deze ervaringen door een insluitings ervaring te bieden. Deze ervaring kan zich voordoen op de API of het UX-niveau op de optie van de partner. Door een aanroep van controle sfeer liggen in te sluiten, kan de organisatie profiteren van de toewijzing van de gegevens van controle sfeer liggen om gegevensassets te vinden, afkomst te controleren, schema's te bekijken, beoordelingen te bekijken, contact personen, enzovoort.

## <a name="phase-1-pilot"></a>Fase 1: pilot

In deze fase moet controle sfeer liggen worden gemaakt en geconfigureerd voor een zeer kleine set gebruikers. Normaal gesp roken is het slechts een groep van 2-3 mensen die samen werken om te worden uitgevoerd met end-to-end scenario's. Ze worden beschouwd als de belangen van controle sfeer liggen in hun organisatie. Het belangrijkste doel van deze fase is om ervoor te zorgen dat aan belang rijke functionele eisen kan worden voldaan en dat de juiste belanghebbenden op de hoogte zijn van het project.

### <a name="tasks-to-complete"></a>Taken die moeten worden voltooid

|Taak|Detail|Duur|
|---------|---------|---------|
|Verzamelen & akkoord met vereisten|Discussie met alle belanghebbenden om een volledige set vereisten te verzamelen. Verschillende personen moeten deel nemen aan een subset van vereisten die moeten worden voltooid voor elke fase van het project.|1 week|
|Het Start pakket instellen|Ga door naar [controle sfeer liggen Quick Start](create-catalog-portal.md) en stel de [controle sfeer liggen-Start pakket](tutorial-scan-data.md) in op demo controle sfeer liggen voor alle betrokkenen.|1 dag|
|Navigeren in controle sfeer liggen|Meer informatie over het gebruik van controle sfeer liggen op de start pagina.|1 dag|
|ADF configureren voor afkomst|Bepaal de belangrijkste pijp lijnen en gegevensassets. Verzamel alle informatie die nodig is om verbinding te maken met een intern ADF-account.|1 dag|
|Een gegevens bron scannen, zoals Azure Data Lake Storage|Voeg de gegevens bron toe en stel een scan in. Zorg ervoor dat alle assets zijn gedetecteerd met de scan.|2 dag|
|Zoeken en bladeren|Eind gebruikers toestaan controle sfeer liggen te openen en end-to-end Zoek-en blader scenario's uit te voeren.|1 dag|

### <a name="acceptance-criteria"></a>Acceptatie criteria

* Het controle sfeer liggen-account is gemaakt in het organisatie abonnement onder de Tenant van de organisatie.
* Een kleine groep gebruikers met meerdere rollen heeft toegang tot controle sfeer liggen.
* Controle sfeer liggen is zo geconfigureerd dat er ten minste één gegevens bron wordt gescand.
* Gebruikers moeten de sleutel waarden van controle sfeer liggen kunnen extra heren, zoals:
  * Zoeken en bladeren
  * Herkomst
* Gebruikers moeten eigen activa-eigendom kunnen toewijzen op de pagina Asset.
* Presentatie en demo om de belangrijkste belanghebbenden op de hoogte te brengen.
* Inkopen vanuit het beheer om aanvullende resources voor de MVP-fase goed te keuren.

## <a name="phase-2-minimum-viable-product"></a>Fase 2: Mini maal levensvat bare product

Zodra u de overeengekomen vereisten en deel nemers hebt voor het onboarden van controle sfeer liggen, is de volgende stap het werken met een mini maal levensvat bare product (MVP). In deze fase breidt u het gebruik van controle sfeer liggen uit voor meer gebruikers die horizon taal en verticaal extra behoeften zullen hebben. Er zijn belang rijke scenario's waaraan horizon taal moet worden voldaan voor alle gebruikers, zoals de terminologie, zoeken en bladeren. Er zijn ook verticale vereisten voor elke bedrijfs eenheid of groep om specifieke end-to-end scenario's, zoals afkomst van Azure Data Lake Storage naar Azure Synapse DW, te behandelen naar Power BI.

### <a name="tasks-to-complete"></a>Taken die moeten worden voltooid

|Taak|Detail|Duur|
|---------|---------|---------|
|[Azure Synapse Analytics scannen](register-scan-azure-synapse-analytics.md)|Begin met het voorbereiden van uw database bronnen en het controleren van de belangrijkste assets|2 dagen|
|[Aangepaste classificaties en regels maken](create-a-custom-classification-and-classification-rule.md)|Zodra uw assets zijn gescand, kunnen uw gebruikers zich afleiden dat er meer use cases zijn voor meer classificatie naast de standaard classificaties van controle sfeer liggen.|2-4 weken|
|[Power BI scannen](register-scan-power-bi-tenant.md)|Als uw organisatie gebruikmaakt van Power BI, kunt u Power BI scannen om alle gegevensassets te verzamelen die worden gebruikt door gegevens wetenschappers of gegevens analisten die vereisten hebben om afkomst op te halen uit de opslaglaag.|1-2 weken|
|[Termen van woorden lijst importeren](how-to-create-import-export-glossary.md)|In de meeste gevallen kan uw organisatie al een verzameling woorden lijst en termen toewijzing aan assets ontwikkelen. Hiervoor is een import proces vereist in controle sfeer liggen via een CSV-bestand.|1 week|
|Contacten toevoegen aan assets|Voor de beste activa wilt u mogelijk een proces instellen om andere personen toe te staan om contact personen toe te wijzen of te importeren via REST-Api's.|1 week|
|Gevoelige labels toevoegen en scannen|Dit kan optioneel zijn voor sommige organisaties, afhankelijk van het gebruik van labelen vanuit M365.|1-2 weken|
|Classificatie en gevoelige inzichten ophalen|Voor rapportage en inzicht in controle sfeer liggen hebt u toegang tot deze functionaliteit om verschillende rapporten op te halen en een presentatie te kunnen beheren.|1 dag|
|Aan de slag toevoegen gebruikers die controle sfeer liggen Managed users gebruiken|Voor deze stap moet de controle sfeer liggen-beheerder samen werken met de Azure Active Directory-beheerder om nieuwe beveiligings groepen te kunnen maken voor het verlenen van toegang tot controle sfeer liggen.|1 week|

### <a name="acceptance-criteria"></a>Acceptatie criteria

* Onboarding van een grotere groep gebruikers naar controle sfeer liggen (50 +)
* Bedrijfs kritieke gegevens bronnen scannen
* Alle kritieke woordenlijst termen importeren en toewijzen
* Het testen van belang rijke labels op belangrijkste assets is geslaagd
* Er is voldaan aan de minimale scenario's voor gebruikers van de betrokken business units

## <a name="phase-3-pre-production"></a>Fase 3: pre-productie

Zodra de MVP-fase is verstreken, is het tijd om de voorafgaande productie mijlpaal te plannen. Uw organisatie kan ervoor kiezen om een afzonderlijk exemplaar van controle sfeer liggen te hebben voor preproductie en productie, of om hetzelfde exemplaar te behouden, maar de toegang te beperken. In deze fase wilt u mogelijk ook scannen op on-premises gegevens bronnen, zoals SQL Server, toevoegen. Als er sprake is van een hiaat in gegevens bronnen die niet worden ondersteund door controle sfeer liggen, is het tijd om de Atlas API te verkennen om meer opties te begrijpen.

### <a name="tasks-to-complete"></a>Taken die moeten worden voltooid

|Taak|Detail|Duur|
|---------|---------|---------|
|Uw Scan verfijnen met behulp van de scan regel set|Uw organisatie heeft veel gegevens bronnen voor de pre-productie. Het is belang rijk om de belangrijkste criteria voor het scannen vooraf te definiëren, zodat de classificaties en de bestands extensie consistent op het bord kunnen worden toegepast.|1-2 dagen|
|Beschik baarheid van regio voor scan bepalen|Afhankelijk van de regio van de gegevens bronnen en de organisatorische vereisten op het gebied van naleving en beveiliging, wilt u wellicht overwegen welke regio's beschikbaar moeten zijn voor het scannen.|1 dag|
|Firewall concept voor het scannen|Voor deze stap is het mogelijk om te verkennen hoe de organisatie de firewall configureert en hoe controle sfeer liggen zichzelf kan verifiëren voor toegang tot de gegevens bronnen voor het scannen.|1 dag|
|Concept over persoonlijke koppelingen begrijpen bij het scannen|Als uw organisatie gebruikmaakt van een persoonlijke koppeling, moet u de basis van netwerk beveiliging zodanig indelen dat persoonlijke koppelingen worden opgenomen als onderdeel van de vereisten.|1 dag|
|[On-premises SQL Server scannen](register-scan-on-premises-sql-server.md)|Dit is optioneel als u on-premises SQL Server hebt. Voor de scan is het instellen van [zelf-hostende Integration runtime](manage-integration-runtimes.md) vereist en het toevoegen van SQL Server als een gegevens bron.|1-2 weken|
|Controle sfeer liggen-REST API gebruiken voor integratie scenario's|Als u vereisten hebt om controle sfeer liggen te integreren met andere technologieën van derden, zoals een indelings-of ticket systeem, kunt u REST API gebied verkennen.|1-4 weken|
|Meer informatie over controle sfeer liggen prijzen|In deze stap wordt de organisatie belang rijke financiële informatie geboden om beslissing te nemen.|1-5 dagen|

### <a name="acceptance-criteria"></a>Acceptatie criteria

* Er is ten minste één bedrijfs eenheid met alle gebruikers onboarding
* On-premises gegevens bron scannen, zoals SQL Server
* Haalbaarheids test ten minste één integratie scenario met REST API
* Een plan volt ooien om naar de productie te gaan, die belang rijke aspecten van de infra structuur en de beveiliging moet omvatten

## <a name="phase-4-production"></a>Fase 4: productie

De bovenstaande fasen moeten worden gevolgd om een effectief Information governance te maken. Dit is de basis voor betere governance-Program ma's. Data governance helpt uw organisatie voor te bereiden op de groeiende trends, zoals AI, Hadoop, IoT en block chain. Het is slechts de begin datum van veel dingen en analyses, en er zijn er nog veel meer die kunnen worden besproken. De uitkomst van deze oplossing levert het volgende:

* **Gericht bedrijf** : een oplossing die is afgestemd op de zakelijke vereisten en scenario's met betrekking tot technische vereisten.
* **Toekomstige kant** -en-klare oplossing zorgt voor een optimaal gebruik van de standaard functies van het platform en gebruikt gestandaardiseerde industrie procedures voor configuratie-of script activiteiten ter ondersteuning van de voor uitgangen/ontwikkeling van het platform.

### <a name="tasks-to-complete"></a>Taken die moeten worden voltooid

|Taak|Detail|Duur|
|---------|---------|---------|
|Productie gegevens bronnen scannen met ingeschakelde firewall|Als dit optioneel is wanneer er een firewall is ingesteld, is het belang rijk dat u de opties voor het beveiligen van uw infra structuur kunt verkennen.|1-5 dagen|
|Persoonlijke koppeling inschakelen|Als dit optioneel is wanneer een persoonlijke koppeling wordt gebruikt. Als dat niet het geval is, kunt u dit overs Laan als privé is ingeschakeld.|1-5 dagen|
|Geautomatiseerde werk stroom maken|Werk stroom is belang rijk voor het automatiseren van proces, zoals goed keuring, escalatie, beoordeling en probleem beheer.|2-3 weken|
|Documentatie over de bewerking|Data Governance is geen eenmalig project. Het is een doorlopend programma voor het maken van brandstof gegevens op basis van de gegevensgestuurde beslissingen en het creëren van mogelijkheden voor bedrijven. Het is essentieel om de belangrijkste procedure en bedrijfs normen te documenteren.|1 week|

### <a name="acceptance-criteria"></a>Acceptatie criteria

* Alle Business Unit en hun gebruikers worden onboardd
* Voldoen aan de infra structuur-en beveiligings vereisten voor productie
* Voldoet aan alle use cases die vereist zijn voor de gebruikers

## <a name="platform-hardening"></a>Platform beveiliging

Er kunnen extra beveiligings stappen worden uitgevoerd:

* Beveiligings postuur verhogen door scan in te scha kelen op Firewall bronnen of een persoonlijke koppeling te gebruiken
* Bereik scan nauw keurig afstemmen om de scan prestaties te verbeteren
* REST-Api's gebruiken om essentiële meta gegevens en eigenschappen voor back-up en herstel te exporteren
* Werk stroom gebruiken om tickets en gebeurtenissen te automatiseren om menselijke fouten te voor komen

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: het Start pakket uitvoeren en gegevens scannen](tutorial-scan-data.md)
- [Zelf studie: door de start pagina navigeren en naar een Asset zoeken](tutorial-asset-search.md)