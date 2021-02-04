---
title: 'Zelfstudie: Controles op naleving van regelgeving - Azure Security Center'
description: 'Zelfstudie: Meer informatie over het verbeteren van de naleving van uw regelgeving met behulp van Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550909"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Zelfstudie: Uw regelnaleving verbeteren

Met Azure Security Center kunt u ervoor zorgen dat het proces voldoet aan de wettelijke vereisten met behulp van het dashboard **Naleving van regelgeving**. 

Security Center doorlopend uw hybride cloud omgeving beoordelen om de risico factoren te analyseren op basis van de besturings elementen en aanbevolen procedures in de standaarden die op uw abonnementen worden toegepast. Het dash board weerspiegelt de status van uw naleving aan deze standaarden. 

Wanneer u Security Center op een Azure-abonnement inschakelt, wordt automatisch de [Azure Security-benchmark](../security/benchmarks/introduction.md)waarde toegewezen. Dit algemeen gerespecteerde Bench Mark bouwt voort op de besturings elementen van het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging.

Het nalevings Dashboard van de regelgeving toont de status van alle evaluaties in uw omgeving voor uw gekozen standaarden en voor Schriften. Terwijl u reageert op de aanbevelingen en u de risicofactoren in uw omgeving verkleint, wordt uw nalevingspostuur verbeterd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De naleving van uw regelgeving evalueren met het dashboard naleving van regelgeving
> * Uw nalevingspostuur verbeteren door op aanbevelingen te reageren
> * Waarschuwingen instellen voor wijzigingen in uw nalevings postuur
> * Uw compatibiliteits gegevens exporteren als een doorlopende stroom en als wekelijkse moment opnamen

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om de functies uit deze zelfstudie te doorlopen:

- [Azure Defender](azure-defender.md) moet zijn ingeschakeld. U kunt Azure Defender gratis uitproberen gedurende 30 dagen.
- U moet zijn aangemeld met een account dat leestoegang heeft tot de beleidsnalevingsgegevens (**Beveiligingslezer** is niet voldoende). De rol van **globale lezer** voor het abonnement werkt wel. U moet ten minste over de rollen **Inzender voor resourcebeleid** en **Beveiligingsbeheerder** beschikken.

##  <a name="assess-your-regulatory-compliance"></a>Naleving van uw regelgeving beoordelen

Het nalevings Dashboard van de regelgeving bevat uw geselecteerde nalevings standaarden met al hun vereisten, waarbij ondersteunde vereisten worden toegewezen aan toepasselijke beveiligings Beoordelingen. De status van deze evaluaties weerspiegelt uw naleving met de standaard.

Gebruik het regelgevings dashboard voor naleving om uw aandacht te vestigen op de gaten in naleving van uw gekozen standaarden en voor Schriften. Met deze weer gave gericht kunt u uw naleving continu in de loop van de tijd in dynamische Cloud-en hybride omgevingen bewaken.

1. Selecteer **Naleving van regelgeving** in het menu van Security Center.

    Boven aan het scherm bevindt zich een dash board met een overzicht van de nalevings status met de set ondersteunde nalevings regels. U ziet uw algemene nalevings Score en het aantal door gegeven versus mislukte evaluaties die zijn gekoppeld aan elke standaard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard voor naleving van regelgeving" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selecteer een tabblad voor een nalevingsstandaard die voor u relevant is (1). U ziet op welke abonnementen de standaard wordt toegepast (2) en de lijst met alle besturingselementen voor die standaard (3). Voor de van toepassing zijnde besturingselementen kunt u de details bekijken van positieve en negatieve beoordelingen met betrekking tot dat besturingselement (4), evenals de aantallen betrokken resources (5). Sommige besturingselementen zijn grijs weergegeven. Op deze besturingselementen zijn geen beoordelingen van het Azure Security Center van toepassing. Controleer de vereisten hiervoor en ze zelf in uw omgeving te beoordelen. Sommige hiervan kunnen procesgerelateerd zijn in plaats van technisch.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="De details van naleving met een specifieke standaard verkennen":::

1. Als u een PDF-rapport wilt genereren met een samen vatting van de huidige nalevings status voor een bepaalde standaard, selecteert u **rapport downloaden**.

    Het rapport biedt een samenvatting op hoog niveau van uw nalevingsstatus voor de geselecteerde standaard op basis van uw Security Center-beoordelingsgegevens en is geordend overeenkomstig de controles van die bepaalde standaard. Het rapport kan worden gedeeld met relevante belanghebbenden en dient mogelijk als bewijs voor interne en externe auditeurs.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Nalevingsrapport downloaden":::

## <a name="improve-your-compliance-posture"></a>De nalevingspostuur verbeteren

Met behulp van de informatie in het dash board voor naleving van regelgeving kunt u uw nalevings postuur verbeteren door aanbevelingen rechtstreeks in het dash board te herleiden.

1.  Klik op de negatieve beoordelingen die in het dashboard worden weergegeven om de details ervan te bekijken. Elke aanbeveling bevat een serie herstelstappen die dienen te worden gevolgd om het probleem op te lossen.

1.  Selecteer een bepaalde resource om meer details weer te geven en de aanbeveling voor die resource op te lossen. <br>Selecteer bijvoorbeeld in de **Azure CIS 1.1.0** -standaard de aanbeveling **schijf versleuteling moet worden toegepast op virtuele machines**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Het selecteren van een aanbeveling van een standaard leidt rechtstreeks naar de pagina met aanbevelingsgegevens":::

1. Als u in dit voor beeld **actie ondernemen** selecteert op de pagina met aanbevelings Details, ontvangt u de pagina's van de virtuele Azure-machine van de Azure Portal, waar u versleuteling kunt inschakelen op het tabblad **beveiliging** :

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="De knop actie uitvoeren op de pagina aanbevelingsgegevens leidt naar de herstelopties":::

    Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.

1.  Nadat u actie hebt ondernomen om aanbevelingen op te lossen, ziet u de impact van het rapport over het compatibiliteits dashboard, omdat uw compatibiliteits score wordt verbeterd.

    > [!NOTE]
    > Beoordelingen worden ongeveer elke 12 uur uitgevoerd, dus u ziet de impact ervan op uw nalevingsgegevens pas na de volgende uitvoering van de relevante beoordeling.


## <a name="export-your-compliance-status-data"></a>Uw nalevings status gegevens exporteren

Als u de nalevings status wilt bijhouden met andere controle hulpprogramma's in uw omgeving, bevat Security Center een export mechanisme om dit eenvoudig te maken. Configureer **continue export** om Selecteer gegevens te verzenden naar een Azure Event hub of een log Analytics-werk ruimte.

Continue export gegevens gebruiken naar een Azure Event hub of een Log Analytics-werk ruimte:

- Alle reglementaire nalevings gegevens in een **doorlopende stroom** exporteren:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Continu een stroom met reglementaire nalevings gegevens exporteren" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- **Wekelijkse moment opnamen** van uw reglementaire nalevings gegevens exporteren:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Doorlopend een wekelijkse moment opname van de nalevings gegevens van de regelgeving exporteren" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

U kunt ook een **PDF/CSV-rapport** van uw compatibiliteits gegevens rechtstreeks vanuit het regelgevings dashboard voor naleving exporteren:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Uw reglementaire nalevings gegevens exporteren als een PDF-of CSV-rapport" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Meer informatie over het [voortdurend exporteren van Security Center gegevens](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Werk stroom automatisering uitvoeren als er wijzigingen zijn aangebracht in uw naleving

De functie werk stroom automatisering van Security Center kan worden geactiveerd Logic Apps wanneer een van de nalevings beoordelings status van de regelgeving wordt gewijzigd.

U kunt bijvoorbeeld Security Center een e-mail bericht verzenden wanneer een nalevings beoordeling mislukt. U moet eerst de logische app maken (met behulp van [Azure Logic apps](../logic-apps/logic-apps-overview.md)) en vervolgens de trigger instellen in een nieuwe automatisering van werk stromen, zoals wordt beschreven in [reacties op Security Center triggers automatiseren](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Wijzigingen in de nalevings beoordeling van de regelgeving gebruiken om een werk stroom automatisering te activeren" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het dashboard naleving van regelgeving van het Azure Security Center kunt gebruiken om:

- Bekijk en bewaak uw nalevings postuur met betrekking tot de standaarden en voor Schriften die belang rijk voor u zijn.
- De nalevingsstatus te verbeteren door relevante beoordelingen op te lossen en te zien hoe de nalevingsscore verbetert.

Het nalevings Dashboard van de regelgeving kan het compatibiliteits proces aanzienlijk vereenvoudigen en de tijd die nodig is voor het verzamelen van nalevings bewijzen voor uw Azure-, hybride-en multi-cloud omgeving aanzienlijk verminderen.

Ga voor meer informatie naar deze gerelateerde pagina's:

- [De set met standaarden in uw nalevings dashboard aanpassen](update-regulatory-compliance-packages.md) : informatie over het selecteren van de standaarden die worden weer gegeven in het dash board voor naleving van regelgeving. 
- [Beveiligingsstatus controleren in Azure Security Center](security-center-monitoring.md): meer informatie over het controleren van de status van uw Azure-resources.
- [Aanbevelingen voor beveiliging in Azure Security Center beheren](security-center-recommendations.md): leer hoe u aanbevelingen in Azure Security Center kunt gebruiken om uw Azure-resources te beveiligen.