---
title: Azure Key Vault Diagnostische logboeken verbinden met Azure Sentinel
description: Meer informatie over het gebruik van Azure Policy om Azure Key Vault Diagnostische logboeken te verbinden met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 56587ae91de086cccb7cc85c125f935be2a56f73
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102491266"
---
# <a name="connect-azure-key-vault-diagnostics-and-auditing-logs"></a>Azure Key Vault diagnostische gegevens en controle logboeken verbinden

Azure Key Vault is een Cloud service voor het veilig opslaan en openen van geheimen. Een geheim is alles wat u de toegang tot, zoals API-sleutels, wacht woorden, certificaten of cryptografische sleutels, nauw keurig wilt beheren.

Met deze connector kunt u uw Azure Key Vault Diagnostische logboeken streamen naar Azure Sentinel, zodat u de activiteiten in al uw instanties voortdurend kunt bewaken.

Meer informatie over het [bewaken Azure Key Vault](../azure-monitor/insights/key-vault-insights-overview.md) en over het Azure Key Vault van de [telemetrie diagnostische gegevens](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Vereisten

Azure Key Vault-logboeken opnemen in azure Sentinel:

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- Als u Azure Policy wilt gebruiken om een beleid voor het streamen van Logboeken toe te passen op Azure Key Vault-resources, moet u de rol eigenaar voor het beleid toewijzings bereik toewijzen.

## <a name="connect-to-azure-key-vault"></a>Verbinding maken met Azure Key Vault

Deze connector gebruikt Azure Policy voor het Toep assen van één Azure Key Vault logboek streaming configuratie op een verzameling exemplaren, gedefinieerd als een bereik. U ziet de logboek typen die zijn opgenomen in Azure Key Vault aan de linkerkant van de connector pagina, onder **gegevens typen**.

1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**.

1. Selecteer **Azure Key Vault** in de galerie met gegevens connectors en selecteer vervolgens **pagina connector openen** in het voorbeeld venster.

1. Vouw in de sectie **configuratie** van de pagina connector **Diagnostische logboeken inschakelen in azure Key Vault** uit.

1. Selecteer de knop **wizard Azure Policy toewijzing starten** .

    De wizard beleids toewijzing wordt geopend. er wordt nu een nieuw beleid met de naam **Deploy-configure Diagnostische instellingen voor Azure Key Vault op Log Analytics werk ruimte** gemaakt.

    1. Klik op het tabblad **basis beginselen** op de knop met de drie puntjes onder **bereik** om uw abonnement te selecteren (en eventueel een resource groep). U kunt ook een beschrijving toevoegen.

    1. Kies op het tabblad **para meters** de Azure Sentinel-werk ruimte in de vervolg keuzelijst **log Analytics werk ruimte** . De resterende vervolg keuzelijst velden vertegenwoordigen de beschik bare typen diagnose Logboeken. Geef op ' True ' alle logboek typen op die u wilt opnemen.

    1. Als u het beleid wilt Toep assen op uw bestaande resources, selecteert u het tabblad **herstel** en schakelt u het selectie vakje **een herstel taak maken** in.

    1. Klik op het tabblad **Beoordelen en maken** op **Maken**. Uw beleid wordt nu toegewezen aan het bereik dat u hebt gekozen.

> [!NOTE]
>
> Met deze specifieke gegevens connector wordt de verbindings status indicator (een kleur balk in de galerie met gegevens connectors en verbindings pictogrammen naast de namen van gegevens typen) alleen weer gegeven als *verbonden* (groen) als de gegevens op een bepaald moment in de afgelopen twee weken zijn opgenomen. Als er twee weken zijn geslaagd zonder opname van gegevens, wordt de connector weer gegeven als niet-verbonden. Op het moment dat er meer gegevens worden opgehaald, wordt de status *verbonden* geretourneerd.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure Key Vault kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
