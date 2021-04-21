---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4407ffb9ab16a720ef00a288d72b0fb4c2dbced1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774558"
---
Deze fout kan optreden wanneer de Azure File Sync-service niet toegankelijk is vanaf de server. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. Controleer of de `FileSyncSvc.exe` Windows-service niet wordt geblokkeerd door uw firewall.
2. Controleer of poort 443 is geopend voor uitgaande verbindingen met de Azure File Sync service. U kunt dit doen met de `Test-NetConnection` cmdlet . De URL voor de onderstaande tijdelijke aanduiding `<azure-file-sync-endpoint>` vindt u in het document [Azure File Sync-proxy en -firewallinstellingen](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Zorg ervoor dat de proxyconfiguratie is ingesteld zoals verwacht. U kunt dit doen met de cmdlet `Get-StorageSyncProxyConfiguration`. In [Azure File Sync-proxy en -firewallinstellingen](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall) vindt u meer informatie over het configureren van de proxyconfiguratie voor Azure File Sync.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Gebruik de Test-StorageSyncNetworkConnectivity om de netwerkverbinding met de service-eindpunten te controleren. Zie Test network [connectivity to service endpoints (Netwerkverbinding met service-eindpunten testen) voor meer informatie.](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)    

5. Neem contact op met uw netwerkbeheerder voor aanvullende hulp bij het oplossen van problemen met de netwerkverbinding.