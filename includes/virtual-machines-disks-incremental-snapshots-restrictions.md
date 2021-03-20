---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95992885"
---
- Incrementele moment opnamen kunnen momenteel niet worden verplaatst tussen abonnementen.
- U kunt momenteel alleen SAS-Uri's van Maxi maal vijf moment opnamen van een bepaalde momentopname familie genereren op een bepaald moment.
- U kunt geen incrementele moment opname maken voor een bepaalde schijf buiten het abonnement van de schijf.
- Er kunnen Maxi maal zeven incrementele moment opnamen per schijf worden gemaakt om de vijf minuten.
- Er kunnen voor één schijf een totaal van 200 incrementele moment opnamen worden gemaakt.
- U kunt de wijzigingen niet ophalen tussen moment opnamen die zijn gemaakt vóór en nadat u de grootte van de bovenliggende schijf hebt gewijzigd in een grens van 4 TB. U hebt bijvoorbeeld een incrementele moment opname van moment opnamen gemaakt: een wanneer de grootte van een schijf 2 TB was. U hebt nu de schijf grootte tot 6 TB verhoogd en vervolgens een andere incrementele moment opname van de moment opname-b. U kunt de wijzigingen niet ophalen tussen snap shot-a en snap shot-b. U moet het volledige exemplaar van de moment opname opnieuw downloaden-b nadat het formaat is gewijzigd. Daarna kunt u de wijzigingen ophalen tussen moment opnamen-b en moment opnamen die zijn gemaakt na moment opname-b. 
