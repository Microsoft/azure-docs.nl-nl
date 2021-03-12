---
title: Groepen toevoegen, verwijderen en weer geven in een beheer eenheid-Azure Active Directory | Microsoft Docs
description: Groepen en machtigingen voor rollen beheren in een beheer eenheid in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: f88a0818d93c33b6265cc8c695479d2a42678ada
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011031"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Groepen toevoegen en beheren in een beheer eenheid in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u groepen toevoegen aan een administratieve eenheid voor een nauw keurigere administratieve controle mogelijkheden.

Zie aan de [slag](admin-units-manage.md#get-started)voor het voorbereiden van het gebruik van Power shell en Microsoft Graph voor beheer van beheer eenheden.

## <a name="add-groups-to-an-administrative-unit"></a>Groepen toevoegen aan een beheer eenheid

U kunt groepen toevoegen aan een administratieve eenheid door gebruik te maken van de Azure Portal, Power shell of Microsoft Graph.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt alleen afzonderlijke groepen toewijzen aan een beheer eenheid. Er is geen optie om groepen toe te wijzen als een bulk bewerking. In de Azure Portal kunt u op twee manieren een groep toewijzen aan een beheer eenheid:

* In het deel venster **groepen** :

  1. Ga in de Azure Portal naar **Azure AD**.
  1. Selecteer **groepen** en selecteer vervolgens de groep die u aan de beheer eenheid wilt toewijzen. 
  1. Selecteer in het linkerdeel venster **beheer eenheden** om een lijst weer te geven van de beheer eenheden waaraan de groep is toegewezen. 

     ![Scherm afbeelding van de koppeling toewijzen aan beheer eenheid in het deel venster beheer eenheden.](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Selecteer **toewijzen aan beheer eenheid**.
  1. Selecteer de beheer eenheid in het rechterdeel venster.

* In het deel venster **beheer eenheden** van  >  **alle groepen** :

  1. Ga in de Azure Portal naar **Azure AD**.
  
  1. Selecteer **beheer eenheden** in het linkerdeel venster en selecteer vervolgens **alle groepen**. 
     Alle groepen die al zijn toegewezen aan de beheer eenheid, worden weer gegeven in het rechterdeel venster. 

  1. Selecteer in het deel venster **groepen** de optie **toevoegen**.
    In het rechterdeel venster worden alle beschik bare groepen in uw Azure AD-organisatie weer gegeven. 

     ![Scherm afbeelding van de knop toevoegen voor het toevoegen van een groep aan een beheer eenheid.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Selecteer een of meer groepen die u aan de beheer eenheid wilt toewijzen en selecteer vervolgens de knop **selecteren** .

### <a name="use-powershell"></a>PowerShell gebruiken

In het volgende voor beeld gebruikt u de `Add-AzureADMSAdministrativeUnitMember` cmdlet om de groep toe te voegen aan de beheer eenheid. De object-ID van de beheer eenheid en de object-ID van de groep die moet worden toegevoegd, worden als argumenten beschouwd. Wijzig de gemarkeerde sectie zoals vereist voor uw specifieke omgeving.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Voer de volgende opdrachten uit:

Aanvraag

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Hoofdtekst

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Voorbeeld

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Een lijst met groepen in een beheer eenheid weer geven

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in de Azure Portal naar **Azure AD**.

1. Selecteer **beheer eenheden** in het linkerdeel venster en selecteer vervolgens de beheer eenheid waarvan u de groepen wilt weer geven. Standaard is **alle gebruikers** geselecteerd in het linkerdeel venster. 

1. Selecteer **groepen** in het linkerdeel venster. In het rechterdeel venster wordt een lijst weer gegeven met groepen die lid zijn van de geselecteerde beheer eenheid.

   ![Scherm opname van het deel venster groepen waarin een lijst met groepen in een administratieve eenheid wordt weer gegeven.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit om een lijst met alle leden van de beheer eenheid weer te geven: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId
```

Als u alle groepen wilt weer geven die lid zijn van de beheer eenheid, gebruikt u het volgende code fragment:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Voer de volgende opdracht uit:

Aanvraag

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Hoofdtekst

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Een lijst met beheer eenheden voor een groep weer geven

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in de Azure Portal naar **Azure AD**.

1. Selecteer in het linkerdeel venster **groepen** om een lijst met groepen weer te geven.

1. Selecteer een groep om het profiel van de groep te openen. 

1. Selecteer in het linkerdeel venster **beheer eenheden** om alle administratieve eenheden weer te geven waarvan de groep lid is.

   ![Scherm opname van het deel venster "administratieve eenheden", waarin een lijst administratieve eenheden wordt weer gegeven waaraan een groep is toegewezen.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Voer de volgende opdracht uit:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Een groep verwijderen uit een beheer eenheid

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt op twee manieren een groep uit een beheer eenheid verwijderen in de Azure Portal:

- Verwijderen uit een groeps overzicht:

  1. Ga in de Azure Portal naar **Azure AD**.
  1. Selecteer **groepen** in het linkerdeel venster en open vervolgens het profiel voor de groep die u wilt verwijderen uit een beheer eenheid.
  1. Selecteer in het linkerdeel venster **beheer eenheden** om alle administratieve eenheden weer te geven waaraan de groep is toegewezen. 
  1. Selecteer de beheer eenheid waarvan u de groep wilt verwijderen en selecteer vervolgens **verwijderen uit beheer eenheid**.

     ![Scherm opname van het deel venster "administratieve eenheden", waarin een lijst met groepen wordt weer gegeven die zijn toegewezen aan de geselecteerde beheer eenheid.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Verwijderen uit een administratieve eenheid:

  1. Ga in de Azure Portal naar **Azure AD**.
  1. Selecteer **beheer eenheden** in het linkerdeel venster en selecteer vervolgens de beheer eenheid waaraan de groep is toegewezen.
  1. Selecteer in het linkerdeel venster **groepen** om alle groepen weer te geven die aan de beheer eenheid zijn toegewezen.
  1. Selecteer de groep die u wilt verwijderen en selecteer vervolgens **groepen verwijderen**.

    ![Scherm opname van het deel venster groepen, waarin een lijst met groepen in een administratieve eenheid wordt weer gegeven.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Voer de volgende opdracht uit:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een beheer eenheid](admin-units-assign-roles.md)
- [Gebruikers beheren in een beheer eenheid](admin-units-add-manage-users.md)
