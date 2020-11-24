---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: f1e7b33630ea52bb0cb675b81fff9cc738f27980
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557512"
---
| Resource | Limiet |
| --- | --- |
| Resources per [resourcegroep](../articles/azure-resource-manager/management/overview.md#resource-groups) | Het aantal resources wordt niet beperkt door de resourcegroep. In plaats daarvan wordt het aantal beperkt door het resourcetype in een resourcegroep. Zie de volgende rij. |
| Resources per resourcegroep, per resourcetype |800 - Sommige resourcetypen kunnen de limiet van 800 overschrijden. Raadpleeg [Resources not limited to 800 instances per resource group](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md) (Resources die niet beperkt zijn tot 800 exemplaren per resourcegroep). |
| Implementaties per resourcegroep in de implementatiegeschiedenis |800<sup>1</sup> |
| Resources per implementatie |800 |
| Beheervergrendelingen per uniek [bereik](../articles/azure-resource-manager/management/overview.md#understand-scope)  |20 |
| Aantal tags per resource of resourcegroep |50 |
| Lengte van tagsleutel |512 |
| Lengte van tagwaarde |256 |

<sup>1</sup>Implementaties worden automatisch uit de geschiedenis verwijderd wanneer u de limiet nadert. Als u een item uit de implementatiegeschiedenis verwijdert, heeft dit geen gevolgen voor de geïmplementeerde resources. Raadpleeg [Automatic deletions from deployment history](../articles/azure-resource-manager/templates/deployment-history-deletions.md) (Automatische verwijderingen uit de implementatiegeschiedenis) voor meer informatie.

#### <a name="template-limits"></a>Limieten voor sjablonen

| Waarde | Limiet |
| --- | --- |
| Parameters |256 |
| Variabelen |256 |
| Resources (inclusief het aantal kopieën) |800 |
| Uitvoerwaarden |64 |
| Sjabloonexpressie |24.576 tekens |
| Resources in geëxporteerde sjablonen |200 |
| Sjabloongrootte |4 MB |
| Grootte van parameterbestand |64 kB |

U kunt enkele limieten voor sjablonen overschrijden met behulp van een geneste sjabloon. Voor meer informatie raadpleegt u [Use linked templates when you deploy Azure resources](../articles/azure-resource-manager/templates/linked-templates.md) (Gekoppelde sjablonen gebruiken bij het implementeren van Azure-resources). Als u het aantal parameters, variabelen of uitvoerwaarden wilt verkleinen, kunt u verschillende waarden combineren in een object. Raadpleeg [Objects as parameters](/azure/architecture/building-blocks/extending-templates/objects-as-parameters) (Objecten als parameters) voor meer informatie.