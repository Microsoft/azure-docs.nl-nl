---
title: Bestaande hostgroep uitbreiden met nieuwe sessie hosts-Azure
description: Een bestaande hostgroep uitbreiden met nieuwe sessie-hosts in virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b65560a3b10d04887040c4da1e137912810b3095
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929588"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Een bestaande hostgroep uitbreiden met nieuwe sessie-hosts

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Wanneer u het gebruik in uw hostgroep wilt verbeteren, moet u mogelijk uw bestaande hostgroep uitbreiden met nieuwe sessie-hosts om de nieuwe belasting af te handelen.

In dit artikel wordt uitgelegd hoe u een bestaande hostgroep kunt uitbreiden met nieuwe sessie-hosts.

## <a name="what-you-need-to-expand-the-host-pool"></a>Wat u nodig hebt om de hostgroep uit te breiden

Voordat u begint, moet u ervoor zorgen dat u een hostgroep en virtuele machines (Vm's) voor de host hebt gemaakt met behulp van een van de volgende methoden:

- [Azure-portal](./create-host-pools-azure-marketplace.md)
- [Een hostpool maken met PowerShell](./create-host-pools-powershell.md)

U hebt ook de volgende informatie nodig bij het maken van de hostgroep en de Vm's voor de sessiehost:

- VM-grootte, installatie kopie en naam voorvoegsel
- Referenties voor domein toevoegen aan beheerder
- Naam van het virtuele netwerk en het subnet

## <a name="add-virtual-machines-with-the-azure-portal"></a>Virtuele machines met de Azure Portal toevoegen

U kunt als volgt uw hostgroep uitbreiden door virtuele machines toe te voegen:

1. Meld u aan bij Azure Portal.

2. Zoek en selecteer **Windows Virtual Desktop**.

3. Selecteer in het menu aan de linkerkant van het scherm de optie **hostgroepen** en selecteer vervolgens de naam van de hostgroep waaraan u virtuele machines wilt toevoegen.

4. Selecteer **sessie-hosts** in het menu aan de linkerkant van het scherm.

5. Selecteer **+ toevoegen** om te beginnen met het maken van de hostgroep.

6. Negeer het tabblad basis informatie en selecteer in plaats daarvan het tabblad **VM-Details** . Hier kunt u de details weer geven en bewerken van de virtuele machine (VM) die u wilt toevoegen aan de hostgroep.

7. Selecteer de resource groep waarvoor u de virtuele machines wilt maken en selecteer vervolgens de regio. U kunt kiezen welke huidige regio u gebruikt of een nieuwe regio.

8. Voer het aantal sessie-hosts in dat u wilt toevoegen aan uw hostgroep in **aantal vm's**. Als u uw hostgroep bijvoorbeeld met vijf hosts wilt uitbreiden, voert u **5** in.

    >[!NOTE]
    >Hoewel het mogelijk is om de installatie kopie en het voor voegsel van de Vm's te bewerken, wordt het niet aanbevolen deze te bewerken als u virtuele machines met verschillende installatie kopieën in dezelfde hostgroep hebt. Bewerk de installatie kopie en het voor voegsel alleen als u virtuele machines met oudere installatie kopieën uit de betrokken hostgroep wilt verwijderen.

9. Selecteer voor de gegevens van het **virtuele netwerk** het virtuele netwerk en het subnet waaraan u de virtuele machines wilt koppelen. U kunt hetzelfde virtuele netwerk selecteren als de bestaande machines die momenteel worden gebruikt, of een andere naam kiezen die geschikter is voor de regio die u in stap 7 hebt geselecteerd.

10. Voer voor het **beheerders account** de Active Directory domein gebruikers naam en het wacht woord in die zijn gekoppeld aan het virtuele netwerk dat u hebt geselecteerd. Deze referenties worden gebruikt om de virtuele machines lid te maken van het virtuele netwerk.

      >[!NOTE]
      >Zorg ervoor dat de namen van uw beheerders overeenkomen met de informatie die hier wordt gegeven. En dat er geen MFA is ingeschakeld voor het account.

11. Selecteer het tabblad **tag** als u labels hebt waarop u de virtuele machines wilt groeperen. Als dat niet het geval is, slaat u dit tabblad over.

12. Selecteer het tabblad **controleren + maken** . Controleer uw keuzes en selecteer **maken** als alles er goed uitziet.

## <a name="next-steps"></a>Volgende stappen

Nu u uw bestaande hostgroep hebt uitgebreid, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om ze te testen als onderdeel van een gebruikers sessie. U kunt verbinding maken met een sessie met een van de volgende clients:

- [Verbinding maken met de Windows Desktop-client](./connect-windows-7-10.md)
- [Verbinding maken met de webclient](./connect-web.md)
- [Verbinding maken met de Android-client](./connect-android.md)
- [Verbinding maken met de macOS-client](./connect-macos.md)
- [Verbinding maken met de iOS-client](./connect-ios.md)
