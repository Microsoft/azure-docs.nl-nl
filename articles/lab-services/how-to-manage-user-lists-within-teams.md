---
title: Azure Lab Services gebruikers lijsten beheren vanuit teams
description: Meer informatie over het beheren van Azure Lab Services gebruikers lijsten van teams.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946648"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Gebruikers lijsten van Lab-Services beheren vanuit teams

Wanneer een lab in teams wordt gemaakt (Zie [aan de slag en een Lab-service Lab van teams maken](how-to-get-started-create-lab-within-teams.md)), wordt de gebruikers lijst van de test omgeving automatisch ingevuld en gesynchroniseerd met het team lidmaatschap. Iedereen in het team, met inbegrip van eigen aren, leden en gasten, wordt automatisch toegevoegd aan de gebruikers lijst van de test omgeving. Azure Lab-Services onderhoudt een synchronisatie met het team lidmaatschap en een automatische synchronisatie wordt elke 24 uur geactiveerd. 

## <a name="sync-users"></a>Gebruikers synchroniseren

Docenten kunnen de knop **synchroniseren** gebruiken om een hand matige synchronisatie te activeren zodra het lidmaatschap van het team is bijgewerkt. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Gebruikers synchroniseren":::

Zodra de automatische of hand matige synchronisatie is voltooid, is het volgende waar, afhankelijk van het feit of het lab is gepubliceerd.

* Als het lab niet ten minste eenmaal is gepubliceerd:
    * Gebruikers worden toegevoegd aan of verwijderd uit de lijst met gebruikers van de test omgeving op basis van wijzigingen in het team lidmaatschap. 
* Als het lab ten minste één keer is gepubliceerd, wordt de test capaciteit automatisch bijgewerkt als u niet alleen gebruikers toevoegt of verwijdert.
    * Als er nieuwe toevoegingen aan het team zijn, worden er nieuwe Vm's gemaakt.
    * Als een gebruiker uit het team is verwijderd, wordt ook de bijbehorende virtuele machine verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zodra de sjabloon-VM is geconfigureerd en wanneer de docent selecteert om de sjabloon te publiceren, wordt het aantal Vm's gemaakt dat gelijk is aan het aantal gebruikers in de gebruikers lijst van het lab. Zodra de test omgeving is gepubliceerd en de virtuele machines zijn gemaakt, worden gebruikers automatisch geregistreerd bij het lab en worden de Vm's toegewezen aan de eerste aanmelding bij Azure Lab Services, wanneer ze voor het eerst toegang hebben tot het tabblad met **Azure Lab Services** app. 

Als u de sjabloon-VM wilt publiceren, gaat u naar het venster teams Lab-Services en selecteert u het tabblad **sjabloon** > **...**  ->  **Publiceren**.

Zie [een VM-groep beheren in Lab-services van teams](how-to-manage-vm-pool-within-teams.md)voor informatie over het beheren van VM-groepen.

### <a name="also-review"></a>Ook controleren

Zie de volgende artikelen:

- [Overzicht van het gebruik van Azure Lab Services in teams](lab-services-within-teams-overview.md)
- [Aan de slag en een Lab-service Lab maken op basis van teams](how-to-get-started-create-lab-within-teams.md)
- [Planningen voor Lab-Services maken op basis van teams](how-to-create-schedules-within-teams.md)
- [Toegang tot een virtuele machine (weer gave student) in Lab-services van teams](how-to-access-vm-for-students-within-teams.md)

