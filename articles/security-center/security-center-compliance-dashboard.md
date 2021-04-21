---
title: 'Zelfstudie: Controles op naleving van regelgeving - Azure Security Center'
description: 'Zelfstudie: Meer informatie over het verbeteren van de naleving van uw regelgeving met behulp van Azure Security Center.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: c8ac9079321e47a1e6d9b8689be46bf55bdd4243
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834607"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Zelfstudie: Uw regelnaleving verbeteren

Met Azure Security Center kunt u ervoor zorgen dat het proces voldoet aan de wettelijke vereisten met behulp van het dashboard **Naleving van regelgeving**. 

Security Center evalueert continu uw hybride cloudomgeving om de risicofactoren te analyseren op basis van de controles en best practices in de standaarden die worden toegepast op uw abonnementen. Het dashboard geeft de status van uw naleving aan deze standaarden weer. 

Wanneer u een Security Center azure-abonnement inschakelen, wordt de [Azure Security-benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) automatisch toegewezen aan dat abonnement. Deze breed gerespecteerde benchmark bouwt voort op de controles van het [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) met de focus op cloudgerichte beveiliging.

Het dashboard voor naleving van regelgeving toont de status van alle evaluaties binnen uw omgeving voor uw gekozen standaarden en voorschriften. Terwijl u reageert op de aanbevelingen en u de risicofactoren in uw omgeving verkleint, wordt uw nalevingspostuur verbeterd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De naleving van uw regelgeving evalueren met het dashboard naleving van regelgeving
> * Uw nalevingspostuur verbeteren door op aanbevelingen te reageren
> * Waarschuwingen instellen voor wijzigingen in uw nalevingsstatus
> * Uw nalevingsgegevens exporteren als een continue stroom en als wekelijkse momentopnamen

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om de functies uit deze zelfstudie te doorlopen:

- [Azure Defender](azure-defender.md) moet zijn ingeschakeld. U kunt Azure Defender gratis uitproberen gedurende 30 dagen.
- U moet zijn aangemeld met een account met leestoegang tot de nalevingsgegevens van het beleid **(Beveiligingslezer** is onvoldoende). De rol van **globale lezer** voor het abonnement werkt wel. U moet ten minste over de rollen **Inzender voor resourcebeleid** en **Beveiligingsbeheerder** beschikken.

##  <a name="assess-your-regulatory-compliance"></a>Naleving van uw regelgeving beoordelen

Het dashboard voor naleving van regelgeving toont uw geselecteerde nalevingsstandaarden met alle vereisten, waarbij ondersteunde vereisten worden toegepast op toepasselijke beveiligingsevaluaties. De status van deze evaluaties weerspiegelt uw naleving van de standaard.

Gebruik het dashboard voor naleving van regelgeving om uw aandacht te richten op de hiaten in de naleving van uw gekozen standaarden en voorschriften. Deze gerichte weergave stelt u ook in staat om uw naleving gedurende een periode continu te bewaken binnen dynamische cloud- en hybride omgevingen.

1. Selecteer **Naleving van regelgeving** in het menu van Security Center.

    Bovenaan het scherm ziet u een dashboard met een overzicht van uw nalevingsstatus met de set ondersteunde nalevingsvoorschriften. U ziet uw algemene nalevingsscore en het aantal door te geven versus mislukte evaluaties die aan elke standaard zijn gekoppeld.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard voor naleving van regelgeving" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selecteer een tabblad voor een nalevingsstandaard die voor u relevant is (1). U ziet op welke abonnementen de standaard wordt toegepast (2) en de lijst met alle besturingselementen voor die standaard (3). Voor de toepasselijke besturingselementen kunt u de details bekijken van het doorgeven en mislukken van evaluaties die zijn gekoppeld aan dat besturingselement (4) en het aantal betrokken resources (5). Sommige besturingselementen zijn grijs weergegeven. Op deze besturingselementen zijn geen beoordelingen van het Azure Security Center van toepassing. Controleer de vereisten en beoordeel deze in uw omgeving. Sommige hiervan kunnen procesgerelateerd zijn in plaats van technisch.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="De details van naleving met een specifieke standaard verkennen":::

1. Als u een PDF-rapport wilt genereren met een samenvatting van uw huidige nalevingsstatus voor een bepaalde standaard, **selecteert u Rapport downloaden.**

    Het rapport bevat een overzicht op hoog niveau van uw nalevingsstatus voor de geselecteerde standaard op basis Security Center evaluatiegegevens. Het rapport is ingedeeld op basis van de besturingselementen van die specifieke standaard. Het rapport kan worden gedeeld met relevante belanghebbenden en dient mogelijk als bewijs voor interne en externe auditeurs.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Nalevingsrapport downloaden":::

## <a name="improve-your-compliance-posture"></a>De nalevingspostuur verbeteren

Verbeter met behulp van de informatie in het dashboard voor naleving van regelgeving uw nalevingsstatus door aanbevelingen rechtstreeks in het dashboard op te lossen.

1.  Selecteer een van de mislukte evaluaties die in het dashboard worden weergegeven om de details voor die aanbeveling weer te geven. Elke aanbeveling bevat een reeks herstelstappen om het probleem op te lossen.

1.  Selecteer een bepaalde resource om meer details weer te geven en de aanbeveling voor die resource op te lossen. <br>Selecteer bijvoorbeeld in de **Azure CIS 1.1.0-standaard** de aanbeveling Schijfversleuteling **moet worden toegepast op virtuele machines.**

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Het selecteren van een aanbeveling van een standaard leidt rechtstreeks naar de pagina met aanbevelingsgegevens":::

1. Wanneer u in dit  voorbeeld Actie ondernemen selecteert op de pagina met aanbevelingsdetails, komt u aan op de pagina's van de virtuele Azure-machine van de Azure Portal, waar u versleuteling kunt inschakelen op het **tabblad** Beveiliging:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="De knop actie uitvoeren op de pagina aanbevelingsgegevens leidt naar de herstelopties":::

    Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.

1.  Nadat u actie hebt ondernomen om aanbevelingen op te lossen, ziet u het resultaat in het nalevingsdashboardrapport omdat uw nalevingsscore wordt verbeterd.

    > [!NOTE]
    > Beoordelingen worden ongeveer elke 12 uur uitgevoerd, dus u ziet de impact ervan op uw nalevingsgegevens pas na de volgende uitvoering van de relevante beoordeling.


## <a name="export-your-compliance-status-data"></a>Uw nalevingsstatusgegevens exporteren

Als u uw nalevingsstatus wilt bijhouden met andere bewakingshulpprogramma's in uw omgeving, Security Center een exportmechanisme om dit eenvoudig te maken. Configureer **continue export** om bepaalde gegevens te verzenden naar een Azure Event Hub of een Log Analytics-werkruimte.

Gebruik continue exportgegevens naar een Azure Event Hub of een Log Analytics-werkruimte:

- Exporteert alle nalevingsgegevens van regelgeving in een **doorlopende stroom:**

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Continu een stroom van nalevingsgegevens voor regelgeving exporteren" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Wekelijkse **momentopnamen van uw nalevingsgegevens** voor regelgeving exporteren:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Continu een wekelijkse momentopname van nalevingsgegevens van regelgeving exporteren" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

U kunt ook een **PDF-/CSV-rapport met** uw nalevingsgegevens rechtstreeks vanuit het dashboard voor naleving van regelgeving exporteren:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Uw nalevingsgegevens voor regelgeving exporteren als pdf- of CSV-rapport" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Meer informatie in [Continu gegevens exporteren Security Center gegevens](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Werkstroomautomatiseringen uitvoeren wanneer er wijzigingen in uw naleving zijn

Security Center functie voor werkstroomautomatisering kan een Logic Apps wanneer een van uw nalevingsevaluaties van regelgeving de status wijzigt.

U kunt bijvoorbeeld een e-Security Center een specifieke gebruiker e-mailen wanneer een nalevingsevaluatie mislukt. U moet eerst de logische app maken (met behulp van [Azure Logic Apps](../logic-apps/logic-apps-overview.md)) en vervolgens de trigger instellen in een nieuwe werkstroomautomatisering, zoals uitgelegd in Reacties op Security Center [triggers automatiseren.](workflow-automation.md)

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Wijzigingen in evaluaties van naleving van regelgeving gebruiken om een werkstroomautomatisering te activeren" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Veelgestelde vragen: dashboard voor Naleving van regelgeving

- [Welke standaarden worden ondersteund in het nalevingsdashboard?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Waarom worden sommige besturingselementen grijs weergegeven?](#why-do-some-controls-appear-grayed-out)
- [Hoe kan ik een ingebouwde standaard, zoals PCI-DSS, ISO 27001 of SOC2 TSP, uit het dashboard verwijderen?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Ik heb de voorgestelde gewijzigd op basis van de aanbeveling, maar deze wordt niet weergegeven in het dashboard](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Welke machtigingen heb ik nodig om toegang te krijgen tot het nalevingsdashboard?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Het dashboard voor naleving van regelgeving wordt niet voor mij geladen](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Hoe kan ik een rapport weergeven over het doorgeven van en mislukken van besturingselementen per standaard in mijn dashboard?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Hoe kan ik een rapport met nalevingsgegevens downloaden in een andere indeling dan PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Hoe kan ik uitzonderingen maken voor sommige beleidsregels in het dashboard voor naleving van regelgeving?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Welke Azure Defender of licenties heb ik nodig om het dashboard voor naleving van regelgeving te gebruiken?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)
- [Hoe kan ik welke benchmark of standaard moet worden gebruikt?](#how-do-i-know-which-benchmark-or-standard-to-use)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Welke standaarden worden ondersteund in het nalevingsdashboard?
Standaard wordt in het dashboard voor naleving van regelgeving de Azure Security-benchmark weergegeven. De Azure Security-benchmark is de door Microsoft opgestelde, azure-specifieke richtlijnen voor beveiliging en best practices voor naleving op basis van algemene nalevingskaders. Meer informatie in de [inleiding tot Azure Security Benchmark.](../security/benchmarks/introduction.md)

Als u de naleving van een andere standaard wilt bijhouden, moet u deze expliciet toevoegen aan uw dashboard.
 
U kunt andere standaarden toevoegen, zoals Azure CIS 1.3.0, NIST SP 800-53, NIST SP 800-171, SWIFT CSP CSCF-v2020, UK Official en UK NHS, HIPAA, Canada Federal PBMM, ISO 27001, SOC2-TSP en PCI-DSS 3.2.1.  

Er worden meer standaarden toegevoegd aan het dashboard en opgenomen in de informatie over De set standaarden [aanpassen in uw dashboard voor naleving van regelgeving.](update-regulatory-compliance-packages.md)

### <a name="why-do-some-controls-appear-grayed-out"></a>Waarom worden sommige besturingselementen grijs weergegeven?
Voor elke nalevingsstandaard in het dashboard is er een lijst met besturingselementen van de standaard. Voor de toepasselijke besturingselementen kunt u de details bekijken van het doorgeven van en mislukken van evaluaties.

Sommige besturingselementen zijn grijs weergegeven. Op deze besturingselementen zijn geen beoordelingen van het Azure Security Center van toepassing. Sommige kunnen betrekking hebben op procedures of processen en kunnen daarom niet worden geverifieerd door Security Center. Voor sommige is nog geen geautomatiseerd beleid of evaluaties geïmplementeerd, maar in de toekomst wel. En sommige besturingselementen zijn mogelijk de verantwoordelijkheid van het platform, zoals uitgelegd in [Gedeelde verantwoordelijkheid in de cloud.](../security/fundamentals/shared-responsibility.md) 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Hoe kan ik een ingebouwde standaard, zoals PCI-DSS, ISO 27001 of SOC2 TSP, uit het dashboard verwijderen? 
Als u het dashboard voor naleving van regelgeving wilt aanpassen en u alleen wilt richten op de standaarden die op u van toepassing zijn, kunt u alle weergegeven regelgevingsstandaarden verwijderen die niet relevant zijn voor uw organisatie. Als u een standaard wilt verwijderen, volgt u de instructies in [Een standaard verwijderen uit uw dashboard.](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Ik heb de voorgestelde gewijzigd op basis van de aanbeveling, maar deze wordt niet weergegeven in het dashboard
Nadat u actie hebt ondernomen om aanbevelingen op te lossen, wacht u 12 uur om de wijzigingen in uw nalevingsgegevens te zien. Evaluaties worden ongeveer elke 12 uur uitgevoerd, dus u ziet pas het effect op uw nalevingsgegevens nadat de evaluaties zijn uitgevoerd.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Welke machtigingen heb ik nodig om toegang te krijgen tot het nalevingsdashboard?
Als u nalevingsgegevens wilt weergeven, moet u ten minste **lezertoegang** hebben tot de nalevingsgegevens van het beleid; Beveiligingslezer alleen is dus niet voldoende. Als u een globale lezer van het abonnement bent, is dat ook voldoende.

De minimale set rollen voor toegang tot het dashboard en het beheren van standaarden is **Inzender** voor resourcebeleid **en Beveiligingsbeheerder.**


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Het dashboard voor naleving van regelgeving wordt niet voor mij geladen
Als u het dashboard voor naleving van regelgeving wilt gebruiken, Azure Security Center de Azure Defender ingeschakeld op abonnementsniveau. Als het dashboard niet correct wordt geladen, probeert u de volgende stappen:

1. De cache van uw browser wissen.
1. Probeer een andere browser.
1. Open het dashboard vanaf een andere netwerklocatie.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Hoe kan ik een rapport weergeven over het doorgeven van en mislukken van besturingselementen per standaard in mijn dashboard?
Op het hoofddashboard ziet u een rapport van het doorgeven en mislukken van besturingselementen voor (1) de 'top 4' laagste nalevingsstandaarden in het dashboard. Selecteer (2) **Alle *x*** weergeven (waarbij x het aantal standaarden is dat u volgt) om alle statussen van het doorgeven/mislukken van besturingselementen weer te geven. Een contextvlak toont de nalevingsstatus voor elk van uw bij te houden standaarden.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Samenvattingssectie van het dashboard voor naleving van regelgeving":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Hoe kan ik een rapport met nalevingsgegevens downloaden in een andere indeling dan PDF?
Wanneer u Rapport **downloaden selecteert,** selecteert u de standaard en de indeling (PDF of CSV). Het resulterende rapport geeft de huidige set abonnementen weer die u hebt geselecteerd in het filter van de portal.

- Het PDF-rapport bevat een samenvattingsstatus voor de standaard die u hebt geselecteerd
- Het CSV-rapport bevat gedetailleerde resultaten per resource, omdat het betrekking heeft op beleidsregels die zijn gekoppeld aan elk besturingselement

Er is momenteel geen ondersteuning voor het downloaden van een rapport voor een aangepast beleid; alleen voor de opgegeven regelgevingsstandaarden.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Hoe kan ik uitzonderingen maken voor sommige beleidsregels in het dashboard voor naleving van regelgeving?
Voor beleidsregels die zijn ingebouwd in Security Center en zijn opgenomen in de beveiligde score, kunt u rechtstreeks in de portal uitzonderingen maken voor een of meer resources, zoals uitgelegd in Resources en aanbevelingen van uw beveiligde [score](exempt-resource.md)vrijstellen.

Voor andere beleidsregels kunt u een uitzondering rechtstreeks in het beleid zelf maken door de instructies in Azure Policy [uitzonderingsstructuur te volgen.](../governance/policy/concepts/exemption-structure.md)


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Welke Azure Defender of licenties heb ik nodig om het dashboard voor naleving van regelgeving te gebruiken?
Als u een van de Azure Defender-pakketten hebt ingeschakeld voor een van uw Azure-resourcetypen, hebt u toegang tot het Dashboard voor naleving van regelgeving, met alle gegevens ervan, in Security Center.


### <a name="how-do-i-know-which-benchmark-or-standard-to-use"></a>Hoe kan ik welke benchmark of standaard moet worden gebruikt?
[Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) (ASB) is de canonieke reeks beveiligingsaanbevelingen en best practices die door Microsoft zijn gedefinieerd, afgestemd op algemene frameworks voor nalevingscontrole, waaronder [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/) en [NIST SP 800-53.](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) ASB is een zeer uitgebreide benchmark en is ontworpen om de meest recente beveiligingsmogelijkheden van een breed scala aan Azure-services aan te bevelen. We raden ASB aan klanten die hun beveiligingsstatus willen maximaliseren en de mogelijkheid hebben om hun nalevingsstatus af te stemmen op industriestandaarden.

De [CIS-benchmark](https://www.cisecurity.org/benchmark/azure/) is geschreven door een onafhankelijke entiteit , Center for Internet Security (CIS) en bevat aanbevelingen voor een subset van Azure-kernservices. We werken samen met CIS om ervoor te zorgen dat hun aanbevelingen up-to-date zijn met de nieuwste verbeteringen in Azure, maar ze raken soms achterop en raken verouderd. Niettemin willen sommige klanten deze doelstelling, evaluatie van derden van CIS, gebruiken als hun eerste en primaire beveiligingsbasislijn.

Omdat we de Azure Security Benchmark hebben uitgebracht, hebben veel klanten ervoor gekozen om deze te migreren als vervanging voor CIS-benchmarks.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het dashboard naleving van regelgeving van het Azure Security Center kunt gebruiken om:

> [!div class="checklist"]
> * Bekijk en controleer uw nalevingsstatus met betrekking tot de standaarden en voorschriften die voor u belangrijk zijn.
> * De nalevingsstatus te verbeteren door relevante beoordelingen op te lossen en te zien hoe de nalevingsscore verbetert.

Het dashboard voor naleving van regelgeving kan het nalevingsproces aanzienlijk vereenvoudigen en de benodigde tijd voor het verzamelen van nalevings bewijs voor uw Azure-, hybride en multi-cloudomgeving aanzienlijk verminderen.

Zie deze gerelateerde pagina's voor meer informatie:

- [De set standaarden aanpassen in uw](update-regulatory-compliance-packages.md) dashboard voor naleving van regelgeving: leer hoe u kunt selecteren welke standaarden worden weergegeven in uw dashboard voor naleving van regelgeving. 
- [Beveiligingsaanbevelingen beheren in Azure Security Center:](security-center-recommendations.md) meer informatie over het gebruik van aanbevelingen in Security Center om uw Azure-resources te beveiligen.