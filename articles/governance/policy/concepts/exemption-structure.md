---
title: Details van de structuur van de beleids uitsluiting
description: Beschrijft de beleids uitsluiting definitie die wordt gebruikt door Azure Policy om resources uit te sluiten van de evaluatie van initiatieven of definities.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: ecf956d7507dfa2168e4f2591e4b661423801365
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091732"
---
# <a name="azure-policy-exemption-structure"></a>Structuur van Azure Policy-uitzonde ring

De functie Azure Policy uitzonde ringen (preview) wordt gebruikt om een resource hiërarchie of een afzonderlijke resource uit te _sluiten_ van de evaluatie van initiatieven of definities. Resources met een _uitzonde_ ring op de algehele naleving, maar niet kunnen worden geëvalueerd of die geen tijdelijke ontheffing hebben. Zie [bereik begrijpen in azure Policy](./scope.md)voor meer informatie. Azure Policy uitzonde ringen werken alleen met de [Resource Manager-modus](./definition-structure.md#resource-manager-modes) en werken niet met de modi van de [resource provider](./definition-structure.md#resource-provider-modes).

> [!IMPORTANT]
> Deze functie is gratis tijdens de **Preview-versie**. Zie [Azure Policy prijzen](https://azure.microsoft.com/pricing/details/azure-policy/)voor prijs informatie. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U gebruikt JSON om een beleids uitsluiting te maken. De beleids uitsluiting bevat elementen voor:

- weergave naam
- beschrijving
- metagegevens
- beleids toewijzing
- beleids definities binnen een initiatief
- categorie uitzonde ringen
- verval

> [!NOTE]
> Er wordt een beleids uitsluiting gemaakt als onderliggend object op de resource hiërarchie of de afzonderlijke resource heeft de uitzonde ring toegewezen, zodat het doel niet is opgenomen in de definitie van de uitzonde ring.

De volgende JSON toont bijvoorbeeld een uitzonde ring van het beleid in de categorie **kwijt schel** ding van een resource aan een initiatief toewijzing met de naam `resourceShouldBeCompliantInit` . De _bron is niet_ alleen van de beleids definities in het initiatief, de `customOrgPolicy` aangepaste beleids definitie (verwijzing `requiredTags` ) en de ingebouwde beleids definitie voor ' toegestane locaties ' (id: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , referentie `allowedLocations` ):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

Fragment van het gerelateerde initiatief met de overeenkomst `policyDefinitionReferenceIds` die wordt gebruikt door de beleids uitsluiting:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Weergave naam en beschrijving

U gebruikt **DisplayName** en **Beschrijving** om de beleids uitzondering te identificeren en context te bieden voor gebruik met de specifieke resource. **DisplayName** heeft een maximale lengte van _128_ tekens en een **Beschrijving** van Maxi maal _512_ tekens.

## <a name="metadata"></a>Metagegevens

Met de eigenschap **meta gegevens** kunt u een onderliggend element maken dat nodig is voor het opslaan van relevante informatie. In het bovenstaande voor beeld bevat de eigenschappen **requestedBy**, **approvedBy**, **approvedOn** en **ticketRef** klant waarden om informatie te geven over wie de uitzonde ring heeft aangevraagd, wie deze heeft goedgekeurd en wanneer en een intern tracking ticket voor de aanvraag. Deze eigenschappen van **meta gegevens** zijn voor beelden, maar ze zijn niet vereist en **meta gegevens** zijn niet beperkt tot deze onderliggende eigenschappen.

## <a name="policy-assignment-id"></a>Toewijzings-ID van beleid

Dit veld moet de volledige padnaam zijn van een beleids toewijzing of een initiatief toewijzing.
`policyAssignmentId` is een teken reeks en geen matrix. Met deze eigenschap definieert u welke toewijzing de bovenliggende resource hiërarchie of afzonderlijke resource moet worden _uitgesloten_ .

## <a name="policy-definition-ids"></a>Beleids definitie-Id's

Als de `policyAssignmentId` is voor een initiatief toewijzing, `policyDefinitionReferenceIds` kan de eigenschap worden gebruikt om op te geven welke beleids definitie (s) in het initiatief van de onderwerps bron een uitzonde ring op heeft. Omdat de resource kan worden uitgesloten van een of meer opgenomen beleids definities, is deze eigenschap een _matrix_. De waarden moeten overeenkomen met de waarden in de initiatief definitie in de `policyDefinitions.policyDefinitionReferenceId` velden.

## <a name="exemption-category"></a>Categorie uitzonde ringen

Er bestaan twee vrijstellings categorieën en worden gebruikt om uitzonde ringen te groeperen:

- **Verminderd**: de uitzonde ring wordt verleend omdat de beleids intentie wordt voldaan via een andere methode.
- **Afwijzinger**: de uitzonde ring wordt verleend omdat de niet-nalevings status van de resource tijdelijk wordt geaccepteerd. Een andere reden voor het gebruik van deze categorie is voor een resource of resource hiërarchie die moet worden uitgesloten van een of meer definities in een initiatief, maar niet moeten worden uitgesloten van het hele initiatief.

## <a name="expiration"></a>Verloopdatum

Als u wilt instellen wanneer een resource hiërarchie of een afzonderlijke resource niet meer wordt _uitgesloten_ voor een toewijzing, stelt u de `expiresOn` eigenschap in. Deze optionele eigenschap moet de DateTime-indeling (Universal ISO 8601) hebben `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> De beleids uitzonderingen worden niet verwijderd wanneer de `expiresOn` datum is bereikt. Het object blijft behouden voor record behoud, maar de uitzonde ring wordt niet meer nageleefd.

## <a name="required-permissions"></a>Vereiste machtigingen

De Azure RBAC-machtigingen die nodig zijn voor het beheren van beleids uitsluiting objecten bevinden zich in de `Microsoft.Authorization/policyExemptions` bewerkings groep. De ingebouwde rollen Inzender en [beveiligings beheerder](../../../role-based-access-control/built-in-roles.md#security-admin) van het [resource beleid](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) hebben beide machtigingen en de `read` `write` machtiging [gegevens schrijver (preview)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) van het beleid `read` .

Uitzonde ringen hebben extra beveiligings maatregelen vanwege de gevolgen van het verlenen van een uitzonde ring. `Microsoft.Authorization/policyExemptions/write`De maker van een uitzonde ring moet de bewerking hebben voor de doel toewijzing, behalve dat hiervoor de resource hiërarchie of afzonderlijke resource wordt vereist `exempt/Action` .

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de structuur van de [beleids definitie](./definition-structure.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).