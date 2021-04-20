---
title: Azure Key Vault beveiligingswerelden | Microsoft Docs
description: Azure Key Vault is een service met meerdere tenants. Er wordt gebruikgemaakt van een groep HMS's in elke Azure-regio. Alle regio's in een geografische regio delen een cryptografische grens.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0d82a3cb4c08d47b6827072378b9827037d32412
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751810"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault beveiligingswerelden en geografische grenzen

Azure-producten zijn beschikbaar in een aantal [Azure-geografieën,](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)met elke Azure-geografie die een of meer regio's bevat. De geografie Europa bevat bijvoorbeeld twee regio's, Europa - noord en Europa - west, terwijl de enige regio in de geografie Brazilië Brazilië - zuid.

Azure Key Vault is een service met meerdere tenants die gebruikmaakt van een groep HMS's (Hardware Security Modules). Alle HMS's in een geografie delen dezelfde cryptografische grens, aangeduid als een 'beveiligingswereld'. Elke geografie komt overeen met één beveiligingswereld en vice versa.

VS - oost en VS - west delen dezelfde beveiligingswereld omdat ze deel uitmaken van de geografie (Verenigde Staten). Op dezelfde manier delen alle Azure-regio's in Japan dezelfde beveiligingswereld, net als alle Azure-regio's in Australië, enzovoort.

>[!NOTE]
> Een uitzondering hierop is dat US DOD EAST en US DOD CENTRAL hun eigen beveiligingswerelden hebben.

## <a name="backup-and-restore-behavior"></a>Back-up- en herstelgedrag

Een back-up van een sleutel uit een sleutelkluis in de ene Azure-regio kan worden hersteld naar een sleutelkluis in een andere Azure-regio, zolang aan beide voorwaarden wordt voldaan:

- Beide Azure-regio's behoren tot dezelfde geografie.
- Beide sleutelkluizen behoren tot hetzelfde Azure-abonnement.

Een back-up van een sleutel in een sleutelkluis in India - west kan bijvoorbeeld worden hersteld naar een andere sleutelkluis in hetzelfde abonnement in de geografische regio India (India - west, India - centraal en India - zuid regio's).

## <a name="next-steps"></a>Volgende stappen

- Zie [Microsoft-producten per regio](https://azure.microsoft.com/regions/services/)
