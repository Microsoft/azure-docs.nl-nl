---
title: Gedelegeerde toegang in virtueel bureau blad van Windows (klassiek)-Azure
description: Beheer mogelijkheden delegeren op een Windows-implementatie op virtueel bureau blad (klassiek), met inbegrip van voor beelden.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0152dc5102fd3f77418448234cab1234d25b97c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008148"
---
# <a name="delegated-access-in-windows-virtual-desktop-classic"></a>Gedelegeerde toegang in het virtuele bureau blad van Windows (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../delegated-access-virtual-desktop.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Het virtuele bureau blad van Windows beschikt over een gedelegeerd toegangs model waarmee u de hoeveelheid toegang kunt definiëren die een bepaalde gebruiker mag hebben door hen een rol toe te wijzen. Een roltoewijzing heeft drie onderdelen: beveiligingsprincipal, roldefinitie en bereik. Het Windows-model voor gedelegeerde toegang voor virtueel bureau blad is gebaseerd op het Azure RBAC-model. Voor meer informatie over specifieke roltoewijzingen en de bijbehorende onderdelen raadpleegt u [het overzicht van toegangs beheer op basis van rollen in azure](../../role-based-access-control/built-in-roles.md).

Het Windows-bureau blad gedelegeerde toegang ondersteunt de volgende waarden voor elk element van de roltoewijzing:

* Beveiligingsprincipal
    * Gebruikers
    * Service-principals
* Roldefinitie
    * Ingebouwde rollen
* Bereik
    * Tenant groepen
    * Tenants
    * Hostgroepen
    * App-groepen

## <a name="built-in-roles"></a>Ingebouwde rollen

Gedelegeerde toegang in het virtuele bureau blad van Windows heeft verschillende ingebouwde roldefinities die u kunt toewijzen aan gebruikers en service-principals.

* Een RDS-eigenaar kan alles beheren, inclusief toegang tot resources.
* Een RDS-Inzender kan alles beheren, maar heeft geen toegang tot resources.
* Een RDS-lezer kan alles weer geven, maar kan geen wijzigingen aanbrengen.
* Een RDS-operator kan diagnostische activiteiten weer geven.

## <a name="powershell-cmdlets-for-role-assignments"></a>Power shell-cmdlets voor roltoewijzingen

U kunt de volgende cmdlets uitvoeren om roltoewijzingen te maken, weer te geven en te verwijderen:

* **Get-RdsRoleAssignment** toont een lijst met roltoewijzingen.
* **New-RdsRoleAssignment** maakt een nieuwe roltoewijzing.
* **Met Remove-RdsRoleAssignment** worden roltoewijzingen verwijderd.

### <a name="accepted-parameters"></a>Geaccepteerde para meters

U kunt de Basic drie cmdlets met de volgende para meters wijzigen:

* **AadTenantId**: Hiermee geeft u de Azure Active Directory Tenant-id op waarvan de service-principal lid is.
* **AppGroupName**: naam van de app-groep voor extern bureaublad.
* **Diagnostische gegevens**: Hiermee wordt het bereik voor diagnostische gegevens aangegeven. (Moet worden gekoppeld aan ofwel de **infra structuur** -of **Tenant** parameters zijn.)
* **HostPoolName**: de naam van de hostgroep extern bureaublad.
* **Infra structuur**: geeft het bereik van de infra structuur aan.
* **RoleDefinitionName**: extern bureaublad-services de naam van de op rollen gebaseerde toegangs beheer functie die is toegewezen aan de gebruiker, groep of app. (Bijvoorbeeld Extern bureaublad-services eigenaar, Extern bureaublad-services lezer, enzovoort.)
* **ServerPrincipleName**: naam van de toepassing Azure Active Directory.
* **SignInName**: het e-mail adres of de User Principal name van de gebruiker.
* **Tenantnaam**: naam van de Extern bureaublad Tenant.

## <a name="next-steps"></a>Volgende stappen

Zie de [Power shell-referentie](/powershell/windows-virtual-desktop/overview)voor een volledige lijst met Power shell-cmdlets die elke rol kan gebruiken.

Zie voor richt lijnen voor het instellen van een virtuele Windows-desktop omgeving [Windows Virtual Desktop Environment](environment-setup-2019.md)(Engelstalig).
