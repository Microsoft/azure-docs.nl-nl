---
title: Gegevens repliceren via ExpressRoute met Azure Migrate server migratie
description: Azure ExpressRoute gebruiken voor replicatie met Azure Migrate server migratie
author: ms-deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: fe44ff423240cbe24d91cef0c0f4bb803ad8df98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745562"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Gegevens repliceren via ExpressRoute met Azure Migrate: Server migratie

In dit artikel vindt u informatie over het configureren van [Azure migrate: Server migratie](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) om gegevens te repliceren via een ExpressRoute-circuit tijdens het migreren van servers naar Azure.

## <a name="understand-azure-expressroute-circuits"></a>Meer informatie over Azure ExpressRoute-circuits
Een ExpressRoute-circuit (er) is een verbinding met uw on-premises infra structuur met micro soft via een connectiviteits provider. ExpressRoute-circuits kunnen worden geconfigureerd voor het gebruik van privé-peering, micro soft-peering of beide. Raadpleeg het artikel over [ExpressRoute-circuits en peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) voor meer informatie over de verschillende opties voor peering die beschikbaar zijn met ExpressRoute.

Het hulp programma voor server migratie van Azure Migrate helpt u bij het migreren van on-premises servers en servers van andere Clouds naar Azure virtual machines. Het hulp programma werkt door een doorlopende replicatie stroom in te stellen voor het repliceren van gegevens van de servers die moeten worden gemigreerd naar Managed disks in uw Azure-abonnement. Wanneer u klaar bent om de servers te migreren, worden de gerepliceerde gegevens in azure gebruikt voor het migreren van de servers.

Gegevens die vanaf uw on-premises servers worden gerepliceerd, kunnen worden geconfigureerd voor verzen ding naar uw Azure-abonnement via internet (met een beveiligde versleutelde verbinding) of via een ExpressRoute-verbinding. Wanneer u een groot aantal servers hebt om te migreren, kunt u met ExpressRoute voor replicatie efficiënter servers migreren met behulp van de ingerichte band breedte die beschikbaar is met uw ExpressRoute-circuit.

In dit artikel leert u
> [!div class="checklist"]
>
> * Gegevens repliceren met behulp van een ExpressRoute-circuit met persoonlijke peering.
> * Gegevens repliceren met behulp van een ExpressRoute-circuit met micro soft-peering.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Gegevens repliceren met behulp van een ExpressRoute-circuit met privé-peering

> [!NOTE]
> Repliceren via een privé-peering-circuit wordt momenteel alleen ondersteund voor de [migratie van virtuele VMware-machines naar Azure](./tutorial-migrate-vmware.md). Ondersteuning voor privé-eind punten voor andere [replicatie methoden](./migrate-services-overview.md#azure-migrate-server-migration-tool) is binnenkort beschikbaar.

In de methode zonder agent van het migreren van virtuele VMware-machines naar Azure, uploadt het Azure Migrate apparaat eerst de replicatie gegevens naar een opslag account (cache-opslag account) in uw abonnement. Gerepliceerde gegevens uit het cache-opslag account worden vervolgens verplaatst naar met replica's beheerde schijven in uw abonnement door de Azure Migrate-service. Als u een privé-peering-circuit voor replicatie wilt gebruiken, maakt en koppelt u een persoonlijk eind punt aan het gebruik van het cache-opslag account. Privé-eind punten gebruiken een of meer privé IP-adressen van uw virtuele netwerk (VNet), waardoor het opslag account effectief in uw Azure VNet wordt gezet. Met het persoonlijke eind punt kan het Azure Migrate apparaat verbinding maken met het cache-opslag account met behulp van ExpressRoute-persoonlijke peering en gegevens rechtstreeks op het privé-IP-adres overdragen. <br/>  

![Replicatieproces](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Naast de replicatie gegevens communiceert het Azure Migrate-apparaat met de Azure Migrate-service voor de beheer activiteiten van het besturings systeem, waaronder het organiseren van replicatie. Het beheer van de communicatie tussen het Azure Migrate apparaat en de Azure Migrate-service blijft plaatsvinden via internet op het open bare eind punt van Azure Migrate service.
> - Het persoonlijke eind punt van het opslag account moet toegankelijk zijn vanaf het netwerk waarop het Azure Migrate apparaat wordt geïmplementeerd.
> - DNS moet worden geconfigureerd om DNS-query's door het Azure Migrate-apparaat voor het BLOB-service-eind punt van het cache-opslag account om te zetten naar het privé-IP-adres van het privé-eind punt dat is gekoppeld aan het cache-opslag account.
> - Het cache-opslag account moet toegankelijk zijn op het open bare eind punt. (De Azure Migrate-service maakt gebruik van het open bare eind punt van het cache-opslag account om gegevens te verplaatsen van het opslag account naar door de replica beheerde schijven.) 


### <a name="1-pre-requisites"></a>1. vereisten

De Azure-gebruiker die het persoonlijke eind punt maakt, moet de volgende machtigingen hebben voor de resource groep en het virtuele netwerk waarin het persoonlijke eind punt wordt gemaakt.

**Gebruiksscenario** | **Machtigingen** 
--- | --- 
 Privé-eind punten maken en beheren. | Micro soft. Network/privateEndpoint/schrijven/actie<br/>Micro soft. Network/privateEndpoint/lezen/actie  
|Koppel een persoonlijk eind punt aan een VNet/subnet.<br/>Dit is vereist in het virtuele netwerk waar het persoonlijke eind punt wordt gemaakt.| Micro soft. Network/virtualNetworks/subnet/lid/actie micro soft. Network/virtualNetworks/lid/actie
|Koppel het persoonlijke eind punt aan een opslag account. <br/>| Micro soft. micro soft. Storage/Storage accounts/privateEndpointConnectionApproval/actie <br/> Micro soft. micro soft. Storage/Storage accounts/privateEndpointConnections/lezen
|Maak een netwerk interface en voeg deze toe aan een netwerk beveiligings groep. | Micro soft. Network/networkInterfaces/lezen <br/> Micro soft. Network/networkInterfaces/subnetten/schrijven <br/> Micro soft. Network/networkInterfaces/subnetten/lezen<br/> Micro soft. Network/networkSecurityGroups/lid/actie (optioneel)
Maak en beheer privé-DNS-zones.| Rol Inzender Privé-DNS zone <br/> _Of_ <br/> Micro soft. Network/privateDnsZones/A/* <br/>  Micro soft. Network/privateDnsZones/schrijven micro soft. Network/privateDnsZones/lezen <br/> Micro soft. Network/privateEndpoints/privateDnsZoneGroups/schrijven <br/> Micro soft. Network/privateEndpoints/privateDnsZoneGroups/lezen <br/> Micro soft. Network/privateDnsZones/virtualNetworkLinks/schrijven <br/>  Micro soft. Network/privateDnsZones/virtualNetworkLinks/lezen <br/> Micro soft. Network/virtualNetworks/samen voegen/actie 

### <a name="2-identify-the-cache-storage-account"></a>2. het cache-opslag account identificeren 
 
Azure Migrate maakt automatisch een cache-opslag account wanneer u de replicatie (met behulp van de Azure Portal-ervaring) voor een virtuele machine voor de eerste keer in een Azure Migrate project configureert. Het opslag account wordt gemaakt in hetzelfde abonnement en dezelfde resource groep als waarin u het Azure Migrate project hebt gemaakt.

Het opslag account maken en vinden:

1. Gebruik de Azure Portal-ervaring voor Azure Migrate om een of meer virtuele machines in het project te repliceren.
2. Navigeer naar de resource groep van het Azure Migrate project.
3. Zoek het cache-opslag account door het voor voegsel **' LSA '** te identificeren in de naam van het opslag account.

![Weer gave resource groep](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Als u meer dan één opslag account met het voor voegsel **' LSA '** in uw resource groep hebt, kunt u het opslag account controleren door te navigeren naar het menu replicatie-instellingen en doel configuratie voor een van de replicerende vm's in het project. <br/> 
> ![Overzicht van replicatie-instellingen](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. upgrade cache-opslag account naar Algemeen v2 

U kunt alleen privé-eind punten maken op een GPv2-opslag account (Algemeen v2). Als het cache-opslag account geen GPv2-opslag account is, moet u het bijwerken naar GPv2 met behulp van de volgende stappen:

1. Ga naar uw opslagaccount.
2. Selecteer **Configuratie**.
3. Onder **soort account** selecteert u **upgrade**.
4. Typ bij **Upgrade bevestigen** de naam van uw account.
5. Selecteer een **upgrade** onder aan de pagina.

![Opslag account upgraden](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. Maak een persoonlijk eind punt voor het opslag account

1. Ga naar uw opslag account, selecteer **netwerken** in het menu links en selecteer het tabblad **verbindingen met het particuliere eind punt** .  
2. Selecteer **+ Privé-eindpunt**.

    a. Selecteer in het venster **een privé-eind punt maken** het **abonnement** en de **resource groep**. Geef een naam op voor uw privé-eind punt en selecteer de regio van het opslag account.  
    ![Configuratie venster van PE](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. Geef op het tabblad **resource** de naam op van het **abonnement** waarin het opslag account zich bevindt. Kies **micro soft. Storage/Storage accounts** als het **resource type**. Geef in **resource** de naam van het GPv2-type replicatie opslag account op. Selecteer **BLOB** als de **subresource** van het doel.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. Selecteer op het tabblad **configuratie** het **virtuele netwerk** en **subnet** voor het persoonlijke eind punt van het opslag account.  

    > [!Note]
    > Het virtuele netwerk moet het ExpressRoute gateway-eind punt bevatten of moeten zijn verbonden met het virtuele netwerk met de ExpressRoute-gateway. 

    Selecteer in de sectie **integratie** van privé-DNS **Ja** en integreer met een privé-DNS-zone. Als u **Ja** selecteert, wordt de DNS-zone automatisch gekoppeld aan het geselecteerde virtuele netwerk en worden de DNS-records toegevoegd die vereist zijn voor de DNS-omzetting van nieuwe ip's en volledig gekwalificeerde domein namen die zijn gemaakt voor het persoonlijke eind punt. Meer informatie over [privé-DNS-zones.](https://docs.microsoft.com/azure/dns/private-dns-overview)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. U kunt ook **labels** voor uw persoonlijke eind punt toevoegen.  

    e. Ga door met het invoeren van gegevens en het eenmaal **maken** is voltooid. Wanneer de validatie is voltooid, selecteert u **maken** om het persoonlijke eind punt te maken.

    > [!Note]
    > Als de gebruiker die het persoonlijke eind punt maakt ook de eigenaar van het opslag account is, wordt het persoonlijke eind punt automatisch goedgekeurd. Anders moet de eigenaar het persoonlijke eind punt goed keuren voor gebruik. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Persoonlijke DNS-zones maken en DNS-records hand matig toevoegen (optioneel)

Als u niet de optie hebt geselecteerd om te integreren met een privé-DNS-zone op het moment dat het persoonlijke eind punt wordt gemaakt, volgt u de stappen in deze sectie om hand matig een privé-DNS-zone te maken. 

> [!Note]
> Als u **Ja** hebt geselecteerd om te integreren met een privé-DNS-zone, kunt u deze sectie overs Laan. 

1. Maak een privé-DNS-zone. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  Selecteer op de pagina **privé-DNS zones** de knop **+ toevoegen** om te beginnen met het maken van een nieuwe zone.  
    b.  Vul op de pagina **persoonlijke DNS-zone maken** de vereiste gegevens in. Voer de naam van de privé-DNS-zone in als _privatelink_. blob.core.Windows.net. c. Ga naar het tabblad **controleren en maken** om de DNS-zone te controleren en te maken.

2. Koppel de privé-DNS-zone aan uw virtuele netwerk.  

    De hierboven gemaakte privé-DNS-zone moet zijn gekoppeld aan het virtuele netwerk waaraan het persoonlijke eind punt is gekoppeld.

    a. Ga naar de privé-DNS-zone die u in de vorige stap hebt gemaakt en navigeer naar koppelingen naar het virtuele netwerk aan de linkerkant van de pagina. Selecteer de knop **+ toevoegen** .   
    b. Vul de vereiste gegevens in. De velden **abonnement** en **virtueel netwerk** moeten worden ingevuld met de bijbehorende details van het virtuele netwerk waar uw persoonlijke eind punt is gekoppeld. De overige velden kunnen worden achtergelaten.

3. De volgende stap is het toevoegen van DNS-records aan de DNS-zone. Voeg een vermelding voor de Fully Qualified Domain Name van het opslag account toe aan uw privé-DNS-zone.

    a. Ga naar de privé-DNS-zone en navigeer naar de sectie **overzicht** aan de linkerkant van de pagina. Selecteer **+ Recordset** om records toe te voegen.  

    b. Voeg op de pagina **recordset toevoegen** een vermelding voor de Fully Qualified Domain name en het persoonlijke IP-adres toe als een type record.

> [!Important]
> U kunt extra DNS-instellingen vereisen om het privé-IP-adres van het privé-eind punt van het opslag account op te lossen vanuit de bron omgeving. [Lees dit artikel](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) om inzicht te krijgen in de vereiste DNS-configuratie.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Gegevens repliceren met behulp van een ExpressRoute-circuit met micro soft-peering

U kunt micro soft-peering of een bestaand openbaar peering domein (afgeschaft voor nieuwe ExpressRoute-verbindingen) gebruiken om het replicatie verkeer via een ExpressRoute-circuit te routeren, zoals in het onderstaande diagram wordt geïllustreerd.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Zelfs als er replicatie gegevens over het micro soft-peered circuit worden overgenomen, hebt u nog steeds Internet verbinding nodig vanaf de on-premises site voor andere communicatie (besturings vlak) met de Azure Migrate service. Er zijn enkele extra Url's die niet bereikbaar zijn via ExpressRoute, dat de replicatie-en Hyper-V-host toegang nodig heeft om het replicatie proces te organiseren. U kunt de URL-vereisten controleren op basis van het migratie scenario, [VMware-agentloze migraties](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) of [migraties op basis](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance)van een agent.  

Als u een proxy op uw on-premises site gebruikt en ExpressRoute voor het replicatie verkeer wilt gebruiken, moet u een proxy-bypass voor relevante Url's op het on-premises apparaat configureren. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Regels voor het overs laan van proxy's op het Azure Migrate apparaat configureren (voor VMware-agenten zonder agent)

1. Meld u aan bij het Azure Migrate apparaat (extern bureau blad).   
2. Open het bestand C:/Program Data/MicrosoftAzure/config/appliance.jsop het gebruik van Klad blok.
3. Wijzig in het bestand de regel met "EnableProxyBypassList": "false" in "EnableProxyBypassList": "True". Sla de wijzigingen op en start het apparaat opnieuw op.
4. Na het opnieuw opstarten van het configuratie beheer van het apparaat kunt u de optie voor het overs laan van een proxy weer geven in de gebruikers interface van de web-app. Voeg de onderstaande Url's toe aan de lijst met overs laan van de proxy.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Regels voor het overs laan van proxy's op het replicatie apparaat configureren (voor migraties op basis van een agent)

Volg de onderstaande stappen voor het configureren van de lijst proxy bypass op de configuratie server en de proces servers:

1. [Down load het PsExec-hulp programma](https://docs.microsoft.com/sysinternals/downloads/psexec) om toegang te krijgen tot de systeem gebruikers context.
2. Open Internet Explorer in de context van het systeem gebruikers door de volgende opdracht regel PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe" uit te voeren.
3. Proxy-instellingen toevoegen in Internet Explorer.
4. Voeg in de lijst overs Laan de Azure Storage URL. *. blob. core. Windows. net toe.  

De bovenstaande regels voor overs Laan zorgen ervoor dat het replicatie verkeer kan stromen via ExpressRoute terwijl de beheer communicatie de proxy voor Internet kan passeren.  

Daarnaast moet u routes adverteren in het route filter voor de volgende BGP-community's om ervoor te zorgen dat uw Azure Migrate replicatie verkeer in een ExpressRoute-circuit in plaats van Internet gaat.  

- Regionale BGP-Community voor de Azure-bron regio (Azure Migrate project regio)
- Regionale BGP-Community voor de Azure-doel regio (regio voor migratie)
- BGP-Community voor Azure Active Directory (12076:5060)

Meer informatie over [route filters](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) en de lijst met [BGP-community's voor ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp). 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [ExpressRoute-circuits](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings).
- Meer informatie over [ExpressRoute-routerings domeinen](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare).
- Meer informatie over [privé-eind punten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).