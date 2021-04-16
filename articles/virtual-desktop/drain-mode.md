---
title: De Windows Virtual Desktop instellen - Azure
description: De modus Voor leeg gebruik configureren en gebruiken in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509078"
---
# <a name="set-drain-mode"></a>De waterafvoermodus instellen

De modus Voor leeg gebruik isoleert een sessiehost wanneer u patches wilt toepassen en onderhoud wilt uitvoeren zonder gebruikerssessies te onderbreken. Wanneer deze is geïsoleerd, accepteert de sessiehost geen nieuwe gebruikerssessies. Nieuwe verbindingen worden omgeleid naar de volgende beschikbare sessiehost. Bestaande verbindingen in de sessiehost blijven werken totdat de gebruiker zich aftekent of de beheerder de sessie beëindigt. Wanneer de sessiehost zich in de waterafvoermodus, kunnen beheerders ook op afstand verbinding maken met de server zonder via de service Windows Virtual Desktop gaan. U kunt deze instelling toepassen op zowel gepoolde als persoonlijke bureaubladen.

## <a name="set-drain-mode-using-the-azure-portal"></a>Stel de waterafvoermodus in met behulp van Azure Portal

De modus voor leegstand inschakelen in de Azure Portal:

1. Open het Azure Portal ga naar de hostgroep die u wilt isoleren.

2. Selecteer sessiehosts in het **navigatiemenu.**

3. Selecteer vervolgens de hosts voor wie u de leegstand wilt inschakelen en selecteer **vervolgens De modus Leegstand inschakelen.**

4. Als u de leegstand wilt uitschakelen, selecteert u de hostgroepen die de modus Leegstand hebben ingeschakeld en selecteert u **vervolgens De modus Leegvoer uitschakelen.**

## <a name="set-drain-mode-using-powershell"></a>De leegstand instellen met Behulp van PowerShell

U kunt de modus voor leegstand instellen in PowerShell met de parameter *AllowNewSessions,* die deel uitmaakt van de [opdracht Update-AzWvdSessionhost.](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true)

Voer deze cmdlet uit om de leegloopmodus in te schakelen:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Voer deze cmdlet uit om de leegloopmodus uit te schakelen:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>U moet deze opdracht uitvoeren voor elke sessiehost waarin u de instelling wilt toepassen.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over de Azure Portal voor Windows Virtual Desktop, raadpleegt u [onze zelfstudies.](create-host-pools-azure-marketplace.md) Als u al bekend bent met de basisprincipes, bekijkt u enkele van de andere functies die u kunt gebruiken met de Azure Portal, zoals [msix-app-attach](app-attach-azure-portal.md) en [Azure Advisor](azure-advisor.md).

Als u de PowerShell-methode gebruikt en wilt zien wat de module nog meer kan doen, raadpleegt u De [PowerShell-module](powershell-module.md) instellen voor Windows Virtual Desktop en onze [PowerShell-referentie](/powershell/module/az.desktopvirtualization/).