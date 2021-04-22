---
title: Verbinding maken Azure Kubernetes Service diagnostische logboeken (AKS) met Azure Sentinel
description: Informatie over het gebruik van Azure Policy om verbinding te maken Azure Kubernetes Service diagnostische logboeken met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/22/2021
ms.author: yelevin
ms.openlocfilehash: f1ef860f1b84de84c42996a7523af8ce174d5981
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890794"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Verbinding Azure Kubernetes Service diagnostische logboeken

Azure Kubernetes Service (AKS) is een open source, volledig beheerde container orchestration-service waarmee u Docker-containers en containertoepassingen in een clusteromgeving kunt implementeren, schalen en beheren.

Met deze connector kunt u diagnostische logboeken Azure Kubernetes Service AKS (AKS) streamen naar Azure Sentinel, zodat u de activiteiten in al uw exemplaren continu kunt bewaken. 

Meer informatie over [bewaking van Azure Kubernetes Service](../azure-monitor/containers/container-insights-overview.md) en over [diagnostische AKS-telemetrie.](../aks/view-control-plane-logs.md)

## <a name="prerequisites"></a>Vereisten

AKS-logboeken opnemen in Azure Sentinel:

- U moet lees- en schrijfmachtigingen hebben voor de Azure Sentinel werkruimte.

- Als u Azure Policy om een beleid voor logboekstreaming toe te passen op AKS-resources, moet u de rol Eigenaar hebben voor het bereik van de beleidstoewijzing.

## <a name="connect-to-azure-kubernetes-service"></a>Verbinding maken met Azure Kubernetes Service

Deze connector gebruikt Azure Policy om één configuratie Azure Kubernetes Service voor logboekstreaming toe te passen op een verzameling resources, gedefinieerd als een bereik. U ziet de logboektypen die zijn opgenomen Azure Kubernetes Service aan de linkerkant van de connectorpagina, onder **Gegevenstypen.**

1. Selecteer in Azure Sentinel navigatiemenu **Gegevensconnectoren.**

1. Selecteer **Azure Kubernetes Service (AKS) in** de galerie met gegevensconnectoren en selecteer vervolgens **Connectorpagina** openen in het voorbeeldvenster.

1. Vouw in **de sectie** Configuratie van de connectorpagina diagnostische logboeken streamen uit vanuit uw **Azure Kubernetes Service (AKS) op schaal**.

1. Selecteer de **knop Azure Policy wizard Toewijzing** starten.

    De wizard beleidstoewijzing wordt geopend, klaar om een nieuw beleid te maken met de naam Implementeren - Diagnostische instellingen configureren voor Azure Kubernetes Service **naar Log Analytics-werkruimte**.

    1. Klik op **het** tabblad Basisinformatie op de knop met de drie puntjes onder **Bereik** om uw abonnement (en eventueel een resourcegroep) te selecteren. U kunt ook een beschrijving toevoegen.

    1. Laat op **het** tabblad Parameters de **velden Effect** en **Setting name** staan. Kies uw Azure Sentinel werkruimte in de **vervolgkeuzelijst Log** Analytics-werkruimte. De resterende vervolgkeuzevelden vertegenwoordigen de beschikbare typen diagnostische logboeken. Laat alle logboektypen die u wilt opnemen gemarkeerd als Waar.

    1. Het beleid wordt toegepast op resources die in de toekomst worden toegevoegd. Als u het beleid ook wilt toepassen  op uw bestaande resources, selecteert u het tabblad Herstel en selecteert u het selectievakje Een **hersteltaak** maken.

    1. Klik op het tabblad **Beoordelen en maken** op **Maken**. Uw beleid is nu toegewezen aan het bereik dat u hebt gekozen.

> [!NOTE]
>
> Met deze specifieke gegevensconnector worden de connectiviteitsstatusindicatoren (een kleurstree in de galerie met  gegevensconnectoren en verbindingspictogrammen naast de gegevenstypenamen) alleen weergegeven als verbonden (groen) als gegevens op een bepaald moment in de afgelopen 14 dagen zijn opgenomen. Zodra er 14 dagen zijn verstreken zonder dat er gegevens worden opgenomen, wordt de verbinding met de connector verbroken. Zodra er meer gegevens worden verzameld, wordt *de verbonden* status weer terug.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure Policy verbinding kunt Azure Kubernetes Service met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [krijgen van inzicht in uw gegevens en mogelijke bedreigingen.](quickstart-get-visibility.md)
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
