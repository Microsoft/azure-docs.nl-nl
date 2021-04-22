---
title: Taken voor Azure Stream Analytics bewaken en beheren met PowerShell
description: In dit artikel wordt beschreven hoe u Azure PowerShell en cmdlets gebruikt voor het bewaken en beheren van Azure Stream Analytics taken.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 15b2e5ef5873ea48c6c3f2c790619392f622fb66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870135"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Taken bewaken en Stream Analytics beheren met Azure PowerShell-cmdlets
Meer informatie over het bewaken en beheren Stream Analytics resources met Azure PowerShell cmdlets en PowerShell-scripts die eenvoudige Stream Analytics uitvoeren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Vereisten voor het uitvoeren Azure PowerShell cmdlets voor Stream Analytics
* Maak een Azure-resourcegroep in uw abonnement. Hier volgt een voorbeeld van Azure PowerShell script. Zie Azure PowerShell installeren en configureren [voor meer Azure PowerShell;](/powershell/azure/)  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Stream Analytics taken die programmatisch zijn gemaakt, is bewaking niet standaard ingeschakeld.  U kunt bewaking handmatig inschakelen in Azure Portal door te navigeren naar de pagina Controleren van de taak en op de knop Inschakelen te klikken. U kunt dit ook programmatisch doen door de stappen te volgen die zich bevinden in [Azure Stream Analytics - Stream Analytics-taken programmatisch controleren.](stream-analytics-monitor-jobs.md)
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell cmdlets voor Stream Analytics
De volgende Azure PowerShell-cmdlets kunnen worden gebruikt voor het bewaken en beheren Azure Stream Analytics taken. Houd er rekening mee Azure PowerShell verschillende versies heeft. 
**In de vermelde voorbeelden is de eerste opdracht voor Azure PowerShell 0.9.8, de tweede opdracht is voor Azure PowerShell 1.0.** De Azure PowerShell 1.0-opdrachten hebben altijd 'Az' in de opdracht.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Geeft een lijst Stream Analytics taken die zijn gedefinieerd in het Azure-abonnement of de opgegeven resourcegroep, of haalt taakinformatie op over een specifieke taak binnen een resourcegroep.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Deze PowerShell-opdracht retourneert informatie over alle Stream Analytics taken in het Azure-abonnement.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Deze PowerShell-opdracht retourneert informatie over alle Stream Analytics taken in de resourcegroep StreamAnalytics-Default-Central-US.

**Voorbeeld 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Deze PowerShell-opdracht retourneert informatie over de Stream Analytics taak StreamingJob in de resourcegroep StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Een lijst met alle invoer die zijn gedefinieerd in een opgegeven Stream Analytics taak of krijgt informatie over een specifieke invoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Deze PowerShell-opdracht retourneert informatie over alle invoer die is gedefinieerd in de taak StreamingJob.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Deze PowerShell-opdracht retourneert informatie over de invoer met de naam EntryStream die is gedefinieerd in de taak StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Een lijst met alle uitvoer die zijn gedefinieerd in een opgegeven Stream Analytics taak of krijgt informatie over een specifieke uitvoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Deze PowerShell-opdracht retourneert informatie over de uitvoer die is gedefinieerd in de taak StreamingJob.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Deze PowerShell-opdracht retourneert informatie over de uitvoer met de naam Output gedefinieerd in de taak StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Hiermee haalt u informatie op over het quotum voor streaming-eenheden in een opgegeven regio.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Deze PowerShell-opdracht retourneert informatie over het quotum en het gebruik van streaming-eenheden in de regio VS - centraal.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Haalt informatie op over een specifieke transformatie die is gedefinieerd in Stream Analytics taak.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Deze PowerShell-opdracht retourneert informatie over de transformatie met de naam StreamingJob in de taak StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Hiermee maakt u een nieuwe invoer binnen Stream Analytics taak of werkt u een bestaande opgegeven invoer bij.

De naam van de invoer kan worden opgegeven in het JSON-bestand of op de opdrachtregel. Als beide zijn opgegeven, moet de naam op de opdrachtregel hetzelfde zijn als de naam in het bestand.

Als u een invoer opgeeft die al bestaat en niet de parameter -Force opgeeft, vraagt de cmdlet of de bestaande invoer moet worden vervangen.

Als u de parameter -Force opgeeft en een bestaande invoernaam opgeeft, wordt de invoer zonder bevestiging vervangen.

Raadpleeg de sectie Invoer maken [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] van de Stream Analytics Management REST API Reference Library voor gedetailleerde informatie over de structuur en inhoud van het [JSON-bestand.][stream.analytics.rest.api.reference]

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Met deze PowerShell-opdracht maakt u een nieuwe invoer van het Input.jsop. Als een bestaande invoer met de naam die is opgegeven in het invoerdefinitiebestand al is gedefinieerd, vraagt de cmdlet of deze moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Met deze PowerShell-opdracht maakt u een nieuwe invoer in de taak met de naam EntryStream. Als er al een bestaande invoer met deze naam is gedefinieerd, vraagt de cmdlet of deze wel of niet moet worden vervangen.

**Voorbeeld 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Met deze PowerShell-opdracht wordt de definitie van de bestaande invoerbron EntryStream vervangen door de definitie van het bestand.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Hiermee maakt u Stream Analytics nieuwe taak in Microsoft Azure of werkt u de definitie van een bestaande opgegeven taak bij.

De naam van de taak kan worden opgegeven in het JSON-bestand of op de opdrachtregel. Als beide zijn opgegeven, moet de naam op de opdrachtregel hetzelfde zijn als de naam in het bestand.

Als u een taaknaam opgeeft die al bestaat en niet de parameter -Force opgeeft, vraagt de cmdlet of de bestaande taak moet worden vervangen.

Als u de parameter -Force opgeeft en een bestaande taaknaam opgeeft, wordt de taakdefinitie zonder bevestiging vervangen.

Raadpleeg de sectie Create [Stream Analytics Job][msdn-rest-api-create-stream-analytics-job] van de Stream Analytics Management REST API Reference Library voor gedetailleerde informatie over de structuur en inhoud van het [JSON-bestand.][stream.analytics.rest.api.reference]

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Met deze PowerShell-opdracht maakt u een nieuwe taak op basis van de definitie in JobDefinition.jsaan. Als een bestaande taak met de naam die is opgegeven in het taakdefinitiebestand al is gedefinieerd, vraagt de cmdlet of deze moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Met deze PowerShell-opdracht wordt de taakdefinitie voor StreamingJob vervangen.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Hiermee maakt u een nieuwe uitvoer binnen Stream Analytics taak of werkt u een bestaande uitvoer bij.  

De naam van de uitvoer kan worden opgegeven in het JSON-bestand of op de opdrachtregel. Als beide zijn opgegeven, moet de naam op de opdrachtregel hetzelfde zijn als de naam in het bestand.

Als u een uitvoer opgeeft die al bestaat en niet de parameter -Force opgeeft, vraagt de cmdlet of de bestaande uitvoer moet worden vervangen.

Als u de parameter -Force opgeeft en een bestaande uitvoernaam opgeeft, wordt de uitvoer zonder bevestiging vervangen.

Raadpleeg de sectie Uitvoer maken [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] van de Stream Analytics Management REST API Reference Library voor gedetailleerde informatie over de JSON-bestandsstructuur [en -inhoud.][stream.analytics.rest.api.reference]

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Met deze PowerShell-opdracht maakt u een nieuwe uitvoer met de naam 'output' in de taak StreamingJob. Als er al een bestaande uitvoer met deze naam is gedefinieerd, vraagt de cmdlet of deze wel of niet moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Met deze PowerShell-opdracht wordt de definitie voor 'uitvoer' in de taak StreamingJob vervangen.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Hiermee maakt u een nieuwe transformatie binnen een Stream Analytics taak of werkt u de bestaande transformatie bij.

De naam van de transformatie kan worden opgegeven in het JSON-bestand of op de opdrachtregel. Als beide zijn opgegeven, moet de naam op de opdrachtregel hetzelfde zijn als de naam in het bestand.

Als u een transformatie opgeeft die al bestaat en niet de parameter -Force opgeeft, vraagt de cmdlet of de bestaande transformatie moet worden vervangen.

Als u de parameter -Force opgeeft en een bestaande transformatienaam opgeeft, wordt de transformatie zonder bevestiging vervangen.

Raadpleeg de sectie Create [Transformation (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] van de Stream Analytics Management REST API Reference Library voor gedetailleerde informatie over de structuur en inhoud van het [JSON-bestand.][stream.analytics.rest.api.reference]

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Met deze PowerShell-opdracht maakt u een nieuwe transformatie met de naam StreamingJobTransform in de taak StreamingJob. Als er al een bestaande transformatie met deze naam is gedefinieerd, vraagt de cmdlet of deze wel of niet moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Met deze PowerShell-opdracht wordt de definitie van StreamingJobTransform in de taak StreamingJob vervangen.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Hiermee verwijdert u asynchroon een specifieke invoer van een Stream Analytics taak in Microsoft Azure.  
Als u de parameter -Force opgeeft, wordt de invoer verwijderd zonder bevestiging.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Met deze PowerShell-opdracht wordt de invoer-EventStream in de taak StreamingJob verwijderd.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Hiermee wordt asynchroon een specifieke Stream Analytics verwijderd in Microsoft Azure.  
Als u de parameter -Force opgeeft, wordt de taak zonder bevestiging verwijderd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Met deze PowerShell-opdracht wordt de taak StreamingJob verwijderd.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Hiermee wordt asynchroon een specifieke uitvoer van een Stream Analytics verwijderd in Microsoft Azure.  
Als u de parameter -Force opgeeft, wordt de uitvoer zonder bevestiging verwijderd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Met deze PowerShell-opdracht wordt de uitvoeruitvoer in de taak StreamingJob verwijderd.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Asynchroon implementeert en start een Stream Analytics-taak in Microsoft Azure.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Met deze PowerShell-opdracht wordt de taak StreamingJob gestart met een aangepaste begintijd voor uitvoer ingesteld op 12 december 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Een taak wordt asynchroon Stream Analytics uitgevoerd in Microsoft Azure en de toewijzing van resources die werden gebruikt, wordt verwijderd. De taakdefinitie en metagegevens blijven beschikbaar in uw abonnement via zowel de Azure Portal- als beheer-API's, zodat de taak kan worden bewerkt en opnieuw kan worden gestart. Er worden geen kosten in rekening gebracht voor een taak met de status Gestopt.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Met deze PowerShell-opdracht wordt de taak StreamingJob gestopt.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Test de mogelijkheid om Stream Analytics verbinding te maken met een opgegeven invoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Met deze PowerShell-opdracht wordt de verbindingsstatus van de invoer-EntryStream in StreamingJob getest.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Test de mogelijkheid om Stream Analytics verbinding te maken met een opgegeven uitvoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Met deze PowerShell-opdracht wordt de verbindingsstatus van de uitvoer in StreamingJob getest.  

## <a name="get-support"></a>Ondersteuning krijgen
Probeer onze Microsoft [Q&A-vragenpagina](/answers/topics/azure-stream-analytics.html)voor meer Azure Stream Analytics. 

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/