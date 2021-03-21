---
title: Azure Stream Analytics-taken bewaken en beheren met Power shell
description: In dit artikel wordt beschreven hoe u Azure PowerShell en cmdlets kunt gebruiken om Azure Stream Analytics taken te bewaken en te beheren.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 6b404516c513dea0888974ffb4fa3d8d43db6c44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015178"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Stream Analytics-taken bewaken en beheren met Azure PowerShell-cmdlets
Meer informatie over het bewaken en beheren van Stream Analytics resources met Azure PowerShell-cmdlets en Power shell-scripts waarmee basis Stream Analytics taken worden uitgevoerd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Vereisten voor het uitvoeren van Azure PowerShell-cmdlets voor Stream Analytics
* Maak een Azure-resource groep in uw abonnement. Hier volgt een voor beeld van een Azure PowerShell script. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor Azure PowerShell informatie.  

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

Azure PowerShell 1,0:

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
> Stream Analytics taken die programmatisch zijn gemaakt, hebben geen bewaking standaard ingeschakeld.  U kunt de bewaking hand matig inschakelen in azure portal door te navigeren naar de bewakings pagina van de taak en op de knop inschakelen te klikken, of u kunt dit via een programma uitvoeren door de stappen te volgen die worden beschreven in [Azure Stream Analytics stream Analytics taken programmatisch controleren](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-cmdlets voor Stream Analytics
De volgende Azure PowerShell-cmdlets kunnen worden gebruikt om Azure Stream Analytics-taken te controleren en te beheren. Houd er rekening mee dat Azure PowerShell verschillende versies heeft. 
**In de voor beelden in de eerste opdracht is voor Azure PowerShell 0.9.8 de tweede opdracht Azure PowerShell 1,0.** De Azure PowerShell 1,0-opdrachten hebben altijd ' AZ ' in de opdracht.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Een lijst met alle Stream Analytics taken die in het Azure-abonnement of de opgegeven resource groep zijn gedefinieerd, of er worden taak gegevens over een specifieke taak in een resource groep opgehaald.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob
```

Deze Power shell-opdracht retourneert informatie over alle Stream Analytics taken in het Azure-abonnement.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Deze Power shell-opdracht retourneert informatie over alle Stream Analytics taken in de resource groep StreamAnalytics-default-Central-nl.

**Voorbeeld 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Met deze Power shell-opdracht wordt informatie geretourneerd over de Stream Analytics taak StreamingJob in de resource groep StreamAnalytics-default-Central-nl.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Een lijst met alle invoer waarden die zijn gedefinieerd in een opgegeven Stream Analytics taak, of het ophalen van informatie over een specifieke invoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Deze Power shell-opdracht retourneert informatie over alle invoer waarden die zijn gedefinieerd in de taak StreamingJob.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Deze Power shell-opdracht retourneert informatie over de invoer met de naam EntryStream die is gedefinieerd in de taak StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Een lijst met alle uitvoer die is gedefinieerd in een opgegeven Stream Analytics taak, of het ophalen van informatie over een bepaalde uitvoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Deze Power shell-opdracht retourneert informatie over de uitvoer die is gedefinieerd in de taak StreamingJob.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Deze Power shell-opdracht retourneert informatie over de uitvoer die is gedefinieerd in de taak StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Haalt informatie op over het quotum van streaming-eenheden in een opgegeven regio.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Deze Power shell-opdracht retourneert informatie over het quotum en het gebruik van streaming-eenheden in de regio VS-midden.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Hiermee haalt u informatie op over een specifieke trans formatie die is gedefinieerd in een Stream Analytics taak.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Deze Power shell-opdracht retourneert informatie over de trans formatie met de naam StreamingJob in de taak-StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Hiermee wordt een nieuwe invoer gemaakt binnen een Stream Analytics-taak, of een bestaande opgegeven invoer bijgewerkt.

De naam van de invoer kan worden opgegeven in het JSON-bestand of op de opdracht regel. Als beide zijn opgegeven, moet de naam op de opdracht regel gelijk zijn aan die in het bestand.

Als u een invoer opgeeft die al bestaat en de para meter-Force niet opgeeft, wordt u door de cmdlet gevraagd of u de bestaande invoer wilt vervangen.

Als u de para meter-Force opgeeft en een bestaande invoer naam opgeeft, wordt de invoer vervangen zonder bevestiging.

Raadpleeg de sectie [invoer maken (Azure stream Analytics)][msdn-rest-api-create-stream-analytics-input] van de [referentie bibliotheek voor stream Analytics beheer rest API][stream.analytics.rest.api.reference]voor gedetailleerde informatie over de structuur en inhoud van het JSON-bestand.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Met deze Power shell-opdracht maakt u een nieuwe invoer van het bestand Input.jsop. Als een bestaande invoer met de naam die is opgegeven in het invoer definitie bestand al is gedefinieerd, wordt door de cmdlet gevraagd of deze moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Met deze Power shell-opdracht maakt u een nieuwe invoer in de taak met de naam EntryStream. Als er al een bestaande invoer met deze naam is gedefinieerd, wordt u door de cmdlet gevraagd of u deze wilt vervangen.

**Voorbeeld 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Met deze Power shell-opdracht wordt de definitie van de bestaande invoer bron met de naam EntryStream vervangen door de definitie uit het bestand.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Hiermee wordt een nieuwe Stream Analytics-taak gemaakt in Microsoft Azure of wordt de definitie van een bestaande opgegeven taak bijgewerkt.

De naam van de taak kan worden opgegeven in het JSON-bestand of op de opdracht regel. Als beide zijn opgegeven, moet de naam op de opdracht regel gelijk zijn aan die in het bestand.

Als u een taak naam opgeeft die al bestaat en de para meter-Force niet opgeeft, wordt u door de cmdlet gevraagd of u de bestaande taak wilt vervangen.

Als u de para meter-Force opgeeft en een bestaande taak naam opgeeft, wordt de taak definitie vervangen zonder bevestiging.

Raadpleeg de sectie [Stream Analytics-taak maken][msdn-rest-api-create-stream-analytics-job] van de [Stream Analytics beheer rest API referentie bibliotheek][stream.analytics.rest.api.reference]voor gedetailleerde informatie over de structuur en inhoud van het JSON-bestand.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Met deze Power shell-opdracht wordt een nieuwe taak gemaakt op basis van de definitie in JobDefinition.jsop. Als er al een bestaande taak met de naam in het taak definitie bestand is gedefinieerd, wordt u door de cmdlet gevraagd of u deze wilt vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Met deze Power shell-opdracht wordt de taak definitie voor StreamingJob vervangen.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Hiermee wordt een nieuwe uitvoer in een Stream Analytics-taak gemaakt of wordt een bestaande uitvoer bijgewerkt.  

De naam van de uitvoer kan worden opgegeven in het JSON-bestand of op de opdracht regel. Als beide zijn opgegeven, moet de naam op de opdracht regel gelijk zijn aan die in het bestand.

Als u een uitvoer opgeeft die al bestaat en de para meter-Force niet opgeeft, wordt u door de cmdlet gevraagd of u de bestaande uitvoer wilt vervangen.

Als u de para meter-Force opgeeft en een bestaande uitvoer naam opgeeft, wordt de uitvoer zonder bevestiging vervangen.

Raadpleeg de sectie [uitvoer maken (Azure stream Analytics)][msdn-rest-api-create-stream-analytics-output] van de [Stream Analytics beheer rest API referentie bibliotheek][stream.analytics.rest.api.reference]voor gedetailleerde informatie over de structuur en inhoud van het JSON-bestand.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Met deze Power shell-opdracht maakt u een nieuwe uitvoer met de naam ' output ' in de taak StreamingJob. Als er al een bestaande uitvoer met deze naam is gedefinieerd, wordt u door de cmdlet gevraagd of u deze wilt vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Met deze Power shell-opdracht wordt de definitie voor "output" vervangen door de taak StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Hiermee wordt een nieuwe trans formatie gemaakt binnen een Stream Analytics taak of wordt de bestaande trans formatie bijgewerkt.

De naam van de trans formatie kan worden opgegeven in het JSON-bestand of op de opdracht regel. Als beide zijn opgegeven, moet de naam op de opdracht regel gelijk zijn aan die in het bestand.

Als u een trans formatie opgeeft die al bestaat en de para meter-Force niet opgeeft, wordt u door de cmdlet gevraagd of u de bestaande trans formatie wilt vervangen.

Als u de para meter-Force opgeeft en een bestaande transformatie naam opgeeft, wordt de trans formatie vervangen zonder bevestiging.

Raadpleeg de sectie [trans formatie maken (Azure stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] van de [Stream Analytics beheer rest API referentie bibliotheek][stream.analytics.rest.api.reference]voor gedetailleerde informatie over de structuur en inhoud van het JSON-bestand.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Met deze Power shell-opdracht maakt u een nieuwe trans formatie met de naam StreamingJobTransform in de taak StreamingJob. Als er al een bestaande trans formatie met deze naam is gedefinieerd, wordt door de cmdlet gevraagd of deze moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Met deze Power shell-opdracht wordt de definitie van StreamingJobTransform in de taak-StreamingJob vervangen.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Hiermee wordt een specifieke invoer asynchroon verwijderd uit een Stream Analytics-taak in Microsoft Azure.  
Als u de para meter-Force opgeeft, wordt de invoer zonder bevestiging verwijderd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Met deze Power shell-opdracht wordt de invoer-EventStream in de taak-StreamingJob verwijderd.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Hiermee verwijdert u een specifieke Stream Analytics taak asynchroon in Microsoft Azure.  
Als u de para meter-Force opgeeft, wordt de taak zonder bevestiging verwijderd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Met deze Power shell-opdracht verwijdert u de taak StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Hiermee verwijdert u een specifieke uitvoer van een Stream Analytics taak asynchroon in Microsoft Azure.  
Als u de para meter-Force opgeeft, wordt de uitvoer zonder bevestiging verwijderd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Met deze Power shell-opdracht wordt de uitvoer uitvoer in het StreamingJob van de taak verwijderd.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Hiermee wordt een Stream Analytics taak asynchroon geïmplementeerd en gestart in Microsoft Azure.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1,0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Met deze Power shell-opdracht start u de taak StreamingJob met een aangepaste start tijd voor uitvoer ingesteld op 12 december 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Hiermee wordt een Stream Analytics taak asynchroon gestopt in Microsoft Azure en worden de resources die worden gebruikt, opnieuw toegewezen. De taak definitie en meta gegevens blijven beschikbaar in uw abonnement via de Api's voor Azure Portal en beheer, zodat de taak kan worden bewerkt en opnieuw wordt gestart. Er worden geen kosten in rekening gebracht voor een taak met de status gestopt.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Met deze Power shell-opdracht stopt u de taak StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Test de mogelijkheid van Stream Analytics om verbinding te maken met een opgegeven invoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Met deze Power shell-opdracht test u de verbindings status van de invoer EntryStream in StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Hiermee test u de mogelijkheid van Stream Analytics om verbinding te maken met een opgegeven uitvoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Met deze Power shell-opdracht wordt de verbindings status van de uitvoer uitvoer in StreamingJob getest.  

## <a name="get-support"></a>Ondersteuning krijgen
Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html). 

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
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/