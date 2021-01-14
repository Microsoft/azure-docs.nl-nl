---
title: Door de klant beheerde opslagaccounts gebruiken in Azure Monitor Log Analytics
description: Uw eigen opslag account gebruiken voor Log Analytics scenario's
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 706392d95e371fe303bb9f2c18f59e4a224d83c0
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201056"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Door de klant beheerde opslagaccounts gebruiken in Azure Monitor Log Analytics

Log Analytics is afhankelijk van Azure Storage in verschillende scenario's. Dit gebruik wordt doorgaans automatisch beheerd. In sommige gevallen moet u echter uw eigen opslag account opgeven en beheren, ook wel een door de klant beheerd opslag account genoemd. Dit document bevat informatie over het gebruik van door de klant beheerde opslag voor WAD/LAD-logboeken, persoonlijke koppelingen en versleuteling van door de klant beheerde sleutel (CMK). 

> [!NOTE]
> U wordt aangeraden geen afhankelijkheid te nemen van de inhoud Log Analytics uploads naar door de klant beheerde opslag, op voorwaarde dat de opmaak en inhoud kan veranderen.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Azure Diagnostics extensie logboeken opnemen (WAD/LAD)
De Azure Diagnostics extensie agents (ook wel WAD-en LAD voor Windows-en Linux-agents genoemd) verzamelen verschillende besturings systeem logboeken en slaan ze op in een door de klant beheerd opslag account. U kunt deze logboeken vervolgens opnemen in Log Analytics om ze te bekijken en te analyseren.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Azure Diagnostics extensie logboeken van uw opslag account verzamelen
Verbind het opslag account met uw Log Analytics-werk ruimte als een opslag gegevens bron met behulp van [de Azure Portal](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) of door de [opslag Insights-API](/rest/api/loganalytics/storage%20insights/createorupdate)aan te roepen.

Ondersteunde gegevens typen:
* Syslog
* Windows-gebeurtenissen
* Service Fabric
* ETW-gebeurtenissen
* IIS-logboeken

## <a name="using-private-links"></a>Persoonlijke koppelingen gebruiken
Door de klant beheerde opslag accounts worden gebruikt om aangepaste Logboeken of IIS-logboeken op te nemen wanneer persoonlijke koppelingen worden gebruikt om verbinding te maken met Azure Monitor-resources. Het opname proces van deze gegevens typen uploadt eerst logboeken naar een tussenliggend Azure Storage account en vervolgens worden ze alleen opgenomen in een werk ruimte. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Een door de klant beheerd opslag account gebruiken via een persoonlijke koppeling
#### <a name="workspace-requirements"></a>Werkruimte vereisten
Wanneer u verbinding maakt met Azure Monitor via een persoonlijke koppeling, kunnen Log Analytics-agents alleen logboeken verzenden naar werk ruimten die toegankelijk zijn via een privé-koppeling. Dit betekent dat u het volgende moet doen:
* Een Azure Monitor-object (AMPLS private link scope) configureren
* Verbinding maken met uw werk ruimten
* Verbind de AMPLS met uw netwerk via een persoonlijke koppeling. 

Zie voor meer informatie over de AMPLS-configuratie procedure [Azure private link gebruiken om een beveiligde verbinding te maken tussen netwerken en Azure monitor](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount
Het opslag account kan alleen verbinding maken met uw persoonlijke koppeling als:
* Bevinden zich op uw VNet of een peered netwerk en is verbonden met uw VNet via een persoonlijke koppeling.
* Bevinden zich in dezelfde regio als de werk ruimte waaraan deze is gekoppeld.
* Azure Monitor toegang tot het opslag account toestaan. Als u ervoor hebt gekozen om alleen netwerken te selecteren voor toegang tot uw opslag account, moet u de uitzonde ring: ' vertrouwde micro soft-Services toestaan voor toegang tot dit opslag account ' selecteren.
![Afbeelding van de MS-Service vertrouwens relatie van het opslag account](./media/private-storage/storage-trust.png)
* Als uw werk ruimte ook verkeer afhandelt vanuit andere netwerken, moet u het opslag account configureren om binnenkomend verkeer van de relevante netwerken/internet toe te staan.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Een door de klant beheerd opslag account gebruiken voor CMK-gegevens versleuteling
Azure Storage versleutelt alle gegevens in rust in een opslag account. Standaard maakt deze gebruik van door micro soft beheerde sleutels (MMK) om de gegevens te versleutelen. Met Azure Storage kunt u echter ook CMK van Azure sleutel kluis gebruiken om uw opslag gegevens te versleutelen. U kunt uw eigen sleutels importeren in Azure Key Vault, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>CMK-scenario's waarvoor een door de klant beheerd opslag account is vereist
* Logboek-waarschuwings query's versleutelen met CMK
* Opgeslagen query's versleutelen met CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>CMK Toep assen op door de klant beheerde opslag accounts
##### <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount
Het opslag account en de sleutel kluis moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Azure Storage versleuteling voor Data-at-rest](../../storage/common/storage-service-encryption.md)voor meer informatie over Azure Storage versleuteling en sleutel beheer.

##### <a name="apply-cmk-to-your-storage-accounts"></a>CMK Toep assen op uw opslag accounts
Als u uw Azure Storage-account wilt configureren voor het gebruik van CMK met Azure Key Vault, gebruikt u de [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [Power shell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)of de [cli](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Opslag accounts koppelen aan uw Log Analytics-werk ruimte
### <a name="using-the-azure-portal"></a>Azure Portal gebruiken
Open in het Azure Portal menu van de werk ruimte en selecteer *gekoppelde opslag accounts*. Er wordt een Blade geopend waarin de gekoppelde opslag accounts worden weer gegeven op basis van de hierboven genoemde gebruiks voorbeelden (opname via een privé-koppeling, het Toep assen van CMK op opgeslagen query's of op waarschuwingen).
![Afbeelding van de Blade gekoppelde opslag accounts ](./media/private-storage/all-linked-storage-accounts.png) Als u een item in de tabel selecteert, worden de gegevens van het opslag account geopend, waar u het gekoppelde opslag account voor dit type kunt instellen of bijwerken. 
![Een installatie kopie van een opslag account koppelen ](./media/private-storage/link-a-storage-account-blade.png) u kunt hetzelfde account gebruiken voor verschillende use cases als u dat wilt.

### <a name="using-the-azure-cli-or-rest-api"></a>Azure CLI of REST API gebruiken
U kunt ook een opslag account aan uw werk ruimte koppelen via de [Azure cli](/cli/azure/monitor/log-analytics/workspace/linked-storage) of [rest API](/rest/api/loganalytics/linkedstorageaccounts).

De toepasselijke data source type-waarden zijn:
* CustomLogs: het opslag account voor aangepaste logboeken en IIS-logboek opname gebruiken
* Query-om het opslag account te gebruiken voor het opslaan van opgeslagen query's (vereist voor CMK-versleuteling)
* Waarschuwingen: gebruik het opslag account voor het opslaan van waarschuwingen op basis van Logboeken (vereist voor CMK-versleuteling)


## <a name="managing-linked-storage-accounts"></a>Gekoppelde opslag accounts beheren

### <a name="create-or-modify-a-link"></a>Een koppeling maken of wijzigen
Wanneer u een opslag account koppelt aan een werk ruimte, wordt Log Analytics gebruikt in plaats van het opslag account dat eigendom is van de service. U kunt 
* Meerdere opslag accounts registreren om de belasting van Logboeken ertussen te verdelen
* Hetzelfde opslag account voor meerdere werk ruimten opnieuw gebruiken

### <a name="unlink-a-storage-account"></a>Een opslag account ontkoppelen
Als u een opslag account niet meer wilt gebruiken, ontkoppelt u de opslag van de werk ruimte. Bij het ontkoppelen van alle opslag accounts van een werk ruimte betekent Log Analytics dat er wordt geprobeerd te vertrouwen op door service beheerde opslag accounts. Als uw netwerk beperkte toegang tot het internet heeft, zijn deze opslag mogelijk niet beschikbaar en zullen scenario's die afhankelijk zijn van opslag, mislukken.

### <a name="replace-a-storage-account"></a>Een opslag account vervangen
Als u een opslag account wilt vervangen dat wordt gebruikt voor opname,
1.  **Maak een koppeling naar een nieuw opslag account.** De logboek registratie agenten ontvangen de bijgewerkte configuratie en beginnen ook met het verzenden van gegevens naar de nieuwe opslag. Het proces kan enkele minuten duren.
2.  **Koppel vervolgens het oude opslag account ongedaan zodat agents het wegschrijven naar het verwijderde account worden gestopt.** Het opname proces bewaart gegevens van dit account totdat het is opgenomen. Verwijder het opslag account pas wanneer u ziet dat alle logboeken zijn opgenomen.

### <a name="maintaining-storage-accounts"></a>Opslag accounts onderhouden
#### <a name="manage-log-retention"></a>Bewaren van logboeken beheren
Wanneer u uw eigen opslag account gebruikt, is de Bewaar periode. Log Analytics verwijdert geen logboeken die zijn opgeslagen in uw privé-opslag. In plaats daarvan moet u een beleid instellen om de belasting op basis van uw voor keuren af te handelen.

#### <a name="consider-load"></a>Overweeg load
Opslag accounts kunnen een bepaalde werk belasting voor lees-en schrijf aanvragen verwerken voordat ze aanvragen voor het beperken (Zie [schaal baarheid en prestatie doelen voor Blob Storage](../../storage/common/scalability-targets-standard-account.md)) voor meer informatie. Beperking is van invloed op de tijd die nodig is voor opname van Logboeken. Als uw opslag account is overbelast, registreert u een extra opslag account om de belasting ertussen te spreiden. Als u de capaciteit en prestaties van uw opslag account wilt controleren, bekijkt u de [inzichten in de Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Gerelateerde kosten
Opslag accounts worden in rekening gebracht op basis van het volume van de opgeslagen gegevens, het type opslag en het type redundantie. Zie prijzen voor [blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs) en [Table Storage prijzen](https://azure.microsoft.com/pricing/details/storage/tables)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het gebruik van een persoonlijke Azure-koppeling voor het veilig verbinden van netwerken met Azure monitor](private-link-security.md)
- Meer informatie over Azure Monitor door de [klant beheerde sleutels](customer-managed-keys.md)
