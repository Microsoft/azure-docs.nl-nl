---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f730b2abad244132b06c0fa21f07a171559d22f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96309945"
---
Nee: het verwijderen van een server eindpunt is niet hetzelfde als het opnieuw opstarten van een server. Het verwijderen en opnieuw maken van het server eindpunt is bijna nooit een geschikte oplossing voor het oplossen van problemen met synchronisatie, Cloud lagen of andere aspecten van Azure File Sync. Het verwijderen van een server eindpunt is een destructieve bewerking. Dit kan leiden tot gegevens verlies in het geval dat gelaagde bestanden zich buiten de naam ruimte van het server eindpunt bevinden. Zie [Waarom gelaagde bestanden zich buiten de naam ruimte van het server eindpunt bevinden](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) voor meer informatie. Of dit kan leiden tot ontoegankelijke bestanden voor gelaagde bestanden die zich in de naam ruimte van het server eindpunt bevinden. Deze problemen worden niet opgelost wanneer het server-eind punt opnieuw wordt gemaakt. Gelaagde bestanden kunnen bestaan in de naam ruimte van uw server eindpunt, zelfs als u geen Cloud lagen hebt ingeschakeld. Daarom wordt u aangeraden het server eindpunt niet te verwijderen, tenzij u wilt stoppen met het gebruik van Azure File Sync met deze map of expliciet door een micro soft-Engineer is geïnstrueerd. Zie [een server eindpunt verwijderen](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)voor meer informatie over het verwijderen van server eindpunten.    
