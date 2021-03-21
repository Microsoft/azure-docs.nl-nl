---
title: Een bedrijfs toepassing verbergen voor gebruikers ervaring in azure AD
description: Het verbergen van een bedrijfs toepassing op basis van de gebruikers ervaring in Azure Active Directory toegangs Vensters of Microsoft 365 starters.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258877"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Zakelijke toepassingen verbergen voor eind gebruikers in Azure Active Directory

Instructies voor het verbergen van toepassingen van het deel venster MyApps of Microsoft 365 Launcher van eind gebruikers. Wanneer een toepassing wordt verborgen, hebben gebruikers nog steeds machtigingen voor de toepassing. 

## <a name="prerequisites"></a>Vereisten

De bevoegdheden van de toepassings beheerder zijn vereist om een toepassing te verbergen in het deel venster MyApps en Microsoft 365 Launcher.

Globale beheerders bevoegdheden zijn vereist om alle Microsoft 365-toepassingen te verbergen.


## <a name="hide-an-application-from-the-end-user"></a>Een toepassing verbergen voor de eind gebruiker
Voer de volgende stappen uit om een toepassing te verbergen in het deel venster MyApps en Microsoft 365 start programma voor toepassingen.

1.  Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder voor uw Directory.
2.  Selecteer **Azure Active Directory**.
3.  Selecteer **Enterprise-toepassingen**. De Blade **bedrijfs toepassingen-alle toepassingen** wordt geopend.
4.  Onder **toepassings type** selecteert u **bedrijfs toepassingen**, als deze nog niet is geselecteerd.
5.  Zoek de toepassing die u wilt verbergen en klik op de toepassing.  Het overzicht van de toepassing wordt geopend.
6.  Klik op **Eigenschappen**. 
7.  Voor de **zicht bare gebruiker?** vraag, klikt u op **Nee**.
8.  Klik op **Opslaan**.

> [!NOTE]
> Deze instructies zijn alleen van toepassing op bedrijfs toepassingen.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD Power shell gebruiken om een toepassing te verbergen

Als u een toepassing wilt verbergen in het deel venster MyApps, kunt u de HideApp-tag hand matig toevoegen aan de service-principal voor de toepassing. Voer de volgende [AzureAD Power shell](/powershell/module/azuread/#service_principals) -opdrachten uit om de eigenschap **zichtbaar voor gebruikers** in te stellen op **Nee**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Microsoft 365 toepassingen verbergen in het deel venster MyApps

Gebruik de volgende stappen om alle Microsoft 365 toepassingen te verbergen in het deel venster MyApps. De toepassingen zijn nog steeds zichtbaar in de Office 365-Portal.

1.  Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder voor uw Directory.
2.  Selecteer **Azure Active Directory**.
3.  Selecteer **Gebruikers**.
4.  Selecteer **Gebruikersinstellingen**.
5.  Onder **bedrijfs toepassingen** klikt **u op beheren hoe eind gebruikers hun toepassingen starten en weer geven.**
6.  Voor **gebruikers kunnen alleen office 365-apps in de office 365-Portal zien**, klikt u op **Ja**.
7.  Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
* [Alle groepen weer geven](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](assign-user-or-group-access-portal.md)
* [Een gebruiker of groeps toewijzing verwijderen uit een bedrijfs-app](./assign-user-or-group-access-portal.md)
* [De naam of het logo van een zakelijke app wijzigen](./add-application-portal-configure.md)
