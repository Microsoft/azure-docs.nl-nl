---
title: Azure Traffic Analytics | Microsoft Docs
description: Meer informatie over het analyseren van stroom logboeken van de Azure-netwerk beveiligings groep met Traffic Analytics.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.reviewer: vinigam
ms.custom: references_regions
ms.openlocfilehash: 8d877f666e03bbbe2b9d4f01bc9059b95f9a1f6b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385272"
---
# <a name="traffic-analytics"></a>Traffic Analytics

Traffic Analytics is een Cloud oplossing die inzicht geeft in de activiteit van gebruikers en toepassingen in Cloud netwerken. Traffic Analytics analyseert Network Watcher-stroom logboeken voor netwerk beveiligings groepen (NSG) om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Met Traffic Analytics kunt u het volgende doen:

- Visualiseer netwerk activiteiten in uw Azure-abonnementen en Identificeer HOTS pots.
- Identificeer beveiligings Risico's voor en beveilig uw netwerk met informatie zoals open poorten, toepassingen die toegang proberen te krijgen tot internet en virtuele machines (VM) die verbinding maken met een Rogue-netwerk.
- Inzicht in de verkeers stromen in azure-regio's en Internet om uw netwerk implementatie te optimaliseren voor prestaties en capaciteit.
- Spoort onjuiste netwerk configuraties in de loop van mislukte verbindingen in uw netwerk.

> [!NOTE]
> Traffic Analytics ondersteunt nu het verzamelen van gegevens van NSG-stroom logboeken met een hogere frequentie van 10 minuten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Waarom Traffic Analytics?

Het is essentieel om uw eigen netwerk te bewaken, beheren en kennen voor ongeoorloofde beveiliging, naleving en prestaties. Het is belang rijk om uw eigen omgeving te beschermen en te optimaliseren. Vaak moet u de huidige status van het netwerk weten, die verbinding maakt, waar ze verbinding mee maken, welke poorten open zijn op het Internet, het verwachte netwerk gedrag, onregelmatig netwerk gedrag en onverwachte toename van het verkeer.

Cloud netwerken wijken af van on-premises bedrijfs netwerken, waarbij u over netwerk stromen of gelijkwaardige protocol routers en-switches beschikt die de mogelijkheid bieden om IP-netwerk verkeer te verzamelen wanneer het binnenkomt of afsluit van een netwerk interface. Door verkeers stroom gegevens te analyseren, kunt u een analyse van netwerk verkeer en een volume maken.

Virtuele Azure-netwerken hebben NSG-stroom logboeken, die u informatie geven over binnenkomend en uitgaand IP-verkeer via een netwerk beveiligings groep die is gekoppeld aan afzonderlijke netwerk interfaces, Vm's of subnetten. Door het analyseren van RAW NSG-stroom logboeken en het invoegen van intelligentie van beveiliging, topologie en geografie, kan Traffic Analytics u inzicht geven in de verkeers stroom in uw omgeving. Traffic Analytics biedt informatie zoals de meeste communicerende hosts, de meeste communicerende toepassings protocollen, de meeste ondergeschikte host-paren, toegestaan/geblokkeerd verkeer, binnenkomend/uitgaand verkeer, open Internet poorten, de meeste blokkerings regels, verkeers distributie per Azure Data Center, virtueel netwerk, subnetten of Rogue netwerken.

## <a name="key-components"></a>Belangrijkste onderdelen

- **Netwerk beveiligings groep (NSG)**: bevat een lijst met beveiligings regels waarmee netwerk verkeer wordt toegestaan of geweigerd voor resources die zijn verbonden met een Azure-Virtual Network. NSG's kunnen worden gekoppeld aan subnetten, afzonderlijke virtuele machines (klassiek) of afzonderlijke netwerkinterfaces (NIC) die zijn gekoppeld aan VM’s (Resource Manager). Zie [overzicht van netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)voor meer informatie.
- **Stroom logboeken voor netwerk beveiligings groepen (NSG)**: Hiermee kunt u informatie weer geven over binnenkomend en IP-verkeer met behulp van een netwerk beveiligings groep. NSG-stroom logboeken worden geschreven in JSON-indeling en uitgaande en inkomende stromen per regel weer gegeven, de NIC waarop de stroom van toepassing is, vijf tuple informatie over de stroom (bron/doel-IP-adres, bron/doel poort en Protocol), en als het verkeer is toegestaan of geweigerd. Zie [NSG flow logs](network-watcher-nsg-flow-logging-overview.md)(Engelstalig) voor meer informatie over NSG-stroom Logboeken.
- **Log Analytics**: een Azure-service waarmee bewakings gegevens worden verzameld en de gegevens worden opgeslagen in een centrale opslag plaats. Deze gegevens kunnen gebeurtenissen, prestatie gegevens of aangepaste gegevens bevatten die via de Azure API worden geleverd. Na verzameling zijn de gegevens beschikbaar voor waarschuwingen, analyse en export. Het bewaken van toepassingen zoals netwerk prestatie meter en verkeers analyse zijn gebouwd op basis van Azure Monitor-Logboeken als basis. Zie [Azure monitor-logboeken](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)voor meer informatie.
- **Log Analytics-werk ruimte**: een exemplaar van Azure monitor logboeken, waarbij de gegevens die betrekking hebben op een Azure-account, worden opgeslagen. Zie [een log Analytics-werk ruimte maken](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)voor meer informatie over log Analytics-werk ruimten.
- **Network Watcher**: een regionale service waarmee u voor waarden kunt bewaken en diagnosticeren op het niveau van een netwerk scenario in Azure. U kunt NSG-stroom Logboeken in-en uitschakelen met Network Watcher. Zie [Network Watcher](network-watcher-monitoring-overview.md)voor meer informatie.

## <a name="how-traffic-analytics-works"></a>Hoe Traffic Analytics werkt

Met Traffic Analytics worden de onbewerkte NSG-stroom logboeken onderzocht en worden gereduceerde logboeken vastgelegd door het samen voegen van algemene stromen onder hetzelfde bron-IP-adres, doel-IP-adres, doel poort en protocol. Bijvoorbeeld: host 1 (IP-adres: 10.10.10.10) die communiceert met host 2 (IP-adres: 10.10.20.10), 100 keer over een periode van 1 uur met behulp van poort (bijvoorbeeld 80) en protocol (bijvoorbeeld http). Het gereduceerde logboek heeft één vermelding, die host 1 & host 2 100 keer heeft gecommuniceerd gedurende een periode van 1 uur met behulp van poort *80* en protocol *http*, in plaats van 100 vermeldingen. Gereduceerde logboeken zijn verbeterd met informatie over geografie, beveiliging en topologie en worden vervolgens opgeslagen in een Log Analytics-werk ruimte. In de volgende afbeelding ziet u de gegevens stroom:

![Gegevens stroom voor de verwerking van NSG-stroom logboeken](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Ondersteunde regio's: NSG 

U kunt Traffic Analytics voor Nsg's gebruiken in een van de volgende ondersteunde regio's:
:::row:::
   :::column span="":::
      Australië - centraal  
      Australië - oost  
      Australië - zuidoost  
      Brazilië - zuid  
      Canada - midden  
      Canada - oost  
      India - centraal  
      Central US  
      China - oost 2  
      China - noord 2  
      Azië - oost       
   :::column-end:::
   :::column span="":::
      VS - oost  
      VS - oost 2  
      VS-Oost 2 EUAP  
      Frankrijk - centraal  
      Duitsland - west-centraal  
      Japan - oost  
      Japan - west  
      Korea - centraal  
      Korea - zuid  
      VS - noord-centraal  
      Europa - noord    
   :::column-end:::
   :::column span="":::
      Zuid-Afrika - noord  
      VS - zuid-centraal  
      India - zuid  
      Azië - zuidoost  
      Zwitserland - noord  
      Zwitserland - west  
      VAE - noord  
      Verenigd Koninkrijk Zuid  
      Verenigd Koninkrijk West   
      USGov Arizona  
      USGov Texas
   :::column-end:::
   :::column span="":::
      USGov Virginia  
      USNat-Oost  
      USNat-West  
      USSec-Oost  
      USSec-West  
      VS - west-centraal  
      Europa -west  
      VS - west  
      VS - west 2  
   :::column-end:::
:::row-end:::

## <a name="supported-regions-log-analytics-workspaces"></a>Ondersteunde regio's: Log Analytics werk ruimten

De Log Analytics-werk ruimte moet in de volgende regio's bestaan:
:::row:::
   :::column span="":::
      Australië - centraal  
      Australië - oost  
      Australië - zuidoost  
      Brazilië - zuid  
      Canada - midden  
      India - centraal  
      Central US  
      China - oost 2  
      Azië - oost  
      VS - oost  
   :::column-end:::
   :::column span="":::
      VS - oost 2  
      VS-Oost 2 EUAP  
      Frankrijk-centraal Japan-Oost  
      Korea - centraal  
      VS - noord-centraal  
      Europa - noord  
      Zuid-Afrika - noord  
      VS - zuid-centraal  
   :::column-end:::
   :::column span="":::
      Azië - zuidoost  
      Zwitserland - noord  
      Zwitserland - west  
      UAE - centraal  
      VAE - noord  
      Verenigd Koninkrijk Zuid  
      Verenigd Koninkrijk West   
      USGov Arizona  
      USGov Virginia  
      USNat-Oost   
   :::column-end:::
   :::column span="":::
      USNat-West   
      USSec-Oost  
      USSec-West  
      VS - west-centraal  
      Europa -west  
      VS - west  
      VS - west 2  
   :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Vereisten

### <a name="user-access-requirements"></a>Toegangs vereisten voor gebruikers

Uw account moet lid zijn van een van de volgende [ingebouwde Azure-rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Implementatiemodel   | Rol                   |
|---------          |---------               |
|Resource Manager   | Eigenaar                  |
|                   | Inzender            |
|                   | Lezer                 |
|                   | Inzender voor netwerken    |

Als uw account niet is toegewezen aan een van de ingebouwde rollen, moet dit worden toegewezen aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) waaraan de volgende acties zijn toegewezen op het abonnements niveau:

- "Micro soft. Network/applicationGateways/lezen"
- "Micro soft. netwerk/verbindingen/lezen"
- "Micro soft. Network/loadBalancers/lezen"
- "Micro soft. Network/localNetworkGateways/lezen"
- "Micro soft. Network/networkInterfaces/lezen"
- "Micro soft. Network/networkSecurityGroups/lezen"
- "Micro soft. Network/publicIPAddresses/lezen"
- "Micro soft. Network/routeTables/lezen"
- "Micro soft. Network/virtualNetworkGateways/lezen"
- "Micro soft. Network/virtualNetworks/lezen"
- "Micro soft. Network/expressRouteCircuits/lezen"

Zie [Veelgestelde vragen over Traffic Analytics](traffic-analytics-faq.md)voor informatie over het controleren van toegangs machtigingen voor gebruikers.

### <a name="enable-network-watcher"></a>Network Watcher inschakelen

Als u verkeer wilt analyseren, moet u een bestaande netwerk-Watcher hebben of [een netwerk-Watcher inschakelen](network-watcher-create.md) in elke regio waarvoor u nsg's hebt waarvoor u verkeer wilt analyseren. Traffic Analytics kan worden ingeschakeld voor Nsg's die worden gehost in een van de [ondersteunde regio's](#supported-regions-nsg).

### <a name="select-a-network-security-group"></a>Een netwerk beveiligings groep selecteren

Voordat u logboek registratie van NSG inschakelt, moet u een netwerk beveiligings groep hebben voor het vastleggen van stromen voor. Als u geen netwerk beveiligings groep hebt, raadpleegt u [een netwerk beveiligings groep maken](../virtual-network/manage-network-security-group.md#create-a-network-security-group) om er een te maken.

Ga in Azure Portal naar **Network Watcher** en selecteer vervolgens NSG- **stroom logboeken**. Selecteer de netwerk beveiligings groep waarvoor u een NSG-stroom logboek wilt inschakelen, zoals wordt weer gegeven in de volgende afbeelding:

![Selectie van Nsg's waarvoor NSG-stroom logboek moet worden aangevraagd](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Als u Traffic Analytics wilt inschakelen voor een NSG die wordt gehost in een andere regio dan de [ondersteunde regio's](#supported-regions-nsg), ontvangt u de fout melding "niet gevonden".

## <a name="enable-flow-log-settings"></a>Instellingen voor stroom logboek inschakelen

Voordat u de instellingen voor het stroom logboek inschakelt, moet u de volgende taken uitvoeren:

Registreer de Azure Insights-provider als deze nog niet is geregistreerd voor uw abonnement:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Als u nog geen Azure Storage account hebt om NSG-stroom logboeken op te slaan in, moet u een opslag account maken. U kunt een opslag account maken met behulp van de volgende opdracht. Voordat u de opdracht uitvoert, vervangt `<replace-with-your-unique-storage-account-name>` u door een naam die uniek is voor alle Azure-locaties, tussen 3-24 tekens lang, met alleen cijfers en kleine letters. U kunt ook de naam van de resource groep wijzigen, indien nodig.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selecteer de volgende opties, zoals wordt weer gegeven in de afbeelding:

1. Selecteren *voor* **status**
2. Selecteer *versie 2* voor **flow logboeken versie**. Versie 2 bevat statistische gegevens over stroomsessies (bytes en pakketten)
3. Selecteer een bestaand opslag account om de stroom logboeken op te slaan in. Zorg ervoor dat op uw opslag ' Data Lake Storage Gen2 hiërarchische naam ruimte is ingeschakeld ' is ingesteld op waar.
4. Stel de **Bewaar periode** in op het aantal dagen waarvoor u gegevens wilt opslaan. Als u de gegevens permanent wilt opslaan, stelt u de waarde in op *0*. U hebt Azure Storage kosten voor het opslag account. 
5. Selecteer *aan* voor **Traffic Analytics status**.
6. Selecteer een verwerkings interval. Op basis van uw keuze worden de stroom logboeken verzameld van het opslag account en verwerkt door Traffic Analytics. U kunt het verwerkings interval van elke 1 uur of elke 10 minuten kiezen. 
7. Selecteer een bestaande Log Analytics (OMS)-werk ruimte of selecteer **nieuwe werk ruimte maken** om een nieuwe te maken. Een Log Analytics-werk ruimte wordt gebruikt door Traffic Analytics om de geaggregeerde en geïndexeerde gegevens op te slaan die vervolgens worden gebruikt voor het genereren van de analyse. Als u een bestaande werk ruimte selecteert, moet deze bestaan in een van de [ondersteunde regio's](#supported-regions-log-analytics-workspaces) en zijn bijgewerkt naar de nieuwe query taal. Als u een bestaande werk ruimte niet wilt upgraden of als u geen werk ruimte in een ondersteunde regio hebt, maakt u een nieuwe. Zie [Azure log Analytics upgrade uitvoeren naar nieuwe zoek opdracht in Logboeken](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)voor meer informatie over query talen.

> [!NOTE]
>De log Analytics-werk ruimte die als host fungeert voor de Traffic Analytics-oplossing en de Nsg's hoeven zich niet in dezelfde regio te bevinden. U kunt bijvoorbeeld Traffic Analytics hebben in een werk ruimte in de Europa-west regio, terwijl u mogelijk Nsg's hebt in VS-Oost en VS-West. Meerdere Nsg's kunnen in dezelfde werk ruimte worden geconfigureerd.

8. Selecteer **Opslaan**.

    ![Selectie van opslag account, Log Analytics werk ruimte en Traffic Analytics-activering](./media/traffic-analytics/ta-customprocessinginterval.png)

Herhaal de vorige stappen voor andere Nsg's waarvoor u Traffic Analytics wilt inschakelen voor. Gegevens uit stroom logboeken worden verzonden naar de werk ruimte, dus zorg ervoor dat de lokale wetten en voor Schriften in uw land/regio gegevens opslag toestaan in de regio waar de werk ruimte zich bevindt. Als u verschillende verwerkings intervallen voor verschillende Nsg's hebt ingesteld, worden de gegevens op verschillende tijdstippen verzameld. Bijvoorbeeld: u kunt ervoor kiezen om het verwerkings interval van 10 minuten voor kritieke VNETs en 1 uur voor niet-kritieke VNETs in te scha kelen.

U kunt ook Traffic Analytics configureren met de Power shell [-cmdlet Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) in azure PowerShell. Voer uit `Get-Module -ListAvailable Az` om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Traffic Analytics weer geven

Zoek **Network Watcher** in de zoek balk van de portal om Traffic Analytics te bekijken. **Traffic Analytics** Klik in het menu links in de Network Watcher om Traffic Analytics en de mogelijkheden ervan te verkennen. 

![Toegang tot het dash board van Traffic Analytics](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Het kan tot 30 minuten duren voordat het dash board de eerste keer wordt weer gegeven, omdat Traffic Analytics eerst voldoende gegevens moet verzamelen om zinvolle inzichten te verkrijgen, voordat deze rapporten kunnen genereren.

## <a name="usage-scenarios"></a>Gebruiksscenario's

Enkele van de inzichten die u mogelijk wilt krijgen nadat Traffic Analytics volledig is geconfigureerd, zijn als volgt:

### <a name="find-traffic-hotspots"></a>Verkeer HOTS pots zoeken

**Zoeken**

- Welke hosts, subnetten en virtuele netwerken worden het meeste verkeer verzonden of ontvangen, waardoor het maximale schadelijke verkeer wordt gepasseerd en significante stromen worden geblokkeerd?
    - Controleer de vergelijkende grafiek voor de host, het subnet en het virtuele netwerk. Als u wilt weten welke hosts, subnetten en virtuele netwerken het meeste verkeer verzenden of ontvangen, kan u helpen bij het identificeren van de hosts die het meeste verkeer verwerken en of de distributie van verkeer goed wordt uitgevoerd.
    - U kunt evalueren of de hoeveelheid verkeer geschikt is voor een host. Is het volume van het normale verkeer of gaat het verder onderzoek?
- Hoeveel inkomend/uitgaand verkeer is er?
    -   Verwacht de host dat er meer inkomend verkeer wordt ontvangen dan uitgaande of vice versa?
- Statistieken van het geblokkeerde verkeer.
    - Waarom blokkeert een host een aanzienlijk volume aan goed aardig verkeer? Dit gedrag vereist verder onderzoek en waarschijnlijk optimalisatie van de configuratie
- Statistieken van schadelijk toegestaan/geblokkeerd verkeer
  - Waarom ontvangt een host kwaad aardig verkeer en waarom zijn stromen van schadelijke bronnen toegestaan? Dit gedrag vereist verder onderzoek en waarschijnlijk optimalisatie van de configuratie.

    Selecteer **alles weer geven** onder **host**, zoals wordt weer gegeven in de volgende afbeelding:

    ![Dash board show host met de meeste verkeers Details](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- De volgende afbeelding toont tijd trends voor de vijf meest voorkomende hosts en de gegevens met betrekking tot de stroom (toegestaan: inkomend/uitgaand en geweigerd-inkomende/uitgaande stromen) voor een host:

    ![Bovenste vijf meest pratende hostprioriteit](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Zoeken**

- Wat zijn de meeste conversatie-paren?
    - Verwacht gedrag als een front-end-of back-end-communicatie of onregelmatige werking, zoals een back-end-Internet verkeer.
- Statistieken van toegestaan/geblokkeerd verkeer
    - Waarom een host een aanzienlijk verkeers volume toestaat of blokkeert
- Meest gebruikte toepassings Protocol tussen de meeste ondergeschikte hostgroepen:
    - Zijn deze toepassingen toegestaan op dit netwerk?
    - Zijn de toepassingen correct geconfigureerd? Gebruiken ze het juiste protocol voor communicatie? Selecteer **alles weer** geven onder **frequente conversatie**, zoals in de volgende afbeelding wordt weer gegeven:

        ![Dash board met het meeste frequente gesprek](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- De volgende afbeelding toont tijd trends voor de vijf beste conversaties en de gegevens over de stroom, zoals toegestane en geweigerde binnenkomende en uitgaande stromen voor een gespreks paar:

    ![Bovenste vijf intensieve en trend van de conversatie](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Zoeken**

- Welk toepassings protocol wordt het meest gebruikt in uw omgeving en welke host-paren gebruiken het toepassings protocol het meest?
    - Zijn deze toepassingen toegestaan op dit netwerk?
    - Zijn de toepassingen correct geconfigureerd? Gebruiken ze het juiste protocol voor communicatie? Verwacht gedrag is algemene poorten, zoals 80 en 443. Voor standaard communicatie, als er ongebruikelijke poorten worden weer gegeven, is het mogelijk dat er een configuratie wijziging is vereist. Selecteer **alles weer geven** onder **toepassings poort** in de volgende afbeelding:

        ![Dash board met meest voorkomende toepassings protocollen](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- De volgende afbeeldingen tonen tijd trends voor de vijf N7-protocollen en de gegevens met betrekking tot de stroom (bijvoorbeeld toegestane en geweigerde stromen) voor een N7-Protocol:

    ![Bovenste vijf laag 7-protocollen Details en trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Details van de stroom voor toepassings protocol in zoeken in Logboeken](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Zoeken**

- Trends in capaciteits gebruik van een VPN-gateway in uw omgeving.
    - Met elke VPN-SKU kunt u een bepaalde hoeveelheid band breedte gebruiken. Zijn de VPN-gateways onderbezet?
    - Bereiken uw gateways capaciteit? Moet u upgraden naar de volgende hogere SKU?
- Wat zijn de meeste conversatie-hosts, via welke VPN-gateway over welke poort?
    - Is dit patroon normaal? Selecteer **alles weer geven** onder **VPN-gateway**, zoals wordt weer gegeven in de volgende afbeelding:

        ![Dash board met de eerste actieve VPN-verbindingen](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- De volgende afbeelding toont tijd trends voor het capaciteits gebruik van een Azure-VPN Gateway en de gegevens over de stroom (zoals toegestane stromen en poorten):

    ![Trend en stroom gegevens van VPN-gateway gebruik](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Verkeer distributie door geografie visualiseren

**Zoeken**

- Distributie van verkeer per Data Center, zoals de belangrijkste bronnen van verkeer naar een Data Center, Top Rogue-netwerken die met het Data Center en de meest voorkomende toepassings protocollen bespreken.
  - Als u meer belasting in een Data Center ziet, kunt u een efficiënte distributie van verkeer plannen.
  - Als rogue-netwerken in het Data Center worden gepraat, corrigeert u de NSG-regels om ze te blok keren.

    Selecteer **kaart weer geven** onder **uw omgeving**, zoals wordt weer gegeven in de volgende afbeelding:

    ![Distributie dashboard overzicht verkeer](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- De geo-map toont het bovenste lint voor de selectie van para meters, zoals Data Centers (geïmplementeerd/geen implementatie/actief/inactief/Traffic Analytics ingeschakeld/Traffic Analytics niet ingeschakeld) en landen/regio's die het goed aardige/schadelijke verkeer naar de actieve implementatie bijdragen:

    ![Weer gave geo-map met show Active Deployment](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- De geo-map toont de distributie van verkeer naar een Data Center uit landen/regio's en continenten die met de IT-afdeling in blauw (goed aardig verkeer) en rood (schadelijk verkeer) gekleurde lijnen kunnen communiceren:

    ![Geokaart weergave met uitstaand verkeer distribueren naar landen/regio's en continenten](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Details van de stroom voor verkeer distributie in zoeken in Logboeken](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Verdeling van verkeer via virtuele netwerken visualiseren

**Zoeken**

- Distributie van verkeer per virtueel netwerk, topologie, de belangrijkste bronnen van verkeer naar het virtuele netwerk, Top Rogue-netwerken die met het virtuele netwerk zijn gepraat en de meest voorkomende toepassings protocollen.
  - Weten welk virtueel netwerk praat met het virtuele netwerk. Als de conversatie niet wordt verwacht, kan deze worden gecorrigeerd.
  - Als rogue-netwerken worden gepraat met een virtueel netwerk, kunt u NSG-regels voor het blok keren van de Rogue-netwerken corrigeren.
 
    Selecteer **VNets weer geven** onder **uw omgeving**, zoals wordt weer gegeven in de volgende afbeelding:

    ![Dash board overzicht virtuele netwerk distributie](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- De Virtual Network topologie toont het bovenste lint voor het selecteren van para meters, zoals een virtueel netwerk (tussen virtuele netwerk verbindingen/actief/inactief), externe verbindingen, actieve stromen en schadelijke stromen van het virtuele netwerk.
- U kunt de Virtual Network topologie filteren op basis van abonnementen, werk ruimten, resource groepen en tijds interval. Aanvullende filters die u inzicht geven in de stroom zijn: stroom type (InterVNet, IntraVNET, enzovoort), stroom richting (inkomend, uitgaand), stroom status (toegestaan, geblokkeerd), VNETs (aangeduid en verbonden), verbindings type (peering of gateway-P2S en S2S), en NSG. Gebruik deze filters om te focussen op VNets die u gedetailleerd wilt bekijken.
- De Virtual Network topologie toont de distributie van verkeer naar een virtueel netwerk met betrekking tot stromen (toegestaan/geblokkeerd/inkomend/uitgaand/onschadelijk/kwaad aardig), toepassings protocol en netwerk beveiligings groepen, bijvoorbeeld:

    ![Topologie van virtueel netwerk overzicht van verkeer distributie en stroom Details](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologie van virtueel netwerk met etalage op het hoogste niveau en meer filters](./media/traffic-analytics/virtual-network-filters.png)

    ![Details van de stroom voor distributie van het virtuele netwerk verkeer in zoeken in Logboeken](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Zoeken**

- Distributie van verkeer per subnet, topologie, belangrijkste bronnen van verkeer naar het subnet, Top Rogue-netwerken die met het subnet worden gepraat en de meest voorkomende toepassings protocollen.
    - Weten welk subnet praat met welk subnet. Als er onverwachte conversaties worden weer geven, kunt u uw configuratie corrigeren.
    - Als rogue-netwerken worden gepraat met een subnet, kunt u dit corrigeren door NSG-regels te configureren om de Rogue-netwerken te blok keren.
- De subnetten topologie toont het bovenste lint voor de selectie van para meters, zoals actief/inactief subnet, externe verbindingen, actieve stromen en schadelijke stromen van het subnet.
- In de topologie van het subnet ziet u de distributie van verkeer naar een virtueel netwerk met betrekking tot stromen (toegestaan/geblokkeerd/inkomend/uitgaand/onschadelijk/kwaad aardig), toepassings protocol en Nsg's, bijvoorbeeld:

    ![Subnet topologie overzicht van verkeer distributie een subnet van een virtueel netwerk met betrekking tot stromen](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Zoeken**

Distributie van verkeer per toepassings gateway & Load Balancer, topologie, de belangrijkste bronnen van verkeer, Top Rogue-netwerken die met de toepassings gateway & Load Balancer en de meest voorkomende toepassings protocollen. 
    
 - Weten welk subnet praat met de toepassings gateway of Load Balancer. Als u onverwachte conversaties bekijkt, kunt u uw configuratie corrigeren.
 - Als rogue-netwerken worden gepraat met een toepassings gateway of Load Balancer, kunt u deze corrigeren door NSG-regels te configureren om de Rogue-netwerken te blok keren. 

    ![Scherm afbeelding toont een subnet topologie met verkeers distributie naar een Application Gateway-subnet met betrekking tot stromen.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Poorten en virtuele machines weer geven die verkeer ontvangen van Internet

**Zoeken**

- Welke open poorten worden via internet gepraat?
  - Als onverwachte poorten worden gevonden, kunt u de configuratie corrigeren:

    ![Poorten voor het door sturen van het dash board die verkeer ontvangen en verzenden naar Internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Details van Azure-doel poorten en-hosts](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Zoeken**

Hebt u schadelijk verkeer in uw omgeving? Waar bevindt deze zich vandaan? Waar is de oplossing bestemd?

![Details van beschadigd verkeer in Logboeken zoeken](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualiseren van de trends in NSG/NSG-regel treffers

**Zoeken**

- Welke NSG-NSG-regels hebben de meeste treffers in vergelijkende grafiek met de verdeling van stromen?
- Wat zijn de bovenste bron-en doel conversatie paren per NSG/NSG-regel?

    ![Statistieken voor NSG hits in dash board](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- De volgende afbeeldingen tonen tijd trends voor treffers van NSG-regels en informatie over de bron doel stroom voor een netwerk beveiligings groep:

  - Snel detecteren welke Nsg's-en NSG-regels schadelijke stromen passeren en welke de meest voorkomende schadelijke IP-adressen zijn die toegang hebben tot uw cloud omgeving
  - Identificeren welke NSG/NSG-regels aanzienlijk netwerk verkeer toestaan/blok keren
  - Bovenste filters selecteren voor een gedetailleerde inspectie van een NSG-of NSG-regel

    ![Overzicht van tijd trends voor NSG regel treffers en top NSG-regels](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Statistieken voor de top NSG-regels in zoeken in Logboeken](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Zie [Veelgestelde vragen over Traffic Analytics](traffic-analytics-faq.md)voor antwoorden op veelgestelde vragen.

## <a name="next-steps"></a>Volgende stappen

- Zie [NSG-stroom logboeken](network-watcher-nsg-flow-logging-portal.md)inschakelen voor meer informatie over het inschakelen van stroom Logboeken.
- Zie [Traffic Analytics schema](traffic-analytics-schema.md)voor meer informatie over het schema en de verwerkings details van Traffic Analytics.
