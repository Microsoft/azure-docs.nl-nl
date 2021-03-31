---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75477146"
---
Deze referentie bevat schaal baarheid en prestatie doelen voor Azure Storage. De schaal baarheid en prestatie doelen die hier worden weer gegeven, zijn hoogwaardige doelen, maar kunnen worden behaald. In alle gevallen is de aanvraag snelheid en band breedte die door uw opslag account wordt behaald, afhankelijk van de grootte van de opgeslagen objecten, de toegangs patronen die worden gebruikt en het type werk belasting dat door uw toepassing wordt uitgevoerd.

Test uw service om te bepalen of de prestaties voldoen aan uw vereisten. Vermijd zo mogelijk plotselinge pieken in de frequentie van verkeer en zorg ervoor dat het verkeer goed wordt gedistribueerd over partities.

Wanneer uw toepassing de limiet bereikt van wat een partitie voor uw workload kan verwerken, begint Azure Storage met het retour neren van fout code 503 (server bezet) of fout code 500 (time-out van bewerking). Als er 503 fouten optreden, kunt u de toepassing aanpassen om een exponentieel uitstel-beleid te gebruiken voor nieuwe pogingen. Met de exponentiële uitstel kan de belasting van de partitie worden verkleind en kunnen pieken in het verkeer naar die partitie worden vergemakkelijkt.
