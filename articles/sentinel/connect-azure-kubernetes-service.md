---
title: Diagnostische logboeken van Azure Kubernetes service (AKS) verbinden met Azure Sentinel
description: Informatie over het gebruik van Azure Policy om Diagnostische logboeken van Azure Kubernetes-service te verbinden met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: c3a4593aa92acededf9784974b2a1e2dd3cfb319
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507174"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Verbinding maken met Azure Kubernetes service Diagnostics-logboeken

Azure Kubernetes service (AKS) is een open-source, volledig beheerde container service waarmee u docker-containers en op containers gebaseerde toepassingen in een cluster omgeving kunt implementeren, schalen en beheren.

Met deze connector kunt u Diagnostische logboeken van Azure Kubernetes service (AKS) streamen naar Azure Sentinel, zodat u de activiteiten in al uw instanties voortdurend kunt bewaken. 

Meer informatie over het [bewaken van de Azure Kubernetes-service](../azure-monitor/containers/container-insights-overview.md) en over [AKS Diagnostic-telemetrie](../aks/view-control-plane-logs.md).

## <a name="prerequisites"></a>Vereisten

AKS-logboeken opnemen in azure Sentinel:

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- Als u Azure Policy wilt gebruiken om een beleid voor het streamen van Logboeken toe te passen op AKS-resources, moet u de rol van eigenaar toegewezen zijn voor het bereik van beleids toewijzing.

## <a name="connect-to-azure-kubernetes-service"></a>Verbinding maken met de Azure Kubernetes-service

Deze connector gebruikt Azure Policy voor het Toep assen van één configuratie van een logboek streaming van Azure Kubernetes service op een verzameling exemplaren, gedefinieerd als een bereik. U kunt de logboek typen die zijn opgenomen vanuit de Azure Kubernetes-service weer geven aan de linkerkant van de connector pagina onder **gegevens typen**.

1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**.

1. Selecteer **Azure Kubernetes service (AKS)** in de galerie met gegevens connectors en selecteer vervolgens **connector pagina openen** in het voorbeeld venster.

1. Vouw in de sectie **configuratie** van de pagina connector **Diagnostische logboeken inschakelen in azure KUBERNETES service (AKS)** uit.

1. Selecteer de knop **wizard Azure Policy toewijzing starten** .

    De wizard beleids toewijzing wordt geopend, klaar om een nieuw beleid te maken met de naam **Deploy-configure Diagnostic Settings for Azure Kubernetes service to log Analytics Workspace**.

    1. Klik op het tabblad **basis beginselen** op de knop met de drie puntjes onder **bereik** om uw abonnement te selecteren (en eventueel een resource groep). U kunt ook een beschrijving toevoegen.

    1. Kies op het tabblad **para meters** de Azure Sentinel-werk ruimte in de vervolg keuzelijst **log Analytics werk ruimte** . De resterende vervolg keuzelijst velden vertegenwoordigen de beschik bare typen diagnose Logboeken. Geef op ' True ' alle logboek typen op die u wilt opnemen.

    1. Als u het beleid wilt Toep assen op uw bestaande resources, selecteert u het tabblad **herstel** en schakelt u het selectie vakje **een herstel taak maken** in.

    1. Klik op het tabblad **Beoordelen en maken** op **Maken**. Uw beleid wordt nu toegewezen aan het bereik dat u hebt gekozen.

> [!NOTE]
>
> Met deze specifieke gegevens connector wordt de verbindings status indicator (een kleur balk in de galerie met gegevens connectors en verbindings pictogrammen naast de namen van gegevens typen) alleen weer gegeven als *verbonden* (groen) als de gegevens op een bepaald moment in de afgelopen twee weken zijn opgenomen. Als er twee weken zijn geslaagd zonder opname van gegevens, wordt de connector weer gegeven als niet-verbonden. Op het moment dat er meer gegevens worden opgehaald, wordt de status *verbonden* geretourneerd.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de Azure Kubernetes-service kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
