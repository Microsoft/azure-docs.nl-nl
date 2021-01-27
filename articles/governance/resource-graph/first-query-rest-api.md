---
title: 'Quickstart: Uw eerste REST API-query'
description: In deze quickstart voert u de stappen uit om het Resource Graph-eindpunt voor REST API aan te roepen en uw eerste query uit te voeren.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920051"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Quickstart: Uw eerste Resource Graph-query uitvoeren met REST API

De eerste stap voor het gebruik van Azure Resource Graph met REST API is om te controleren of u een hulpprogramma hebt om REST API's aan te roepen. In deze quickstart wordt uitgelegd hoe u een query uitvoert en de resultaten ophaalt door het Azure Resource Graph REST API-eindpunt aan te roepen.

Aan het einde van dit proces hebt u de hulpprogramma's om REST API-eindpunten aan te roepen en uw eerste Resource Graph-query uit te voeren.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Aan de slag met REST API

Als u niet bekend bent met REST API, begint u met de [Azure REST API-verwijzing](/rest/api/azure/) voor een algemeen begrip van REST API, in het bijzonder van aanvraag-URI en aanvraagbody. In dit artikel worden deze concepten gebruikt om u aan de slag te helpen met Azure Resource Graph. Er wordt van uitgegaan dat u er praktische kennis van hebt. Hulpprogramma's zoals [ARMClient](https://github.com/projectkudu/ARMClient) en anderen verwerken autorisatie automatisch en zijn voor beginners aangeraden.

Zie [Azure resource Graph REST API](/rest/api/azure-resourcegraph/) voor de specificaties van de Azure Resource Graph.

### <a name="rest-api-and-powershell"></a>REST API en PowerShell

Als u nog geen hulpprogramma hebt om REST API-aanroepen te doen, kunt u overwegen PowerShell te gebruiken voor deze instructies. Het volgende codevoorbeeld haalt een header op voor verificatie met Azure. Genereer een verificatieheader, ook wel een **Bearer-token** genoemd, en zorg ervoor dat de REST API-URI verbinding maakt met parameters of een **Aanvraagbody**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Vervang `{subscriptionId}` in de variabele `$restUri` om informatie over uw abonnement op te halen.
De variabele `$response` bevat het resultaat van de cmdlet `Invoke-RestMethod`, die kan worden geparseerd met cmdlets als [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Als het service-eindpunt van REST API een **Aanvraagbody** verwacht, geeft u een met JSON geformatteerde variabele op in de parameter `-Body` van `Invoke-RestMethod`.

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Nu de REST API-hulpprogramma's zijn toegevoegd aan uw gewenste omgeving, kunt u een eenvoudige Resource Graph-query uitvoeren. De query retourneert de eerste vijf Azure-resources met de **naam** en het **resourcetype** van elke resource.

In de aanvraagbody van elke REST API-aanroep wordt een variabele gebruikt die u moet vervangen door uw eigen waarde:

- Vervang `{subscriptionID}` door uw abonnements-ID

1. Voer als volgt uw eerste Azure Resource Graph-query uit met de REST API en het eindpunt `resources`:

   - REST API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Aanvraagtekst

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Werk de aanroep naar het eindpunt `resouces` bij en wijzig de **query** om `order by` de eigenschap **Naam**:

   - REST API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Aanvraagtekst

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Met deze opdracht worden de queryresultaten eerst beperkt en vervolgens gerangschikt.

1. Werk de aanroep naar het eindpunt `resources` bij en wijzig de **query** om eerst `order by` de eigenschap **Naam** en daarna `limit` de eerste vijf resultaten:

   - REST API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Aanvraagtekst

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en gesorteerd op de eigenschap **Naam**, maar nog steeds beperkt tot de top vijf.

Zie de [Azure Resource Graph REST-voorbeelden](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples) voor meer voorbeelden van REST API-aanroepen voor Azure resource Graph.

## <a name="clean-up-resources"></a>Resources opschonen

REST API heeft geen bibliotheken of modules die kunnen worden verwijderd. Als u een hulpprogramma hebt geïnstalleerd zoals _ARMClient_ of _Postman_ om de aanroepen te doen en dit hulpprogramma meer nodig hebt, kunt u het nu verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het Resource Graph REST API-eindpunt aangeroepen en uw eerste query uitgevoerd. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)
