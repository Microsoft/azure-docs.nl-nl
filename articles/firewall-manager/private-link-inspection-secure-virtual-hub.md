---
title: Beveiligd verkeer dat is bestemd voor privé-eind punten in azure Virtual WAN
description: Meer informatie over het gebruik van netwerk regels en toepassings regels voor het beveiligen van verkeer dat is bestemd voor privé-eind punten in azure Virtual WAN
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259350"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Beveiligd verkeer dat is bestemd voor privé-eind punten in azure Virtual WAN

> [!NOTE]
> Dit artikel is alleen van toepassing op beveiligde virtuele hub. Als u verkeer wilt controleren dat is bestemd voor privé-eind punten met behulp van Azure Firewall in een hub-virtueel netwerk, raadpleegt u [Azure firewall gebruiken om verkeer te controleren dat is bestemd voor een persoonlijk eind punt](../private-link/inspect-traffic-with-azure-firewall.md).

[Persoonlijk Azure-eind punt](../private-link/private-endpoint-overview.md) is de fundamentele bouw steen voor [persoonlijke Azure-koppelingen](../private-link/private-link-overview.md). Met persoonlijke eind punten kunnen Azure-resources worden geïmplementeerd in een virtueel netwerk om privé te communiceren met persoonlijke koppelings bronnen.

Met persoonlijke eind punten is toegang tot de persoonlijke koppelings service in een virtueel netwerk mogelijk. Toegang tot het persoonlijke eind punt via de peering van virtuele netwerken en on-premises netwerk verbindingen breiden de connectiviteit uit.

Mogelijk moet u verkeer van clients op locatie of in azure filteren dat is bestemd voor services die worden weer gegeven via persoonlijke eind punten in een virtueel WAN verbonden virtueel netwerk. Dit artikel begeleidt u bij deze taak met behulp van [beveiligde virtuele hub](../firewall-manager/secured-virtual-hub.md) met [Azure firewall](../firewall/overview.md) als de beveiligings provider.

Azure Firewall verkeer met een van de volgende methoden wordt gefilterd:

* [FQDN in netwerk regels](../firewall/fqdn-filtering-network-rules.md) voor TCP-en UDP-protocollen
* [FQDN in toepassings regels](../firewall/features.md#application-fqdn-filtering-rules) voor http, HTTPS en MSSQL.
* Bron-en doel-IP-adressen, poort en protocol met [netwerk regels](../firewall/features.md#network-traffic-filtering-rules)

Gebruik toepassings regels via netwerk regels om verkeer te controleren dat bestemd is voor privé-eind punten.
Een beveiligde virtuele hub wordt beheerd door micro soft en kan niet worden gekoppeld aan een [privé-DNS zone](../dns/private-dns-privatednszone.md). Dit is vereist om de bron-FQDN van een [persoonlijke koppeling](../private-link/private-endpoint-overview.md#private-link-resource) naar het bijbehorende IP-adres van het privé-eind punt op te lossen.

SQL FQDN-filtering wordt alleen ondersteund in de [proxy modus](../azure-sql/database/connectivity-architecture.md#connection-policy) (poort 1433). *Proxy* modus kan leiden tot meer latentie in vergelijking met *omleiden*. Als u de omleidings modus wilt blijven gebruiken. Dit is de standaard instelling voor clients die verbinding maken in Azure. u kunt de toegang filteren met behulp van FQDN in Firewall-netwerk regels.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Verkeer filteren met FQDN in netwerk-en toepassings regels

Met de volgende stappen schakelt u Azure Firewall het verkeer te filteren met behulp van FQDN in netwerk-en toepassings regels:

1. Implementeer een [DNS-doorstuur server-](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) virtuele machine in een virtueel netwerk dat is verbonden met de beveiligde virtuele hub en gekoppeld aan de privé-DNS zones die de a-record typen voor de persoonlijke eind punten hosten.

2. Configureer [aangepaste DNS-instellingen](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) zodanig dat ze verwijzen naar het IP-adres van de DNS-doorstuur server en DNS-proxy inschakelen in het firewall beleid dat is gekoppeld aan de Azure firewall geïmplementeerd in de beveiligde virtuele hub.

3. Configureer [aangepaste DNS-servers](../virtual-network/manage-virtual-network.md#change-dns-servers) voor de virtuele netwerken die zijn verbonden met de beveiligde virtuele hub, om te verwijzen naar het privé-IP-adres dat is gekoppeld aan de Azure firewall geïmplementeerd in de beveiligde virtuele hub.

4. Configureer on-premises DNS-servers om DNS-query's voor de open bare DNS-zones met persoonlijke eind punten door te sturen naar het privé-IP-adres dat is gekoppeld aan de Azure Firewall geïmplementeerd in de beveiligde virtuele hub.

5. Configureer zo nodig een [toepassings regel](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) of [netwerk regel](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) in het firewall beleid dat is gekoppeld aan de Azure firewall geïmplementeerd in de beveiligde virtuele hub met het *doel type* FQDN en de open bare FQDN-bron van de persoonlijke *koppeling.*

6. Navigeer naar *beveiligde virtuele hubs* in het firewall beleid dat is gekoppeld aan de Azure firewall die is geïmplementeerd in de beveiligde virtuele hub en selecteer de beveiligde virtuele hub waar verkeer filters die bestemd zijn voor persoonlijke eind punten, worden geconfigureerd.

7. Navigeer naar **beveiligings configuratie**, selecteer **verzenden via Azure firewall** onder **privé verkeer**.

8. Selecteer voor **voegsels voor privé verkeer** om de CIDR-voor voegsels te bewerken die worden geïnspecteerd via Azure firewall in de beveiligde virtuele hub en voeg als volgt een voor voegsel voor/32 voor elk privé-eind punt toe:

   > [!IMPORTANT]
   > Als deze/32 voor voegsels niet zijn geconfigureerd, wordt het verkeer dat is bestemd voor privé-eind punten, omzeild Azure Firewall.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Firewall Manager-beveiligings configuratie" border="true":::

Deze stappen werken alleen wanneer de clients en persoonlijke eind punten worden geïmplementeerd in verschillende virtuele netwerken die zijn verbonden met dezelfde beveiligde virtuele hub en voor on-premises clients. Als de clients en persoonlijke eind punten in hetzelfde virtuele netwerk zijn geïmplementeerd, moeten er een UDR met/32-routes voor de persoonlijke eind punten worden gemaakt. Configureer deze routes met het type van de **volgende hop**  ingesteld op **virtueel apparaat** en **adres van volgende hop** ingesteld op het privé IP-adres van de Azure firewall geïmplementeerd in de beveiligde virtuele hub. **Gateway routes door geven** moeten worden ingesteld op **Ja**.

In het volgende diagram ziet u de DNS-en gegevens verkeer stromen voor de verschillende clients om verbinding te maken met een persoonlijk eind punt dat is geïmplementeerd in azure Virtual WAN:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Verkeers stromen" border="true":::

## <a name="troubleshooting"></a>Problemen oplossen

De belangrijkste problemen die u mogelijk hebt tijdens het filteren van verkeer dat is bestemd voor privé-eind punten via beveiligde virtuele hub zijn:

- Clients kunnen geen verbinding maken met privé-eind punten.

- Azure Firewall wordt overgeslagen. Dit symptoom kan worden gevalideerd door het ontbreken van logboek vermeldingen voor netwerk-of toepassings regels in Azure Firewall.

In de meeste gevallen worden deze problemen veroorzaakt door een van de volgende problemen:

- Onjuiste DNS-naam omzetting

- Onjuiste routerings configuratie

### <a name="incorrect-dns-name-resolution"></a>Onjuiste DNS-naam omzetting

1. Controleer of de DNS-servers van het virtuele netwerk zijn ingesteld op *aangepast* en het IP-adres is het privé-IP-adres van Azure firewall in de beveiligde virtuele hub.

   Azure CLI:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Verifieer clients in hetzelfde virtuele netwerk als de DNS-doorstuur server-virtuele machine kan de open bare FQDN van het privé-eind punt omzetten in het bijbehorende privé-IP-adres door de virtuele machine die is geconfigureerd als DNS-doorstuur server rechtstreeks te doorzoeken.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Inspecteer *AzureFirewallDNSProxy* Azure firewall-logboek vermeldingen en valideer het kan DNS-query's ontvangen en omzetten van de clients.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Controleer of de *DNS-proxy* is ingeschakeld en of een *aangepaste* DNS-server die wijst naar het IP-adres van het IP-adres van de DNS Forwarder-virtuele machine is geconfigureerd in het firewall beleid dat is gekoppeld aan de Azure firewall in de beveiligde virtuele hub.

   Azure CLI:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Onjuiste routerings configuratie

1. Controleer de *beveiligings configuratie* in het firewall beleid dat is gekoppeld aan de Azure firewall geïmplementeerd in de beveiligde virtuele hub. Zorg ervoor dat de kolom **privé verkeer** wordt weer gegeven als **beveiligd door Azure firewall** voor het virtuele netwerk en de verbindingen van filialen waarvoor u verkeer wilt filteren.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Privé verkeer beveiligd door Azure Firewall" border="true":::

2. Controleer de **beveiligings configuratie** in het firewall beleid dat is gekoppeld aan de Azure firewall geïmplementeerd in de beveiligde virtuele hub. Zorg ervoor dat er een/32-vermelding is voor elk privé-IP-adres van het privé-eind punt waarvoor u verkeer wilt filteren onder **privé-verkeer voor voegsels**.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Firewall Manager-beveiligings configuratie-voor voegsels voor privé verkeer" border="true":::

3. Inspecteer in de beveiligde virtuele hub onder virtueel WAN de juiste routes voor de route tabellen die zijn gekoppeld aan de virtuele netwerken en de verbindingen van filialen waarvoor u verkeer wilt filteren. Zorg ervoor dat er/32-vermeldingen zijn voor elk privé-IP-adres van het privé-eind punt waarvoor u verkeer wilt filteren.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Efficiënte routes van beveiligde virtuele hub" border="true":::

4. Inspecteer de efficiënte routes op de Nic's die zijn gekoppeld aan de virtuele machines waarop u verkeer wilt filteren. Zorg ervoor dat er/32-vermeldingen zijn voor elk privé-IP-adres van het privé-eind punt waarvoor u verkeer wilt filteren.
 
   Azure CLI:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Inspecteer de routerings tabellen van uw on-premises routerings apparaten. Zorg ervoor dat u de adres ruimten van de virtuele netwerken bekijkt waarin de privé-eind punten worden geïmplementeerd.

   Met Azure Virtual WAN worden de voor voegsels die zijn geconfigureerd onder **privé-verkeer voor voegsels** in firewall beleid **beveiligings configuratie** niet geadverteerd naar on-premises. Er wordt verwacht dat de/32-vermeldingen niet worden weer gegeven in de routerings tabellen van uw on-premises routerings apparaten.

6. Inspecteer Azure Firewall logboeken van **AzureFirewallApplicationRule** en **AzureFirewallNetworkRule** . Controleer of het verkeer dat bestemd is voor de persoonlijke eind punten, wordt geregistreerd.

   **AzureFirewallNetworkRule** -logboek vermeldingen bevatten geen FQDN-gegevens. Filteren op IP-adres en poort bij het controleren van netwerk regels.

   Bij het filteren van verkeer dat is bestemd voor [Azure files](../storage/files/storage-files-introduction.md) privé-eind punten, worden **AzureFirewallNetworkRule** -logboek vermeldingen alleen gegenereerd wanneer een client het eerst koppelt of verbinding maakt met de bestands share. Azure Firewall genereert geen logboeken voor [ruwe](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) bewerkingen voor bestanden in de bestands share. Dit komt doordat ruwe bewerkingen worden uitgevoerd via het permanente TCP-kanaal dat wordt geopend wanneer de client voor het eerst verbinding maakt of koppelt aan de bestands share.

   Voor beeld van query op toepassings regel logboek:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Volgende stappen

- [Azure Firewall gebruiken om verkeer te controleren dat is bestemd voor een privé-eindpunt](../private-link/inspect-traffic-with-azure-firewall.md)
