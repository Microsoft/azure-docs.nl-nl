---
title: Dashboards van Azure Log Analytics-gegevens maken en delen | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Log Analytics-dashboards al uw opgeslagen logboekquery's kunt visualiseren, zodat u door één lens naar uw omgeving kunt kijken.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 05/28/2020
ms.custom: mvc
ms.openlocfilehash: 880d173c6eef9d267e8860ebad805e071706de42
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186147"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Dashboards van Log Analytics-gegevens maken en delen

Met Log Analytics-dashboards kunt u al uw opgeslagen logboekquery's visualiseren, zodat u operationele IT-gegevens in de organisatie kunt zoeken, met elkaar in verband kunt brengen en kunt delen.  Deze zelfstudie bevat informatie over het maken van een logboekquery die wordt gebruikt ter ondersteuning van een gedeeld dashboard waartoe uw IT-team toegang heeft.  In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een gedeeld dashboard maken in de Azure-portal
> * Een query voor het prestatielogboek visualiseren 
> * Een logboekquery toevoegen aan een gedeeld dashboard 
> * Een tegel in een gedeeld dashboard aanpassen

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine [hebben verbonden met de Log Analytics-werkruimte](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Een gedeeld dashboard maken
Selecteer **Dashboard** om uw standaard [dashboard](../../azure-portal/azure-portal-dashboards.md) te openen. Uw dashboard ziet er anders uit dan het onderstaande voorbeeld.

![Dashboard in Azure-portal](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Hier verzamelt u de belangrijkste operationele IT-gegevens van al uw Azure-resources, waaronder telemetrie van Azure Log Analytics.  Voordat u een logboekquery gaat visualiseren, gaat u een dashboard maken en delen.  We kunnen ons vervolgens richten op het voorbeeld van de query van het prestatielogboek, die als een lijndiagram wordt weergegeven, en deze aan het dashboard toevoegen.  

> [!NOTE]
> De volgende grafiektypen worden ondersteund in Azure-dashboards met behulp van logboekquery's:
> - vlakdiagram
> - kolomdiagram
> - cirkeldiagram (wordt in het dashboard weergegeven als ringdiagram)
> - spreidingsdiagram
> - tijddiagram

Om een dashboard te maken, selecteert u de knop **Nieuw dashboard** naast de naam van het huidige dashboard.

![Nieuw dashboard maken in Azure-portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Met deze actie wordt een nieuw, leeg, persoonlijk dashboard gemaakt en in de aanpassingsmodus gezet. In deze modus kunt u het dashboard een naam geven en tegels toevoegen of opnieuw rangschikken. Bewerk de naam van het dashboard en geef *Voorbeelddashboard* op voor deze zelfstudie. Selecteer vervolgens **Aanpassen voltooid**.<br><br> ![Aangepast Azure-dashboard opslaan](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Wanneer u een dashboard maakt, is het standaard persoonlijk. Dat betekent dat u de enige bent die het kan zien. Als u het zichtbaar wilt maken voor anderen, gebruik dan de knop **Delen** die naast de andere dashboardopdrachten wordt weergegeven.

![Een nieuw dashboard in Azure-portal delen](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

U wordt gevraagd een abonnement en resourcegroep te kiezen waarnaar uw dashboard zal worden gepubliceerd. Voor het gemak helpt de publicatie-ervaring van de portal u naar een patroon waar u dashboards kunt plaatsen in een resourcegroep die **dashboards** wordt genoemd.  Controleer het geselecteerde abonnement en klik op **Publiceren**.  Toegang tot de informatie die wordt weergegeven in het dashboard wordt geregeld met [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Een logboekquery visualiseren
[Log Analytics](../log-query/log-analytics-tutorial.md) is een speciale portal die wordt gebruikt voor het werken met logboekquery's en de bijbehorende resultaten. Voorbeelden van functies zijn de mogelijkheid om een query van meerdere regels te bewerken, code selectief uit te voeren, contextafhankelijke Intellisense en slimme analyse. In deze zelfstudie gebruikt u Log Analytics om een grafische prestatieweergave te maken, slaat u deze op voor gebruik in een toekomstige query en maakt u de weergave vast aan het gedeelde dashboard dat u eerder hebt gemaakt.

Open Log Analytics door **Logboeken** te selecteren in het menu van Azure Monitor. U ziet een nieuwe lege query.

![Startpagina](media/tutorial-logs-dashboards/homepage.png)

Voer de volgende query in om records over processorverbruik te retourneren voor zowel Windows- als Linux-computers, gegroepeerd op Computer en TimeGenerated, en weergegeven in een visuele grafiek. Klik op **Uitvoeren** om de query uit te voeren en de resulterende grafiek te bekijken.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Sla de query op door boven aan de pagina de knop **Opslaan** te selecteren.

![Query opslaan](media/tutorial-logs-dashboards/save-query.png)

Geef in het dialoogvenster **Query opslaan** een naam op, bijvoorbeeld *Azure-VM's - processorgebruik* en een categorie zoals *Dashboards*. Klik ten slotte op **Opslaan**.  Op deze manier kunt u een bibliotheek met algemene query's maken die u kunt gebruiken en aanpassen.  Tot slot maakt u de query vast aan het gedeelde dashboard dat u eerder hebt gemaakt. Daarvoor selecteert u de knop **Vastmaken aan dashboard** in de rechterbovenhoek van de pagina en vervolgens de naam van het dashboard.

Nu u een query hebt die is vastgemaakt aan het dashboard, ziet u dat deze een algemene naam heeft met een opmerking eronder.

![Voorbeeld van Azure-dashboard](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 U gaat de naam wijzigen in iets met meer betekenis voor gebruikers die de grafiek willen bekijken.  Klik op de knop Bewerken om de titel en subtitel voor de tegel aan te passen en klik vervolgens op **Bijwerken**.  Er wordt een banner weergegeven waarin u wordt gevraagd of u de wijzigingen wilt publiceren of negeren.  Klik op **Een kopie opslaan**.  

![Voltooide configuratie van voorbeelddashboard](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een dashboard maakt in de Azure-portal en hoe u daar een logboekquery aan toevoegt.  Ga verder met de volgende zelfstudie voor meer informatie over de verschillende reacties die u op basis van de resultaten van logboekquery's kunt implementeren.  

> [!div class="nextstepaction"]
> [Reageren op gebeurtenissen met Log Analytics-waarschuwingen](tutorial-response.md)