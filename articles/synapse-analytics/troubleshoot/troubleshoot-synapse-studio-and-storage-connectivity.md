---
title: Problemen met de connectiviteit tussen Synapse Studio en opslag oplossen
description: Problemen met de connectiviteit tussen Synapse Studio en opslag oplossen
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d5f79131608315f7e1c05cbfc0117300eea6c511
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566270"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Problemen met de connectiviteit tussen Azure Synapse Analytics Synapse Studio en opslag oplossen

In Synapse Studio kunt u gegevensbronnen verkennen die zich in uw gekoppelde opslag bevinden. Deze handleiding helpt u connectiviteitsproblemen op te lossen wanneer u toegang probeert te krijgen tot uw gegevensbronnen. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Case #1: Opslagaccount heeft niet de juiste machtigingen

Als uw opslagaccount niet de juiste machtigingen heeft, kunt u de opslagstructuur niet uitbreiden via 'Gegevens' --> 'Gekoppeld' in Synapse Studio. Zie de schermopname van het symptoom van het probleem hieronder. 

Het gedetailleerde foutbericht kan variëren, maar de algemene betekenis van het foutbericht is: 'Deze aanvraag is niet gemachtigd om deze bewerking uit te voeren'.

In het gekoppelde opslag-knooppunt:  
![Verbindingsprobleem met opslag 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

In het knooppunt van de opslagcontainer:  
![Verbindingsprobleem met opslag 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**OPLOSSING:** als u uw account wilt toewijzen aan de juiste rol, zie Use the Azure Portal to assign an Azure role for access to blob and queue data (De functie Azure Portal om een Azure-rol toe te wijzen voor toegang [tot blob- en wachtrijgegevens)](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Case #2: kan de aanvraag niet verzenden naar de opslagserver

Wanneer u de pijl selecteert om de opslagstructuur uit te vouwen in 'Gegevens' --> 'Gekoppeld' in Synapse Studio, ziet u mogelijk het probleem 'REQUEST_SEND_ERROR' in het linkerpaneel. Zie de schermopname van het symptoom van het probleem hieronder:

In het gekoppelde opslag-knooppunt:  
![Verbindingsprobleem met opslag 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

In het knooppunt van de opslagcontainer:  
![Verbindingsprobleem met opslag 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Dit probleem kan verschillende oorzaken hebben:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>De opslagresource staat achter een vNet en een privé-eindpunt voor opslag moet worden geconfigureerd

**OPLOSSING:** in dit geval moet u het privé-eindpunt voor opslag configureren voor uw opslagaccount. Zie Use the Azure Portal to assign an Azure role for access to blob and queue data (De Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens) voor informatie over het configureren van het privé-eindpunt [voor opslag voor vNet.](../security/how-to-connect-to-workspace-from-restricted-network.md)

U kunt de opdracht nslookup .dfs.core.windows.net' gebruiken om de connectiviteit te controleren nadat het privé-eindpunt voor \<storage-account-name\> opslag is geconfigureerd. Deze moet een tekenreeks retourneren die vergelijkbaar is met: \<storage-account-name\> .privatelink.dfs.core.windows.net.

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>De opslagresource ligt niet achter een vNet, maar het eindpunt Blob service (Azure AD) is niet toegankelijk omdat de firewall is geconfigureerd

**OPLOSSING:** in dit geval moet u uw opslagaccount openen in Azure Portal. Schuif in het linkernavigatievenster omlaag naar  **Ondersteuning en** probleemoplossing en selecteer Connectiviteitscontrole om de verbindingsstatus van de Blob service **(Azure AD)** te controleren. Als deze niet toegankelijk is, volgt u de gepromoveerde handleiding om de configuratie **van firewalls** en virtuele netwerken onder de pagina van uw opslagaccount te controleren. Zie Configure Azure Storage firewalls and virtual networks (Firewalls en virtuele netwerken configureren) voor meer informatie [over opslagfirewalls.](../../storage/common/storage-network-security.md)

### <a name="other-issues-to-check"></a>Andere problemen om te controleren 

* De opslagresource die u gebruikt, wordt Azure Data Lake Storage Gen2 en zich achter een firewall en vNet (met een geconfigureerd privé-eindpunt voor opslag) op hetzelfde moment.
* De containerresource die u wilt openen, is verwijderd of bestaat niet.
* Cross-tenant: de werkruimte-tenant die de gebruiker heeft gebruikt om zich aan te melden, is niet hetzelfde met de tenant van het opslagaccount. 


## <a name="next-steps"></a>Volgende stappen
Als u het probleem niet kunt oplossen met de vorige stappen, maakt [u een ondersteuningsticket](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)aan.