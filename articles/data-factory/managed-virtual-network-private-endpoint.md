---
title: Beheerd virtueel netwerk & beheerde privé-eind punten
description: Meer informatie over beheerde virtuele netwerken en beheerde privé-eind punten in Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: b6000d8ff3eb35d678a94adc021efcadf8a77f81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699625"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Beheerde Virtual Network Azure Data Factory (preview-versie)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden beheerde Virtual Network en beheerde privé-eind punten in Azure Data Factory uitgelegd.


## <a name="managed-virtual-network"></a>Beheerd virtueel netwerk

Wanneer u een Azure Integration Runtime (IR) in Azure Data Factory Managed Virtual Network (VNET) maakt, wordt de Integration runtime ingericht met de beheerde Virtual Network en worden privé-eind punten gebruikt om een veilige verbinding te maken met ondersteunde gegevens archieven. 

Het maken van een Azure IR binnen beheerde Virtual Network zorgt ervoor dat het gegevens integratie proces wordt geïsoleerd en beveiligd. 

Voor delen van het gebruik van beheerde Virtual Network:

- Met een beheerde Virtual Network, kunt u de last van het beheren van de Virtual Network naar Azure Data Factory verenigen. U hoeft geen subnet te maken voor Azure Integration Runtime die uiteindelijk veel privé Ip's van uw Virtual Network kunnen gebruiken en hiervoor eerdere planning van de netwerk infrastructuur nodig had. 
- Er is geen grondige kennis van Azure Networking nodig om gegevens integraties veilig uit te voeren. In plaats daarvan is het uitvoeren van beveiligde ETL veel vereenvoudigd voor gegevens technici. 
- Beheerde Virtual Network worden samen met beheerde persoonlijke eind punten beschermd tegen gegevens exfiltration. 

> [!IMPORTANT]
>Op dit moment wordt het beheerde VNet alleen ondersteund in dezelfde regio als Azure Data Factory regio.
 

![Virtual Network architectuur voor gebeheerde ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Beheerde privé-eindpunten

Beheerde privé-eind punten zijn particuliere eind punten die zijn gemaakt in de Azure Data Factory beheerde Virtual Network een persoonlijke koppeling naar Azure-resources tot stand brengen. Deze privé-eindpunten worden namens u beheerd in Azure Data Factory. 

![Nieuw Beheerd privé-eindpunt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory ondersteunt persoonlijke koppelingen. Met persoonlijke koppeling kunt u Azure-Services (PaaS) gebruiken (zoals Azure Storage, Azure Cosmos DB, Azure Synapse Analytics).

Wanneer u een persoonlijke koppeling gebruikt, loopt het verkeer tussen uw gegevens archieven en beheerde Virtual Network volledig over het micro soft backbone-netwerk. Private Link beschermt tegen exfiltratie van gegevens. U kunt een privé-koppeling naar een resource tot stand brengen door een privé-eindpunt te maken.

Persoonlijk eind punt maakt gebruik van een privé-IP-adres in de beheerde Virtual Network om de service effectief in te zetten. Privé-eindpunten worden toegewezen aan een specifieke resource in Azure, en niet de volledige service. Klanten kunnen de connectiviteit beperken tot een specifieke resource die is goedgekeurd door hun organisatie. Meer informatie over [privé-koppelingen en privé-eindpunten](../private-link/index.yml).

> [!NOTE]
> Het wordt aanbevolen dat u beheerde persoonlijke eind punten maakt om verbinding te maken met al uw Azure-gegevens bronnen. 
 
> [!WARNING]
> Als er voor een PaaS-gegevens archief (BLOB, ADLS Gen2, Azure Synapse Analytics) al een persoonlijk eind punt is gemaakt, en zelfs als deze toegang toestaat vanuit alle netwerken, zou ADF alleen toegang kunnen krijgen met een beheerd privé-eind punt. Zorg ervoor dat u in dergelijke scenario's een persoonlijk eind punt maakt. 

Een VPN-verbinding wordt gemaakt met de status ' in behandeling ' wanneer u een beheerd privé-eind punt maakt in Azure Data Factory. Er wordt een goedkeuringswerkstroom geïnitieerd. De eigenaar van de privékoppelingsresource is verantwoordelijk voor het goedkeuren of afwijzen van de verbinding.

![Privé-eindpunt beheren](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Als de eigenaar de verbinding goedkeurt, wordt de privé-koppeling tot stand gebracht. Anders wordt de privé-koppeling niet tot stand gebracht. In beide gevallen wordt het beheerde privé-eindpunt bijgewerkt met de status van de verbinding.

![Beheerd privé-eind punt goed keuren](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Alleen een beheerd privé-eindpunt met een goedgekeurde status kan verkeer verzenden naar een gegeven privé-koppelingsresource.

## <a name="interactive-authoring"></a>Interactief ontwerpen
Interactieve ontwerp mogelijkheden worden gebruikt voor functies als test verbinding, bladeren in mappen lijst en tabel lijst, schema ophalen en voor beeld van gegevens. U kunt interactief ontwerpen inschakelen bij het maken of bewerken van een Azure Integration Runtime dat zich in het virtuele netwerk met ADF-beheer bevindt. De back-end-service wijst de reken kracht vooraf toe voor interactieve ontwerp functies. Anders wordt de berekening toegewezen telkens wanneer een interactieve bewerking wordt uitgevoerd, waardoor er meer tijd in beslag wordt genomen. De TTL (time to Live) voor interactieve ontwerpen is 60 minuten, wat betekent dat deze automatisch wordt uitgeschakeld na 60 minuten van de laatste interactieve ontwerp bewerking.

![Interactieve creatie](./media/managed-vnet/interactive-authoring.png)

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen
### <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen
De onderstaande gegevens bronnen worden ondersteund om verbinding te maken via een persoonlijke koppeling vanuit de Virtual Network van ADF die wordt beheerd.
- Azure Blob Storage
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (exclusief Azure SQL Managed instance)
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Persoonlijke koppelings service van Azure
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory beheerde Virtual Network is beschikbaar in de volgende Azure-regio's:
- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2
- South Central US
- Central US
- Europa - noord
- Europa -west
- Verenigd Koninkrijk Zuid
- Azië - zuidoost
- Australië - oost
- Australië - zuidoost

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Uitgaande communicatie via het open bare eind punt van de Virtual Network voor ADF beheerd
- Alleen poort 443 wordt geopend voor uitgaande communicatie.
- Azure Storage en Azure Data Lake Gen2 worden niet ondersteund om te worden verbonden via een openbaar eindpunt vanuit door ADF beheerd Virtual Network.

### <a name="linked-service-creation-of-azure-key-vault"></a>Het maken van een gekoppelde service van Azure Key Vault 
- Wanneer u een gekoppelde service voor Azure Key Vault maakt, is er geen Azure Integration Runtime-verwijzing. U kunt geen persoonlijk eind punt maken tijdens het maken van de gekoppelde service van Azure Key Vault. Maar wanneer u een gekoppelde service maakt voor gegevens archieven die verwijst naar Azure Key Vault gekoppelde service en deze gekoppelde service verwijst naar Azure Integration Runtime met beheerde Virtual Network ingeschakeld, kunt u tijdens het maken een persoonlijk eind punt voor de gekoppelde Azure Key Vault-service maken. 
- **Test verbindings** bewerking voor de gekoppelde Service van Azure Key Vault valideert alleen de URL-indeling, maar voert geen netwerk bewerking uit.
- De kolom **die gebruikmaakt van een persoonlijk eind punt** , wordt altijd weer gegeven als leeg, zelfs als u een persoonlijk eind punt maakt voor Azure Key Vault.
![Persoonlijk eind punt voor Azure](./media/managed-vnet/akv-pe.png)

## <a name="next-steps"></a>Volgende stappen

- Zelf studie: [een Kopieer pijplijn bouwen met beheerde Virtual Network en privé-eind punten](tutorial-copy-data-portal-private.md) 
- Zelf studie: een [pijp lijn voor toewijzings gegevensstroom maken met beheerde Virtual Network en privé-eind punten](tutorial-data-flow-private.md)