---
title: Eigen aren en gebruikers toevoegen in Azure DevTest Labs | Microsoft Docs
description: Eigen aren en gebruikers toevoegen in Azure DevTest Labs met behulp van de Azure Portal of Power shell
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ca6a1ffa66ab294e34a1b4866953a393aba4d6d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511974"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Eigen aren en gebruikers toevoegen in Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Toegang in Azure DevTest Labs wordt beheerd door [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md). Met behulp van Azure RBAC kunt u de taken binnen uw team scheiden in *rollen* , waar u alleen de benodigde toegangs rechten voor gebruikers hebt voor het uitvoeren van hun taken. Drie van deze Azure-rollen zijn *eigenaar*, *DevTest Labs-gebruiker* en *Inzender*. In dit artikel vindt u informatie over de acties die kunnen worden uitgevoerd in elk van de drie belangrijkste Azure-rollen. Van daaruit leert u hoe u gebruikers kunt toevoegen aan een Lab: zowel via de portal als via een Power shell-script, en hoe u gebruikers toevoegt op abonnements niveau.

## <a name="actions-that-can-be-performed-in-each-role"></a>Acties die kunnen worden uitgevoerd in elke rol
Er zijn drie belang rijke rollen die u kunt toewijzen aan een gebruiker:

* Eigenaar
* DevTest Labs-gebruiker
* Inzender

In de volgende tabel ziet u de acties die kunnen worden uitgevoerd door gebruikers in elk van deze rollen:

| **Acties die gebruikers in deze rol kunnen uitvoeren** | **DevTest Labs-gebruiker** | **Eigenaar** | **Inzender** |
| --- | --- | --- | --- |
| **Lab-taken** | | | |
| Gebruikers toevoegen aan een Lab |Nee |Ja |Nee |
| Instellingen voor update kosten |Nee |Ja |Ja |
| **VM-basis taken** | | | |
| Aangepaste installatie kopieën toevoegen en verwijderen |Nee |Ja |Ja |
| Formules toevoegen, bijwerken en verwijderen |Ja |Ja |Ja |
| Marketplace-installatie kopieën inschakelen |Nee |Ja |Ja |
| **VM-taken** | | | |
| Virtuele machines maken |Ja |Ja |Ja |
| Vm's starten, stoppen en verwijderen |Alleen Vm's die door de gebruiker zijn gemaakt |Ja |Ja |
| VM-beleid bijwerken |Nee |Ja |Ja |
| Gegevens schijven toevoegen aan/verwijderen uit Vm's |Alleen Vm's die door de gebruiker zijn gemaakt |Ja |Ja |
| **Artefact taken** | | | |
| Artefact opslagplaatsen toevoegen en verwijderen |Nee |Ja |Ja |
| Artefacten Toep assen |Ja |Ja |Ja |

> [!NOTE]
> Wanneer een gebruiker een virtuele machine maakt, wordt die gebruiker automatisch toegewezen aan de rol **eigenaar** van de gemaakte virtuele machine.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Een eigenaar of gebruiker toevoegen op het niveau van het lab
U kunt eigen aren en gebruikers toevoegen op het niveau van de test omgeving via de Azure Portal. Een gebruiker kan een externe gebruiker met een geldig [Microsoft-account (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)zijn.
De volgende stappen leiden u door het proces van het toevoegen van een eigenaar of gebruiker aan een lab in Azure DevTest Labs:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het gewenste Lab.
4. Selecteer op de Blade van het lab de optie **configuratie en beleid**. 
5. Selecteer **toegangs beheer (IAM)** in het menu aan de linkerkant op de pagina **configuratie en beleid** . 
6. Selecteer **functie toewijzing toevoegen** op de werk balk om een gebruiker aan een rol toe te voegen.
1. Voer in het venster **machtigingen toevoegen** de volgende acties uit: 
    1. Selecteer een rol (bijvoorbeeld: DevTest Labs-gebruiker). De sectie [acties die kunnen worden uitgevoerd in elke rol](#actions-that-can-be-performed-in-each-role) , worden de verschillende acties die kunnen worden uitgevoerd door gebruikers in de rollen owner, DevTest gebruiker en Inzender.
    2. Selecteer de gebruiker die u aan de rol wilt toevoegen. 
    3. Selecteer **Opslaan**. 
11. Wanneer u terugkeert naar de Blade **gebruikers** , is de gebruiker toegevoegd.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Een externe gebruiker toevoegen aan een Lab met behulp van Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Naast het toevoegen van gebruikers in de Azure Portal, kunt u een externe gebruiker aan uw Lab toevoegen met behulp van een Power shell-script. In het volgende voor beeld wijzigt u de parameter waarden onder de opmerking **waarden die moeten worden gewijzigd** .
U kunt de `subscriptionId` waarden, `labResourceGroup` en, ophalen `labName` van de Blade lab in de Azure Portal.

> [!NOTE]
> In het voorbeeld script wordt ervan uitgegaan dat de opgegeven gebruiker is toegevoegd als een gast aan de Active Directory en mislukt als dat niet het geval is. Als u een gebruiker wilt toevoegen die niet aan een Lab Active Directory, gebruikt u de Azure Portal om de gebruiker aan een rol toe te wijzen, zoals aangegeven in de sectie, [een eigenaar of gebruiker toevoegen op het niveau van het lab](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Een eigenaar of gebruiker toevoegen op het abonnements niveau
Azure-machtigingen worden door gegeven vanuit het bovenliggende bereik naar een onderliggend bereik in Azure. Daarom zijn de eigen aren van een Azure-abonnement dat Labs bevat, automatisch eigenaar van deze Labs. Ze zijn ook eigenaar van de virtuele machines en andere resources die zijn gemaakt door de gebruikers van het lab en de Azure DevTest Labs-service. 

U kunt extra eigen aren toevoegen aan een lab via de Blade van het lab in de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Het beheer bereik van de toegevoegde eigenaar is echter meer beperkt dan het bereik van de eigenaar van het abonnement. De toegevoegde eigen aren hebben bijvoorbeeld geen volledige toegang tot een aantal van de resources die in het abonnement zijn gemaakt door de DevTest Labs-service. 

Voer de volgende stappen uit om een eigenaar aan een Azure-abonnement toe te voegen:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **alle services** en selecteer vervolgens **abonnementen** in de lijst.
3. Selecteer het gewenste abonnement.
4. Selecteer het pictogram **toegang** . 
   
    ![Gebruikers toegang](./media/devtest-lab-add-devtest-user/access-users.png)
5. Selecteer **toevoegen** op de Blade **gebruikers** .
   
    ![Gebruiker toevoegen](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Selecteer op de Blade **een rol selecteren** de optie **eigenaar**.
7. Voer op de Blade **gebruikers toevoegen** het e-mail adres of de naam in van de gebruiker die u wilt toevoegen als een eigenaar. Als de gebruiker niet kan worden gevonden, wordt er een fout bericht weer gegeven waarin het probleem wordt uitgelegd. Als de gebruiker wordt gevonden, wordt die gebruiker weer gegeven in het tekstvak **gebruiker** .
8. Selecteer de gevonden gebruikers naam.
9. Kies **Selecteren**.
10. Selecteer **OK** om de Blade **toegang toevoegen** te sluiten.
11. Wanneer u terugkeert naar de Blade **gebruikers** , is de gebruiker toegevoegd als een eigenaar. Deze gebruiker is nu een eigenaar van alle Labs die in dit abonnement is gemaakt, en kan dus eigenaars taken uitvoeren. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
