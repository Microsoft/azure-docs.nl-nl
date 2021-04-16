---
title: Groepen in een beheereenheid toevoegen, verwijderen en Azure Active Directory | Microsoft Docs
description: Groepen en hun rolmachtigingen beheren in een beheereenheid in Azure Active Directory.
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
ms.openlocfilehash: 2ad8cce8375ecd670a481541a091e36aacb41240
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505289"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Groepen toevoegen en beheren in een beheereenheid in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u groepen toevoegen aan een beheereenheid voor een gedetailleerder beheerbereik.

Zie Aan de slag om het gebruik van PowerShell en Microsoft Graph voor beheer van [beheereenheden voor te bereiden.](admin-units-manage.md#get-started)

## <a name="add-groups-to-an-administrative-unit"></a>Groepen toevoegen aan een beheereenheid

U kunt groepen toevoegen aan een beheereenheid met behulp van Azure Portal, PowerShell of Microsoft Graph.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt alleen afzonderlijke groepen toewijzen aan een beheereenheid. Er is geen optie om groepen toe te wijzen als een bulkbewerking. In de Azure Portal kunt u op twee manieren een groep toewijzen aan een beheereenheid:

* In het **deelvenster** Groepen:

  1. Ga in Azure Portal naar **Azure AD.**
  1. Selecteer **Groepen** en selecteer vervolgens de groep die u wilt toewijzen aan de beheereenheid. 
  1. Selecteer in het linkerdeelvenster **Beheereenheden om** een lijst weer te geven met de beheereenheden aan welke groep is toegewezen. 

     ![Schermopname van de koppeling Toewijzen aan beheereenheid in het deelvenster Beheereenheden.](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Selecteer **Toewijzen aan beheereenheid.**
  1. Selecteer in het rechterdeelvenster de beheereenheid.

* In het **deelvenster Beheereenheden**  >  **Alle** groepen:

  1. Ga in Azure Portal naar **Azure AD.**
  
  1. Selecteer in het **linkerdeelvenster Beheereenheden** en selecteer vervolgens **Alle groepen.** 
     Groepen die al aan de beheereenheid zijn toegewezen, worden weergegeven in het rechterdeelvenster. 

  1. Selecteer in **het** deelvenster Groepen de optie **Toevoegen.**
    In het rechterdeelvenster worden alle beschikbare groepen in uw Azure AD-organisatie weergegeven. 

     ![Schermopname van de knop Toevoegen voor het toevoegen van een groep aan een beheereenheid.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Selecteer een of meer groepen die aan de beheereenheid moeten worden toegewezen en selecteer vervolgens de **knop** Selecteren.

### <a name="use-powershell"></a>PowerShell gebruiken

In het volgende voorbeeld gebruikt u de `Add-AzureADMSAdministrativeUnitMember` cmdlet om de groep toe te voegen aan de beheereenheid. De object-id van de beheereenheid en de object-id van de groep die moet worden toegevoegd, worden als argumenten gebruikt. Wijzig de gemarkeerde sectie zoals vereist voor uw specifieke omgeving.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

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

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Een lijst met groepen in een beheereenheid weergeven

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in Azure Portal naar **Azure AD.**

1. Selecteer in het linkerdeelvenster **Beheereenheden** en selecteer vervolgens de beheereenheid waarvan u de groepen wilt weergeven. Standaard is **Alle gebruikers** geselecteerd in het linkerdeelvenster. 

1. Selecteer groepen in het **linkerdeelvenster.** In het rechterdeelvenster wordt een lijst weergegeven met groepen die lid zijn van de geselecteerde beheereenheid.

   ![Schermopname van het deelvenster Groepen met een lijst met groepen in een beheereenheid.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit om een lijst met alle leden van de beheereenheid weer te geven: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id
```

Gebruik het volgende codefragment om alle groepen weer te geven die lid zijn van de beheereenheid:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

Voer de volgende opdracht uit:

Aanvraag

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Hoofdtekst

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Een lijst met beheereenheden voor een groep weergeven

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in Azure Portal naar **Azure AD.**

1. Selecteer in het linkerdeelvenster Groepen **om een** lijst met groepen weer te geven.

1. Selecteer een groep om het profiel van de groep te openen. 

1. Selecteer in het linkerdeelvenster **Beheereenheden om** alle beheereenheden weer te geven waar de groep lid van is.

   ![Schermopname van het deelvenster 'Beheereenheden', met een lijst met beheereenheden waar een groep aan is toegewezen.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

Voer de volgende opdracht uit:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Een groep uit een beheereenheid verwijderen

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt een groep op twee manieren uit een beheereenheid in Azure Portal verwijderen:

- Verwijder deze uit een groepsoverzicht:

  1. Ga in Azure Portal naar **Azure AD.**
  1. Selecteer in het linkerdeelvenster **Groepen** en open vervolgens het profiel voor de groep die u wilt verwijderen uit een beheereenheid.
  1. Selecteer in het linkerdeelvenster **Beheereenheden om** alle beheereenheden weer te geven die aan de groep zijn toegewezen. 
  1. Selecteer de beheereenheid waar u de groep uit wilt verwijderen en selecteer vervolgens **Verwijderen uit beheereenheid**.

     ![Schermopname van het deelvenster 'Beheereenheden', met een lijst met groepen die zijn toegewezen aan de geselecteerde beheereenheid.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Verwijder deze uit een beheereenheid:

  1. Ga in Azure Portal naar **Azure AD.**
  1. Selecteer in het **linkerdeelvenster Beheereenheden** en selecteer vervolgens de beheereenheid aan de groep die is toegewezen.
  1. Selecteer in het linkerdeelvenster **Groepen om** alle groepen weer te geven die zijn toegewezen aan de beheereenheid.
  1. Selecteer de groep die u wilt verwijderen en selecteer vervolgens **Groepen verwijderen.**

    ![Schermopname van het deelvenster Groepen, met een lijst met de groepen in een beheereenheid.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

Voer de volgende opdracht uit:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een beheereenheid](admin-units-assign-roles.md)
- [Gebruikers beheren in een beheereenheid](admin-units-add-manage-users.md)
