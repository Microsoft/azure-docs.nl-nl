---
title: 'Azure ExpressRoute: NPM voor circuits configureren'
description: Configureer de Cloud netwerk bewaking (NPM) voor Azure ExpressRoute-circuits. Dit omvat het controleren van ExpressRoute persoonlijke peering en micro soft-peering.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 837d1f78a9fde72137383e6c22adb8870997dc9b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986036"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Netwerkprestatiemeter configureren voor ExpressRoute

Dit artikel helpt u bij het configureren van een Netwerkprestatiemeter-extensie voor het bewaken van ExpressRoute. Netwerkprestatiemeter (NPM) is een cloudoplossing voor netwerkcontrole die de connectiviteit controleert tussen implementaties in de Azure-cloud en on-premises locaties (filialen, enzovoort). NPM is een onderdeel van Azure Monitor-logboeken. NPM biedt een uitbreiding voor ExpressRoute waarmee u netwerkprestaties kunt controleren via ExpressRoute-circuits die zijn geconfigureerd voor het gebruik van persoonlijke peering of Microsoft-peering. Wanneer u NPM configureert voor ExpressRoute, kunt u netwerkproblemen detecteren om te identificeren en op te lossen. Deze service is ook beschikbaar voor Azure Government-Cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

U kunt:

* Verlies en latentie bewaken in verschillende VNets en waarschuwingen instellen

* Alle paden controleren (met inbegrip van redundante paden) op het netwerk

* Problemen oplossen met tijdelijke en punt-in-time-netwerk problemen die moeilijk te repliceren zijn

* Hulp bij het bepalen van een specifiek segment op het netwerk dat verantwoordelijk is voor de verslechterde prestaties

* Door Voer per virtueel netwerk ophalen (als er agents in elk VNet zijn geïnstalleerd)

* De status van het ExpressRoute-systeem van een eerder tijdstip weer geven

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

Bewakings agenten worden op meerdere servers geïnstalleerd, zowel on-premises als in Azure. De agents communiceren met elkaar, maar verzenden geen gegevens, ze verzenden TCP-handshake-pakketten. De communicatie tussen de agents staat Azure toe om de netwerk topologie toe te wijzen en het verkeer kan worden uitgevoerd.

1. Maak een NPM-werk ruimte. Dit is hetzelfde als een Log Analytics-werk ruimte.
2. Software-agents installeren en configureren. (Als u alleen wilt bewaken over micro soft-peering, hoeft u geen software-agents te installeren en te configureren.): 
    * Installeer bewakings agents op de on-premises servers en de virtuele Azure-machines (voor persoonlijke peering).
    * Configureer de instellingen op de servers van de bewakings agent zodat de bewakings agenten kunnen communiceren. (Open firewall poorten, enz.)
3. Configureer NSG-regels (netwerk beveiligings groep) om de bewakings agent op virtuele machines van Azure te laten communiceren met on-premises bewakings agenten.
4. Controle instellen: automatisch detecteren en beheren welke netwerken worden weer gegeven in NPM.

Als u Netwerkprestatiemeter al gebruikt om andere objecten of services te bewaken en u al een werk ruimte hebt in een van de ondersteunde regio's, kunt u stap 1 en stap 2 overs Laan en de configuratie starten met stap 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Stap 1: een werk ruimte maken

Maak een werk ruimte in het abonnement met de VNets-koppeling naar de ExpressRoute-circuit (s).

1. Selecteer in de [Azure Portal](https://portal.azure.com)het abonnement waaraan de VNETs is gekoppeld aan uw ExpressRoute-circuit. Zoek vervolgens in de lijst met Services op de **Marketplace** naar ' Netwerkprestatiemeter '. Klik in het retour venster om de pagina **Netwerkprestatiemeter** te openen.

   >[!NOTE]
   >U kunt een nieuwe werk ruimte maken of een bestaande werk ruimte gebruiken. Als u een bestaande werk ruimte wilt gebruiken, moet u ervoor zorgen dat de werk ruimte is gemigreerd naar de nieuwe query taal. [Meer informatie...](../azure-monitor/log-query/log-query-overview.md)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Klik onder aan de pagina hoofd **Netwerkprestatiemeter** op **maken** om te openen **Netwerkprestatiemeter-pagina nieuwe oplossing maken** . Klik op **log Analytics werk ruimte: Selecteer een werk ruimte** om de pagina werk ruimten te openen. Klik op **+ nieuwe werk ruimte maken** om de pagina werk ruimte te openen.
3. Selecteer op de pagina **log Analytics werk ruimte** de optie **nieuwe maken** en configureer de volgende instellingen:

   * Log Analytics werkruimte: Typ een naam voor uw werk ruimte.
   * Abonnement: als u meerdere abonnementen hebt, kiest u het account dat u wilt koppelen aan de nieuwe werk ruimte.
   * Resource groep: Maak een resource groep of gebruik een bestaande.
   * Locatie: deze locatie wordt gebruikt om de locatie op te geven van het opslag account dat wordt gebruikt voor de verbindings logboeken van de agent.
   * Prijs categorie: Selecteer de prijs categorie.
  
     >[!NOTE]
     >Het ExpressRoute-circuit kan overal ter wereld zijn. Deze hoeft zich niet in dezelfde regio als de werk ruimte te bevinden.
     >
  
     ![werkruimte](./media/how-to-npm/4.png)<br><br>
4. Klik op **OK** om de sjabloon instellingen op te slaan en te implementeren. Zodra de sjabloon is gevalideerd, klikt u op **maken** om de werk ruimte te implementeren.
5. Nadat de werk ruimte is geïmplementeerd, gaat u naar de **NetworkMonitoring-resource (name)** die u hebt gemaakt. Valideer de instellingen en klik vervolgens op **oplossing vereist aanvullende configuratie**.

   ![aanvullende configuratie](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Stap 2: agents installeren en configureren

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2,1: het installatie bestand van de agent downloaden

1. Ga naar het tabblad **algemene instellingen** van de pagina **Netwerkprestatiemeter configuratie** voor uw resource. Klik op de agent die overeenkomt met de processor van uw server in de sectie **log Analytics agents installeren** en down load het installatie bestand.
2. Kopieer vervolgens de **werk ruimte-id** en **primaire sleutel** naar Klad blok.
3. Down load het Power shell-script uit de sectie **log Analytics agents configureren voor bewaking met behulp** van het TCP-protocol. Het Power shell-script helpt u bij het openen van de relevante firewall poort voor de TCP-trans acties.

   ![PowerShell-script](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2,2: Installeer een bewakings agent op elke monitoring server (op elk VNET dat u wilt bewaken)

U wordt aangeraden ten minste twee agents te installeren aan elke kant van de ExpressRoute-verbinding voor redundantie (bijvoorbeeld on-premises, Azure VNETs). De agent moet worden geïnstalleerd op een Windows-Server (2008 SP1 of hoger). Het bewaken van ExpressRoute-circuits met Windows Desktop OS en Linux-besturings systeem wordt niet ondersteund. Gebruik de volgende stappen om agents te installeren:
   
  >[!NOTE]
  >Door SCOM gepushte agents (inclusief [MMA](/previous-versions/system-center/system-center-2012-R2/dn465154(v=sc.12))) kunnen hun locatie mogelijk niet consistent detecteren als ze worden gehost in Azure. U wordt aangeraden deze agents in azure VNETs niet te gebruiken om ExpressRoute te controleren.
  >

1. Voer **Setup** uit om de agent te installeren op elke server die u wilt gebruiken voor het controleren van ExpressRoute. De server die u voor bewaking gebruikt, kan een virtuele machine of een on-premises computer zijn en moet toegang hebben tot internet. U moet ten minste één on-premises agent installeren en één agent op elk netwerk segment dat u wilt bewaken in Azure.
2. Klik op de pagina **Welkom** op **Volgende**.
3. Lees de licentie op de pagina **licentie voorwaarden** en klik vervolgens op **Ik ga akkoord**.
4. Op de pagina **doelmap** wijzigt of behoudt u de standaardmap voor installatie en klikt u vervolgens op **volgende**.
5. Op de pagina **installatie opties voor agent** kunt u ervoor kiezen om de agent te verbinden met Azure monitor Logboeken of Operations Manager. U kunt ook de keuzes leeg laten als u de agent later wilt configureren. Klik op **volgende** nadat u de gewenste selectie (s) hebt gemaakt.

   * Als u verbinding met **Azure log Analytics** wilt maken, plakt u de **werk ruimte-id** en de **werkruimte sleutel** (primaire sleutel) die u in het klad blok hebt gekopieerd in de vorige sectie. Klik op **Volgende**.

     ![ID en sleutel](./media/how-to-npm/8.png)
   * Als u ervoor hebt gekozen om verbinding te maken met **Operations Manager**, typt u op de pagina **configuratie van beheer groep** de naam van de **beheer groep**, de **beheer server** en de poort van de **beheer server**. Klik op **Volgende**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Kies op de pagina **actie-account van agent** ofwel het **lokale systeem** account, ofwel het  **domein of het lokale computer account**. Klik op **Volgende**.

     ![Account](./media/how-to-npm/10.png)
6. Controleer uw keuzes op de pagina **gereed voor installatie** en klik op **installeren**.
7. Klik op de pagina **Configuratie voltooid** op **Voltooien**.
8. Als u klaar bent, wordt micro soft monitoring agent weer gegeven in het configuratie scherm. U kunt uw configuratie daar controleren en controleren of de agent is verbonden met Azure Monitor Logboeken. Als er verbinding is gemaakt, wordt in de agent een bericht weer gegeven: **de micro soft monitoring agent heeft verbinding gemaakt met de Microsoft Operations Management Suite-Service**.

9. Herhaal deze procedure voor elk VNET dat u wilt bewaken.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2,3: proxy-instellingen configureren (optioneel)

Als u een webproxy gebruikt voor toegang tot internet, gebruikt u de volgende stappen om proxy-instellingen te configureren voor de micro soft monitoring agent. Voer de volgende stappen uit voor elke server. Als er veel servers zijn die u moet configureren, is het wellicht eenvoudiger om een script te gebruiken om dit proces te automatiseren. Als dit het geval is, raadpleegt [u proxy-instellingen voor de micro soft monitoring agent configureren met een script](../azure-monitor/platform/agent-windows.md).

Proxy-instellingen voor de micro soft monitoring agent configureren met behulp van het configuratie scherm:

1. Open het **configuratie scherm**.
2. Open **Microsoft Monitoring Agent**.
3. Klik op het tabblad **Proxyinstellingen**.
4. Selecteer **een proxy server gebruiken** en typ de URL en het poort nummer, indien nodig. Als er voor uw proxyserver verificatie is vereist, voert u de gebruikersnaam en het wachtwoord in om de proxyserver te openen.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2,4: agent connectiviteit controleren

U kunt eenvoudig controleren of uw agents communiceren.

1. Open het **configuratie scherm** op een server met de bewakings agent.
2. Open **micro soft Monitoring Agent**.
3. Klik op het tabblad **Azure log Analytics** .
4. In de kolom **status** ziet u dat de agent is verbonden met het Azure monitor Logboeken.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2,5: Open de firewall poorten op de monitoring agent-servers

Als u het TCP-protocol wilt gebruiken, moet u Firewall poorten openen om ervoor te zorgen dat de bewakings agenten kunnen communiceren.

U kunt een Power shell-script uitvoeren om de register sleutels te maken die vereist zijn voor de Netwerkprestatiemeter. Met dit script worden ook de Windows Firewall-regels gemaakt om bewakings agenten toe te staan om TCP-verbindingen met elkaar te maken. De register sleutels die door het script worden gemaakt, geven aan of de logboeken voor fout opsporing en het pad naar het logboek bestand moeten worden geregistreerd. Het definieert ook de TCP-poort van de agent die wordt gebruikt voor communicatie. De waarden voor deze sleutels worden automatisch ingesteld door het script. U moet deze sleutels niet hand matig wijzigen.

Poort 8084 is standaard geopend. U kunt een aangepaste poort gebruiken door de para meter ' poort nummer ' op te geven voor het script. Als u dit wel doet, moet u echter dezelfde poort opgeven voor alle servers waarop u het script uitvoert.

>[!NOTE]
>Met het Power shell-script ' EnableRules ' worden alleen Windows Firewall regels geconfigureerd op de server waarop het script wordt uitgevoerd. Als u een netwerk firewall hebt, moet u ervoor zorgen dat het verkeer dat is bestemd voor de TCP-poort die wordt gebruikt door Netwerkprestatiemeter wordt toegestaan.
>
>

Open een Power shell-venster met beheerders bevoegdheden op de agent servers. Voer het Power shell-script [EnableRules](https://aka.ms/npmpowershellscript) uit (die u eerder hebt gedownload). Gebruik geen para meters.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Stap 3: regels voor netwerk beveiligings groepen configureren

Als u agent servers wilt bewaken die zich in azure bevinden, moet u NSG-regels (netwerk beveiligings groep) configureren om TCP-verkeer toe te staan op een poort die door NPM wordt gebruikt voor synthetische trans acties. De standaard poort is 8084. Hiermee kan een bewakings agent die is geïnstalleerd op een virtuele machine van Azure, communiceren met een on-premises bewakings agent.

Zie [netwerk beveiligings groepen](../virtual-network/tutorial-filter-network-traffic.md)voor meer informatie over NSG.

>[!NOTE]
>Zorg ervoor dat u de agents (zowel de on-premises Server Agent als de Azure Server-Agent) hebt geïnstalleerd en het Power shell-script hebt uitgevoerd voordat u doorgaat met deze stap.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Stap 4: peering-verbindingen detecteren

1. Ga naar de tegel Netwerkprestatiemeter overzicht door naar de pagina **alle resources** te gaan en klik vervolgens op de werk ruimte allowlisted NPM.

   ![NPM-werk ruimte](./media/how-to-npm/npm.png)
2. Klik op de tegel overzicht van **Netwerkprestatiemeter** om het dash board te openen. Het dash board bevat een ExpressRoute-pagina, die laat zien dat ExpressRoute de status niet-geconfigureerd heeft. Klik op **functie-instellingen** om de pagina Netwerkprestatiemeter configuratie te openen.

   ![functie-instellingen](./media/how-to-npm/npm2.png)
3. Ga op de pagina configuratie naar het tabblad ' ExpressRoute peerings ' in het deel venster aan de linkerkant. Klik vervolgens op **nu detecteren**.

   ![via](./media/how-to-npm/13.png)
4. Wanneer de detectie is voltooid, ziet u een lijst met de volgende items:
   * Alle micro soft-peering verbindingen in het ExpressRoute-circuit (en) die zijn gekoppeld aan dit abonnement.
   * Alle particuliere peering-verbindingen die verbinding maken met de VNets die zijn gekoppeld aan dit abonnement.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Stap 5: monitors configureren

In deze sectie configureert u de monitors. Volg de stappen voor het type peering dat u wilt bewaken: **persoonlijke peering** of **micro soft-peering**.

### <a name="private-peering"></a>Persoonlijke peering

Als de detectie is voltooid, ziet u voor persoonlijke peering regels voor unieke **circuit naam** en **VNet-naam**. In eerste instantie zijn deze regels uitgeschakeld.

![regels](./media/how-to-npm/14.png)

1. Schakel het selectie vakje **deze peering bewaken** in.
2. Schakel het selectie vakje **status controle inschakelen voor deze peering in**.
3. Kies de bewakings voorwaarden. U kunt aangepaste drempels instellen om status gebeurtenissen te genereren door drempel waarden te typen. Wanneer de waarde van de voor waarde boven de geselecteerde drempelwaarde komt voor het geselecteerde netwerk/subnetwerk, wordt een status gebeurtenis gegenereerd.
4. Klik op de knop **agenten toevoegen** op de on-PREMISes agents om de on-premises servers toe te voegen van waaruit u de particuliere peering-verbinding wilt bewaken. Zorg ervoor dat u alleen agenten kiest die verbinding hebben met het micro soft service-eind punt dat u hebt opgegeven in de sectie voor stap 2. De on-premises agents moeten het eind punt kunnen bereiken met behulp van de ExpressRoute-verbinding.
5. Sla de instellingen op.
6. Na het inschakelen van de regels en het selecteren van de waarden en agents die u wilt bewaken, is er een wacht tijd van ongeveer 30-60 minuten voordat de waarden worden gevuld en de **ExpressRoute-bewakings** tegels beschikbaar worden.

### <a name="microsoft-peering"></a>Microsoft-peering

Voor micro soft-peering klikt u op de micro soft-peering-verbinding (en) die u wilt bewaken en configureert u de instellingen.

1. Schakel het selectie vakje **deze peering bewaken** in. 
2. Beschrijving U kunt het doel-micro soft service-eind punt wijzigen. NPM kiest standaard een micro soft service-eind punt als doel. NPM bewaakt de connectiviteit van uw on-premises servers naar dit doel eindpunt via ExpressRoute. 
    * Als u dit eind punt wilt wijzigen, klikt u op de koppeling **(bewerken)** onder **doel:** en selecteert u een ander micro soft service target-eind punt in de lijst met url's.
      ![doel bewerken](./media/how-to-npm/edit_target.png)<br>

    * U kunt een aangepaste URL of IP-adres gebruiken. Deze optie is vooral relevant als u micro soft-peering gebruikt om verbinding te maken met Azure PaaS-Services, zoals Azure Storage, SQL-data bases en websites die worden aangeboden op open bare IP-adressen. Hiertoe klikt u op de koppeling **(gebruik in plaats daarvan aangepaste URL of IP-adres)** onder aan de lijst met url's en voert u vervolgens het open bare eind punt in van de Azure PaaS-service die is verbonden via de ExpressRoute micro soft-peering.
    ![aangepaste URL](./media/how-to-npm/custom_url.png)<br>

    * Als u deze optionele instellingen gebruikt, zorg er dan voor dat alleen het micro soft service-eind punt is geselecteerd. Het eind punt moet worden verbonden met ExpressRoute en bereikbaar zijn door de on-premises agents.
3. Schakel het selectie vakje **status controle inschakelen voor deze peering in**.
4. Kies de bewakings voorwaarden. U kunt aangepaste drempels instellen om status gebeurtenissen te genereren door drempel waarden te typen. Wanneer de waarde van de voor waarde boven de geselecteerde drempelwaarde komt voor het geselecteerde netwerk/subnetwerk, wordt een status gebeurtenis gegenereerd.
5. Klik op de knop **agenten toevoegen** op de on-PREMISes agents om de on-premises servers toe te voegen van waaruit u de micro soft peering-verbinding wilt bewaken. Zorg ervoor dat u alleen agenten kiest die verbinding hebben met de micro soft-service-eind punten die u hebt opgegeven in de sectie voor stap 2. De on-premises agents moeten het eind punt kunnen bereiken met behulp van de ExpressRoute-verbinding.
6. Sla de instellingen op.
7. Na het inschakelen van de regels en het selecteren van de waarden en agents die u wilt bewaken, is er een wacht tijd van ongeveer 30-60 minuten voordat de waarden worden gevuld en de **ExpressRoute-bewakings** tegels beschikbaar worden.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Stap 6: bewakings tegels weer geven

Zodra u de tegels voor bewaking ziet, worden uw ExpressRoute-circuits en verbindings bronnen bewaakt door NPM. U kunt klikken op de tegel micro soft-peering om in te zoomen op de status van micro soft-peering-verbindingen.

![bewakings tegels](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Netwerkprestatiemeter pagina

De pagina NPM bevat een pagina voor ExpressRoute waarin een overzicht van de status van ExpressRoute-circuits en peerings wordt weer gegeven.

![Scherm afbeelding toont een dash board met een overzicht van de status van de ExpressRoute-circuits en peerings.](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Lijst met circuits

Als u een lijst met alle bewaakte ExpressRoute-circuits wilt weer geven, klikt u op de tegel **ExpressRoute-circuits** . U kunt een circuit selecteren en de bijbehorende status, trend grafieken voor pakket verlies, bandbreedte gebruik en latentie weer geven. De grafieken zijn interactief. U kunt een aangepast tijd venster selecteren voor het uitzetten van de grafieken. U kunt de muis aanwijzer over een gebied in het diagram slepen om in te zoomen en de nauw keurige gegevens punten weer te geven.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Trend van verlies, latentie en door Voer

De grafieken band breedte, latentie en verlies zijn interactief. U kunt inzoomen op elke sectie van deze grafieken, met behulp van muis besturings elementen. U kunt de band breedte, latentie en gegevens verlies voor andere intervallen ook zien door te klikken op **datum en tijd**, onder de knop acties in de linkerbovenhoek.

![stijgende](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Lijst met peerings

Als u een lijst wilt weer geven met alle verbindingen met virtuele netwerken via persoonlijke peering, klikt u op de tegel **privé-peerings** op het dash board. Hier kunt u een virtuele netwerk verbinding selecteren en de bijbehorende status, trend grafieken voor pakket verlies, bandbreedte gebruik en latentie weer geven.

![circuit lijst](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Knoop punten weer geven

Klik op **knooppunt koppelingen weer geven** om een lijst weer te geven met alle koppelingen tussen de on-premises knoop punten en Azure Vm's/micro soft-service-eind punten voor de gekozen ExpressRoute peering-verbinding. U kunt de status van elke koppeling bekijken, evenals de trend van het verlies en de latentie die eraan is gekoppeld.

![knoop punten weer geven](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Circuit topologie

Als u de circuit topologie wilt weer geven, klikt u op de tegel **topologie** . Hiermee gaat u naar de weer gave topologie van het geselecteerde circuit of peering. Het topologie diagram geeft de latentie voor elk segment in het netwerk. Elke Layer 3-hop wordt vertegenwoordigd door een knoop punt van het diagram. Als u op een hop klikt, wordt meer informatie over de hop.

U kunt het zichtbaarheids niveau verhogen om on-premises hops op te nemen door de schuif regelaar onder **filters** te verplaatsen. Door de schuif regelaar naar links of rechts te verplaatsen, wordt het aantal hops in de topologie grafiek verg root of verkleind. De latentie voor elk segment is zichtbaar, zodat er een snellere isolatie van segmenten van hoge latentie mogelijk is in uw netwerk.

![filters](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Gedetailleerde topologie weergave van een circuit

In deze weer gave worden VNet-verbindingen weer gegeven.
![gedetailleerde topologie](./media/how-to-npm/17.png)