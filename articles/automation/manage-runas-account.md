---
title: Een uitvoeren Azure Automation Uitvoeren als-account beheren
description: In dit artikel wordt beschreven hoe u uw Uitvoeren als-account beheert met PowerShell of vanuit Azure Portal.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e440a27c8f7778c800148feb5bec76ca5a48f4f5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833918"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Een uitvoeren Azure Automation Uitvoeren als-account beheren

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources op het Azure Resource Manager of het klassieke Azure-implementatiemodel met behulp van Automation-runbooks en andere Automation-functies. Dit artikel bevat richtlijnen voor het beheren van een Uitvoeren als- of Klassiek Uitvoeren als-account.

Zie Automation-accountverificatieoverzicht Azure Automation meer informatie over verificatie van uw account en richtlijnen met betrekking tot [procesautomatiseringsscenario's.](automation-security-overview.md)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Een zelf-ondertekend certificaat vernieuwen

Het zelf-ondertekende certificaat dat u hebt gemaakt voor het Uitvoeren als-account verloopt één jaar na de aanmaakdatum. Op een bepaald moment voordat uw Uitvoeren als-account verloopt, moet u het certificaat vernieuwen. U kunt deze op elk moment vernieuwen voordat deze verloopt.

Wanneer u het zelf-ondertekende certificaat vernieuwt, wordt het huidige geldige certificaat bewaard om ervoor te zorgen dat runbooks die in de wachtrij staan of actief worden uitgevoerd en die worden geverifieerd met het Uitvoeren als-account, niet negatief worden beïnvloed. Het certificaat blijft geldig tot de vervaldatum.

>[!NOTE]
>Als u denkt dat het Uitvoeren als-account is aangetast, kunt u het zelf-ondertekende certificaat verwijderen en opnieuw maken.

>[!NOTE]
>Als u uw Uitvoeren als-account hebt geconfigureerd voor het gebruik van een certificaat dat is uitgegeven door uw certificeringsinstantie voor ondernemingen en u de optie gebruikt om een zelf-ondertekend certificaat te vernieuwen, wordt het bedrijfscertificaat vervangen door een zelf-ondertekend certificaat.

Gebruik de volgende stappen om het zelf-ondertekende certificaat te vernieuwen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar uw Automation-account en selecteer **Uitvoeren als-accounts** in de sectie accountinstellingen.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Deelvenster Eigenschappen van Automation-account.":::

1. Selecteer op **de eigenschappenpagina Uitvoeren** als-accounts de optie Uitvoeren als-account of Klassiek Uitvoeren als-account, afhankelijk van het account waarvoor u het certificaat moet vernieuwen.  

1. Selecteer op **de** pagina Eigenschappen voor het geselecteerde account de optie **Certificaat vernieuwen.**

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Certificaat vernieuwen voor Uitvoeren als-account.":::

1. Terwijl het certificaat wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Machtigingen voor Uitvoeren als-account verlenen in andere abonnementen

Azure Automation ondersteunt het gebruik van één Automation-account uit één abonnement en het uitvoeren van runbooks voor Azure Resource Manager resources in meerdere abonnementen. Deze configuratie biedt geen ondersteuning voor het klassieke Azure-implementatiemodel.

U wijst de service-principal van het Uitvoeren als-account [de rol Inzender](../role-based-access-control/built-in-roles.md#contributor) toe in het andere abonnement of meer beperkende machtigingen. Zie Op rollen gebaseerd [toegangsbeheer](automation-role-based-access-control.md) in Azure Automation. Als u het Uitvoeren als-account wilt toewijzen aan de rol in het andere  abonnement, moet het gebruikersaccount dat deze taak moet uitvoeren, lid zijn van de rol Eigenaar in dat abonnement.

> [!NOTE]
> Deze configuratie ondersteunt alleen meerdere abonnementen van een organisatie die een gemeenschappelijke Azure AD-tenant gebruiken.

Voordat u de machtigingen voor het Uitvoeren als-account verleent, moet u eerst de weergavenaam noteren van de service-principal die u wilt toewijzen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer in uw Automation-account **Uitvoeren als-accounts** onder **Accountinstellingen.**
1. Selecteer **Azure Uitvoeren als-account.**
1. Kopieer of noteer de waarde voor **Weergavenaam** op de **pagina Uitvoeren als-account van Azure.**

Raadpleeg de volgende artikelen voor gedetailleerde stappen voor het toevoegen van roltoewijzingen, afhankelijk van de methode die u wilt gebruiken.

* [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Azure-rollen toewijzen met Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Azure-rollen toewijzen met behulp van de Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [Azure-rollen toewijzen met behulp van de REST API](..//role-based-access-control/role-assignments-rest.md)

Nadat u het Uitvoeren als-account aan de rol hebt toegewezen, geeft u in uw runbook op om de abonnementscontext in `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` te stellen op gebruik. Zie [Set-AzContext voor meer informatie.](/powershell/module/az.accounts/set-azcontext)

## <a name="limit-run-as-account-permissions"></a>Machtigingen voor Uitvoeren als-account beperken

Als u de targeting van Automation op resources in Azure wilt beheren, kunt u het script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) uitvoeren. Met dit script wordt uw bestaande Service-principal voor het Uitvoeren als-account gewijzigd om een aangepaste roldefinitie te maken en te gebruiken. De rol heeft machtigingen voor alle resources, [behalve Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Nadat u het script **Update-AutomationRunAsAccountRoleAssignments.ps1** uitgevoerd, werken runbooks die toegang Key Vault het gebruik van Uitvoeren als-accounts niet meer. Voordat u het script gaat uitvoeren, moet u runbooks in uw account controleren op aanroepen naar Azure Key Vault. Als u toegang tot Key Vault-Azure Automation runbooks wilt inschakelen, moet u het Uitvoeren als-account toevoegen aan [Key Vault machtigingen van de gebruiker.](#add-permissions-to-key-vault)

Als u verder wilt beperken wat de Run As-service-principal kan doen, kunt u andere resourcetypen toevoegen aan het `NotActions` element van de aangepaste roldefinitie. In het volgende voorbeeld wordt de toegang beperkt tot `Microsoft.Compute/*` . Als u dit resourcetype toevoegt aan voor de roldefinitie, heeft de rol geen toegang `NotActions` tot rekenresources. Zie Roldefinities voor [Azure-resources begrijpen voor meer informatie over roldefinities.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

U kunt bepalen of aan de service-principal die wordt gebruikt door uw Uitvoeren als-account de rol **Inzender** of een aangepaste rol is toegewezen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar uw Automation-account en selecteer **Uitvoeren als-accounts** in de sectie accountinstellingen.
1. Selecteer **Azure Uitvoeren als-account.**
1. Selecteer **Rol om** de roldefinitie te zoeken die wordt gebruikt.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Controleer de rol Uitvoeren als-account." lightbox="media/manage-runas-account/verify-role-expanded.png":::

U kunt ook de roldefinitie bepalen die wordt gebruikt door de Uitvoeren als-accounts voor meerdere abonnementen of Automation-accounts. Doe dit met behulp van [ hetCheck-AutomationRunAsAccountRoleAssignments.ps1script ](https://aka.ms/AA5hug5) in de PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Machtigingen toevoegen aan Key Vault

U kunt toestaan Azure Automation te controleren of Key Vault en de service-principal van uw Uitvoeren als-account een aangepaste roldefinitie gebruiken. U moet:

* Verleen machtigingen voor Key Vault.
* Stel het toegangsbeleid in.

U kunt het script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) in de PowerShell Gallery om uw Uitvoeren als-account machtigingen te verlenen voor Key Vault. Zie [Een Key Vault-toegangsbeleid toewijzen](../key-vault/general/assign-access-policy-powershell.md) voor meer informatie over het instellen van machtigingen voor Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Configuratieproblemen voor Uitvoeren als-accounts oplossen

Sommige configuratie-items die nodig zijn voor een Uitvoeren als- of Klassiek Uitvoeren als-account zijn mogelijk verwijderd of onjuist gemaakt tijdens de eerste installatie. Mogelijke exemplaren van een onjuiste configuratie zijn:

* Certificaatasset
* Verbindingsasset
* Uitvoeren als-account verwijderd uit de rol Inzender
* Service-principal of toepassing in Azure AD

Voor dergelijke exemplaren met onjuiste configuratie detecteert het Automation-account de wijzigingen en wordt de status Onvolledig weergegeven *in* het eigenschappenvenster Uitvoeren als-accounts voor het account.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Onvolledige Uitvoeren als-accountconfiguratie.":::

Wanneer u het Uitvoeren als-account selecteert, wordt in het deelvenster met accounteigenschappen het volgende foutbericht weergegeven:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

U kunt deze problemen met het Uitvoeren als-account snel oplossen door [het](delete-run-as-account.md) Uitvoeren [als-account](create-run-as-account.md) te verwijderen en opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen

* [Toepassingsobjecten en service-principalobjecten.](../active-directory/develop/app-objects-and-service-principals.md)
* [Overzicht van certificaten voor Azure Cloud Services.](../cloud-services/cloud-services-certs-create.md)
* Zie Een Uitvoeren als-account maken als u een Uitvoeren als-account wilt maken [of opnieuw wilt maken.](create-run-as-account.md)
* Zie Een Uitvoeren als-account verwijderen als u geen Uitvoeren als-account [meer hoeft te gebruiken.](delete-run-as-account.md)
