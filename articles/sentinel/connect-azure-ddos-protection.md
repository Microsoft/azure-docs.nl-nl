---
title: Azure DDoS Protection gegevens verbinden met Azure Sentinel
description: Meer informatie over het opnemen van Azure DDoS Protection gegevens in azure Sentinel, zodat u deze kunt bekijken, analyseren en onderzoeken.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: b5d98a834ed4f87fb95a13e666967a7b2d80975d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656051"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Verbinding maken met gegevens van Azure DDoS Protection

> [!IMPORTANT]
> De Azure DDoS Protection gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met DDoS-aanvallen (Distributed Denial of service) wordt geprobeerd om de resources van een toepassing te laten uitgeputen, waardoor de toepassing niet beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet. [Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md), gecombineerd met aanbevolen procedures voor het ontwerpen van toepassingen, biedt een robuuste verdediging tegen DDoS-aanvallen. U kunt Azure DDoS Protection-logboeken verbinden met Azure-Sentinel, zodat u logboek gegevens in werkmappen weer geven, kunt gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om uw onderzoek te verbeteren. 

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- U moet beschikken over een geconfigureerd [Azure DDoS Standard-beveiligings plan](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).

- U moet een geconfigureerd [virtueel netwerk hebben waarvoor Azure DDoS Standard is ingeschakeld](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Verbinding maken met Azure DDoS Protection
    
1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**.

1. Selecteer **Azure DDoS Protection** in de galerie met gegevens connectors en selecteer vervolgens **pagina connector openen** in het voorbeeld venster.

1. Schakel **Diagnostische logboeken** in op alle firewalls waarvan u de logboeken wilt maken:

    1. Selecteer de koppeling **Diagnostische instellingen openen >** en kies een **open bare IP-adres** resource in de lijst.

    1. Selecteer **+ Diagnostische instelling toevoegen**.

    1. In het scherm **Diagnostische instellingen** :
       - Voer een naam in het veld naam van de  **Diagnostische instelling** in.

       - Schakel het selectie vakje **verzenden naar log Analytics** in. Er worden twee nieuwe velden weer gegeven. Kies het relevante **abonnement** en de **log Analytics werk ruimte** (waar Azure Sentinel zich bevindt).

       - Schakel de selectie vakjes in van de regel typen waarvan u de logboeken wilt opnemen. U kunt het beste **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs** en **DDoSMitigationReports**.

    1. Klik boven aan het scherm op **Opslaan** . Herhaal dit proces voor alle extra firewalls (open bare IP-adressen) waarvoor u DDoS-beveiliging hebt ingeschakeld.

1. Als u het relevante schema in Log Analytics voor Azure DDoS Protection-waarschuwingen wilt gebruiken, zoekt u naar **AzureDiagnostics**.

> [!NOTE]
>
> Met deze specifieke gegevens connector wordt de verbindings status indicator (een kleur balk in de galerie met gegevens connectors en verbindings pictogrammen naast de namen van gegevens typen) alleen weer gegeven als *verbonden* (groen) als de gegevens op een bepaald moment in de afgelopen twee weken zijn opgenomen. Als er twee weken zijn geslaagd zonder opname van gegevens, wordt de connector weer gegeven als niet-verbonden. Op het moment dat er meer gegevens worden opgehaald, wordt de status *verbonden* geretourneerd.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure DDoS Protection-logboeken met Azure Sentinel verbindt. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).