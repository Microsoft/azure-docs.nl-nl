---
title: Limieten en beperking aanvragen
description: Hierin wordt beschreven hoe u bandbreedte beperking gebruikt met Azure Resource Manager-aanvragen wanneer de abonnements limieten zijn bereikt.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: c1fd707bf9f13ed88bf9acb5d2ad70d56c453a90
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024622"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager-aanvragen beperken

In dit artikel wordt beschreven hoe Azure Resource Manager vertragings aanvragen opstuurt. U ziet hoe u het aantal aanvragen kunt bijhouden dat achterblijft voordat de limiet wordt bereikt en hoe u kunt reageren wanneer u de limiet hebt bereikt.

Beperking gebeurt op twee niveaus. Azure Resource Manager beperkt aanvragen voor het abonnement en de Tenant. Als de aanvraag wordt beperkt door de beperkings limieten voor het abonnement en de Tenant, stuurt Resource Manager de aanvraag door naar de resource provider. De resource provider past beperkings limieten toe die zijn afgestemd op de bewerkingen. In de volgende afbeelding ziet u hoe beperking wordt toegepast wanneer een aanvraag van de gebruiker naar Azure Resource Manager en de resource provider wordt verplaatst.

![Aanvraag beperking](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Abonnement-en Tenant limieten

Voor elke bewerking op abonnements niveau en op Tenant niveau gelden beperkingen voor het beperken van limieten. Er zijn abonnements aanvragen die moeten worden door gegeven aan uw abonnement-ID, zoals het ophalen van de resource groepen in uw abonnement. Tenant aanvragen bevatten niet uw abonnements-ID, zoals het ophalen van geldige Azure-locaties.

De standaard limieten voor beperking per uur worden weer gegeven in de volgende tabel.

| Bereik | Operations | Limiet |
| ----- | ---------- | ------- |
| Abonnement | titel | 12000 |
| Abonnement | Delete | 15.000 |
| Abonnement | schrijfopdrachten | 1200 |
| Tenant | titel | 12000 |
| Tenant | schrijfopdrachten | 1200 |

Deze limieten zijn van toepassing op de beveiligingsprincipal (gebruiker of toepassing) die de aanvragen doet en de abonnements-id of tenant-id. Als uw aanvragen afkomstig zijn van meerdere beveiligingsprincipals, is uw limiet voor het abonnement of de tenant groter dan 12.000 en 1200 per uur.

Deze limieten gelden voor elke Azure Resource Manager-instantie. Er zijn meerdere exemplaren in elke Azure-regio en Azure Resource Manager wordt geïmplementeerd in alle Azure-regio's.  In de praktijk zijn de limieten dus hoger dan deze limieten. De aanvragen van een gebruiker worden meestal verwerkt door verschillende exemplaren van Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limieten van resource provider

Resource providers passen hun eigen beperkings limieten toe. Omdat Resource Manager wordt beperkt door de principal-ID en het exemplaar van Resource Manager, kan de resource provider mogelijk meer aanvragen ontvangen dan de standaard limieten in de vorige sectie.

In deze sectie worden de beperkings limieten voor sommige veelgebruikte resource providers beschreven.

### <a name="storage-throttling"></a>Opslagbeperking

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Netwerkbeperking

De resource provider micro soft. Network heeft de volgende beperkings limieten:

| Bewerking | Limiet |
| --------- | ----- |
| schrijven/verwijderen (PUT) | 1000 per 5 minuten |
| lezen (GET) | 10000 per 5 minuten |

### <a name="compute-throttling"></a>Reken beperking

Zie [Troubleshooting API Throttle Errors-Compute](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors)(Engelstalig) voor meer informatie over het beperken van limieten voor reken bewerkingen.

Voor het controleren van exemplaren van virtuele machines in een schaalset voor virtuele machines gebruikt u de [Virtual Machine Scale sets bewerkingen](/rest/api/compute/virtualmachinescalesetvms). Gebruik bijvoorbeeld de [virtuele machine Scale set vm's: lijst](/rest/api/compute/virtualmachinescalesetvms/list) met para meters om de energie status van exemplaren van virtuele machines te controleren. Deze API vermindert het aantal aanvragen.

### <a name="azure-resource-graph-throttling"></a>Beperking van Azure-resource grafiek

[Azure-resource grafiek](../../governance/resource-graph/overview.md) beperkt het aantal aanvragen voor de bewerkingen. De stappen in dit artikel om de resterende aanvragen te bepalen en te reageren wanneer de limiet is bereikt, geldt ook voor de resource grafiek. Resource grafiek stelt echter een eigen limiet en opnieuw ingestelde frequentie in. Zie [resource Graph Throttle headers](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)(Engelstalig) voor meer informatie.

### <a name="other-resource-providers"></a>Andere resource providers

Zie voor informatie over het beperken van beperkingen in andere resource providers:

* [Richtlijnen voor beperkingen in Azure Key Vault](../../key-vault/general/overview-throttling.md)
* [AKS-problemen oplossen](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>Foutcode

Wanneer u de limiet bereikt, ontvangt u de HTTP-status code **429 te veel aanvragen**. Het antwoord bevat een **nieuwe waarde voor opnieuw proberen** , waarmee het aantal seconden wordt aangegeven dat de toepassing moet wachten (of slaap stand) voordat de volgende aanvraag wordt verzonden. Als u een aanvraag verzendt voordat de waarde voor opnieuw proberen is verstreken, wordt uw aanvraag niet verwerkt en wordt er een nieuwe waarde voor opnieuw proberen geretourneerd.

Nadat u het opgegeven tijdstip hebt gewacht, kunt u de verbinding met Azure ook sluiten en opnieuw openen. Door de verbinding opnieuw in te stellen, kunt u verbinding maken met een ander exemplaar van Azure Resource Manager.

Als u een Azure SDK gebruikt, kan de SDK een automatische configuratie voor opnieuw proberen hebben. Zie [richt lijnen voor opnieuw proberen voor Azure-Services](/azure/architecture/best-practices/retry-service-specific)voor meer informatie.

Sommige resource providers retour neren 429 om een tijdelijk probleem te melden. Het probleem kan een overbelasting voor waarde zijn die niet rechtstreeks wordt veroorzaakt door uw aanvraag. Het kan ook duiden op een tijdelijke fout over de status van de doel resource of afhankelijke resource. De netwerk resource provider retourneert bijvoorbeeld 429 met de fout code **RetryableErrorDueToAnotherOperation** wanneer de doel resource is vergrendeld door een andere bewerking. Bekijk de fout details in het antwoord om te bepalen of de fout afkomstig is van beperking of een tijdelijke voor waarde.

## <a name="remaining-requests"></a>Resterende aanvragen

U kunt het aantal resterende aanvragen bepalen door de antwoord headers te controleren. Lees aanvragen retour neren een waarde in de koptekst voor het aantal resterende Lees aanvragen. Schrijf aanvragen bevatten een waarde voor het aantal resterende schrijf aanvragen. In de volgende tabel worden de antwoord headers beschreven die u kunt controleren op deze waarden:

| Reactie header | Beschrijving |
| --- | --- |
| x-MS-ratelimit-resterend abonnement-Lees bewerkingen |Resterende Lees bewerkingen voor het abonnement. Deze waarde wordt geretourneerd bij Lees bewerkingen. |
| x-MS-ratelimit-resterend-abonnement-schrijf bewerkingen |Resterend aantal schrijf bewerkingen in het abonnement. Deze waarde wordt geretourneerd voor schrijf bewerkingen. |
| x-MS-ratelimit-resterend-tenants-Lees bewerkingen |Resterende Lees bewerkingen in Tenant bereik |
| x-MS-ratelimit-resterend-Tenant-schrijf bewerkingen |Resterende schrijf bewerkingen in het Tenant bereik |
| x-MS-ratelimit-resterend abonnement-resource-aanvragen |Resterende aanvragen voor het resource type van het abonnement.<br /><br />Deze header waarde wordt alleen geretourneerd als een service de standaard limiet heeft overschreden. Resource Manager voegt deze waarde toe in plaats van het abonnement op lees-of schrijf bewerkingen. |
| x-MS-ratelimit-resterend abonnement-resource-entiteiten-lezen |Aantal resterende aanvragen voor het resource type voor het abonnement.<br /><br />Deze header waarde wordt alleen geretourneerd als een service de standaard limiet heeft overschreden. Deze waarde geeft het aantal resterende verzamelings aanvragen (lijst resources). |
| x-MS-ratelimit-resterend-Tenant-resource-aanvragen |Resterende aanvragen voor het resource type van het Tenant bereik.<br /><br />Deze header wordt alleen toegevoegd voor aanvragen op Tenant niveau en alleen als de standaard limiet is overschreven door een service. Resource Manager voegt deze waarde toe in plaats van de Tenant leest of schrijft. |
| x-MS-ratelimit-resterend-Tenant-bron-entiteiten-lezen |Aantal resterende aanvragen van het resource type voor het Tenant bereik.<br /><br />Deze header wordt alleen toegevoegd voor aanvragen op Tenant niveau en alleen als de standaard limiet is overschreven door een service. |

De resource provider kan ook antwoord headers retour neren met informatie over de resterende aanvragen. Voor informatie over reactie headers die door de compute resource provider worden geretourneerd, Zie [aanroep frequentie informatie-antwoord headers](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>De header waarden worden opgehaald

Het ophalen van deze header waarden in uw code of script wijkt af van het ophalen van elke header waarde. 

In **C#** haalt u bijvoorbeeld de waarde header op uit een **HttpWebResponse** -object met de naam **Response** met de volgende code:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

In **Power shell** haalt u de waarde van de header op uit een Invoke-WebRequest bewerking.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Zie [limieten voor Resource Manager voor een abonnement controleren](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)voor een volledig Power shell-voor beeld.

Als u de resterende aanvragen voor fout opsporing wilt zien, kunt u de para meter **-debug** opgeven voor uw **Power shell** -cmdlet.

```powershell
Get-AzResourceGroup -Debug
```

Die een groot aantal waarden retourneert, met inbegrip van de volgende reactie waarde:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Gebruik een schrijf bewerking om schrijf limieten op te halen: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Voor het retour neren van een groot aantal waarden, met inbegrip van de volgende waarden:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

In **Azure cli** haalt u de waarde van de header op met behulp van de uitgebreidere optie.

```azurecli
az group list --verbose --debug
```

Voor het retour neren van een groot aantal waarden, met inbegrip van de volgende waarden:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Gebruik een schrijf bewerking om schrijf limieten op te halen: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Voor het retour neren van een groot aantal waarden, met inbegrip van de volgende waarden:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Volgende stappen

* Zie [limieten voor Resource Manager voor een abonnement controleren](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)voor een volledig Power shell-voor beeld.
* Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over limieten en quota's.
* Zie [asynchrone Azure-bewerkingen volgen](async-operations.md)voor meer informatie over het verwerken van asynchrone rest-aanvragen.