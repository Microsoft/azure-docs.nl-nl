---
title: OMS-Portal naar Azure verplaatsen | Microsoft Docs
description: De OMS-portal wordt sunsetted met alle functionaliteit die wordt verplaatst naar de Azure Portal. In dit artikel vindt u meer informatie over deze overgang.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: ba81e0bd74390d8b20803f8f9cf6e9f2ae9f0eab
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184515"
---
# <a name="oms-portal-moving-to-azure"></a>OMS-Portal, verplaatsen naar Azure

> [!NOTE]
> Dit artikel is van toepassing op zowel de open bare cloud van Azure als de overheids Cloud, tenzij anders vermeld.

**De OMS-portal voor de open bare Azure-Cloud is officieel buiten gebruik gesteld. De OMS-portal voor de Azure-Cloud voor de Amerikaanse overheid is op 15 mei 2019 buiten gebruik gesteld.** We zijn enthousiast over de Azure Portal en verwachten dat de overgang eenvoudig is. Maar we begrijpen dat wijzigingen moeilijk zijn en kunnen storend zijn. De rest van dit artikel gaat over de belangrijkste scenario's en het schema voor deze overgang.

De Azure Portal is de hub voor alle Azure-Services en biedt een rijke beheer ervaring met mogelijkheden zoals Dash boards voor het vastmaken van resources, het zoeken naar resources en het labelen voor resource beheer. Om de bewakings-en beheer werk stroom te kunnen consolideren en stroom lijnen, beginnen we met het toevoegen van de OMS-Portal mogelijkheden in de Azure Portal. Alle functies van de OMS-portal maken nu deel uit van de Azure Portal. Sommige van de nieuwe functies, zoals Traffic Analytics, zijn alleen beschikbaar in de Azure Portal. Met de Azure Portal en meer kunt u alles doen wat u in de OMS-Portal hebt gedaan. Als u dit nog niet hebt gedaan, moet u de Azure Portal vandaag nog gebruiken.

## <a name="what-is-changing"></a>Wat wordt er gewijzigd? 
De volgende wijzigingen worden aangekondigd met de afschaffing van de OMS-Portal. Elk van deze wijzigingen wordt gedetailleerd beschreven in de volgende secties.

- U kunt alleen nieuwe [werk ruimten](#new-workspaces) maken in de Azure Portal.
- De nieuwe ervaring voor het beheer van waarschuwingen [vervangt de Waarschuwingenbeheer oplossing](#changes-to-alerts).
- [Beheer van gebruikers toegang](#user-access-and-role-migration) wordt nu uitgevoerd in de Azure Portal met behulp van Azure op rollen gebaseerd toegangs beheer.
- De [Application Insights-connector is niet meer vereist](#application-insights-connector-and-solution) omdat dezelfde functionaliteit is ingeschakeld via query's in meerdere werk ruimten.
- De [OMS Mobile-App](#oms-mobile-app) wordt afgeschaft. 
- De [NSG-oplossing wordt vervangen](#azure-network-security-group-analytics) door de uitgebreide functionaliteit die beschikbaar is via Traffic Analytics oplossing.
- Nieuwe verbindingen van System Center Operations Manager naar Log Analytics [bijgewerkte Management Packs](#system-center-operations-manager)vereist.
- Zie [uw OMS-update-implementaties migreren naar Azure](../../automation/migrate-oms-update-deployments.md) voor meer informatie over wijzigingen in [updatebeheer](../../automation/update-management/overview.md).


## <a name="what-should-i-do-now"></a>Wat moet ik nu doen?
Hoewel de meeste functies blijven werken zonder een migratie uit te voeren, moet u de volgende taken uitvoeren:

- U moet [uw gebruikers machtigingen migreren](#user-access-and-role-migration) naar het Azure Portal.
- Zie [uw OMS-update-implementaties migreren naar Azure](../../automation/migrate-oms-update-deployments.md) voor meer informatie over het overstappen van de updatebeheer-oplossing.

Raadpleeg de [Veelgestelde vragen over de overgang van de OMS-Portal naar Azure portal voor log Analytics gebruikers](../overview.md) voor informatie over hoe u kunt overstappen naar de Azure Portal. 

## <a name="user-access-and-role-migration"></a>Gebruikers toegang en rollen migreren
Azure Portal Access Management is rijker en krachtiger dan het toegangs beheer in de OMS-Portal. Zie [uw Azure monitor-werk ruimte voor logboeken ontwerpen](design-logs-deployment.md) voor meer informatie over toegangs beheer in log Analytics.

> [!NOTE]
> In vorige versies van dit artikel is aangegeven dat de machtigingen van de OMS-Portal automatisch worden geconverteerd naar het Azure Portal. Deze automatische conversie wordt niet meer gepland en u moet de conversie zelf uitvoeren.

Mogelijk hebt u al de juiste toegangs rechten in de Azure Portal in welk geval u geen wijzigingen hoeft aan te brengen. Er zijn enkele gevallen waarin u mogelijk niet de juiste toegang hebt, in welk geval de beheerder u machtigingen moet toewijzen.

- U hebt alleen-lezen-gebruikers machtigingen in de OMS-Portal, maar geen machtigingen in de Azure Portal. 
- U hebt Inzender machtigingen in de OMS-Portal, maar alleen lezers toegang in de Azure Portal.
 
In beide gevallen moet uw beheerder hand matig de juiste rol toewijzen uit de volgende tabel. U wordt aangeraden deze rol toe te wijzen aan het niveau van de resource groep of het abonnement.  In beide gevallen worden er binnenkort meer prescriptieve richt lijnen gegeven.

| Machtiging OMS-Portal | Azure-rol |
|:---|:---|
| ReadOnly | Lezer van Log Analytics |
| Inzender | Inzender van Log Analytics |
| Beheerder | Eigenaar | 
 

## <a name="new-workspaces"></a>Nieuwe werk ruimten
U kunt geen nieuwe werk ruimten meer maken met behulp van de OMS-Portal. Volg de richt lijnen in [een log Analytics-werk ruimte maken in de Azure Portal](../learn/quick-create-workspace.md) om een nieuwe werk ruimte in de Azure portal te maken.

## <a name="changes-to-alerts"></a>Wijzigingen in waarschuwingen

### <a name="alert-extension"></a>Uitbrei ding voor waarschuwingen  

Er zijn waarschuwingen [uitgebreid naar de Azure Portal](./alerts-unified-log.md) bestaande waarschuwingen worden weer gegeven in de OMS-Portal, maar u kunt ze alleen beheren in azure Portal. Als u waarschuwingen programmatisch opent met behulp van de Log Analytics waarschuwing REST API of Log Analytics waarschuwing resource sjabloon, moet u actie groepen gebruiken in plaats van acties in uw API-aanroepen, Azure Resource Manager-sjablonen en Power shell-opdrachten.

### <a name="alert-management-solution"></a>Oplossing voor waarschuwings beheer
Als wijziging ten opzichte van een eerdere aankondiging blijft de [oplossing voor waarschuwings beheer](alert-management-solution.md) beschikbaar en wordt deze volledig ondersteund in de Azure Portal. U kunt door gaan met de installatie van de oplossing van Azure Marketplace.

Hoewel de oplossing voor waarschuwings beheer nog steeds beschikbaar blijft, raden we u aan de [Unified Alerting-interface van Azure monitor](alerts-overview.md) te gebruiken om alle waarschuwingen in azure te visualiseren en te beheren. Deze nieuwe ervaring voegt systeem eigen waarschuwingen toe van meerdere bronnen in azure, inclusief logboek waarschuwingen van Log Analytics. Als u gebruikmaakt van de Unified Alerting-interface van Azure Monitor, is de oplossing voor het beheer van waarschuwingen alleen nodig om de integratie van waarschuwingen van System Center Operations Manager in azure mogelijk te maken. In Azure Monitor Unified Alerting interface kunt u distributies van uw waarschuwingen bekijken, profiteren van de geautomatiseerde groepering van gerelateerde waarschuwingen via slimme groepen, en waarschuwingen over meerdere abonnementen weer geven terwijl u uitgebreide filters toepast. Toekomstige verbeteringen in waarschuwings beheer zijn voornamelijk beschikbaar via deze nieuwe ervaring. 

De gegevens die worden verzameld door de oplossing voor waarschuwings beheer (records met een type waarschuwing), blijven in Log Analytics zolang de oplossing voor de werk ruimte is geïnstalleerd. 

## <a name="oms-mobile-app"></a>Mobiele OMS-app
De OMS-app voor mobiele apps wordt samen met de OMS-Portal sunsetted. In plaats van de OMS-app voor toegang tot informatie over uw IT-infra structuur, Dash boards en opgeslagen query's, kunt u rechtstreeks vanuit uw browser toegang krijgen tot de Azure Portal op uw mobiele apparaat. Als u meldingen wilt ontvangen, moet u [Azure-actie groepen](action-groups.md) configureren om meldingen te ontvangen in de vorm van SMS of een telefoon oproep

## <a name="application-insights-connector-and-solution"></a>Application Insights-connector en oplossing
[Application Insights-connector](app-insights-connector.md) biedt een manier om Application Insights gegevens in een log Analytics-werk ruimte te gebruiken. Deze gegevens duplicatie is vereist om inzicht in de infra structuur en toepassings gegevens mogelijk te maken. Met Application Insights uitgebreide ondersteuning voor het bewaren van gegevens in maart, 2019 en de mogelijkheid om [query's](../log-query/cross-workspace-query.md) uit te voeren op meerdere resources, maar u hoeft niet alleen [meer Azure monitor Application Insights bronnen weer te geven](../log-query/unify-app-resource-data.md), hoeft u geen gegevens van uw Application Insights resources te dupliceren en deze naar log Analytics te verzenden. Bovendien verzendt de connector een subset van de eigenschappen van de toepassing naar Log Analytics, terwijl de query's voor meerdere bronnen u uitgebreide flexibiliteit bieden.  

Daarom is Application Insights-connector afgeschaft en uit Azure Marketplace verwijderd, samen met de OMS-Portal op 30 maart 2019. Bestaande verbindingen blijven werken tot en met 30 juni 2019. In de OMS-Portal is het niet mogelijk om bestaande verbindingen te configureren en te verwijderen vanuit de portal. Dit wordt ondersteund met behulp van de REST API die beschikbaar worden gesteld in januari, 2019 en er wordt een melding verzonden naar de [updates van Azure](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Azure Analyse van netwerkbeveiligingsgroep
De [Azure analyse van netwerkbeveiligingsgroep oplossing](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) wordt vervangen door de onlangs geopende [Traffic Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) die inzicht heeft in de activiteit van gebruikers en toepassingen in Cloud netwerken. Traffic Analytics helpt u bij het controleren van de netwerk activiteit van uw organisatie, het beveiligen van toepassingen en gegevens, het optimaliseren van de prestaties van de werk belasting en blijven voldoen. 

Deze oplossing analyseert NSG-stroom logboeken en biedt inzicht in het volgende.

- Verkeer loopt over uw netwerken tussen Azure en Internet, open bare Cloud regio's, VNETs en subnetten.
- Toepassingen en protocollen in uw netwerk, zonder dat er sniffers of speciale stroom verzamelings apparaten nodig zijn.
- Belangrijkste Talkers, intensieve-toepassingen, VM-conversaties in de Cloud, verkeers HOTS pots.
- Bronnen en bestemmingen van verkeer via VNETs, interrelatie tussen belang rijke bedrijfs Services en toepassingen.
- Beveiliging met inbegrip van schadelijk verkeer, poorten worden geopend op internet, toepassingen of Vm's die toegang proberen tot internet.
- Het capaciteits gebruik, waarmee u problemen van het inrichten of het ondergebruik kunt voor komen.

U kunt de diagnostische instellingen blijven gebruiken om NSG-logboeken te verzenden naar Log Analytics zodat uw bestaande opgeslagen Zoek opdrachten, waarschuwingen en dash boards blijven werken. Klanten die de oplossing al hebben geïnstalleerd, kunnen deze blijven gebruiken tot verdere kennisgeving. Vanaf 5 september wordt de oplossing voor de analyse van netwerk beveiligings groepen verwijderd uit de Marketplace en beschikbaar gesteld via de community als een [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)start-sjabloon.

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Als u [uw Operations Manager-beheer groep hebt verbonden met log Analytics](om-agents.md), blijft het werken zonder wijzigingen. Voor nieuwe verbindingen moet u echter de instructies in [micro soft System Center Operations Manager Management Pack volgen om Operations Management Suite te configureren](https://techcommunity.microsoft.com/t5/system-center-blog/bg-p/SystemCenterBlog).

## <a name="next-steps"></a>Volgende stappen
- Zie [Veelgestelde vragen over de overgang van de OMS-Portal naar Azure portal voor log Analytics gebruikers](../overview.md) voor hulp bij het overstappen van de OMS-Portal naar de Azure Portal.