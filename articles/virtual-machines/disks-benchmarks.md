---
title: Bench Mark-toepassing op Azure Disk Storage
description: Meer informatie over het proces van benchmarking van uw toepassing in Azure.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094593"
---
# <a name="benchmark-a-disk"></a>Een benchmark-test uitvoeren op een schijf

Benchmarking is het proces van het simuleren van verschillende werk belastingen in uw toepassing en het meten van de toepassings prestaties voor elke werk belasting. Door de stappen te volgen die worden beschreven in het [artikel ontwerpen voor hoge prestaties](premium-storage-performance.md), hebt u de prestatie vereisten voor de toepassing verzameld. Door Bench Mark-hulpprogram ma's uit te voeren op de Vm's die de toepassing hosten, kunt u de prestatie niveaus bepalen die uw toepassing kan behaalt met Premium Ssd's. In dit artikel bieden we u voor beelden van een benchmarking voor een Standard_D8ds_v4 VM die is ingericht met Azure Premium Ssd's.

We hebben respectievelijk gebruikgemaakt van common Bench Mark-hulpprogram ma's DiskSpd en FIO voor Windows en Linux. Deze hulpprogram ma's starten meerdere threads die een productie simuleren, zoals workload, en meten de systeem prestaties. Met de hulpprogram ma's kunt u ook para meters configureren, zoals blok grootte en wachtrij diepte, die normaal gesp roken niet kunnen worden gewijzigd voor een toepassing. Dit biedt u meer flexibiliteit om de maximale prestaties van een grootschalige virtuele machine die is ingericht met Premium Ssd's voor verschillende soorten werk belastingen voor toepassingen te verzorgen. Ga naar [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) en [FIO](http://freecode.com/projects/fio)voor meer informatie over elk Bench Mark-hulp programma.

Als u de onderstaande voor beelden wilt volgen, maakt u een Standard_D8ds_v4 en koppelt u vier Premium-Ssd's aan de VM. Van de vier schijven configureert u drie met host caching als ' geen ' en stript u deze in een volume met de naam NoCacheWrites. Configureer host-caching als ' ReadOnly ' op de resterende schijf en maak een volume met de naam CacheReads met deze schijf. Met deze installatie kunt u de maximale Lees-en schrijf prestaties van een Standard_D8ds_v4 virtuele machine bekijken. Zie voor gedetailleerde stappen voor het maken van een Standard_D8ds_v4 met Premium Ssd's [ontwerpen voor hoge prestaties](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar ons artikel over het [ontwerpen voor hoge prestaties](premium-storage-performance.md).

In dat artikel maakt u een controle lijst die vergelijkbaar is met uw bestaande toepassing voor het prototype. Met Bench Mark-hulpprogram ma's kunt u de werk belastingen simuleren en de prestaties meten op de prototype toepassing. Op die manier kunt u bepalen welke schijf aanbieding kan overeenkomen of de prestatie vereisten van uw toepassing overschrijden. Vervolgens kunt u dezelfde richt lijnen voor uw productie toepassing implementeren.
