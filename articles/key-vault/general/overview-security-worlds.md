---
title: Azure Key Vault beveiligings werelden | Microsoft Docs
description: Azure Key Vault is een service met meerdere tenants. Er wordt gebruikgemaakt van een pool met Hsm's op elke Azure-locatie. Alle locaties in een geografische regio delen een cryptografische grens.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: d21deea13aac3d40c452a183c340d3108a1a01f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936325"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault beveiligings werelden en geografische grenzen

Azure Key Vault is een service met meerdere tenants en maakt gebruik van een groep Hardware Security modules (Hsm's) op elke Azure-locatie. 

Alle Hsm's op Azure-locaties in dezelfde geografische regio delen dezelfde cryptografische grens (Thales Security World). Bijvoorbeeld: VS-Oost en VS-West delen dezelfde beveiligings wereld omdat ze deel uitmaken van de geografische locatie van de Verenigde Staten. Op dezelfde manier delen alle Azure-locaties in Japan dezelfde beveiligings wereld en alle Azure-locaties in Australië, India, enzovoort. 

## <a name="backup-and-restore-behavior"></a>Gedrag voor back-up en herstel

Een back-up die is gemaakt van een sleutel kluis in één Azure-locatie kan worden hersteld naar een sleutel kluis in een andere Azure-locatie, zolang beide voor waarden waar zijn:

- Beide Azure-locaties behoren tot dezelfde geografische locatie
- Beide sleutel kluizen behoren tot hetzelfde Azure-abonnement

Zo kan een back-up die is gemaakt door een bepaald abonnement op een sleutel in een sleutel kluis in West-India, alleen worden hersteld naar een andere sleutel kluis in hetzelfde abonnement en geografische locatie. India-West, Centraal-India of India-zuid.

## <a name="regions-and-products"></a>Regio's en producten

- [Azure-regio's](https://azure.microsoft.com/regions/)
- [Micro soft-producten per regio](https://azure.microsoft.com/regions/services/)

Regio's worden toegewezen aan beveiligings werelden, weer gegeven als primaire koppen in de tabellen:

In het artikel producten per regio, **bijvoorbeeld het tabblad** VS-Oost, centraal VS, VS-West alle toewijzing aan de regio in het land van Amerika. 

>[!NOTE]
>Een uitzonde ring hierop is dat US DOD-Oost en US DOD CENTRAL hun eigen beveiligings werelden hebben. 

Op het tabblad **Europa** , Noord-Europa en West-Europa, wordt ook toegewezen aan de regio Europa. Hetzelfde geldt ook op het tabblad **Azië en Stille Oceaan** .
