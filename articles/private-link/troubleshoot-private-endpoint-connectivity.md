---
title: Verbindingsproblemen met het Azure-privé-eindpunt oplossen
description: Stapsgewijs richtlijnen voor het diagnosticeren van connectiviteit van privé-eindpunten
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 2a4f86d9fae7b78a57cf8da7ab42d2d4a4cd7be5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835394"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Verbindingsproblemen met het Azure-privé-eindpunt oplossen

Dit artikel bevat stapsgewijse richtlijnen voor het valideren en diagnosticeren van de configuratie van uw azure-privé-eindpuntconnectiviteit.

Azure Private Endpoint is een netwerkinterface die u privé en veilig verbindt met een Private Link-service. Met deze oplossing kunt u uw workloads in Azure beveiligen door privéconnectiviteit te bieden met uw Azure-servicebronnen vanuit uw virtuele netwerk. Deze oplossing brengt deze services effectief naar uw virtuele netwerk.

Dit zijn de connectiviteitsscenario's die beschikbaar zijn met een privé-eindpunt:

- Virtueel netwerk uit dezelfde regio
- Regionaal peering van virtuele netwerken
- Wereldwijd peered virtuele netwerken
- Klant on-premises via VPN- of Azure ExpressRoute-circuits

## <a name="diagnose-connectivity-problems"></a>Connectiviteitsproblemen vaststellen 

Bekijk deze stappen om ervoor te zorgen dat alle gebruikelijke configuraties zijn zoals verwacht om verbindingsproblemen met de installatie van uw privé-eindpunt op te lossen.

1. Controleer de configuratie van het privé-eindpunt door door de resource te bladeren.

    a. Ga naar **Private Link-centrum**.

      ![Private Link-centrum](./media/private-endpoint-tsg/private-link-center.png)

    b. Selecteer privé-eindpunten **in het linkerdeelvenster.**
    
      ![Privé-eindpunten](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filter en selecteer het privé-eindpunt dat u wilt diagnosticeren.

    d. Controleer het virtuele netwerk en de DNS-gegevens.
     - Controleer of de verbindingstoestand **Goedgekeurd is.**
     - Zorg ervoor dat de virtuele machine verbinding heeft met het virtuele netwerk dat als host voor de privé-eindpunten wordt gebruikt.
     - Controleer of de FQDN-gegevens (kopie) en het privé-IP-adres zijn toegewezen.
    
       ![Virtueel netwerk en DNS-configuratie](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Gebruik [Azure Monitor](../azure-monitor/overview.md) om te zien of er gegevens stromen.

    a. Selecteer monitor op de **privé-eindpuntresource.**
     - Selecteer **Bytes in** of **Bytes Out.** 
     - Kijk of er gegevens stromen wanneer u verbinding probeert te maken met het privé-eindpunt. Verwacht een vertraging van ongeveer 10 minuten.
    
       ![Telemetrie van privé-eindpunt verifiëren](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Gebruik **Problemen met VM-verbinding** oplossen vanuit Azure Network Watcher.

    a. Selecteer de client-VM.

    b. Selecteer **Verbinding oplossen** en selecteer vervolgens het tabblad **Uitgaande** verbindingen.
    
      ![Network Watcher- Uitgaande verbindingen testen](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selecteer **Use Network Watcher voor gedetailleerde verbindingstracing.**
    
      ![Network Watcher - Problemen met de verbinding oplossen](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selecteer **Testen op FQDN.**
     - Plak de FQDN van de privé-eindpuntresource.
     - Geef een poort op. Gebruik normaal gesproken 443 voor Azure Storage of Azure Cosmos DB en 1336 voor SQL.

    e. Selecteer **Testen** en valideer de testresultaten.
    
      ![Network Watcher - Testresultaten](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. Voor DNS-resolutie uit de testresultaten moet hetzelfde privé-IP-adres zijn toegewezen aan het privé-eindpunt.

    a. Als de DNS-instellingen onjuist zijn, volgt u deze stappen:
     - Als u een privézone gebruikt: 
       - Zorg ervoor dat het virtuele netwerk van de client-VM is gekoppeld aan de privézone.
       - Controleer of de privé-DNS-zonerecord bestaat. Als deze niet bestaat, maakt u deze.
     - Als u aangepaste DNS gebruikt:
       - Controleer uw aangepaste DNS-instellingen en controleer of de DNS-configuratie juist is.
       Zie Overzicht van [privé-eindpunten: DNS-configuratie voor hulp.](./private-endpoint-overview.md#dns-configuration)

    b. Als de connectiviteit mislukt vanwege netwerkbeveiligingsgroepen (NSG's) of door de gebruiker gedefinieerde routes:
     - Controleer de regels voor uitgaand verkeer van de NSG en maak de juiste uitgaande regels om verkeer toe te staan.
    
       ![Regels voor uitgaand verkeer van NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Virtuele bronmachine moet de volgende hop naar privé-eindpunt-IP hebben als InterfaceEndpoints in de effectieve NIC-routes. 

    a. Als u de route van het privé-eindpunt niet kunt zien in de bron-VM, controleert u of 
     - De bron-VM en het privé-eindpunt behoren tot hetzelfde VNET. Zo ja, dan moet u ondersteuning betrekken. 
     - De bron-VM en het privé-eindpunt maken deel uit van verschillende VNET's en controleren vervolgens op de IP-verbinding tussen de VNETS. Als er IP-connectiviteit is en u de route nog steeds niet kunt zien, schakelt u ondersteuning in. 

1. Als de verbinding resultaten heeft gevalideerd, kan het connectiviteitsprobleem te maken hebben met andere aspecten, zoals geheimen, tokens en wachtwoorden op de toepassingslaag.
   - Controleer in dit geval de configuratie van de private link-resource die is gekoppeld aan het privé-eindpunt. Zie de gids voor probleemoplossing voor Azure Private Link [meer informatie](troubleshoot-private-link-connectivity.md)
   
1. Het is altijd goed om dit te beperken voordat u een ondersteuningsticket maakt. 

    a. Als de bron on-premises verbinding maakt met een privé-eindpunt in Azure, probeert u verbinding te maken 
      - Ga naar een andere virtuele machine vanaf on-premises en controleer of u een IP-verbinding hebt met de Virtual Network on-premises. 
      - Van een virtuele machine in de Virtual Network naar het privé-eindpunt.
      
    b. Als de bron Azure is en het privé-eindpunt zich in een ander Virtual Network, probeert u verbinding te maken 
      - Naar het privé-eindpunt vanuit een andere bron. Hiermee kunt u eventuele specifieke problemen met virtuele machines isoleren. 
      - Voor elke virtuele machine die deel uitmaakt van dezelfde Virtual Network van die van het privé-eindpunt.  

1. Neem contact [Ondersteuning voor Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) het team als uw probleem nog steeds niet is opgelost en er nog steeds een verbindingsprobleem bestaat.

## <a name="next-steps"></a>Volgende stappen

 * [Een privé-eindpunt maken in het bijgewerkte subnet (Azure Portal)](./create-private-endpoint-portal.md)
 * [Azure Private Link voor het oplossen van problemen](troubleshoot-private-link-connectivity.md)
