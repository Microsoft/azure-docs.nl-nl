---
title: Naleving van regelgeving in initiatief definities
description: Hierin wordt beschreven hoe u een initiatief definitie gebruikt om beleid te groeperen op regelgevings domein, zoals Access Control, configuratie beheer en andere.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 08173cd94e0841fa7a18fd55d53aa97281724ede
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094061"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Naleving van regelgeving in Azure Policy

Naleving van regelgeving in Azure Policy voorziet in ingebouwde initiatief definities voor het weer geven van een lijst met **besturings elementen** en **nalevings domeinen** op basis van verantwoordelijkheid (_klant_, _micro soft_, _gedeeld_).
Voor besturings elementen van micro soft bieden we aanvullende informatie over onze controle resultaten op basis van Attestation van derden en onze implementatie gegevens om die naleving te vervolgen.
Door micro soft verantwoordelijke besturings elementen zijn van `type` [statisch](./definition-structure.md#type).

> [!NOTE]
> Naleving van regelgeving is een preview-functie. Voor bijgewerkte ingebouwde functies worden de besturings elementen van de initiatieven toegewezen aan de bijbehorende nalevings standaard. Bestaande initiatieven van nalevings standaard worden bijgewerkt om naleving van de regelgeving te ondersteunen.

## <a name="regulatory-compliance-defined"></a>Naleving van regelgeving gedefinieerd

Naleving van regelgeving is gebaseerd op het [Groepeer](./initiative-definition-structure.md#policy-definition-groups) gedeelte van een initiatief definitie. In ingebouwde modules definieert elke groepering in de initiatief definitie een naam (**besturings element**), een categorie (**nalevings domein**) en een verwijzing naar het [policyMetadata](./initiative-definition-structure.md#metadata-objects) -object dat informatie bevat over dat **besturings element**. Voor een definitie van een regelgevings nalevings initiatief moet de `category` eigenschap zijn ingesteld op naleving van de **regelgeving**. Als een andere definitie van het Standard-initiatief [, ondersteunen de](./initiative-definition-structure.md#parameters) nalevings initiatieven voor regelgeving om dynamische toewijzingen te maken.

Klanten kunnen hun eigen regelgeving voor nalevings beleid maken. Deze definities kunnen origineel zijn of worden gekopieerd uit bestaande ingebouwde definities. Als u een ingebouwde regelgevings definitie voor naleving als referentie gebruikt, is het raadzaam om de bron van de nalevings definities van de regelgeving te bewaken in de [Azure Policy github opslag plaats](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance).

Zie voor het koppelen van een aangepast regelgevings initiatief voor naleving aan uw Azure Security Center-dash board [Azure Security Center-aangepast beveiligings beleid gebruiken](../../../security-center/custom-security-policies.md).

## <a name="regulatory-compliance-in-portal"></a>Naleving van regelgeving in Portal

Wanneer een initiatief definitie is gemaakt met [groepen](./initiative-definition-structure.md#policy-definition-groups), bevat de pagina **nalevings** Details in de portal voor dat initiatief aanvullende informatie. 

Een nieuw tabblad, **besturings elementen** worden toegevoegd aan de pagina. Filteren is beschikbaar op **nalevings domein** en beleids definities worden gegroepeerd op het `title` veld van het **policyMetadata** -object. Elke rij vertegenwoordigt een **besturings element** met de nalevings status, het **nalevings domein** waarvan het deel uitmaakt, de verantwoordelijkheids gegevens en het aantal niet-compatibele en compatibele beleids definities die het **beheer** vormen.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="Scherm afbeelding van het overzicht van reglementaire naleving voor de ingebouwde definitie van het NIST SP 800-53 R4 met compatibele en niet-compatibele besturings elementen.":::

Als u een **besturings element** selecteert, wordt er een pagina met details over dat besturings element geopend. Het **overzicht** bevat de gegevens van `description` en `requirements` . Op het tabblad **beleid** vindt u alle afzonderlijke beleids definities in het initiatief die bijdragen aan dit **besturings element**. Het tabblad **resource compatibiliteit** biedt een gedetailleerd overzicht van elke resource die wordt geëvalueerd door een beleid voor leden van het **besturings element** dat momenteel wordt weer gegeven.

> [!NOTE]
> Een evaluatie type van door **micro soft beheerd** is voor een [statische](./definition-structure.md#type) beleids definitie `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="Scherm afbeelding van de nalevings Details van de regelgeving voor het grens beveiligings beheer van de ingebouwde definitie van het NIST SP 800-53 R4.":::

Op dezelfde **besturings** pagina kunt u de beleids definities van dit **besturings element** weer geven door te wijzigen in het tabblad **resource naleving** . Er zijn filters beschikbaar voor naam of ID, compatibiliteits status, resource type en locatie.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="Scherm afbeelding van de bron compatibiliteit met het grens beveiligings beheer van de ingebouwde definitie van het NIST SP 800-53 R4.":::

## <a name="regulatory-compliance-in-sdk"></a>Naleving van regelgeving in SDK

Als de naleving van de regelgeving voor een initiatief definitie is ingeschakeld, retour neren de evaluatie scan record, gebeurtenissen en beleids status SDK elke extra eigenschappen. Deze aanvullende eigenschappen worden gegroepeerd op nalevings status en bieden informatie over het aantal groepen in elke status.

De volgende code is een voor beeld van het toevoegen van resultaten van een `summarize` aanroep:

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [definitie structuur](./initiative-definition-structure.md) van het initiatief
- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees [Informatie over de effecten van het beleid](./effects.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
