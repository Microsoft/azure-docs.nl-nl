---
title: Veelgestelde vragen over beheerde identiteiten voor Azure-bronnen-Azure AD
description: Veelgestelde vragen over beheerde identiteiten
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 3d3ab9859eb9f85d5ca7d0573fa79443ae9fe964
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250986"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Veelgestelde vragen over beheerde identiteiten voor Azure-bronnen-Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

## <a name="administration"></a>Beheer

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hoe kunt u resources vinden die een beheerde identiteit hebben?

U kunt de lijst met resources die een door het systeem toegewezen beheerde identiteit hebben, vinden met behulp van de volgende Azure CLI-opdracht: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Welke Azure RBAC-machtigingen zijn vereist voor de beheerde identiteit van een resource? 

- Door het systeem toegewezen beheerde identiteit: u hebt schrijf machtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Deze actie is opgenomen in resource-specifieke ingebouwde rollen als [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Door de gebruiker toegewezen beheerde identiteit: u hebt schrijf machtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Naast de roltoewijzing voor [beheerde identiteits operatoren](../../role-based-access-control/built-in-roles.md#managed-identity-operator) via de beheerde identiteit.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Hoe kan ik voor komen dat door de gebruiker toegewezen beheerde identiteiten worden gemaakt?

U kunt ervoor zorgen dat uw gebruikers door de gebruiker toegewezen beheerde identiteiten maken met behulp van [Azure Policy](../../governance/policy/overview.md)

1. Ga naar het [Azure Portal](https://portal.azure.com) en ga naar **beleid**.
2. **Definities** kiezen
3. Selecteer **+ beleids definitie** en voer de benodigde gegevens in.
4. In de sectie beleids regel plakken
    
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

1. Navigeer naar resource groepen.
2. Zoek de resource groep die u gebruikt voor het testen.
3. Kies **beleid** in het menu links.
4. **Beleid toewijzen** selecteren
5. Geef in de sectie **basis beginselen** het volgende op:
    1. **Bereik** De resource groep die wordt gebruikt voor het testen van
    1. **Beleids definitie**: het beleid dat we eerder hebben gemaakt.
6. Laat alle andere instellingen op de standaard waarden staan en kies **controleren + maken** .

Op dit moment zullen pogingen om een door de gebruiker toegewezen beheerde identiteit in de resource groep te maken, mislukken.

  ![Beleids schending](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Concepten

### <a name="do-managed-identities-have-a-backing-app-object"></a>Hebben beheerde identiteiten een object voor een back-uptoepassing?

Nee. Beheerde identiteiten en Azure AD-app registraties zijn niet hetzelfde als in de Directory.

App-registraties hebben twee onderdelen: een toepassings object + een Service-Principal-object.
Beheerde identiteiten voor Azure-resources hebben slechts een van deze onderdelen: een Service-Principal-object.

Beheerde identiteiten hebben geen toepassings object in de map, wat vaak wordt gebruikt om app-machtigingen voor MS Graph te verlenen. In plaats daarvan moeten de MS Graph-machtigingen voor beheerde identiteiten rechtstreeks aan de service-principal worden verleend.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Wat is de referentie die is gekoppeld aan een beheerde identiteit? Hoe lang is het geldig en hoe vaak het draait?

> [!NOTE]
> De verificatie van beheerde identiteiten is een intern implementatie detail dat kan zonder kennisgeving worden gewijzigd.

Beheerde identiteiten gebruiken verificatie op basis van certificaten. De referenties van elke beheerde identiteit hebben een verval van 90 dagen en worden na 45 dagen opgerold.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Met welke identiteit wordt de IMDS standaard ingesteld als de identiteit in de aanvraag niet wordt opgegeven?

- Als door het systeem toegewezen beheerde identiteit is ingeschakeld en er geen identiteit is opgegeven in de aanvraag, IMDS standaard ingesteld op de door het systeem toegewezen beheerde identiteit.
- Als door het systeem toegewezen beheerde identiteit niet is ingeschakeld en er slechts één door de gebruiker toegewezen beheerde identiteit bestaat, IMDS standaard ingesteld op de door de gebruiker toegewezen beheerde identiteit.
- Als door het systeem toegewezen beheerde identiteit niet is ingeschakeld en er meerdere door de gebruiker toegewezen beheerde identiteiten bestaan, moet u een beheerde identiteit opgeven in de aanvraag.

## <a name="limitations"></a>Beperkingen

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Kan dezelfde beheerde identiteit worden gebruikt in meerdere regio's?

Kortom, ja, u kunt door de gebruiker toegewezen beheerde identiteiten gebruiken in meer dan één Azure-regio. Het langere antwoord is dat terwijl door de gebruiker toegewezen beheerde identiteiten worden gemaakt als regionale resources de bijbehorende [Service-Principal](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) die is gemaakt in azure AD is wereld wijd beschikbaar. De service-principal kan vanuit elke Azure-regio worden gebruikt en de beschik baarheid ervan is afhankelijk van de beschik baarheid van Azure AD. Als u bijvoorbeeld een door de gebruiker toegewezen beheerde identiteit in de South-Central regio hebt gemaakt en deze regio niet beschikbaar is, is dit probleem alleen van invloed op de [beheer](../../azure-resource-manager/management/control-plane-and-data-plane.md) activiteiten op de beheerde identiteit zelf.  De activiteiten die worden uitgevoerd door resources die al zijn geconfigureerd voor het gebruik van de beheerde identiteiten, worden niet beïnvloed.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Werken beheerde identiteiten voor Azure-resources met Azure Cloud Services?

Nee, er zijn geen plannen voor het ondersteunen van beheerde identiteiten voor Azure-resources in azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Wat is de beveiligings grens van beheerde identiteiten voor Azure-resources?

De beveiligings grens van de identiteit is de resource waaraan deze is gekoppeld. Zo is de beveiligings grens voor een virtuele machine met beheerde identiteiten voor Azure-resources ingeschakeld, de virtuele machine. Alle code die op die virtuele machine wordt uitgevoerd, kan de beheerde identiteiten voor het eind punt van Azure-resources aanroepen en tokens aanvragen. Het is een vergelijk bare ervaring met andere resources die beheerde identiteiten voor Azure-resources ondersteunen.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Worden beheerde identiteiten automatisch opnieuw gemaakt als ik een abonnement naar een andere map Verplaats?

Nee. Als u een abonnement naar een andere map verplaatst, moet u deze hand matig opnieuw maken en Azure-roltoewijzingen opnieuw verlenen.
- Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
- Voor door de gebruiker toegewezen beheerde identiteiten: verwijderen, opnieuw maken en opnieuw koppelen aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan ik een beheerde identiteit gebruiken om toegang te krijgen tot een resource in een andere Directory/Tenant?

Nee. Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Gelden er frequentie limieten die van toepassing zijn op beheerde identiteiten?

Beperkingen voor beheerde identiteiten hebben afhankelijkheden van Azure-service limieten, Azure Instance Metadata Service-limieten (IMDS) en Azure Active Directory-service limieten.

- **Azure-service limieten** bepalen het aantal Create-bewerkingen dat kan worden uitgevoerd op het niveau van de Tenant en het abonnement. Door de gebruiker toegewezen beheerde identiteiten hebben ook [beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) ten aanzien van hoe ze kunnen worden genoemd.
- **IMDS** Over het algemeen zijn aanvragen voor IMDS beperkt tot vijf aanvragen per seconde. Aanvragen die de drempel waarde overschrijden, worden met 429 reacties afgewezen. Aanvragen voor de categorie beheerde identiteit zijn beperkt tot 20 aanvragen per seconde en vijf gelijktijdige aanvragen. Meer informatie vindt u in het artikel [Azure instance meta data service (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) .
- **Azure Active Directory-service** Elke beheerde identiteit telt naar de object quotum limiet in een Azure AD-Tenant, zoals beschreven in azure [ad-service limieten en-beperkingen](../enterprise-users/directory-service-limits-restrictions.md).


## <a name="is-it-ok-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Is het OK om een door de gebruiker toegewezen beheerde identiteit te verplaatsen naar een andere resource groep of een ander abonnement?

Het verplaatsen van een door de gebruiker toegewezen beheerde identiteit naar een andere resource groep wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over hoe beheerde identiteiten werken met virtuele machines](how-managed-identities-work-vm.md)
