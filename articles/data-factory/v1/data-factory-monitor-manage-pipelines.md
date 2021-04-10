---
title: Pijp lijnen bewaken en beheren met behulp van de Azure Portal en Power shell
description: Meer informatie over het gebruik van de Azure Portal en Azure PowerShell voor het bewaken en beheren van de Azure-gegevens fabrieken en-pijp lijnen die u hebt gemaakt.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 0744a7d915d6bee868b160abc29964d58947dd28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779627"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Azure Data Factory pijp lijnen bewaken en beheren met behulp van de Azure Portal en Power shell
> [!div class="op_single_selector"]
> * [Azure Portal/Azure PowerShell gebruiken](data-factory-monitor-manage-pipelines.md)
> * [De app voor bewaking en beheer gebruiken](data-factory-monitor-manage-app.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Data Factory pijp lijnen bewaken en beheren in](../monitor-visually.md).

In dit artikel wordt beschreven hoe u uw pijp lijnen kunt bewaken, beheren en fouten kunt opsporen met behulp van Azure Portal en Power shell.

> [!IMPORTANT]
> De bewakings & beheer toepassing biedt een betere ondersteuning voor het bewaken en beheren van uw gegevens pijplijnen en het oplossen van problemen. Zie [Data Factory pijp lijnen bewaken en beheren met behulp van de app voor bewaking en beheer](data-factory-monitor-manage-app.md)voor meer informatie over het gebruik van de toepassing. 

> [!IMPORTANT]
> Azure Data Factory versie 1 maakt nu gebruik van de nieuwe [Azure monitor-infra structuur voor waarschuwingen](../../azure-monitor/alerts/alerts-metric.md). De oude infra structuur voor waarschuwingen is afgeschaft. Als gevolg hiervan werken de bestaande waarschuwingen die zijn geconfigureerd voor gegevens fabrieken van versie 1 niet meer. Uw bestaande waarschuwingen voor v1-gegevens fabrieken worden niet automatisch gemigreerd. U moet deze waarschuwingen opnieuw maken op de nieuwe infra structuur voor waarschuwingen. Meld u aan bij de Azure Portal en selecteer **monitor** om nieuwe waarschuwingen te maken over metrische gegevens (zoals mislukte uitvoeringen of geslaagde uitvoeringen) voor uw data-fabrieken van versie 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Pijp lijnen en activiteiten status begrijpen
Met behulp van de Azure Portal kunt u het volgende doen:

* Uw data factory als diagram weer geven.
* Activiteiten in een pijp lijn weer geven.
* Invoer-en uitvoer gegevens sets weer geven.

In deze sectie wordt ook beschreven hoe een segment overgang van een status naar een andere status.   

### <a name="navigate-to-your-data-factory"></a>Ga naar uw data factory
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **gegevens fabrieken** in het menu aan de linkerkant. Als u dit niet ziet, klikt u op **meer services >** en klikt u vervolgens op **gegevens fabrieken** onder de categorie **Intelligence en analyse** .

   ![Door alle >-gegevens fabrieken bladeren](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Selecteer op de Blade **gegevens fabrieken** de Data Factory waarin u bent geïnteresseerd.

    ![Data factory selecteren](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   U ziet de start pagina voor de data factory.

   ![Blade Data factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Diagram weergave van uw data factory
De **diagram** weergave van een Data Factory biedt één glas venster om de Data Factory en de bijbehorende activa te controleren en te beheren. Als u de **diagram** weergave van uw Data Factory wilt zien, klikt u op het **diagram** op de start pagina van de Data Factory.

![Diagramweergave](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

U kunt inzoomen, uitzoomen, passend maken, inzoomen op 100%, de indeling van het diagram vergren delen en pijp lijnen en gegevens sets automatisch positioneren. U kunt ook de gegevens afkomst informatie bekijken (dat wil zeggen upstream-en downstream-items van geselecteerde items weer geven).

### <a name="activities-inside-a-pipeline"></a>Activiteiten in een pijp lijn
1. Klik met de rechter muisknop op de pijp lijn en klik vervolgens op **pijp lijn openen** om alle activiteiten in de pijp lijn te bekijken, samen met de invoer-en uitvoer gegevens sets voor de activiteiten. Deze functie is handig als uw pijp lijn meer dan één activiteit bevat en u inzicht wilt krijgen in de operationele afkomst van één pijp lijn.

    ![Menu Pijplijn openen](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. In het volgende voor beeld ziet u een Kopieer activiteit in de pijp lijn met een invoer en een uitvoer. 

    ![Activiteiten in een pijp lijn](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. U kunt teruggaan naar de start pagina van de data factory door te klikken op de koppeling **Data Factory** in het breadcrumb in de linkerbovenhoek.

    ![Ga terug naar data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>De status van elke activiteit in een pijp lijn weer geven
U kunt de huidige status van een activiteit weer geven door de status weer te geven van de gegevens sets die door de activiteit worden geproduceerd.

Door te dubbel klikken op de **OutputBlobTable** in het **diagram**, kunt u alle segmenten zien die worden geproduceerd door verschillende activiteiten, in een pijp lijn. U kunt zien dat de Kopieer activiteit met succes is uitgevoerd voor de laatste acht uur en dat de segmenten zijn gemaakt met de status **gereed** .  

![Status van de pijp lijn](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

De segmenten van de gegevensset in de data factory kunnen een van de volgende statussen hebben:

<table>
<tr>
    <th align="left">Staat</th><th align="left">Substatus</th><th align="left">Description</th>
</tr>
<tr>
    <td rowspan="8">Wachten</td><td>ScheduleTime</td><td>De tijd voor het uitvoeren van het segment is niet beschikbaar.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>De upstream-afhankelijkheden zijn niet gereed.</td>
</tr>
<tr>
<td>ComputeResources</td><td>De reken resources zijn niet beschikbaar.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alle activiteiten exemplaren worden bezet en andere segmenten worden uitgevoerd.</td>
</tr>
<tr>
<td>ActivityResume</td><td>De activiteit wordt gepauzeerd en kan de segmenten pas worden uitgevoerd als de activiteit wordt hervat.</td>
</tr>
<tr>
<td>Opnieuw proberen</td><td>De uitvoering van de activiteit wordt opnieuw uitgevoerd.</td>
</tr>
<tr>
<td>Validatie</td><td>De validatie is nog niet gestart.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Er wordt gewacht tot de validatie is uitgevoerd.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Valideren</td><td>De validatie wordt uitgevoerd.</td>
</tr>
<td>-</td>
<td>Het segment wordt verwerkt.</td>
</tr>
<tr>
<td rowspan="4">Mislukt</td><td>Verlopen</td><td>De uitvoering van de activiteit duurde langer dan is toegestaan door de activiteit.</td>
</tr>
<tr>
<td>Geannuleerd</td><td>Het segment is geannuleerd door de gebruiker.</td>
</tr>
<tr>
<td>Validatie</td><td>De validatie is mislukt.</td>
</tr>
<tr>
<td>-</td><td>Het segment kan niet worden gegenereerd en/of gevalideerd.</td>
</tr>
<td>Gereed</td><td>-</td><td>Het segment is gereed voor gebruik.</td>
</tr>
<tr>
<td>Overgeslagen</td><td>Geen</td><td>Het segment wordt niet verwerkt.</td>
</tr>
<tr>
<td>Geen</td><td>-</td><td>Er wordt een segment gebruikt dat bestaat uit een andere status, maar dit is opnieuw ingesteld.</td>
</tr>
</table>



U kunt de details van een segment weer geven door te klikken op een segment item op de Blade **recent bijgewerkte segmenten** .

![Details van segment](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Als het segment meerdere keren is uitgevoerd, ziet u meerdere rijen in de lijst **uitvoeringen van activiteit** . U kunt details over een uitvoering van een activiteit weer geven door te klikken op de vermelding uitvoeren in de lijst uitvoeringen van **activiteit** . De lijst bevat alle logboek bestanden, samen met een fout bericht als er een wordt weer gegeven. Deze functie is handig als u logboeken wilt weer geven en fouten wilt opsporen zonder uw data factory te verlaten.

![Details uitvoering van activiteit](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Als het segment niet de status **gereed** heeft, kunt u de upstream-segmenten zien die niet gereed zijn en het huidige segment blok keren voor uitvoering in de lijst **upstream-segmenten die niet gereed zijn** . Deze functie is handig als uw segment zich in de **wacht** stand bevindt en u inzicht wilt krijgen in de upstream-afhankelijkheden waar het segment op wacht.

![Constream-segmenten die niet gereed zijn](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Status diagram van gegevensset
Nadat u een data factory hebt geïmplementeerd en de pijp lijnen een geldige actieve periode hebben, overstapt de gegevensset de segmenten van de ene status naar de andere. Op dit moment volgt de segment status het volgende status diagram:

![Status diagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

De status overgangs stroom van de gegevensset in data factory is het volgende: wachtende > in uitvoering/in uitvoering (valideren)-> Ready/failed.

Het segment begint met een **wacht** status. er wordt gewacht tot aan de voor waarden wordt voldaan voordat deze wordt uitgevoerd. Vervolgens wordt de activiteit uitgevoerd en wordt het segment de status **in uitvoering** heeft. De uitvoering van de activiteit kan slagen of mislukken. Het segment is gemarkeerd als **gereed** of **mislukt**, op basis van het resultaat van de uitvoering.

U kunt het segment opnieuw instellen om terug te gaan van de status **gereed** of **mislukt** in de **wacht** stand. U kunt ook de segment status markeren om **over te slaan**, waardoor de activiteit niet kan worden uitgevoerd en niet verwerkt.

## <a name="pause-and-resume-pipelines"></a>Pijp lijnen onderbreken en hervatten
U kunt uw pijp lijnen beheren door gebruik te maken van Azure PowerShell. U kunt pijp lijnen bijvoorbeeld onderbreken en hervatten door Azure PowerShell-cmdlets uit te voeren. 

> [!NOTE] 
> De diagram weergave biedt geen ondersteuning voor het onderbreken en hervatten van pijp lijnen. Als u een gebruikers interface wilt gebruiken, gebruikt u de toepassing bewaking en beheer. Zie [Data Factory pijp lijnen bewaken en beheren met behulp van het artikel controle-en beheer-apps](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van de toepassing. 

U kunt pijp lijnen onderbreken/uitstellen met behulp van de Power shell **-cmdlet Suspend-AzDataFactoryPipeline** . Deze cmdlet is handig als u uw pijp lijnen niet wilt uitvoeren totdat een probleem is opgelost. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Bijvoorbeeld:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Nadat het probleem met de pijp lijn is opgelost, kunt u de onderbroken pijp lijn hervatten door de volgende Power shell-opdracht uit te voeren:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Bijvoorbeeld:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Debug-pijp lijnen
Azure Data Factory biedt uitgebreide mogelijkheden voor het opsporen en oplossen van problemen met pijp lijnen met behulp van de Azure Portal en Azure PowerShell.

> [!NOTE] 
> Het is veel eenvoudiger om troubleshot fouten te maken met behulp van de bewaking & management-app. Zie [Data Factory pijp lijnen bewaken en beheren met behulp van het artikel controle-en beheer-apps](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van de toepassing. 

### <a name="find-errors-in-a-pipeline"></a>Fouten in een pijp lijn zoeken
Als de uitvoering van de activiteit in een pijp lijn mislukt, heeft de gegevensset die wordt geproduceerd door de pijp lijn een fout status vanwege de fout. U kunt fouten opsporen en problemen oplossen in Azure Data Factory met behulp van de volgende methoden.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>De Azure Portal gebruiken om een fout op te lossen
1. Klik op de Blade **tabel** op het probleem segment waarvan de **status** is ingesteld op **mislukt**.

   ![Blade met de tabel met een probleem segment](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Klik op de Blade **gegevens segment** op de uitvoering van de activiteit die is mislukt.

   ![Gegevens segment met een fout](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Op de Blade **Details uitvoering van activiteit** kunt u de bestanden downloaden die zijn gekoppeld aan de HDInsight-verwerking. Klik op **down load** voor status/stderr om het fouten logboek bestand te downloaden dat details bevat over de fout.

   ![Blade Details uitvoering van activiteit met fout](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Power shell gebruiken om een fout op te lossen
1. Start **PowerShell**.
2. Voer de opdracht **Get-AzDataFactorySlice** uit om de segmenten en hun status weer te geven. Als het goed is, ziet u een segment met de status **mislukt**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Bijvoorbeeld:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Vervang **StartDateTime** door de begin tijd van de pijp lijn. 
3. Voer nu de cmdlet **Get-AzDataFactoryRun** uit om meer informatie over de uitvoering van de activiteit voor het segment op te halen.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Bijvoorbeeld:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    De waarde van StartDateTime is de start tijd voor het fout/probleem segment dat u in de vorige stap hebt genoteerd. De datum/tijd moet tussen dubbele aanhalings tekens worden geplaatst.
4. Als het goed is, ziet u de volgende informatie:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. U kunt de cmdlet **Save-AzDataFactoryLog** uitvoeren met de id-waarde die u uit de uitvoer ziet en de logboek bestanden downloaden met behulp van de **-DownloadLogsoption** voor de cmdlet.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Fouten in een pijp lijn opnieuw uitvoeren

> [!IMPORTANT]
> Het is eenvoudiger om fouten op te lossen en mislukte segmenten opnieuw uit te voeren met behulp van de bewaking & management-app. Zie [Data Factory pijp lijnen bewaken en beheren met behulp van de app voor bewaking en beheer](data-factory-monitor-manage-app.md)voor meer informatie over het gebruik van de toepassing. 

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken
Na het oplossen van fouten in een pijp lijn, kunt u storingen opnieuw uitvoeren door te navigeren naar het fout segment en te klikken op de knop **uitvoeren** op de opdracht balk.

![Een mislukt segment opnieuw uitvoeren](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Als de validatie van het segment is mislukt vanwege een beleids fout (bijvoorbeeld als er gegevens niet beschikbaar zijn), kunt u de fout oplossen en opnieuw valideren door te klikken op de knop **Validate** op de opdracht balk.

![Fouten corrigeren en valideren](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken
U kunt fouten opnieuw uitvoeren met behulp van de cmdlet **set-AzDataFactorySliceStatus** . Zie het onderwerp [set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus) voor de syntaxis en andere informatie over de cmdlet.

**Voorbeeld:**

In het volgende voor beeld wordt de status van alle segmenten voor de tabel DAWikiAggregatedData ingesteld op wachtend in azure data factory ' WikiADF '.

De ' UpdateType ' is ingesteld op ' UpstreamInPipeline ', wat betekent dat de statussen van elk segment voor de tabel en alle afhankelijke (upstream) tabellen worden ingesteld op wait. De andere mogelijke waarde voor deze para meter is ' persoonlijk '.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Waarschuwingen maken in de Azure Portal

1.  Meld u aan bij de Azure Portal en selecteer **monitor-> waarschuwingen** om de pagina waarschuwingen te openen.

    ![Open de pagina waarschuwingen.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Selecteer **+ nieuwe waarschuwings regel** om een nieuwe waarschuwing te maken.

    ![Een nieuwe waarschuwing maken](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definieer de **waarschuwings voorwaarde**. (Zorg ervoor dat u **gegevens fabrieken** selecteert in het veld **filteren op resource type** .) U kunt ook waarden voor **dimensies** opgeven.

    ![De waarschuwings voorwaarde definiëren-doel selecteren](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![De waarschuwings voorwaarde definiëren-waarschuwings criteria toevoegen](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![De waarschuwings voorwaarde definiëren-waarschuwings logica toevoegen](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definieer de **Details** van de waarschuwing.

    ![De waarschuwings Details definiëren](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definieer de **actie groep**.

    ![De actie groep definiëren-een nieuwe actie groep maken](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![De instellingen van de actie groep definiëren](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definieer de actie groep-nieuwe actie groep gemaakt](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Een data factory verplaatsen naar een andere resource groep of een ander abonnement
U kunt een data factory verplaatsen naar een andere resource groep of een ander abonnement met behulp van de knop opdracht balk **verplaatsen** op de start pagina van uw Data Factory.

![data factory verplaatsen](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

U kunt ook alle gerelateerde resources (zoals waarschuwingen die zijn gekoppeld aan de data factory) verplaatsen, samen met de data factory.

![Het dialoog venster Resources verplaatsen](./media/data-factory-monitor-manage-pipelines/MoveResources.png)