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
ms.date: 12/07/2020
ms.author: memildin
ms.openlocfilehash: 5b257e45a86a7b22e9064fcfc6092b3c946ae99b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757632"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>Beheer groepen, abonnementen en zicht baarheid van de hele Tenant organiseren

In dit artikel wordt uitgelegd hoe u de beveiligings postuur van uw organisatie op schaal kunt beheren door beveiligings beleid toe te passen op alle Azure-abonnementen die zijn gekoppeld aan uw Azure Active Directory-Tenant.

Om inzicht te krijgen in de beveiligings postuur van alle abonnementen die in de Azure AD-Tenant zijn geregistreerd, moet een Azure-rol met voldoende Lees machtigingen worden toegewezen aan de hoofd beheer groep.


## <a name="organize-your-subscriptions-into-management-groups"></a>Uw abonnementen organiseren in beheer groepen

### <a name="introduction-to-management-groups"></a>Inleiding tot beheer groepen

Azure-beheer groepen bieden de mogelijkheid om op efficiënte wijze toegang, beleid en rapportage over groepen abonnementen te beheren, en het hele Azure-erfgoed effectief te beheren door acties uit te voeren op de hoofd beheer groep. U kunt abonnementen indelen in beheer groepen en uw beheer beleid Toep assen op de Management groepen. Alle abonnementen in een beheergroep nemen automatisch het beleid over dat op de beheergroep is toegepast. 

Elke Azure AD-Tenant krijgt één beheer groep op het hoogste niveau die de **hoofd beheer groep** heet. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met deze groep kunnen globale beleids regels en Azure-roltoewijzingen op mapniveau worden toegepast. 

De hoofd beheer groep wordt automatisch gemaakt wanneer u een van de volgende acties uitvoeren: 
- Open **beheergroepen** in het [Azure Portal](https://portal.azure.com).
- Maak een beheer groep met een API-aanroep.
- Maak een beheer groep met Power shell. Zie [beheer groepen maken voor resource-en organisatie beheer](../governance/management-groups/create-management-group-portal.md)voor instructies voor Power shell.

Er zijn geen beheer groepen nodig om Security Center uit te voeren, maar we raden u aan om ten minste één te maken zodat de hoofd beheer groep wordt gemaakt. Nadat de groep is gemaakt, worden alle abonnementen onder uw Azure AD-Tenant aan gekoppeld. 


Zie het artikel [uw resources organiseren met Azure-beheer groepen](../governance/management-groups/overview.md) voor een gedetailleerd overzicht van beheer groepen.

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Beheer groepen weer geven en maken in de Azure Portal

1. Gebruik in de [Azure Portal](https://portal.azure.com)het zoekvak in de bovenste balk om **beheergroepen** te zoeken en te openen.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Toegang tot uw beheer groepen":::

    De lijst met beheer groepen wordt weer gegeven.

1. Als u een beheer groep wilt maken, selecteert u **beheer groep toevoegen**, voert u de relevante gegevens in en selecteert u **Opslaan**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Een beheer groep toevoegen aan Azure":::

    - De **beheergroep-ID** is de unieke ID van de map die wordt gebruikt voor het verzenden van opdrachten in deze beheergroep. Deze id kan niet worden bewerkt nadat deze is gemaakt, omdat deze wordt gebruikt in het Azure-systeem om deze groep te identificeren. 
    - Het veld Weergavenaam is de naam die wordt weergegeven in de Azure-portal. Een afzonderlijke weergavenaam is een optioneel veld bij het maken van de beheergroep en kan op elk gewenst moment worden gewijzigd.  


### <a name="add-subscriptions-to-a-management-group"></a>Abonnementen aan een beheer groep toevoegen
U kunt abonnementen toevoegen aan de beheer groep die u hebt gemaakt.

1. Selecteer onder **beheergroepen** de beheer groep voor uw abonnement.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Selecteer een beheer groep voor uw abonnement":::

1. Wanneer de pagina van de groep wordt geopend, selecteert u **Details**

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="De pagina Details van een beheer groep openen":::

1. Selecteer op de pagina Details van de groep de optie **abonnement toevoegen**, selecteer uw abonnementen en selecteer vervolgens **Opslaan**. Herhaal deze stap totdat u alle abonnementen in het bereik hebt toegevoegd.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Een abonnement toevoegen aan een beheer groep":::
   > [!IMPORTANT]
   > Beheer groepen kunnen zowel abonnementen als onderliggende beheer groepen bevatten. Wanneer u een gebruiker van een Azure-rol aan de bovenliggende beheer groep toewijst, wordt de toegang overgenomen door de abonnementen van de onderliggende beheer groep. Beleids regels die zijn ingesteld voor de bovenliggende beheer groep, worden ook overgenomen door de onderliggende items. 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>Machtigingen voor de hele Tenant verlenen

Een gebruiker met de Azure Active Directory-rol **Globale beheerder** kan verantwoordelijkheden hebben voor de hele tenant, maar geen Azure-machtigingen om deze organisatiebrede informatie te bekijken in Azure Security Center. 

> [!TIP]
> Als uw organisatie toegang tot resources beheert met [Azure AD privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)of een ander PIM-hulp programma, moet de rol van globale beheerder actief zijn voor de gebruiker die deze wijzigingen aanbrengt.

Machtigingen op Tenant niveau toewijzen:

1. Als globale beheerder als gebruiker zonder een toewijzing in de hoofd beheer groep van de Tenant, opent u de **overzichts** pagina van Security Center en selecteert u de koppeling voor de **zicht baarheid voor de hele Tenant** in de banner. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Machtigingen op Tenant niveau inschakelen in Azure Security Center":::

1. Selecteer de nieuwe Azure-rol die u wilt toewijzen. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulier voor het definiëren van machtigingen op Tenant niveau die aan uw gebruiker moeten worden toegewezen":::

    > [!TIP]
    > Over het algemeen is de rol beveiligings beheerder vereist voor het Toep assen van beleid op het hoofd niveau, terwijl beveiligings lezers voldoende zijn om zicht baarheid op Tenant niveau te bieden. Voor meer informatie over de machtigingen die door deze rollen worden verleend, raadpleegt u de beschrijving van de [ingebouwde rol beveiligings beheerder](../role-based-access-control/built-in-roles.md#security-admin) of de [ingebouwde rol van beveiligings lezer](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Zie de tabel in [rollen en toegestane acties](security-center-permissions.md#roles-and-allowed-actions)voor verschillen tussen deze rollen die specifiek zijn voor Security Center.

    De organisatie-brede weer gave wordt bereikt door rollen toe te kennen op het niveau van de hoofd beheer groep van de Tenant.  

1. Meld u af bij de Azure Portal en meld u vervolgens opnieuw aan.

1. Zodra u toegang hebt tot verhoogde bevoegdheden, opent of vernieuwt u Azure Security Center om te controleren of u zicht hebt op alle abonnementen onder uw Azure AD-Tenant. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Machtigingen voor de hele Tenant aanvragen wanneer u niet voldoende bent

Als u zich aanmeldt bij Security Center en een banner ziet dat uw weer gave beperkt is, kunt u klikken om een aanvraag naar de globale beheerder voor uw organisatie te verzenden. In de aanvraag kunt u de rol toevoegen die u wilt toewijzen en de globale beheerder neemt een beslissing over welke rol moet worden verleend. 

Het is het besluit van de globale beheerder of u deze aanvragen accepteert of weigert. 

> [!IMPORTANT]
> U kunt slechts elke zeven dagen één aanvraag indienen.

Verhoogde machtigingen aanvragen bij de globale beheerder:

1. Open Azure Security Center vanuit het Azure Portal.

1. Als de banner ' u ziet beperkte informatie ' wordt weer gegeven. Selecteer deze.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Vaandel dat een gebruiker informeert, kan de machtigingen voor de hele Tenant aanvragen.":::

1. Selecteer in het formulier voor gedetailleerde aanvragen de gewenste rol en de reden waarom u deze machtigingen nodig hebt.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Details pagina voor het aanvragen van Tenant machtigingen van uw globale beheerder van Azure":::

1. Selecteer **toegang aanvragen**.

    Er wordt een e-mail bericht verzonden naar de globale beheerder. Het e-mail bericht bevat een koppeling naar Security Center waar ze de aanvraag kunnen goed keuren of afwijzen.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Een e-mail naar de globale beheerder verzenden voor nieuwe machtigingen":::

    Nadat de globale beheerder **de aanvraag** heeft geselecteerd en het proces heeft voltooid, wordt de beslissing verzonden naar de gebruiker die het verzoek heeft ingediend. 

## <a name="assign-azure-roles-to-other-users"></a>Azure-rollen toewijzen aan andere gebruikers

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Wijs Azure-rollen toe aan gebruikers via de Azure Portal: 
1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
1. Als u beheer groepen wilt weer geven, selecteert u **alle services** onder het hoofd menu van Azure en selecteert u vervolgens **beheergroepen**.
1.  Selecteer een beheer groep en selecteer **Details**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Scherm afbeelding van Beheergroepen Details":::

1. Selecteer **toegangs beheer (IAM)** en **roltoewijzingen**.
1. Selecteer **Roltoewijzing toevoegen**.
1. Selecteer de rol die u wilt toewijzen en de gebruiker en selecteer vervolgens **Opslaan**.  
   
   ![Scherm opname van rol van beveiligings lezer toevoegen](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Azure-rollen toewijzen aan gebruikers met Power shell: 
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

## <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen 

Zodra de Azure-functies aan de gebruikers zijn toegewezen, moet de Tenant beheerder zichzelf verwijderen van de rol beheerder van gebruikers toegang.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of het [Azure Active Directory beheer centrum](https://aad.portal.azure.com).

2. Selecteer in de navigatie lijst **Azure Active Directory** en selecteer vervolgens **Eigenschappen**.

3. Stel onder **toegangs beheer voor Azure-resources** de schakel optie in op **Nee**.

4. Selecteer **Opslaan** om de instelling op te slaan.



## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u de zicht baarheid van de hele Tenant kunt verkrijgen voor Azure Security Center. Zie voor verwante informatie:

- [Machtigingen in Azure Security Center](security-center-permissions.md)