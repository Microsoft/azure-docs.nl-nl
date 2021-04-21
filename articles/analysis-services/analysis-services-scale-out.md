---
title: Azure Analysis Services uitschalen| Microsoft Docs
description: Repliceer Azure Analysis Services servers met uitschalen. Clientquery's kunnen vervolgens worden gedistribueerd over meerdere queryreplica's in een uitschaalquerypool.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2385cb811e322bd44daefa03d821b2ae47e0652
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769244"
---
# <a name="azure-analysis-services-scale-out"></a>Uitschalen van Azure Analysis Services

Met uitschalen kunnen clientquery's  worden gedistribueerd over meerdere queryreplica's in een *querygroep,* waardoor de reactietijden tijdens hoge querywerkbelastingen worden verkort. U kunt de verwerking ook scheiden van de querygroep, zodat clientquery's niet nadelig worden beïnvloed door verwerkingsbewerkingen. Uitschalen kan worden geconfigureerd in Azure Portal of met behulp van de Analysis Services REST API.

Uitschalen is beschikbaar voor servers in de prijscategorie Standard. Elke queryreplica wordt gefactureerd tegen hetzelfde tarief als uw server. Alle queryreplica's worden gemaakt in dezelfde regio als uw server. Het aantal queryreplica's dat u kunt configureren, wordt beperkt door de regio waarin uw server zich in zich. Zie Beschikbaarheid per regio [voor meer informatie.](analysis-services-overview.md#availability-by-region) Door uit te schalen wordt de hoeveelheid beschikbaar geheugen voor uw server niet vergroot. Als u het geheugen wilt vergroten, moet u uw abonnement upgraden. 

## <a name="why-scale-out"></a>Waarom uitschalen?

In een typische serverimplementatie fungeert één server als zowel verwerkingsserver als queryserver. Als het aantal clientquery's op modellen op uw server de QPU (Query Processing Units) voor het plan van uw server overschrijdt, of als de modelverwerking plaatsvindt op hetzelfde moment als hoge querywerkbelastingen, kunnen de prestaties afnemen. 

Met uitschalen kunt u een querygroep maken met maximaal zeven extra queryreplicaresources (in totaal acht, inclusief uw *primaire* server). U kunt het aantal replica's in de querygroep schalen om op kritieke momenten te voldoen aan de QPU-eisen en u kunt een verwerkingsserver op elk moment scheiden van de querygroep. 

Ongeacht het aantal queryreplica's dat u in een querygroep hebt, worden verwerkingsworkloads niet verdeeld over queryreplica's. De primaire server fungeert als de verwerkingsserver. Queryreplica's dienen alleen query's voor de modeldatabases die zijn gesynchroniseerd tussen de primaire server en elke replica in de querygroep. 

Bij het uitschalen kan het tot vijf minuten duren voordat nieuwe queryreplica's incrementeel worden toegevoegd aan de querygroep. Wanneer alle nieuwe queryreplica's actief zijn, worden nieuwe clientverbindingen verdeeld over resources in de querygroep. Bestaande clientverbindingen worden niet gewijzigd van de resource waar ze momenteel mee zijn verbonden. Bij het inschalen worden alle bestaande clientverbindingen met een querygroepresource die uit de querygroep wordt verwijderd, beëindigd. Clients kunnen opnieuw verbinding maken met een resterende querygroepresource.

## <a name="how-it-works"></a>Uitleg

Bij de eerste keer uitschalen worden modeldatabases  op uw primaire server automatisch gesynchroniseerd met nieuwe replica's in een nieuwe querygroep. Automatische synchronisatie vindt slechts één keer plaats. Tijdens automatische synchronisatie worden de gegevensbestanden van de primaire server (in rust versleuteld in blobopslag) gekopieerd naar een tweede locatie, ook versleuteld at rest in blob-opslag. Replica's in de querygroep worden vervolgens gedrenkt *met* gegevens uit de tweede set bestanden. 

Hoewel een automatische synchronisatie alleen wordt uitgevoerd wanneer u een server voor het eerst uitschaalt, kunt u ook een handmatige synchronisatie uitvoeren. Synchronisatie zorgt ervoor dat gegevens op replica's in de querygroep overeenkomen met die van de primaire server. Bij het verwerken (vernieuwen) van modellen op de primaire server moet een synchronisatie worden uitgevoerd *nadat* de verwerkingsbewerkingen zijn voltooid. Met deze synchronisatie worden bijgewerkte gegevens gekopieerd van de bestanden van de primaire server in blobopslag naar de tweede set bestanden. Replica's in de querygroep worden vervolgens voorzien van bijgewerkte gegevens uit de tweede set bestanden in blobopslag. 

Bij het uitvoeren van een volgende uitschaalbewerking, bijvoorbeeld het verhogen van het aantal replica's in de querygroep van twee naar vijf, worden de nieuwe replica's geschaald met gegevens uit de tweede set bestanden in blobopslag. Er is geen synchronisatie. Als u vervolgens na het uitschalen een synchronisatie uitvoert, worden de nieuwe replica's in de querygroep twee keer geslingeerd: een redundante redundante redundantie. Bij het uitvoeren van een volgende uitschaalbewerking is het belangrijk om het volgende in gedachten te houden:

* Voer een synchronisatie uit *vóór de uitschaalbewerking om* redundante redundante redundantie van de toegevoegde replica's te voorkomen. Gelijktijdige synchronisatie- en uitschaalbewerkingen die op hetzelfde moment worden uitgevoerd, zijn niet toegestaan.

* Bij het automatiseren  van zowel verwerkings- als uitschaalbewerkingen is het belangrijk om eerst gegevens op de primaire server te verwerken, vervolgens een synchronisatie uit te voeren en vervolgens de uitschaalbewerking uit te voeren. Deze reeks garandeert minimale gevolgen voor QPU en geheugenbronnen.

* Tijdens uitschaalbewerkingen zijn alle servers in de querygroep, met inbegrip van de primaire server, tijdelijk offline.

* Synchronisatie is toegestaan, zelfs wanneer er geen replica's in de querygroep zijn. Als u uitschaalt van nul naar een of meer replica's met nieuwe gegevens van een verwerkingsbewerking op de primaire server, voert u eerst de synchronisatie uit zonder replica's in de querygroep en vervolgens uitschalen. Synchronisatie vóór uitschalen voorkomt redundante redundante redundantie van de zojuist toegevoegde replica's.

* Wanneer u een modeldatabase van de primaire server wilt verwijderen, wordt deze niet automatisch verwijderd uit replica's in de querygroep. U moet een synchronisatiebewerking uitvoeren met behulp van de [PowerShell-opdracht Sync-AzAnalysisServicesInstance,](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) die het bestand/de bestanden voor die database verwijdert uit de gedeelde blobopslaglocatie van de replica en vervolgens de modeldatabase op de replica's in de querygroep verwijdert. Als u wilt bepalen of er een modeldatabase bestaat op replica's in de querygroep, maar niet op de primaire server, zorgt u ervoor dat de instelling De verwerkingsserver scheiden van **de querygroep** is ingesteld op **Ja.** Gebruik vervolgens SSMS om verbinding te maken met de primaire server met behulp van de `:rw` kwalificatie om te zien of de database bestaat. Maak vervolgens verbinding met replica's in de querygroep door verbinding te maken zonder de kwalificatie om te zien `:rw` of dezelfde database ook bestaat. Als de database op replica's in de querygroep bestaat, maar niet op de primaire server, moet u een synchronisatiebewerking uitvoeren.   

* Bij het wijzigen van de naam van een database op de primaire server is er een extra stap nodig om ervoor te zorgen dat de database correct wordt gesynchroniseerd met eventuele replica's. Nadat u de naam hebt hernoemd, voert u een synchronisatie uit met behulp van de opdracht [Sync-AzAnalysisServicesInstance,](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) die de parameter met de oude `-Database` databasenaam opgeeft. Met deze synchronisatie worden de database en bestanden met de oude naam uit alle replica's verwijderd. Voer vervolgens een andere synchronisatie uit en geef de `-Database` parameter op met de naam van de nieuwe database. Met de tweede synchronisatie wordt de zojuist benoemde database gekopieerd naar de tweede set bestanden en worden alle replica's verwijderd. Deze synchronisaties kunnen niet worden uitgevoerd met behulp van de opdracht Model synchroniseren in de portal.

### <a name="synchronization-mode"></a>Synchronisatiemodus

Queryreplica's worden standaard volledig gerehydrateerd, niet incrementeel. Rehydratatie vindt in fasen plaats. Ze worden losgekoppeld en twee tegelijk gekoppeld (ervan uitgaande dat er ten minste drie replica's zijn) om ervoor te zorgen dat ten minste één replica online wordt gehouden voor query's op een bepaald moment. In sommige gevallen moeten clients mogelijk opnieuw verbinding maken met een van de online replica's terwijl dit proces plaatsvindt. Met behulp van **de instelling ReplicaSyncMode** kunt u nu de synchronisatie van queryreplica's parallel opgeven. Parallelle synchronisatie biedt de volgende voordelen: 

- Aanzienlijke vermindering van de synchronisatietijd. 
- Gegevens tussen replica's zijn waarschijnlijk consistent tijdens het synchronisatieproces. 
- Omdat databases online worden gehouden op alle replica's tijdens het synchronisatieproces, hoeven clients niet opnieuw verbinding te maken. 
- De cache in het geheugen wordt incrementeel bijgewerkt met alleen de gewijzigde gegevens, wat sneller kan zijn dan het model volledig te rehydrateren. 

#### <a name="setting-replicasyncmode"></a>ReplicaSyncMode instellen

Gebruik SSMS om ReplicaSyncMode in te stellen in Geavanceerde eigenschappen. De mogelijke waarden zijn: 

- `1` (standaardinstelling): volledige replicadatabaserehydratatie in fasen (incrementeel). 
- `2`: Geoptimaliseerde synchronisatie parallel. 

![Instelling RelicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Bij het **instellen van ReplicaSyncMode=2** kan er, afhankelijk van hoeveel van de cache moet worden bijgewerkt, extra geheugen worden verbruikt door de queryreplica's. Om de database online te houden en beschikbaar te houden voor query's, kan  de bewerking, afhankelijk van hoeveel gegevens zijn gewijzigd, maximaal twee keer zoveel geheugen op de replica vereisen, omdat zowel de oude als de nieuwe segmenten tegelijkertijd in het geheugen worden bewaard. Replicaknooppunten hebben dezelfde geheugentoewijzing als het primaire knooppunt en er is normaal gesproken extra geheugen op het primaire knooppunt voor vernieuwingsbewerkingen, waardoor het onwaarschijnlijk is dat de replica's geen geheugen meer hebben. Daarnaast is het algemene scenario dat de database incrementeel wordt bijgewerkt op het primaire knooppunt en dat de vereiste voor het dubbele van het geheugen daarom ongebruikelijk moet zijn. Als er bij de synchronisatiebewerking een fout in het geheugen is opgetreden, wordt het opnieuw uitgevoerd met behulp van de standaardtechniek (twee tegelijk koppelen/loskoppelen). 

### <a name="separate-processing-from-query-pool"></a>Verwerking scheiden van querygroep

Voor maximale prestaties voor zowel verwerkings- als querybewerkingen kunt u ervoor kiezen om uw verwerkingsserver te scheiden van de querygroep. Wanneer ze zijn gescheiden, worden nieuwe clientverbindingen alleen toegewezen aan queryreplica's in de querygroep. Als verwerkingsbewerkingen slechts een korte tijd duren, kunt u ervoor kiezen om de verwerkingsserver alleen te scheiden van de querygroep voor de hoeveelheid tijd die nodig is om verwerkings- en synchronisatiebewerkingen uit te voeren en deze vervolgens weer op te nemen in de querygroep. Wanneer u de verwerkingsserver van de querygroep scheidt of weer toevoegt aan de querygroep, kan het maximaal vijf minuten duren voordat de bewerking is voltooid.

## <a name="monitor-qpu-usage"></a>QPU-gebruik bewaken

Als u wilt bepalen of uitschalen voor uw server nodig is, controleert u [uw server](analysis-services-monitor.md) in Azure Portal met behulp van metrische gegevens. Als uw QPU regelmatig uit het maximum komt, betekent dit dat het aantal query's op uw modellen de QPU-limiet voor uw plan overschrijdt. De metrische gegevens voor de wachtrijlengte van querypools nemen ook toe wanneer het aantal query's in de wachtrij van de querythreadpool de beschikbare QPU overschrijdt. 

Een andere goede metrische waarde om te bekijken is het gemiddelde QPU per ServerResourceType. Deze metrische waarde vergelijkt de gemiddelde QPU voor de primaire server met de querygroep. 

![Metrische gegevens voor uitschalen van query's](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**QPU configureren op ServerResourceType**

1. Klik in een lijndiagram met metrische gegevens **op Metrische waarde toevoegen.** 
2. Selecteer in **RESOURCE** uw server, selecteer vervolgens in **METRISCHE** NAAMRUIMTE de **optie Analysis Services standard metrics** en selecteer vervolgens in **METRISCHE** GEGEVENS **QPU** en selecteer vervolgens in **AGGREGATIE** De optie **Gem.** 
3. Klik **op Splitsen toepassen.** 
4. Selecteer **in WAARDEN** De optie **ServerResourceType.**  

### <a name="detailed-diagnostic-logging"></a>Gedetailleerde diagnostische logboekregistratie

Gebruik Azure Monitor logboeken voor gedetailleerdere diagnostische gegevens van uitgebreide serverresources. Met logboeken kunt u Log Analytics-query's gebruiken om QPU en geheugen op server en replica uit te breken. Zie voor meer informatie voorbeeldquery's in Analysis Services [diagnostische logboekregistratie.](analysis-services-logging.md#example-queries)


## <a name="configure-scale-out"></a>Uitschalen configureren

### <a name="in-azure-portal"></a>In Azure Portal

1. Klik in de portal op **Uitschalen.** Gebruik de schuifregelaar om het aantal queryreplicaservers te selecteren. Het aantal replica's dat u kiest, is een aanvulling op uw bestaande server.  

2. Selecteer **ja in Scheid de verwerkingsserver van de querygroep** om uw verwerkingsserver uit te sluiten van queryservers. Clientverbindingen [met](#connections) de standaardwaarde connection string (zonder `:rw` ) worden omgeleid naar replica's in de querygroep. 

   ![Schuifregelaar voor uitschalen](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klik **op Opslaan om** uw nieuwe queryreplicaservers in terichten. 

Wanneer u voor het eerst uitschalen voor een server configureert, worden modellen op uw primaire server automatisch gesynchroniseerd met replica's in de querygroep. Automatische synchronisatie vindt slechts één keer plaats wanneer u voor het eerst uitschalen naar een of meer replica's configureert. Volgende wijzigingen in het aantal replica's op dezelfde server *activeren geen andere automatische synchronisatie.* Automatische synchronisatie wordt niet opnieuw uitgevoerd, zelfs niet als u de server in stelt op nul replica's en vervolgens opnieuw uitschaalt naar een aantal replica's. 

## <a name="synchronize"></a>Synchroniseren 

Synchronisatiebewerkingen moeten handmatig of met behulp van de REST API.

### <a name="in-azure-portal"></a>In Azure Portal

In **Overzicht** > model > **Model synchroniseren.**

![Synchronisatiepictogram](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST-API

Gebruik de **synchronisatiebewerking.**

#### <a name="synchronize-a-model"></a>Een model synchroniseren   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Synchronisatiestatus krijgen  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Retourstatuscodes:


|Code  |Beschrijving  |
|---------|---------|
|-1     |  Ongeldig       |
|0     | Repliceren        |
|1     |  Rehydrateren       |
|2     |   Voltooid       |
|3     |   Mislukt      |
|4     |    Finaliseren     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u PowerShell gebruikt, [installeert of updatet](/powershell/azure/install-az-ps)u de meest recente Azure PowerShell module . 

Gebruik [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)om synchronisatie uit te voeren.

Gebruik [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)om het aantal queryreplica's in te stellen. Geef de optionele `-ReadonlyReplicaCount` parameter op.

Gebruik [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)om de verwerkingsserver te scheiden van de querygroep. Geef de optionele `-DefaultConnectionMode` parameter op om te `Readonly` gebruiken.

Zie Using [a service principal with the Az.AnalysisServices module (Een service-principal gebruiken met de module Az.AnalysisServices) voor meer informatie.](analysis-services-service-principal.md#azmodule)

## <a name="connections"></a>Verbindingen

Op de pagina Overzicht van uw server staan twee servernamen. Als u uitschalen voor een server nog niet hebt geconfigureerd, werken beide servernamen hetzelfde. Zodra u uitschalen voor een server hebt geconfigureerd, moet u de juiste servernaam opgeven, afhankelijk van het verbindingstype. 

Voor clientverbindingen van eindgebruikers, zoals Power BI Desktop, Excel en aangepaste apps, gebruikt u **Servernaam.** 

Gebruik voor SSMS, Visual Studio en verbindingsreeksen in PowerShell, Azure Function-apps en AMO de naam **van de beheerserver.** De naam van de beheerserver bevat een speciale `:rw` kwalificatie (lezen/schrijven). Alle verwerkingsbewerkingen worden uitgevoerd op de (primaire) beheerserver.

![Servernamen](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Omhoog schalen, omlaag schalen versus uitschalen

U kunt de prijscategorie op een server met meerdere replica's wijzigen. Dezelfde prijscategorie is van toepassing op alle replica's. Met een schaalbewerking worden eerst alle replica's in één keer omlaag schalen, waarna alle replica's in de nieuwe prijscategorie worden uitgevoerd.

## <a name="troubleshoot"></a>Problemen oplossen

**Probleem:** Gebruikers krijgen de fout **'Instantie van server \<Name of the server> ' niet vinden in verbindingsmodus 'ReadOnly'.**

**Oplossing:** Wanneer u de optie De verwerkingsserver scheiden van de **querygroep** selecteert, worden clientverbindingen met de standaardwaarde connection string (zonder ) omgeleid naar `:rw` querygroepreplica's. Als replica's in de querygroep nog niet online zijn omdat de synchronisatie nog niet is voltooid, kunnen omgeleide clientverbindingen mislukken. Om mislukte verbindingen te voorkomen, moeten er ten minste twee servers in de querygroep zijn bij het uitvoeren van een synchronisatie. Elke server wordt afzonderlijk gesynchroniseerd terwijl anderen online blijven. Als u ervoor kiest om de verwerkingsserver niet in de querygroep te hebben tijdens de verwerking, kunt u ervoor kiezen om deze te verwijderen uit de pool voor verwerking en deze vervolgens weer toe te voegen aan de groep nadat de verwerking is voltooid, maar vóór de synchronisatie. Gebruik geheugen- en QPU-metrische gegevens om de synchronisatiestatus te bewaken.



## <a name="related-information"></a>Gerelateerde informatie

[Metrische servergegevens bewaken](analysis-services-monitor.md)   
[Beheer Azure Analysis Services](analysis-services-manage.md)
