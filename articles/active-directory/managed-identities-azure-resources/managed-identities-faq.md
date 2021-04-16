---
title: Veelgestelde vragen over beheerde identiteiten voor Azure-resources - Azure AD
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
ms.openlocfilehash: 07b106630cffae75c5e4588d14de7ae938945614
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534125"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Veelgestelde vragen over beheerde identiteiten voor Azure-resources - Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

## <a name="administration"></a>Beheer

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hoe kunt u resources vinden die een beheerde identiteit hebben?

U vindt de lijst met resources met een door het systeem toegewezen beheerde identiteit met behulp van de volgende Azure CLI-opdracht: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Welke Azure RBAC-machtigingen zijn vereist voor een beheerde identiteit in een resource? 

- Door het systeem toegewezen beheerde identiteit: u hebt schrijfmachtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Deze actie is opgenomen in resourcespecifieke ingebouwde rollen, zoals [Inzender voor virtuele machines.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
- Door de gebruiker toegewezen beheerde identiteit: u hebt schrijfmachtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Naast roltoewijzing [beheerde identiteitsoperator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) over de beheerde identiteit.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Hoe kan ik het maken van door de gebruiker toegewezen beheerde identiteiten voorkomen?

U kunt ervoor zorgen dat uw gebruikers geen door de gebruiker toegewezen beheerde identiteiten maken met behulp [van Azure Policy](../../governance/policy/overview.md)

1. Navigeer [naar Azure Portal](https://portal.azure.com) en ga naar **Beleid.**
2. Definities **kiezen**
3. Selecteer **+ Beleidsdefinitie** en voer de benodigde gegevens in.
4. Plak in de sectie Beleidsregel de tekst
    
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

Nadat u het beleid hebt gemaakt, wijst u het toe aan de resourcegroep die u wilt gebruiken.

1. Navigeer naar resourcegroepen.
2. Zoek de resourcegroep die u gebruikt voor het testen.
3. Kies **Beleidsregels** in het menu links.
4. Beleid **toewijzen selecteren**
5. Geef in **de sectie** Basisinformatie het volgende op:
    1. **Bereik** De resourcegroep die we gebruiken voor het testen
    1. **Beleidsdefinitie:** het beleid dat we eerder hebben gemaakt.
6. Laat alle andere instellingen op de standaardwaarden staan en kies **Beoordelen en maken**

Op dit moment mislukt elke poging om een door de gebruiker toegewezen beheerde identiteit in de resourcegroep te maken.

  ![Beleidsschending](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Concepten

### <a name="do-managed-identities-have-a-backing-app-object"></a>Hebben beheerde identiteiten een app-object voor back-overingen?

Nee. Beheerde identiteiten en Azure AD-app registraties zijn niet hetzelfde in de map.

App-registraties twee onderdelen hebben: Een toepassingsobject en een service-principalobject.
Beheerde identiteiten voor Azure-resources hebben slechts één van deze onderdelen: een service-principalobject.

Beheerde identiteiten hebben geen toepassingsobject in de map. Dit wordt meestal gebruikt om app-machtigingen te verlenen voor MS Graph. In plaats daarvan moeten MS Graph-machtigingen voor beheerde identiteiten rechtstreeks worden verleend aan de service-principal.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Wat is de referentie die is gekoppeld aan een beheerde identiteit? Hoe lang is de toepassing geldig en hoe vaak wordt deze geroteerd?

> [!NOTE]
> Hoe beheerde identiteiten worden geverifieerd, is een intern implementatiedetail dat zonder kennisgeving kan worden gewijzigd.

Beheerde identiteiten maken gebruik van verificatie op basis van certificaten. De referentie van elke beheerde identiteit is 90 dagen verlopen en wordt na 45 dagen uitgerold.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Op welke identiteit wordt IMDS standaard ingesteld als de identiteit niet in de aanvraag wordt opgegeven?

- Als door het systeem toegewezen beheerde identiteit is ingeschakeld en er geen identiteit is opgegeven in de aanvraag, wordt IMDS standaard ingesteld op de door het systeem toegewezen beheerde identiteit.
- Als de door het systeem toegewezen beheerde identiteit niet is ingeschakeld en er slechts één door de gebruiker toegewezen beheerde identiteit bestaat, wordt IMDS standaard ingesteld op die ene door de gebruiker toegewezen beheerde identiteit.
- Als de door het systeem toegewezen beheerde identiteit niet is ingeschakeld en er meerdere door de gebruiker toegewezen beheerde identiteiten bestaan, moet u een beheerde identiteit opgeven in de aanvraag.

## <a name="limitations"></a>Beperkingen

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Kan dezelfde beheerde identiteit worden gebruikt in meerdere regio's?

Kortom, ja, u kunt door de gebruiker toegewezen beheerde identiteiten in meer dan één Azure-regio gebruiken. Het langere antwoord is dat terwijl door de gebruiker toegewezen beheerde identiteiten worden gemaakt als regionale resources, de bijbehorende [service-principal](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) die in Azure AD is gemaakt, wereldwijd beschikbaar is. De service-principal kan worden gebruikt vanuit elke Azure-regio en de beschikbaarheid ervan is afhankelijk van de beschikbaarheid van Azure AD. Als u bijvoorbeeld een door de gebruiker toegewezen beheerde identiteit hebt gemaakt in de [](../../azure-resource-manager/management/control-plane-and-data-plane.md) South-Central-regio en die regio niet meer beschikbaar is, heeft dit probleem alleen gevolgen voor activiteiten op het besturingsvlak op de beheerde identiteit zelf.  De activiteiten die worden uitgevoerd door resources die al zijn geconfigureerd voor het gebruik van de beheerde identiteiten, worden niet beïnvloed.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Werken beheerde identiteiten voor Azure-resources met Azure Cloud Services?

Nee, er zijn geen plannen om beheerde identiteiten te ondersteunen voor Azure-resources in Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Wat is de beveiligingsgrens van beheerde identiteiten voor Azure-resources?

De beveiligingsgrens van de identiteit is de resource waaraan deze is gekoppeld. De beveiligingsgrens voor een virtuele machine met beheerde identiteiten voor Azure-resources is bijvoorbeeld de virtuele machine. Elke code die op die VM wordt uitgevoerd, kan de beheerde identiteiten voor het eindpunt van Azure-resources aanroepen en tokens aanvragen. Het is de vergelijkbare ervaring met andere resources die ondersteuning bieden voor beheerde identiteiten voor Azure-resources.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Worden beheerde identiteiten automatisch opnieuw gemaakt als ik een abonnement naar een andere directory verplaats?

Nee. Als u een abonnement verplaatst naar een andere directory, moet u deze handmatig opnieuw maken en Azure-roltoewijzingen opnieuw verlenen.
- Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
- Voor door de gebruiker toegewezen beheerde identiteiten: verwijder ze, maak ze opnieuw en koppel ze opnieuw aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan ik een beheerde identiteit gebruiken voor toegang tot een resource in een andere directory/tenant?

Nee. Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Zijn er tarieflimieten die van toepassing zijn op beheerde identiteiten?

Limieten voor beheerde identiteiten zijn afhankelijk van Azure-servicelimieten, LIMIETen voor Azure Instance Metadata Service (IMDS) en Azure Active Directory servicelimieten.

- **Azure-servicelimieten** definiëren het aantal maakbewerkingen dat kan worden uitgevoerd op tenant- en abonnementsniveau. Door de gebruiker toegewezen beheerde identiteiten hebben ook [beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) met betrekking tot hoe ze kunnen worden benoemd.
- **IMDS** In het algemeen zijn aanvragen voor IMDS beperkt tot vijf aanvragen per seconde. Aanvragen die deze drempelwaarde overschrijden, worden geweigerd met 429 antwoorden. Aanvragen voor de categorie Beheerde identiteit zijn beperkt tot 20 aanvragen per seconde en 5 gelijktijdige aanvragen. Meer informatie vindt u in het [artikel Azure Instance Metadata Service (Windows).](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity)
- **Azure Active Directory service** Elke beheerde identiteit telt mee voor de quotumlimiet voor objecten in een Azure AD-tenant, zoals beschreven in Azure [AD-servicelimieten en -beperkingen.](../enterprise-users/directory-service-limits-restrictions.md)


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Is het mogelijk om een door de gebruiker toegewezen beheerde identiteit te verplaatsen naar een andere resourcegroep/ander abonnement?

Het verplaatsen van een door de gebruiker toegewezen beheerde identiteit naar een andere resourcegroep wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Meer [informatie over hoe beheerde identiteiten werken met virtuele machines](how-managed-identities-work-vm.md)
