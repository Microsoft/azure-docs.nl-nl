---
title: Azure Analysis Services scale-out | Microsoft Docs
description: Azure Analysis Services servers repliceren met scale-out. Client query's kunnen vervolgens worden verdeeld over meerdere query replica's in een scale-out-query groep.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 24ee31b941d836d296c30927cfb9636f3023fa89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019427"
---
# <a name="azure-analysis-services-scale-out"></a>Uitschalen van Azure Analysis Services

Met scale-out kunnen client query's worden verdeeld over meerdere *query replica's* in een *query groep*, waardoor de reactie tijden tijdens veel query's worden verminderd. U kunt ook de verwerking van de query groep scheiden, zodat de client query's niet nadelig worden beïnvloed door de verwerking van bewerkingen. Scale-out kan worden geconfigureerd in Azure Portal of door gebruik te maken van de Analysis Services REST API.

Uitschalen is beschikbaar voor servers in de prijs categorie Standard. Elke query replica wordt gefactureerd op basis van hetzelfde aantal als uw server. Alle query replica's worden in dezelfde regio gemaakt als uw server. Het aantal query replica's dat u kunt configureren, is beperkt door de regio waarin uw server zich bevindt. Zie [Beschik baarheid per regio](analysis-services-overview.md#availability-by-region)voor meer informatie. Als u uitschalen uitbreidt, wordt de hoeveelheid beschikbaar geheugen voor uw server niet verg root. Als u geheugen wilt verg Roten, moet u een upgrade voor uw abonnement uitvoeren. 

## <a name="why-scale-out"></a>Waarom uitschalen?

In een typische Server implementatie fungeert één server als zowel de verwerkings server als de query server. Als het aantal client query's voor modellen op uw server groter is dan de QPU (query processing units) voor het abonnement van uw server, of als de verwerking van modellen op hetzelfde moment als hoge query werkbelastingen plaatsvindt, kan de prestaties afnemen. 

Met scale-out kunt u een query pool maken met Maxi maal zeven aanvullende bronnen voor query replica's (acht totaal, inclusief uw *primaire* server). U kunt het aantal replica's in de query pool schalen om te voldoen aan QPU-vereisten op kritieke tijden, en u kunt op elk gewenst moment een verwerkings server scheiden van de query pool. 

Ongeacht het aantal query replica's in een query groep, worden de verwerkings werkbelastingen niet verdeeld over query replica's. De primaire server fungeert als de verwerkings server. Query replica's bieden alleen query's voor de model databases die zijn gesynchroniseerd tussen de primaire server en elke replica in de query groep. 

Wanneer u uitschaalt, kan het tot vijf minuten duren voordat nieuwe query replica's incrementeel worden toegevoegd aan de query groep. Wanneer alle nieuwe query replica's actief zijn, worden nieuwe client verbindingen verdeeld over alle resources in de query groep. Bestaande client verbindingen worden niet gewijzigd ten opzichte van de resource waarmee ze momenteel zijn verbonden. Bij het schalen in worden alle bestaande client verbindingen met een bron van de query groep die uit de query groep wordt verwijderd, beëindigd. Clients kunnen opnieuw verbinding maken met een resterende resource in de query groep.

## <a name="how-it-works"></a>Uitleg

Wanneer u de eerste keer scale-out configureert, worden model databases op uw primaire server *automatisch* gesynchroniseerd met nieuwe replica's in een nieuwe query groep. Automatische synchronisatie wordt slechts één keer uitgevoerd. Tijdens automatische synchronisatie worden de gegevens bestanden van de primaire server (versleuteld op rest in Blob Storage) gekopieerd naar een tweede locatie, ook versleuteld op rest in Blob Storage. Replica's in de query groep worden vervolgens *gehydrateerd* met gegevens uit de tweede set bestanden. 

Hoewel een automatische synchronisatie alleen wordt uitgevoerd wanneer u een server voor de eerste keer uitbreidt, kunt u ook een hand matige synchronisatie uitvoeren. Bij het synchroniseren worden de gegevens van replica's in de query groep vergeleken met die van de primaire server. Wanneer modellen op de primaire server worden verwerkt (vernieuwd), moet er een synchronisatie worden uitgevoerd *nadat* de verwerkings bewerkingen zijn voltooid. Deze synchronisatie kopieert bijgewerkte gegevens van de bestanden van de primaire server in Blob Storage naar de tweede set bestanden. Replica's in de query groep worden vervolgens gehydrateerd met bijgewerkte gegevens uit de tweede set bestanden in Blob Storage. 

Wanneer u een volgende uitschaal bewerking uitvoert, bijvoorbeeld het aantal replica's in de query groep verhogen van twee naar vijf, worden de nieuwe replica's gehydrateerd met gegevens uit de tweede set bestanden in Blob Storage. Er is geen synchronisatie. Als u vervolgens een synchronisatie uitvoert nadat u uitschalen hebt uitgevoerd, worden de nieuwe replica's in de query groep twee keer gehydrateerd: een redundante Hydration. Wanneer u een volgende scale-out-bewerking uitvoert, is het belang rijk dat u rekening houdt met het volgende:

* Voer een synchronisatie uit *vóór de uitschaal bewerking* om redundante Hydration van de toegevoegde replica's te voor komen. Gelijktijdige synchronisatie-en scale-out bewerkingen die op hetzelfde moment worden uitgevoerd, zijn niet toegestaan.

* Wanneer u zowel verwerkings- *als* uitschaal bewerkingen automatiseert, is het belang rijk om eerst gegevens op de primaire server te verwerken, vervolgens een synchronisatie uit te voeren en vervolgens de uitschaal bewerking uit te voeren. Met deze reeks wordt de minimale impact op QPU en geheugen bronnen gegarandeerd.

* Tijdens scale-out bewerkingen zijn alle servers in de query groep, met inbegrip van de primaire server, tijdelijk offline.

* Synchronisatie is ook toegestaan wanneer er geen replica's in de query groep zijn. Als u opschalen van nul naar een of meer replica's met nieuwe gegevens van een verwerkings bewerking op de primaire server, voert u de synchronisatie eerst uit zonder replica's in de query groep en vervolgens scale-out. Bij het synchroniseren van wordt geen redundante Hydration van de zojuist toegevoegde replica's voor komen.

* Wanneer u een model database van de primaire server verwijdert, wordt deze niet automatisch verwijderd uit replica's in de query groep. U moet een synchronisatie bewerking uitvoeren met behulp van de Power shell [-opdracht Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) die het bestand/de s voor die data base verwijdert uit de gedeelde Blob-opslag locatie van de replica en de model database vervolgens op de replica's in de query groep verwijdert. Als u wilt bepalen of een model database bestaat voor replica's in de query pool, maar niet op de primaire server, moet u ervoor zorgen dat de **afzonderlijke verwerkings server van de query groep** is ingesteld op **Ja**. Gebruik vervolgens SSMS om verbinding te maken met de primaire server met behulp van de `:rw` kwalificatie om te controleren of de data base bestaat. Maak vervolgens verbinding met replica's in de query groep door verbinding te maken zonder de `:rw` kwalificatie om te zien of dezelfde data base ook bestaat. Als de data base bestaat in replica's in de query groep, maar niet op de primaire server, voert u een synchronisatie bewerking uit.   

* Bij het wijzigen van de naam van een Data Base op de primaire server is er een extra stap die nodig is om ervoor te zorgen dat de data base correct is gesynchroniseerd met replica's. Nadat u de naam hebt gewijzigd, moet u een synchronisatie uitvoeren met behulp van de opdracht [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) en de `-Database` para meter met de oude database naam opgeven. Deze synchronisatie verwijdert de data base en bestanden met de oude naam uit een of meer replica's. Voer vervolgens een andere synchronisatie uit `-Database` met de para meter met de naam van de nieuwe data base. Bij de tweede synchronisatie wordt de data base met de nieuwe naam gekopieerd naar de tweede set bestanden en worden eventuele replica's gehydrateerd. Deze synchronisaties kunnen niet worden uitgevoerd met behulp van de opdracht model synchroniseren in de portal.

### <a name="synchronization-mode"></a>Synchronisatie modus

Standaard worden query replica's volledig opnieuw gehydrateerd, niet incrementeel. Rehydratatie treedt op in fasen. Ze worden twee tegelijk losgekoppeld en gekoppeld (ervan uitgaande dat er ten minste drie replica's zijn) om ervoor te zorgen dat ten minste één replica online wordt gehouden voor query's op een bepaald moment. In sommige gevallen moeten clients mogelijk opnieuw verbinding maken met een van de online replica's terwijl dit proces plaatsvindt. Met behulp van de **ReplicaSyncMode** -instelling kunt u nu de synchronisatie van query's replica's opgeven, parallel. Parallelle synchronisatie biedt de volgende voor delen: 

- Aanzienlijke vermindering van de synchronisatie tijd. 
- Gegevens in replica's zijn waarschijnlijker consistent tijdens het synchronisatie proces. 
- Omdat data bases tijdens het synchronisatie proces online blijven op alle replica's, hoeven clients niet opnieuw verbinding te maken. 
- De cache in het geheugen wordt incrementeel bijgewerkt met alleen de gewijzigde gegevens. Dit kan sneller zijn dan het volledige reactiveren van het model. 

#### <a name="setting-replicasyncmode"></a>ReplicaSyncMode instellen

Gebruik SSMS om ReplicaSyncMode in te stellen in geavanceerde eigenschappen. De mogelijke waarden zijn: 

- `1` (standaard): volledige replica database rehydratatie in fasen (incrementeel). 
- `2`: Geoptimaliseerde synchronisatie parallel. 

![RelicaSyncMode-instelling](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Bij het instellen van **ReplicaSyncMode = 2**, afhankelijk van hoeveel van de cache moet worden bijgewerkt, kan extra geheugen worden gebruikt door de query replica's. Als u de data base online en beschikbaar wilt houden voor query's, afhankelijk van de hoeveelheid gegevens die is gewijzigd, kan het zijn dat de bewerking het geheugen van de replica moet *Double* hebben, omdat zowel de oude als de nieuwe segmenten tegelijkertijd in het geheugen worden bewaard. Replica knooppunten hebben dezelfde geheugen toewijzing als het primaire knoop punt en er is meestal extra geheugen op het primaire knoop punt voor de vernieuwings bewerkingen, waardoor het onwaarschijnlijk is dat er onvoldoende geheugen beschikbaar is voor de replica's. Daarnaast is het gebruikelijk dat de data base incrementeel wordt bijgewerkt op het primaire knoop punt en daarom de vereiste voor dubbele het geheugen ongebruikelijk moet zijn. Als er een fout optreedt in de synchronisatie bewerking, wordt de standaard techniek opnieuw geprobeerd (twee tegelijk koppelen/loskoppelen). 

### <a name="separate-processing-from-query-pool"></a>Afzonderlijke verwerking van de query pool

U kunt de verwerkings server scheiden van de query groep voor een maximale prestaties voor zowel verwerkings-als query bewerkingen. Wanneer deze worden gescheiden, worden nieuwe client verbindingen alleen toegewezen aan query replica's in de query groep. Als verwerkings bewerkingen slechts korte tijd duren, kunt u ervoor kiezen om de verwerkings server alleen te scheiden van de query pool voor de hoeveelheid tijd die nodig is voor het uitvoeren van verwerkings-en synchronisatie bewerkingen en deze vervolgens weer op te nemen in de query groep. Wanneer u de verwerkings server van de query groep scheidt of deze opnieuw in de query groep toevoegt, kan het tot vijf minuten duren voordat de bewerking is voltooid.

## <a name="monitor-qpu-usage"></a>QPU-gebruik bewaken

Als u wilt bepalen of uitschalen voor uw server nodig is, [controleert u de server](analysis-services-monitor.md) in azure Portal met behulp van metrische gegevens. Als uw QPU regel matig uitdergelijke, betekent dit dat het aantal query's voor uw modellen de limiet van QPU voor uw abonnement overschrijdt. De metrische gegevens wachtrij lengte van de query pool neemt ook toe wanneer het aantal query's in de wachtrij van de query thread de beschik bare QPU overschrijdt. 

Een andere goede metrische waarde om te kijken, is gemiddelde QPU door ServerResourceType. Deze metrische gegevens vergelijkt de gemiddelde QPU voor de primaire server met de query groep. 

![Metrische gegevens van query uitschalen](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**QPU configureren met ServerResourceType**

1. In een lijn diagram met metrische gegevens klikt u op **metrische gegevens toevoegen**. 
2. Selecteer uw server in **resource**, Selecteer in **metrische naam ruimte** de optie **Analysis Services standaard metrieken** en selecteer vervolgens in **metrische gegevens** **QPU**, en selecteer vervolgens bij **aggregatie** de optie **Gem**. 
3. Klik op **splitsing Toep assen**. 
4. In **waarden** selecteert u **ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Gedetailleerde logboek registratie voor diagnostische gegevens

Gebruik Azure Monitor logboeken voor meer gedetailleerde diagnostische gegevens van Server bronnen die worden uitgeschaald. Met Logboeken kunt u Log Analytics query's gebruiken om de QPU en het geheugen van de server en de replica te verdelen. Zie voorbeeld query's in [Analysis Services diagnostische gegevens registratie](analysis-services-logging.md#example-queries)voor meer informatie.


## <a name="configure-scale-out"></a>Uitschalen configureren

### <a name="in-azure-portal"></a>In Azure Portal

1. Klik in de portal op **uitschalen**. Gebruik de schuif regelaar om het aantal query replica servers te selecteren. Het aantal replica's dat u kiest, is naast uw bestaande server.  

2. Selecteer op **de verwerkings server scheiden van de query groep** Ja om de verwerkings server van de query servers uit te sluiten. Client [verbindingen](#connections) die gebruikmaken van de standaard Connection String (zonder `:rw` ) worden omgeleid naar replica's in de query groep. 

   ![Schuif regelaar voor uitschalen](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klik op **Opslaan** om uw nieuwe query replica servers in te richten. 

Bij het configureren van scale-out voor een server wordt de eerste keer dat modellen op de primaire server automatisch worden gesynchroniseerd met replica's in de query groep. Automatische synchronisatie vindt slechts eenmaal plaats, wanneer u scale-out voor het eerst op een of meer replica's configureert. Volgende wijzigingen in het aantal replica's op dezelfde server *activeren geen andere automatische synchronisatie*. De automatische synchronisatie wordt niet opnieuw uitgevoerd, zelfs niet als u de server instelt op nul replica's en vervolgens weer uitschaalt naar een wille keurig aantal replica's. 

## <a name="synchronize"></a>Synchroniseren 

Synchronisatie bewerkingen moeten hand matig of via de REST API worden uitgevoerd.

### <a name="in-azure-portal"></a>In Azure Portal

In **overzicht** > model > **model synchroniseren**.

![Pictogram synchroniseren](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST-API

Gebruik de **synchronisatie** bewerking.

#### <a name="synchronize-a-model"></a>Een model synchroniseren   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Synchronisatie status ophalen  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Retour status codes:


|Code  |Beschrijving  |
|---------|---------|
|-1     |  Ongeldig       |
|0     | Repliceren        |
|1     |  Reactiveren       |
|2     |   Voltooid       |
|3     |   Mislukt      |
|4     |    Wordt voltooid     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u Power shell gebruikt, [moet u de nieuwste Azure PowerShell-module installeren of bijwerken](/powershell/azure/install-az-ps). 

Als u synchronisatie wilt uitvoeren, gebruikt u [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Als u het aantal query replica's wilt instellen, gebruikt u [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Geef de optionele `-ReadonlyReplicaCount` para meter op.

Als u de verwerkings server van de query groep wilt scheiden, gebruikt u [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Geef de optionele `-DefaultConnectionMode` para meter op die moet worden gebruikt `Readonly` .

Zie [een Service-Principal gebruiken met de module AZ. AnalysisServices](analysis-services-service-principal.md#azmodule)voor meer informatie.

## <a name="connections"></a>Verbindingen

Op de overzichts pagina van de server bevinden zich twee server namen. Als u nog geen uitschalen hebt geconfigureerd voor een server, werken beide server namen op hetzelfde niveau. Zodra u scale-out voor een server hebt geconfigureerd, moet u de juiste server naam opgeven, afhankelijk van het verbindings type. 

Voor client verbindingen van eind gebruikers, zoals Power BI Desktop, Excel en aangepaste apps, gebruikt u **Server naam**. 

Voor SSMS, Visual Studio en verbindings reeksen in Power shell, Azure function-apps en AMO, gebruikt u de naam van de **beheer server**. De naam van de beheer server bevat een speciale `:rw` Kwalificatie (lezen/schrijven). Alle verwerkings bewerkingen worden uitgevoerd op de beheer server (primair).

![Server namen](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Omhoog schalen, omlaag schalen versus scale-out

U kunt de prijs categorie wijzigen op een server met meerdere replica's. Dezelfde prijs categorie is van toepassing op alle replica's. Bij een schaal bewerking worden eerst alle replica's in één keer omlaag gebracht. vervolgens worden alle replica's op de nieuwe prijs categorie weer geven.

## <a name="troubleshoot"></a>Problemen oplossen

**Probleem:** Fout bij het ophalen **van gebruikers kan het exemplaar van de server niet vinden \<Name of the server> in de verbindings modus readonly.**

**Oplossing:** Wanneer u de **afzonderlijke verwerking van de verwerkings server van de optie query groep** selecteert, worden client verbindingen die gebruikmaken van de standaard Connection String (zonder `:rw` ), omgeleid naar de replica's van de query groep. Als replica's in de query groep nog niet online zijn omdat de synchronisatie nog niet is voltooid, kunnen omgeleide client verbindingen mislukken. Als u mislukte verbindingen wilt voor komen, moeten er ten minste twee servers in de query groep aanwezig zijn bij het uitvoeren van een synchronisatie. Elke server wordt afzonderlijk gesynchroniseerd, terwijl anderen online blijven. Als u de verwerkings server niet in de query groep hebt tijdens de verwerking, kunt u deze verwijderen uit de groep voor verwerking en deze vervolgens opnieuw toevoegen aan de groep nadat de verwerking is voltooid, maar vóór de synchronisatie. Gebruik geheugen-en QPU-metrische gegevens om de synchronisatie status te bewaken.



## <a name="related-information"></a>Gerelateerde informatie

[Metrische Server gegevens bewaken](analysis-services-monitor.md)   
[Azure Analysis Services beheren](analysis-services-manage.md)