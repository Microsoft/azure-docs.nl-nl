---
title: Beheerd virtueel netwerk & privé-eindpunten
description: Meer informatie over beheerd virtueel netwerk en beheerde privé-eindpunten in Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: d777588f0abdd1f771deb259c597f6407e61d874
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364602"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory Managed Virtual Network (preview)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden beheerde Virtual Network en beheerde privé-eindpunten in Azure Data Factory.


## <a name="managed-virtual-network"></a>Beheerd virtueel netwerk

Wanneer u een Azure Integration Runtime (IR) maakt in Azure Data Factory Managed Virtual Network (VNET), wordt de integratieruntime ingericht met de beheerde Virtual Network en worden privé-eindpunten gebruikt om veilig verbinding te maken met ondersteunde gegevensopslag. 

Door een Azure IR binnen beheerde Virtual Network zorgt u ervoor dat het gegevensintegratieproces geïsoleerd en veilig is. 

Voordelen van het gebruik van beheerde Virtual Network:

- Met een beheerde Virtual Network kunt u de werklast van het beheer van de Virtual Network naar Azure Data Factory. U hoeft geen subnet te maken voor Azure Integration Runtime die uiteindelijk veel privé-IP's van uw Virtual Network en waarvoor vooraf planning van de netwerkinfrastructuur nodig is. 
- Voor een veilige integratie van gegevens is geen diepgaande kennis van Azure-netwerken vereist. In plaats daarvan is het veel eenvoudiger om aan de slag te gaan met beveiligde ETL voor data engineers. 
- Beheerde Virtual Network samen met beheerde privé-eindpunten beschermt tegen gegevens exfiltratie. 

> [!IMPORTANT]
>Op dit moment wordt het beheerde VNet alleen ondersteund in dezelfde regio als Azure Data Factory regio.
 

![ADF Managed Virtual Network-architectuur](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Beheerde privé-eindpunten

Beheerde privé-eindpunten zijn privé-eindpunten die zijn gemaakt in de Azure Data Factory Managed Virtual Network tot stand brengen van een privékoppeling naar Azure-resources. Deze privé-eindpunten worden namens u beheerd in Azure Data Factory. 

![Nieuw Beheerd privé-eindpunt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory ondersteunt privékoppelingen. Met Private Link hebt u toegang tot Azure(PaaS)-services (zoals Azure Storage, Azure Cosmos DB, Azure Synapse Analytics).

Wanneer u een privékoppeling gebruikt, loopt het verkeer tussen uw gegevensopslag en beheerde Virtual Network volledig via het backbone-netwerk van Microsoft. Private Link beschermt tegen exfiltratie van gegevens. U kunt een privé-koppeling naar een resource tot stand brengen door een privé-eindpunt te maken.

Privé-eindpunt gebruikt een privé-IP-adres in de Virtual Network om de service er effectief in te zetten. Privé-eindpunten worden toegewezen aan een specifieke resource in Azure, en niet de volledige service. Klanten kunnen de connectiviteit beperken tot een specifieke resource die is goedgekeurd door hun organisatie. Meer informatie over [privé-koppelingen en privé-eindpunten](../private-link/index.yml).

> [!NOTE]
> Het is raadzaam om beheerde privé-eindpunten te maken om verbinding te maken met al uw Azure-gegevensbronnen. 
 
> [!WARNING]
> Als een PaaS-gegevensopslag (Blob, ADLS Gen2, Azure Synapse Analytics) al een privé-eindpunt op basis van het gegevensopslag heeft en zelfs als het toegang vanuit alle netwerken toestaat, zou ADF er alleen toegang toe kunnen krijgen via een beheerd privé-eindpunt. Zorg ervoor dat u in dergelijke scenario's een privé-eindpunt maakt. 

Een privé-eindpuntverbinding wordt gemaakt met de status In behandeling wanneer u een beheerd privé-eindpunt maakt in Azure Data Factory. Er wordt een goedkeuringswerkstroom geïnitieerd. De eigenaar van de privékoppelingsresource is verantwoordelijk voor het goedkeuren of afwijzen van de verbinding.

![Privé-eindpunt beheren](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Als de eigenaar de verbinding goedkeurt, wordt de privé-koppeling tot stand gebracht. Anders wordt de privé-koppeling niet tot stand gebracht. In beide gevallen wordt het beheerde privé-eindpunt bijgewerkt met de status van de verbinding.

![Beheerd privé-eindpunt goedkeuren](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Alleen een beheerd privé-eindpunt met een goedgekeurde status kan verkeer verzenden naar een gegeven privé-koppelingsresource.

## <a name="interactive-authoring"></a>Interactief schrijven
Interactieve ontwerpmogelijkheden worden gebruikt voor functies zoals een testverbinding, bladeren in mappenlijst en tabellijst, schema op halen en voorbeeldgegevens bekijken. U kunt interactief schrijven inschakelen bij het maken of bewerken van een Azure Integration Runtime in een door ADF beheerd virtueel netwerk. De back-endservice wijst vooraf rekenkracht toe voor interactieve ontwerpfunctionaliteiten. Anders wordt de berekening elke keer toegewezen als een interactieve bewerking wordt uitgevoerd, wat meer tijd in neemt. De Time To Live (TTL) voor interactief schrijven is 60 minuten, wat betekent dat deze automatisch wordt uitgeschakeld na 60 minuten van de laatste interactieve bewerking.

![Interactieve creatie](./media/managed-vnet/interactive-authoring.png)

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Beheerd virtueel netwerk maken via Azure PowerShell
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}"

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen
### <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen
Onderstaande gegevensbronnen worden ondersteund om verbinding te maken via private link vanuit ADF Managed Virtual Network.
- Azure Blob Storage
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (inclusief Azure SQL Managed Instance)
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Azure Private Link Service
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory Managed Virtual Network is beschikbaar in de volgende Azure-regio's:
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
- Noorwegen - oost
- Japan - oost
- Japan - west
- Korea - centraal
- Brazilië - zuid
- Frankrijk - centraal
- Zwitserland - noord
- Verenigd Koninkrijk West
- Canada - oost
- Canada - midden

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Uitgaande communicatie via een openbaar eindpunt van ADF Managed Virtual Network
- Alleen poort 443 is geopend voor uitgaande communicatie.
- Azure Storage en Azure Data Lake Gen2 worden niet ondersteund om te worden verbonden via een openbaar eindpunt vanuit door ADF beheerd Virtual Network.

### <a name="linked-service-creation-of-azure-key-vault"></a>Gekoppelde service voor het maken van Azure Key Vault 
- Wanneer u een gekoppelde service voor Azure Key Vault maakt, is er geen Azure Integration Runtime-verwijzing. U kunt dus geen privé-eindpunt maken tijdens het maken van een gekoppelde service Azure Key Vault. Maar wanneer u een gekoppelde service maakt voor gegevensopslag die verwijst naar de gekoppelde Azure Key Vault-service en deze gekoppelde service Azure Integration Runtime waarvoor Beheerde Virtual Network is ingeschakeld, kunt u tijdens het maken een privé-eindpunt maken voor de gekoppelde Azure Key Vault-service. 
- **Test de** verbindingsbewerking voor de gekoppelde Azure Key Vault valideert alleen de URL-indeling, maar er wordt geen netwerkbewerking uitgevoerd.
- De kolom **Privé-eindpunt gebruiken** wordt altijd als leeg weergegeven, zelfs als u een privé-eindpunt maakt voor Azure Key Vault.
![Privé-eindpunt voor AKV](./media/managed-vnet/akv-pe.png)

## <a name="next-steps"></a>Volgende stappen

- Zelfstudie: [Een kopieerpijplijn bouwen met beheerde Virtual Network en privé-eindpunten](tutorial-copy-data-portal-private.md) 
- Zelfstudie: [Een toewijzingsgegevensstroompijplijn bouwen met beheerde Virtual Network en privé-eindpunten](tutorial-data-flow-private.md)