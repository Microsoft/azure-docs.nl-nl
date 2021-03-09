---
title: Computers verbinden met behulp van de Log Analytics-gateway | Microsoft Docs
description: Verbind uw apparaten en Operations Manager bewaakte computers met behulp van de Log Analytics-gateway om gegevens te verzenden naar de Azure Automation-en Log Analytics-service wanneer ze geen toegang tot internet hebben.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: bae48dc78eb6973e5bce4d535091bc330c4c897f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509027"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Computers zonder Internet toegang verbinden met behulp van de Log Analytics-gateway in Azure Monitor

>[!NOTE]
>Als Microsoft Operations Management Suite (OMS) overgangen naar Microsoft Azure monitor, wordt de terminologie gewijzigd. Dit artikel heeft betrekking op de OMS-gateway als de Azure Log Analytics-gateway. 
>

In dit artikel wordt beschreven hoe u communicatie met Azure Automation en Azure Monitor kunt configureren met behulp van de Log Analytics gateway wanneer computers die rechtstreeks zijn verbonden of die worden bewaakt door Operations Manager geen Internet toegang hebben. 

De Log Analytics-gateway is een HTTP-doorstuur proxy die ondersteuning biedt voor HTTP-tunnels met behulp van de HTTP-opdracht verbinding. Deze Gateway verzendt gegevens naar Azure Automation en een Log Analytics-werk ruimte in Azure Monitor namens de computers die niet rechtstreeks verbinding kunnen maken met internet. 

De Log Analytics gateway ondersteunt:

* Het rapporteren van Maxi maal dezelfde Log Analytics werk ruimten die zijn geconfigureerd op elke afzonderlijke agent en die zijn geconfigureerd met Azure Automation Hybrid Runbook Workers.  
* Windows-computers waarop micro soft Monitoring Agent rechtstreeks is verbonden met een Log Analytics-werk ruimte in Azure Monitor.
* Linux-computers waarop een Log Analytics-agent voor Linux rechtstreeks is verbonden met een Log Analytics-werk ruimte in Azure Monitor.  
* System Center Operations Manager 2012 SP1 met UR7, Operations Manager 2012 R2 met UR3, of een beheer groep in Operations Manager 2016 of hoger die is geïntegreerd met Log Analytics.  

In sommige IT-beveiligings beleidsregels is Internet verbinding voor netwerk computers niet toegestaan. Deze niet-verbonden computers kunnen bijvoorbeeld POS-apparaten (Point of Sale) zijn of servers die IT-Services ondersteunen. Als u deze apparaten wilt verbinden met Azure Automation of een Log Analytics werk ruimte, zodat u ze kunt beheren en controleren, moet u ze configureren om rechtstreeks te communiceren met de Log Analytics gateway. De Log Analytics-gateway kan configuratie-informatie ontvangen en gegevens namens u door sturen. Als de computers zijn geconfigureerd met de Log Analytics-agent om rechtstreeks verbinding te maken met een Log Analytics-werk ruimte, communiceren de computers in plaats daarvan met de Log Analytics gateway.  

De Log Analytics-gateway brengt gegevens rechtstreeks over van de agents naar de service. De gegevens in de overdracht worden niet geanalyseerd en de gateway slaat geen gegevens op in de cache wanneer de verbinding met de service wordt verbroken. Wanneer de gateway niet kan communiceren met de service, blijft de agent actief en worden de verzamelde gegevens op de schijf van de bewaakte computer bewaard. Wanneer de verbinding wordt hersteld, verzendt de agent de gegevens in de cache die worden verzameld naar Azure Monitor.

Wanneer een Operations Manager-beheer groep is geïntegreerd met Log Analytics, kunnen de beheerser vers worden geconfigureerd om verbinding te maken met de gateway van Log Analytics om configuratie-informatie te ontvangen en verzamelde gegevens te verzenden, afhankelijk van de oplossing die u hebt ingeschakeld.  Operations Manager-agents verzenden enkele gegevens naar de-beheer server. Agents kunnen bijvoorbeeld Operations Manager-waarschuwingen, gegevens over de configuratie-evaluatie, gegevens over de exemplaar ruimte en capaciteits gegevens verzenden. Andere gegevens met een hoog volume, zoals Internet Information Services (IIS)-logboeken, prestatie gegevens en beveiligings gebeurtenissen, worden rechtstreeks naar de Log Analytics gateway verzonden. 

Als er een of meer Operations Manager Gateway servers zijn geïmplementeerd om niet-vertrouwde systemen in een perimeter netwerk of een geïsoleerd netwerk te bewaken, kunnen die servers niet communiceren met een Log Analytics gateway.  Operations Manager Gateway servers kunnen alleen rapporteren aan een beheer server.  Wanneer een Operations Manager-beheer groep is geconfigureerd voor communicatie met de Log Analytics gateway, worden de configuratie gegevens van de proxy automatisch gedistribueerd naar elke door agents beheerde computer die is geconfigureerd voor het verzamelen van logboek gegevens voor Azure Monitor, zelfs als de instelling leeg is.

Gebruik Network Load Balancing (NLB) voor het omleiden en distribueren van verkeer tussen meerdere Gateway servers om hoge Beschik baarheid te bieden voor rechtstreeks verbonden of Operations Management-groepen die communiceren met een Log Analytics-werk ruimte via de gateway. Op die manier wordt het verkeer omgeleid naar een ander beschikbaar knoop punt als één gateway server uitvalt.  

Op de computer waarop de Log Analytics-gateway wordt uitgevoerd, is de Log Analytics Windows-agent vereist voor het identificeren van de service-eind punten waarmee de gateway moet communiceren. De agent moet ook de gateway omleiden om te rapporteren aan dezelfde werk ruimten als de agents of Operations Manager beheer groep achter de gateway zijn geconfigureerd met. Met deze configuratie kunnen de gateway en de agent communiceren met hun toegewezen werk ruimte.

Een gateway kan een multihomed tot vier werk ruimten zijn. Dit is het totale aantal werk ruimten dat een Windows-agent ondersteunt.  

Elke agent moet een netwerk verbinding met de gateway hebben, zodat agents automatisch gegevens kunnen overdragen van en naar de gateway. Vermijd de installatie van de gateway op een domein controller. Linux-computers die zich achter een gateway server bevinden, kunnen de installatie methode voor [wrapper-scripts](../agents/agent-linux.md#install-the-agent-using-wrapper-script) niet gebruiken om de log Analytics-agent voor Linux te installeren. De agent moet hand matig worden gedownload, naar de computer worden gekopieerd en hand matig worden geïnstalleerd, omdat de gateway alleen ondersteuning biedt voor communicatie met de Azure-Services die eerder zijn beschreven.

In het volgende diagram ziet u gegevens die vanuit direct agents, via de gateway, worden gestroomd naar Azure Automation en Log Analytics. De configuratie van de agent proxy moet overeenkomen met de poort waarop de Log Analytics-gateway is geconfigureerd.  

![Diagram van directe agent communicatie met Services](./media/gateway/oms-omsgateway-agentdirectconnect.png)

In het volgende diagram ziet u de gegevens stroom van een Operations Manager-beheer groep naar Log Analytics.   

![Diagram van Operations Manager communicatie met Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Uw systeem instellen

Computers die zijn aangewezen voor het uitvoeren van de Log Analytics gateway, moeten de volgende configuratie hebben:

* Windows 10, Windows 8,1 of Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 of Windows Server-2008
* Microsoft .NET Framework 4.5
* Minstens een 4-core-processor en 8 GB aan geheugen 
* Een [log Analytics-agent voor Windows](../agents/agent-windows.md) die is geconfigureerd om te rapporteren aan dezelfde werk ruimte als de agents die communiceren via de gateway

### <a name="language-availability"></a>Beschik baarheid taal

De Log Analytics gateway is beschikbaar in de volgende talen:

- Chinees (Vereenvoudigd)
- Chinees (Traditioneel)
- Tsjechisch
- Nederlands
- Engels
- Frans
- Duits
- Hongaars
- Italiaans
- Japans
- Koreaans
- Pools
- Portugees (Brazilië)
- Portugees (Portugal)
- Russisch
- Spaans (internationaal)

### <a name="supported-encryption-protocols"></a>Ondersteunde versleutelings protocollen

De Log Analytics-gateway ondersteunt alleen Transport Layer Security (TLS) 1,0, 1,1 en 1,2.  Secure Sockets Layer (SSL) wordt niet ondersteund.  Configureer de gateway voor het gebruik van ten minste TLS 1,2 om te zorgen voor de beveiliging van gegevens die onderweg zijn naar Log Analytics. Oudere versies van TLS of SSL zijn kwetsbaar. Hoewel ze momenteel achterwaartse compatibiliteit toestaan, kunt u ze beter niet gebruiken.  

Raadpleeg voor meer informatie [veilig verzenden van gegevens met behulp van TLS 1,2](../logs/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Ondersteund aantal agent verbindingen

In de volgende tabel ziet u ongeveer hoeveel agents kunnen communiceren met een gateway server. Ondersteuning is gebaseerd op agents die elke 6 seconden ongeveer 200 KB aan gegevens uploaden. Voor elke geteste agent is het gegevens volume ongeveer 2,7 GB per dag.

|Gateway |Ondersteunde agents (bij benadering)|  
|--------|----------------------------------|  
|CPU: Intel Xeon-processor E5-2660 v3 \@ 2,6 GHz 2 kernen<br> Geheugen: 4 GB<br> Netwerk bandbreedte: 1 Gbps| 600|  
|CPU: Intel Xeon-processor E5-2660 v3 \@ 2,6 GHz 4-cores<br> Geheugen: 8 GB<br> Netwerk bandbreedte: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>De Log Analytics-gateway downloaden

Down load de nieuwste versie van het installatie bestand voor de Log Analytics gateway vanuit het micro soft Download centrum ([Download koppeling](https://go.microsoft.com/fwlink/?linkid=837444)) of via de Azure Portal.

Voer de volgende stappen uit om de Log Analytics-gateway op te halen uit de Azure Portal:

1. Blader door de lijst met Services en selecteer vervolgens **log Analytics**. 
1. Selecteer een werkruimte.
1. Selecteer in de Blade van de werk ruimte onder **algemeen** **snel starten**. 
1. Selecteer **computers** onder **een gegevens bron kiezen om verbinding te maken met de werk ruimte**.
1. Selecteer op de Blade **direct-agent** de optie **log Analytics gateway downloaden**.
 
   ![Scherm afbeelding van de stappen voor het downloaden van de Log Analytics-gateway](./media/gateway/download-gateway.png)

of 

1. Selecteer in de Blade van de werk ruimte onder **instellingen** de optie **Geavanceerde instellingen**.
1. Ga naar **verbonden bronnen**  >  **Windows-servers** en selecteer **log Analytics gateway downloaden**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Log Analytics gateway installeren met de wizard Setup

Voer de volgende stappen uit als u een gateway wilt installeren met de wizard Setup. 

1. Dubbel klik in de doelmap op **Log Analytics gateway.msi**.
1. Op de pagina **Welkom** selecteert u **Volgende**.

   ![Scherm afbeelding van de welkomst pagina in de wizard gateway installeren](./media/gateway/gateway-wizard01.png)

1. Selecteer op de pagina **gebruiksrecht overeenkomst** **de optie Ik ga akkoord met de voor waarden in de gebruiksrecht overeenkomst** om akkoord te gaan met de licentie voorwaarden voor micro soft-software en selecteer **volgende**.
1. Op de pagina **poort en proxy adres** :

   a. Voer het TCP-poort nummer in dat voor de gateway moet worden gebruikt. Setup gebruikt dit poort nummer voor het configureren van een binnenkomende regel op Windows Firewall.  De standaard waarde is 8080.
      Het geldige bereik van het poort nummer is 1 tot en met 65535. Als de invoer niet in dit bereik valt, wordt een fout bericht weer gegeven.

   b. Als de server waarop de gateway is geïnstalleerd, moet communiceren via een proxy, voert u het proxy adres in waar de gateway verbinding moet maken. Voer bijvoorbeeld `http://myorgname.corp.contoso.com:80` in.  Als u dit veld leeg laat, probeert de gateway rechtstreeks verbinding te maken met het internet.  Als voor uw proxy server verificatie is vereist, voert u een gebruikers naam en wacht woord in.

   c. Selecteer **Volgende**.

   ![Scherm opname van configuratie voor de gateway proxy](./media/gateway/gateway-wizard02.png)

1. Als Microsoft Update niet is ingeschakeld, wordt de pagina Microsoft Update weer gegeven en kunt u ervoor kiezen om deze in te scha kelen. Maak een selectie en selecteer **volgende**. Anders gaat u verder met de volgende stap.
1. Accepteer op de pagina **doelmap** de standaardmap C:\Program Files\OMS gateway of voer de locatie in waar u de gateway wilt installeren. Selecteer vervolgens **Volgende**.
1. Selecteer **installeren** op de pagina **gereed voor installatie** . Selecteer **Ja** als u wilt dat Gebruikersaccountbeheer toestemming heeft om te installeren.
1. Nadat de installatie is voltooid, selecteert u **volt ooien**. Als u wilt controleren of de service wordt uitgevoerd, opent u de module Services. msc en controleert u of de **OMS-gateway** wordt weer gegeven in de lijst met Services en of de status wordt **uitgevoerd**.

   ![Scherm opname van lokale services, met de uitvoering van de OMS-gateway](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>De Log Analytics-gateway installeren met behulp van de opdracht regel

Het gedownloade bestand voor de gateway is een Windows Installer-pakket dat een installatie op de achtergrond ondersteunt vanaf de opdracht regel of een andere automatische methode. Zie [opdracht regel opties](/windows/desktop/msi/command-line-options)als u niet bekend bent met de standaard opdracht regel opties voor Windows Installer.
 
De volgende tabel geeft een overzicht van de para meters die door Setup worden ondersteund.

|Parameters| Notities|
|----------|------| 
|PORTNUMBER | TCP-poort nummer voor de gateway waarop moet worden geluisterd |
|WEBTOEPASSINGSPROXY | IP-adres van proxy server |
|INSTALLDIR | Volledig gekwalificeerde pad voor het opgeven van de installatiemap van de gateway software bestanden |
|USERNAME | Gebruikers-ID voor verificatie met proxy server |
|WACHT woord | Wacht woord van de gebruikers-ID die moet worden geverifieerd met de proxy |
|LicenseAccepted | Geef een waarde van **1** op om te controleren of u akkoord gaat met de gebruiksrecht overeenkomst |
|HASAUTH | Geef een waarde van **1** op wanneer de para meters gebruikers naam/wacht woord zijn opgegeven |
|HASPROXY | Geef een waarde van **1** op bij het opgeven van het IP-adres voor de **proxy** parameter |

Als u de gateway op de achtergrond wilt installeren en configureren met een specifiek proxy adres, poort nummer, typt u het volgende:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Met de opdracht regel optie/qn wordt Setup verborgen,/QB geeft de installatie weer tijdens de installatie op de achtergrond.  

Als u referenties moet opgeven voor verificatie bij de proxy, typt u het volgende:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Na de installatie kunt u controleren of de instellingen zijn geaccepteerd (met uitzonde ring van de gebruikers naam en het wacht woord) met behulp van de volgende Power shell-cmdlets:

- **Get-OMSGatewayConfig** : retourneert de TCP-poort waarop de gateway is geconfigureerd om te Luis teren.
- **Get-OMSGatewayRelayProxy** : retourneert het IP-adres van de proxy server waarmee u deze hebt geconfigureerd om te communiceren met.

## <a name="configure-network-load-balancing"></a>Network Load Balancing configureren

U kunt de gateway configureren voor maximale Beschik baarheid met behulp van Network Load Balancing (NLB) met behulp van micro soft [Network Load Balancing (NLB)](/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)of load balancers op basis van hardware. De load balancer beheert het verkeer door de aangevraagde verbindingen van de Log Analytics agents of Operations Manager beheerser vers over de knoop punten te omleiden. Als één gateway server uitvalt, wordt het verkeer omgeleid naar andere knoop punten.

### <a name="microsoft-network-load-balancing"></a>Micro soft Network Load Balancing

Zie [Network Load Balancing](/windows-server/networking/technologies/network-load-balancing)voor meer informatie over het ontwerpen en implementeren van een Windows Server 2016 Network Load Balancing-cluster. In de volgende stappen wordt beschreven hoe u een micro soft Network Load Balancing-cluster configureert.  

1. Meld u aan bij de Windows-Server die lid is van het NLB-cluster met een Administrator-account.  
2. Open beheer van netwerk taakverdeling in Serverbeheer, klik op **extra** en klik vervolgens op **beheer van netwerk** taakverdeling.
3. Klik met de rechter muisknop op het IP-adres van het cluster en klik vervolgens op **host toevoegen aan cluster** om verbinding te maken met een log Analytics Gateway server waarop micro soft monitoring agent is geïnstalleerd. 

    ![Beheer van netwerk taakverdeling – host aan cluster toevoegen](./media/gateway/nlb02.png)
 
4. Voer het IP-adres in van de gateway server die u wilt verbinden. 

    ![Beheer van netwerk taakverdeling: een host toevoegen aan het cluster: verbinding maken](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Zie [Wat is Azure Load Balancer?](../../load-balancer/load-balancer-overview.md)voor meer informatie over het ontwerpen en implementeren van een Azure Load Balancer. Als u een basis load balancer wilt implementeren, volgt u de stappen die worden beschreven in deze [Quick](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) start, met uitzonde ring van de stappen die worden beschreven in de sectie **back-endservers maken**.   

> [!NOTE]
> Voor het configureren van de Azure Load Balancer met behulp van de **basis-SKU**, moeten virtuele Azure-machines deel uitmaken van een beschikbaarheidsset. Zie [de beschik baarheid van virtuele Windows-machines beheren in azure](../../virtual-machines/availability.md)voor meer informatie over beschikbaarheids sets. Als u bestaande virtuele machines wilt toevoegen aan een beschikbaarheidsset, raadpleegt u [Azure Resource Manager-beschikbaarheidsset voor Vm's instellen](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Nadat de load balancer is gemaakt, moet er een back-end-pool worden gemaakt die verkeer naar een of meer Gateway servers distribueert. Volg de stappen die worden beschreven in de sectie Quick Start-artikel [resources maken voor de Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>Bij het configureren van de status test moet deze worden geconfigureerd voor het gebruik van de TCP-poort van de gateway server. Met de Health probe worden de gateway servers dynamisch toegevoegd aan of verwijderd uit de load balancer draaiing op basis van hun reactie op status controles. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>De Log Analytics agent en Operations Manager beheer groep configureren

In deze sectie ziet u hoe u rechtstreeks verbonden Log Analytics agents, een Operations Manager beheer groep of Azure Automation Hybrid Runbook Workers met de Log Analytics gateway kunt configureren om te communiceren met Azure Automation of Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Een zelfstandige Log Analytics-agent configureren

Vervang bij het configureren van de Log Analytics-agent de waarde van de proxy server door het IP-adres van de Log Analytics Gateway server en het poort nummer. Als u meerdere Gateway servers achter een load balancer hebt geïmplementeerd, is de proxy configuratie van de Log Analytics-agent het virtuele IP-adres van de load balancer.  

>[!NOTE]
>Zie [Windows-computers verbinden met de log Analytics-service in azure](../agents/agent-windows.md)om de log Analytics-agent te installeren op de gateway en Windows-computers die rechtstreeks verbinding maken met log Analytics. Zie [Linux-computers verbinden met Azure monitor](../agents/agent-linux.md)voor meer informatie over het verbinden van Linux-computers. 
>

Nadat u de agent op de gateway server hebt geïnstalleerd, moet u deze configureren om te rapporteren aan de werk ruimte of werkruimte agenten die communiceren met de gateway. Als de Log Analytics Windows-agent niet is geïnstalleerd op de gateway, wordt gebeurtenis 300 naar het gebeurtenis logboek van de OMS-gateway geschreven, waarmee wordt aangegeven dat de agent moet worden geïnstalleerd. Als de agent is geïnstalleerd, maar niet is geconfigureerd om te rapporteren aan dezelfde werk ruimte als de agents die door worden gecommuniceerd, wordt gebeurtenis 105 naar hetzelfde logboek geschreven. Dit betekent dat de agent op de gateway moet worden geconfigureerd om te rapporteren aan dezelfde werk ruimte als de agents die communiceren met de gateway.

Nadat u de configuratie hebt voltooid, start u de **OMS-gateway** service opnieuw om de wijzigingen toe te passen. Anders weigert de gateway agents die proberen te communiceren met Log Analytics en rapporteert gebeurtenis 105 in het gebeurtenis logboek van de OMS-gateway. Dit gebeurt ook wanneer u een werk ruimte toevoegt aan of verwijdert uit de configuratie van de agent op de gateway server.

Zie [resources in uw Data Center of Cloud automatiseren met behulp van Hybrid Runbook worker](../../automation/automation-hybrid-runbook-worker.md)voor informatie met betrekking tot de automatiserings Hybrid Runbook Worker.

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Operations Manager configureren, waarbij alle agents dezelfde proxy server gebruiken

De configuratie van de Operations Manager-proxy wordt automatisch toegepast op alle agents die rapporteren aan Operations Manager, zelfs als de instelling leeg is.  

Als u de OMS-gateway wilt gebruiken voor de ondersteuning van Operations Manager, hebt u het volgende nodig:

* Micro soft Monitoring Agent (versie 8.0.10900.0 of hoger) geïnstalleerd op de OMS-Gateway server en is geconfigureerd met dezelfde Log Analytics werk ruimten die door uw beheer groep zijn geconfigureerd om te rapporteren.
* Internet verbinding. De OMS-gateway moet ook zijn verbonden met een proxy server die is verbonden met internet.

> [!NOTE]
> Als u geen waarde voor de gateway opgeeft, worden lege waarden naar alle agents gepusht.
>

Als uw Operations Manager-beheer groep voor de eerste keer wordt geregistreerd met een Log Analytics-werk ruimte, ziet u de optie om de proxy configuratie voor de beheer groep niet op te geven in de operations-console. Deze optie is alleen beschikbaar als de beheer groep is geregistreerd bij de service.  

Als u de integratie wilt configureren, werkt u de systeem proxy configuratie bij met behulp van Netsh op het systeem waarop u de operations-console uitvoert en op alle beheerser vers in de beheer groep. Volg deze stappen:

1. Open een opdracht prompt met verhoogde bevoegdheid:

   a. Selecteer **Start** en typ **cmd**.  

   b. Klik met de rechter muisknop op **opdracht prompt** en selecteer **als administrator uitvoeren**.  

1. Voer de volgende opdracht in:

   `netsh winhttp set proxy <proxy>:<port>`

Nadat de integratie met Log Analytics is voltooid, verwijdert u de wijziging door uit te voeren `netsh winhttp reset proxy` . Gebruik vervolgens de optie **proxy server configureren** in de operations-console om de log Analytics Gateway server op te geven. 

1. Selecteer in de Operations Manager-console, onder **Operations Management Suite**, de optie **verbinding** en selecteer vervolgens **proxy server configureren**.

   ![Scherm opname van Operations Manager, met de selectie proxy server configureren](./media/gateway/scom01.png)

1. Selecteer **een proxy server gebruiken voor toegang tot de Operations Management Suite** en voer vervolgens het IP-adres in van de log Analytics Gateway server of het virtuele IP-adres van de Load Balancer. Zorg ervoor dat u begint met het voor voegsel `http://` .

   ![Scherm opname van Operations Manager, met het proxyserver adres](./media/gateway/scom02.png)

1. Selecteer **Finish**. Uw Operations Manager-beheer groep is nu geconfigureerd om te communiceren via de gateway server naar de Log Analytics-service.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Operations Manager configureren, waarbij specifieke agenten een proxy server gebruiken

Voor grote of complexe omgevingen wilt u mogelijk dat alleen specifieke servers (of groepen) de Log Analytics Gateway server gebruiken.  Voor deze servers kunt u de Operations Manager-agent niet rechtstreeks bijwerken omdat deze waarde wordt overschreven door de globale waarde voor de beheer groep.  In plaats daarvan overschrijft u de regel die wordt gebruikt om deze waarden te pushen.  

> [!NOTE] 
> Gebruik deze configuratie techniek als u meerdere Log Analytics Gateway servers in uw omgeving wilt toestaan.  U kunt bijvoorbeeld vereisen dat specifieke Log Analytics Gateway servers op regionale basis worden opgegeven.
>

Specifieke servers of groepen configureren voor het gebruik van de Log Analytics-Gateway server: 

1. Open de Operations Manager-console en selecteer de werk ruimte **ontwerpen** .  
1. Selecteer in de werk ruimte ontwerpen de optie **regels**. 
1. Selecteer de knop **bereik** op de werk balk Operations Manager. Als deze knop niet beschikbaar is, moet u ervoor zorgen dat u een object hebt geselecteerd, niet een map, in het deel venster **bewaking** . In het dialoog venster **bereik van Management Pack-objecten** wordt een lijst met veelgebruikte doel klassen, groepen of objecten weer gegeven. 
1. In het veld **zoeken naar** voert u **Health Service** in en selecteert u deze in de lijst. Selecteer **OK**.  
1. Zoek naar een regel voor de instellingen van de **Advisor-proxy**. 
1. Selecteer op de Operations Manager werk balk **onderdrukkingen** en wijs vervolgens **de Rule\For een specifiek object van klasse: Health Service** en selecteer een object in de lijst.  Of maak een aangepaste groep die het Health Service-object bevat van de servers waarop u deze onderdrukking wilt Toep assen. Pas vervolgens de onderdrukking toe op uw aangepaste groep.
1. Voeg in het dialoog venster **onderdrukkings eigenschappen** het selectie vakje in de kolom **overschrijven** naast de para meter **WebProxyAddress** toe.  Voer in het veld **onderdrukkings waarde** de URL in van de log Analytics Gateway server. Zorg ervoor dat u begint met het voor voegsel `http://` .  

    >[!NOTE]
    > U hoeft de regel niet in te scha kelen. Het wordt al automatisch beheerd met een onderdrukking in de beveiligings onderdrukking van micro soft System Center Advisor management pack die gericht is op de micro soft System Center Advisor monitoring Server-groep.
    > 

1. Selecteer een management pack in de lijst **doel Management Pack selecteren** of maak een nieuw niet-verzegeld Management Pack door **Nieuw** te selecteren. 
1. Selecteer **OK** wanneer u klaar bent. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configureren voor Hybrid Automation-Runbook Workers

Als u Automation Hybrid Runbook Workers in uw omgeving hebt, volgt u deze stappen om de gateway te configureren voor de ondersteuning van de werk nemers.

Raadpleeg de sectie [uw netwerk configureren](../../automation/automation-hybrid-runbook-worker.md#network-planning) van de Automation-documentatie om de URL voor elke regio te vinden.

Als uw computer automatisch is geregistreerd als Hybrid Runbook Worker, bijvoorbeeld als de Updatebeheer oplossing is ingeschakeld voor een of meer virtuele machines, volgt u deze stappen:

1. Voeg de Url's van de taak runtime gegevens service toe aan de lijst met toegestane hosts op de Log Analytics gateway. Bijvoorbeeld: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Start de Log Analytics Gateway Service opnieuw met behulp van de volgende Power shell-cmdlet: `Restart-Service OMSGatewayService`

Als uw computer is toegevoegd aan Azure Automation met behulp van de Hybrid Runbook Worker registratie-cmdlet, volgt u deze stappen:

1. Voeg de registratie-URL voor de Agent service toe aan de lijst met toegestane hosts op de Log Analytics gateway. Bijvoorbeeld: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Voeg de Url's van de taak runtime gegevens service toe aan de lijst met toegestane hosts op de Log Analytics gateway. Bijvoorbeeld: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Start de Log Analytics Gateway Service opnieuw.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Handige Power shell-cmdlets

U kunt cmdlets gebruiken om de taken uit te voeren om de configuratie-instellingen van de Log Analytics-gateway bij te werken. Voordat u cmdlets gebruikt, moet u het volgende doen:

1. Installeer de Log Analytics-gateway (Microsoft Windows Installer).
1. Open een Power shell-console venster.
1. Importeer de module door de volgende opdracht te typen: `Import-Module OMSGateway`
1. Als er in de vorige stap geen fout is opgetreden, is de module met succes geïmporteerd en kunnen de cmdlets worden gebruikt. Voer `Get-Module OMSGateway` in
1. Nadat u de cmdlets hebt gebruikt om wijzigingen aan te brengen, start u de OMS-Gateway Service opnieuw.

Een fout in stap 3 betekent dat de module niet is geïmporteerd. De fout kan optreden wanneer Power shell de module niet kan vinden. U kunt de module vinden in het pad van de OMS-gateway: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parameters** | **Beschrijving** | **Voorbeeld** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Sleutel |Hiermee wordt de configuratie van de service opgehaald |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Sleutel (vereist) <br> Waarde |Hiermee wordt de configuratie van de service gewijzigd |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Hiermee wordt het adres van de relay-proxy (upstream) opgehaald |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adres<br> Gebruikersnaam<br> Wacht woord (beveiligde teken reeks) |Hiermee stelt u het adres (en de referentie) van de relay-proxy (upstream) in |1. een doorstuur proxy en referentie instellen:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. een relay-proxy instellen waarvoor geen authenticatie is vereist: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Wis de instelling voor de relay-proxy:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Hiermee wordt de momenteel toegestane host opgehaald (alleen de lokaal geconfigureerde host, niet automatisch gedownloade hosts) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (vereist) |De host wordt toegevoegd aan de lijst met toegestane hosts |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (vereist) |Hiermee verwijdert u de host uit de lijst met toegestane hosts |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Subject (vereist) |Het client certificaat onderwerp toevoegen aan de lijst met toegestane certificaten |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Subject (vereist) |Hiermee wordt het onderwerp van het client certificaat verwijderd uit de lijst met toegestane clients |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Hiermee worden de momenteel toegestane certificaat houders van client certificaten opgehaald (alleen de lokaal geconfigureerde, niet automatisch gedownloade onderwerpen) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Problemen oplossen

Als u gebeurtenissen wilt verzamelen die door de gateway worden geregistreerd, moet de Log Analytics-agent zijn geïnstalleerd.

![Scherm afbeelding van de lijst Logboeken in het Log Analytics gateway-logboek](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics gateway gebeurtenis-Id's en-beschrijvingen

In de volgende tabel worden de gebeurtenis-Id's en beschrijvingen voor Log Analytics-gateway logboek gebeurtenissen weer gegeven.

| **Id** | **Beschrijving** |
| --- | --- |
| 400 |Een toepassings fout die geen specifieke ID heeft. |
| 401 |Onjuiste configuratie. Bijvoorbeeld, listenPort = "text" in plaats van een geheel getal. |
| 402 |Uitzonde ring bij het parseren van TLS-Handshake-berichten. |
| 403 |Netwerk fout. Kan bijvoorbeeld geen verbinding maken met de doel server. |
| 100 |Algemene informatie. |
| 101 |De service is gestart. |
| 102 |De service is gestopt. |
| 103 |Er is een HTTP CONNECT-opdracht ontvangen van de client. |
| 104 |Geen HTTP-VERBINDINGS opdracht. |
| 105 |De doel server bevindt zich niet in de lijst met toegestane servers of de doel poort is niet beveiligd (443). <br> <br> Zorg ervoor dat de MMA-agent op de OMS-Gateway server en de agents die communiceren met de OMS-gateway, zijn verbonden met dezelfde Log Analytics-werk ruimte. |
| 105 |FOUT TcpConnection: ongeldig client certificaat: CN = gateway. <br><br> Zorg ervoor dat u de OMS-gateway versie 1.0.395.0 of hoger gebruikt. Zorg er ook voor dat de MMA-agent op de OMS-Gateway server en de agents die communiceren met de OMS-gateway, zijn verbonden met dezelfde Log Analytics-werk ruimte. |
| 106 |Niet-ondersteunde TLS/SSL-protocol versie.<br><br> De Log Analytics-gateway ondersteunt alleen TLS 1,0, TLS 1,1 en 1,2. SSL wordt niet ondersteund.|
| 107 |De TLS-sessie is gecontroleerd. |

### <a name="performance-counters-to-collect"></a>Te verzamelen prestatie meter items

De volgende tabel bevat de prestatie meter items die beschikbaar zijn voor de Log Analytics-gateway. Gebruik prestatie meter om de tellers toe te voegen.

| **Naam** | **Beschrijving** |
| --- | --- |
| Log Analytics gateway/actieve client verbinding |Aantal actieve client netwerk (TCP)-verbindingen |
| Aantal Log Analytics gateway/fout |Aantal fouten |
| Log Analytics gateway/verbonden client |Aantal verbonden clients |
| Aantal Log Analytics gateway/weigering |Aantal afwijzingen vanwege een TLS-validatie fout |

![Scherm opname van Log Analytics Gateway Interface, met prestatie meter items](./media/gateway/counters.png)

## <a name="assistance"></a>Ondersteuning

Wanneer u bent aangemeld bij de Azure Portal, kunt u hulp krijgen bij de Log Analytics gateway of een andere Azure-service of-functie.
Als u hulp wilt krijgen, selecteert u het pictogram vraag teken in de rechter bovenhoek van de portal en selecteert u **nieuwe ondersteunings aanvraag**. Vul vervolgens het nieuwe formulier voor de ondersteunings aanvraag in.

![Scherm opname van een nieuwe ondersteunings aanvraag](./media/gateway/support.png)

## <a name="next-steps"></a>Volgende stappen

[Voeg gegevens bronnen](./../agents/agent-data-sources.md) toe om gegevens te verzamelen van verbonden bronnen en sla de gegevens op in uw log Analytics-werk ruimte.