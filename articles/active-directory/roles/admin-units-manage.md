---
title: Beheereenheden toevoegen en verwijderen - Azure Active Directory | Microsoft Docs
description: Gebruik beheereenheden om het bereik van rolmachtigingen in de Azure Active Directory.
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
ms.openlocfilehash: 6fad9356d3379e76aa259d67711d18f14a4e266f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505272"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Beheereenheden beheren in Azure Active Directory

Voor gedetailleerder beheer in Azure Active Directory (Azure AD) kunt u gebruikers toewijzen aan een Azure AD-rol met een bereik dat is beperkt tot een of meer beheereenheden.

## <a name="get-started"></a>Aan de slag

1. Ga als volgt te werk om query's uit te voeren via de volgende instructies via [Graph Explorer:](https://aka.ms/ge)

    a. Ga in Azure Portal naar Azure AD. 
    
    b. Selecteer in de lijst met **toepassingen Grafiekverkenner**.
    
    c. Selecteer in **het deelvenster Machtigingen** de optie **Beheerder toestemming geven voor Grafiekverkenner.**

    ![Schermopname met de koppeling 'Beheerder toestemming Grafiekverkenner verlenen'.](./media/admin-units-manage/select-graph-explorer.png)


1. Gebruik [Azure AD PowerShell.](https://www.powershellgallery.com/packages/AzureAD/)

## <a name="add-an-administrative-unit"></a>Een beheereenheid toevoegen

U kunt een beheereenheid toevoegen met behulp van de Azure Portal of PowerShell.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in Azure Portal naar Azure AD. Selecteer vervolgens in het linkerdeelvenster **Beheereenheden**.

    ![Schermopname van de koppeling 'Beheereenheden' in Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Selecteer de **knop** Toevoegen bovenaan het deelvenster en voer  vervolgens in het vak Naam de naam van de beheereenheid in. Voeg desgewenst een beschrijving van de beheereenheid toe.

    ![Schermopname van de knop Toevoegen en het vak Naam voor het invoeren van de naam van de beheereenheid.](./media/admin-units-manage/add-new-admin-unit.png)

1. Selecteer de blauwe **knop Toevoegen** om de beheereenheid te maken.

### <a name="use-powershell"></a>PowerShell gebruiken

Installeer [Azure AD PowerShell voordat](https://www.powershellgallery.com/packages/AzureAD/) u de volgende opdrachten probeert uit te voeren:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

U kunt de waarden tussen aanhalingstekens wijzigen, indien nodig.

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

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

## <a name="remove-an-administrative-unit"></a>Een beheereenheid verwijderen

In Azure AD kunt u een beheereenheid verwijderen die u niet meer nodig hebt als bereikeenheid voor beheerdersrollen.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in Azure Portal naar **Azure AD** en selecteer vervolgens **Beheereenheden.** 
1. Selecteer de beheereenheid die u wilt verwijderen en selecteer **vervolgens Verwijderen.** 
1. Selecteer Ja om te bevestigen dat u de beheereenheid **wilt verwijderen.** De beheereenheid wordt verwijderd.

![Schermopname van de knop Verwijderen van de beheereenheid en het bevestigingsvenster.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

U kunt de waarden tussen aanhalingstekens wijzigen, zoals vereist voor de specifieke omgeving.

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

* [Gebruikers beheren in een beheereenheid](admin-units-add-manage-users.md)
* [Groepen in een beheereenheid beheren](admin-units-add-manage-groups.md)
