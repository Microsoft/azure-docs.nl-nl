---
title: Logboeken uploaden naar logboeken van Azure Monitor
description: Logboeken voor Azure-Arc-gegevens Services uploaden naar Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92376216"
---
# <a name="upload-logs-to-azure-monitor"></a>Logboeken uploaden naar logboeken van Azure Monitor

U kunt periodiek logboeken exporteren en deze vervolgens uploaden naar Azure. Als u Logboeken exporteert en uploadt, worden ook de gegevens controller, SQL Managed instance en PostgreSQL grootschalige-server groeps resources in azure gemaakt en bijgewerkt.

> [!NOTE] 
> Tijdens de preview-periode zijn er geen kosten verbonden aan het gebruik van Azure Arc enabled Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Voordat u begint

Voordat u Logboeken kunt uploaden, moet u het volgende doen: 

1. [Een log Analytics-werk ruimte maken](#create-a-log-analytics-workspace)
1. [ID en gedeelde sleutel toewijzen aan omgevings variabelen](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Een log Analytics-werk ruimte maken

Als u een log Analytics-werk ruimte wilt maken, voert u deze opdrachten uit om een Log Analytics-werk ruimte te maken en de toegangs gegevens in omgevings variabelen in te stellen.

> [!NOTE]
> Sla deze stap over als u al een werk ruimte hebt.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Voorbeelduitvoer:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>ID en gedeelde sleutel toewijzen aan omgevings variabelen

Sla de logboek werkruimte analyse `customerId` op als een omgevings variabele die u later kunt gebruiken:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Met deze opdracht worden de toegangs sleutels geretourneerd die nodig zijn om verbinding te maken met uw log Analytics-werk ruimte:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Voorbeelduitvoer:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Sla de primaire sleutel op in een omgevings variabele die u later wilt gebruiken:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Definitieve omgevings variabelen instellen en bevestigen

Stel de URL van de SPN-instantie in een omgevings variabele in:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Omgevings variabelen verifiëren

Controleer of alle vereiste omgevings variabelen zijn ingesteld als u wilt:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Als u de omgevings variabelen hebt ingesteld, kunt u Logboeken uploaden naar de werk ruimte logboek. 

## <a name="upload-logs-to-azure-monitor"></a>Logboeken uploaden naar logboeken van Azure Monitor

 Als u logboeken wilt uploaden voor uw Azure Arc enabled SQL Managed instances en AzureArc ingeschakeld PostgreSQL grootschalige-Server groepen voert u de volgende CLI-opdrachten uit:

1. Meld u aan bij de Azure-Arc-gegevens controller met [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

   ```console
   azdata login
   ```

   Volg de aanwijzingen om de naam ruimte, de gebruikers naam van de beheerder en het wacht woord in te stellen. 

1. Alle logboeken exporteren naar het opgegeven bestand:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Logboeken uploaden naar een Azure monitor log Analytics-werk ruimte:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Uw logboeken in Azure Portal weer geven

Zodra de logboeken zijn geüpload, zou u er als volgt query's op moeten kunnen uitvoeren met de logboekqueryverkenner:

1. Open de Azure Portal en zoek naar uw werk ruimte op naam in de zoek balk bovenin en selecteer deze.
2. Selecteer Logboeken in het linkerdeel venster.
3. Selecteer aan de slag (of selecteer de koppelingen op de pagina aan de slag om meer te weten te komen over Log Analytics als u er nog geen ervaring voor hebt).
4. Volg de zelf studie voor meer informatie over Log Analytics als dit de eerste keer is met behulp van Log Analytics.
5. Vouw Aangepaste logboeken aan de onderkant van de lijst met tabellen uit. Er wordt dan een tabel met de naam sql_instance_logs_CL weergegeven.
6. Selecteer het oogpictogram naast de tabel naam.
7. Selecteer de knop weer geven in query-editor.
8. U hebt nu een query in de query-editor waarin de meest recente 10 gebeurtenissen in het logboek worden weer gegeven.
9. Nu kunt u experimenteren met het uitvoeren van query's in de logboeken met behulp van de query-editor, het instellen van waarschuwingen, enzovoort.

## <a name="automating-uploads-optional"></a>Uploads automatiseren (optioneel)

Als u metrische gegevens en logboeken op een geplande basis wilt uploaden, kunt u een script maken en dit elke paar minuten uitvoeren op een timer. Hieronder ziet u een voor beeld van het automatiseren van de uploads met behulp van een Linux-shell script.

Voeg in uw favoriete tekst/code-editor het volgende script toe aan het bestand en sla het op als een uitvoerbaar script bestand zoals. v (Linux/Mac) of. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Het uitvoer bare script bestand maken

```console
chmod +x myuploadscript.sh
```

Voer het script uit om de 20 minuten:

```console
watch -n 1200 ./myuploadscript.sh
```

U kunt ook een job scheduler gebruiken zoals cron of Windows taak planner of een Orchestrator zoals Ansible, puppet of chef.

## <a name="next-steps"></a>Volgende stappen

[Metrische gegevens en logboeken uploaden naar Azure Monitor](upload-metrics.md)

[De gebruiks gegevens, de metrieken en de logboeken uploaden naar Azure Monitor](upload-usage-data.md)

[Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal](view-billing-data-in-azure.md)

[Resource voor Azure-Arc-gegevens controller in Azure Portal weer geven](view-data-controller-in-azure-portal.md)
