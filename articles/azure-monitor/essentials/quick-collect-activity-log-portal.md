---
title: Azure-activiteitenlogboek verzenden naar Log Analytics-werkruimte met behulp van een Azure-portal
description: Gebruik de Azure-portal om een Log Analytics-werkruimte te maken en een diagnostische instelling om het activiteitenlogboek naar Azure Monitor-logboeken te verzenden.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 2e6dc55b54bc3abaea22544d457f98307596c5dd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628703"
---
# <a name="send-azure-activity-log-to-log-analytics-workspace-using-azure-portal"></a>Azure-activiteitenlogboek verzenden naar Log Analytics-werkruimte met behulp van een Azure-portal
Het activiteitenlogboek is een platformlogboek in Azure dat inzicht biedt in gebeurtenissen op abonnementsniveau. Dit geldt ook voor gegevens zoals wanneer een resource wordt gewijzigd of wanneer een virtuele machine wordt gestart. U kunt het activiteitenlogboek weergeven in de Azure-portal, of items ophalen met PowerShell en CLI. Deze Quick Start laat zien hoe u de Azure-portal kunt gebruiken voor het maken van een Log Analytics-werkruimte en een diagnostische instelling om het activiteitenlogboek te verzenden naar Azure Monitor-logboeken waar u het kunt analyseren met behulp van [logboekquery's](../log-query/log-query-overview.md) en om andere functies, zoals [logboekwaarschuwingen](../alerts/alerts-log-query.md) en [werkmappen](../visualize/workbooks-overview.md), in te schakelen. 

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 



## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken
Zoek en selecteer vervolgens **Log Analytics-werkruimten** in de Azure-portal. 

![Schermafbeelding van het Azure-portaal met 'log analytics-werkruimten' in het zoekvak en Log Analytics-werkruimten gemarkeerd onder Services.](../logs/media/quick-create-workspace/azure-portal-01.png)
  
Klik op **Toevoegen** en geef vervolgens waarden op voor de **Resourcegroep**, de **Naam** van de werkruimte en **Locatie**. De naam van de werkruimte moet uniek zijn voor elk Azure-abonnement.

![Werkruimte maken](media/quick-collect-activity-log/create-workspace.png)

Klik op **Beoordelen en maken** om de instellingen te controleren en klik vervolgens **Maken** om de werkruimte te maken. Hiermee selecteert u een standaardprijscategorie van **Betalen per gebruik-** die geen wijzigingen doorvoert totdat u voldoende gegevens begint te verzamelen. Er worden geen kosten in rekening gebracht voor het verzamelen van het Activiteitenlogboek.


## <a name="create-diagnostic-setting"></a>Diagnostische instelling maken
Zoek in de Azure-portal naar **Monitor** en selecteer dit. 

![Schermafbeelding van het Azure-portaal met 'monitor ' in het zoekvak en Monitor gemarkeerd onder Services.](media/quick-collect-activity-log/azure-portal-monitor.png)

Selecteer **Activiteitenlogboek**. U ziet recente gebeurtenissen voor het huidige abonnement. Klik op **Diagnostische instellingen** om de diagnostische instellingen voor het abonnement weer te geven.

![Activiteitenlogboek](media/quick-collect-activity-log/activity-log.png)

Klik op **Diagnostische instelling toevoegen** om een nieuwe instelling te maken. 

![Diagnostische instelling maken](media/quick-collect-activity-log/create-diagnostic-setting.png)

Typ een naam zoals *Activiteitenlogboek verzenden naar werkruimte*. Selecteer elke categorie. Selecteer **Verzenden naar Log Analytics** als enige bestemming en geef vervolgens de werkruimte op die u hebt gemaakt. Klik op **Opslaan** om de diagnostische instelling te maken en sluit de pagina.

![Nieuwe diagnostische instelling](media/quick-collect-activity-log/new-diagnostic-setting.png)

## <a name="generate-log-data"></a>Logboekgegevens genereren
Alleen nieuwe vermeldingen in het activiteitenlogboek worden verzonden naar de Log Analytics-werkruimte, dus voer enkele acties in uw abonnement uit die worden geregistreerd, zoals het starten of stoppen van een virtuele machine of het maken of wijzigen van een andere resource. Mogelijk moet u enkele minuten wachten totdat de diagnostische instelling is gemaakt en de eerste gegevens naar de werkruimte worden geschreven. Na deze vertraging worden alle gebeurtenissen die naar het activiteitenlogboek worden geschreven, binnen een paar seconden verzonden naar de werkruimte.

## <a name="retrieve-data-with-a-log-query"></a>Gegevens ophalen met een logboekquery

Selecteer **Logboeken** in het menu **Azure Monitor**. Sluit de pagina **Voorbeeldquery's**. Als het bereik niet is ingesteld op de werkruimte die u hebt gemaakt, klikt u op **Bereik selecteren** en zoekt u de werkruimte op.

![Log Analytics-bereik](media/quick-collect-activity-log/log-analytics-scope.png)

Typ `AzureActivity` in het queryvenster en klik op **Uitvoeren**. Dit is een eenvoudige query waarmee alle records in de tabel *AzureActivity* worden geretourneerd. Deze tabel bevat alle records die vanuit het activiteitenlogboek zijn verzonden.

![Eenvoudige query](media/quick-collect-activity-log/query-01.png)

Vouw een van de records uit om de gedetailleerde eigenschappen ervan te bekijken.

![Eigenschappen uitvouwen](media/quick-collect-activity-log/expand-properties.png)

Probeer een complexere query, zoals `AzureActivity | summarize count() by CategoryValue`, die een telling van gebeurtenissen per categorie bevat.

![Complexe query](media/quick-collect-activity-log/query-02.png)


## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u het activiteitenlogboek geconfigureerd om te worden verzonden naar een Log Analytics-werkruimte. U kunt nu andere gegevens configureren die moeten worden verzameld in de werkruimte, waar u ze kunt analyseren met [logboekquery's](../log-query/log-query-overview.md) in Azure Monitor, en gebruikmaken van functies zoals [logboekwaarschuwingen](../alerts/alerts-log-query.md) en [werkmappen](../visualize/workbooks-overview.md). Vervolgens moet u de [resourcelogboeken](../essentials/resource-logs.md) uit uw Azure-resources verzamelen, die de gegevens in het Activiteitenlogboek aanvullen en die inzicht geven in de bewerkingen die binnen elke resource zijn uitgevoerd.


> [!div class="nextstepaction"]
> [Resourcelogboeken verzamelen en analyseren met Azure Monitor](../essentials/tutorial-resource-logs.md)
