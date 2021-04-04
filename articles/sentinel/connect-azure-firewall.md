---
title: Azure Firewall gegevens verbinden met Azure Sentinel
description: Meer informatie over het verbinden van Azure Firewall gegevens met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: b21ce75bfb33b5a8869c63b7d3f71fb9f0c93768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621292"
---
# <a name="connect-data-from-azure-firewall"></a>Verbinding maken met gegevens van Azure Firewall

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledig stateful firewall-as-a-service met ingebouwde hoge Beschik baarheid en een onbeperkte Cloud schaalbaarheid. 

U kunt Azure Firewall-logboeken verbinden met Azure-Sentinel, zodat u logboek gegevens in werkmappen weer geven, kunt gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om uw onderzoek te verbeteren.

Meer informatie over het [bewaken van Azure firewall-logboeken](../firewall/firewall-diagnostics.md).

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

## <a name="connect-to-azure-firewall"></a>Verbinding maken met Azure Firewall
    
1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**.

1. Selecteer **Azure firewall** in de galerie met gegevens connectors en selecteer vervolgens **pagina connector openen**  in het voorbeeld venster.

1. Schakel **Diagnostische logboeken** in op alle firewalls waarvan u de logboeken wilt maken:

    1. Selecteer de koppeling **Open Azure firewall resource >** .

    1. Selecteer **Diagnostische instellingen** in het navigatie menu van **firewalls** .

    1. Selecteer **+ Diagnostische instelling toevoegen** onder aan de lijst.

    1. Voer in het scherm **Diagnostische instellingen** een naam in het veld  **naam Diagnostische instellingen** in.
    
    1. Schakel het selectie vakje **verzenden naar log Analytics** in. Er worden twee nieuwe velden weer gegeven. Kies het relevante **abonnement** en de **log Analytics werk ruimte** (waar Azure Sentinel zich bevindt).

    1. Schakel de selectie vakjes in van de regel typen waarvan u de logboeken wilt opnemen. We raden **AzureFirewallApplicationRule** en **AzureFirewallNetworkRule** aan.

    1. Selecteer **Opslaan** boven aan het scherm.

1. Als u het relevante schema in Log Analytics voor Azure Firewall-waarschuwingen wilt gebruiken, zoekt u naar **AzureDiagnostics**.

> [!NOTE]
>
> Met deze specifieke gegevens connector wordt de verbindings status indicator (een kleur balk in de galerie met gegevens connectors en verbindings pictogrammen naast de namen van gegevens typen) alleen weer gegeven als *verbonden* (groen) als de gegevens op een bepaald moment in de afgelopen twee weken zijn opgenomen. Als er twee weken zijn geslaagd zonder opname van gegevens, wordt de connector weer gegeven als niet-verbonden. Op het moment dat er meer gegevens worden opgehaald, wordt de status *verbonden* geretourneerd.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure Firewall-logboeken met Azure Sentinel verbindt. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).