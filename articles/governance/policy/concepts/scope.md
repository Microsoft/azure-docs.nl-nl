---
title: Bereik in Azure Policy begrijpen
description: Hierin wordt het concept van bereik in Azure Resource Manager beschreven en wordt uitgelegd hoe dit wordt toegepast op Azure Policy om te bepalen welke resources Azure Policy evalueren.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90984438"
---
# <a name="understand-scope-in-azure-policy"></a>Bereik in Azure Policy begrijpen

Er zijn een aantal instellingen die bepalen welke resources kunnen worden geëvalueerd en welke resources door Azure Policy worden geëvalueerd. Het primaire concept voor deze besturings elementen is _bereik_.
Zie [bereik in azure Resource Manager](../../../azure-resource-manager/management/overview.md#understand-scope)voor een overzicht op hoog niveau.
In dit artikel wordt uitgelegd wat de gevolgen van het _bereik_ in azure Policy zijn en welke objecten en eigenschappen hieraan zijn gerelateerd.

## <a name="definition-location"></a>Locatie van definitie

Het eerste exemplaar bereik dat door Azure Policy wordt gebruikt, is wanneer een beleids definitie wordt gemaakt. De definitie kan worden opgeslagen in een beheer groep of een abonnement. De locatie bepaalt het bereik waaraan het initiatief of beleid kan worden toegewezen. Resources moeten zich in de resource hiërarchie van de definitie locatie bevallen op het doel voor de toewijzing.

Als de definitie locatie een:

- Alleen voor **abonnements** resources binnen dit abonnement kan de beleids definitie worden toegewezen.
- **Beheer groep** -alleen resources binnen onderliggende beheer groepen en onderliggende abonnementen kunnen worden toegewezen aan de beleids definitie. Als u van plan bent de beleids definitie toe te passen op verschillende abonnementen, moet de locatie een beheer groep zijn die elk abonnement bevat.

De locatie moet de resource container zijn die wordt gedeeld door alle resources waarvoor u de beleids definitie wilt gebruiken. Deze resource container is doorgaans een beheer groep in de buurt van de hoofd beheer groep.

## <a name="assignment-scopes"></a>Toewijzings bereik

Een toewijzing heeft verschillende eigenschappen die een bereik instellen. Het gebruik van deze eigenschappen bepaalt welke resource voor Azure Policy moet worden geëvalueerd en welke resources in overeenstemming zijn met de naleving. Deze eigenschappen zijn gekoppeld aan de volgende concepten:

- Opnemen: een resource hiërarchie of afzonderlijke resource moet worden geëvalueerd voor naleving door de definitie. De `properties.scope` eigenschap van een toewijzings object bepaalt wat moet worden opgezocht en beoordeeld op naleving. Zie [toewijzings definitie](./assignment-structure.md)voor meer informatie.

- Uitsluiting: een resource hiërarchie of afzonderlijke resource moet niet worden geëvalueerd voor naleving door de definitie. De `properties.notScopes` _matrix_ eigenschap van een toewijzings object bepaalt wat moet worden uitgesloten. Resources binnen deze bereiken worden niet geëvalueerd of opgenomen in het aantal vereisten. Zie [toewijzings definitie-uitgesloten bereiken](./assignment-structure.md#excluded-scopes)voor meer informatie.

Naast de eigenschappen van de beleids toewijzing is het uitzonderings object van het [beleid](./exemption-structure.md) . Uitzonde ringen verbeteren de reik wijdte van het bereik door een methode te bieden voor het identificeren van een deel van een toewijzing om niet te worden geëvalueerd.

- Uitzonde ring (**gratis in Preview** -functie): een resource hiërarchie of afzonderlijke resource moet worden geëvalueerd voor naleving door de definitie, maar niet worden geëvalueerd om een reden, zoals een ontheffing of door middel van een andere methode. Resources in deze status geven aan dat ze zijn **uitgesloten** in nalevings rapporten, zodat ze kunnen worden getraceerd. Het uitzonderings object wordt gemaakt op de resource hiërarchie of afzonderlijke resource als onderliggend object, waardoor het bereik van de uitzonde ring wordt bepaald. Een resource hiërarchie of afzonderlijke resource kan worden vrijgesteld voor meerdere toewijzingen. De uitzonde ring kan worden geconfigureerd om te verlopen volgens een schema met behulp van de- `expiresOn` eigenschap. Zie de definitie van de [uitzonde ring](./exemption-structure.md)voor meer informatie.

  > [!NOTE]
  > Als gevolg van de gevolgen van het verlenen van een uitzonde ring voor een resource hiërarchie of afzonderlijke resource, hebben uitzonde ringen extra beveiligings maatregelen. Naast het vereisen van de `Microsoft.Authorization/policyExemptions/write` bewerking op de resource hiërarchie of afzonderlijke resource, moet de maker van een uitzonde ring de `exempt/Action` opdracht hebben op de doel toewijzing.

## <a name="scope-comparison"></a>Vergelijking van bereik

De volgende tabel bevat een vergelijking van de scope-opties:

| | Insluitings | Uitsluiting (notScopes) | Gesteld |
|---|:---:|:---:|:---:|
|**Resources worden geëvalueerd** | &#10004; | - | - |
|**Resource Manager-object** | - | - | &#10004; |
|**Vereist dat beleids toewijzings object wijzigen** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de structuur van de [beleids definitie](./definition-structure.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).