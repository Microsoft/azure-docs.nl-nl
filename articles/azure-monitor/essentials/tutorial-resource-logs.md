---
title: Resourcelogboeken verzamelen van een Azure-resource en analyseren met Azure Monitor
description: Zelfstudie voor het configureren van diagnostische instellingen voor het verzamelen van resourcelogboeken van een Azure-resource in een werkruimte van Log Analytics waar ze kunnen worden geanalyseerd met een logboekquery.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 88138cc1366dcef68aeccf652af05704e72c8e15
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627199"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Zelfstudie: Resourcelogboeken van een Azure-resource verzamelen en analyseren

Resourcelogboeken bieden inzicht in de gedetailleerde werking van een Azure-resource en zijn handig om de status en beschikbaarheid van een resource te controleren. Azure-resources genereren automatisch resourcelogboeken, maar u moet instellen waar ze moeten worden verzameld. In deze zelfstudie gaat u een diagnostische instelling definiëren voor het verzamelen van resourcelogboeken voor een resource in uw Azure-abonnement, waarna u de logboeken analyseert met behulp van een logboekquery.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Log Analytics-werkruimte maken in Azure Monitor
> * Een diagnostische instelling definiëren voor het verzamelen van resourcelogboeken 
> * Een eenvoudige logboekquery maken voor het analyseren van logboeken


## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u een Azure-resource nodig die kan worden gecontroleerd. U kunt hiervoor elke resource in uw Azure-abonnement gebruiken die ondersteuning biedt voor diagnostische instellingen. Dit kunt u vaststellen door in de Azure-portal naar het menu van de resource te gaan en te kijken of er een optie **Diagnostische instellingen** staat in de sectie **Controle** van het menu.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij de Azure-portal op [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-workspace"></a>Een werkruimte maken
Een Log Analytics-werkruimte in Azure Monitor verzamelt en indexeert logboekgegevens uit verschillende bronnen en maakt geavanceerde analyses mogelijk met behulp van een krachtige querytaal. De Log Analytics-werkruimte moet bestaan voordat u een diagnostische instelling maakt om gegevens naar de werkruimte te verzenden. U kunt een bestaande werkruimte gebruiken uit uw Azure-abonnement of er een maken met de volgende procedure. 

> [!NOTE]
> Hoewel u kunt werken met gegevens in werkruimten van Log Analytics via het menu **Azure Monitor**, maakt en beheert u werkruimten via het menu **Log Analytics-werkruimten**.

1. Ga naar **Alle services** en selecteer **Log Analytics-werkruimten**.
2. Klik bovenaan het scherm op **Toevoegen** en geef de volgende gegevens op voor de werkruimte:
   - **Log Analytics-werkruimte**: Een naam voor de nieuwe werkruimte. Deze naam moet uniek zijn binnen alle Azure Monitor-abonnementen.
   - **Abonnement**: Selecteer het abonnement voor het opslaan van de werkruimte. Dit hoeft niet hetzelfde abonnement te zijn als dat van de resource die u wilt controleren.
   - **Resourcegroep**: Selecteer een bestaande resourcegroep of klik op **Nieuw** om er een te maken. Dit hoeft niet dezelfde resourcegroep te zijn als die van de resource die u wilt controleren.
   - **Locatie**: Selecteer een Azure-regio of maak er een. Dit hoeft niet dezelfde locatie te zijn als die van de resource die u wilt controleren.
   - **Prijscategorie**: Selecteer *Betalen per gebruik* als de prijscategorie. U kunt deze prijscategorie later wijzigen. Klik op de koppeling **Log Analytics-prijzen** voor meer informatie over de verschillende prijscategorieën.

    ![Nieuwe werkruimte](media/tutorial-resource-logs/new-workspace.png)

3. Klik op **OK** om de werkruimte te maken.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
[Diagnostische instellingen](../essentials/diagnostic-settings.md) bepalen waarnaar resourcelogboeken moeten worden verzonden voor een bepaalde resource. Een diagnostische instelling kan meerdere [bestemmingen](../essentials/diagnostic-settings.md#destinations) hebben, maar in deze zelfstudie gebruiken we alleen een Log Analytics-werkruimte.

1. Selecteer in de sectie **Controle** van uw resourcemenu de optie **Diagnostische instellingen**.
2. U ziet een bericht dat er geen diagnostische instellingen zijn gedefinieerd. Klik op **Diagnostische instelling toevoegen**.

    ![Diagnostische instellingen](media/tutorial-resource-logs/diagnostic-settings.png)

3. Elke diagnostische instelling bestaat uit drie basisonderdelen:
 
   - **Naam**: Dit onderdeel is niet zo belangrijk en u kunt hier een beschrijvende naam opgeven.
   - **Bestemmingen**: Een of meer bestemmingen voor de logboeken. Alle Azure-services delen dezelfde set van drie mogelijke bestemmingen. Elke diagnostische instelling kan een of meer bestemmingen hebben, maar niet meer dan één bestemming van een bepaald type. 
   - **Categorieën**: Categorieën van logboeken voor verzending naar elk van de bestemmingen. De set categorieën verschilt per Azure-service.

4. Selecteer **Verzenden naar Log Analytics-werkruimte** en selecteer vervolgens de werkruimte die u hebt gemaakt.
5. Selecteer de categorieën die u wilt verzamelen. Raadpleeg de documentatie voor elke service voor een definitie van de beschikbare categorieën.

    ![Diagnostische instelling](media/tutorial-resource-logs/diagnostic-setting.png)

6. Klik op **Opslaan** om de diagnostische instellingen op te slaan.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Logboeken ophalen met een logboekquery
U kunt gegevens ophalen uit een Log Analytics-werkruimte met behulp van een logboekquery die is geschreven in Kusto Query Language (KQL). Inzichten en oplossingen in Azure Monitor bieden logboekquery's voor het ophalen van gegevens voor een bepaalde service, maar u kunt ook rechtstreeks met logboekquery's werken en met hun resultaten in de Azure-portal met Log Analytics. 

1. Selecteer in de sectie **Controle** van uw resourcemenu de optie **Logboeken**.
2. Log Analytics wordt geopend met een leeg queryvenster met het bereik ingesteld op uw resource. Alle query's retourneren alleen records uit die resource.

    > [!NOTE]
    > Als u Logboeken hebt geopend via het menu Azure Monitor, is het bereik ingesteld op de Log Analytics-werkruimte. In dit geval bevatten alle query's alle records in de werkruimte.
   
    ![Schermopname met de sectie Logboeken voor een logische app waarin een nieuwe query met de naam van de logische app is gemarkeerd.](media/tutorial-resource-logs/logs.png)

4. De service in het voorbeeld schrijft resourcelogboeken weg naar de tabel **AzureDiagnostics**, maar andere services kunnen gegevens wegschrijven naar andere tabellen. Zie [Ondersteunde services, schema's en categorieën voor Azure-resourcelogboeken](../essentials/resource-logs-schema.md) voor de tabellen die worden gebruikt door de verschillende Azure-services.

    > [!NOTE]
    > Er zijn diverse services die resourcelogboeken wegschrijven naar de tabel AzureDiagnostics. Als u Log Analytics start vanuit het menu Azure Monitor, moet u een instructie `where` met de kolom `ResourceProvider` toevoegen om de specifieke service aan te geven. Wanneer u Log Analytics start via het menu van een resource, wordt het bereik ingesteld op alleen records uit deze resource. U hoeft deze kolom dan niet toe te voegen. Raadpleeg de documentatie van de service voor voorbeeldquery's.


5. Typ een query en klik op **Uitvoeren** om de resultaten te bekijken. 
6. Zie [Aan de slag met logboekquery's in Azure Monitor](../log-query/get-started-queries.md) voor een zelfstudie over het schrijven van logboekquery's.

    ![Logboekquery](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Volgende stappen
U weet nu hoe u resourcelogboeken kunt verzamelen in een Log Analytics-werkruimte. De volgende stap is het volgen van een zelfstudie voor het schrijven van logboekquery's om deze gegevens te analyseren.

> [!div class="nextstepaction"]
> [Aan de slag met logboekquery’s in Azure Monitor](../log-query/get-started-queries.md)
