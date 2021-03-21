---
title: Voorbereiden van opmaak wijziging in Azure Monitor bron logboeken
description: Azure-resource logboeken die zijn verplaatst voor het gebruik van toevoeg-blobs op 1 november 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.openlocfilehash: d4be3983d70a1ca78d849e231b8cc55e2b5895d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033194"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Voorbereiden van opmaak wijziging in Azure Monitor platform-logboeken die zijn gearchiveerd in een opslag account

> [!WARNING]
> Als u Azure- [resource Logboeken of-metrische gegevens naar een opslag account verzendt met Diagnostische instellingen](./resource-logs.md#send-to-azure-storage) of [activiteiten logboeken naar een opslag account met behulp van logboek profielen](./resource-logs.md#send-to-azure-storage), wordt de indeling van de informatie in het opslag account gewijzigd in JSON-regels op november. 1, 2018. De onderstaande instructies beschrijven de impact en het bijwerken van uw hulp programma voor het afhandelen van de nieuwe indeling.
>

## <a name="what-changed"></a>Wat is er gewijzigd

Azure Monitor biedt een mogelijkheid waarmee u bron logboeken en activiteiten Logboeken kunt verzenden naar een Azure-opslag account, Event Hubs naam ruimte of in een Log Analytics-werk ruimte in Azure Monitor. Als u een probleem met de systeem prestaties wilt aanpakken, op **1 November 2018 om 12:00 MIDDERNACHT UTC** de indeling van logboek gegevens verzenden naar Blob-opslag is gewijzigd. Als u hulp nodig hebt bij het lezen van gegevens uit de Blob-opslag, moet u uw hulp programma bijwerken om inzicht te krijgen in de nieuwe gegevens indeling.

* Op donderdag 1 november 2018 om 12:00 middernacht UTC is de BLOB-indeling gewijzigd in JSON- [lijnen](http://jsonlines.org/). Dit betekent dat elke record wordt gescheiden door een nieuwe regel, zonder een matrix van buitenste records en geen komma's tussen JSON-records.
* De BLOB-indeling is gewijzigd voor alle diagnostische instellingen in alle abonnementen tegelijk. De eerste PT1H.jsvoor het bestand dat is verzonden voor 1 november, heeft deze nieuwe indeling gebruikt. De namen van de BLOB en de container blijven hetzelfde.
* Als u een diagnostische instelling instelt tussen vóór 1 november, blijven gegevens in de huidige indeling verzenden tot 1 november.
* Deze wijziging is in alle open bare Cloud regio's in één keer opgetreden. De wijziging wordt niet doorgevoerd in Microsoft Azure die door 21Vianet, Azure Duitsland of Azure Government Clouds worden gebruikt.
* Deze wijziging is van invloed op de volgende gegevens typen:
  * [Azure-resource logboeken](./resource-logs.md#send-to-azure-storage) ([Zie de lijst met resources hier](./resource-logs-schema.md))
  * [Metrische gegevens van Azure-resource die worden geëxporteerd door Diagnostische instellingen](../essentials/diagnostic-settings.md)
  * [Gegevens van Azure-activiteiten logboek worden geëxporteerd op logboek profielen](./activity-log.md)
* Deze wijziging heeft geen invloed op:
  * Netwerk stroom logboeken
  * Azure-service logboeken zijn nog niet via Azure Monitor beschikbaar gemaakt (bijvoorbeeld Azure App Service Resource logboeken, logboeken voor opslag analyse)
  * Route ring van Azure-resource logboeken en activiteiten logboeken naar andere doelen (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Controleren of er gevolgen voor u hebben

U hebt deze wijziging alleen van invloed op de volgende wijzigingen:
1. Verzendt logboek gegevens naar een Azure-opslag account met behulp van een diagnostische instelling en
2. Laat hulp middelen afhangen van de JSON-structuur van deze logboeken in de opslag.
 
Als u Diagnostische instellingen voor het verzenden van gegevens naar een Azure-opslag account hebt, kunt u naar de sectie **monitor** van de portal gaan, op **Diagnostische instellingen** klikken en alle resources identificeren waarvoor de **Diagnostische status** is ingesteld op **ingeschakeld**:

![Blade Diagnostische instellingen Azure Monitor](media/resource-logs-blob-format/portal-diag-settings.png)

Als de status van de diagnose is ingesteld op ingeschakeld, hebt u een actieve diagnostische instelling voor die bron. Klik op de resource om te zien of er Diagnostische instellingen zijn die gegevens verzenden naar een opslag account:

![Opslag account ingeschakeld](media/resource-logs-blob-format/portal-storage-enabled.png)

Als u resources hebt die gegevens verzenden naar een opslag account met behulp van deze diagnostische instellingen voor de resource, wordt de indeling van de gegevens in dat opslag account beïnvloed door deze wijziging. Tenzij u aangepaste hulp middelen hebt die van deze opslag accounts worden uitgevoerd, heeft dit geen invloed op de indelings wijziging.

### <a name="details-of-the-format-change"></a>Details van de indelings wijziging

De huidige indeling van de PT1H.jsvoor het bestand in Azure Blob Storage maakt gebruik van een JSON-matrix van records. Hier volgt een voor beeld van een logboek bestand voor de sleutel kluis:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

De nieuwe indeling maakt gebruik van [JSON-lijnen](http://jsonlines.org/), waarbij elke gebeurtenis een regel is en het teken voor nieuwe regel geeft aan dat er een gebeurtenis is. Hier ziet u hoe het bovenstaande voor beeld eruitziet in de PT1H.jsin het bestand na de wijziging:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Met deze nieuwe indeling Azure Monitor kunt u logboek bestanden pushen met behulp van [toevoeg-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Dit is efficiënter voor het voortdurend toevoegen van nieuwe gebeurtenis gegevens.

## <a name="how-to-update"></a>Update

U hoeft alleen updates te maken als u aangepaste hulp middelen hebt waarmee deze logboek bestanden worden opgenomen voor verdere verwerking. Als u een externe log Analytics-of SIEM-hulp programma gebruikt, raden we [u aan Event hubs te gebruiken om in plaats daarvan deze gegevens op te](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)nemen. De integratie van Event hubs is gemakkelijker voor het verwerken van logboeken van veel services en de locatie van de blad wijzers in een bepaald logboek.

Aangepaste hulpprogram ma's moeten worden bijgewerkt om zowel de huidige indeling als de indeling van de JSON-lijnen te verwerken die hierboven wordt beschreven. Dit zorgt ervoor dat de hulpprogram ma's niet worden onderbroken wanneer gegevens worden weer gegeven in de nieuwe indeling.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het archiveren van resource bron Logboeken in een opslag account](./resource-logs.md#send-to-azure-storage)
* Meer informatie over [het archiveren van activiteiten logboek gegevens naar een opslag account](./activity-log.md#legacy-collection-methods)
