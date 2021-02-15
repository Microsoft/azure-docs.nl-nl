---
title: Azure Private Link voor Azure Data Factory
description: Meer informatie over hoe Azure private link werkt in Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 9e4d686f582a202dbc543620c7bf73dc4e7adb22
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389175"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link voor Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Met behulp van een persoonlijke Azure-koppeling kunt u verbinding maken met verschillende platforms als een service (PaaS)-implementaties in azure via een persoonlijk eind punt. Een persoonlijk eind punt is een privé-IP-adres binnen een specifiek virtueel netwerk en subnet. Zie de documentatie van een [persoonlijke koppeling](../private-link/index.yml)voor een lijst met PaaS-implementaties die functionaliteit voor persoonlijke koppelingen ondersteunen. 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Beveiligde communicatie tussen klanten netwerken en Azure Data Factory 
U kunt een virtueel Azure-netwerk instellen als een logische weer gave van uw netwerk in de Cloud. Dit biedt de volgende voor delen:
* U helpt uw Azure-resources te beschermen tegen aanvallen in open bare netwerken.
* U kunt de netwerken en Data Factory veilig met elkaar communiceren. 

U kunt ook een on-premises netwerk verbinden met uw virtuele netwerk door Internet Protocol een VPN-verbinding (site-naar-site) of een Azure ExpressRoute-verbinding (privé-peering) in te stellen. 

U kunt ook een zelf-hostende Integration runtime installeren op een on-premises computer of een virtuele machine in het virtuele netwerk. Zo kunt u:
* Kopieer activiteiten uit te voeren tussen een gegevens archief in de Cloud en een gegevens archief in een particulier netwerk.
* Verzend transformatie activiteiten op basis van reken bronnen in een on-premises netwerk of een virtueel Azure-netwerk. 

Er zijn verschillende communicatie kanalen vereist tussen Azure Data Factory en het virtuele netwerk van de klant, zoals wordt weer gegeven in de volgende tabel:

| Domain | Poort | Beschrijving |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Een besturings vlak dat vereist is voor Data Factory ontwerpen en bewaken. |
| `*.{region}.datafactory.azure.net` | 443 | Vereist door de zelf-hostende Integration Runtime om verbinding te maken met de Data Factory-service. |
| `*.servicebus.windows.net` | 443 | Vereist door de zelf-hostende integration runtime voor interactief ontwerpen. |
| `download.microsoft.com` | 443 | Vereist door de zelf-hostende Integration Runtime voor het downloaden van de updates. |

Met de ondersteuning van een persoonlijke koppeling voor Azure Data Factory kunt u het volgende doen:
* Maak een persoonlijk eind punt in uw virtuele netwerk.
* De particuliere verbinding met een specifiek data factory exemplaar inschakelen. 

De communicatie met de Azure Data Factory-service gaat via een privé koppeling en biedt beveiligde persoonlijke connectiviteit. 

![Diagram van een persoonlijke koppeling voor Azure Data Factory architectuur.](./media/data-factory-private-link/private-link-architecture.png)

Het inschakelen van de service voor persoonlijke koppelingen voor elk van de voor gaande communicatie kanalen biedt de volgende functionaliteit:
- **Ondersteund**:
   - U kunt de data factory in uw virtuele netwerk ontwerpen en bewaken, zelfs als u alle uitgaande communicatie blokkeert.
   - De opdracht communicatie tussen de zelf-hostende Integration runtime en de Azure Data Factory-service kan veilig worden uitgevoerd in een particuliere netwerk omgeving. Het verkeer tussen de zelf-hostende Integration runtime en de Azure Data Factory-service verloopt via een persoonlijke koppeling. 
- **Momenteel niet ondersteund**:
   - Interactief ontwerpen waarbij gebruik wordt gemaakt van een zelf-hostende Integration runtime, zoals de verbinding testen, bladeren in mappen lijst en tabel lijst, schema ophalen en gegevens bekijken, via een persoonlijke koppeling.
   - De nieuwe versie van de zelf-hostende Integration runtime kan automatisch worden gedownload van het micro soft Download centrum als u automatisch bijwerken inschakelt.

   > [!NOTE]
   > Voor functionaliteit die momenteel niet wordt ondersteund, moet u nog steeds het eerder genoemde domein en de poort in het virtuele netwerk of de firewall van uw bedrijf configureren. 

   > [!NOTE]
   > Verbinding maken met Azure Data Factory via een privé-eind punt is alleen van toepassing op zelf-hostende Integration runtime in data factory. Het wordt niet ondersteund in Synapse.

> [!WARNING]
> Wanneer u een gekoppelde service maakt, moet u ervoor zorgen dat uw referenties zijn opgeslagen in een Azure-sleutel kluis. Anders werken de referenties niet wanneer u een persoonlijke koppeling inschakelt in Azure Data Factory.

## <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privé-eind punten
Wanneer u een persoonlijk eind punt maakt, wordt de DNS CNAME-bron record voor de Data Factory bijgewerkt naar een alias in een subdomein met het voor voegsel ' privatelink '. Standaard maken we ook een [privé-DNS-zone](../dns/private-dns-overview.md), die overeenkomt met het subdomein ' privatelink ', met de DNS a-bron records voor de privé-eind punten.

Wanneer u de data factory eind punt-URL van buiten het VNet met het persoonlijke eind punt omzetten, wordt deze omgezet in het open bare eind punt van de data factory-service. Wanneer het is opgelost vanuit het VNet dat het persoonlijke eind punt host, wordt de URL van het opslag eindpunt omgezet naar het IP-adres van het privé-eind punt.

Voor het bovenstaande voor beeld is de DNS-bron records voor de Data Factory ' DataFactoryA ', wanneer deze zijn omgezet van buiten de VNet die als host fungeert voor het persoonlijke eind punt:

| Naam | Type | Waarde |
| ---------- | -------- | --------------- |
| DataFactoryA. {Region}. DataFactory. Azure. net | CNAME   | DataFactoryA. {Region}. privatelink. DataFactory. Azure. net |
| DataFactoryA. {Region}. privatelink. DataFactory. Azure. net | CNAME   | < open bare eind punt van data factory-service > |
| < open bare eind punt van data factory-service >  | A | < open bare IP-adres van data factory-service > |

De DNS-resource records voor DataFactoryA, wanneer deze zijn opgelost in het VNet dat als host fungeert voor het persoonlijke eind punt, zijn:

| Naam | Type | Waarde |
| ---------- | -------- | --------------- |
| DataFactoryA. {Region}. DataFactory. Azure. net | CNAME   | DataFactoryA. {Region}. privatelink. DataFactory. Azure. net |
| DataFactoryA. {Region}. privatelink. DataFactory. Azure. net   | A | IP-adres van < privé-eind punt > |

Als u een aangepaste DNS-server in uw netwerk gebruikt, moeten clients de FQDN voor het Data Factory-eind punt kunnen omzetten in het IP-adres van het privé-eind punt. U moet uw DNS-server zo configureren dat uw privé-koppelings subdomein wordt overgedragen aan de privé-DNS-zone voor het VNet, of dat u de A-records voor DataFactoryA configureert. {Region}. privatelink. DataFactory. Azure. net ' met het IP-adres van het privé-eind punt.

Raadpleeg de volgende artikelen voor meer informatie over het configureren van uw eigen DNS-server voor de ondersteuning van persoonlijke eind punten:
- [Naamomzetting voor resources in virtuele Azure-netwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-configuratie voor privé-eind punten](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Persoonlijke koppeling instellen voor Azure Data Factory
U kunt persoonlijke eind punten maken met behulp van [de Azure Portal](../private-link/create-private-endpoint-portal.md).

U kunt kiezen of u uw zelf-hostende Integration runtime wilt verbinden met Azure Data Factory via een openbaar eind punt of een persoonlijk eind punt. 

![Scherm afbeelding van het blok keren van open bare toegang van zelf-hostende Integration Runtime.](./media/data-factory-private-link/disable-public-access-shir.png)


U kunt ook naar uw Azure data factory gaan in de Azure Portal en een persoonlijk eind punt maken, zoals hier wordt weer gegeven:

![Scherm opname van het deel venster "persoonlijke eindpunt verbindingen" voor het maken van een persoonlijk eind punt.](./media/data-factory-private-link/create-private-endpoint.png)

Selecteer in de stap van de resource **micro soft. DataFactory/fabrieken** als **resource type**. En als u een persoonlijk eind punt wilt maken voor de opdracht communicatie tussen de zelf-hostende Integration runtime en de Azure Data Factory-Service, selecteert u **DataFactory** als **doel-subresource**.

![Scherm opname van het deel venster "persoonlijke eindpunt verbindingen" voor het selecteren van een resource.](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> Het uitschakelen van open bare netwerk toegang is alleen van toepassing op de zelf-hostende Integration runtime, niet op Azure Integration Runtime en SQL Server Integration Services (SSIS) Integration Runtime.

Als u een persoonlijk eind punt wilt maken voor het ontwerpen en bewaken van de data factory in uw virtuele netwerk, selecteert u **Portal** als **doel-subresource**.

> [!NOTE]
> U hebt nog steeds toegang tot de Azure Data Factory portal via een openbaar netwerk nadat u een persoonlijk eind punt voor de portal hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen

- [Een data factory maken met behulp van de Azure Data Factory gebruikers interface](quickstart-create-data-factory-portal.md)
- [Inleiding tot Azure Data Factory](introduction.md)
- [Visueel ontwerpen in Azure Data Factory](author-visually.md)