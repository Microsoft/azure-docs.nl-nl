---
title: Windows Virtual Desktop host pool verwijderen-Azure
description: Een hostgroep verwijderen in virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88007586"
---
# <a name="delete-a-host-pool"></a>Een hostgroep verwijderen

Alle hostgroepen die zijn gemaakt in het virtuele bureau blad van Windows worden gekoppeld aan sessie-hosts en app-groepen. Als u een hostgroep wilt verwijderen, moet u de bijbehorende toepassings groepen en sessie-hosts verwijderen. Het verwijderen van een app-groep is vrij eenvoudig, maar het verwijderen van een sessie-host is ingewik kelder. Wanneer u een sessiehost verwijdert, moet u ervoor zorgen dat er geen actieve gebruikers sessies zijn. Alle gebruikers sessies op de sessiehost moeten worden afgemeld om te voor komen dat gebruikers gegevens verliezen.

## <a name="delete-a-host-pool-with-powershell"></a>Een hostgroep verwijderen met Power shell

Als u een hostgroep met Power shell wilt verwijderen, moet u eerst alle app-groepen in de hostgroep verwijderen. Als u alle app-groepen wilt verwijderen, voert u de volgende Power shell-cmdlet uit:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Voer vervolgens deze cmdlet uit om de hostgroep te verwijderen:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

Met deze cmdlet worden alle bestaande gebruikers sessies op de sessiehost van de hostgroep verwijderd. Ook wordt de registratie van de sessiehost van de hostgroep ongedaan gemaakt. Eventuele gerelateerde virtuele machines (Vm's) blijven bestaan in uw abonnement.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Een hostgroep met de Azure Portal verwijderen

Een hostgroep verwijderen in de Azure Portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Zoek en selecteer **Windows Virtual Desktop**.

3. Selecteer **hostgroepen** in het menu aan de linkerkant van de pagina en selecteer vervolgens de naam van de hostgroep die u wilt verwijderen.

4. Selecteer **toepassings groepen** in het menu aan de linkerkant van de pagina.

5. Selecteer alle toepassings groepen in de hostgroep die u wilt verwijderen en selecteer vervolgens **verwijderen**.

6. Wanneer u de app-groepen hebt verwijderd, gaat u naar het menu aan de linkerkant van de pagina en selecteert u **overzicht**.

7. Selecteer **Verwijderen**.

8. Als er sessie-hosts aanwezig zijn in de hostgroep die u wilt verwijderen, wordt er een bericht weer gegeven waarin u wordt gevraagd of u toestemming wilt geven om door te gaan. Selecteer **Ja**.

9. De Azure Portal zal nu alle sessie-hosts verwijderen en de hostgroep verwijderen. De Vm's die zijn gerelateerd aan de sessiehost, worden niet verwijderd en blijven in uw abonnement.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende artikelen voor meer informatie over het maken van een hostgroep:

- [Een hostpool maken met de Azure-portal](create-host-pools-azure-marketplace.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell.md)

Raadpleeg de volgende artikelen voor meer informatie over het configureren van instellingen voor een hostgroep:

- [Remote Desktop Protocol eigenschappen voor een hostgroep aanpassen](customize-rdp-properties.md)
- [De taakverdelingsmethode voor Windows Virtual Desktop configureren](configure-host-pool-load-balancing.md)
- [Het toewijzings type voor de hostgroep voor persoonlijk bureau blad configureren](configure-host-pool-personal-desktop-assignment-type.md)