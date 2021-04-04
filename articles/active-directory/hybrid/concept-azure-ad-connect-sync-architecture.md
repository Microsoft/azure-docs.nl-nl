---
title: 'Azure AD Connect Sync: informatie over de architectuur-Azure'
description: In dit onderwerp wordt de architectuur van Azure AD Connect Sync beschreven en worden de gebruikte termen uitgelegd.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27055ce84bbb073045b69b942fd13f4fde4e3b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563859"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect synchronisatie: uitleg van de architectuur
In dit onderwerp wordt de basis architectuur voor Azure AD Connect synchronisatie beschreven. In veel aspecten lijkt het op de voorafgaande taken MIIS 2003, ILM 2007 en FIM 2010. Azure AD Connect synchronisatie is de ontwikkeling van deze technologieën. Als u bekend bent met een van deze eerdere technologieën, is de inhoud van dit onderwerp ook bekend voor u. Als u geen ervaring hebt met synchronisatie, is dit onderwerp voor u. Het is echter geen vereiste om de details van dit onderwerp te weten als u wijzigingen wilt aanbrengen in Azure AD Connect synchronisatie (synchronisatie-engine genoemd in dit onderwerp).

## <a name="architecture"></a>Architectuur
De synchronisatie-engine maakt een geïntegreerde weer gave van objecten die zijn opgeslagen in meerdere verbonden gegevens bronnen en beheert identiteits gegevens in deze gegevens bronnen. Deze geïntegreerde weer gave wordt bepaald door de identiteits gegevens die zijn opgehaald uit verbonden gegevens bronnen en een set regels die bepalen hoe deze gegevens moeten worden verwerkt.

### <a name="connected-data-sources-and-connectors"></a>Verbonden gegevens bronnen en connectors
De synchronisatie-engine verwerkt identiteits gegevens uit verschillende gegevens opslagplaatsen, zoals Active Directory of een SQL Server-Data Base. Elke gegevensopslag plaats waarmee gegevens worden ingedeeld in een Data Base-achtige indeling en die standaard methoden voor gegevens toegang biedt, is een potentiële gegevens bron kandidaat voor de synchronisatie-engine. De gegevens opslagplaatsen die door de synchronisatie-engine worden gesynchroniseerd, worden **verbonden gegevens bronnen** of **verbonden directory's** (cd) genoemd.

De synchronisatie-engine encapsulateert interactie met een verbonden gegevens bron binnen een module die een **connector** wordt genoemd. Elk type verbonden gegevens bron heeft een specifieke connector. De connector zet een vereiste bewerking om in de indeling die de verbonden gegevens bron begrijpt.

Connectors maken API-aanroepen naar Exchange-identiteits gegevens (zowel lezen als schrijven) met een verbonden gegevens bron. Het is ook mogelijk om een aangepaste connector toe te voegen met behulp van het Extensible Connectivity Framework. In de volgende afbeelding ziet u hoe een connector een verbonden gegevens bron verbindt met de synchronisatie-engine.

![Diagram toont een verbonden gegevens bron en een synchronisatie-engine die is gekoppeld aan een lijn genaamd connector.](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Gegevens kunnen in een van beide richtingen stromen, maar deze kunnen niet tegelijkertijd in beide richtingen stromen. Met andere woorden, een connector kan zodanig worden geconfigureerd dat gegevens die afkomstig zijn van de verbonden gegevens bron, kunnen worden gestroomd naar de gekoppelde gegevens bron, maar slechts één van deze bewerkingen kan worden uitgevoerd op elk gewenst moment voor één object en kenmerk. De richting kan verschillen voor verschillende objecten en voor verschillende kenmerken.

Als u een connector wilt configureren, geeft u de object typen op die u wilt synchroniseren. Als u de object typen opgeeft, wordt het bereik van objecten gedefinieerd die zijn opgenomen in het synchronisatie proces. De volgende stap is het selecteren van de kenmerken die moeten worden gesynchroniseerd. dit wordt ook wel een lijst met kenmerken opgenomen. Deze instellingen kunnen op elk gewenst moment worden gewijzigd als reactie op wijzigingen in uw bedrijfs regels. Wanneer u de installatie wizard van Azure AD Connect gebruikt, worden deze instellingen voor u geconfigureerd.

Als u objecten wilt exporteren naar een verbonden gegevens bron, moet de lijst met kenmerk opnames ten minste de vereiste minimum kenmerken voor het maken van een specifiek object type in een verbonden gegevens bron bevatten. Het kenmerk **sAMAccountName** moet bijvoorbeeld zijn opgenomen in de lijst met kenmerk opnames om een gebruikers object te exporteren naar Active Directory, omdat voor alle gebruikers objecten in Active Directory een kenmerk **sAMAccountName** moet worden gedefinieerd. De installatie wizard voert deze configuratie voor u opnieuw uit.

Als de verbonden gegevens bron gebruikmaakt van structurele onderdelen, zoals partities of containers om objecten te organiseren, kunt u de gebieden in de verbonden gegevens bron beperken die worden gebruikt voor een bepaalde oplossing.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Interne structuur van de naam ruimte van de synchronisatie-engine
De volledige naam ruimte van de synchronisatie-engine bestaat uit twee naam ruimten waarin de identiteits gegevens worden opgeslagen. De twee naam ruimten zijn:

* De connector ruimte (CS)
* De omgekeerde (MV)

De **connector ruimte** is een faserings gebied met daarin de weer gaven van de aangewezen objecten uit een verbonden gegevens bron en de kenmerken die zijn opgegeven in de lijst met kenmerken. De synchronisatie-engine gebruikt de connector ruimte om te bepalen wat er is gewijzigd in de verbonden gegevens bron en om binnenkomende wijzigingen te betreden. De synchronisatie-engine gebruikt ook de ruimte van de connector om uitgaande wijzigingen te bewerk te nemen voor export naar de verbonden gegevens bron. De synchronisatie-engine onderhoudt een afzonderlijke connector ruimte als een faserings gebied voor elke connector.

Als u een faserings gebied gebruikt, blijft de synchronisatie-engine onafhankelijk van de verbonden gegevens bronnen en wordt deze niet beïnvloed door hun Beschik baarheid en toegankelijkheid. Als gevolg hiervan kunt u identiteits gegevens op elk gewenst moment verwerken door gebruik te maken van de gegevens in het faserings gebied. De synchronisatie-engine kan alleen de wijzigingen aanvragen die zijn aangebracht in de gekoppelde gegevens bron sinds de laatste communicatie sessie is beëindigd of alleen de wijzigingen van de identiteits gegevens die de verbonden gegevens bron nog heeft ontvangen, hebben gereduceerd, waardoor het netwerk verkeer tussen de synchronisatie-engine en de verbonden gegevens bron wordt verkleind.

Daarnaast slaat de synchronisatie-engine status informatie op over alle objecten die ze in de connector ruimte faseert. Wanneer er nieuwe gegevens worden ontvangen, evalueert de synchronisatie-engine altijd of de gegevens al zijn gesynchroniseerd.

Het **omgekeerde** is een opslag gebied dat de geaggregeerde identiteits gegevens uit meerdere verbonden gegevens bronnen bevat, waardoor één globale, geïntegreerde weer gave van alle gecombineerde objecten wordt geboden. Omgekeerde objecten worden gemaakt op basis van de identiteits gegevens die zijn opgehaald uit de verbonden gegevens bronnen en een set regels waarmee u het synchronisatie proces kunt aanpassen.

In de volgende afbeelding ziet u de naam ruimte van de connector en de naam ruimte van de tekst in de synchronisatie-engine.

![In het diagram ziet u een verbonden gegevens bron en een synchronisatie-engine, die is gescheiden in connector ruimte en in omgekeerde naam ruimten die zijn gekoppeld aan een lijn met de naam connector.](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Id-objecten van synchronisatie-engine
De objecten in de synchronisatie-engine zijn representaties van objecten in de verbonden gegevens bron of de geïntegreerde weer gave die de synchronisatie-engine heeft van deze objecten. Elk sync engine-object moet een Globally Unique Identifier (GUID) hebben. GUID'S bieden gegevens integriteit en snelle relaties tussen objecten.

### <a name="connector-space-objects"></a>Connector-ruimte objecten
Wanneer de synchronisatie-engine communiceert met een verbonden gegevens bron, worden de identiteits gegevens in de gekoppelde gegevens bron gelezen en wordt deze informatie gebruikt om een weer gave van het identiteits object in de connector ruimte te maken. U kunt deze objecten niet afzonderlijk maken of verwijderen. U kunt echter alle objecten in een connector ruimte hand matig verwijderen.

Alle objecten in de connector ruimte hebben twee kenmerken:

* Een Globally Unique Identifier (GUID)
* Een DN-naam (ook bekend als DN)

Als de verbonden gegevens bron een uniek kenmerk toewijst aan het object, kunnen objecten in de connector ruimte ook een anker kenmerk hebben. Het anker kenmerk geeft een unieke identificatie van een object in de verbonden gegevens bron aan. De synchronisatie-engine gebruikt het anker om de bijbehorende representatie van dit object in de gekoppelde gegevens bron te vinden. Sync engine gaat ervan uit dat het anker van een object nooit wordt gewijzigd gedurende de levens duur van het object.

Veel van de connectors gebruiken een bekende unieke id voor het automatisch genereren van een anker voor elk object wanneer het wordt geïmporteerd. De Active Directory-connector gebruikt bijvoorbeeld het kenmerk **ObjectGUID** voor een anker. Voor verbonden gegevens bronnen die geen duidelijk gedefinieerde unieke id bieden, kunt u het genereren van ankers opgeven als onderdeel van de configuratie van de connector.

In dat geval wordt het anker gebouwd op basis van een of meer unieke kenmerken van een object type, waarvan geen van de wijzigingen, en waarmee het object op unieke wijze wordt geïdentificeerd in de connector ruimte (bijvoorbeeld een werknemers nummer of een gebruikers-ID).

Een connector ruimte-object kan een van de volgende zijn:

* Een staging-object
* Een tijdelijke aanduiding

### <a name="staging-objects"></a>Staging-objecten
Een staging-object vertegenwoordigt een exemplaar van de aangewezen object typen van de verbonden gegevens bron. Naast de GUID en de DN-naam heeft een staging-object altijd een waarde die het object type aangeeft.

Tijdelijke objecten die zijn geïmporteerd hebben altijd een waarde voor het anker kenmerk. Staging-objecten die nieuw zijn ingericht door de synchronisatie-engine en in het proces worden gemaakt in de verbonden gegevens bron hebben geen waarde voor het anker kenmerk.

Staging-objecten bevatten ook de huidige waarden van bedrijfs kenmerken en operationele gegevens die nodig zijn voor de synchronisatie-engine om het synchronisatie proces uit te voeren. Operationele informatie omvat vlaggen die aangeven welk type updates er worden klaargezet voor het staging-object. Als een staging-object nieuwe identiteits gegevens heeft ontvangen van de verbonden gegevens bron die nog niet is verwerkt, wordt het object gemarkeerd als **import in behandeling**. Als een staging-object nieuwe identiteits gegevens heeft die nog niet zijn geëxporteerd naar de verbonden gegevens bron, wordt deze gemarkeerd als **geëxporteerd in behandeling**.

Een staging-object kan een import object of een export object zijn. De synchronisatie-engine maakt een import-object door gebruik te maken van object gegevens die zijn ontvangen van de verbonden gegevens bron. Wanneer de synchronisatie-engine informatie ontvangt over het bestaan van een nieuw object dat overeenkomt met een van de object typen die in de connector zijn geselecteerd, wordt er een import object in de connector ruimte gemaakt als een representatie van het object in de verbonden gegevens bron.

In de volgende afbeelding ziet u een import object dat een object in de verbonden gegevens bron vertegenwoordigt.

![Diagram toont een import object dat van de verbonden gegevens bron wordt binnengebracht in de naam ruimte van de connector in de sync-engine.](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

De synchronisatie-engine maakt een export object door gebruik te maken van object gegevens in de tekst. Export objecten worden tijdens de volgende communicatie sessie geëxporteerd naar de verbonden gegevens bron. Vanuit het perspectief van de synchronisatie-engine bestaan er nog geen export objecten in de verbonden gegevens bron. Daarom is het anker kenmerk voor een export object niet beschikbaar. Nadat het object van de synchronisatie-engine is ontvangen, maakt de verbonden gegevens bron een unieke waarde voor het anker kenmerk van het object.

In de volgende afbeelding ziet u hoe een export object wordt gemaakt met behulp van identiteits gegevens in de tekst.

![Diagram toont een export object dat is overgezet van de tekst naar de naam ruimte van de connector en vervolgens naar de verbonden gegevens bron.](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

De synchronisatie-engine bevestigt het exporteren van het object door het opnieuw te importeren uit de verbonden gegevens bron. Objecten exporteren worden geïmporteerd objecten wanneer de synchronisatie-engine deze ontvangt tijdens de volgende import bewerking van die verbonden gegevens bron.

### <a name="placeholders"></a>Tijdelijke aanduidingen
De synchronisatie-engine gebruikt een platte naam ruimte voor het opslaan van objecten. Sommige verbonden gegevens bronnen, zoals Active Directory, maken echter gebruik van een hiërarchische naam ruimte. Voor het transformeren van gegevens van een hiërarchische naam ruimte naar een platte naam ruimte, gebruikt synchronisatie-engine tijdelijke aanduidingen voor het behouden van de hiërarchie.

Elke tijdelijke aanduiding vertegenwoordigt een onderdeel (bijvoorbeeld een organisatie-eenheid) van de hiërarchische naam van een object dat niet is geïmporteerd in de synchronisatie-engine, maar is vereist voor het maken van de hiërarchische naam. De door verwijzingen gemaakte hiaten worden doorgevoerd in de verbonden gegevens bron naar objecten die geen objecten bevatten die zich in de connector ruimte bevinden.

De synchronisatie-engine gebruikt ook tijdelijke aanduidingen voor het opslaan van objecten waarnaar wordt verwezen en die nog niet zijn geïmporteerd. Als synchronisatie bijvoorbeeld is geconfigureerd om het kenmerk Manager op te nemen voor het *Abbie Spencer* -object en de ontvangen waarde een object is dat nog niet is geïmporteerd, zoals *CN = Lee Sperry, CN = Users, dc = fabrikam, DC = com*, worden de gegevens van de Manager opgeslagen als tijdelijke aanduidingen in de connector ruimte. Als het beheer object later wordt geïmporteerd, wordt het object met de tijdelijke aanduiding overschreven door het staging-object dat de manager vertegenwoordigt.

### <a name="metaverse-objects"></a>Omgekeerde objecten
Een omgekeerd object bevat de geaggregeerde weer gave die de synchronisatie-engine heeft van de tijdelijke objecten in de connector ruimte. De synchronisatie-engine maakt omgekeerde objecten met behulp van de gegevens in import objecten. Verschillende connector ruimte objecten kunnen worden gekoppeld aan een enkel omgekeerd object, maar een connector ruimte-object kan niet worden gekoppeld aan meer dan een omgekeerd object.

Omgekeerde objecten kunnen niet hand matig worden gemaakt of verwijderd. De synchronisatie-engine verwijdert automatisch omgekeerde objecten die geen koppeling naar een connector ruimte-object in de connector ruimte hebben.

Als u objecten binnen een verbonden gegevens bron wilt toewijzen aan een bijbehorend object type binnen de omgekeerde, biedt synchronisatie-engine een uitbreidbaar schema met een vooraf gedefinieerde set object types en bijbehorende kenmerken. U kunt nieuwe object typen en kenmerken maken voor omgekeerde objecten. Kenmerken kunnen één waarde of meerdere waarden hebben en de kenmerk typen kunnen teken reeksen, verwijzingen, getallen en Booleaanse waarden zijn.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relaties tussen tijdelijke objecten en omgekeerde objecten
Binnen de naam ruimte van de synchronisatie-engine wordt de gegevens stroom ingeschakeld door de koppelings relatie tussen tijdelijke objecten en omgekeerde objecten. Een staging-object dat is gekoppeld aan een omgekeerd object, wordt een **gekoppeld object** (of een **connector object**) genoemd. Een staging-object dat niet is gekoppeld aan een omgekeerd object, wordt een niet- **samengevoegd** object (of een object voor de **loskoppeling**) genoemd. De termen join en disjoind worden aanbevolen om niet te verwarren met de connectors die verantwoordelijk zijn voor het importeren en exporteren van gegevens uit een verbonden map.

Tijdelijke aanduidingen zijn nooit gekoppeld aan een omgekeerd object

Een samengevoegd object bestaat uit een staging-object en de gekoppelde relatie met een enkel omgekeerd object. Gekoppelde objecten worden gebruikt voor het synchroniseren van kenmerk waarden tussen een connector ruimte object en een omgekeerd object.

Wanneer een staging-object tijdens de synchronisatie een samengevoegd object wordt, kunnen kenmerken stromen tussen het tijdelijke object en het omgekeerde object. De kenmerk stroom is bidirectionele en wordt geconfigureerd met behulp van kenmerk regels importeren en kenmerk regels exporteren.

U kunt één connector ruimte-object aan slechts één omgekeerd object koppelen. Elk omgekeerd object kan echter worden gekoppeld aan meerdere connector ruimte-objecten in dezelfde of in verschillende connector ruimten, zoals wordt weer gegeven in de volgende afbeelding.

![Diagram toont twee verbonden gegevens objecten die zijn gekoppeld aan Connect oren met een synchronisatie-engine, die gekoppelde objecten en een niet-samengevoegd object bevat.](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

De gekoppelde relatie tussen het tijdelijke object en een omgekeerd object is permanent en kan alleen worden verwijderd door de regels die u opgeeft.

Een niet-samengevoegd object is een staging-object dat niet is gekoppeld aan een omgekeerd object. De kenmerk waarden van een niet-samengevoegd object worden niet verder in de tekst doorgevoerd. De kenmerk waarden van het bijbehorende object in de verbonden gegevens bron worden niet bijgewerkt door de synchronisatie-engine.

Door het gebruik van niet-samenvoegde objecten kunt u identiteits gegevens opslaan in de synchronisatie-engine en deze later verwerken. Het behouden van een staging-object als een niet-samengevoegd object in de connector ruimte heeft veel voor delen. Omdat het systeem de vereiste informatie over dit object al heeft klaargezet, is het niet nodig om opnieuw een representatie van dit object te maken tijdens de volgende import bewerking vanuit de gekoppelde gegevens bron. Op deze manier heeft de synchronisatie-engine altijd een volledige moment opname van de verbonden gegevens bron, zelfs als er geen huidige verbinding is met de verbonden gegevens bron. Ontkoppelde objecten kunnen worden geconverteerd naar gekoppelde objecten en omgekeerd, afhankelijk van de regels die u opgeeft.

Er wordt een import object gemaakt als een niet-samengevoegd object. Een export object moet een gekoppeld object zijn. De systeem logica dwingt deze regel af en verwijdert elk export object dat geen lid is van een object.

## <a name="sync-engine-identity-management-process"></a>Proces van identiteits beheer van synchronisatie-engine
Het proces voor identiteits beheer bepaalt hoe identiteits gegevens worden bijgewerkt tussen verschillende verbonden gegevens bronnen. Identiteits beheer vindt plaats in drie processen:

* Importeren
* Synchronisatie
* Exporteren

Tijdens het import proces evalueert de synchronisatie-engine de binnenkomende identiteits gegevens van een verbonden gegevens bron. Wanneer er wijzigingen worden gedetecteerd, worden er nieuwe staging-objecten gemaakt of bestaande tijdelijke objecten bijgewerkt in de connector ruimte voor synchronisatie.

Tijdens het synchronisatie proces werkt de synchronisatie-engine bij om de wijzigingen die in de connector ruimte zijn aangebracht, bij te houden en de connector ruimte bij te houden om wijzigingen die in de tekst zijn aangebracht, te reflecteren.

Tijdens het export proces worden door de synchronisatie-engine wijzigingen doorgevoerd die worden klaargezet voor tijdelijke objecten en die als in behandeling zijn gemarkeerd.

In de volgende afbeelding ziet u hoe elk van de processen plaatsvindt als identiteits gegevens van een verbonden gegevens bron naar een andere.

![Diagram toont de stroom van identiteits gegevens van verbonden gegevens naar connector ruimte (importeren) naar de synchonization (voor de verbindings ruimte) naar verbonden gegevens (exporteren).](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Import proces
Tijdens het import proces evalueert de synchronisatie-engine updates op identiteits gegevens. De synchronisatie-engine vergelijkt de identiteits gegevens die zijn ontvangen van de verbonden gegevens bron met de identiteits gegevens over een staging-object en bepaalt of het staging-object updates vereist. Als het nodig is om het staging-object bij te werken met nieuwe gegevens, wordt het staging-object gemarkeerd als import in behandeling.

Door objecten in de connector ruimte vóór synchronisatie te klaarzetten, kan de synchronisatie-engine alleen de identiteits gegevens verwerken die zijn gewijzigd. Dit proces biedt de volgende voor delen:

* **Efficiënte synchronisatie**. De hoeveelheid gegevens die tijdens de synchronisatie wordt verwerkt, is geminimaliseerd.
* **Efficiënte hersynchronisatie**. U kunt wijzigen hoe de synchronisatie-engine identiteits gegevens verwerkt zonder de synchronisatie-engine opnieuw te verbinden met de gegevens bron.
* **Kans om synchronisatie te bekijken**. U kunt de synchronisatie vooraf bekijken om te controleren of uw veronderstellingen over het proces voor identiteits beheer correct zijn.

Voor elk object dat in de connector is opgegeven, probeert de synchronisatie-engine eerst een representatie van het object in de connector ruimte van de connector te vinden. De synchronisatie-engine onderzoekt alle staging-objecten in de connector ruimte en probeert een overeenkomend staging-object te vinden dat een overeenkomend anker kenmerk heeft. Als er geen bestaand staging-object een overeenkomend anker kenmerk heeft, probeert de synchronisatie-engine een overeenkomend staging-object met dezelfde DN-naam te vinden.

Wanneer de synchronisatie-engine een staging-object vindt dat overeenkomt met de DN-naam, maar niet op basis van het anker, gebeurt het volgende speciale gedrag:

* Als het object dat zich in de connector ruimte bevindt, geen anker heeft, wordt dit object door de synchronisatie-engine uit de ruimte van de connector verwijderd en wordt het omgekeerde object gemarkeerd als **opnieuw inrichten bij de volgende synchronisatie**. Vervolgens wordt het nieuwe import object gemaakt.
* Als het object dat zich in de connector ruimte bevindt een anker heeft, wordt ervan uitgegaan dat dit object is hernoemd of verwijderd uit de verbonden Directory. Er wordt een tijdelijke, nieuwe DN-naam voor het object voor de connector ruimte toegewezen zodat het binnenkomende object kan worden gestage. Het oude object wordt vervolgens **tijdelijk** en er wordt gewacht tot de connector de naam wijzigen of verwijderen heeft geïmporteerd om de situatie op te lossen.

Als de synchronisatie-engine een staging-object vindt dat overeenkomt met het object dat is opgegeven in de connector, wordt bepaald welke soort wijzigingen moeten worden toegepast. De synchronisatie-engine kan bijvoorbeeld de naam wijzigen of verwijderen van het object in de verbonden gegevens bron, of kan de kenmerk waarden van het object alleen bijwerken.

Tijdelijke objecten met bijgewerkte gegevens worden gemarkeerd als import in behandeling. Er zijn verschillende typen in behandeling zijnde invoer. Afhankelijk van het resultaat van het import proces bevat een staging-object in de ruimte van de connector een van de volgende invoer typen die in behandeling zijn:

* **Geen**. Er zijn geen wijzigingen in de kenmerken van het staging-object beschikbaar. De synchronisatie-engine markeert dit type niet als import in behandeling.
* **Toevoegen**. Het staging-object is een nieuw import object in de ruimte van de connector. De synchronisatie-engine markeert dit type als in behandeling zijnde invoer voor aanvullende verwerking in de tekst.
* **Update**. De synchronisatie-engine vindt een overeenkomend staging-object in de connector ruimte en markeert dit type als import in behandeling, zodat de updates van de kenmerken kunnen worden verwerkt in de tekst. Updates zijn onder andere het wijzigen van object namen.
* **Delete**. De synchronisatie-engine vindt een bijbehorend staging-object in de connector ruimte en markeert dit type als in behandeling zijnde invoer zodat het gekoppelde object kan worden verwijderd.
* **Verwijderen/toevoegen**. De synchronisatie-engine vindt een overeenkomend staging-object in de connector ruimte, maar de object typen komen niet overeen. In dit geval wordt een wijziging van het toevoegen van een verwijdering verwijderd. Een verwijderings wijziging toevoegen geeft aan de synchronisatie-engine aan dat een volledige hersynchronisatie van dit object moet plaatsvinden omdat verschillende sets regels van toepassing zijn op dit object wanneer het object type wordt gewijzigd.

Door de import status in behandeling van een staging-object in te stellen, is het mogelijk om de hoeveelheid gegevens die tijdens de synchronisatie wordt verwerkt, aanzienlijk te verminderen, omdat het systeem hiermee alleen de objecten kan verwerken die bijgewerkte gegevens hebben.

### <a name="synchronization-process"></a>Synchronisatie proces
Synchronisatie bestaat uit twee gerelateerde processen:

* Inkomende synchronisatie, wanneer de inhoud van de tijdelijke tekst wordt bijgewerkt met behulp van de gegevens in de connector ruimte.
* Uitgaande synchronisatie, wanneer de inhoud van de connector ruimte wordt bijgewerkt met behulp van gegevens in de tekst.

Met behulp van de gegevens die in de connector ruimte worden klaargezet, wordt het inkomende synchronisatie proces in de omgekeerde de geïntegreerde weer gave van de gegevens die zijn opgeslagen in de verbonden gegevens bronnen gemaakt. Alle staging-objecten of alleen die met een in behandeling zijnde import gegevens worden geaggregeerd, afhankelijk van de configuratie van de regels.

Tijdens het uitgaande synchronisatie proces worden de export objecten bijgewerkt wanneer de objecten van het omgekeerde worden gewijzigd.

Bij binnenkomende synchronisatie wordt de geïntegreerde weer gave gemaakt in het omgekeerde van de identiteits gegevens die zijn ontvangen van de verbonden gegevens bronnen. De synchronisatie-engine kan identiteits gegevens op elk gewenst moment verwerken door gebruik te maken van de meest recente identiteits gegevens uit de verbonden gegevens bron.

**Inkomende synchronisatie**

Inkomende synchronisatie omvat de volgende processen:

* **Inrichting** (ook wel **projectie** genoemd als het belang rijk is om dit proces te onderscheiden van uitgaande synchronisatie-inrichting). De synchronisatie-engine maakt een nieuw omgekeerd object op basis van een staging-object en koppelt deze. Inrichten is een bewerking op object niveau.
* **Samen voegen**. De synchronisatie-engine koppelt een staging-object aan een bestaand omgekeerd object. Een samen voeging is een bewerking op object niveau.
* **Kenmerk stroom importeren**. De synchronisatie-engine werkt de kenmerk waarden, kenmerk stroom genaamd, bij van het object in de omgekeerde tekst. Import kenmerk stroom is een bewerking op kenmerk niveau waarvoor een koppeling tussen een staging-object en een omgekeerd object is vereist.

Inrichten is het enige proces waarmee objecten in de tekst worden gemaakt. Inrichting geldt alleen voor het importeren van objecten die niet-samenvoegde objecten zijn. Tijdens de inrichting maakt synchronisatie-engine een omgekeerd object dat overeenkomt met het object type van het import object en wordt er een koppeling tot stand gebracht tussen beide objecten, waardoor een samengevoegd object wordt gemaakt.

Het proces voor samen voegen brengt ook een koppeling tot stand tussen import objecten en een omgekeerd object. Het verschil tussen samen voegen en inrichten is dat het proces van het samen voegen vereist dat het import object wordt gekoppeld aan een bestaand omgekeerd object, waarbij het inrichtings proces een nieuw omgekeerd object maakt.

De synchronisatie-engine probeert een import object toe te voegen aan een omgekeerd object met behulp van criteria die zijn opgegeven in de configuratie van de synchronisatie regel.

Tijdens het inrichten en samen voegen wordt een niet-samengevoegd object gekoppeld aan een omgekeerd object, waardoor de engine lid wordt. Nadat deze bewerkingen op object niveau zijn voltooid, kan de synchronisatie-engine de kenmerk waarden van het gekoppelde omgekeerde-object bijwerken. Dit proces wordt de kenmerk stroom importeren genoemd.

Kenmerk stroom importeren vindt plaats op alle import objecten die nieuwe gegevens bevatten en die zijn gekoppeld aan een omgekeerd object.

**Uitgaande synchronisatie**

Bij uitgaande synchronisatie-updates worden objecten geëxporteerd wanneer een omgekeerd object wordt gewijzigd, maar niet wordt verwijderd. De doel stelling van uitgaande synchronisatie is om te evalueren of wijzigingen aan objecten in de tekst van een object moeten worden bijgewerkt naar tijdelijke objecten in de connector ruimten. In sommige gevallen kunnen de wijzigingen vereisen dat tijdelijke objecten in alle connector ruimten worden bijgewerkt. Staging-objecten die worden gewijzigd, worden gemarkeerd als export in behandeling, waardoor ze objecten exporteren. Deze export objecten worden later naar de verbonden gegevens bron gepusht tijdens het export proces.

Uitgaande synchronisatie heeft drie processen:

* **Inrichten**
* **Inrichting**
* **Kenmerk stroom exporteren**

Inrichting en ongedaan maken van de inrichting zijn beide bewerkingen op object niveau. Het ongedaan maken van de inrichting is afhankelijk van het inrichten, omdat deze alleen door inrichting kan worden gestart. Het ongedaan maken van de inrichting wordt geactiveerd wanneer het inrichten de koppeling verwijdert tussen een omgekeerd object en een export object.

Het inrichten wordt altijd geactiveerd wanneer wijzigingen worden toegepast op objecten in de tekst. Als er wijzigingen worden aangebracht in de omgekeerde objecten, kan de synchronisatie-engine een van de volgende taken uitvoeren als onderdeel van het inrichtings proces:

* Gekoppelde objecten maken, waarbij een omgekeerd object wordt gekoppeld aan een zojuist gemaakt export object.
* Wijzig de naam van een gekoppeld object.
* Koppelingen tussen een omgekeerd object en tijdelijke objecten loskoppelen, waarbij een niet-samengevoegd object wordt gemaakt.

Als voor de inrichting synchronisatie-engine vereist is om een nieuw connector object te maken, is het staging-object waaraan het omgekeerde object is gekoppeld, altijd een export object, omdat het object nog niet in de verbonden gegevens bron bestaat.

Als voor de inrichting sync engine vereist is om een samengevoegd object te ontkoppelen, wordt het maken van een niet-samengevoegd object geactiveerd. Het proces voor het ongedaan maken van de inrichting verwijdert het object.

Tijdens het ongedaan maken van de inrichting verwijdert het object niet fysiek met een export object. Het object wordt gemarkeerd als **verwijderd**, wat betekent dat de Verwijder bewerking wordt klaargezet voor het object.

De kenmerk stroom exporteren vindt ook plaats tijdens het uitgaande synchronisatie proces, vergelijkbaar met de manier waarop de kenmerk stroom importeren plaatsvindt tijdens de inkomende synchronisatie. De export kenmerk stroom vindt alleen plaats tussen omgekeerde en objecten die zijn gekoppeld.

### <a name="export-process"></a>Export proces
Tijdens het export proces onderzoekt de synchronisatie-engine alle export objecten die zijn gemarkeerd als in behandeling zijnde export in de connector ruimte en worden vervolgens updates naar de verbonden gegevens bron verzonden.

De synchronisatie-engine kan het succes van een export bepalen, maar kan niet bepalen dat het proces voor identiteits beheer is voltooid. Objecten in de verbonden gegevens bron kunnen altijd worden gewijzigd door andere processen. Omdat de synchronisatie-engine geen permanente verbinding met de verbonden gegevens bron heeft, is het niet voldoende om veronderstellingen te doen over de eigenschappen van een object in de verbonden gegevens bron op basis van een geslaagde export melding.

Zo kan een proces in de verbonden gegevens bron de kenmerken van het object weer wijzigen in hun oorspronkelijke waarden (dat wil zeggen dat de verbonden gegevens bron de waarden kan overschrijven zodra de gegevens zijn gepusht door de synchronisatie-engine en is toegepast in de verbonden gegevens bron).

De synchronisatie-engine slaat gegevens over de export en import status van elk staging-object op. Als de waarden van de kenmerken die zijn opgegeven in de lijst met kenmerk opnames sinds de laatste export zijn gewijzigd, kan de opslag van de import-en export status de synchronisatie-engine inschakelen om op de juiste wijze te reageren. De synchronisatie-engine gebruikt het import proces om kenmerk waarden te bevestigen die zijn geëxporteerd naar de verbonden gegevens bron. Een vergelijking tussen de geïmporteerde en geëxporteerde informatie, zoals wordt weer gegeven in de volgende afbeelding, maakt het mogelijk om te synchroniseren of het exporteren is geslaagd of dat het moet worden herhaald.

![Diagram toont de synchronisatie van een object tussen connector ruimte en verbonden gegevens via de connector.](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Als de synchronisatie-engine bijvoorbeeld kenmerk C, met de waarde 5, naar een verbonden gegevens bron exporteert, wordt C = 5 opgeslagen in het export status geheugen. Elke extra export van dit object resulteert in een poging om C = 5 opnieuw te exporteren naar de gekoppelde gegevens bron omdat de synchronisatie-engine ervan uitgaat dat deze waarde niet permanent is toegepast op het object (dat wil zeggen, tenzij een andere waarde recent is geïmporteerd vanuit de gekoppelde gegevens bron). Het export geheugen wordt gewist wanneer C = 5 wordt ontvangen tijdens een import bewerking op het object.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md) configuratie.

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

