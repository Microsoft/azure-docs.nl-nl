---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b7c097306ba46d4f0024aecc55994508e2d8a090
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011699"
---
Om uw gegevens in uw Azure-bestandsshares te beschermen tegen gegevensverlies of beschadiging, bewaren alle Azure-bestandsshares meerdere kopieën van elk bestand wanneer ze worden geschreven. Afhankelijk van de vereisten van uw werkbelasting, kan u aanvullende maten van redundantie selecteren. Azure Files ondersteunt momenteel de volgende opties voor gegevensredundantie:

- **Lokaal redundant**: Lokaal redundante opslag, ook wel LRS genoemd, houdt in dat elk bestand drie keer in een Azure Storage-cluster wordt opgeslagen. Zo wordt u beschermd tegen gegevensverlies door hardwarefouten, zoals een beschadigd schijfstation.
- **Zone-redundant**: Zone-redundante opslag, ook wel ZRS genoemd, houdt in dat elk bestand in drie verschillende Azure Storage-cluster wordt opgeslagen. Net als met lokaal redundante opslag biedt zoneredundantie u drie kopieën van elk bestand, alleen worden deze kopieën fysiek geïsoleerd in drie verschillende opslagclusters in verschillende *Azure-beschikbaarheidszones*. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Een schrijfbewerking naar de opslag wordt niet geaccepteerd totdat er naar de opslagclusters in alle drie de beschikbaarheidszones wordt geschreven. 
- **Geografisch redundant**: Geografisch redundante opslag, ook wel GRS genoemd, is vergelijkbaar met lokaal redundante opslag, omdat een bestand drie keer in een Azure Storage-cluster in de primaire regio wordt opgeslagen. Alle schrijfbewerkingen worden vervolgens asynchroon gerepliceerd naar een door Microsoft gedefinieerde secundaire regio. Geografisch redundante opslag biedt zes kopieën van uw gegevens, verspreid over twee Azure-regio's. In het geval van een ernstige ramp, zoals het permanente verlies van een Azure-regio door een natuurramp of een soortgelijke gebeurtenis, voert Microsoft een failover uit, zodat de secundaire regio de primaire regio wordt en alle bewerkingen blijven worden uitgevoerd. Omdat de replicatie tussen de primaire en secundaire regio's asynchroon is, gaan gegevens die nog niet zijn gerepliceerd naar de secundaire regio verloren in het geval van een ernstige ramp. U kunt ook een handmatige failover uitvoeren van een geografisch redundante opslagaccount.
- **Geografische zoneredundantie**: Geografisch zone-redundante opslag, ook wel GZRS genoemd, is vergelijkbaar met zone-redundante opslag, omdat een bestand in drie verschillende Azure Storage-cluster in de primaire regio wordt opgeslagen. Alle schrijfbewerkingen worden vervolgens asynchroon gerepliceerd naar een door Microsoft gedefinieerde secundaire regio. Het failoverproces voor een geografisch zone-redundante opslag werkt hetzelfde als voor geografisch redundante opslag.

Standaard Azure-bestandsshares ondersteunen alle vier de soorten redundante, waar Premium Azure-bestandsshares alleen lokaal redundante en zone-redundante opslag ondersteunen.

Opslagaccount voor algemeen gebruik versie 2 (GPv2) bieden twee aanvullende redundantie-opties die niet worden ondersteund door Azure Files: geografisch redundante opslag met leestoegang, vaak RA-GRS genoemd, en geografisch zone-redundante opslag met leestoegang, vaak RA-GZRS genoemd. U kunt Azure-bestandsshares inrichten in opslagaccounts waarin deze opties zijn ingesteld, maar Azure Files ondersteunt het lezen vanuit de secundaire regio niet. Azure-bestandsshares die in geografisch redundante of geografisch zone-redundante opslagaccounts met leestoegang worden geïmplementeerd, worden gefactureerd als respectievelijk geografisch redundante of geografisch zone-redundante opslag.