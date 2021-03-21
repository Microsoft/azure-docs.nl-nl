---
title: Beheer eenheden toevoegen en verwijderen-Azure Active Directory | Microsoft Docs
description: Beheer eenheden gebruiken om het bereik van rolmachtigingen te beperken in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0706fad1e5340625c32eab691ac3e4d58eeafc9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012097"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Beheer eenheden in Azure Active Directory beheren

Voor gedetailleerdere administratieve controle in Azure Active Directory (Azure AD) kunt u gebruikers toewijzen aan een Azure AD-rol met een bereik dat beperkt is tot een of meer beheer eenheden.

## <a name="get-started"></a>Aan de slag

1. Ga als volgt te werk om query's uit te voeren vanuit de volgende instructies via [Graph Explorer](https://aka.ms/ge):

    a. Ga in de Azure Portal naar Azure AD. 
    
    b. Selecteer in de lijst toepassingen de optie **Graph Explorer**.
    
    c. Selecteer in het deel venster **machtigingen** de optie **beheerder toestemming geven voor Graph Explorer**.

    ![Scherm afbeelding van de koppeling ' toestemming geven aan beheerder voor Graph Explorer '.](./media/admin-units-manage/select-graph-explorer.png)


1. Gebruik [Azure AD Power shell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="add-an-administrative-unit"></a>Een beheer eenheid toevoegen

U kunt een administratieve eenheid toevoegen met behulp van de Azure Portal of Power shell.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in de Azure Portal naar Azure AD. Selecteer vervolgens in het linkerdeel venster **beheer eenheden**.

    ![Scherm afbeelding van de koppeling "administratieve eenheden" in azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Selecteer de knop **toevoegen** in het bovenste gedeelte van het deel venster en voer vervolgens in het vak **naam** de naam van de beheer eenheid in. U kunt desgewenst een beschrijving van de beheer eenheid toevoegen.

    ![Scherm afbeelding met de knop toevoegen en het vak naam voor het invoeren van de naam van de beheer eenheid.](./media/admin-units-manage/add-new-admin-unit.png)

1. Selecteer de knop Blue **toevoegen** om de beheer eenheid te volt ooien.

### <a name="use-powershell"></a>PowerShell gebruiken

Installeer [Azure AD Power shell](https://www.powershellgallery.com/packages/AzureAD/) voordat u probeert de volgende opdrachten uit te voeren:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

U kunt de waarden die zijn Inge sloten tussen aanhalings tekens, indien nodig, wijzigen.

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Aanvraag

```http
POST /administrativeUnits
```

Hoofdtekst

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Een beheer eenheid verwijderen

In azure AD kunt u een administratieve eenheid verwijderen die u niet meer nodig hebt als een eenheid van het bereik voor beheerders rollen.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in de Azure Portal naar **Azure AD** en selecteer vervolgens **beheer eenheden**. 
1. Selecteer de beheer eenheid die u wilt verwijderen en selecteer vervolgens **verwijderen**. 
1. Selecteer **Ja** om te bevestigen dat u de beheer eenheid wilt verwijderen. De administratieve eenheid wordt verwijderd.

![Scherm afbeelding van de knop voor het verwijderen van de beheer eenheid en het bevestigings venster.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $adminUnitObj.ObjectId
```

U kunt de waarden tussen aanhalings tekens, zoals vereist voor de specifieke omgeving, wijzigen.

### <a name="use-the-graph-api"></a>Met behulp van de Graph API

Aanvraag

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Hoofdtekst

```http
{}
```

## <a name="next-steps"></a>Volgende stappen

* [Gebruikers beheren in een beheer eenheid](admin-units-add-manage-users.md)
* [Groepen beheren in een beheer eenheid](admin-units-add-manage-groups.md)
