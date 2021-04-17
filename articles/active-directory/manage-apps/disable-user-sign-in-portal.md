---
title: Aanmeldingen van gebruikers uitschakelen voor een bedrijfs-app in Azure AD
description: Een bedrijfstoepassing uitschakelen zodat gebruikers zich niet kunnen aanmelden bij Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/12/2019
ms.author: iangithinji
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9af580ef8b3691a5f188ac15069dda00a5f5802
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374247"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Aanmeldingen van gebruikers uitschakelen voor een bedrijfs-app in Azure Active Directory

Het is eenvoudig om een bedrijfstoepassing uit te schakelen, zodat gebruikers zich niet kunnen aanmelden bij Azure Active Directory (Azure AD). U hebt de juiste machtigingen nodig om de bedrijfs-app te beheren. En u moet een globale beheerder zijn voor de directory.

## <a name="how-do-i-disable-user-sign-ins"></a>Hoe kan ik gebruikers sign-ins uitschakelen?

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account van een globale beheerder voor de directory.
1. Selecteer **Alle services,** voer **Azure Active Directory** in het tekstvak in en selecteer vervolgens **Enter.**
1. Selecteer in **Azure Active Directory**  -   **_deelvenster directoryname_*_ (dat wil zeggen,*** het deelvenster Azure AD voor de directory die u beheert) de optie _ Bedrijfstoepassingen.
1. In het **deelvenster Bedrijfstoepassingen - Alle toepassingen** ziet u een lijst met de apps die u kunt beheren. Selecteer een app.
1. Selecteer in het deelvenster ***appname** _ (dat wil zeggen, het deelvenster met de naam van de geselecteerde app in de titel) _*Eigenschappen**.
1. Selecteer in het deelvenster ***appname** _ - _ *Properties** de optie **Nee** bij Ingeschakeld voor gebruikers **om zich aan te melden?**.
1. Selecteer de **opdracht** Opslaan.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD PowerShell gebruiken om een niet-lijst met apps uit te schakelen

Als u de AppId kent van een app die niet wordt weergegeven in de lijst met Enterprise-apps (bijvoorbeeld omdat u de app hebt verwijderd of omdat de service-principal nog niet is gemaakt omdat de app vooraf is geautoriseerd door Microsoft), kunt u de service-principal voor de app handmatig maken en vervolgens uitschakelen met behulp van [de AzureAD PowerShell-cmdlet](/powershell/module/azuread/New-AzureADServicePrincipal).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Volgende stappen

* [Al mijn groepen bekijken](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](assign-user-or-group-access-portal.md)
* [Een gebruikers- of groepstoewijzing verwijderen uit een bedrijfs-app](./assign-user-or-group-access-portal.md)
* [De naam of het logo van een bedrijfs-app wijzigen](./add-application-portal-configure.md)
