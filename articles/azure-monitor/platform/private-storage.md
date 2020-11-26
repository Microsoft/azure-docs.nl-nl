---
title: Door de klant beheerde opslag accounts gebruiken in Azure Monitor Log Analytics
description: Uw eigen opslag account gebruiken voor Log Analytics scenario's
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: f424a2c3102f7b270a64c612a91d645ab71461fc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184090"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Door de klant beheerde opslag accounts gebruiken in Azure Monitor Log Analytics

Log Analytics is afhankelijk van Azure Storage in verschillende scenario's. Dit gebruik wordt doorgaans automatisch beheerd. In sommige gevallen moet u echter uw eigen opslag account opgeven en beheren, ook wel een door de klant beheerd opslag account genoemd. Dit document bevat informatie over het gebruik van door de klant beheerde opslag voor de opname van WAD/LAD-logboeken, specifieke scenario's voor persoonlijke koppelingen en versleuteling van door de klant beheerde sleutel (CMK). 

> [!NOTE]
> U wordt aangeraden geen afhankelijkheid te nemen van de inhoud Log Analytics uploads naar door de klant beheerde opslag, op voorwaarde dat de opmaak en inhoud kan veranderen.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Azure Diagnostics extensie logboeken opnemen (WAD/LAD)
De Azure Diagnostics extensie agents (ook wel WAD-en LAD voor Windows-en Linux-agents genoemd) verzamelen verschillende besturings systeem logboeken en slaan ze op in een door de klant beheerd opslag account. U kunt deze logboeken vervolgens opnemen in Log Analytics om ze te bekijken en te analyseren.
Azure Diagnostics extensie logboeken van uw opslag account verzamelen sluit het opslag account aan uw Log Analytics-werk ruimte als een opslag gegevens bron met behulp van [de Azure Portal](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) of door de [opslag Insights-API](/rest/api/loganalytics/connectedsources/storage%20insights/createorupdate)aan te roepen.

Ondersteunde gegevens typen:
* Syslog
* Windows-gebeurtenissen
* Service Fabric
* ETW-gebeurtenissen
* IIS-logboeken

## <a name="using-private-links"></a>Persoonlijke koppelingen gebruiken
In sommige gevallen zijn door klanten beheerde opslag accounts vereist, wanneer persoonlijke koppelingen worden gebruikt om verbinding te maken met Azure Monitor-resources. Een dergelijk geval is de opname van aangepaste Logboeken of IIS-logboeken. Deze gegevens typen worden eerst als blobs geüpload naar een tussenliggend Azure Storage account en worden alleen opgenomen in een werk ruimte. Zo kunnen sommige Azure Monitor oplossingen gebruikmaken van opslag accounts voor het opslaan van grote bestanden, zoals Watson-dump bestanden, die worden gebruikt door de Azure Security Center-oplossing. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Scenario's voor persoonlijke koppelingen waarvoor een door de klant beheerde opslag is vereist
* Opname van aangepaste logboeken en IIS-logboeken
* Een ASC-oplossing toestaan om Watson-dump bestanden te verzamelen

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Een door de klant beheerd opslag account gebruiken via een persoonlijke koppeling
##### <a name="workspace-requirements"></a>Werkruimte vereisten
Wanneer u verbinding maakt met Azure Monitor via een persoonlijke koppeling, kunnen Log Analytics-agents alleen logboeken verzenden naar werk ruimten die via een persoonlijke koppeling aan uw netwerk zijn gekoppeld. Voor deze regel moet u een Azure Monitor-object voor een persoonlijk koppelings bereik (AMPLS) goed configureren, het verbinden met uw werk ruimten en de AMPLS vervolgens met uw netwerk verbinden via een privé-koppeling. Zie voor meer informatie over de AMPLS-configuratie procedure [Azure private link gebruiken om een beveiligde verbinding te maken tussen netwerken en Azure monitor](./private-link-security.md). 
##### <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount
Het opslag account kan alleen verbinding maken met uw persoonlijke koppeling als:
* Bevinden zich op uw VNet of een peered netwerk en is verbonden met uw VNet via een persoonlijke koppeling. Hiermee kunnen agents op uw VNet logboeken naar het opslag account verzenden.
* Bevinden zich in dezelfde regio als de werk ruimte waaraan deze is gekoppeld.
* Azure Monitor toegang tot het opslag account toestaan. Als u ervoor hebt gekozen om alleen netwerken te selecteren voor toegang tot uw opslag account, moet u ook de volgende uitzonde ring toestaan: ' vertrouwde micro soft-Services toegang geven tot dit opslag account '. Hiermee kan Log Analytics de logboeken lezen die zijn opgenomen in dit opslag account.
* Als uw werk ruimte ook verkeer afhandelt vanuit andere netwerken, moet u het opslag account configureren om binnenkomend verkeer van de relevante netwerken/internet toe te staan.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>Uw opslag account koppelen aan een Log Analytics-werk ruimte
U kunt uw opslag account aan de werk ruimte koppelen via de [Azure cli](/cli/azure/monitor/log-analytics/workspace/linked-storage) of [rest API](/rest/api/loganalytics/linkedstorageaccounts). Toepasselijke data source type-waarden:
* CustomLogs: voor het gebruik van de opslag voor aangepaste logboeken en IIS-logboeken tijdens opname.
* AzureWatson: gebruik de opslag voor Watson-dump bestanden die zijn geüpload door de ASC (Azure Security Center)-oplossing. Zie voor meer informatie over het beheren van de Bewaar periode van een gekoppeld opslag account en het controleren van de activiteit van uw opslag account [beheer van gekoppelde opslag accounts](#managing-linked-storage-accounts). 

## <a name="encrypting-data-with-cmk"></a>Gegevens versleutelen met CMK
Azure Storage versleutelt alle gegevens in rust in een opslag account. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels (MMK). In plaats daarvan kunt u met Azure Storage echter een door de klant beheerde sleutel (CMK) van de Azure-sleutel kluis gebruiken om uw opslag gegevens te versleutelen. U kunt uw eigen sleutels importeren in Azure Key Vault, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>CMK-scenario's waarvoor een door de klant beheerd opslag account is vereist
* Logboek-waarschuwings query's versleutelen met CMK
* Opgeslagen query's versleutelen met CMK

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>CMK Toep assen op door de klant beheerde opslag accounts
##### <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount
Het opslag account en de sleutel kluis moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Azure Storage versleuteling voor Data-at-rest](../../storage/common/storage-service-encryption.md)voor meer informatie over Azure Storage versleuteling en sleutel beheer.

##### <a name="apply-cmk-to-your-storage-accounts"></a>CMK Toep assen op uw opslag accounts
Als u uw Azure Storage-account wilt configureren voor het gebruik van door de klant beheerde sleutels met Azure Key Vault, gebruikt u de [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Power shell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) of de [cli](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="managing-linked-storage-accounts"></a>Gekoppelde opslag accounts beheren

Als u opslag accounts wilt koppelen of ontkoppelen naar uw werk ruimte, gebruikt u de [Azure cli](/cli/azure/monitor/log-analytics/workspace/linked-storage) of de [rest API](/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Een koppeling maken of wijzigen
Wanneer u een opslag account koppelt aan een werk ruimte, wordt Log Analytics gebruikt in plaats van het opslag account dat eigendom is van de service. U kunt 
* Meerdere opslag accounts registreren om de belasting van Logboeken ertussen te verdelen
* Hetzelfde opslag account voor meerdere werk ruimten opnieuw gebruiken

##### <a name="unlink-a-storage-account"></a>Een opslag account ontkoppelen
Als u een opslag account niet meer wilt gebruiken, ontkoppelt u de opslag van de werk ruimte. Bij het ontkoppelen van alle opslag accounts van een werk ruimte betekent Log Analytics dat er wordt geprobeerd te vertrouwen op door service beheerde opslag accounts. Als uw netwerk beperkte toegang tot het internet heeft, zijn deze opslag mogelijk niet beschikbaar en zullen scenario's die afhankelijk zijn van opslag, mislukken.

##### <a name="replace-a-storage-account"></a>Een opslag account vervangen
Als u een opslag account wilt vervangen dat wordt gebruikt voor opname,
1.  **Maak een koppeling naar een nieuw opslag account.** De logboek registratie agenten ontvangen de bijgewerkte configuratie en beginnen ook met het verzenden van gegevens naar de nieuwe opslag. Het proces kan enkele minuten duren.
2.  **Koppel vervolgens het oude opslag account ongedaan zodat agents het wegschrijven naar het verwijderde account worden gestopt.** Het opname proces bewaart gegevens van dit account totdat het is opgenomen. Verwijder het opslag account pas wanneer u ziet dat alle logboeken zijn opgenomen.

### <a name="maintaining-storage-accounts"></a>Opslag accounts onderhouden
##### <a name="manage-log-retention"></a>Bewaren van logboeken beheren
Wanneer u uw eigen opslag account gebruikt, is de Bewaar periode. Met andere woorden, Log Analytics verwijdert geen logboeken die zijn opgeslagen in uw privé-opslag. In plaats daarvan moet u een beleid instellen om de belasting op basis van uw voor keuren af te handelen.

##### <a name="consider-load"></a>Overweeg load
Opslag accounts kunnen een bepaalde belasting van lees-en schrijf aanvragen afhandelen voordat ze aanvragen voor het beperken (Zie [schaal baarheid en prestatie doelen voor Blob Storage](../../storage/common/scalability-targets-standard-account.md) voor meer informatie). Beperking is van invloed op de tijd die nodig is voor opname van Logboeken. Als uw opslag account is overbelast, registreert u een extra opslag account om de belasting ertussen te spreiden. Als u de capaciteit en prestaties van uw opslag account wilt controleren, bekijkt u de [inzichten in de Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Gerelateerde kosten
Opslag accounts worden in rekening gebracht op basis van het volume van de opgeslagen gegevens, het type opslag en het type redundantie. Zie prijzen voor [blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs) en [Table Storage prijzen](https://azure.microsoft.com/pricing/details/storage/tables)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het gebruik van een persoonlijke Azure-koppeling voor het veilig verbinden van netwerken met Azure monitor](private-link-security.md)
- Meer informatie over Azure Monitor door de [klant beheerde sleutels](customer-managed-keys.md)
