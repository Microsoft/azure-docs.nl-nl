---
title: Een VM-groep in Azure Lab Services beheren vanuit teams
description: Meer informatie over het beheren van een VM-groep in Azure Lab Services van teams.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946675"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Een VM-groep in Lab-Services beheren vanuit teams

Het maken van virtuele machines (VM) begint zodra de VM van de sjabloon voor het eerst wordt gepubliceerd. Vm's die gelijk zijn aan het aantal gebruikers in de lijst test omgeving, worden gemaakt. Vm's worden automatisch aan studenten toegewezen bij de eerste aanmelding bij Azure Lab Services. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Een sjabloon publiceren en een VM-groep beheren

Als u de sjabloon wilt publiceren, gaat u naar het venster teams Lab-Services en selecteert u het tabblad **sjabloon** > **...**  ->  **Publiceren**.

Zodra de sjabloon-VM is geconfigureerd en wanneer de docent selecteert om de sjabloon te publiceren, wordt het aantal Vm's gemaakt dat gelijk is aan het aantal gebruikers in de gebruikers lijst van het lab. Zodra de test omgeving is gepubliceerd en de virtuele machines zijn gemaakt, worden gebruikers automatisch geregistreerd bij het lab en worden de Vm's toegewezen aan de eerste aanmelding bij Azure Lab Services, wanneer ze voor het eerst toegang hebben tot het tabblad met **Azure Lab Services** app. 

Wanneer een synchronisatie van een gebruikers lijst wordt geactiveerd, wordt lab-capaciteit (aantal Vm's in het lab) automatisch bijgewerkt op basis van de wijzigingen in het team lidmaatschap. Er worden nieuwe Vm's gemaakt wanneer er nieuwe gebruikers worden toegevoegd en er Vm's worden toegewezen aan de gebruikers die uit het team worden verwijderd, worden ook verwijderd. Zie [gebruikers in teams beheren](how-to-manage-user-lists-within-teams.md)voor meer informatie. 

Docenten kunnen rechtstreeks vanuit het tabblad VM-groep toegang krijgen tot Vm's van studenten. En docenten hebben toegang tot Vm's die zijn toegewezen aan zichzelf op het tabblad **virtuele-machine groep** of door te klikken op de knop **mijn virtual machines** (de bovenste of rechter benedenhoek van het scherm). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM-groep":::

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Overzicht van het gebruik van Azure Lab Services in teams](lab-services-within-teams-overview.md)
- [Aan de slag en een Lab-service Lab maken op basis van teams](how-to-get-started-create-lab-within-teams.md)
- [Gebruikers lijsten van Lab-Services beheren vanuit teams](how-to-manage-user-lists-within-teams.md)
- [Planningen voor Lab-Services maken op basis van teams](how-to-create-schedules-within-teams.md)
- [Toegang tot een virtuele machine (weer gave student) in Lab-services van teams](how-to-access-vm-for-students-within-teams.md)


