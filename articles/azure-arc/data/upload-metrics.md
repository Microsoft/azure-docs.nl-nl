---
title: Gegevens uploaden naar Azure Monitor
description: Data Services-metrische gegevens voor Azure Arc ingeschakeld uploaden naar Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f319f912520a69a0c68f89a3d4178f63cc45ca1f
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356545"
---
# <a name="upload-metrics-to-azure-monitor"></a>Gegevens uploaden naar Azure Monitor

U kunt regel matig bewakings gegevens exporteren en deze vervolgens uploaden naar Azure. Het exporteren en uploaden van gegevens maakt en werkt ook de gegevens controller, het SQL Managed instance en de PostgreSQL grootschalige-server groeps resources in Azure.

> [!NOTE] 
> Tijdens de preview-periode zijn er geen kosten verbonden aan het gebruik van Azure Arc enabled Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet u ervoor zorgen dat u de vereiste service-principal hebt gemaakt en deze aan een geschikte rol hebt toegewezen. Zie deze artikelen voor meer informatie:
* [Service-Principal maken](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Rollen toewijzen aan de Service-Principal](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Metrische gegevens uploaden

Met Azure Arc Data Services kunt u eventueel uw metrische gegevens uploaden naar Azure Monitor, zodat u metrische gegevens kunt samen voegen en analyseren, waarschuwingen wilt genereren, meldingen wilt verzenden of geautomatiseerde acties wilt activeren. 

Door uw gegevens naar Azure Monitor te verzenden, kunt u ook metrische gegevens opslaan op een locatie en op grote schaal, waardoor de gegevens op lange termijn kunnen worden opgeslagen voor geavanceerde analyses.

Als u meerdere sites hebt met Azure Arc Data Services, kunt u Azure Monitor gebruiken als centrale locatie voor het verzamelen van alle logboeken en metrische gegevens over uw sites.

## <a name="set-final-environment-variables-and-confirm"></a>Definitieve omgevings variabelen instellen en bevestigen

Stel de URL van de SPN-instantie in een omgevings variabele in:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Controleer of alle vereiste omgevings variabelen zijn ingesteld als u wilt:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Gegevens uploaden naar Azure Monitor

Voor het uploaden van metrische gegevens voor uw Azure Arc enabled SQL Managed instances en PostgreSQL grootschalige-Server groepen met Azure Arc enabled, voert u de volgende CLI-opdrachten uit:

1. Meld u aan bij de gegevens controller met `azdata` .
 
1. Alle metrische gegevens exporteren naar het opgegeven bestand:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Metrische gegevens uploaden naar Azure monitor:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Wacht mini maal 30 minuten nadat de Azure Arc-gegevens exemplaren zijn gemaakt voor de eerste upload.
   >
   >Zorg ervoor dat `upload` de gegevens direct worden verwijderd nadat `export` Azure monitor alleen metrische gegevens voor de laatste 30 minuten accepteert. [Meer informatie](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting).


Als er fouten worden weer gegeven met de melding ' fout bij het ophalen van metrische gegevens ' tijdens het exporteren, controleert u of de gegevensverzameling is ingesteld op `true` door de volgende opdracht uit te voeren:

```console
azdata arc dc config show
```

Zoek onder "beveiliging"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Controleer of de `allowNodeMetricsCollection` `allowPodMetricsCollection` Eigenschappen en zijn ingesteld op `true` .

## <a name="view-the-metrics-in-the-portal"></a>De metrische gegevens weergeven in de Azure Portal

Zodra uw metrische gegevens zijn geüpload, kunt u ze weer geven vanuit de Azure Portal.
> [!NOTE]
> Het kan enkele minuten duren voordat de geüploade gegevens worden verwerkt voordat u de metrieken in de portal kunt weer geven.


Als u uw metrische gegevens in de portal wilt weer geven, gebruikt u deze koppeling om de portal te openen: <https://portal.azure.com> Zoek vervolgens naar uw data base-exemplaar op naam in de zoek balk:

U kunt het CPU-gebruik op de overzichts pagina weer geven of u kunt klikken op metrische gegevens in het navigatie paneel aan de linkerkant.

Kies SQL Server als metrische naam ruimte:

Selecteer de metrische gegevens die u wilt visualiseren (u kunt ook meerdere selecteren):

Wijzig de frequentie in de laatste 30 minuten:

> [!NOTE]
> U kunt alleen metrische gegevens uploaden voor de afgelopen 30 minuten. Azure Monitor negeert metrische gegevens die ouder zijn dan 30 minuten.

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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Algemene richt lijnen voor het exporteren en uploaden van gebruik, metrische gegevens

Maken, lezen, bijwerken en verwijderen (ruwe) bewerkingen op Azure Arc-gegevens services worden geregistreerd voor facturerings-en bewakings doeleinden. Er zijn achtergrond services die controleren op deze ruwe bewerkingen en het verbruik op de juiste manier berekenen. De daad werkelijke berekening van het gebruik of verbruik gebeurt volgens een planning en wordt op de achtergrond uitgevoerd. 

Tijdens de preview-periode vindt dit proces een nacht. De algemene richt lijnen zijn het gebruik slechts één keer per dag te uploaden. Wanneer gebruiks gegevens binnen dezelfde periode van 24 uur meerdere keren worden geëxporteerd en geüpload, wordt alleen de resource-inventaris bijgewerkt in Azure Portal, maar niet het resource gebruik.

Voor het uploaden van metrische gegevens accepteert Azure monitor alleen de laatste 30 minuten aan data ([meer informatie](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). De richt lijnen voor het uploaden van metrische gegevens is het uploaden van de metrieken onmiddellijk na het maken van het export bestand, zodat u de volledige gegevensset in Azure Portal kunt weer geven. Als u bijvoorbeeld de metrische gegevens van 2:00 uur hebt geëxporteerd en de upload opdracht hebt uitgevoerd om 2:50 uur. Omdat Azure Monitor alleen gegevens voor de afgelopen 30 minuten accepteert, worden er mogelijk geen gegevens in de portal weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

[Logboeken uploaden naar logboeken van Azure Monitor](upload-logs.md)

[De gebruiks gegevens, de metrieken en de logboeken uploaden naar Azure Monitor](upload-usage-data.md)

[Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal](view-billing-data-in-azure.md)

[Resource voor Azure-Arc-gegevens controller in Azure Portal weer geven](view-data-controller-in-azure-portal.md)
