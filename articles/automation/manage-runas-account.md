---
title: Een Azure Automation uitvoeren als-account beheren
description: In dit artikel leest u hoe u uw uitvoeren als-account kunt beheren met Power shell of via de Azure Portal.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f170fc948f136f4f46634e7ae2645ed2eb357afa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101096469"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Een Azure Automation uitvoeren als-account beheren

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources op het Azure Resource Manager of het klassieke Azure-implementatie model met behulp van Automation-runbooks en andere automatiserings functies. Dit artikel bevat richt lijnen voor het beheren van een uitvoeren als-of klassiek uitvoeren als-account.

Zie [overzicht van Automation-account verificatie](automation-security-overview.md)voor meer informatie over Azure Automation account verificatie en richt lijnen met betrekking tot proces automatiserings scenario's.

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Een zelfondertekend certificaat vernieuwen

Het zelfondertekende certificaat dat u voor het uitvoeren als-account hebt gemaakt, verloopt één jaar na de aanmaak datum. Op een bepaald moment voordat het run as-account verloopt, moet u het certificaat vernieuwen. U kunt deze op elk gewenst moment vernieuwen voordat het verloopt.

Wanneer u het zelfondertekende certificaat verlengt, wordt het huidige geldige certificaat bewaard om ervoor te zorgen dat alle runbooks die in de wachtrij zijn geplaatst of actief actief zijn, en die worden geverifieerd met het uitvoeren als-account, niet negatief worden beïnvloed. Het certificaat blijft geldig tot de vervaldatum.

>[!NOTE]
>Als u denkt dat het uitvoeren als-account is aangetast, kunt u het zelfondertekende certificaat verwijderen en opnieuw maken.

>[!NOTE]
>Als u uw uitvoeren als-account hebt geconfigureerd voor het gebruik van een certificaat dat is uitgegeven door de certificerings instantie van uw bedrijf en u de optie voor het vernieuwen van een zelfondertekend certificaat gebruikt, wordt het ondernemings certificaat vervangen door een zelfondertekend certificaat.

Gebruik de volgende stappen om het zelfondertekende certificaat te vernieuwen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar uw Automation-account en selecteer **uitvoeren als-accounts** in de sectie account instellingen.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Deel venster met eigenschappen van Automation-account.":::

1. Selecteer op de pagina **uitvoeren als-accounts** de optie **uitvoeren als-account** of **klassiek uitvoeren als-account** , afhankelijk van het account waarvoor u het certificaat wilt vernieuwen.

1. Selecteer **certificaat vernieuwen** op de pagina **Eigenschappen** voor het geselecteerde account.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Vernieuw het certificaat voor het uitvoeren als-account.":::

1. Terwijl het certificaat wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Machtigingen voor het uitvoeren als-account verlenen in andere abonnementen

Azure Automation ondersteunt het gebruik van één Automation-account uit één abonnement en voor het uitvoeren van runbooks op Azure Resource Manager resources in meerdere abonnementen. Deze configuratie biedt geen ondersteuning voor het klassieke Azure-implementatie model.

U wijst de rol van de run as-account service [in het andere](../role-based-access-control/built-in-roles.md#contributor) abonnement of meer beperkende machtigingen toe. Zie op [rollen gebaseerd toegangs beheer](automation-role-based-access-control.md) in azure Automation voor meer informatie. Om het uitvoeren als-account toe te wijzen aan de rol in het andere abonnement, moet het gebruikers account dat deze taak uitvoert, lid zijn van de rol **eigenaar** in dat abonnement.

> [!NOTE]
> Deze configuratie ondersteunt alleen meerdere abonnementen van een organisatie met behulp van een gemeen schappelijke Azure AD-Tenant.

Voordat u de machtigingen voor het uitvoeren als-account toewijst, moet u eerst de weergave naam van de Service-Principal noteren die u wilt toewijzen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer in uw Automation-account **uitvoeren als-accounts** onder **account instellingen**.
1. Selecteer een **uitvoeren als-account voor Azure**.
1. Kopieer of noteer de waarde voor **weergave naam** op de pagina **uitvoeren als-account van Azure** .

Raadpleeg de volgende artikelen voor gedetailleerde stappen voor het toevoegen van roltoewijzingen, afhankelijk van de methode die u wilt gebruiken.

* [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Azure-rollen toewijzen met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Azure-rollen toewijzen met behulp van Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [Azure-rollen toewijzen met behulp van de REST API](..//role-based-access-control/role-assignments-rest.md)

Nadat u het uitvoeren als-account aan de rol hebt toegewezen, geeft u in uw runbook op `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` dat u de context van het abonnement wilt gebruiken. Zie [set-AzContext](/powershell/module/az.accounts/set-azcontext)voor meer informatie.

## <a name="limit-run-as-account-permissions"></a>Machtigingen voor het uitvoeren als-account beperken

Als u het doel van automatisering wilt beheren op basis van resources in azure, kunt u het [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) script uitvoeren. Met dit script wordt de bestaande service-principal van het run as-account gewijzigd om een aangepaste roldefinitie te maken en te gebruiken. De rol heeft machtigingen voor alle resources, met uitzonde ring van [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Nadat u het **Update-AutomationRunAsAccountRoleAssignments.ps1** script hebt uitgevoerd, werken runbooks die toegang hebben tot Key Vault door het gebruik van run as-accounts niet meer. Voordat u het script uitvoert, moet u runbooks in uw account controleren op aanroepen naar Azure Key Vault. Als u toegang tot Key Vault van Azure Automation runbooks wilt inschakelen, moet u [het uitvoeren als-account toevoegen aan de machtigingen van Key Vault](#add-permissions-to-key-vault).

Als u de werking van de run as-Service-Principal verder wilt beperken, kunt u andere resource typen toevoegen aan het `NotActions` element van de definitie van de aangepaste rol. In het volgende voor beeld wordt de toegang beperkt tot `Microsoft.Compute/*` . Als u dit resource type toevoegt aan `NotActions` voor de roldefinitie, heeft de rol geen toegang tot een reken resource. Zie [inzicht in roldefinities voor Azure-resources](../role-based-access-control/role-definitions.md)voor meer informatie over functie definities.

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

U kunt bepalen of de service-principal die wordt gebruikt door het run as-account de rol **Inzender** of een aangepaste toewijzing heeft toegewezen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar uw Automation-account en selecteer **uitvoeren als-accounts** in de sectie account instellingen.
1. Selecteer een **uitvoeren als-account voor Azure**.
1. Selecteer **rol** om de roldefinitie te zoeken die wordt gebruikt.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Controleer de rol van het run as-account." lightbox="media/manage-runas-account/verify-role-expanded.png":::

U kunt ook de roldefinitie bepalen die wordt gebruikt door de run as-accounts voor meerdere abonnementen of Automation-accounts. Dit doet u met behulp van het [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) script in de PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Machtigingen toevoegen aan Key Vault

U kunt Azure Automation laten verifiëren of Key Vault en de service-principal van het run as-account een aangepaste roldefinitie gebruiken. U moet het volgende doen:

* Machtigingen verlenen aan Key Vault.
* Stel het toegangs beleid in.

U kunt het [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) script in de PowerShell Gallery gebruiken om uw uitvoeren als-account machtigingen te verlenen aan Key Vault. Zie [een Key Vault toegangs beleid toewijzen](../key-vault/general/assign-access-policy-powershell.md) voor meer informatie over het instellen van machtigingen op Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Onjuiste configuratie problemen oplossen voor Run as-accounts

Bepaalde configuratie-items die nodig zijn voor een uitvoeren als-of klassiek uitvoeren als-account, zijn mogelijk verwijderd of onjuist gemaakt tijdens de eerste installatie. Mogelijke instanties van een onjuiste configuratie zijn:

* Certificaatasset
* Verbindingsasset
* Het run as-account is verwijderd uit de rol Inzender
* Service-principal of toepassing in Azure AD

Voor dergelijke onjuiste configuratie-instanties detecteert het Automation-account de wijzigingen en geeft de status *onvolledig* weer in het deel venster Eigenschappen van run as-accounts voor het account.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="De configuratie van het run as-account is onvolledig.":::

Wanneer u het uitvoeren als-account selecteert, wordt het volgende fout bericht weer gegeven in het deel venster Eigenschappen van account:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

U kunt deze problemen met het run as-account snel oplossen door het uitvoeren als-account te [verwijderen](delete-run-as-account.md) en [opnieuw te maken](create-run-as-account.md) .

## <a name="next-steps"></a>Volgende stappen

* [Toepassings objecten en Service-Principal-objecten](../active-directory/develop/app-objects-and-service-principals.md).
* [Overzicht van certificaten voor Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
* Zie [een uitvoeren als-account maken](create-run-as-account.md)als u een uitvoeren als-account wilt maken of opnieuw wilt maken.
* Als u een uitvoeren als-account niet meer nodig hebt, raadpleegt u [een uitvoeren als-account verwijderen](delete-run-as-account.md).