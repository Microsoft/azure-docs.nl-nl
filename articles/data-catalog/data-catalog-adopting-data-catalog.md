---
title: Aanpak en proces voor het overstappen op Azure Data Catalog
description: Dit artikel bevat een benadering en proces voor organisaties die overwegen om over te stappen op Azure Data Catalog, met inbegrip van het definiëren van een visie, het identificeren van de belangrijkste zakelijke gebruiksvoorbeelden en het kiezen van een proefproject.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ebfaa59f1800da084d611f6e050a59da63a7f195
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674747"
---
# <a name="approach-and-process-for-adopting-azure-data-catalog"></a>Aanpak en proces voor het overstappen op Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Dit artikel helpt u aan de slag te gaan met de overstap op **Azure Data Catalog** in uw organisatie. Als u wilt overstappen op **Azure Data Catalog**, richt u zich op drie essentiële items: het definiëren van uw visie, het identificeren van de belangrijkste zakelijke gebruiksvoorbeelden binnen uw organisatie en het kiezen van een proefproject.

## <a name="introducing-the-azure-data-catalog"></a>Introductie van de Azure Data Catalog

In de wereld van werk hebben mensen tegenwoordig andere verwachtingen van hoe ze deskundige informatie over gegevensassets willen kunnen vinden. Vandaag de dag verwachten mensen dat ze snel hulp en advies kunnen krijgen over een breed scala aan onderwerpen via het wijdverbreide gebruik van sociale media-programma's zoals Yammer op de werkplek. **Azure Data Catalog** helpt bedrijven en teams informatie over zakelijke gegevensassets in een centrale opslagplaats samen te voegen. Gegevensgebruikers kunnen deze gegevensassets opzoeken en kennis verwerven die is bijgedragen door deskundigen.

Dit artikel biedt een benadering om aan de slag te gaan met **Azure Data Catalog**. In het artikel wordt een gebruikelijk plan voor de overstap op Data Catalog beschreven voor het fictieve bedrijf Adventure Works.

**Wat is Azure Data Catalog?**

**Azure Data Catalog** is een volledig beheerde service in Azure en een catalogus met informatie van de hele onderneming (metagegevens) waarmee gegevensbronnen via self-service kunnen worden gedetecteerd. Met Data Catalog registreert, detecteert, annoteert en maakt u verbinding met gegevensassets. Data Catalog is ontworpen voor het beheren van ongelijksoortige gegevensassets zodat ze gemakkelijk te vinden, te begrijpen en op te halen zijn. Het vermindert de tijd die nodig is om inzichten te krijgen uit beschikbare gegevens en verhoogt de waarde voor organisaties. Zie voor meer informatie [Microsoft Azure Data Catalog](https://azure.microsoft.com/services/data-catalog/).

## <a name="azure-data-catalog-adoption-plan"></a>Plan voor overstap op Azure Data Catalog

In een plan voor de overstap op **Azure Data Catalog** wordt omschreven hoe de voordelen van het gebruik van de service worden gecommuniceerd naar belanghebbenden en gebruikers, en welke soorten training u biedt aan gebruikers. Een belangrijke factor voor een succesvolle overstap op Data Catalog is hoe effectief u de meerwaarde van de service communiceert naar gebruikers en belanghebbenden. De primaire doelgroepen van een eerste overstapplan zijn de gebruikers van de service. Het maakt niet uit hoe betrokken belanghebbenden zijn, want als de gebruikers of klanten van Data Catalog dit niet dagelijks gebruiken, is de overstap niet geslaagd. Daarom wordt er in dit artikel van uitgegaan dat belanghebbenden betrokken zijn en wordt de nadruk gelegd op het maken van een plan om de gebruiker te laten overstappen op Data Catalog.
Bij een effectief overstapplan maken mensen kennis met de mogelijkheden van Data Catalog en krijgen ze de informatie en begeleiding om dit te bereiken. Gebruikers moeten begrijpen hoeveel meerwaarde Data Catalog biedt zodat ze kunnen slagen in hun werk. Wanneer mensen zien hoe ze met behulp van Data Catalog meer resultaten kunnen behalen met gegevens, wordt het heel duidelijk wat de meerwaarde is van een overstap op Data Catalog. Verandering is moeilijk, dus een doeltreffend plan moet rekening houden met de uitdagingen van verandering.

Met een overstapplan kunt u communiceren wat mensen absoluut nodig hebben om te slagen en hun doelstellingen te realiseren. In een typerend plan wordt uitgelegd hoe Data Catalog de levens van gebruikers eenvoudiger maakt. In het plan zijn de volgende onderdelen opgenomen:

* **Uitleg visie**: aan de hand van de visie kunt u het overstapplan kort en bondig bespreken met gebruikers en beheerders. Het is uw korte presentatie.
* **Testteam en gebruikers met invloed**: Als u leert van een testteam en gebruikers met invloed, kunt u de introductie van Data Catalog aan teams en gebruikers verfijnen. Gebruikers met invloed kunnen medegebruikers coachen. Hiermee kunt u ook bepalen welke factoren de overstap blokkeren en stimuleren.
* **Plan voor communicatie en enthousiasme**: dit helpt gebruikers begrijpen hoe Data Catalog ze kan helpen, en kan zorgen voor organische acceptatie binnen teams en uiteindelijk de hele organisatie.
* **Trainingsplan** - uitgebreide training leidt over het algemeen tot een succesvolle overstap en goede resultaten.

Hier volgen enkele tips voor het definiëren van een overstapplan voor **Azure Data Catalog**.

## <a name="define-your-data-catalog-project-vision"></a>Uw projectvisie voor Data Catalog definiëren

De eerste stap voor het definiëren van een overstapplan voor **Azure Data Catalog** is het schrijven van een ambitieuze beschrijving van wat u probeert te bereiken. U kunt uw visie het beste vrij breed houden, maar beknopt genoeg om specifieke doelstellingen op de korte en lange termijn te definiëren.

Hier volgen enkele tips voor het definiëren van uw visie:

* **De belangrijkste stimulans voor implementatie identificeren** - denk na over de specifieke behoeften voor beheer van gegevensbronnen in het bedrijf waaraan Data Catalog kan voldoen. Dit helpt u ook om aan te geven wat de belangrijkste voordelen zijn van het gebruik van Data Catalog. Mogelijk zijn er bijvoorbeeld algemene gegevensbronnen waar alle nieuwe medewerkers mee moeten leren werken, of belangrijke en complexe gegevensbronnen waarvan slechts enkele personen diepgaande kennis hebben. **Azure Data Catalog** kan ervoor zorgen dat deze gegevensbronnen sneller worden gedetecteerd en begrepen, zodat deze bekende knelpunten direct en vroeg in de overstap op de service kunnen worden verholpen.
* **Wees helder en duidelijk**: Als iedereen de visie goed begrijpt, denken alle betrokkenen met elkaar mee over de waarde die Data Catalog toevoegt aan de organisatie en over de wijze waarop de visie ondersteuning biedt voor organisatiedoelstellingen.
* **Inspireer mensen tot het gebruik van Data Catalog** - uw visie en communicatieplan dienen ter inspiratie, zodat mensen inzien dat ze met Data Catalog gegevensbronnen kunnen vinden en verbinden om zo meer te bereiken met gegevens.
* **Geef specifieke doelen en een tijdlijn op**: hiermee zorgt u ervoor dat uw overstapplan specifieke, haalbare doelen bevat. Met een tijdlijn blijft iedereen gefocust en kunt u succes meten aan de hand van controlepunten.

Hier vindt u een voorbeeld van een visie voor een overstapplan naar Data Catalog voor het fictieve bedrijf Adventure Works.

**Azure Data Catalog** biedt het IT-team van Adventure Works de mogelijkheid om samen te werken aan belang rijke gegevens bronnen, zodat elk teamlid de benodigde gegevens gemakkelijk kan vinden en gebruiken en hun kennis kan delen met het team als geheel.

Zodra u een heldere visie hebt gemaakt, moet u een geschikt proefproject bepalen voor Data Catalog. Er zijn verschillende scenario's voor Data Catalog. Daarom bevat de volgende sectie een aantal tips om relevante gebruiksvoorbeelden te herkennen.

## <a name="identify-data-catalog-business-use-cases"></a>Zakelijke gebruiksvoorbeelden van Data Catalog identificeren

Om gebruiksvoorbeelden te identificeren die relevant voor Data Catalog zijn, spreekt u met experts van verschillende bedrijfseenheden over relevante gebruiksvoorbeelden en op te lossen zakelijke problemen. Kijk welke problemen anderen hebben bij het identificeren van en inzicht krijgen in gegevensassets. Bijvoorbeeld, leren teams pas meer over gegevensassets nadat ze verschillende mensen binnen de organisatie hebben gevraagd wie relevante gegevensbronnen heeft?

U kunt het beste gebruiksvoorbeelden kiezen die "laaghangend fruit" zijn: voorbeelden die belangrijk zijn en snel tot succes kunnen leiden als ze worden opgelost met Data Catalog.

Hier volgen enkele tips voor het identificeren van gebruiksvoorbeelden:

* **Definieer de doelstellingen van het team** - hoe bereikt het team hun doelstellingen? Richt u nog niet op Data Catalog, omdat u in deze fase objectief wilt zijn. Vergeet niet dat het om de bedrijfsresultaten en niet om de technologie draait.
* **Definieer het zakelijke probleem** - wat zijn de problemen van het team met betrekking tot het zoeken en leren over gegevensassets? Bijvoorbeeld, er staat informatie over belangrijke gegevensbronnen in Excel-werkmappen in een netwerkmap, en het team spendeert mogelijk veel tijd aan het vinden van de werkmappen.
* **Krijg inzicht in de teamcultuur met betrekking tot verandering** - veel uitdagingen met betrekking tot acceptatie hebben te maken met weerstand tegen verandering, in plaats van met de implementatie van een nieuw hulpprogramma. Hoe een team reageert op verandering is belangrijk bij het identificeren van gebruiksvoorbeelden, omdat processen mogelijk bestaan met de redenering "dit hebben we altijd zo hebben gedaan" of "waarom zouden we het veranderen als het niet stuk is?". De overstap op een nieuw hulpprogramma of proces is altijd het gemakkelijkst als de betrokken mensen weten welke meerwaarde ze kunnen realiseren door de verandering, en erkennen dat het belangrijk is dat de problemen worden opgelost. 
* **Blijf op de hoogte** van gegevensassets-bij het bespreken van de bedrijfs problemen die een team gezichten hebben, moet u de onkruid door lopen en zich richten op wat u nodig hebt om bedrijfs gegevensassets effectiever te benutten.

Hier volgen enkele gebruiksvoorbeelden met betrekking tot Data Catalog:

### <a name="example-use-cases"></a>Gebruiksvoorbeelden

* **Centrale waardevolle gegevensbronnen registreren** - IT beheert gegevensbronnen die worden gebruikt in de hele organisatie. IT kan aan de slag met Data Catalog door algemene gegevensbronnen van de onderneming te registreren en aantekeningen te maken.
* **Registreren van gegevensbronnen op basis van een team** - verschillende teams hebben handige line-of-business-gegevensbronnen. Ga aan de slag met **Azure Data Catalog** door het identificeren en registreren van belangrijke gegevensbronnen die worden gebruikt door veel verschillende teams, en de specifieke kennis van het team vast te leggen in aantekeningen in de **Azure Data Catalog**.
* **Self-service Business Intelligence** - teams besteden veel tijd aan het combineren van gegevens uit meerdere bronnen. Registreer en maak aantekeningen voor gegevensbronnen in een centrale locatie, zodat een handmatig proces voor het detecteren van gegevensbronnen niet meer nodig is.

Voor meer informatie over scenario's met Data Catalog, zie [Algemene scenario’s met Azure Data Catalog](data-catalog-common-scenarios.md).

Wanneer u enkele gebruiksvoorbeelden voor Data Catalog hebt geïdentificeerd, komen er algemene scenario's naar voren. In de volgende sectie wordt beschreven hoe u uw eerste proefproject identificeert op basis van een gebruiksvoorbeeld.

## <a name="choose-a-data-catalog-pilot-project"></a>Een proefproject van Data Catalog kiezen

Een belangrijke succesfactor is vereenvoudigen en klein beginnen. Een goed gedefinieerde proef met een beperkt bereik zorgt ervoor dat het project vooruitgang boekt, maar niet te complex is of te veel deelnemers heeft. Het is echter ook belangrijk om verschillende soorten gebruikers te hebben, van vroege gebruikers tot sceptici. Gebruikers die open staan voor de oplossing, helpen u bij het verfijnen van uw toekomstige communicatie en plan voor het wekken van enthousiasme. Sceptici helpen u bij het identificeren en oplossen van problemen met blokkeringen. Zodra sceptici voorstanders worden, kunt u hun feedback gebruiken om succesfactoren te identificeren.

Uw proefplan moet rekening houden met zakelijke doelstellingen die u wilt behalen met Data Catalog. Terwijl u leert van de eerste proef, kunt u uw gebruikersgroep uitbreiden. Een eerste gesloten proef is handig om meetbaar succes tot stand te brengen, maar het einddoel is organische of zeer snelle groei. Met organische groei van Data Catalog hebben gebruikers controle over het gebruik van hun eigen gegevens. Daarmee kunnen ze anderen beïnvloeden en aansporen om over te stappen op en bij te dragen aan de catalogus.

### <a name="target-the-right-team"></a>Richt u op het juiste team

Als u uw proefproject kiest, selecteert u een team met de meest interessante scenario's dat een bestaand zakelijk probleem oplost. Bijvoorbeeld, een bedrijfsanalist maakt rapporten van een SQL Server-database. Het probleem is dat de gegevens bron pas na het communiceren met verschillende collega's bekend is geworden. Ten slotte, nadat u de tijd hebt verspild om te bepalen welke gegevens bronnen u moet gebruiken, hebben ze een Excel-werkmap gevonden met een beschrijving van elke gegevens bron. Hoewel de Excel-werkmap op de juiste wijze de tabellen beschrijft die ze nodig hebben, zouden ze deze gegevens bronnen snel kunnen vinden als ze zijn geregistreerd en aantekeningen hebben gemaakt in **Azure Data Catalog**.

### <a name="identify-data-heroes"></a>Gegevensproducenten en -gebruikers identificeren

In uw eerste proefproject neemt u enkele personen op die gegevens produceren en gebruiken, zodat de representatie van het team gebalanceerd is.

**Gegevensproducenten** zijn mensen met kennis over gegevensbronnen. Bijvoorbeeld, David werkt in een ander team en heeft uitgebreid gewerkt met belangrijke gegevensbronnen van Adventure Works. Voorafgaand aan de overstap op **Azure Data Catalog** heeft David een Excel-werkmap gemaakt om informatie over de gegevensbronnen van Adventure Works vast te leggen.

**Gegevensgebruikers** zijn mensen met expertise over het gebruik van gegevens om bedrijfsproblemen op te lossen. Nancy is bijvoorbeeld een bedrijfsanalist die gegevensbronnen van SQL Server bij Adventure Works gebruikt om gegevens te analyseren.

Een van de bedrijfsproblemen die **Azure Data Catalog** oplost, is het verbinden van **gegevensproducenten** met **gegevensgebruikers**. Data Catalog doet dit door te fungeren als een centrale opslagplaats voor informatie over zakelijke gegevensbronnen. Met behulp van Data Catalog registreert David gegevensbronnen van Adventure Works en SQL Server. Door crowdsourcing te gebruiken, kunnen alle gebruikers die deze gegevens bron detecteren, haar meningen over de gegevens delen, naast het gebruik van de gegevens die ze hebben gedetecteerd. Bijvoorbeeld, Nancy detecteert de gegevensbronnen door in de catalogus te zoeken en deelt haar gespecialiseerde kennis over de gegevens.  Anderen in de organisatie profiteren nu van de gedeelde kennis door in de catalogus met gegevens te zoeken.

* Zie voor meer informatie over het registreren van gegevensbronnen [Gegevensbronnen registreren](data-catalog-get-started.md).
* Zie voor meer informatie over het detecteren van gegevensbronnen [Gegevensbronnen zoeken](data-catalog-get-started.md).

### <a name="start-small-and-focused"></a>Klein en gericht beginnen

Voor de meeste zakelijke proefprojecten moet u de catalogus vullen met waardevolle gegevensbronnen, zodat zakelijke gebruikers snel de waarde van Data Catalog kunnen zien. IT is een goede plaats om te beginnen met het identificeren van algemene gegevensbronnen die van belang zijn voor uw proefteam. Voor ondersteunde gegevensbronnen, zoals SQL Server, wordt u aangeraden het hulpprogramma voor registratie van gegevensbronnen van **Azure Data Catalog** te gebruiken. Met het hulpprogramma voor registratie van gegevensbronnen kunt u een breed scala aan gegevensbronnen registreren, met inbegrip van SQL Server- en Oracle-databases en rapporten van SQL Server Reporting Services. Zie voor een volledige lijst met huidige gegevensbronnen [Ondersteunde gegevensbronnen voor Azure Data Catalog](data-catalog-dsr.md).

Nadat u belangrijke gegevensbronnen hebt geïdentificeerd en geregistreerd, is het mogelijk om ook beschrijvingen van gegevensbronnen die zijn opgeslagen in andere locaties te importeren. Met de API van Data Catalog kunnen ontwikkelaars beschrijvingen en aantekeningen laden vanaf een andere locatie, zoals de Excel-werkmap die David heeft gemaakt en onderhoudt.

In de volgende sectie wordt een voorbeeldproject van het bedrijf Adventure Works beschreven.

### <a name="an-example-project"></a>Een voorbeeldproject

In dit voorbeeld maakt bedrijfsanalist Nancy rapporten voor haar team met behulp van gegevens uit een SQL Server-database. Het probleem is dat de gegevens bron pas na het communiceren met verschillende collega's bekend is geworden. Ze zouden deze gegevens bronnen snel hebben gevonden als ze zijn geregistreerd en aantekeningen maken op een centrale locatie, zoals **Azure Data Catalog**.

Als u wilt laten zien hoe eenvoudig Nancy en haar team waardevolle gegevens kunnen vinden, gebruikt u het hulpprogramma voor registratie van gegevensbronnen om de catalogus met informatie (metagegevens) over de gegevensbronnen te vullen. Op deze manier is de informatie over de database beschikbaar voor het team en de onderneming, in plaats van slechts enkele personen. Nadat de relevante gegevensbronnen zijn geregistreerd in Data Catalog, kunnen Nancy en haar team deze bronnen gemakkelijk gebruiken. Het resultaat is een uitgebreidere en relevante gegevenscatalogus voor haar team en de onderneming. Naarmate er meer teams overstappen op Data Catalog, zijn gegevensbronnen binnen het bedrijf steeds gemakkelijker te vinden en gebruiken. Daardoor ontstaat er een meer gegevensgerichte cultuur waarin u meer kunt bereiken met uw gegevens.

Zie voor meer informatie over het hulpprogramma voor de registratie van gegevensbronnen [Aan de slag met Azure Data Catalog](data-catalog-get-started.md).

Als onderdeel van het proefproject maakt Nancy’s team ook gebruik van gegevensbronnen die worden beschreven in een Excel-werkmap, die wordt onderhouden door David en zijn collega's. Omdat andere teams in de onderneming ook Excel-werkmappen gebruiken om gegevensbronnen te beschrijven, besluit het IT-team tot het maken van een hulpprogramma om de Excel-werkmap te migreren naar Data Catalog. Door bestaande aantekeningen te importeren met de REST API van Data Catalog, kan het proefteam een volledige gegevenscatalogus maken, die bestaat uit metagegevens die uit de gegevensbronnen zijn geëxtraheerd met behulp van het hulpprogramma voor registratie van gegevensbronnen. Deze gegevens bevatten ook de informatie die eerder is gedocumenteerd door gegevensproducenten en -gebruikers. Het team hoeft de gegevens dus niet handmatig opnieuw in te voeren. Wanneer de gegevenscatalogus van de onderneming groeit, kan de organisatie het hulpprogramma voor de registratie van algemene gegevensbronnen gebruiken, alsmede de API van Data Catalog voor aangepaste bronnen en ongewone scenario's.

> [!NOTE]
> We hebben een voorbeeldprogramma geschreven dat gebruik maakt van de API van **Azure Data Catalog** API om een Excel-werkmap te migreren naar Data Catalog. Voor informatie over de API van Data Catalog en het voorbeeldprogramma, kunt u [De voorbeeldcode van de Ad Hoc-werkmap downloaden](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets) en de documentatie van de [REST API van Azure Data Catalog](/rest/api/datacatalog/) lezen.

Nadat het proefproject is ingevoerd, is het tijd om uw overstapplan voor Data Catalog uit te voeren.

### <a name="execute"></a>Uitvoeren

Op dit moment hebt u gebruiksvoorbeelden geïdentificeerd voor Data Catalog. Ook hebt u uw eerste project geïdentificeerd. Bovendien hebt u de belangrijkste gegevensbronnen van Adventure Works geregistreerd en hebt u informatie toegevoegd uit de bestaande Excel-werkmap met het hulpprogramma dat IT heeft gebouwd. Nu is het tijd om samen met het proefteam te beginnen aan het overstapproces voor Data Catalog.

Hier volgen enkele tips om aan de slag te gaan:

* **Creëer enthousiasme** - zakelijke gebruikers worden enthousiast als ze van mening zijn dat **Azure Data Catalog** hun leven eenvoudiger maakt. Probeer het gesprek te richten op de oplossing en de voordelen die de oplossing biedt, niet op de technologie.
* **Maak veranderingen mogelijk** - begin klein en communiceer het plan naar zakelijke gebruikers. Als u wilt slagen, is het van cruciaal belang gebruikers vanaf het begin bij de oplossing te betrekken, zodat ze invloed hebben op het resultaat en vertrouwd raken met de oplossing.
* **Let op vroege gebruikers** - vroege gebruikers zijn zakelijke gebruikers die enthousiast zijn over wat ze doen en de voordelen van **Azure Data Catalog** graag doorvertellen aan hun collega’s.
* **Doelgerichte training** - zakelijke gebruikers hoeven niet alles te weten over Data Catalog, dus richt training op specifieke doelstellingen van teams. Richt u op wat gebruikers gaan doen en hoe sommige taken kunnen wijzigen, om **Azure Data Catalog** te integreren in hun dagelijkse werk.
* **Wees bereid om fouten te maken** - als de proef niet de gewenste resultaten oplevert, evalueer dan opnieuw en identificeer wat u kunt veranderen. Los problemen oplossen tijdens de proef voordat u de oplossing implementeert met een groter bereik.

Voordat uw team aan de slag gaat met Data Catalog, plant u een vergadering ter introductie om verwachtingen voor het proefproject te bespreken en een eerste training te geven.

### <a name="set-expectations"></a>Verwachtingen bijstellen

Door verwachtingen en doelstellingen te omschrijven, kunnen zakelijke gebruikers zich beter concentreren op specifieke resultaten. Om het project op schema te houden, geeft u regelmatig (bijvoorbeeld dagelijks of wekelijks op basis van het bereik en de duur van het project) huiswerkopdrachten op. Een van de belangrijkste mogelijkheden van Data Catalog is crowdsourcing van gegevensassets, zodat zakelijke gebruikers van de kennis van bedrijfsgegevens kunnen profiteren. Een goede huiswerkopdracht is om elk lid van het proefteam ten minste één gegevensbron die hij of zij heeft gebruikt te laten registreren of aantekenen. Zie [Een gegevensbron registreren](data-catalog-get-started.md) en [Aantekeningen toevoegen aan gegevensbronnen](data-catalog-get-started.md).

Vergader regelmatig met het team om enkele aantekeningen te beoordelen. Goede aantekeningen over gegevensbronnen zijn het meest belangrijk voor een geslaagde overstap op Data Catalog, omdat ze betekenisvolle inzichten voor gegevensbronnen leveren op een centrale locatie. Kennis over gegevensbronnen blijft zonder goede aantekeningen verspreid over de hele onderneming. Zie [Aantekeningen toevoegen aan gegevensbronnen](data-catalog-get-started.md).

En de ultieme test van het project is of gebruikers de gegevensbronnen die ze nodig hebben kunnen detecteren en inzicht in de bronnen kunnen krijgen. Proefgebruikers moeten de catalogus regelmatig testen om ervoor te zorgen dat de gegevensbronnen die ze voor hun dagelijkse werk gebruiken relevant zijn. Wanneer een vereiste gegevensbron ontbreekt of geen juiste aantekeningen heeft, moet dit fungeren als een herinnering om extra gegevensbronnen te registreren of aanvullende aantekeningen in te voeren. Met deze procedure voegt u niet alleen waarde toe aan het proefproject, maar bouwt u ook effectieve gewoontes op die andere teams overnemen nadat het proefproject is voltooid.

### <a name="provide-training"></a>Training geven

Training moet genoeg zijn zodat gebruikers aan de slag kunnen, en moet zijn afgestemd op de specifieke doelstellingen en ervaringsniveaus van de leden in het proefteam. Om te beginnen met de training, kunt u de stappen volgen in het artikel [Aan de slag met Azure Data Catalog](data-catalog-get-started.md). Bovendien kunt u de [presentatie Training voor het proefproject van Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/blob/master/Azure%20Data%20Catalog%20Training.pptx?raw=true) downloaden. Met behulp van deze PowerPoint-presentatie kunt u Data Catalog introduceren aan de leden in uw proefteam.

## <a name="conclusion"></a>Conclusie

Als uw proefteam eenmaal vrij goed werkt en u de oorspronkelijke doelstellingen hebt bereikt, kunt u de overstap op Data Catalog uitbreiden naar meer teams. Pas toe en verfijn wat u in uw proefproject hebt geleerd om Data Catalog uit te breiden binnen uw hele organisatie.

De vroege gebruikers die hebben deelgenomen aan de proef kunnen helpen om informatie te verspreiden over de voordelen van een overstap op Data Catalog. Ze kunnen met andere teams delen hoe Data Catalog hun team heeft geholpen om zakelijke problemen op te lossen, gegevensbronnen eenvoudiger te ontdekken en inzichten te delen over de gegevensbronnen die ze gebruiken. Vroege gebruikers in het proefteam van Adventure Works kunnen bijvoorbeeld aan anderen laten zien hoe gemakkelijk het is om te zoeken naar gegevensassets van Adventure Works die ooit moeilijk te vinden en begrijpen waren.

In dit artikel is beschreven hoe u aan de slag kunt met **Azure Data Catalog** in uw organisatie. We hopen dat u een proefproject voor Data Catalog hebt kunnen starten, en dat u Data Catalog hebt uitgebreid binnen uw organisatie.

## <a name="next-steps"></a>Volgende stappen

[Een Azure Data Catalog maken](data-catalog-get-started.md)
