---
title: Zicht baarheid voor de hele Tenant verkrijgen voor Azure Security Center | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u uw beveiligings postuur op schaal kunt beheren door beleid toe te passen op alle abonnementen die aan uw Azure Active Directory-Tenant zijn gekoppeld.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 7252a6ccd77212f75f5db54e5f3fcad7aa2df50a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013799"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Zicht baarheid voor de hele Tenant verkrijgen voor Azure Security Center
In dit artikel wordt uitgelegd hoe u de beveiligings postuur van uw organisatie op schaal kunt beheren door beveiligings beleid toe te passen op alle Azure-abonnementen die zijn gekoppeld aan uw Azure Active Directory-Tenant.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Beheergroepen
Azure-beheer groepen bieden de mogelijkheid om op efficiënte wijze toegang, beleid en rapportage over groepen abonnementen te beheren, en het hele Azure-erfgoed effectief te beheren door acties uit te voeren op de hoofd beheer groep. Elke Azure AD-Tenant krijgt één beheer groep op het hoogste niveau die de hoofd beheer groep heet. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met deze groep kunnen globale beleids regels en Azure-roltoewijzingen op mapniveau worden toegepast. 

De hoofd beheer groep wordt automatisch gemaakt wanneer u een van de volgende acties uitvoeren: 
1. Meld u aan voor het gebruik van Azure-beheer groepen door te navigeren naar **beheergroepen** in het [Azure Portal](https://portal.azure.com).
2. Maak een beheer groep via een API-aanroep.
3. Maak een beheer groep met Power shell.

Zie het artikel [uw resources organiseren met Azure-beheer groepen](../governance/management-groups/overview.md) voor een gedetailleerd overzicht van beheer groepen.

## <a name="create-a-management-group-in-the-azure-portal"></a>Een beheer groep maken in de Azure Portal
U kunt abonnementen indelen in beheer groepen en uw beheer beleid Toep assen op de Management groepen. Alle abonnementen in een beheergroep nemen automatisch het beleid over dat op de beheergroep is toegepast. Hoewel beheer groepen niet vereist zijn voor de onboarding van Security Center, wordt het ten zeerste aanbevolen om ten minste één beheer groep te maken zodat de hoofd beheer groep wordt gemaakt. Nadat de groep is gemaakt, worden alle abonnementen onder uw Azure AD-Tenant aan gekoppeld. Zie [beheer groepen maken voor resource-en organisatie beheer](../governance/management-groups/create-management-group-portal.md)voor instructies voor Power shell en meer informatie.

 
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **alle services**-  >  **beheer groepen**.
3. Selecteer **nieuwe beheer groep** op de hoofd pagina. 

    ![Hoofd groep](./media/security-center-management-groups/main.png) 
4.  Vul het veld beheer groep-ID in. 
    - De **beheergroep-ID** is de unieke ID van de map die wordt gebruikt voor het verzenden van opdrachten in deze beheergroep. Deze id kan niet worden bewerkt nadat deze is gemaakt, omdat deze wordt gebruikt in het Azure-systeem om deze groep te identificeren. 
    - Het veld Weergavenaam is de naam die wordt weergegeven in de Azure-portal. Een afzonderlijke weergavenaam is een optioneel veld bij het maken van de beheergroep en kan op elk gewenst moment worden gewijzigd.  

      ![Maken](./media/security-center-management-groups/create_context_menu.png)  
5.  Selecteer **Opslaan**.

### <a name="view-management-groups-in-the-azure-portal"></a>Beheer groepen weer geven in de Azure Portal
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Als u beheer groepen wilt weer geven, selecteert u **alle services** onder het hoofd menu van Azure.
3. Selecteer onder **algemeen** **beheergroepen**.

    ![Een beheergroep maken](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Zicht baarheid op Tenant niveau en de mogelijkheid om beleid toe te wijzen

Om inzicht te krijgen in de beveiligings postuur van alle abonnementen die in de Azure AD-Tenant zijn geregistreerd, moet een Azure-rol met voldoende Lees machtigingen worden toegewezen aan de hoofd beheer groep.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Toegang verhogen voor een globale beheerder in Azure Active Directory
Een Azure Active Directory Tenant beheerder heeft geen rechtstreekse toegang tot Azure-abonnementen. Als Directory-beheerder hebben ze echter het recht om zichzelf te verhogen naar een rol die toegang heeft. Een Azure AD-Tenant beheerder moet zichzelf uitbreiden voor de beheerder van de gebruikers toegang op het niveau van de hoofd beheer groep, zodat ze Azure-rollen kunnen toewijzen. Zie [toegang verhogen voor een globale beheerder in azure Active Directory](../role-based-access-control/elevate-access-global-admin.md)voor instructies voor Power shell en aanvullende informatie. 


1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of het [Azure Active Directory beheer centrum](https://aad.portal.azure.com).

2. Klik in de navigatie lijst op **Azure Active Directory** en klik vervolgens op **Eigenschappen**.

   ![Eigenschappen van Azure AD-scherm opname](./media/security-center-management-groups/aad-properties.png)

3. Stel onder **toegangs beheer voor Azure-resources** de schakel optie in op **Ja**.

   ![Toegangs beheer voor Azure-resources-scherm afbeelding](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Wanneer u de schakel optie instelt op Ja, wordt de rol gebruikers toegangs beheerder toegewezen in azure RBAC in het hoofd bereik (/). Hiermee verleent u toestemming om rollen toe te wijzen aan alle Azure-abonnementen en-beheer groepen die zijn gekoppeld aan deze Azure AD-Directory. Deze schakel optie is alleen beschikbaar voor gebruikers aan wie de rol van globale beheerder is toegewezen in azure AD.

   - Wanneer u de switch instelt op Nee, wordt de rol beheerder voor gebruikers toegang in azure RBAC verwijderd uit uw gebruikers account. U kunt geen rollen meer toewijzen in alle Azure-abonnementen en-beheer groepen die zijn gekoppeld aan deze Azure AD-Directory. U kunt alleen de Azure-abonnementen en-beheer groepen weer geven en beheren waartoe u toegang hebt gekregen.

4. Klik op **Opslaan** om de instelling op te slaan.

    - Deze instelling is geen algemene eigenschap en is alleen van toepassing op de gebruiker die momenteel is aangemeld.

5. Voer de taken uit die u nodig hebt om toegang tot verhoogde bevoegdheden uit te voeren. Wanneer u klaar bent, stelt u de schakel optie weer in op **Nee**.


### <a name="assign-azure-roles-to-users"></a>Azure-rollen toewijzen aan gebruikers
Om inzicht te krijgen in alle abonnementen, moeten Tenant beheerders de juiste Azure-rol toewijzen aan alle gebruikers die ze willen toestaan zicht baarheid op Tenant niveau, met inbegrip van eigen inzicht, op hoofd beheer groepniveau. De aanbevolen rollen die u wilt toewijzen, zijn **beveiligings beheerder** of **beveiligings lezer**. Over het algemeen is de rol beveiligings beheerder vereist voor het Toep assen van beleid op het hoofd niveau, terwijl beveiligings lezers voldoende zijn om zicht baarheid op Tenant niveau te bieden. Voor meer informatie over de machtigingen die door deze rollen worden verleend, raadpleegt u de beschrijving van de [ingebouwde rol beveiligings beheerder](../role-based-access-control/built-in-roles.md#security-admin) of de [ingebouwde rol van beveiligings lezer](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Wijs Azure-rollen toe aan gebruikers via de Azure Portal: 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
1. Als u beheer groepen wilt weer geven, selecteert u **alle services** onder het hoofd menu van Azure en selecteert u vervolgens **beheergroepen**.
1.  Selecteer een beheer groep en klik op **Details**.

    ![Scherm afbeelding van Beheergroepen Details](./media/security-center-management-groups/management-group-details.PNG)
 
1. Klik op **toegangs beheer (IAM)** en **roltoewijzingen**.

1. Klik op **roltoewijzing toevoegen**.

1. Selecteer de rol die u wilt toewijzen en de gebruiker en klik vervolgens op **Opslaan**.  
   
   ![Scherm opname van rol van beveiligings lezer toevoegen](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-azure-roles-to-users-with-powershell"></a>Azure-rollen toewijzen aan gebruikers met Power shell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installeer [Azure PowerShell](/powershell/azure/install-az-ps).
2. Voer de volgende opdrachten uit: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Meld u aan met de referenties van de globale beheerder als u hierom wordt gevraagd. 

    ![Scherm opname van prompt voor aanmelden](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Ken machtigingen voor de rol van lezer toe door de volgende opdracht uit te voeren:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Als u de rol wilt verwijderen, gebruikt u de volgende opdracht: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Security Center openen of vernieuwen
Zodra u toegang hebt tot verhoogde bevoegdheden, opent of vernieuwt u Azure Security Center om te controleren of u zicht hebt op alle abonnementen onder uw Azure AD-Tenant. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Zorg ervoor dat u alle abonnementen selecteert in de abonnements kiezer die u wilt weer geven in Security Center.

    ![Scherm opname van de abonnements kiezer](./media/security-center-management-groups/subscription-selector.png)

1. Selecteer **alle services** onder het hoofd menu van Azure en selecteer vervolgens **Security Center**.
2. In het **overzicht** is er een dekkings grafiek voor abonnementen.

    ![Scherm opname van de abonnements dekkings grafiek](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Klik op **dekking** voor een overzicht van de gedekte abonnementen. 

    ![Scherm opname van de lijst met abonnements behoeften](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen 
Zodra de Azure-functies aan de gebruikers zijn toegewezen, moet de Tenant beheerder zichzelf verwijderen van de rol beheerder van gebruikers toegang.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of het [Azure Active Directory beheer centrum](https://aad.portal.azure.com).

2. Klik in de navigatie lijst op **Azure Active Directory** en klik vervolgens op **Eigenschappen**.

3. Stel onder **toegangs beheer voor Azure-resources** de schakel optie in op **Nee**.

4. Klik op **Opslaan** om de instelling op te slaan.



## <a name="adding-subscriptions-to-a-management-group"></a>Abonnementen aan een beheer groep toevoegen
U kunt abonnementen toevoegen aan de beheer groep die u hebt gemaakt. Deze stappen zijn niet verplicht voor het verkrijgen van zicht baarheid op Tenant niveau en globaal beleid en toegangs beheer.

1. Selecteer onder **beheergroepen** een beheer groep waaraan u uw abonnement wilt toevoegen.

    ![Selecteer een beheer groep om een abonnement toe te voegen aan](./media/security-center-management-groups/management-group-subscriptions.png)

2. Selecteer **bestaande toevoegen**.

    ![Bestaande toevoegen](./media/security-center-management-groups/add-existing.png)

3. Voer het abonnement in onder **bestaande resource toevoegen** en klik op **Opslaan**.

4. Herhaal stap 1 tot en met 3 totdat u alle abonnementen in het bereik hebt toegevoegd.

   > [!NOTE]
   > Beheer groepen kunnen zowel abonnementen als onderliggende beheer groepen bevatten. Wanneer u een gebruiker van een Azure-rol aan de bovenliggende beheer groep toewijst, wordt de toegang overgenomen door de abonnementen van de onderliggende beheer groep. Beleids regels die zijn ingesteld voor de bovenliggende beheer groep, worden ook overgenomen door de onderliggende items. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u de zicht baarheid van de hele Tenant kunt verkrijgen voor Azure Security Center. Zie de volgende artikelen voor meer informatie over Security Center:

> [!div class="nextstepaction"]
> [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)