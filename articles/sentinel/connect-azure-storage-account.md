---
title: Verbinding maken Azure Storage diagnostische logboeken van uw account met Azure Sentinel
description: Meer informatie over het gebruik van Azure Policy om diagnostische logboeken Azure Storage uw account te Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: b4260d31b587f2a20d7bc9d4c4e3e6a0d225a416
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879126"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Diagnostische Azure Storage van uw account verbinden

Azure Storage account is een cloudoplossing voor moderne scenario's voor gegevensopslag. Het bevat al uw gegevensobjecten: blobs, bestanden, wachtrijen, tabellen en schijven.

Met deze connector kunt u de diagnostische logboeken van uw Azure Storage-accounts streamen naar Azure Sentinel, zodat u continu activiteiten kunt bewaken en beveiligingsrisico's kunt detecteren in al uw Azure-opslagresources in uw organisatie.

Meer informatie over [het controleren Azure Storage account](../storage/common/storage-analytics-logging.md).

## <a name="prerequisites"></a>Vereisten

Diagnostische logboeken Azure Storage account opnemen in Azure Sentinel:

- U moet lees- en schrijfmachtigingen hebben voor de Azure Sentinel werkruimte.

- Als u Azure Policy logboekstreamingbeleid wilt toepassen op Azure Storage resources, moet u de rol Eigenaar hebben voor het bereik van de beleidstoewijzing.

## <a name="connect-to-azure-storage-account"></a>Verbinding maken met Azure Storage account

Deze connector gebruikt Azure Policy om één configuratie voor logboekstreaming toe te passen op een verzameling Azure Storage-accountbronnen, gedefinieerd als een bereik. U ziet de beschikbare Azure Storage logboek- en metrische typen aan de linkerkant van de connectorpagina, onder **Gegevenstypen.**

1. Selecteer in Azure Sentinel navigatiemenu **Gegevensconnectoren.**

1. Selecteer **Azure Storage-account** in de galerie met gegevensconnectoren en selecteer **vervolgens Pagina connector openen** in het voorbeeldvenster.

1. Vouw in **de sectie** Configuratie van de connectorpagina diagnostische logboeken van Stream uit vanuit uw **Azure Storage-account op schaal.**

1. Selecteer de **knop Wizard Azure Policy starten.**

    De wizard beleidstoewijzing wordt geopend, klaar voor het maken van een nieuw beleid met de naam Diagnostische instellingen configureren voor **opslagaccounts naar Log Analytics-werkruimte**.

    1. Klik op **het** tabblad Basisinformatie op de knop met de drie puntjes naast **Bereik** om uw abonnement (en eventueel een resourcegroep) te selecteren. U kunt ook een beschrijving toevoegen.

    1. Op het **tabblad Parameters:**
        - Kies uw Azure Sentinel werkruimte in de **vervolgkeuzelijst Log** Analytics-werkruimte.
        - Selecteer in de vervolgkeuzelijst **Opslagservices** de typen opslagresources (bestand, tabel, wachtrij, enzovoort) waarvoor u diagnostische instellingen wilt implementeren.
        - Laat de **velden Naam van** instelling en **Effect** zoals ze zijn.
        - De resterende vervolgkeuzevelden vertegenwoordigen de beschikbare diagnostische logboek- en metrische typen. Laat alle typen die u wilt opnemen gemarkeerd als Waar.

    1. Met de bovenstaande stappen wordt het beleid toegepast op alle toekomstige opslagbronnen. Als u het beleid wilt toepassen op uw bestaande resources, selecteert u **het** tabblad Herstel en selecteert u het selectievakje Een **hersteltaak** maken.

    1. Klik op het tabblad **Beoordelen en maken** op **Maken**. Uw beleid is nu toegewezen aan het bereik dat u hebt gekozen.

> [!NOTE]
>
> Met deze specifieke gegevensconnector worden de connectiviteitsstatusindicatoren (een kleurstree in de galerie met  gegevensconnectoren en verbindingspictogrammen naast de gegevenstypenamen) alleen weergegeven als verbonden (groen) als gegevens op een bepaald moment in de afgelopen 14 dagen zijn opgenomen. Zodra er 14 dagen zijn verstreken zonder dat er gegevens zijn opgenomen, wordt de verbinding met de connector verbroken. Zodra er meer gegevens worden verzameld, wordt *de status* verbonden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u verbinding kunt maken met Azure Storage-account Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over [het krijgen van inzicht in uw gegevens en mogelijke bedreigingen.](quickstart-get-visibility.md)
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
