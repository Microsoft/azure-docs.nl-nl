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
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: fb8dc22c923b7b53a6263baa43046862af4d2f04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370258"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Zelfstudie: Uw regelnaleving verbeteren

Met Azure Security Center kunt u ervoor zorgen dat het proces voldoet aan de wettelijke vereisten met behulp van het dashboard **Naleving van regelgeving**. 

Security Center doorlopend uw hybride cloud omgeving beoordelen om de risico factoren te analyseren op basis van de besturings elementen en aanbevolen procedures in de standaarden die op uw abonnementen worden toegepast. Het dash board weerspiegelt de status van uw naleving aan deze standaarden. 

Als u Security Center inschakelt voor een Azure-abonnement, wordt de [Azure Security-Bench Mark](../security/benchmarks/introduction.md) automatisch toegewezen aan dat abonnement. Dit algemeen gerespecteerde Bench Mark bouwt voort op de besturings elementen van het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging.

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
- U moet zijn aangemeld met een account dat lees toegang heeft tot de gegevens van de beleids naleving (**beveiligings lezer** is onvoldoende). De rol van **globale lezer** voor het abonnement werkt wel. U moet ten minste over de rollen **Inzender voor resourcebeleid** en **Beveiligingsbeheerder** beschikken.

##  <a name="assess-your-regulatory-compliance"></a>Naleving van uw regelgeving beoordelen

Het nalevings Dashboard van de regelgeving bevat uw geselecteerde nalevings standaarden met al hun vereisten, waarbij ondersteunde vereisten worden toegewezen aan toepasselijke beveiligings Beoordelingen. De status van deze evaluaties weerspiegelt uw naleving met de standaard.

Gebruik het regelgevings dashboard voor naleving om uw aandacht te vestigen op de gaten in naleving van uw gekozen standaarden en voor Schriften. Met deze weer gave gericht kunt u uw naleving continu in de loop van de tijd in dynamische Cloud-en hybride omgevingen bewaken.

1. Selecteer **Naleving van regelgeving** in het menu van Security Center.

    Boven aan het scherm bevindt zich een dash board met een overzicht van de nalevings status met de set ondersteunde nalevings regels. U ziet uw algemene nalevings Score en het aantal door gegeven versus mislukte evaluaties die zijn gekoppeld aan elke standaard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard voor naleving van regelgeving" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selecteer een tabblad voor een nalevingsstandaard die voor u relevant is (1). U ziet op welke abonnementen de standaard wordt toegepast (2) en de lijst met alle besturingselementen voor die standaard (3). Voor de toepasselijke besturings elementen kunt u de details weer geven van de door gegeven en mislukte evaluaties die zijn gekoppeld aan dat besturings element (4) en het aantal betrokken resources (5). Sommige besturingselementen zijn grijs weergegeven. Op deze besturingselementen zijn geen beoordelingen van het Azure Security Center van toepassing. Controleer hun vereisten en beoordeel ze in uw omgeving. Sommige hiervan kunnen procesgerelateerd zijn in plaats van technisch.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="De details van naleving met een specifieke standaard verkennen":::

1. Als u een PDF-rapport wilt genereren met een samen vatting van de huidige nalevings status voor een bepaalde standaard, selecteert u **rapport downloaden**.

    Het rapport bevat een overzicht op hoog niveau van de nalevings status voor de geselecteerde standaard op basis van Security Center beoordelings gegevens. De indeling van het rapport op basis van de besturings elementen van die specifieke norm. Het rapport kan worden gedeeld met relevante belanghebbenden en dient mogelijk als bewijs voor interne en externe auditeurs.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Nalevingsrapport downloaden":::

## <a name="improve-your-compliance-posture"></a>De nalevingspostuur verbeteren

Met behulp van de informatie in het dash board voor naleving van regelgeving kunt u uw nalevings postuur verbeteren door aanbevelingen rechtstreeks in het dash board te herleiden.

1.  Selecteer een van de mislukte evaluaties die in het dash board worden weer gegeven om de Details voor die aanbeveling weer te geven. Elke aanbeveling bevat een reeks herstels tappen om het probleem op te lossen.

1.  Selecteer een bepaalde resource om meer details weer te geven en de aanbeveling voor die resource op te lossen. <br>Selecteer bijvoorbeeld in de **Azure CIS 1.1.0** -standaard de aanbeveling **schijf versleuteling moet worden toegepast op virtuele machines**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Het selecteren van een aanbeveling van een standaard leidt rechtstreeks naar de pagina met aanbevelingsgegevens":::

1. Als u in dit voor beeld **actie ondernemen** selecteert op de pagina met aanbevelings Details, ontvangt u de pagina's van de virtuele Azure-machine van de Azure Portal, waar u versleuteling kunt inschakelen op het tabblad **beveiliging** :

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="De knop actie uitvoeren op de pagina aanbevelingsgegevens leidt naar de herstelopties":::

    Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.

1.  Nadat u actie hebt ondernomen om aanbevelingen op te lossen, ziet u het resultaat in het rapport nalevings dashboard, omdat uw compatibiliteits score wordt verbeterd.

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

U kunt bijvoorbeeld Security Center een e-mail bericht verzenden wanneer een nalevings beoordeling mislukt. U moet eerst de logische app maken (met behulp van [Azure Logic apps](../logic-apps/logic-apps-overview.md)) en vervolgens de trigger instellen in een nieuwe automatisering van werk stromen, zoals wordt uitgelegd in [reacties op Security Center triggers automatiseren](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Wijzigingen in de nalevings beoordeling van de regelgeving gebruiken om een werk stroom automatisering te activeren" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Veelgestelde vragen: dashboard voor Naleving van regelgeving

- [Welke standaarden worden ondersteund in het dash board voor naleving?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Waarom worden sommige besturings elementen lichter gekleurd weer gegeven?](#why-do-some-controls-appear-grayed-out)
- [Hoe kan ik een ingebouwde standaard, zoals PCI-DSS, ISO 27001, of SOC2-TSP verwijderen uit het dash board?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Ik heb de voorgestelde wijzigingen op basis van de aanbeveling aangebracht, maar deze worden niet doorgevoerd in het dash board](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Welke machtigingen heb ik nodig om toegang te krijgen tot het dash board voor naleving?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Het dash board nalevings regelgeving wordt niet voor mij geladen](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Hoe kan ik een rapport bekijken over het door geven en uitvallen van mislukte besturings elementen in mijn dash board?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Hoe kan ik een rapport met compatibiliteits gegevens in een andere indeling dan PDF downloaden?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Hoe kan ik uitzonde ringen maken voor een aantal beleids regels in het dash board nalevings beleid?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Wat voor Azure Defender-plannen of-licenties moet ik het regelgevings dashboard voor naleving gebruiken?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Welke standaarden worden ondersteund in het dash board voor naleving?
Standaard bevat het dash board nalevings beleid de beveiligings benchmarks van Azure. De Azure Security Bench Mark is de door micro soft ontworpen, specifieke Azure-richt lijnen voor beveiliging en aanbevolen procedures voor naleving op basis van algemene nalevings raamwerken. Meer informatie vindt u in de [Inleiding tot Azure Security Bench Mark](../security/benchmarks/introduction.md).

Als u de naleving van een andere standaard wilt bijhouden, moet u deze expliciet toevoegen aan uw dash board.
 
U kunt standaarden toevoegen zoals Azure CIS 1.1.0 (nieuw), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, UK Official en UK NHS, HIPAA HITRUST, Canada Federal PBMM, ISO 27001, SOC2-TSP en PCI-DSS 3.2.1.  
 
Er worden meer standaarden toegevoegd aan het dash board en opgenomen in de informatie over [het aanpassen van de set normen in uw nalevings dashboard](update-regulatory-compliance-packages.md).

### <a name="why-do-some-controls-appear-grayed-out"></a>Waarom worden sommige besturings elementen lichter gekleurd weer gegeven?
Voor elke nalevings norm in het dash board ziet u een lijst met de standaard besturings elementen. Voor de toepasselijke besturings elementen kunt u de Details bekijken van het door geven en mislukken van evaluaties.

Sommige besturingselementen zijn grijs weergegeven. Op deze besturingselementen zijn geen beoordelingen van het Azure Security Center van toepassing. Sommige kunnen procedures of proces gerelateerd zijn, en kunnen daarom niet worden geverifieerd door Security Center. Sommige automatische beleids regels of beoordelingen zijn nog niet geïmplementeerd, maar zullen in de toekomst. En sommige besturings elementen zijn mogelijk de verantwoordelijkheid van het platform, zoals wordt uitgelegd in [de gedeelde verantwoordelijkheid in de Cloud](../security/fundamentals/shared-responsibility.md). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Hoe kan ik een ingebouwde standaard, zoals PCI-DSS, ISO 27001, of SOC2-TSP verwijderen uit het dash board? 
Als u het nalevings Dashboard van de regelgeving wilt aanpassen en de focus alleen wilt richten op de standaarden die van toepassing zijn op u, kunt u de weer gegeven regelgevings normen verwijderen die niet relevant zijn voor uw organisatie. Volg de instructies in [een standaard van uw dash board verwijderen](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)als u een standaard wilt verwijderen.

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Ik heb de voorgestelde wijzigingen op basis van de aanbeveling aangebracht, maar deze worden niet doorgevoerd in het dash board
Nadat u actie hebt ondernomen om aanbevelingen op te lossen, moet u 12 uur wachten om de wijzigingen in uw compatibiliteits gegevens te zien. Evaluaties worden ongeveer elke 12 uur uitgevoerd, zodat het effect van uw nalevings gegevens pas wordt weer gegeven nadat de evaluaties zijn uitgevoerd.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Welke machtigingen heb ik nodig om toegang te krijgen tot het dash board voor naleving?
Als u compatibiliteits gegevens wilt bekijken, moet u ook **ten minste toegang hebben tot de** beleids nalevings gegevens. de beveiligings lezer is dus alleen niet voldoende. Als u een wereld wijde lezer van het abonnement bent, is dat ook voldoende.

De minimale set rollen voor het verkrijgen van toegang tot het dash board en het beheren van standaarden is **resource Policy contributor** en **beveiligings beheerder**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Het dash board nalevings regelgeving wordt niet voor mij geladen
Als u het dash board voor nalevings vereisten wilt gebruiken, moet Azure Security Center Azure Defender hebben ingeschakeld op het abonnements niveau. Als het dash board niet correct wordt geladen, voert u de volgende stappen uit:

1. Wis de cache van uw browser.
1. Probeer een andere browser.
1. Probeer het dash board te openen vanaf een andere netwerk locatie.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Hoe kan ik een rapport bekijken over het door geven en uitvallen van mislukte besturings elementen in mijn dash board?
Op het hoofd-dash board ziet u een rapport van de besturings elementen voor het door geven en mislukken van (1) de ' Top 4 ' van de laagste nalevings standaarden in het dash board. Selecteer (2) **alle *x* weer geven** (waarbij x staat voor het aantal standaarden dat u bijhoudt) als u de status van besturings elementen voor door geven/mislukken wilt bekijken. In een context vlak wordt de nalevings status weer gegeven voor elke van uw bijgehouden standaarden.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Overzichts sectie van het dash board nalevings regelgeving":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Hoe kan ik een rapport met compatibiliteits gegevens in een andere indeling dan PDF downloaden?
Wanneer u **rapport downloaden** selecteert, selecteert u de standaard en de indeling (PDF of CSV). In het rapport wordt de huidige set abonnementen weer gegeven die u hebt geselecteerd in het filter van de portal.

- In het PDF-rapport wordt een samenvattings status weer gegeven voor de standaard die u hebt geselecteerd.
- Het CSV-rapport bevat gedetailleerde resultaten per resource, aangezien deze betrekking heeft op beleids regels die zijn gekoppeld aan elk besturings element

Er is momenteel geen ondersteuning voor het downloaden van een rapport voor een aangepast beleid. alleen voor de opgegeven regelgevings normen.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Hoe kan ik uitzonde ringen maken voor een aantal beleids regels in het dash board nalevings beleid?
Voor beleids regels die zijn ingebouwd in Security Center en die zijn opgenomen in de beveiligde Score, kunt u uitzonde ringen maken voor een of meer resources, direct in de portal, zoals wordt uitgelegd in het [uitsluiten van resources en aanbevelingen van uw beveiligde Score](exempt-resource.md).

Voor andere beleids regels kunt u rechtstreeks in het beleid zelf een uitsluiting maken door de instructies in de [structuur van Azure Policy-uitzonde ring](../governance/policy/concepts/exemption-structure.md)te volgen.


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Wat voor Azure Defender-plannen of-licenties moet ik het regelgevings dashboard voor naleving gebruiken?
Als u een van de Azure Defender-pakketten hebt ingeschakeld in een van uw Azure-resource typen, hebt u toegang tot het nalevings Dashboard van de regelgeving, met alle bijbehorende gegevens, in Security Center.





## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het dashboard naleving van regelgeving van het Azure Security Center kunt gebruiken om:

> [!div class="checklist"]
> * Bekijk en bewaak uw nalevings postuur met betrekking tot de standaarden en voor Schriften die belang rijk voor u zijn.
> * De nalevingsstatus te verbeteren door relevante beoordelingen op te lossen en te zien hoe de nalevingsscore verbetert.

Het nalevings Dashboard van de regelgeving kan het compatibiliteits proces aanzienlijk vereenvoudigen en de tijd die nodig is voor het verzamelen van nalevings bewijzen voor uw Azure-, hybride-en multi-cloud omgeving aanzienlijk verminderen.

Ga voor meer informatie naar deze gerelateerde pagina's:

- [De set met standaarden in uw nalevings dashboard aanpassen](update-regulatory-compliance-packages.md) : informatie over het selecteren van de standaarden die worden weer gegeven in het dash board voor naleving van regelgeving. 
- [Aanbevelingen voor beveiliging beheren in azure Security Center](security-center-recommendations.md) -meer informatie over het gebruik van aanbevelingen in Security Center om uw Azure-resources te beveiligen.