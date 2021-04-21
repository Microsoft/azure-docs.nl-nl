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
ms.openlocfilehash: 2f50a1d046b7b7d3fd7507ef87f8f29096a4faa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774557"
---
Nee: het verwijderen van een server-eindpunt is niet zoals het opnieuw opstarten van een server. Het verwijderen en opnieuw maken van het server-eindpunt is bijna nooit een geschikte oplossing voor het oplossen van problemen met synchronisatie, opslag in cloudlagen of andere aspecten van Azure File Sync. Het verwijderen van een server-eindpunt is een destructieve bewerking. Dit kan leiden tot gegevensverlies in het geval dat gelaagde bestanden buiten de naamruimte van het server-eindpunt bestaan. Zie [Waarom gelaagde bestanden bestaan buiten de naamruimte van](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) het server-eindpunt voor meer informatie. Het kan ook leiden tot ontoegankelijke bestanden voor gelaagde bestanden die aanwezig zijn in de naamruimte van het server-eindpunt. Deze problemen worden niet opgelost wanneer het server-eindpunt opnieuw wordt gemaakt. Gelaagde bestanden kunnen bestaan binnen de naamruimte van uw server-eindpunt, zelfs als u opslag in cloudlagen nooit hebt ingeschakeld. Daarom raden we u aan het server-eindpunt niet te verwijderen, tenzij u wilt stoppen met het gebruik van Azure File Sync met deze specifieke map of expliciet bent geïnstrueerd om dit te doen door een Microsoft-engineer. Zie Een server-eindpunt verwijderen voor meer informatie over het verwijderen [van server-eindpunten.](../articles/storage/file-sync/file-sync-server-endpoint.md#remove-a-server-endpoint)    
