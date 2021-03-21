---
title: Visualisaties voor het analyseren van toepassings wijzigingen-Azure Monitor
description: Meer informatie over het gebruik van visualisaties in de analyse van toepassings wijzigingen in Azure Monitor.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655844"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Visualisaties voor het analyseren van toepassings wijzigingen (preview-versie)

## <a name="standalone-ui"></a>Zelfstandige gebruikers interface

In Azure Monitor is er een zelfstandig deel venster voor het wijzigen van de analyse om alle wijzigingen met inzichten in toepassings afhankelijkheden en resources weer te geven.

Zoek naar veranderingen analyse in de zoek balk op Azure Portal om de ervaring te starten.

![Scherm opname van het zoeken van wijzigingen in Azure Portal](./media/change-analysis/search-change-analysis.png)

Alle resources onder een geselecteerd abonnement worden weer gegeven met wijzigingen van de afgelopen 24 uur. Alle wijzigingen worden weer gegeven met oude waarde en nieuwe waarde om inzicht te bieden in één oogopslag.

![Scherm opname van de Blade voor het wijzigen van de analyse in Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Klik op wijzigen om het volledige Resource Manager-fragment en andere eigenschappen weer te geven.

![Scherm afbeelding van wijzigings gegevens](./media/change-analysis/change-details.png)

Gebruik de knop feedback verzenden of het e-mail adres voor elke feedback changeanalysisteam@microsoft.com .

![Scherm afbeelding van de knop feedback op het tabblad wijzigings analyse](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Ondersteuning voor meerdere abonnementen

De gebruikers interface ondersteunt het selecteren van meerdere abonnementen om resource wijzigingen weer te geven. Het abonnements filter gebruiken:

![Scherm opname van het abonnements filter dat ondersteuning biedt voor het selecteren van meerdere abonnementen](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analyse van toepassings wijzigingen in het hulp programma problemen vaststellen en oplossen

Analyse van toepassings wijzigingen is een zelfstandige detectie in de web-app diagnose en hulpprogram ma's voor probleem oplossing. Het wordt ook geaggregeerd in **toepassings crashes** en **Web app down detectors**. Wanneer u het hulp programma problemen vaststellen en oplossen opgeeft, wordt de resource provider **micro soft. ChangeAnalysis** automatisch geregistreerd. Volg deze instructies voor het inschakelen van het bijhouden van wijzigingen in de gast voor web-apps.

1. Selecteer **Beschik baarheid en prestaties**.

    ![Scherm afbeelding van de opties voor het oplossen van problemen met Beschik baarheid en prestaties](./media/change-analysis/availability-and-performance.png)

2. Selecteer **toepassings wijzigingen**. De functie is ook beschikbaar in **toepassings crashes**.

   ![Scherm opname van de knop ' toepassings crashes '](./media/change-analysis/application-changes.png)

3. De koppeling leidt naar de gebruikers interface voor het wijzigen van de toepassings wijzigingen in de web-app. Als het bijhouden van de web-app in gast wijzigingen niet is ingeschakeld, volgt u de banner om wijzigingen in bestands-en app-instellingen op te halen.

   ![Scherm opname van de opties ' toepassings crashes '](./media/change-analysis/enable-changeanalysis.png)

4. Schakel de **analyse van wijzigingen** in en selecteer **Opslaan**. Het hulp programma geeft alle web-apps weer onder een App Service-abonnement. U kunt de schakel optie plan niveau gebruiken om wijzigings analyse in te scha kelen voor alle web-apps onder een abonnement.

    ![Scherm opname van de gebruikers interface voor het inschakelen van een analyse](./media/change-analysis/change-analysis-on.png)

5. Wijzigings gegevens zijn ook beschikbaar in de Select- **Web-app down** en de detecties van **toepassingen vastlopen** . U ziet een grafiek met een overzicht van het type wijzigingen in de loop van de tijd, samen met details over deze wijzigingen. Standaard worden wijzigingen in de afgelopen 24 uur weer gegeven om onmiddellijke problemen op te lossen.

     ![Scherm opname van de weer gave diff wijzigen](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>Hulp programma problemen vaststellen en oplossen
Wijzigings analyse is beschikbaar als een Insight-kaart in het hulp programma diagnose en probleem oplossing. Als een resource problemen ondervindt en er wijzigingen zijn gedetecteerd in de afgelopen 72 uur, wordt in de Insights-kaart het aantal wijzigingen weer gegeven. Als u op de koppeling wijzigings details weer geven klikt, wordt de gefilterde weer gave van de zelfstandige gebruikers interface van de analyse gewijzigd.

![Scherm opname van het weer geven van wijzigings inzicht in het hulp programma problemen vaststellen en oplossen.](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>Problemen met virtuele machines vaststellen en oplossen

Ga naar het hulp programma problemen vaststellen en oplossen voor een virtuele machine.  Ga naar **Hulpprogram ma's voor probleem oplossing**, blader naar beneden op de pagina en selecteer **recente wijzigingen analyseren** om wijzigingen op de virtuele machine weer te geven.

![Scherm opname van de VM problemen vaststellen en oplossen](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analyse wijzigen in hulpprogram ma's voor probleem oplossing](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Wijzigings geschiedenis van activiteiten logboek

De functie [wijzigings geschiedenis weer geven](../essentials/activity-log.md#view-change-history) in het activiteiten logboek roept de back-end van de analyse service van de toepassings wijziging aan om wijzigingen aan te brengen die aan een bewerking **Wijzigings geschiedenis** die wordt gebruikt om de [Azure-resource grafiek](../../governance/resource-graph/overview.md) rechtstreeks aan te roepen, maar de back-end heeft gewisseld om de analyse van toepassings wijzigingen aan te roepen, zodat wijzigingen die zijn geretourneerd, wijzigingen aanbrengen in het resource niveau van [Azure resource Graph](../../governance/resource-graph/overview.md), resource-eigenschappen van [Azure Resource Manager](../../azure-resource-manager/management/overview.md)en in-Guest wijzigingen van PaaS Services, zoals app Services Web-app. Om ervoor te zorgen dat de service voor het wijzigen van de toepassings wijziging kan scannen op wijzigingen in de abonnementen van gebruikers, moet een resource provider worden geregistreerd. De eerste keer dat u het tabblad **wijzigings overzicht** opgeeft, start het hulp programma automatisch om de resource provider **micro soft. ChangeAnalysis** te registreren. Na de registratie zijn wijzigingen van de **Azure-resource grafiek** onmiddellijk beschikbaar en de afgelopen veer tien dagen. Wijzigingen van andere bronnen zijn na ongeveer 4 uur beschikbaar nadat het abonnement op de onboarding is uitgevoerd.

![Integratie van wijzigings geschiedenis van activiteiten logboek](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>Integratie van VM Insights

Gebruikers die [VM Insights](../vm/vminsights-overview.md) hebben ingeschakeld, kunnen weer geven wat er is gewijzigd in de virtuele machines die mogelijk een piek in een metrische grafiek, zoals CPU of geheugen, hebben veroorzaakt. Wijzigings gegevens worden geïntegreerd in de navigatie balk van de VM Insights-zijde. De gebruiker kan zien of er wijzigingen zijn aangebracht in de VM en selecteren **wijzigingen onderzoeken** om wijzigings details weer te geven in de zelfstandige gebruikers interface voor het wijzigen van de toepassing.

[![Integratie van VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met wijzigings analyse](change-analysis-troubleshoot.md)
