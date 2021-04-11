---
title: Unbox en uw Azure percept DK-onderdelen verzamelen
description: Meer informatie over het Unbox, verbinding maken en inschakelen van uw Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: efa255ba38f7e00785335bf458ecc0ed91da646b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608177"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Snelstartgids: Unbox en assemblage van uw Azure percept DK-onderdelen

Als u uw Azure percept DK hebt ontvangen, raadpleegt u deze hand leiding voor informatie over het verbinden van de onderdelen en het inschakelen van het apparaat.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- P7-draaier (optioneel, gebruikt voor het beveiligen van de stroom kabel van de voeding naar het draaggolf bord)

## <a name="unbox-and-assemble-your-device"></a>Uw apparaat Unbox en samen stellen

1. Unbox de Azure percept DK-onderdelen.

    De Devkit bevat een Carrier Board, het gezichts deel van Azure percept, het vak Bureau-accessoires met antennes en kabels, en een welkomst kaart met een hex-toets.

1. Verbind de Devkit-onderdelen.

    > [!NOTE]
    > De poort van de voedings adapter bevindt zich aan de rechter kant van het vervoerders bord. De overige poorten (2x USB-A, 1x USB-C en 1x-Ethernet) en de aan/uit-knop bevinden zich aan de linkerkant van het vervoerders bord.

    1. Hand schroef beide Wi-Fi antennes in het vervoerders bord.

    1. Verbind het Vision-SoM met de USB-C-poort van het draaggolf bord met de USB-C-kabel.

    1. Verbind de stroom kabel met de voedings adapter.

    1. Verwijder alle resterende plastic verpakkingen van de apparaten.

    1. Verbind de stroom adapter/kabel met de draag signaal kaart en een wand contactdoos. Als u de stekker van de stroom kabel volledig wilt beveiligen, gebruikt u een P7-draaier (niet opgenomen in de Devkit) om de schroef kracht van de connectors te verhogen.

    1. Nadat u de stroom kabel hebt aangesloten op een wand contactdoos, wordt het apparaat automatisch ingeschakeld. De aan/uit-knop aan de linkerkant van het draag signaal bord is lichter. Wacht even totdat het apparaat is opgestart.

        > [!NOTE]
        > De aan/uit-knop is om het apparaat uit te scha kelen of opnieuw op te starten terwijl er verbinding is met een stop contact. In het geval van een stroom storing wordt het apparaat automatisch opnieuw opgestart.

Voor een visuele demonstratie van de Devkit-assembly raadpleegt u 0:00 tot en met 0:50 van de volgende video:

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Volgende stappen

Nu uw Devkit is aangesloten en ingeschakeld, raadpleegt u de installatie van de [Azure PERCEPT DK-ervaring](./quickstart-percept-dk-set-up.md) voor het volt ooien van de installatie van het apparaat. Met de Setup-ervaring kunt u uw Devkit verbinden met een Wi-Fi netwerk, een SSH-aanmelding instellen, een IoT Hub maken en uw Devkit inrichten voor uw Azure-account. Zodra u de installatie van het apparaat hebt voltooid, kunt u beginnen met het maken van prototypen.