---
title: SAS-fout berichten voor virtuele machines-Azure Marketplace
description: Veelgestelde vragen bij het werken met hand tekeningen voor gedeelde toegang (SAS).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126835"
---
# <a name="virtual-machine-sas-failure-messages"></a>Fout berichten van SAS voor virtuele machines

Hieronder volgen enkele veelvoorkomende problemen bij het werken met hand tekeningen voor gedeelde toegang (die worden gebruikt om de geüploade Vhd's voor uw oplossing te identificeren en te delen), samen met voorgestelde oplossingen.

| Probleem | Fout bericht | Herstellen |
| --------- | ------------------- | ------- |
| *Fout bij het kopiëren van installatie kopieën* |  |  |
| '? ' is niet gevonden in de SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij met aanbevolen hulpprogram ma's. |
| de para meters "St" en "SE" niet in SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI met de juiste **begin datum** en **eind datum** waarde bij. |
| "SP = RL" niet in SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij met machtigingen die zijn ingesteld als `Read` en `List` . |
| SAS-URI bevat spaties in naam van VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij om spaties te verwijderen. |
| Fout met SAS URI-autorisatie | `Failure: Copying Images. Not able to download blob due to authorization error.` | Controleer de SAS URI-indeling en corrigeer deze. Genereer indien nodig opnieuw. |
| De para meters van de SAS-URI "St" en "SE" hebben geen volledige datum-tijd specificatie | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | De **begin** -en **eind** datum parameters van de SAS-URI ( `st` en `se` subtekenreeksen) moeten de volledige datum-tijd notatie hebben, zoals `11-02-2017T00:00:00Z` . Verkorte versies zijn ongeldig (sommige opdrachten in azure CLI kunnen standaard verkorte waarden genereren). |
|  |  |  |

Zie [using Shared Access signatures (SAS) (Engelstalig)](../storage/common/storage-sas-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [SAS-URI genereren](azure-vm-get-sas-uri.md)