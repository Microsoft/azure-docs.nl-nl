---
title: Azure Lab Services planningen maken in teams
description: Meer informatie over het maken van planningen voor Lab-Services in teams.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92042334"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Schema's voor Lab-Services maken en beheren in teams

Met planningen kunt u een leslokaal Lab zodanig configureren dat de virtuele machines in het lab automatisch worden gestart en op een opgegeven tijdstip worden afgesloten. U kunt een eenmalig schema of een terugkerende planning definiëren. De volgende procedures geven u stappen voor het maken en beheren van planningen voor een klas Lab: 

Planningen zijn van invloed op virtuele machines van Lab: 

- De virtuele machine van de sjabloon is niet opgenomen in schema's. 
- Alleen toegewezen virtuele machines worden gestart. Dit betekent dat als een computer niet door een eind gebruiker (student) wordt geclaimd, de computer niet kan beginnen met de geplande uren. 
- Alle virtuele machines (of dit wordt geclaimd door een gebruiker of niet), worden gestopt op basis van het lab-schema. 

> [!IMPORTANT]
> De geplande uitvoerings tijd van Vm's telt niet op het quotum dat aan een gebruiker is toegewezen. Het quotum geldt voor de tijd buiten de plannings uren die een student op Vm's doorbrengt. 

Gebruikers kunnen Lab-schema's maken, bewerken en verwijderen in teams, net zoals op de [website van Labs](https://labs.azure.com). Raadpleeg het artikel over het [maken en beheren van planningen](how-to-create-schedules-within-teams.md).

## <a name="automatic-shutdown-and-disconnect-settings"></a>Instellingen voor automatisch afsluiten en verbreken

U kunt verschillende functies voor het automatisch afsluiten van kosten beheer inschakelen om extra kosten te voor komen wanneer de virtuele machines niet actief worden gebruikt. De combi natie van de volgende drie functies voor automatisch afsluiten en verbreken van de meeste gevallen, waarbij gebruikers per ongeluk hun virtuele machines verlaten:
 
- Automatisch de verbinding van gebruikers met virtuele machines die het besturings systeem inactief bewaart.
- Virtuele machines automatisch uitschakelen wanneer gebruikers de verbinding verbreken.
- Virtuele machines die zijn gestart automatisch afsluiten, maar gebruikers maken geen verbinding.

Raadpleeg het artikel over het [configureren van instellingen voor automatisch afsluiten voor een Lab](how-to-enable-shutdown-disconnect.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Overzicht van het gebruik van Azure Lab Services in teams](lab-services-within-teams-overview.md)
- [Aan de slag en een lab maken in teams](how-to-get-started-create-lab-within-teams.md)
- [Test gebruikers lijsten in teams beheren](how-to-manage-user-lists-within-teams.md)
- [De VM-groep van het lab in teams beheren](how-to-manage-vm-pool-within-teams.md)
- [Toegang tot een virtuele machine in teams – student View](how-to-access-vm-for-students-within-teams.md)
