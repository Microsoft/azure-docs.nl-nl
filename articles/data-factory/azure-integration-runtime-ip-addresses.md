---
title: IP-adressen van Azure Integration Runtime
description: Meer informatie over welke IP-adressen u binnenkomend verkeer moet toestaan, om firewalls correct te configureren voor het beveiligen van netwerk toegang tot gegevens archieven.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 7b663c8d6e5849d39bb8366c82f45e0fd66d77dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371393"
---
# <a name="azure-integration-runtime-ip-addresses"></a>IP-adressen van Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De IP-adressen die Azure Integration Runtime gebruikt, zijn afhankelijk van de regio waar uw Azure Integration runtime zich bevindt. *Alle* Azure Integration runtimes die zich in dezelfde regio bevinden, gebruiken dezelfde IP-adresbereiken.

> [!IMPORTANT]  
> Gegevens stromen en Azure Integration Runtime die beheerde Virtual Network inschakelen, bieden geen ondersteuning voor het gebruik van vaste IP-bereiken.
>
> U kunt deze IP-bereiken gebruiken voor het verplaatsen van gegevens, pijp lijnen en externe activiteiten. Deze IP-bereiken kunnen worden gebruikt voor het filteren in gegevens archieven/netwerk beveiligings groepen (NSG)/firewalls voor inkomende toegang vanuit Azure Integration runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP-adressen: specifieke regio's

Sta verkeer toe van de IP-adressen die worden vermeld voor Azure Integration runtime in de specifieke Azure-regio waar uw resources zich bevinden. U kunt een lijst met IP-bereiken voor servicetags ophalen via de koppeling [IP-bereiken voor servicetags downloaden](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). Als de Azure-regio bijvoorbeeld **AustraliaEast** is, kunt u een IP-bereik lijst ophalen uit **DataFactory. AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Bekend probleem met Azure Storage

* Wanneer u verbinding maakt met Azure Storage account, hebben IP-netwerk regels geen invloed op aanvragen die afkomstig zijn van de Azure Integration runtime in dezelfde regio als het opslag account. [Raadpleeg dit artikel](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)voor meer informatie. 

  In plaats daarvan wordt u aangeraden [vertrouwde services te gebruiken terwijl u verbinding maakt met Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Volgende stappen

* [Beveiligings overwegingen voor het verplaatsen van gegevens in Azure Data Factory](data-movement-security-considerations.md)