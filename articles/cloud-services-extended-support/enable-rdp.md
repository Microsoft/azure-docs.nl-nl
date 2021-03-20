---
title: De Extern bureaublad Toep assen in Cloud Services (uitgebreide ondersteuning)
description: Extern bureaublad inschakelen voor Cloud Services (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 53f873013a6f16ce5a28ee5d915afa556057f643
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744280"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>De uitbrei ding Extern bureaublad Toep assen op Azure Cloud Services (uitgebreide ondersteuning)

De Azure Portal maakt gebruik van de extensie extern bureau blad om extern bureau blad in te scha kelen zelfs nadat de toepassing is geïmplementeerd. Met de instellingen voor extern bureau blad voor uw Cloud service kunt u extern bureau blad inschakelen, het lokale Administrator-account bijwerken, de certificaten selecteren die worden gebruikt bij verificatie en de verval datum voor die certificaten instellen. 

## <a name="apply-remote-desktop--extension"></a>Extern bureaublad extensie Toep assen
1. Ga naar de Cloud service waarvoor u extern bureau blad wilt inschakelen en selecteer **extern bureaublad** in het navigatie deel venster aan de linkerkant.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Afbeelding toont het selecteren van de optie Extern bureaublad in de Azure Portal":::

2. Selecteer **Toevoegen**.
3. Kies de rollen om extern bureau blad voor in te scha kelen.
4. Vul de vereiste velden in voor de gebruikers naam, het wacht woord, de verval datum en het certificaat (niet vereist).

    :::image type="content" source="media/remote-desktop-2.png" alt-text="Afbeelding toont de gegevens die nodig zijn om verbinding te maken met extern bureau blad.":::

5. Wanneer u klaar bent, selecteert u **Opslaan**. Het duurt enkele ogen blikken voordat de rolinstanties gereed zijn voor het ontvangen van verbindingen.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Verbinding maken met rolinstanties met Extern bureaublad ingeschakeld
Zodra extern bureau blad is ingeschakeld voor de rollen, kunt u rechtstreeks vanuit de Azure Portal een verbinding starten.

1. Klik op **rollen en instanties** om de instantie-instellingen te openen.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="Afbeelding toont het selecteren van de optie rollen en instanties op de Blade configuratie.":::

2. Selecteer een rolinstantie waarvoor extern bureau blad is geconfigureerd.
3. Klik op **verbinding maken** om een extern bureau blad-verbindings bestand te downloaden.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Afbeelding toont het selecteren van de instantie van de worker-rol in de Azure Portal.":::
    
4. Open het bestand om verbinding te maken met de rolinstantie.


## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).