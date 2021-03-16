---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563233"
---
| Gebruiks model | Cache modus | Back-end-verificatie | Maximale vertraging voor terugschrijven |
|--|--|--|--|
| Zware, incidentele schrijf bewerkingen lezen | Lezen | Nooit | Geen |
| Meer dan 15% schrijf bewerkingen | Lezen/schrijven | 8 uur | 20 minuten |
| Clients slaan de cache over | Lezen | 30 seconden | Geen |
| Meer dan 15% schrijf bewerkingen, frequente back-end-controle (30 seconden) | Lezen/schrijven | 30 seconden | 20 minuten |
| Meer dan 15% schrijf bewerkingen, frequente back-end-controle (60 seconden) | Lezen/schrijven | 60 seconden | 20 minuten |
| Meer dan 15% schrijf bewerkingen, regel matig terugschrijven | Lezen/schrijven | 30 seconden | 30 seconden |
| Lees zo lang de back-upserver om de 3 uur wordt gecontroleerd | Lezen | 3 uur | Geen |
