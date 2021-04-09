---
title: De gebruiks gegevens, de metrieken en de logboeken uploaden naar Azure Monitor
description: Upload resource-inventaris, gebruiks gegevens, metrieken en logboeken naar Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: a522a650413be056ff64d26e90b6c15cf88d9a7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643487"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>De gebruiks gegevens, de metrieken en de logboeken uploaden naar Azure Monitor

U kunt regel matig gebruiks gegevens voor facturerings doeleinden exporteren, metrische gegevens en logboeken bewaken en deze vervolgens uploaden naar Azure. Als u een van deze drie typen gegevens exporteert en uploadt, worden ook de gegevens controller, SQL Managed instance en PostgreSQL grootschalige server-groeps resources in azure gemaakt en bijgewerkt.

> [!NOTE] 
> Tijdens de preview-periode zijn er geen kosten verbonden aan het gebruik van Azure Arc enabled Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Voordat u gebruiks gegevens, metrieken of Logboeken kunt uploaden, moet u het volgende doen:

* Hulpprogramma's installeren 
* [De `Microsoft.AzureArcData` resource provider registreren](#register-the-resource-provider) 
* [De service-principal maken](#create-service-principal)

## <a name="install-tools"></a>Hulpprogramma's installeren

De vereiste hulpprogram ma's zijn: 
* Azure CLI (AZ) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Zie [hulpprogram Ma's installeren](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Voordat u gegevens van de data service naar Azure uploadt, moet u ervoor zorgen dat de bron provider is geregistreerd voor uw Azure-abonnement `Microsoft.AzureArcData` .

Voer de volgende opdracht uit om de resource provider te controleren:

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Als de resource provider momenteel niet is geregistreerd in uw abonnement, kunt u deze registreren. Voer de volgende opdracht uit om deze te registreren.  Het kan een paar minuten duren voordat deze opdracht is uitgevoerd.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Een service-principal maken

De service-principal wordt gebruikt voor het uploaden van gebruiks-en metrische gegevens.

Voer deze opdrachten uit om de service-principal voor metrische gegevens te maken:

> [!NOTE]
> Voor het maken van een Service-Principal zijn [bepaalde machtigingen vereist in azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Als u een Service-Principal wilt maken, moet u het volgende voor beeld bijwerken. Vervang `<ServicePrincipalName>` `SubscriptionId` en door `resourcegroup` uw waarden en voer de volgende opdracht uit:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/{SubscriptionId}/resourceGroups/{resourcegroup}
```

Als u de Service-Principal eerder hebt gemaakt en u de huidige referenties alleen wilt ophalen, voert u de volgende opdracht uit om de referentie opnieuw in te stellen.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Als u bijvoorbeeld een service-principal met de naam wilt maken `azure-arc-metrics` , voert u de volgende opdracht uit:

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

Voorbeelduitvoer:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Sla de `appId` `password` waarden, en `tenant` op in een omgevings variabele voor later gebruik. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Nadat u de Service-Principal hebt gemaakt, wijst u de Service-Principal toe aan de juiste rol. 

## <a name="assign-roles-to-the-service-principal"></a>Rollen toewijzen aan de Service-Principal

Voer deze opdracht uit om de Service-Principal toe te wijzen aan de `Monitoring Metrics Publisher` rol van het abonnement waarin de resources van het data base-exemplaar zich bevinden:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> U moet dubbele aanhalings tekens voor rolnaams gebruiken bij het uitvoeren vanuit een Windows-omgeving.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

Voorbeelduitvoer:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Wanneer de Service-Principal is toegewezen aan de juiste rol, kunt u door gaan met het uploaden van metrische gegevens, of door de gebruiker. 

## <a name="upload-logs-metrics-or-user-data"></a>Logboeken, metrieken of gebruikers gegevens uploaden

De specifieke stappen voor het uploaden van Logboeken, metrieken of gebruikers gegevens variëren, afhankelijk van het type gegevens dat u uploadt. 

[Logboeken uploaden naar logboeken van Azure Monitor](upload-logs.md)

[Gegevens uploaden naar Azure Monitor](upload-metrics.md)

[Gebruiks gegevens uploaden naar Azure Monitor](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Algemene richt lijnen voor het exporteren en uploaden van gebruik, metrische gegevens

Maken, lezen, bijwerken en verwijderen (ruwe) bewerkingen op Azure Arc-gegevens services worden geregistreerd voor facturerings-en bewakings doeleinden. Er zijn achtergrond services die controleren op deze ruwe bewerkingen en het verbruik op de juiste manier berekenen. De daad werkelijke berekening van het gebruik of verbruik gebeurt volgens een planning en wordt op de achtergrond uitgevoerd. 

Tijdens de preview-periode vindt dit proces een nacht. De algemene richt lijnen zijn het gebruik slechts één keer per dag te uploaden. Wanneer gebruiks gegevens binnen dezelfde periode van 24 uur meerdere keren worden geëxporteerd en geüpload, wordt alleen de resource-inventaris bijgewerkt in Azure Portal, maar niet het resource gebruik.

Voor het uploaden van metrische gegevens accepteert Azure monitor alleen de laatste 30 minuten aan data ([meer informatie](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). De richt lijnen voor het uploaden van metrische gegevens is het uploaden van de metrieken onmiddellijk na het maken van het export bestand, zodat u de volledige gegevensset in Azure Portal kunt weer geven. Als u bijvoorbeeld de metrische gegevens van 2:00 uur hebt geëxporteerd en de upload opdracht hebt uitgevoerd om 2:50 uur. Omdat Azure Monitor alleen gegevens voor de afgelopen 30 minuten accepteert, worden er mogelijk geen gegevens in de portal weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over service-principals](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal](view-billing-data-in-azure.md)

[Resource voor Azure-Arc-gegevens controller in Azure Portal weer geven](view-data-controller-in-azure-portal.md)
