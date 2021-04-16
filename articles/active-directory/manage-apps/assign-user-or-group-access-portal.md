---
title: Gebruikerstoewijzing beheren voor een app in Azure Active Directory
description: Meer informatie over het toewijzen en verwijderen van gebruikers en groepen voor een app met behulp Azure Active Directory voor identiteitsbeheer.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: iangithinji
ms.reviewer: luleon
ms.openlocfilehash: 097bf55cc7e5372749240c19afb09ba374d437af
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377969"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Gebruikerstoewijzing beheren voor een app in Azure Active Directory

In dit artikel wordt beschreven hoe u gebruikers en groepen toewijst aan bedrijfstoepassingen in Azure Active Directory (Azure AD), vanuit de Azure Portal of met behulp van PowerShell. Wanneer u een gebruiker toewijst aan een toepassing, wordt de toepassing weergegeven in de Mijn apps [voor](https://myapps.microsoft.com/) eenvoudige toegang. Als de toepassing rollen bevat, kunt u ook een specifieke rol toewijzen aan de gebruiker.

Voor meer controle kunnen bepaalde typen bedrijfstoepassingen worden geconfigureerd om [gebruikerstoewijzing te vereisen.](#configure-an-application-to-require-user-assignment) 

> [!IMPORTANT]
> Wanneer u een groep toewijst aan een toepassing, hebben alleen gebruikers in de groep toegang. De toewijzing verloopt niet trapsgewijs naar geneste groepen.

> [!NOTE]
> Voor een toewijzing op basis van een groep is de editie Azure Active Directory Premium P1 of P2 vereist. Toewijzing op basis van een groep wordt alleen ondersteund voor beveiligingsgroepen. Lidmaatschap van geneste groepen en Microsoft 365-groepen wordt momenteel niet ondersteund. Raadpleeg de [pagina met Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory) voor meer licentievereisten voor de functies die in dit artikel worden besproken. 

## <a name="configure-an-application-to-require-user-assignment"></a>Een toepassing configureren om gebruikerstoewijzing te vereisen

Met de volgende typen toepassingen hebt u de mogelijkheid om te vereisen dat gebruikers aan de toepassing worden toegewezen voordat ze er toegang toe hebben:

- Toepassingen die zijn geconfigureerd voor federatief eenmalige aanmelding (SSO) met verificatie op basis van SAML
- toepassingsproxy toepassingen die gebruikmaken van Azure Active Directory verificatie vooraf
- Toepassingen die zijn gebouwd op het Azure AD-toepassingsplatform en die gebruikmaken van OAuth 2.0/OpenID Connect-verificatie nadat een gebruiker of beheerder toestemming heeft gegeven voor die toepassing.

Wanneer gebruikerstoewijzing is vereist, kunnen alleen de gebruikers die u expliciet aan de toepassing toewijst (via directe gebruikerstoewijzing of op basis van groepslidmaatschap) zich aanmelden. Ze hebben toegang tot de app op Mijn apps pagina of via een directe koppeling. 

Wanneer toewijzing niet *vereist* is, omdat u  deze optie hebt ingesteld op Nee of omdat de toepassing een andere modus voor eenmalige aanmelding gebruikt, heeft elke  gebruiker toegang tot de toepassing als deze een directe koppeling naar de toepassing of de **URL** voor gebruikerstoegang heeft op de pagina Eigenschappen van de toepassing. 

Deze instelling heeft geen invloed op of een toepassing wel of niet wordt weergegeven op Mijn apps. Toepassingen worden weergegeven op de Mijn apps van gebruikers zodra u een gebruiker of groep aan de toepassing hebt toegewezen. Zie Toegang tot [apps beheren voor achtergrondinformatie.](what-is-access-management.md)

Gebruikerstoewijzing vereisen voor een toepassing:
1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een beheerdersaccount of als eigenaar van de toepassing.
2. Selecteer **Azure Active Directory**. Selecteer Bedrijfstoepassingen in het navigatiemenu **aan de linkerkant.**
3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, typt u de naam in het zoekvak. Of gebruik de filterbesturingselementen om het toepassingstype, de status of zichtbaarheid te selecteren en selecteer vervolgens **Toepassen.**
4. Selecteer Eigenschappen in het navigatiemenu **aan de linkerkant.**
5. Zorg ervoor dat **de schakelknop Gebruikerstoewijzing vereist?** is ingesteld op **Ja.**
   > [!NOTE]
   > Als de schakelknop Gebruikerstoewijzing **vereist?** niet beschikbaar is, kunt u PowerShell gebruiken om de eigenschap appRoleAssignmentRequired in te stellen op de service-principal.
6. Selecteer de **knop** Opslaan bovenaan het scherm.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Gebruikers en groepen toewijzen of verwijderen voor een app met behulp van de Azure Portal
Zie de [Quickstart-reeks](add-application-portal-assign-users.md)over toepassingsbeheer voor meer informatie over het toewijzen of in de uitwijsen van een gebruiker Azure Portal groep met behulp van de Azure Portal.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Gebruikers en groepen toewijzen of verwijderen voor een app met behulp van de Graph API
U kunt de Graph API om gebruikers en groepen toe te wijzen aan of uit te wijzen voor een app. Zie App-roltoewijzingen [voor meer informatie.](/graph/api/resources/approleassignment)

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Gebruikers en groepen toewijzen aan een app met behulp van PowerShell
1. Open een opdrachtprompt met verhoogde Windows PowerShell opdrachtprompt.
   > [!NOTE]
   > U moet de AzureAD-module installeren (gebruik de opdracht `Install-Module -Name AzureAD` ). Als u wordt gevraagd om een NuGet-module of de nieuwe powershell Azure Active Directory V2-module te installeren, typt u Y en drukt u op ENTER.
2. Voer `Connect-AzureAD` uit en meld u aan met een globale beheerdersaccount.
3. Gebruik het volgende script om een gebruiker en rol toe te wijzen aan een toepassing:

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
Zie de documentatie voor [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment)voor meer informatie over het toewijzen van een gebruiker aan een toepassingsrol.

Als u een groep wilt toewijzen aan een bedrijfs-app, moet u `Get-AzureADUser` vervangen door en vervangen door `Get-AzureADGroup` `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

Zie de documentatie voor [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment)voor meer informatie over het toewijzen van een groep aan een toepassingsrol.

### <a name="example"></a>Voorbeeld

In dit voorbeeld wijst u de gebruiker Britta Simon toe aan de [Microsoft Workplace Analytics-toepassing](https://products.office.com/business/workplace-analytics) met behulp van PowerShell.

1. Wijs in PowerShell de bijbehorende waarden toe aan de variabelen $username, $app_name en $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. In dit voorbeeld weten we niet wat de exacte naam is van de toepassingsrol die we aan Britta Simon willen toewijzen. Voer de volgende opdrachten uit om de gebruiker ($user) en de service-principal ($sp) op te halen met behulp van de UPN van de gebruiker en de weergavenamen van de service-principal.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Voer de opdracht uit `$sp.AppRoles` om de rollen weer te geven die beschikbaar zijn voor de Workplace Analytics-toepassing. In dit voorbeeld willen we Britta Simon de rol Analist (beperkte toegang) toewijzen.
   ![Toont de rollen die beschikbaar zijn voor een gebruiker met workplace analytics-rol](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Wijs de rolnaam toe aan de `$app_role_name` variabele .

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Voer de volgende opdracht uit om de gebruiker toe te wijzen aan de app-rol:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Gebruikers en groepen uit een app verwijderen met behulp van PowerShell

1. Open een opdrachtprompt met verhoogde Windows PowerShell opdrachtprompt.
   > [!NOTE]
   > U moet de AzureAD-module installeren (gebruik de opdracht `Install-Module -Name AzureAD` ). Als u wordt gevraagd om een NuGet-module of de nieuwe powershell Azure Active Directory V2-module te installeren, typt u Y en drukt u op ENTER.
2. Voer `Connect-AzureAD` uit en meld u aan met een globale beheerdersaccount.
3. Gebruik het volgende script om een gebruiker en rol uit een toepassing te verwijderen:

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

- [Meer informatie over toegang van eindgebruikers tot toepassingen](end-user-experiences.md)
- [Een Azure AD-Mijn apps plannen](my-apps-deployment-plan.md)
- [Toegang tot apps beheren](what-is-access-management.md)
 
## <a name="next-steps"></a>Volgende stappen

- [Al mijn groepen bekijken](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een gebruikers- of groepstoewijzing verwijderen uit een bedrijfs-app]()
- [Aanmeldingen van gebruikers uitschakelen voor een bedrijfs-app](disable-user-sign-in-portal.md)
- [De naam of het logo van een bedrijfs-app wijzigen](./add-application-portal-configure.md)
