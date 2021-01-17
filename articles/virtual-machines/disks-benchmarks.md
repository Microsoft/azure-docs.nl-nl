---
title: Benchmarking van uw toepassing op Azure Disk Storage
description: Meer informatie over het proces van benchmarking van uw toepassing in Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0d1fb4d51aa08ce1c4889e82d7284da05a68aa17
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540727"
---
# <a name="benchmarking-a-disk"></a>Een schijf benchmarken

Benchmarking is het proces van het simuleren van verschillende werk belastingen in uw toepassing en het meten van de toepassings prestaties voor elke werk belasting. Door de stappen te volgen die worden beschreven in het [artikel ontwerpen voor hoge prestaties](premium-storage-performance.md), hebt u de prestatie vereisten voor de toepassing verzameld. Door Bench Mark-hulpprogram ma's uit te voeren op de Vm's die de toepassing hosten, kunt u de prestatie niveaus bepalen die uw toepassing kan verzorgen met Premium Storage. In dit artikel bieden we u voor beelden van benchmarking van een standaard DS14-VM die is ingericht met Azure Premium Storage-schijven.

We hebben respectievelijk gebruikgemaakt van common Bench Mark-hulpprogram ma's Iometer en FIO voor Windows en Linux. Deze hulpprogram ma's starten meerdere threads die een productie simuleren, zoals workload, en meten de systeem prestaties. Met de hulpprogram ma's kunt u ook para meters configureren, zoals blok grootte en wachtrij diepte, die normaal gesp roken niet kunnen worden gewijzigd voor een toepassing. Dit biedt u meer flexibiliteit om de maximale prestaties van een grootschalige virtuele machine die is ingericht met Premium-schijven voor verschillende typen toepassings werkbelastingen te verzorgen. Ga naar [Iometer](http://www.iometer.org/) en [FIO](http://freecode.com/projects/fio)voor meer informatie over elk Bench Mark-hulp programma.

Als u de onderstaande voor beelden wilt volgen, maakt u een standaard DS14-VM en koppelt u elf Premium Storage schijven aan de virtuele machine. Van de 11 schijven configureert u 10 schijven met host-caching als ' geen ' en stript u deze in een volume met de naam NoCacheWrites. Configureer host-caching als ' ReadOnly ' op de resterende schijf en maak een volume met de naam CacheReads met deze schijf. Met deze installatie kunt u de maximale Lees-en schrijf prestaties van een standaard DS14-VM bekijken. Ga voor gedetailleerde stappen voor het maken van een DS14-VM met Premium Ssd's naar [ontwerp voor hoge prestaties](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar ons artikel over het [ontwerpen voor hoge prestaties](premium-storage-performance.md).

In dat artikel maakt u een controle lijst die vergelijkbaar is met uw bestaande toepassing voor het prototype. Met Bench Mark-hulpprogram ma's kunt u de werk belastingen simuleren en de prestaties meten op de prototype toepassing. Op die manier kunt u bepalen welke schijf aanbieding kan overeenkomen of de prestatie vereisten van uw toepassing overschrijden. Vervolgens kunt u dezelfde richt lijnen voor uw productie toepassing implementeren.