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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: b0baa532e8ca986e76cfb938a198d8a8697bd4dd
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757692"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Zelfstudie: Uw regelnaleving verbeteren

Met Azure Security Center kunt u ervoor zorgen dat het proces voldoet aan de wettelijke vereisten met behulp van het dashboard **Naleving van regelgeving**. 

Security Center voert doorlopende evaluaties uit van uw hybride cloud omgeving om de risico factoren te analyseren op basis van de besturings elementen en aanbevolen procedures in de standaarden die op uw abonnementen worden toegepast. Het dash board weerspiegelt de status van uw naleving aan deze standaarden. 

Als u Security Center inschakelt voor een Azure-abonnement, wordt automatisch de [Azure Security-benchmark](../security/benchmarks/introduction.md)waarde toegewezen. Dit algemeen gerespecteerde Bench Mark bouwt voort op de besturings elementen van het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging.

In het dashboard naleving van regelgeving ziet u in de context van een bepaalde regelgevingsstandaard een overzicht van de status van deze evaluaties binnen uw omgeving. Terwijl u reageert op de aanbevelingen en u de risicofactoren in uw omgeving verkleint, wordt uw nalevingspostuur verbeterd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De naleving van uw regelgeving evalueren met het dashboard naleving van regelgeving
> * Uw nalevingspostuur verbeteren door op aanbevelingen te reageren

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om de functies uit deze zelfstudie te doorlopen:

- [Azure Defender](azure-defender.md) moet zijn ingeschakeld. U kunt Azure Defender gratis uitproberen gedurende 30 dagen.
- U moet zijn aangemeld met een account dat leestoegang heeft tot de beleidsnalevingsgegevens (**Beveiligingslezer** is niet voldoende). De rol van **globale lezer** voor het abonnement werkt wel. U moet ten minste over de rollen **Inzender voor resourcebeleid** en **Beveiligingsbeheerder** beschikken.

##  <a name="assess-your-regulatory-compliance"></a>Naleving van uw regelgeving beoordelen

Het nalevings Dashboard van de regelgeving bevat uw geselecteerde nalevings standaarden met al hun vereisten, waarbij ondersteunde vereisten worden toegewezen aan toepasselijke beveiligings Beoordelingen. De status van deze evaluaties weerspiegelt uw naleving met de standaard.

Gebruik het regelgevings dashboard voor naleving om uw aandacht te vestigen op de leemtes in overeenstemming met de standaarden en voor Schriften die voor u van belang zijn. Met deze weer gave gericht kunt u uw naleving continu in de loop van de tijd in dynamische Cloud-en hybride omgevingen bewaken.

1. Selecteer **Naleving van regelgeving** in het menu van Security Center.

    Boven aan het scherm bevindt zich een dash board met een overzicht van de nalevings status met de set ondersteunde nalevings regels. U ziet de totale nalevingsscore en het aantal positieve en negatieve beoordelingen die op elke standaard van toepassing zijn.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard voor naleving van regelgeving":::

1. Selecteer een tabblad voor een nalevingsstandaard die voor u relevant is (1). U ziet op welke abonnementen de standaard wordt toegepast (2) en de lijst met alle besturingselementen voor die standaard (3). Voor de van toepassing zijnde besturingselementen kunt u de details bekijken van positieve en negatieve beoordelingen met betrekking tot dat besturingselement (4), evenals de aantallen betrokken resources (5). Sommige besturingselementen zijn grijs weergegeven. Op deze besturingselementen zijn geen beoordelingen van het Azure Security Center van toepassing. Controleer de vereisten hiervoor en ze zelf in uw omgeving te beoordelen. Sommige hiervan kunnen procesgerelateerd zijn in plaats van technisch.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="De details van naleving met een specifieke standaard verkennen":::

1. Als u een PDF-rapport wilt genereren met een samen vatting van de huidige nalevings status voor een bepaalde standaard, selecteert u **rapport downloaden**.

    Het rapport biedt een samenvatting op hoog niveau van uw nalevingsstatus voor de geselecteerde standaard op basis van uw Security Center-beoordelingsgegevens en is geordend overeenkomstig de controles van die bepaalde standaard. Het rapport kan worden gedeeld met relevante belanghebbenden en dient mogelijk als bewijs voor interne en externe auditeurs.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Nalevingsrapport downloaden":::

## <a name="improve-your-compliance-posture"></a>De nalevingspostuur verbeteren

Met de informatie in het dashboard naleving van regelgeving kunt u uw nalevingspostuur verbeteren door aanbevelingen rechtstreeks vanuit het dashboard op te lossen.

1.  Klik op de negatieve beoordelingen die in het dashboard worden weergegeven om de details ervan te bekijken. Elke aanbeveling bevat een serie herstelstappen die dienen te worden gevolgd om het probleem op te lossen.

1.  U kunt een bepaalde resource selecteren om meer details te zien en de aanbeveling voor die resource op te lossen. <br>In de **Azure CIS 1.1.0** -standaard kunt u bijvoorbeeld de aanbeveling **schijf versleuteling selecteren die moet worden toegepast op virtuele machines**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Het selecteren van een aanbeveling van een standaard leidt rechtstreeks naar de pagina met aanbevelingsgegevens":::

1. In dit voorbeeld selecteert u **Actie ondernemen** van de pagina aanbevelingsgegevens, dan komt u bij de pagina's van de virtuele Azure-machine van de Azure-portal, waar u het tabblad **Beveiliging** kunt openen en encryptie kunt inschakelen:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="De knop actie uitvoeren op de pagina aanbevelingsgegevens leidt naar de herstelopties":::

    Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.

1.  Nadat u actie hebt ondernomen om aanbevelingen op te lossen, ziet u de impact ervan in het dashboardrapport over de naleving omdat de nalevingsscore is verbeterd.

    > [!NOTE]
    > Beoordelingen worden ongeveer elke 12 uur uitgevoerd, dus u ziet de impact ervan op uw nalevingsgegevens pas na de volgende uitvoering van de relevante beoordeling.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het dashboard naleving van regelgeving van het Azure Security Center kunt gebruiken om:

-   Uw nalevingspostuur te bekijken en bewaken met betrekking tot de standaarden en richtlijnen die voor u van belang zijn.
-   De nalevingsstatus te verbeteren door relevante beoordelingen op te lossen en te zien hoe de nalevingsscore verbetert.

Met het dashboard naleving van regelgeving kunt u het nalevingsproces fors vereenvoudigen en de benodigde tijd voor het verzamelen van bewijzen van naleving voor uw Azure- en hybride omgeving aanzienlijk verkorten.

Zie voor meer informatie deze gerelateerde artikelen:

-   [Bijwerken naar dynamische nalevingspakketten in uw nalevingsdashboard (preview)](update-regulatory-compliance-packages.md): meer informatie over deze preview-functie waarmee u de standaarden die worden weergegeven in het nalevingsdashboard kunt bijwerken naar de nieuwe *dynamische* pakketten. U kunt dezelfde preview-functie ook gebruiken om nieuwe nalevingspakketten toe te voegen en uw naleving met aanvullende standaarden te bewaken. 
-   [Beveiligingsstatus controleren in Azure Security Center](security-center-monitoring.md): meer informatie over het controleren van de status van uw Azure-resources.
-   [Aanbevelingen voor beveiliging in Azure Security Center beheren](security-center-recommendations.md): leer hoe u aanbevelingen in Azure Security Center kunt gebruiken om uw Azure-resources te beveiligen.
-   [Uw beveiligingsscore in Azure Security Center verbeteren](secure-score-security-controls.md): leer hoe u prioriteit kunt geven aan kwetsbaarheden en aanbevelingen over beveiliging om uw beveiligingspostuur zo goed mogelijk te verbeteren.
