---
title: Gedelegeerde toegang in het virtuele bureau blad van Windows-Azure
description: Beheer mogelijkheden delegeren voor een implementatie van Windows virtueel bureau blad, met inbegrip van voor beelden.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88010052"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Gedelegeerde toegang in Windows Virtual Desktop

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Het virtuele bureau blad van Windows beschikt over een gedelegeerd toegangs model waarmee u de hoeveelheid toegang kunt definiëren die een bepaalde gebruiker mag hebben door hen een rol toe te wijzen. Een roltoewijzing heeft drie onderdelen: beveiligingsprincipal, roldefinitie en bereik. Het Windows-model voor gedelegeerde toegang voor virtueel bureau blad is gebaseerd op het Azure RBAC-model. Voor meer informatie over specifieke roltoewijzingen en de bijbehorende onderdelen raadpleegt u [het overzicht van toegangs beheer op basis van rollen in azure](../role-based-access-control/built-in-roles.md).

Het Windows-bureau blad gedelegeerde toegang ondersteunt de volgende waarden voor elk element van de roltoewijzing:

* Beveiligingsprincipal
    * Gebruikers
    * Gebruikersgroepen
    * Service-principals
* Roldefinitie
    * Ingebouwde rollen
    * Aangepaste rollen
* Bereik
    * Hostgroepen
    * App-groepen
    * Workspaces

## <a name="powershell-cmdlets-for-role-assignments"></a>Power shell-cmdlets voor roltoewijzingen

Voordat u begint, moet u de instructies in [de Power shell-module instellen](powershell-module.md) voor het instellen van de Windows-module voor virtueel bureau blad-Power shell als u dat nog niet hebt gedaan.

Virtueel bureau blad van Windows maakt gebruik van Azure RBAC (op rollen gebaseerd toegangs beheer) tijdens het publiceren van app-groepen naar gebruikers of gebruikers groepen. De gebruikersrol bureau blad-Virtualisatiehost wordt toegewezen aan de gebruiker of gebruikers groep en het bereik is de app-groep. Deze rol geeft de gebruiker speciale toegang tot gegevens voor de app-groep.

Voer de volgende cmdlet uit om Azure Active Directory gebruikers toe te voegen aan een app-groep:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Voer de volgende cmdlet uit om Azure Active Directory gebruikers groep toe te voegen aan een app-groep:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>Volgende stappen

Zie de [Power shell-referentie](/powershell/windows-virtual-desktop/overview)voor een volledige lijst met Power shell-cmdlets die elke rol kan gebruiken.

Zie [ingebouwde rollen van Azure](../role-based-access-control/built-in-roles.md)voor een volledige lijst met rollen die worden ondersteund in azure RBAC.

Zie voor richt lijnen voor het instellen van een virtuele Windows-desktop omgeving [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig).
