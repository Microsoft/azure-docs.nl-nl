---
title: Gebruiks gegevens uploaden naar Azure Monitor
description: Gebruik van Azure Arc enabled Data Services-gegevens uploaden naar Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 0c72eda59f375c70274b17796ca53614ef95505b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669505"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Gebruiks gegevens uploaden naar Azure Monitor

U kunt regel matig gebruiks gegevens exporteren. Het exporteren en uploaden van deze informatie maakt en werkt de gegevens controller, SQL Managed instance en PostgreSQL grootschalige-server groeps resources in Azure.

> [!NOTE] 
> Tijdens de preview-periode zijn er geen kosten verbonden aan het gebruik van Azure Arc enabled Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Wacht minstens 24 uur nadat u de Azure arctangens-gegevens controller hebt gemaakt voordat u gebruiks gegevens uploadt.

## <a name="create-service-principal-and-assign-roles"></a>Een service-principal maken en rollen toewijzen

Voordat u doorgaat, moet u ervoor zorgen dat u de vereiste service-principal hebt gemaakt en deze aan een geschikte rol hebt toegewezen. Zie deze artikelen voor meer informatie:
* [Service-Principal maken](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Rollen toewijzen aan de Service-Principal](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Gebruiksgegevens uploaden

Informatie over het gebruik van de inventaris en het resource gebruik kan in de volgende twee stappen worden geüpload naar Azure:

1. Meld u aan bij de gegevens controller. Voer de waarden in bij de prompt. 

   ```console
   azdata login
   ```

1. Exporteer de gebruiks gegevens met behulp van de `azdata arc dc export` opdracht als volgt:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Met deze opdracht maakt u een `usage.json` bestand met alle Azure-Arc-gegevens bronnen, zoals SQL Managed instances en postgresql grootschalige, etc. die zijn gemaakt op de gegevens controller.

2. De gebruiks gegevens uploaden met behulp van de ```azdata upload``` opdracht

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Uploads automatiseren (optioneel)

Als u metrische gegevens en logboeken op een geplande basis wilt uploaden, kunt u een script maken en dit elke paar minuten uitvoeren op een timer. Hieronder ziet u een voor beeld van het automatiseren van de uploads met behulp van een Linux-shell script.

Voeg in uw favoriete tekst/code-editor het volgende script toe aan het bestand en sla het op als een uitvoerbaar script bestand zoals `.sh` (Linux/Mac) of `.cmd` , `.bat` of `.ps1` .

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

[Logboeken uploaden naar logboeken van Azure Monitor](upload-logs.md)

[Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal](view-billing-data-in-azure.md)

[Resource voor Azure-Arc-gegevens controller in Azure Portal weer geven](view-data-controller-in-azure-portal.md)
