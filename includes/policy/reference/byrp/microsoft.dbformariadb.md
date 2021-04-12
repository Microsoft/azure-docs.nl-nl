---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d62ceef8ffd16800bb191303ecbeaff7c26d2298
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089747"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Met Azure Database for MariaDB kunt u de redundantieoptie voor uw databaseserver kiezen. Deze kan worden ingesteld op een geografisch redundante back-upopslag waarin de gegevens niet alleen worden opgeslagen in de regio waar uw server wordt gehost, maar ook worden gerepliceerd naar een koppelde regio om een hersteloptie te bieden ingeval van storing in uw regio. Het configureren van geografisch redundante opslag voor back-up is alleen toegestaan tijdens het maken van een server. |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[De MariaDB-server moet gebruikmaken van een service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Firewallregels op basis van virtuele netwerken worden gebruikt om verkeer vanaf een specifiek subnet naar Azure Database for MariaDB in te schakelen, waarbij ervoor wordt gezorgd dat het verkeer binnen de grens van Azure blijft. Dit beleid biedt een manier om te controleren of Azure Database for MariaDB een virtuele-netwerkservice-eindpunt gebruikt. |AuditIfNotExists, uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Het privé-eindpunt moet worden ingeschakeld voor MariaDB-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Met privé-eindpuntverbindingen wordt beveiligde communicatie afgedwongen door middel van het inschakelen van privéconnectiviteit met Azure Database for MariaDB. Configureer een privé-eindpuntverbinding om alleen toegang mogelijk te maken tot verkeer dat afkomstig is van bekende netwerken en om toegang te voorkomen vanaf alle andere IP-adressen, ook binnen Azure. |AuditIfNotExists, uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Openbare netwerktoegang moet worden uitgeschakeld voor MariaDB-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Schakel de eigenschap 'openbare netwerktoegang' uit om de beveiliging te verbeteren en ervoor te zorgen dat uw Azure Database for MariaDB alleen toegankelijk is vanuit een privé-eindpunt. Met deze configuratie wordt de toegang vanuit elke openbare adresruimte buiten Azure IP-bereik geheel uitgeschakeld, en worden alle aanmeldingen die overeenkomen met firewallregels op basis van IP of virtueel netwerk, geweigerd. |Controle, uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
