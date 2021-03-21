---
title: Servicelimieten
titleSuffix: Azure Machine Learning
description: Service limieten die worden gebruikt voor capaciteits planning en maximum aantal limieten voor aanvragen en antwoorden voor Azure Machine Learning.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930800"
---
# <a name="service-limits-in-azure-machine-learning"></a>Service limieten in Azure Machine Learning

In deze sectie vindt u de basis quota en beperkings drempel waarden in Azure Machine Learning. Zie voor meer informatie over het verhogen van resource quota's [' Quota's beheren en verhogen voor resources '](how-to-manage-quotas.md)

## <a name="workspaces"></a>Workspaces
| Limiet | Waarde |
| --- | --- |
| Werkruimtenaam | 2-32 tekens |

## <a name="runs"></a>Wordt uitgevoerd
| Limiet | Waarde |
| --- | --- |
| Uitvoeringen per werk ruimte | 10 miljoen |
| RunId/ParentRunId | 256 tekens |
| DataContainerId | 261 tekens |
| DisplayName |256 tekens|
| Beschrijving |5.000 tekens|
| Aantal eigenschappen |50 |
| Lengte van eigenschaps sleutel |100 tekens |
| Lengte van eigenschaps waarde |1.000 tekens |
| Aantal Tags |50 |
| Lengte van de code sleutel |100 |
| Lengte van de label waarde |1.000 tekens |
| CancelUri / CompleteUri / DiagnosticsUri |1.000 tekens |
| Lengte van fout bericht |3.000 tekens |
| Lengte van waarschuwings bericht |300 tekens |
| Aantal invoer gegevens sets |200 |
| Aantal uitvoer gegevens sets |20 |


## <a name="metrics"></a>Metrische gegevens
| Limiet | Waarde |
| --- | --- |
| Metrische namen per uitvoering |50|
| Metrische rijen per metrische naam |10 miljoen|
| Kolommen per metrische rij |15|
| Lengte van kolom naam voor metrische gegevens |255 tekens |
| Lengte van meet kolom waarde |255 tekens |
| Metrische rijen per batch geüpload | 250 |

> [!NOTE]
> Als u de maximale grootte van metrische gegevens per run bereikt omdat u variabelen opmaakt in de metrische naam, kunt u in plaats daarvan een metrische metriek gebruiken waarbij één kolom de waarde van de variabele is en de tweede kolom de metrische waarde is.

## <a name="artifacts"></a>Artifacts

| Limiet | Waarde |
| --- | --- |
| Aantal artefacten per uitvoering |10 miljoen|
| Maximale lengte van het artefact traject |5.000 tekens |

## <a name="limit-increases"></a>Limiet verhogingen
Een aantal limieten kan worden verg root voor afzonderlijke werk ruimten door [contact op](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)te nemen met de ondersteuning. 

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Machine Learning omgeving configureren](how-to-configure-environment.md)
- Meer informatie over hoe u resource quota kunt verhogen in [' quota's voor resources beheren en verhogen '](how-to-manage-quotas.md).

