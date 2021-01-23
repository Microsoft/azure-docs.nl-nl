---
title: Gebruikers toevoegen, verwijderen en weer geven in een beheer eenheid-Azure Active Directory | Microsoft Docs
description: Gebruikers en machtigingen voor rollen beheren in een beheer eenheid in Azure Active Directory
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
ms.openlocfilehash: d3dc7b37c96d2d82ae42d9bce32a97beab2d91e9
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740513"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Gebruikers toevoegen en beheren in een beheer eenheid in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u gebruikers toevoegen aan een administratieve eenheid voor een nauw keurigere administratieve controle mogelijkheden.

Zie aan de [slag](admin-units-manage.md#get-started)voor het voorbereiden van het gebruik van Power shell en Microsoft Graph voor beheer van beheer eenheden.

## <a name="add-users-to-an-administrative-unit"></a>Gebruikers toevoegen aan een beheer eenheid

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt gebruikers afzonderlijk of als een bulk bewerking toewijzen aan beheer eenheden.

- Afzonderlijke gebruikers uit een gebruikers profiel toewijzen:

   1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com) met privileged Role Administrator Permissions.

   1. Selecteer **gebruikers** en selecteer vervolgens de gebruiker die aan een beheer eenheid moet worden toegewezen om het profiel van de gebruiker te openen.
   
   1. Selecteer **beheer eenheden**. 
   
   1. Als u de gebruiker aan een of meer administratieve eenheden wilt toewijzen, selecteert u **toewijzen aan beheer eenheid** en selecteert u vervolgens in het rechterdeel venster de beheer eenheden waaraan u de gebruiker wilt toewijzen.

       ![Scherm opname van het deel venster "administratieve eenheden" voor het toewijzen van een gebruiker aan een beheer eenheid.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Afzonderlijke gebruikers uit een beheer eenheid toewijzen:

   1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com) met privileged Role Administrator Permissions.
   1. Selecteer **beheer eenheden** en selecteer vervolgens de administratieve eenheid waaraan de gebruiker moet worden toegewezen.
   1. Selecteer **alle gebruikers**, selecteer **lid toevoegen** en selecteer vervolgens in het deel venster **lid toevoegen** een of meer gebruikers die u wilt toewijzen aan de beheer eenheid.

        ![Scherm opname van het deel venster "gebruikers" van de beheer eenheid voor het toewijzen van een gebruiker aan een beheer eenheid.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Gebruikers als een bulk bewerking toewijzen:

   1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com) met privileged Role Administrator Permissions.

   1. Selecteer **beheer eenheden**.

   1. Selecteer de beheer eenheid waaraan u gebruikers wilt toevoegen.

   1. Selecteer **gebruikers** bulksgewijs  >  **activiteiten** bulk  >  **leden toevoegen**. U kunt vervolgens de sjabloon met door komma's gescheiden waarden (CSV) downloaden en het bestand bewerken. De indeling is eenvoudig en er moet één user principal name worden toegevoegd aan elke regel. Sla het bestand op een geschikte locatie op en upload het als onderdeel van deze stap.

      ![Scherm opname van het deel venster ' gebruikers ' voor het toewijzen van gebruikers aan een beheer eenheid als een bulk bewerking.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Gebruik in Power shell de `Add-AzureADAdministrativeUnitMember` cmdlet in het volgende voor beeld om de gebruiker toe te voegen aan de beheer eenheid. De object-ID van de beheer eenheid waaraan u de gebruiker wilt toevoegen en de object-ID van de gebruiker die u wilt toevoegen, wordt als argumenten beschouwd. Wijzig de gemarkeerde sectie zoals vereist voor uw specifieke omgeving.

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Vervang de tijdelijke aanduiding door test informatie en voer de volgende opdracht uit:

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Voorbeeld:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Een lijst met beheer eenheden voor een gebruiker weer geven

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

In de Azure Portal kunt u een gebruikers profiel openen door het volgende te doen:

1. Ga naar **Azure AD** en selecteer vervolgens **gebruikers**.

1. Selecteer de gebruiker van wie u het profiel wilt weer geven.

1. Selecteer **beheer eenheden** om de lijst met beheer eenheden weer te geven waaraan de gebruiker is toegewezen.

   ![Scherm opname van administratieve eenheden waaraan een gebruiker is toegewezen.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
> [!NOTE]
> `Get-AzureADAdministrativeUnitMember`Retourneert standaard slechts 100 leden van een beheer eenheid. Als u meer leden wilt ophalen, kunt u deze toevoegen `"-All $true"` .

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Vervang de tijdelijke aanduiding door test informatie en voer de volgende opdracht uit:

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Eén gebruiker uit een beheer eenheid verwijderen

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt op twee manieren een gebruiker uit een administratieve eenheid verwijderen: 

* Ga in de Azure Portal naar **Azure AD** en selecteer vervolgens **gebruikers**. 
  1. Selecteer de gebruiker om het profiel van de gebruiker te openen. 
  1. Selecteer de beheer eenheid waarvan u de gebruiker wilt verwijderen en selecteer vervolgens **verwijderen uit beheer eenheid**.

     ![Scherm opname waarin wordt getoond hoe een gebruiker uit een administratieve eenheid moet worden verwijderd uit het profiel deel venster van de gebruiker.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Ga in de Azure Portal naar **Azure AD** en selecteer vervolgens **beheer eenheden**.
  1. Selecteer de beheer eenheid waarvan u de gebruiker wilt verwijderen. 
  1. Selecteer de gebruiker en selecteer **lid verwijderen**.
  
     ![Scherm afbeelding die laat zien hoe een gebruiker op het niveau van de beheer eenheid wordt verwijderd.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Vervang de tijdelijke aanduidingen door test informatie en voer de volgende opdracht uit:

`https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref`

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Meerdere gebruikers als een bulk bewerking verwijderen

Ga als volgt te werk om meerdere gebruikers uit een beheer eenheid te verwijderen:

1. Ga in de Azure Portal naar **Azure AD**.

1. Selecteer **beheer eenheden** en selecteer vervolgens de beheer eenheid waaruit u gebruikers wilt verwijderen. 

1. Selecteer **leden bulksgewijs verwijderen** en down load vervolgens de CSV-sjabloon die u wilt gebruiken om de gebruikers weer te geven die u wilt verwijderen.

   ![Scherm afbeelding met de koppeling ' leden bulksgewijs verwijderen ' in het deel venster ' gebruikers '.](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Bewerk de gedownloade CSV-sjabloon met de relevante gebruikers vermeldingen. Verwijder de eerste twee rijen van de sjabloon niet. Voeg één user principal name (UPN) toe aan elke rij.

   ![Scherm afbeelding van een bewerkte CSV-bestand voor het verwijderen van gebruikers uit een beheer eenheid in bulk.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Sla de wijzigingen op, upload het bestand en selecteer vervolgens **verzenden**.

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een beheer eenheid](admin-units-assign-roles.md)
- [Groepen toevoegen aan een beheer eenheid](admin-units-add-manage-groups.md)
