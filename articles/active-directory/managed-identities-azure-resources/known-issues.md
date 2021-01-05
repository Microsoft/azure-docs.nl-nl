---
title: Veelgestelde vragen en bekende problemen met beheerde identiteiten-Azure AD
description: Bekende problemen met beheerde identiteiten voor Azure-resources.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/01/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 2be66904898ecdf2006952f5e80c17dc78b81c06
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825806"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (FAQ)

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hoe kunt u resources vinden die een beheerde identiteit hebben?

U kunt de lijst met resources die een door het systeem toegewezen beheerde identiteit hebben, vinden met behulp van de volgende Azure CLI-opdracht: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>Hebben beheerde identiteiten een object voor een back-uptoepassing?

Nee. Beheerde identiteiten en Azure AD-app registraties zijn niet hetzelfde als in de Directory. 

App-registraties hebben twee onderdelen: een toepassings object + een Service-Principal-object. Beheerde identiteiten voor Azure-resources hebben slechts een van deze onderdelen: een Service-Principal-object. 

Beheerde identiteiten hebben geen toepassings object in de map, wat vaak wordt gebruikt om app-machtigingen voor MS Graph te verlenen. In plaats daarvan moeten de MS Graph-machtigingen voor beheerde identiteiten rechtstreeks aan de service-principal worden verleend.  

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Werken beheerde identiteiten voor Azure-resources met Azure Cloud Services?

Nee, er zijn geen plannen voor het ondersteunen van beheerde identiteiten voor Azure-resources in azure Cloud Services.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Wat is de referentie die is gekoppeld aan een beheerde identiteit? Hoe lang is het geldig en hoe vaak het draait?

> [!NOTE]
> De verificatie van beheerde identiteiten is een intern implementatie detail dat kan zonder kennisgeving worden gewijzigd.

Beheerde identiteiten gebruiken verificatie op basis van certificaten. De referenties van elke beheerde identiteit hebben een verval van 90 dagen en worden na 45 dagen opgerold.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Wat is de beveiligings grens van beheerde identiteiten voor Azure-resources?

De beveiligings grens van de identiteit is de resource waaraan deze is gekoppeld. Zo is de beveiligings grens voor een virtuele machine met beheerde identiteiten voor Azure-resources ingeschakeld, de virtuele machine. Alle code die op die virtuele machine wordt uitgevoerd, kan de beheerde identiteiten voor het eind punt van Azure-resources aanroepen en tokens aanvragen. Het is een vergelijk bare ervaring met andere resources die beheerde identiteiten voor Azure-resources ondersteunen.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Met welke identiteit wordt de IMDS standaard ingesteld als de identiteit in de aanvraag niet wordt opgegeven?

- Als door het systeem toegewezen beheerde identiteit is ingeschakeld en er geen identiteit is opgegeven in de aanvraag, wordt IMDS standaard ingesteld op de door het systeem toegewezen beheerde identiteit.
- Als door het systeem toegewezen beheerde identiteit niet is ingeschakeld en er slechts één door de gebruiker toegewezen beheerde identiteit bestaat, wordt IMDS standaard ingesteld op de door de gebruiker toegewezen beheerde identiteit. 
- Als door het systeem toegewezen beheerde identiteit niet is ingeschakeld en er meerdere door de gebruiker toegewezen beheerde identiteiten bestaan, is het opgeven van een beheerde identiteit in de aanvraag vereist.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Worden beheerde identiteiten automatisch opnieuw gemaakt als ik een abonnement naar een andere map Verplaats?

Nee. Als u een abonnement naar een andere map verplaatst, moet u deze hand matig opnieuw maken en Azure-roltoewijzingen opnieuw verlenen.
- Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
- Voor door de gebruiker toegewezen beheerde identiteiten: verwijderen, opnieuw maken en opnieuw koppelen aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan ik een beheerde identiteit gebruiken om toegang te krijgen tot een resource in een andere Directory/Tenant?

Nee. Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Welke Azure RBAC-machtigingen zijn vereist voor de beheerde identiteit van een resource? 

- Door het systeem toegewezen beheerde identiteit: u hebt schrijf machtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Deze actie is opgenomen in resource-specifieke ingebouwde rollen als [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Door de gebruiker toegewezen beheerde identiteit: u hebt schrijf machtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Naast de roltoewijzing voor [beheerde identiteits operatoren](../../role-based-access-control/built-in-roles.md#managed-identity-operator) via de beheerde identiteit.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Hoe kan ik voor komen dat door de gebruiker toegewezen beheerde identiteiten worden gemaakt?

U kunt ervoor zorgen dat uw gebruikers door de gebruiker toegewezen beheerde identiteiten maken met behulp van [Azure Policy](../../governance/policy/overview.md)

- Ga naar het [Azure Portal](https://portal.azure.com) en ga naar **beleid**.
- **Definities** kiezen
- Selecteer **+ beleids definitie** en voer de benodigde gegevens in.
- In de sectie beleids regel plakken

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {}
}

```

Nadat u het beleid hebt gemaakt, wijst u het toe aan de resource groep die u wilt gebruiken.

- Navigeer naar resource groepen.
- Zoek de resource groep die u gebruikt voor het testen.
- Kies **beleid** in het menu links.
- **Beleid toewijzen** selecteren
- In de sectie **basis beginselen** vindt u het volgende:
    - **Bereik** De resource groep die wordt gebruikt voor het testen van
    - **Beleids definitie**: het beleid dat we eerder hebben gemaakt.
- Laat alle andere instellingen op de standaard waarden staan en kies **controleren + maken** .

Op dit moment mislukt elke poging om een door de gebruiker toegewezen beheerde identiteit in de resource groep te maken.

  ![Beleids schending](./media/known-issues/policy-violation.png)

## <a name="known-issues"></a>Bekende problemen

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Automatiserings script mislukt bij het proberen van schema export voor beheerde identiteiten voor Azure-bronnen uitbreiding

Wanneer beheerde identiteiten voor Azure-resources zijn ingeschakeld op een virtuele machine, wordt de volgende fout weer gegeven wanneer u probeert de functie Automation script te gebruiken voor de virtuele machine of de bijbehorende resource groep:

![Export fout van beheerde identiteiten voor het automatiserings script van Azure-resources](./media/msi-known-issues/automation-script-export-error.png)

De beheerde identiteiten voor de VM-extensie van Azure resources (gepland voor afschaffing in januari 2019) biedt momenteel geen ondersteuning voor de mogelijkheid om het schema te exporteren naar een sjabloon voor een resource groep. Als gevolg hiervan worden door de gegenereerde sjabloon geen configuratie parameters weer gegeven voor het inschakelen van beheerde identiteiten voor Azure-resources op de resource. Deze secties kunnen hand matig worden toegevoegd met behulp van de voor beelden in [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met een sjabloon](qs-configure-template-windows-vm.md).

Wanneer de functie voor het exporteren van het schema beschikbaar wordt voor de VM-extensie Managed Identities voor Azure resources (gepland voor afschaffing in januari 2019), wordt deze weer gegeven bij het [exporteren van resource groepen die VM-extensies bevatten](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>De virtuele machine kan niet worden gestart na het verplaatsen van de resource groep of het abonnement

Als u een virtuele machine met de status actief verplaatst, blijft deze tijdens de verplaatsing actief. Als de virtuele machine eenmaal is gestopt en opnieuw wordt gestart, kan deze echter niet worden gestart. Dit probleem doet zich voor omdat de virtuele machine de verwijzing naar de beheerde identiteiten voor de Azure-resources-identiteit niet bijwerkt en blijft verwijzen naar deze in de oude resource groep.

**Tijdelijke oplossing** 
 
Activeer een update op de VM zodat deze de juiste waarden kan ophalen voor de beheerde identiteiten voor Azure-resources. U kunt een wijziging van de VM-eigenschap uitvoeren om de verwijzing naar de beheerde identiteiten voor de Azure-resources-identiteit bij te werken. U kunt bijvoorbeeld een nieuwe label waarde op de VM instellen met de volgende opdracht:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Met deze opdracht wordt een nieuwe tag ' fixVM ' ingesteld met de waarde 1 in de virtuele machine. 
 
Door deze eigenschap in te stellen, wordt de VM bijgewerkt met de juiste beheerde identiteiten voor de resource-URI van Azure resources. vervolgens moet u de virtuele machine kunnen starten. 
 
Zodra de VM is gestart, kan de tag worden verwijderd met behulp van de volgende opdracht:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Een abonnement verplaatsen tussen Azure AD-directory's

Beheerde identiteiten worden niet bijgewerkt wanneer een abonnement wordt verplaatst of overgedragen naar een andere map. Als gevolg hiervan worden bestaande door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteiten verbroken. 

Tijdelijke oplossing voor beheerde identiteiten in een abonnement dat is verplaatst naar een andere map:

 - Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
 - Voor door de gebruiker toegewezen beheerde identiteiten: verwijderen, opnieuw maken en opnieuw koppelen aan de benodigde resources (bijvoorbeeld virtuele machines)

Raadpleeg [Een Azure-abonnement overzetten naar een andere Azure AD-map](../../role-based-access-control/transfer-subscription.md) voor meer informatie.

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Een door de gebruiker toegewezen beheerde identiteit verplaatsen naar een andere resource groep of een ander abonnement

Het verplaatsen van een door de gebruiker toegewezen beheerde identiteit naar een andere resource groep wordt niet ondersteund.
