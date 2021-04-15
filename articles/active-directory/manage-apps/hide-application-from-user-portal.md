---
title: Een Enterprise-toepassing verbergen voor de gebruikerservaring in Azure AD
description: Een Enterprise-toepassing verbergen voor de gebruikerservaring in Azure Active Directory toegangspaneel of Microsoft 365 startpaneel.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90f3e3aeb1d68c6c6e6eeea29c04ff7880dccd3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374196"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Bedrijfstoepassingen verbergen voor eindgebruikers in Azure Active Directory

Instructies voor het verbergen van toepassingen in het deelvenster MyApps of het startscherm Microsoft 365 eindgebruikers. Wanneer een toepassing verborgen is, hebben gebruikers nog steeds machtigingen voor de toepassing. 

## <a name="prerequisites"></a>Vereisten

Toepassingsbeheerder zijn bevoegdheden vereist om een toepassing te verbergen in het deelvenster MyApps en Microsoft 365 starten.

Globale beheerder zijn vereist om alle toepassingen Microsoft 365 verbergen.


## <a name="hide-an-application-from-the-end-user"></a>Een toepassing verbergen voor de eindgebruiker
Gebruik de volgende stappen om een toepassing te verbergen in het deelvenster MyApps en Microsoft 365 te starten.

1.  Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder voor uw directory.
2.  Selecteer **Azure Active Directory**.
3.  Selecteer **Enterprise-toepassingen**. De **blade Bedrijfstoepassingen - Alle** toepassingen wordt geopend.
4.  Selecteer **bedrijfstoepassingen** onder **Toepassingstype** als dit nog niet is geselecteerd.
5.  Zoek de toepassing die u wilt verbergen en klik op de toepassing.  Het overzicht van de toepassing wordt geopend.
6.  Klik op **Eigenschappen**. 
7.  Klik voor **de vraag Zichtbaar voor gebruikers?** op **Nee.**
8.  Klik op **Opslaan**.

> [!NOTE]
> Deze instructies zijn alleen van toepassing op Bedrijfstoepassingen.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD PowerShell gebruiken om een toepassing te verbergen

Als u een toepassing wilt verbergen in het deelvenster MyApps, kunt u de tag HideApp handmatig toevoegen aan de service-principal voor de toepassing. Voer de volgende [AzureAD PowerShell-opdrachten](/powershell/module/azuread/#service_principals) uit om de eigenschap Zichtbaar voor **gebruikers?** van de toepassing in te stellen op **Nee.** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Toepassingen Microsoft 365 verbergen in het deelvenster MyApps

Gebruik de volgende stappen om alle toepassingen Microsoft 365 te verbergen in het deelvenster MyApps. De toepassingen zijn nog steeds zichtbaar in de Office 365-portal.

1.  Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder voor uw directory.
2.  Selecteer **Azure Active Directory**.
3.  Selecteer **Gebruikers**.
4.  Selecteer **Gebruikersinstellingen**.
5.  Klik **onder Bedrijfstoepassingen** op Beheren hoe eindgebruikers hun toepassingen starten **en weergeven.**
6.  Voor **Gebruikers kunnen Alleen Office 365-apps in de Office 365-portal zien,** klikt u op **Ja.**
7.  Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
* [Al mijn groepen bekijken](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](assign-user-or-group-access-portal.md)
* [Een gebruikers- of groepstoewijzing verwijderen uit een bedrijfs-app](./assign-user-or-group-access-portal.md)
* [De naam of het logo van een bedrijfs-app wijzigen](./add-application-portal-configure.md)
