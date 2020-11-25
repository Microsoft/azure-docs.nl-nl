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
ms.openlocfilehash: b26d4af29a92fb0f14c52e76a7eae1d0073a3aa0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96005308"
---
Deze fout kan optreden wanneer de Azure File Sync-service niet toegankelijk is vanaf de server. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. Controleer of de Windows-service `FileSyncSvc.exe` niet is geblokkeerd door uw firewall.
2. Controleer of poort 443 is geopend voor uitgaande verbindingen met de Azure File Sync-Service. U kunt dit doen met de `Test-NetConnection` cmdlet. De URL voor de onderstaande tijdelijke aanduiding `<azure-file-sync-endpoint>` vindt u in het document [Azure File Sync-proxy en -firewallinstellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Zorg ervoor dat de proxyconfiguratie is ingesteld zoals verwacht. U kunt dit doen met de cmdlet `Get-StorageSyncProxyConfiguration`. In [Azure File Sync-proxy en -firewallinstellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) vindt u meer informatie over het configureren van de proxyconfiguratie voor Azure File Sync.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Gebruik de cmdlet Test-StorageSyncNetworkConnectivity om de netwerk verbinding met de service-eind punten te controleren. Zie [netwerk connectiviteit testen op service-eind punten](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)voor meer informatie.    

5. Neem contact op met de netwerk beheerder voor aanvullende hulp bij het oplossen van problemen met de netwerk verbinding.