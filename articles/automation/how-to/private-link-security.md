---
title: Gebruik Azure Private Link om netwerken veilig te verbinden met Azure Automation
description: Gebruik Azure Private Link om netwerken veilig te verbinden met Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28f4c314b65a27c71c7620ff5941463b1ea68b55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831452"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Gebruik Azure Private Link om netwerken veilig te verbinden met Azure Automation

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de Automation-service in uw VNet wordt ingevoerd. Netwerkverkeer tussen de machines op het VNet en het Automation-account gaat via het VNet en een privékoppeling op het backbone-netwerk van Microsoft, waardoor de blootstelling van het openbare internet wordt voorkomen.

U hebt bijvoorbeeld een VNet waarin u uitgaande internettoegang hebt uitgeschakeld. U wilt echter privé toegang tot uw Automation-account en Gebruikmaken van Automation-functies zoals webhooks, State Configuration en runbooktaken in Hybrid Runbook Workers. Bovendien wilt u dat gebruikers alleen toegang hebben tot het Automation-account via het VNET.  Het implementeren van een privé-eindpunt bereikt deze doelstellingen.

In dit artikel wordt beschreven wanneer u een privé-eindpunt kunt gebruiken en instellen met uw Automation-account.

![Conceptueel overzicht van Private Link voor Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Private Link ondersteuning voor Azure Automation is alleen beschikbaar in commerciële Azure- en Azure-clouds voor de Amerikaanse overheid.

## <a name="advantages"></a>Voordelen

Met Private Link kunt u het volgende doen:

- Privé verbinding maken met Azure Automation zonder openbare netwerktoegang te openen.
- Maak privé verbinding met Azure Monitor Log Analytics-werkruimte zonder openbare netwerktoegang te openen.

    >[!NOTE]
    >Er is een afzonderlijk privé-eindpunt voor uw Log Analytics-werkruimte vereist als uw Automation-account is gekoppeld aan een Log Analytics-werkruimte voor het doorsturen van taakgegevens en wanneer u functies zoals Updatebeheer, Wijzigingen bijhouden en inventaris, State Configuration of VM's buiten bedrijfsuren starten/stoppen hebt ingeschakeld. Zie Use Private Link [Azure Private Link to securely connect networks to](../../azure-monitor/logs/private-link-security.md)Azure Monitor Azure Monitor (Gebruik Azure Private Link om netwerken veilig te verbinden met Azure Monitor) voor meer informatie over Azure Monitor.

- Zorg ervoor dat uw Automation-gegevens alleen toegankelijk zijn via geautoriseerde particuliere netwerken.
- Voorkom gegevens exfiltratie van uw particuliere netwerken door uw Azure Automation resource te definiëren die verbinding maakt via uw privé-eindpunt.
- Uw privé-on-premises netwerk veilig verbinden met Azure Automation expressroute en Private Link.
- Houd al het verkeer binnen het Microsoft Azure backbone-netwerk.

Zie Voor meer informatie [Belangrijke voordelen van Private Link.](../../private-link/private-link-overview.md#key-benefits)

## <a name="limitations"></a>Beperkingen

- In de huidige implementatie van Private Link kunnen cloudtaken van het Automation-account geen toegang krijgen tot Azure-resources die zijn beveiligd met behulp van een privé-eindpunt. Bijvoorbeeld: Azure Key Vault, Azure SQL, Azure Storage account, enzovoort. Als tijdelijke oplossing gebruikt u in plaats daarvan [een Hybrid Runbook Worker.](../automation-hybrid-runbook-worker.md)
- U moet de nieuwste versie van de [Log Analytics-agent voor](../../azure-monitor/agents/log-analytics-agent.md) Windows of Linux gebruiken.
- De [Log Analytics-gateway](../../azure-monitor/agents/gateway.md) biedt geen ondersteuning voor Private Link.

## <a name="how-it-works"></a>Uitleg

Azure Automation Private Link een of meer privé-eindpunten (en dus de virtuele netwerken waarin ze zijn opgenomen) met uw Automation-accountresource. Deze eindpunten zijn machines die webhooks gebruiken om een runbook te starten, machines die als host voor de Hybrid Runbook Worker-rol worden gebruikt en Desired State Configuration knooppunten (DSC).

Nadat u privé-eindpunten voor Automation hebt gemaakt, worden alle openbare Automation-URL's aan één privé-eindpunt in uw VNet. U of een machine kan rechtstreeks contact opnemen met de Automation-URL's.

### <a name="webhook-scenario"></a>Webhookscenario

U kunt runbooks starten door een POST uit te voeren op de webhook-URL. De URL ziet er bijvoorbeeld als volgende uit: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Hybrid Runbook Worker scenario

Met de Hybrid Runbook Worker van Azure Automation kunt u runbooks rechtstreeks uitvoeren op de Azure- of niet-Azure-machine, inclusief servers die zijn geregistreerd bij Azure Arc servers. Vanaf de computer of server die als host voor de rol wordt gebruikt, kunt u er rechtstreeks runbooks op uitvoeren en uitvoeren op resources in de omgeving om deze lokale resources te beheren.

Een JRDS-eindpunt wordt door de Hybrid Worker gebruikt om runbooks te starten/stoppen, de runbooks naar de werker te downloaden en de taaklogboekstroom terug te sturen naar de Automation-service.Nadat JRDS-eindpunt is inschakelen, ziet de URL er als volgende uit: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Dit zorgt ervoor dat runbookuitvoering op de hybrid worker die is verbonden met Azure Virtual Network taken kan uitvoeren zonder dat er een uitgaande verbinding met internet hoeft te worden geopend.  

> [!NOTE]
>Met de huidige implementatie van Privékoppelingen voor Azure Automation ondersteunt het alleen taken die worden uitgevoerd op de Hybrid Runbook Worker die zijn verbonden met een virtueel Azure-netwerk en worden cloudtaken niet ondersteund.

## <a name="hybrid-worker-scenario-for-update-management"></a>Hybrid Worker scenario voor Updatebeheer  

De systeem-Hybrid Runbook Worker ondersteunt een set verborgen runbooks die worden gebruikt door de Updatebeheer-functie die zijn ontworpen voor het installeren van door de gebruiker opgegeven updates op Windows- en Linux-computers. Wanneer Azure Automation Updatebeheer is ingeschakeld, wordt elke machine die is verbonden met uw Log Analytics-werkruimte automatisch geconfigureerd als een Hybrid Runbook Worker.

Als u meer & wilt Updatebeheer u [Over Updatebeheer.](../update-management/overview.md) De Updatebeheer-functie is afhankelijk van een Log Analytics-werkruimte. Daarom moet u de werkruimte koppelen aan een Automation-account. Een Log Analytics-werkruimte slaat gegevens op die door de oplossing zijn verzameld en host de zoekopdrachten en weergaven in logboeken.

Als u wilt dat uw computers die zijn geconfigureerd voor Updatebeheer op een veilige manier verbinding maken met de Automation & Log Analytics-werkruimte via het Private Link-kanaal, moet u Private Link inschakelen voor de Log Analytics-werkruimte die is gekoppeld aan het Automation-account dat is geconfigureerd met Private Link.

U kunt bepalen hoe een Log Analytics-werkruimte buiten de Private Link bereiken kan worden bereikt door de stappen te volgen die worden beschreven in [Log Analytics configureren.](../../azure-monitor/logs/private-link-security.md#configure-log-analytics) Als u **Openbare netwerktoegang voor opname** toestaan in stelt op Nee, kunnen computers buiten de verbonden bereiken geen gegevens uploaden naar deze werkruimte.  Als u Openbare **netwerktoegang voor query's** toestaan in stelt op **Nee,** hebben computers buiten de bereiken geen toegang tot gegevens in deze werkruimte.

Gebruik **DSCAndHybridWorker-doelsubresource** om de Private Link in te & hybrid workers van het systeem.

> [!NOTE]
> Machines die buiten Azure worden gehost en die worden beheerd door Updatebeheer en die zijn verbonden met het Azure VNet via persoonlijke ExpressRoute-peering, VPN-tunnels en gekoppelde virtuele netwerken met behulp van privé-eindpunten ondersteunen Private Link.

### <a name="state-configuration-agentsvc-scenario"></a>State Configuration (agentsvc) scenario

State Configuration biedt u een Azure-configuratiebeheerservice waarmee u PowerShell Desired State Configuration-configuraties (DSC) kunt schrijven, beheren en compileren voor knooppunten in een cloud- of on-premises datacenter.

De agent op de machine registreert zich bij de DSC-service en gebruikt vervolgens het service-eindpunt om de DSC-configuratie op te halen. Het service-eindpunt van de agent ziet er als: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

De URL voor & privé-eindpunt is hetzelfde, maar wordt wel aan een privé-IP-adres gekoppeld wanneer Private Link is ingeschakeld.

## <a name="planning-based-on-your-network"></a>Plannen op basis van uw netwerk

Voordat u uw Automation-accountresource instelt, moet u rekening houden met de vereisten voor netwerkisolatie. Evalueer de toegang van uw virtuele netwerken tot openbaar internet en de toegangsbeperkingen voor uw Automation-account (inclusief het instellen van een Private Link-groepsbereik voor Azure Monitor-logboeken, indien geïntegreerd met uw Automation-account). Neem ook een beoordeling van de [DNS-records](./automation-region-dns-records.md) van de Automation-service op als onderdeel van uw plan om ervoor te zorgen dat de ondersteunde functies probleemloos werken.

### <a name="connect-to-a-private-endpoint"></a>Verbinding maken met een privé-eindpunt

Maak een privé-eindpunt om ons netwerk te verbinden. U kunt deze maken in Azure Portal Private Link [midden.](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) Zodra uw wijzigingen in publicNetworkAccess en Private Link toegepast, kan het tot 35 minuten duren voordat ze van kracht zijn.

In deze sectie maakt u een privé-eindpunt voor uw Automation-account.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerk > Private Link-centrum.**

2. Selecteer in **Private Link-centrum – Overzicht** bij de optie **Een particuliere verbinding met een service maken** de optie **Start**.

3. Voer **in Een virtuele machine maken - Basisprincipes** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam | Voer uw *PrivateEndpoint in.* |
    | Region | Selecteer **YourRegion**. |
    |||

4. Selecteer **Volgende: Resource**.

5. Voer **in Een privé-eindpunt maken - Resource** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer Verbinding maken met een Azure-resource in mijn directory.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.Automation/automationAccounts.** |
    | Resource |Selecteer *myAutomationAccount*|
    |Subresource van doel |Selecteer *Webhook* of *DSCAndHybridWorker,* afhankelijk van uw scenario.|
    |||

6. Selecteer **Volgende: Configuratie**.

7. Voer **in Een privé-eindpunt maken - Configuratie** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**NETWERKEN**| |
    | Virtueel netwerk| Selecteer *MyVirtualNetwork*. |
    | Subnet | Selecteer *mySubnet*. |
    |**INTEGRATIE VAN PRIVÉ-DNS**||
    |Integreren met privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(Nieuw)privatelink.azure-automation.net* |
    |||

8. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

9. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

Selecteer in **Private Link-centrum** **privé-eindpunten om** uw Private Link-resource weer te maken.

![Privékoppeling voor Automation-resource](./media/private-link-security/private-link-automation-resource.png)

Selecteer de resource om alle details te bekijken. Hiermee maakt u een nieuw privé-eindpunt voor uw Automation-account en wijst u er een privé-IP-adres aan toe vanuit uw virtuele netwerk. De **verbindingsstatus** wordt als **goedgekeurd weergeven.**

Op dezelfde manier wordt er een unieke FQDN (Fully Qualified Domain Name) gemaakt voor de State Configuration (agentsvc) en voor de Hybrid Runbook Worker job runtime (jrds). Aan elk van deze ip-adressen wordt een afzonderlijk IP-adres van uw VNet toegewezen en de **verbindingsstatus** wordt als **goedgekeurd weer gegeven.**

Als de servicegebruiker Azure RBAC-machtigingen heeft voor de Automation-resource, kan deze de automatische goedkeuringsmethode kiezen. In dit geval is er geen actie vereist van de serviceprovider wanneer de aanvraag de Automation-providerresource bereikt en wordt de verbinding automatisch goedgekeurd.

## <a name="set-public-network-access-flags"></a>Vlaggen voor openbare netwerktoegang instellen

U kunt een Automation-account configureren om alle openbare configuraties te weigeren en alleen verbindingen via privé-eindpunten toe te staan om de netwerkbeveiliging verder te verbeteren. Als u de toegang tot het Automation-account alleen vanuit het VNet wilt beperken en geen toegang vanaf openbaar internet wilt toestaan, kunt u eigenschap `publicNetworkAccess` instellen op `$false` .

Wanneer  de instelling Openbare netwerktoegang is ingesteld op , zijn alleen verbindingen via privé-eindpunten toegestaan en worden alle verbindingen via openbare eindpunten geweigerd met een niet-geautoriseerd foutbericht en `$false` de HTTP-status 401.

Het volgende PowerShell-script laat zien hoe u en de eigenschap Openbare netwerktoegang op `Get` `Set` automation-accountniveau kunt gebruiken: 

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

U kunt ook de eigenschap openbare netwerktoegang vanuit de Azure Portal. Selecteer in uw Automation-account **netwerkisolatie** in het linkerdeelvenster onder de **sectie Accountinstellingen.** Wanneer de instelling Openbare netwerktoegang is ingesteld op **Nee,** worden alleen verbindingen via privé-eindpunten toegestaan en worden alle verbindingen via openbare eindpunten geweigerd.

![Instelling Openbare netwerktoegang](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>DNS-configuratie

Wanneer u verbinding maakt met een private link-resource met behulp van een FQDN (Fully Qualified Domain Name) als onderdeel van de connection string, is het belangrijk dat u uw DNS-instellingen correct configureert om om te gaan naar het toegewezen privé-IP-adres. Bestaande Azure-services hebben mogelijk al een DNS-configuratie om te gebruiken bij het maken van verbinding via een openbaar eindpunt. Uw DNS-configuratie moet worden gecontroleerd en bijgewerkt om verbinding te maken met behulp van uw privé-eindpunt.

De netwerkinterface die is gekoppeld aan het privé-eindpunt bevat de volledige set informatie die nodig is om uw DNS te configureren, inclusief FQDN en privé-IP-adressen die zijn toegewezen voor een bepaalde private link-resource.

U kunt de volgende opties gebruiken om uw DNS-instellingen voor privé-eindpunten te configureren:

* Gebruik het hostbestand (alleen aanbevolen voor testen). U kunt het hostbestand op een virtuele machine gebruiken om te overschrijven met behulp van DNS voor naam resolutie eerst. Uw DNS-vermelding moet er als volgt uitzien: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Gebruik een [privé-DNS-zone.](../../dns/private-dns-privatednszone.md) U kunt privé-DNS-zones gebruiken om de DNS-resolutie voor een bepaald privé-eindpunt te overschrijven. Een privé-DNS-zone kan worden gekoppeld aan uw virtuele netwerk om specifieke domeinen om te zetten. Als u wilt dat de agent op uw virtuele machine kan communiceren via het privé-eindpunt, maakt u een Privé-DNS record als `privatelink.azure-automation.net` . Voeg een  nieuwe DNS A-recordtoewijzing toe aan het IP-adres van het privé-eindpunt.

* Gebruik uw DNS-doorsturende server (optioneel). U kunt uw DNS-doorschakeling gebruiken om de DNS-resolutie voor een bepaalde Private Link-resource te overschrijven. Als uw [DNS-server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wordt gehost op een virtueel netwerk, kunt u een DNS-regel voor doorsturen maken om een privé-DNS-zone te gebruiken om de configuratie voor alle Private Link-resources te vereenvoudigen.

Raadpleeg [DNS-configuratie voor Azure-privé-eindpunt](../../private-link/private-endpoint-dns.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie Wat is azure-privé-eindpunt? voor meer informatie over [privé-eindpunten.](../../private-link/private-endpoint-overview.md)
