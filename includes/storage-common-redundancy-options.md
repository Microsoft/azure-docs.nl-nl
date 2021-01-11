---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 65729934ea7c4037d6857aec10b14cdddd616368
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805639"
---
De volgende redundantieopties zijn beschikbaar voor een opslagaccount:

* Lokaal redundante opslag (LRS): Een eenvoudige, voordelige redundantiestrategie. Gegevens worden drie keer synchroon gekopieerd binnen één fysieke locatie in de primaire regio.
* Zone-redundante opslag (ZRS): Redundantie voor scenario's die hoge beschikbaarheid vereisen. Gegevens worden synchroon gekopieerd naar drie Azure-beschikbaarheidszones in de primaire regio.
* Geografisch redundante opslag (GRS): Redundantie over meerdere regio's om te beschermen tegen regionale storingen. Gegevens worden synchroon drie keer in de primaire regio gekopieerd en vervolgens asynchroon gekopieerd naar de secundaire regio. Voor leestoegang tot gegevens in de secundaire regio schakelt u geografisch redundante opslag met leestoegang (RA-GRS) in.
* Geografische zone-redundante opslag (GZRS) (preview-versie): Redundantie voor scenario's waarbij hoge beschikbaarheid en maximale duurzaamheid zijn vereist. Gegevens worden synchroon gekopieerd naar drie Azure-beschikbaarheidszones in de primaire regio en vervolgens asynchroon gekopieerd naar de secundaire regio. Voor leestoegang tot gegevens in de secundaire regio schakelt u geografisch zone-redundante opslag met lees toegang (RA-GZRS) in.

Zie [Azure Storage-redundantie](../articles/storage/common/storage-redundancy.md) voor meer informatie over opties voor redundantie in Azure Storage.
