---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176079"
---
<!--v-sharos 10/13/2105 virtual device security-->

Houd rekening met de volgende beveiligings overwegingen wanneer u het virtuele StorSimple-apparaat gebruikt:

* Het virtuele apparaat wordt beveiligd via uw Microsoft Azure-abonnement. Dit betekent dat als u het virtuele apparaat gebruikt en uw Azure-abonnement is aangetast, de gegevens die op het virtuele apparaat zijn opgeslagen, ook vatbaar zijn.
* De open bare sleutel van het certificaat dat wordt gebruikt voor het versleutelen van gegevens die zijn opgeslagen in azure StorSimple, wordt veilig beschikbaar gesteld aan de klassieke Azure-Portal en de persoonlijke sleutel wordt bewaard met het StorSimple-apparaat. Op het virtuele StorSimple-apparaat worden de open bare en persoonlijke sleutels opgeslagen in Azure.
* Het virtuele apparaat wordt gehost in het Microsoft Azure Data Center.

