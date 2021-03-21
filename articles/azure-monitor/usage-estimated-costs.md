---
title: Gebruik en geschatte kosten in Azure Monitor bewaken
description: Overzicht van het proces van het gebruik van de pagina Azure Monitor gebruik en geschatte kosten
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lagayhar
ms.reviewer: Dale.Koetke
ms.openlocfilehash: c4aded73334e38539e1c671831fe812a9525698c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048672"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Gebruik en geschatte kosten in Azure Monitor bewaken

> [!NOTE]
> In dit artikel wordt beschreven hoe u het gebruik en de geschatte kosten kunt weer geven voor meerdere Azure-bewakings functies. Verwante artikelen voor specifieke onderdelen van Azure Monitor zijn onder andere:
> - [Gebruik en kosten beheren met Azure monitor logboeken](logs/manage-cost-storage.md) beschrijft hoe u uw kosten kunt bepalen door de Bewaar periode van uw gegevens te wijzigen en hoe u uw gegevens gebruik kunt analyseren en waarschuwen.
> - [Gebruik en kosten beheren voor Application Insights](app/pricing.md) beschrijft hoe u het gebruik van gegevens in Application Insights kunt analyseren.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor prijs model

Het basis Azure Monitor facturerings model is een op de cloud gebaseerde prijs op basis van verbruik (' betalen naar gebruik '). U betaalt alleen voor wat u gebruikt. Prijs informatie is beschikbaar voor [waarschuwingen, metrische gegevens, meldingen](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) en [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Naast het model voor betalen naar gebruik voor logboek gegevens, heeft Log Analytics capaciteits reserveringen, waarmee u Maxi maal 25% kunt besparen op basis van de betalen naar gebruik-prijs. Met de prijzen voor capaciteits reservering kunt u een reserve ring kopen vanaf 100 GB per dag. Elk gebruik boven het reserverings niveau wordt gefactureerd op basis van het betalen naar gebruik-tarief. Meer [informatie](https://azure.microsoft.com/pricing/details/monitor/) over de prijzen voor capaciteits reservering.

Sommige klanten hebben toegang tot [verouderde log Analytics prijs categorieën](logs/manage-cost-storage.md#legacy-pricing-tiers) en de [verouderde prijs categorie voor Enter prise Application Insights](app/pricing.md#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Meer informatie over uw Azure Monitor kosten

Er zijn twee fasen voor het bekennen van kosten. De eerste is wanneer u Azure Monitor als uw bewakings oplossing overweegt. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Schatting van de kosten voor het beheren van uw omgeving

Als u Azure Monitor-logboeken nog niet gebruikt, kunt u de [Azure monitor prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=monitor) gebruiken om de kosten van het gebruik van Azure monitor te schatten. Begin met het invoeren van ' Azure Monitor ' in het zoekvak en klik op de resulterende Azure Monitor tegel. Schuif omlaag in de pagina naar Azure Monitor en selecteer een van de opties in de vervolg keuzelijst Type:

- Metrische query's en waarschuwingen  
- Log Analytics
- Application Insights

In elk hiervan kunt u met de prijs calculator uw waarschijnlijke kosten schatten op basis van het verwachte gebruik.

Met Log Analytics kunt u bijvoorbeeld het aantal Vm's en de GB aan gegevens invoeren die u van elke virtuele machine verwacht te verzamelen. Doorgaans wordt 1 GB tot 3 GB aan gegevens maand van een typische Azure-VM opgenomen. Als u Azure Monitor logboeken al evalueert, kunt u uw gegevens statistieken uit uw eigen omgeving gebruiken. Hieronder vindt u informatie over het bepalen [van het aantal bewaakte vm's](logs/manage-cost-storage.md#understanding-nodes-sending-data) en het [volume van de gegevens die in uw werk ruimte worden](logs/manage-cost-storage.md#understanding-ingested-data-volume)opgenomen.

Als u de functionaliteit voor het schattings gegevens volume op basis van de toepassings activiteit inschakelt, kunt u op dezelfde manier voor Application Insights een invoer opgeven over uw toepassing (aanvragen per maand en pagina weergaven per maand, voor het geval u telemetrie aan de client zijde verzamelt). vervolgens krijgt u de gemiddelde en negen tigste percentiel hoeveelheid gegevens die door vergelijk bare toepassingen worden verzameld. Deze toepassingen omvatten het bereik van Application Insights configuratie (er zijn bijvoorbeeld standaard steekproef waarden, sommige geen steek proeven enz.), zodat u nog steeds over het besturings element beschikt om de hoeveelheid gegevens die u onder het mediaan niveau hebt opgenomen, te verminderen met behulp van steek proeven. Maar dit is een uitgangs punt om te begrijpen wat andere, vergelijk bare klanten te zien krijgen. Meer [informatie](app/pricing.md#estimating-the-costs-to-manage-your-application) over het schatten van de kosten voor Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Meer informatie over uw gebruik en geschatte kosten

Het is belang rijk dat u het gebruik van Azure Monitor begrijpt en volgt, en er is een uitgebreide set hulpprogram ma's waarmee u dit kunt vergemakkelijken. 

Azure biedt een groot aantal handige functies in de hub [Azure Cost Management en facturering](../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) . Nadat u de hub **Azure Cost Management en facturering** hebt geopend, klikt u op **Cost Management** en selecteert u het [bereik](../cost-management-billing/costs/understand-work-scopes.md) (het aantal te onderzoeken resources). 

Als u de Azure Monitor kosten voor de afgelopen 30 dagen wilt zien, klikt u op de tegel **dagelijkse kosten** , kiest u ' laatste 30 dagen ' onder relatieve datums en voegt u een filter toe waarmee de service namen worden geselecteerd:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Inzicht en analyses

Dit resulteert in een weer gave zoals:

![Scherm afbeelding Azure Cost Management](./media/usage-estimated-costs/010.png)

Hier kunt u inzoomen op basis van dit geaccumuleerde kosten overzicht om de details in de weer gave ' kosten per resource ' te verkrijgen. In de huidige prijs categorieën worden Azure-logboek gegevens in rekening gebracht op basis van dezelfde set meters, ongeacht of deze afkomstig is van Log Analytics of Application Insights. Als u de kosten van uw Log Analytics of Application Insights gebruik wilt scheiden, kunt u een filter toevoegen aan het **resource type**. Als u alle Application Insights kosten wilt zien, filtert u het resource type op ' micro soft. Insights/Components ' en filtert u resource type op ' micro soft. operationalinsights/Workspaces ' voor Log Analytics kosten. 

Meer details van uw gebruik zijn beschikbaar door [uw gebruik te downloaden van de Azure Portal](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). In het gedownloade werk blad kunt u het gebruik per Azure-resource per dag bekijken. In dit Excel-werk blad kunt u het gebruik van uw Application Insights-resources vinden door eerst te filteren op de kolom meter categorie om "Application Insights" en "Log Analytics" weer te geven, en vervolgens een filter toe te voegen aan de kolom exemplaar-ID, dat is "bevat micro soft. Insights/Components".  Het meeste Application Insights gebruik wordt gerapporteerd op meters van de meter categorie Log Analytics, omdat er één logboek back-end is voor alle Azure Monitor-onderdelen.  Alleen Application Insights bronnen in verouderde prijs categorieën en webtests met meerdere stappen worden gerapporteerd met een meter categorie van Application Insights.  Het gebruik wordt weer gegeven in de kolom verbruikte hoeveelheid en de eenheid voor elk item wordt weer gegeven in de kolom eenheid.  Er is meer informatie beschikbaar om u te helpen [inzicht te krijgen in uw Microsoft Azure-factuur](../cost-management-billing/understand/review-individual-bill.md). 

> [!NOTE]
> Het gebruik van **Cost Management** in de **Azure Cost Management + billing** hub is de aanbevolen benadering om de berekenings kosten breed te controleren.  Het **gebruik en de geschatte kosten** voor [log Analytics](logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs)  en [Application Insights](app/pricing.md#understand-your-usage-and-estimate-costs) bieden een dieper inzicht voor elk van deze onderdelen van Azure monitor.

Een andere optie voor het weer geven van uw Azure Monitor gebruik is de pagina **gebruik en geschatte kosten** in de hub bewaken. Hiermee wordt het gebruik van kern bewakings functies, zoals [waarschuwingen, metrische gegevens, meldingen](https://azure.microsoft.com/pricing/details/monitor/), [Azure log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)en [Azure-toepassing Insights](https://azure.microsoft.com/pricing/details/application-insights/), weer gegeven. Voor klanten over de prijzen plannen die beschikbaar zijn vóór 2018 april, bevat dit ook Log Analytics gebruik dat u hebt aangeschaft via de aanbieding inzichten en analyse.

Op deze pagina kunnen gebruikers hun resource gebruik weer geven voor de afgelopen 31 dagen, geaggregeerd per abonnement. `Drill-ins` gebruiks trends weer geven gedurende de periode van 31 dagen. Er zijn veel gegevens nodig om deze schatting samen te stellen. het is dus even geduld als de pagina wordt geladen.

In dit voor beeld ziet u het gebruik van bewaking en een schatting van de resulterende kosten:

![Scherm opname van gebruik en geraamde kosten Portal](./media/usage-estimated-costs/001.png)

Selecteer de koppeling in de kolom maandelijks gebruik om een grafiek te openen waarin de gebruiks trends in de afgelopen periode van 31 dagen worden weer gegeven: 

![Scherm afbeelding van staaf diagram per knoop punt](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Rechten van het abonnement van Operations Management Suite

Klanten die Microsoft Operations Management Suite E1 en E2 hebben gekocht, komen in aanmerking voor gegevens opname rechten per knoop punt voor [log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) en [Application Insights](app/pricing.md). Deze rechten voor Log Analytics werk ruimten of Application Insights resources in een bepaald abonnement ontvangen: 

- Log Analytics-werk ruimten moeten de prijs categorie ' per knoop punt (OMS) ' gebruiken.
- Application Insights resources moeten de prijs categorie onderneming gebruiken.

Afhankelijk van het aantal knoop punten van de suite die uw organisatie heeft aangeschaft, kunnen sommige abonnementen worden verplaatst naar een prijs categorie voor betalen naar gebruik (per GB), maar dit moet zorgvuldig worden overwogen.

> [!WARNING]
> Als uw organisatie actuele Microsoft Operations Management Suite E1 en E2 heeft, is het meestal het beste om uw Log Analytics-werk ruimten in de prijs categorie ' per knoop punt (OMS) ' en uw Application Insights resources in de prijs categorie onderneming te blijven gebruiken. 
>

