---
title: Gebruikers in een beheereenheid toevoegen, verwijderen en er een lijst van maken - Azure Active Directory | Microsoft Docs
description: Gebruikers en hun rolmachtigingen beheren in een beheereenheid in Azure Active Directory
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
ms.openlocfilehash: 101f1452f547f195288e2d22bc516880100c7735
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496823"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Gebruikers toevoegen en beheren in een beheereenheid in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u gebruikers toevoegen aan een beheereenheid voor een meer gedetailleerd beheerbereik.

Zie Aan de slag om het gebruik van PowerShell en Microsoft Graph voor beheer van [beheereenheden voor te bereiden.](admin-units-manage.md#get-started)

## <a name="add-users-to-an-administrative-unit"></a>Gebruikers toevoegen aan een beheereenheid

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt gebruikers afzonderlijk of als bulkbewerking toewijzen aan beheereenheden.

- Afzonderlijke gebruikers toewijzen vanuit een gebruikersprofiel:

   1. Meld u aan bij het [Azure AD-beheercentrum](https://portal.azure.com) met beheerdersmachtigingen voor bevoorrechte rollen.

   1. Selecteer **Gebruikers** en selecteer vervolgens de gebruiker die aan een beheereenheid moet worden toegewezen om het profiel van de gebruiker te openen.
   
   1. Selecteer **Beheereenheden.** 
   
   1. Als u de gebruiker wilt toewijzen aan  een of meer beheereenheden, selecteert u Toewijzen aan beheereenheid en selecteert u vervolgens in het rechterdeelvenster de beheereenheden waaraan u de gebruiker wilt toewijzen.

       ![Schermopname van het deelvenster Beheereenheden voor het toewijzen van een gebruiker aan een beheereenheid.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Afzonderlijke gebruikers toewijzen vanuit een beheereenheid:

   1. Meld u aan bij het [Azure AD-beheercentrum](https://portal.azure.com) met beheerdersmachtigingen voor bevoorrechte rollen.
   1. Selecteer **Beheereenheden** en selecteer vervolgens de beheereenheid waar de gebruiker moet worden toegewezen.
   1. Selecteer **Alle gebruikers,** selecteer **Lid** toevoegen  en selecteer vervolgens in het deelvenster Lid toevoegen een of meer gebruikers die u wilt toewijzen aan de beheereenheid.

        ![Schermopname van het deelvenster Gebruikers van de beheereenheid voor het toewijzen van een gebruiker aan een beheereenheid.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Gebruikers toewijzen als een bulkbewerking:

   1. Meld u aan bij het [Azure AD-beheercentrum](https://portal.azure.com) met beheerdersmachtigingen voor bevoorrechte rollen.

   1. Selecteer **Beheereenheden.**

   1. Selecteer de beheereenheid waaraan u gebruikers wilt toevoegen.

   1. Selecteer **Gebruikers**  >  **Bulkactiviteiten**  >  **Bulksgewijs leden toevoegen.** Vervolgens kunt u de csv-sjabloon (door komma's gescheiden waarden) downloaden en het bestand bewerken. De indeling is eenvoudig en er moet één user principal name elke regel worden toegevoegd. Nadat het bestand gereed is, kunt u het opslaan op een geschikte locatie en vervolgens uploaden als onderdeel van deze stap.

      ![Schermopname van het deelvenster Gebruikers voor het bulksgewijs toewijzen van gebruikers aan een beheereenheid.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Gebruik in PowerShell de `Add-AzureADAdministrativeUnitMember` cmdlet in het volgende voorbeeld om de gebruiker toe te voegen aan de beheereenheid. De object-id van de beheereenheid waaraan u de gebruiker wilt toevoegen en de object-id van de gebruiker die u wilt toevoegen, worden als argumenten gebruikt. Wijzig de gemarkeerde sectie zoals vereist voor uw specifieke omgeving.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

Vervang de tijdelijke aanduiding door testgegevens en voer de volgende opdracht uit:

Aanvraag

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Hoofdtekst

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

Voorbeeld

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Een lijst met beheereenheden voor een gebruiker weergeven

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

In de Azure Portal kunt u het profiel van een gebruiker als volgt openen:

1. Ga naar **Azure AD** en selecteer vervolgens **Gebruikers**.

1. Selecteer de gebruiker van wie u het profiel wilt weergeven.

1. Selecteer **Beheereenheden om** de lijst met beheereenheden weer te geven waaraan de gebruiker is toegewezen.

   ![Schermopname van beheereenheden waaraan een gebruiker is toegewezen.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Retourneert `Get-AzureADAdministrativeUnitMember` standaard slechts 100 leden van een beheereenheid. Als u meer leden wilt ophalen, kunt u `"-All $true"` toevoegen.

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

Vervang de tijdelijke aanduiding door testgegevens en voer de volgende opdracht uit:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Eén gebruiker uit een beheereenheid verwijderen

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt een gebruiker op twee manieren uit een beheereenheid verwijderen: 

* Ga in Azure Portal naar **Azure AD** en selecteer vervolgens **Gebruikers**. 
  1. Selecteer de gebruiker om het profiel van de gebruiker te openen. 
  1. Selecteer de beheereenheid waar u de gebruiker uit wilt verwijderen en selecteer **vervolgens Verwijderen uit beheereenheid**.

     ![Schermopname die laat zien hoe u een gebruiker uit een beheereenheid verwijdert uit het profieldeelvenster van de gebruiker.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Ga in Azure Portal naar **Azure AD** en selecteer vervolgens **Beheereenheden.**
  1. Selecteer de beheereenheid waar u de gebruiker uit wilt verwijderen. 
  1. Selecteer de gebruiker en selecteer vervolgens **Lid verwijderen.**
  
     ![Schermopname die laat zien hoe u een gebruiker verwijdert op het niveau van de beheereenheid.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Voer de volgende opdracht uit:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

Vervang de tijdelijke aanduidingen door testgegevens en voer de volgende opdracht uit:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Meerdere gebruikers verwijderen als bulkbewerking

Ga als volgt te werk om meerdere gebruikers uit een beheereenheid te verwijderen:

1. Ga in Azure Portal naar **Azure AD.**

1. Selecteer **Beheereenheden en** selecteer vervolgens de beheereenheid waar u gebruikers uit wilt verwijderen. 

1. Selecteer **Leden bulksgewijs** verwijderen en download vervolgens de CSV-sjabloon die u gaat gebruiken om de gebruikers weer te geven die u wilt verwijderen.

   ![Schermopname van de koppeling Leden bulksgewijs verwijderen in het deelvenster Gebruikers.](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Bewerk de gedownloade CSV-sjabloon met de relevante gebruikersgegevens. Verwijder de eerste twee rijen van de sjabloon niet. Voeg één user principal name (UPN) toe aan elke rij.

   ![Schermopname van een bewerkt CSV-bestand voor het bulksgewijs verwijderen van gebruikers uit een beheereenheid.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Sla uw wijzigingen op, upload het bestand en selecteer **vervolgens Verzenden.**

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een beheereenheid](admin-units-assign-roles.md)
- [Groepen toevoegen aan een beheereenheid](admin-units-add-manage-groups.md)
