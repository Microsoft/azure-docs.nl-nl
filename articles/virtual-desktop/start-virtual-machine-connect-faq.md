---
title: Windows Virtual Desktop start Veelgestelde vragen over het verbinden van virtuele machines-Azure
description: Veelgestelde vragen en aanbevolen procedures voor het gebruik van de functie voor het starten van virtuele machines in Connect.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0fd6edf3fbcd6fea7503acd7265850723b5ec09f
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080287"
---
# <a name="start-vm-on-connect-faq-preview"></a>VM starten op veelgestelde vragen over Connect (preview-versie)

> [!IMPORTANT]
> De functie voor het starten van de VM in Connect is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel worden veelgestelde vragen behandeld over de functie voor het starten van virtuele machines (VM) in Connect (preview) voor Windows Virtual Desktop-hostgroepen.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Worden de toewijzingen van Vm's automatisch ongedaan gemaakt wanneer een gebruiker deze stopt met het gebruik ervan?

Nee. U moet extra beleids regels configureren om gebruikers uit hun sessies te ondertekenen en Azure Automation-scripts uit te voeren om de toewijzing van Vm's ongedaan te maken.

Het toewijzings beleid voor uitstel configureren:

1. Maak extern verbinding met de virtuele machine waarvoor u het beleid wilt instellen.

2. Open de **Groepsbeleid editor** en ga vervolgens naar de computer configuratie van het **lokale computer beleid**  >    >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **extern bureaublad-services**  >  **extern bureaublad Session Host**-  >  **sessie tijds limieten**.

3. Zoek het beleid dat aangeeft dat de **tijds limiet voor verbroken sessies is ingesteld** en wijzig vervolgens de waarde in **ingeschakeld**.

4. Nadat u het beleid hebt ingeschakeld, selecteert u **een verbroken sessie beëindigen**.

>[!NOTE]
>Zorg ervoor dat u de tijds limiet voor het beleid ' een verbroken sessie beëindigen ' instelt op een waarde die groter is dan vijf minuten. Een geringe tijds limiet kan ertoe leiden dat gebruikers sessies beëindigen als het netwerk de verbinding te lang verliest, waardoor het werk verloren gaat.

Door gebruikers te ondertekenen, wordt de toewijzing van hun Vm's ongedaan. Voor meer informatie over hoe u de toewijzing van Vm's ongedaan kunt maken, raadpleegt u [Vm's starten of stoppen tijdens buiten kantoor uren](../automation/automation-solution-vm-management.md).

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Kunnen gebruikers de virtuele machine uitschakelen vanaf hun clients?

Ja. Gebruikers kunnen de virtuele machine afsluiten met behulp van het menu Start binnen hun sessie, net als bij een fysieke computer. Als u de virtuele machine afsluit, wordt de toewijzing van de virtuele machine niet ongedaan. Voor meer informatie over hoe u de toewijzing van Vm's ongedaan kunt maken, raadpleegt u [Vm's starten of stoppen tijdens buiten kantoor uren](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het configureren van de start-VM bij het maken van verbinding de [virtuele machine starten in verbinding maken (preview)](start-virtual-machine-connect.md).

Bekijk onze algemene [Veelgestelde](faq.md)vragen als u meer algemene vragen hebt over het virtuele bureau blad van Windows.
