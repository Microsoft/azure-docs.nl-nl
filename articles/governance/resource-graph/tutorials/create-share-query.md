---
title: "Zelfstudie: Query's beheren in Azure-portal"
description: In deze zelfstudie maakt u een Resource Graph-query en deelt u de nieuwe query met anderen in het Azure-portal.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: 7240a843cfa041137b28284e396c8a4b62a81926
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916676"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Zelfstudie: Een Azure Resource Graph-query maken en delen in het Azure-portal

Met Azure Resource Graph Explorer kunt u uw Resource Graph-query's rechtstreeks in de Azure-portal opslaan. Er zijn twee soorten query's: _Persoonlijke_ en _gedeelde_. Een persoonlijke query wordt opgeslagen in de instellingen van uw Azure-portal. Een gedeelde query is een resource voor Azure Resource Manager die kan worden beheerd met Azure RBAC (op rollen gebaseerd toegangsbeheer) en beveiligd met resourcevergrendelingen. Beide typen query's zijn versleuteld als ze inactief zijn.

Door query's op te slaan in de Azure-portal bespaart u de tijd die u anders mogelijk kwijt bent met het zoeken naar uw favoriete of veelgebruikte query's. Wanneer u query's deelt, helpt u uw team doelstellingen van consistentie en efficiëntie te halen door middel van herhaling.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> - Een persoonlijke query maken en verwijderen
> - Een gedeelde query maken
> - Gedeelde query's detecteren
> - Een gedeelde query verwijderen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-and-delete-a-private-query"></a>Een persoonlijke query maken en verwijderen

Persoonlijke query's zijn alleen toegankelijk en zichtbaar voor het account dat ze heeft gemaakt. Aangezien ze worden opgeslagen in de instellingen van de Azure-portal van een account, kunnen ze alleen worden gemaakt, gebruikt en verwijderd vanuit de Azure-portal. Een persoonlijke query is geen resource voor Resource Manager. Om een nieuwe persoonlijke query te maken, volgt u deze stappen:

1. Selecteer in het portalmenu **Alle services** of gebruik het Azure-zoekvak bovenaan alle pagina's. Zoek naar en selecteer **Resource Graph Explorer**.

1. Voer in het tabblad **Query 1** op de pagina Azure Resource Graph Explorer de volgende query in:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Selecteer **Query uitvoeren** om de zoekresultaten in het onderste deelvenster weer te geven.

   Zie [Voorbeelden, aantal virtuele machines per type besturingssysteem](../samples/starter.md#count-os) voor meer informatie over deze query.

1. Selecteer **Opslaan** of **Opslaan als**, voer **Aantal vm's per besturingssysteem** in als naam en laat het type staan op **Persoonlijke query**. Selecteer dan **Opslaan** aan de onderkant van het deelvenster **Query opslaan**. De titel van het tabblad verandert van **Query 1** naar **Aantal vm's per besturingssysteem**.

1. Verlaat Azure Resource Graph Explorer in de Azure-portal en keer weer terug. U ziet dat de opgeslagen query niet langer wordt weergegeven en het tabblad **Query 1** is terug.

1. Selecteer **Een query openen**. Zorg ervoor dat het type **Persoonlijke query** is. Nu verschijnt de naam **Aantal vm's per besturingssysteem** in de lijst met **Querynamen**. Wanneer u de titelkoppeling van de opgeslagen query selecteert, wordt deze geladen in een nieuw tabblad met de naam van die query.

   > [!NOTE] 
   > Wanneer een opgeslagen query is geopend en het tabblad de naam weergeeft en u selecteert de knop **Opslaan**, wordt de query bijgewerkt met alle aangebrachte wijzigingen. Om van deze geopende query een nieuwe opgeslagen query te maken, selecteert u **Opslaan als** en gaat u door alsof u een nieuwe query opslaat.

1. Om de opgeslagen query te verwijderen, selecteert u opnieuw **Een query openen** en controleert u dat het veld **Type** is ingesteld op **Persoonlijke query**. In de rij met de query `Count VMs by OS` selecteert u **Verwijderen** (pictogram van prullenbak). In het bevestigingsdialoogvenster selecteert u **Ja** om de query te verwijderen.
   Vervolgens sluit u het deelvenster **Een query openen**.

## <a name="create-a-shared-query"></a>Een gedeelde query maken

In tegenstelling tot een persoonlijke query is een gedeelde query een resource voor Resource Manager. Dit betekent dat de query wordt opgeslagen in een resourcegroep, kan worden beheerd met Azure RBAC en zelfs kan worden beveiligd met resourcevergrendelingen. Omdat het een resource is, kan iedereen met de juiste machtigingen de query zien en gebruiken. Om een nieuwe gedeelde query te maken, volgt u deze stappen:

1. Selecteer in het portalmenu **Alle services** of gebruik het Azure-zoekvak bovenaan alle pagina's om **Resource Graph Explorer** te zoeken en te selecteren.

1. Voer in het tabblad **Query 1** op de pagina Azure Resource Graph Explorer de volgende query in:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Selecteer **Query uitvoeren** om de zoekresultaten in het onderste deelvenster weer te geven.

   Zie [Voorbeelden, aantal virtuele machines per type besturingssysteem](../samples/starter.md#count-os) voor meer informatie over deze query.

1. Selecteer **Opslaan** of **Opslaan als**.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="Sla de nieuwe query op met de knop Opslaan" border="false":::

1. Voer in het deelvenster **Query opslaan** de naam **Aantal vm's per besturingssysteem** in.

1. Wijzig het type in **Gedeelde query**, stel de beschrijving in op **Aantal virtuele machines per besturingssysteem** en stel **Abonnement** in om aan te geven waar de queryresource wordt gemaakt.

1. Zorg ervoor dat het selectievakje **Publiceren naar resourcegroep resource-graph-query's** blijft geselecteerd en dat de **Resourcegroeplokatie** is ingesteld op **VS - west-centraal**.

1. Selecteer **Opslaan** aan de onderkant van het deelvenster **Query opslaan**. De titel van het tabblad verandert van **Query 1** naar **Aantal vm's per besturingssysteem**. De eerste keer dat de resourcegroep **resource-graph-query's** wordt gebruikt, duurt het opslaan langer dan verwacht, omdat de resourcegroep wordt gemaakt.
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="De nieuwe query opslaan als een gedeelde query" border="false":::

   > [!NOTE] 
   > U kunt het selectievakje **Publiceren naar resourcegroep resource-graph-query's** uitschakelen als u de naam van een bestaande resourcegroep wilt opgeven om de query in op te slaan. Als u de standaardnaam van de resourcegroep gebruikt voor query's, zijn de gedeelde query's eenvoudiger te vinden. Ook is zo het doel van die resourcegroep duidelijker. U kunt er echter om beveiligingsredenen op basis van bestaande machtigingen voor kiezen een bestaande resourcegroep te selecteren.

1. Verlaat Azure Resource Graph Explorer in de Azure-portal en keer weer terug. U ziet dat de opgeslagen query niet langer wordt weergegeven en het tabblad **Query 1** is terug.

1. Selecteer **Een query openen**. Controleer of het type is ingesteld op **Gedeelde query** en dat de combinatie van **Abonnement** en **Resourcegroep** overeenkomen met waar u de query hebt opgeslagen. Nu verschijnt het item **Aantal vm's per besturingssysteem** in de lijst met **Querynamen**. Selecteer de titelkoppeling van de opgeslagen query om deze te laden in een nieuw tabblad met de naam van die query. Omdat het een gedeelde query is, wordt er in het tabblad naast de naam een pictogram weergegeven om aan te geven dat de query is gedeeld.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="De gedeelde query met pictogram weergeven" border="false":::

   > [!NOTE] 
   > Wanneer een opgeslagen query is geopend en het tabblad de naam weergeeft, wordt met de knop **Opslaan** de query bijgewerkt met alle aangebrachte wijzigingen. Om een nieuwe opgeslagen query te maken, selecteert u **Opslaan als** en gaat u door alsof u een nieuwe query opslaat.

## <a name="discover-shared-queries"></a>Gedeelde query's detecteren

Omdat een gedeelde query een resource is voor Resource Manager zijn er verschillende manieren om een gedeelde query te vinden:

- Vanuit Resource Graph Explorer. Selecteer **Een query openen** en stel het type in op **Gedeelde query**.
- Vanuit de portalpagina Resource Graph-query's.
- Vanuit de resourcegroep waarin de gedeelde query is opgeslagen.
- Door een query uit te voeren op Resource Graph.

### <a name="view-resource-graph-queries"></a>Resource Graph-query's weergeven

In de Azure-portal geeft de pagina Resource Graph-query's de gedeelde query's weer waartoe het aangemelde account toegang heeft. Op deze pagina kunt u filteren op naam, abonnement, resourcegroep en andere eigenschappen van de Resource Graph-query. U kunt ook Resource Graph-query's labelen, exporteren en verwijderen via deze interface.

Als u een query selecteert, wordt de pagina Resource Graph-query's geopend. Deze pagina biedt, net als andere resources voor Resource Manager, een interactief overzicht samen met het Activiteitenlogboek, toegangsbeheer en labels. U kunt ook rechtstreeks vanaf deze pagina een resourcevergrendeling toepassen.

Ga naar de pagina Resource Graph-query's vanuit het portalmenu door **Alle services** te selecteren of door het Azure-zoekvak bovenaan alle pagina's te gebruiken. Zoek naar en selecteer **Resource Graph Explorer**.

### <a name="list-resource-groups-resources"></a>Resources voor resourcegroepen weergeven

De Resource Graph-query wordt weergegeven naast andere resources die onderdeel zijn van een resourcegroep.
Door de Resource Graph-query te selecteren, wordt de pagina van die query geopend. Het beletselteken en snelmenu (geactiveerd door met de rechtermuisknop te klikken) werken op dezelfde manier als op de pagina Resource Graph-query.

### <a name="query-resource-graph"></a>Resource Graph doorzoeken

U kunt Resource Graph-query's vinden door een query uit te voeren op Resource Graph. De volgende Resource Graph-query beperkt de resultaten tot het type `Microsoft.ResourceGraph/queries` en gebruikt vervolgens `project` om alleen de naam, datum van de laatste wijziging en de query zelf weer te geven:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>Een gedeelde query uitvoeren

Een gedeelde Resource Graph-query kan worden uitgevoerd met de syntaxis `{{shared-query-uri}}` (preview). Zie [Syntaxis voor gedeelde query filteren](../concepts/query-language.md#shared-query-syntax) voor meer informatie.

## <a name="delete-a-shared-query"></a>Een gedeelde query verwijderen

Als een gedeelde query niet langer nodig is, kunt u deze verwijderen. Door een gedeelde query te verwijderen, verwijdert u de bijbehorende resource voor Resource Manager. Alle dashboards waarop de resultatengrafiek was vastgemaakt, geven nu een foutbericht weer. Wanneer dat foutbericht wordt weergegeven, gebruikt u de knop **Verwijderen van dashboard** om uw dashboard op te schonen.

U kunt een gedeelde query verwijderen via de volgende interfaces:
- De pagina Resource Graph-query's
- De pagina Resource Graph-query
- De pagina **Een query openen** in Resource Graph Explorer
- De pagina Resourcegroepen

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze zelfstudie hebt voltooid, kunt u de persoonlijke en gedeelde query's die u hebt gemaakt, verwijderen als u ze niet langer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u persoonlijke en gedeelde query's gemaakt. Ga verder naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](../concepts/query-language.md)