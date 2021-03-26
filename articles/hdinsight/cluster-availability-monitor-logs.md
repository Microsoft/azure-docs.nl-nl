---
title: De beschik baarheid van een cluster bewaken met Azure Monitor-Logboeken in HDInsight
description: Meer informatie over het gebruik van Azure Monitor-logboeken voor het bewaken van de cluster status en beschik baarheid.
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 299a17e23ca3eb2d954bae7335571ae1f645152e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867148"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>De beschik baarheid van een cluster bewaken met Azure Monitor-Logboeken in HDInsight

HDInsight-clusters zijn onder andere integratie van Azure Monitor Logboeken. Dit biedt query's met metrische gegevens en logboeken en configureer bare waarschuwingen. In dit artikel wordt beschreven hoe u Azure Monitor kunt gebruiken om uw cluster te bewaken.

## <a name="azure-monitor-logs-integration"></a>Integratie van Azure Monitor-logboeken

Azure Monitor logboeken maken het mogelijk om gegevens die zijn gegenereerd door meerdere resources, zoals HDInsight-clusters, op één plek te verzamelen en samen te voegen voor een uniforme bewakings ervaring.

Als vereiste hebt u een Log Analytics-werk ruimte nodig om de verzamelde gegevens op te slaan. Als u er nog geen hebt gemaakt, kunt u hier de instructies volgen: [Maak een log Analytics-werk ruimte](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Integratie van HDInsight Azure Monitor-logboeken inschakelen

Selecteer op de pagina HDInsight-cluster resource in de Portal de optie **Azure monitor**. Selecteer vervolgens **inschakelen** en selecteer uw log Analytics-werk ruimte in de vervolg keuzelijst.

:::image type="content" source="media/cluster-availability-monitor-logs/azure-portal-monitoring.png" alt-text="HDInsight Operations Management Suite":::

Standaard installeert de OMS-agent op alle cluster knooppunten, met uitzonde ring van Edge-knoop punten. Omdat er geen OMS-agent is geïnstalleerd op cluster Edge-knoop punten, is er standaard geen telemetrie op Edge-knoop punten aanwezig in Log Analytics.

## <a name="query-metrics-and-logs-tables"></a>Query's uitvoeren op metrische gegevens en logboeken van tabellen

Als de integratie van Azure Monitor logboek is ingeschakeld (dit kan enkele minuten duren), navigeert u naar uw **log Analytics werkruimte** resource en selecteert u **Logboeken**.

:::image type="content" source="media/cluster-availability-monitor-logs/hdinsight-portal-logs.png" alt-text="Log Analytics werkruimte logboeken":::

In Logboeken worden een aantal voorbeeld query's weer geven, zoals:

| Querynaam                      | Beschrijving                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Computers Beschik baarheid vandaag    | Grafiek het aantal computers dat Logboeken verzendt, elk uur                     |
| Heartbeats weer geven                 | Alle Heartbeats van de computer in het afgelopen uur weer geven                           |
| Laatste heartbeat van elke computer | De laatste heartbeat weer geven die door elke computer is verzonden                             |
| Niet-beschik bare computers           | Alle bekende computers weer geven die de afgelopen vijf uur geen heartbeat hebben verzonden |
| Beschikbaarheids tempo               | De beschik baarheid van elke verbonden computer berekenen                |

Voer bijvoorbeeld de voorbeeld query **beschikbaarheids frequentie** uit door **uitvoeren** op die query te selecteren, zoals wordt weer gegeven in de bovenstaande scherm afbeelding. Hiermee wordt de beschikbaarheids percentage van elk knoop punt in het cluster weer gegeven als een percentage. Als u meerdere HDInsight-clusters hebt ingeschakeld voor het verzenden van metrische gegevens naar dezelfde Log Analytics-werk ruimte, ziet u de beschik baarheid van alle knoop punten (exclusief Edge-knoop punten) in die clusters weer gegeven.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-availability-rate.png" alt-text="Voorbeeld query beschik baarheids frequentie van Log Analytics werkruimte logboeken":::

> [!NOTE]  
> Het beschikbaarheids tarief wordt gemeten over een periode van 24 uur, zodat uw cluster ten minste 24 uur moet worden uitgevoerd voordat er nauw keurige beschikbaarheids tarieven worden weer geven.

U kunt deze tabel vastmaken aan een gedeeld dash board door in de rechter bovenhoek op **vastmaken** te klikken. Als u geen Beschrijf bare gedeelde Dash boards hebt, kunt u deze hier maken: [Dash boards maken en delen in de Azure Portal](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Azure Monitor waarschuwingen

U kunt ook Azure Monitor waarschuwingen instellen die worden geactiveerd wanneer de waarde van een metriek of de resultaten van een query voldoen aan bepaalde voor waarden. U kunt bijvoorbeeld een waarschuwing maken om een e-mail bericht te verzenden wanneer een of meer knoop punten niet langer dan 5 uur een heartbeat hebben verzonden (dat wil zeggen dat deze niet beschikbaar is).

Voer vanuit **Logboeken** de voorbeeld query niet- **beschik bare computers** uit door **uitvoeren** op die query te selecteren, zoals hieronder wordt weer gegeven.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-unavailable-computers.png" alt-text="Voor beeld van Log Analytics werkruimte logboeken ' niet-beschik bare computers '":::

Als alle knoop punten beschikbaar zijn, moet deze query voor Taan nul resultaten retour neren. Klik op **nieuwe waarschuwings regel** om te beginnen met het configureren van de waarschuwing voor deze query.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png" alt-text="Nieuwe waarschuwings regel Log Analytics werk ruimte":::

Er zijn drie onderdelen voor een waarschuwing: de *resource* waarvoor u de regel (de log Analytics-werk ruimte in dit geval) wilt maken, de *voor waarde* voor het activeren van de waarschuwing en de *actie groepen* die bepalen wat er gebeurt wanneer de waarschuwing wordt geactiveerd.
Klik op de titel van de **voor waarde**, zoals hieronder wordt weer gegeven, om het configureren van de signaal logica te volt ooien.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-condition-title.png" alt-text="Status voor regel voor maken van portal waarschuwing":::

Hiermee wordt de **logica** voor het configureren van signalen geopend.

Stel de sectie **waarschuwings logica** als volgt in:

*Op basis van: aantal resultaten, voor waarde: groter dan, drempel waarde: 0.*

Omdat deze query alleen niet-beschik bare knoop punten retourneert als resultaat als het aantal resultaten ooit groter is dan 0, moet de waarschuwing worden geactiveerd.

Stel in het gedeelte **geëvalueerd op basis van** de **periode** en **frequentie** in op basis van hoe vaak u wilt controleren op niet-beschik bare knoop punten.

Voor het doel van deze waarschuwing moet u de frequentie van de **periode instellen.** Meer informatie over periode, frequentie en andere waarschuwings parameters vindt u [hier](../azure-monitor/alerts/alerts-unified-log.md#alert-logic-definition).

Selecteer **gereed** wanneer u klaar bent met het configureren van de signaal logica.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-configure-signal-logic.png" alt-text="Waarschuwings regel configureert signaal logica":::

Als u nog geen actie groep hebt, klikt u op **nieuwe maken** onder de sectie **actie groepen** .

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-new-action-group.png" alt-text="Waarschuwings regel maakt nieuwe actie groep":::

Hiermee wordt de **actie groep toevoegen** geopend. Kies een **naam voor de actie groep**, de **korte naam**, het **abonnement** en de **resource groep.** Kies in de sectie **acties** een **actie naam** en selecteer **e-mail/SMS/push/Voice** als **actie type.**

> [!NOTE]
> Er zijn verschillende andere acties die een waarschuwing kan activeren naast een E-mail/SMS/push/Voice, zoals een Azure-functie, LogicApp, webhook, ITSM en Automation-Runbook. [Meer informatie.](../azure-monitor/alerts/action-groups.md#action-specific-information)

Hiermee wordt **e-mail/SMS/push/Voice** geopend. Kies een **naam** voor de ontvanger, **Schakel** het vak **e-mail** in en typ een e-mail adres waarnaar de waarschuwing moet worden verzonden. Selecteer **OK** in  **e-mail/SMS/push/Voice** en klik vervolgens in **actie groep toevoegen** om het configureren van uw actie groep te volt ooien.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-add-action-group.png" alt-text="Waarschuwings regel maakt actie groep toevoegen":::

Nadat deze Blades zijn gesloten, ziet u dat uw actie groep wordt weer gegeven onder de sectie **actie groepen** . Vul tot slot de **sectie waarschuwings Details** in door een naam en **Beschrijving** voor de **waarschuwings regel** te typen en een **Ernst** te kiezen. Klik op **waarschuwings regel maken** om te volt ooien.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png" alt-text="Portal maakt een eind datum voor de waarschuwings regel":::

> [!TIP]
> De mogelijkheid om **Ernst** op te geven is een krachtig hulp programma dat kan worden gebruikt bij het maken van meerdere waarschuwingen. U kunt bijvoorbeeld één waarschuwing maken om een waarschuwing te genereren (Ernst 1) als één hoofd knooppunt wordt weer gegeven en een andere waarschuwing die kritiek (Ernst 0) veroorzaakt in het onwaarschijnlijke geval dat beide hoofd knooppunten omlaag gaan.

Wanneer aan de voor waarde voor deze waarschuwing wordt voldaan, wordt de waarschuwing weer gegeven en ontvangt u een e-mail bericht met de details van de waarschuwing, zoals:

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alert-email.png" alt-text="Voor beeld van een e-mail bericht Azure Monitor":::

U kunt ook alle waarschuwingen weer geven die zijn geactiveerd, gegroepeerd op Ernst, door naar **waarschuwingen** in uw log Analytics- **werk ruimte** te gaan.

:::image type="content" source="media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png" alt-text="Waarschuwingen voor Log Analytics werk ruimte":::

Als u een Ernst groep selecteert (dat wil zeggen **Ernst 1,** zoals hierboven is gemarkeerd), worden de records weer gegeven voor alle waarschuwingen van de ernst die als hieronder zijn geactiveerd:

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png" alt-text="Eén waarschuwing Log Analytics werk ruimte Ernst":::

## <a name="next-steps"></a>Volgende stappen

* [Clusterbeschikbaarheid - Apache Ambari](./hdinsight-cluster-availability.md)
* [Azure Monitor-Logboeken gebruiken](hdinsight-hadoop-oms-log-analytics-tutorial.md)
