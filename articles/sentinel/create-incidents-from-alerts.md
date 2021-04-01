---
title: Incidenten maken op basis van waarschuwingen in azure Sentinel | Microsoft Docs
description: Meer informatie over het maken van incidenten op basis van waarschuwingen in azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 5c7c3d69bb26773171e9e0afc9f79ff25909a12a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99807289"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Automatisch incidenten maken op basis van beveiligings waarschuwingen van micro soft

Waarschuwingen die zijn geactiveerd in micro soft-beveiligings oplossingen die zijn verbonden met Azure Sentinel, zoals Microsoft Cloud App Security en micro soft Defender voor identiteit (voorheen Azure ATP), maken geen incidenten in azure Sentinel niet automatisch. Wanneer u een micro soft-oplossing verbindt met Azure Sentinel, wordt elke waarschuwing die in die service wordt gegenereerd, als onbewerkte gegevens opgeslagen in azure Sentinel, in de tabel beveiligings waarschuwing in uw Azure Sentinel-werk ruimte. U kunt deze gegevens vervolgens gebruiken, zoals andere onbewerkte gegevens die u in azure Sentinel maakt.

U kunt Azure Sentinel zo configureren dat er automatisch incidenten worden gemaakt telkens wanneer er een waarschuwing wordt geactiveerd in een verbonden micro soft-beveiligings oplossing, door de instructies in dit artikel te volgen.

## <a name="prerequisites"></a>Vereisten
U moet [micro soft-beveiligings oplossingen verbinden](connect-data-sources.md#data-connection-methods) om het maken van incidenten vanuit beveiligings Service waarschuwingen in te scha kelen.

## <a name="using-microsoft-security-incident-creation-analytics-rules"></a>Analytics-regels voor het maken van micro soft-beveiligings incidenten gebruiken

Gebruik de ingebouwde regels die beschikbaar zijn in azure Sentinel om te kiezen welke verbonden beveiligings oplossingen van micro soft Azure-Sentinel-incidenten automatisch in realtime moeten maken. U kunt ook de regels bewerken om meer specifieke opties te definiëren voor het filteren van de waarschuwingen die worden gegenereerd door de micro soft-beveiligings oplossing om incidenten te maken in azure Sentinel. U kunt bijvoorbeeld alleen Azure-Sentinel-incidenten automatisch maken op basis van waarschuwingen van Azure Defender (voorheen Azure Security Center) met hoge urgentie.

1. Selecteer in de Azure Portal onder Azure Sentinel **Analytics**.

1. Selecteer het tabblad **regel sjablonen** om alle ingebouwde analyse regels weer te geven.

    ![Regel sjablonen](media/incidents-from-alerts/rule-templates.png)

1. Kies de **micro soft security** Analytics-regel sjabloon die u wilt gebruiken en klik op **regel maken**.

    ![Regel voor beveiligings analyse](media/incidents-from-alerts/security-analytics-rule.png)

1. U kunt de regel details wijzigen en ervoor kiezen om de waarschuwingen te filteren waarmee incidenten worden gemaakt op basis van de ernst van de waarschuwing of de tekst in de naam van de waarschuwing.  
      
    Als u bijvoorbeeld kiest voor **Azure Defender** (kan nog steeds worden aangeroepen *Azure Security Center*) in het **veld micro soft-beveiligings service** en op **hoog** in het veld **filteren op Ernst** klikt, worden alleen incidenten met hoge urgentie en Azure Defender-waarschuwingen automatisch gemaakt in azure Sentinel.  

    ![Wizard regel maken](media/incidents-from-alerts/create-rule-wizard.png)

1. U kunt ook een nieuwe **micro soft-beveiligings** regel maken waarmee waarschuwingen van verschillende micro soft-beveiligings services worden gefilterd door te klikken op **+ maken** en de regel voor het maken van een **micro soft-incident** te selecteren

    ![Regel voor het maken van incidenten](media/incidents-from-alerts/incident-creation-rule.png)

  U kunt meer dan één **micro soft-beveiligings** analyse regel maken per type **beveiligings service van micro soft** . Er worden geen dubbele incidenten gemaakt, omdat elke regel wordt gebruikt als een filter. Zelfs als een waarschuwing overeenkomt met meer dan één **micro soft security** Analytics-regel, wordt er slechts één Azure Sentinel-incident gemaakt.

## <a name="enable-incident-generation-automatically-during-connection"></a>Genereren van incidenten automatisch inschakelen tijdens verbinding
 Wanneer u een micro soft-beveiligings oplossing verbindt, kunt u selecteren of u wilt dat de waarschuwingen van de beveiligings oplossing automatisch incidenten in azure Sentinel automatisch genereren.

1. Verbind een gegevens bron met een micro soft-beveiligings oplossing. 

   ![Beveiligings incidenten genereren](media/incidents-from-alerts/generate-security-incidents.png)

1. Selecteer **inschakelen** onder **incidenten maken** om de standaard analyse regel in te scha kelen die incidenten automatisch maakt op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligings service. U kunt deze regel vervolgens bewerken onder **analyse** en vervolgens op **actieve regels**.

## <a name="next-steps"></a>Volgende stappen

- U moet een Microsoft Azure-abonnement hebben om met Azure Sentinel aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- Leer hoe u [uw gegevens kunt onboarden in Azure Sentinel](quickstart-onboard.md) en [krijg inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
