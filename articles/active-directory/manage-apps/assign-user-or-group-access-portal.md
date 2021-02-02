---
title: Gebruikerstoewijzing beheren voor een app in Azure Active Directory
description: Meer informatie over het toewijzen en verwijderen van gebruikers en groepen voor een app met behulp van Azure Active Directory voor identiteits beheer.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50dcde478b708cd53d8229d70a9ddf4b1ff271be
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259731"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Gebruikerstoewijzing beheren voor een app in Azure Active Directory

In dit artikel wordt beschreven hoe u gebruikers en groepen toewijst aan bedrijfs toepassingen in Azure Active Directory (Azure AD), vanuit de Azure Portal of met behulp van Power shell. Wanneer u een gebruiker toewijst aan een toepassing, wordt de toepassing weer gegeven in [mijn apps](https://myapps.microsoft.com/) van de gebruiker voor eenvoudige toegang. Als de toepassing rollen beschikbaar maakt, kunt u ook een specifieke rol aan de gebruiker toewijzen.

Voor meer controle kunnen bepaalde typen bedrijfs toepassingen worden geconfigureerd voor het vereisen van een [gebruikers toewijzing](#configure-an-application-to-require-user-assignment). 

> [!IMPORTANT]
> Wanneer u een groep toewijst aan een toepassing, hebben alleen gebruikers in de groep toegang. De toewijzing verloopt niet trapsgewijs naar geneste groepen.

> [!NOTE]
> Voor een toewijzing op basis van een groep is de editie Azure Active Directory Premium P1 of P2 vereist. Toewijzing op basis van een groep wordt alleen ondersteund voor beveiligingsgroepen. Lidmaatschap van geneste groepen en Microsoft 365-groepen wordt momenteel niet ondersteund. Raadpleeg de [pagina met Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory) voor meer licentievereisten voor de functies die in dit artikel worden besproken. 

## <a name="configure-an-application-to-require-user-assignment"></a>Een toepassing configureren voor het vereisen van gebruikers toewijzing

Met de volgende typen toepassingen kunt u vereisen dat gebruikers aan de toepassing worden toegewezen voordat ze er toegang toe hebben:

- Toepassingen die zijn geconfigureerd voor federatieve eenmalige aanmelding (SSO) met verificatie op basis van SAML
- Toepassings proxy toepassingen die gebruikmaken van Azure Active Directory verificatie vooraf
- Toepassingen die zijn gebouwd op het Azure AD-toepassings platform die gebruikmaken van OAuth 2,0/OpenID Connect Connect-verificatie nadat een gebruiker of beheerder heeft ingestemd met die toepassing.

Wanneer de gebruikers toewijzing is vereist, kunnen alleen gebruikers die u expliciet aan de toepassing toewijst (via directe gebruikers toewijzing of op basis van groepslid maatschap) zich aanmelden. Ze hebben toegang tot de app op hun pagina mijn apps of met behulp van een directe koppeling. 

Wanneer de toewijzing *niet is vereist*, omdat u deze optie hebt ingesteld op **Nee** of omdat de toepassing een andere SSO-modus gebruikt, kan elke gebruiker toegang krijgen tot de toepassing als deze een rechtstreekse koppeling naar de toepassing of de **gebruikers toegangs-URL** hebben op de **Eigenschappen** pagina van de toepassing. 

Deze instelling heeft geen invloed op de vraag of een toepassing al dan niet wordt weer gegeven in mijn apps. Toepassingen worden weer gegeven op de toegangs Vensters van mijn apps van gebruikers zodra u een gebruiker of groep aan de toepassing hebt toegewezen. Zie [toegang tot apps beheren](what-is-access-management.md)voor achtergrond informatie.

Gebruikers toewijzing voor een toepassing vereisen:
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een beheerders account of als eigenaar van de toepassing.
2. Selecteer **Azure Active Directory**. Selecteer in het navigatie menu links de optie **bedrijfs toepassingen**.
3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, begint u met het invoeren van de naam in het zoekvak. Of gebruik de filter besturings elementen om het toepassings type, de status of de zicht baarheid te selecteren en selecteer vervolgens **Toep assen**.
4. Selecteer **Eigenschappen** in het navigatie menu links.
5. Zorg ervoor dat de **gebruikers toewijzing vereist is?** wissel knop is ingesteld op **Ja**.
   > [!NOTE]
   > Als de **gebruikers toewijzing is vereist?** wissel knop niet beschikbaar is, kunt u Power shell gebruiken om de eigenschap appRoleAssignmentRequired in te stellen voor de Service-Principal.
6. Selecteer de knop **Opslaan** boven aan het scherm.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Gebruikers en groepen voor een app toewijzen of de toewijzing ervan opheffen met behulp van de Azure Portal
Zie voor meer informatie over het toewijzen of verwijderen van een gebruiker of groep met behulp van de Azure Portal de [Snelstartgids serie op toepassings beheer](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Gebruikers en groepen voor een app toewijzen of de toewijzing ervan opheffen met behulp van de Graph API
U kunt de Graph API gebruiken om gebruikers en groepen voor een app toe te wijzen of te verwijderen. Zie [app-roltoewijzingen](/graph/api/resources/approleassignment)voor meer informatie.

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Gebruikers en groepen toewijzen aan een app met behulp van Power shell
1. Open een verhoogde Windows Power shell-opdracht prompt.
   > [!NOTE]
   > U moet de AzureAD-module installeren (gebruik de opdracht `Install-Module -Name AzureAD` ). Als u wordt gevraagd een NuGet-module of de nieuwe Azure Active Directory v2 Power shell-module te installeren, typt u j en drukt u op ENTER.
2. Voer uit `Connect-AzureAD` en meld u aan met een gebruikers account voor globale beheerders.
3. Gebruik het volgende script om een gebruiker en een rol toe te wijzen aan een toepassing:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Voor meer informatie over het toewijzen van een gebruiker aan een toepassingsrol raadpleegt u de documentatie voor [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

Als u een groep aan een bedrijfs-app wilt toewijzen, moet u vervangen door `Get-AzureADUser` `Get-AzureADGroup` en vervangen door `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

Zie de documentatie voor [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment)voor meer informatie over het toewijzen van een groep aan een toepassingsrol.

### <a name="example"></a>Voorbeeld

In dit voor beeld wordt de gebruiker Julia Simon toegewezen aan de [micro soft Workplace Analytics](https://products.office.com/business/workplace-analytics) -toepassing met behulp van Power shell.

1. Wijs in Power shell de overeenkomende waarden toe aan de variabelen $username, $app _name en $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. In dit voor beeld weten we niet wat de exacte naam is van de toepassingsrol die we aan Julia Simon willen toewijzen. Voer de volgende opdrachten uit om de gebruiker ($user) en de Service-Principal ($sp) op te halen met behulp van de UPN van de gebruiker en de weer gegeven namen van de Service-Principal.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Voer de opdracht uit `$sp.AppRoles` om de beschik bare functies voor de Application Analytics-toepassing weer te geven. In dit voor beeld willen we de rol analist (beperkte toegang) Julia Simon toewijzen.
   ![Toont de functies die beschikbaar zijn voor een gebruiker met behulp van de rol werk plek Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Wijs de rolnaam toe aan de `$app_role_name` variabele.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Voer de volgende opdracht uit om de gebruiker toe te wijzen aan de rol van de app:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>De toewijzing van gebruikers en groepen opheffen vanuit een app met behulp van Power shell

1. Open een verhoogde Windows Power shell-opdracht prompt.
   > [!NOTE]
   > U moet de AzureAD-module installeren (gebruik de opdracht `Install-Module -Name AzureAD` ). Als u wordt gevraagd een NuGet-module of de nieuwe Azure Active Directory v2 Power shell-module te installeren, typt u j en drukt u op ENTER.
2. Voer uit `Connect-AzureAD` en meld u aan met een gebruikers account voor globale beheerders.
3. Gebruik het volgende script om een gebruiker en een rol uit een toepassing te verwijderen:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>Verwante artikelen:

- [Meer informatie over toegang door eind gebruikers tot toepassingen](end-user-experiences.md)
- [Een Azure AD-implementatie van mijn apps plannen](my-apps-deployment-plan.md)
- [Toegang tot apps beheren](what-is-access-management.md)
 
## <a name="next-steps"></a>Volgende stappen

- [Alle groepen weer geven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een gebruiker of groeps toewijzing verwijderen uit een bedrijfs-app]()
- [Aanmeldingen van gebruikers voor een bedrijfs-app uitschakelen](disable-user-sign-in-portal.md)
- [De naam of het logo van een zakelijke app wijzigen](./add-application-portal-configure.md)
