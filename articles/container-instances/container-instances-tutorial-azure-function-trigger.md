---
title: 'Zelfstudie: Containergroep activeren met Azure Function'
description: Maak een met HTTP geactiveerde serverloze PowerShell-functie om het maken van Azure Container Instances te automatiseren
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7315796f2f7f89800b58f5fa607e69cd7ae3447
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935414"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Zelfstudie: Een met HTTP geactiveerde Azure-functie gebruiken om een containergroep te maken

[Azure Functions](../azure-functions/functions-overview.md) is een serverloze rekenservice waarmee scripts of code kan worden uitgevoerd als reactie op verschillende gebeurtenissen, zoals een HTTP-aanvraag, een timer, of een bericht in een Azure Storage-wachtrij.

In deze zelfstudie maakt u een Azure-functie die reageert op een HTTP-aanvraag en de implementatie van een [containergroep](container-instances-container-groups.md) activeert. In dit voorbeeld ziet u de basisprincipes van het gebruik van Azure Functions om automatisch resources te maken in Azure Container Instances. Wijzig het voorbeeld, of breid het uit, voor complexere scenario's of andere gebeurtenistriggers. 

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Visual Studio Code gebruiken met de [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions), om een eenvoudige, met HTTP geactiveerde PowerShell-functie te maken.
> * Een identiteit in de functie-app inschakelen, en deze machtigingen verlenen om Azure-resources te maken.
> * De PowerShell-functie wijzigen en opnieuw publiceren om de implementatie van een containergroep met één container te automatiseren.
> * De met HTTP geactiveerde implementatie van de container controleren.

## <a name="prerequisites"></a>Vereisten

Zie [Uw eerste functie maken in Azure met behulp van Visual Studio Code](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell#configure-your-environment) voor vereisten voor het installeren en gebruiken van Visual Studio Code met de Azure Functions-extensie in uw besturingssysteem.

Aanvullende stappen in dit artikel gebruiken Azure PowerShell. Als u PowerShell wilt installeren of upgraden, raadpleegt u [Azure PowerShell installeren][azure-powershell-install] en [Aanmelden bij Azure](/powershell/azure/get-started-azureps#sign-in-to-azure).

## <a name="create-a-basic-powershell-function"></a>Een eenvoudige PowerShell-functie maken

Volg de stappen in [Uw eerste PowerShell-functie maken in Azure](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell) om een PowerShell-functie te maken met behulp van de HTTP-triggersjabloon. Gebruik de standaardnaam voor de Azure-functie: **HttpTrigger**. Zoals wordt getoond in de quickstart, moet u de functie testen, en het project publiceren in een functie-app in Azure. Dit voorbeeld is een eenvoudige, met HTTP geactiveerde functie waarmee een teksttekenreeks wordt geretourneerd. In latere stappen in dit artikel wijzigt u de functie voor het maken van een containergroep.

In dit artikel wordt ervan uitgegaan dat u het project publiceert met de naam *myfunctionapp*, in een Azure-resourcegroep die automatisch een naam krijgt op basis van de naam van de functie-app (ook *myfunctionapp*). Vervang deze namen in latere stappen door een unieke naam voor uw functie-app en resourcegroep.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Een in Azure beheerde identiteit inschakelen in de functie-app

Met de volgende opdrachten wordt een via het systeem toegewezen [beheerde identiteit](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) ingeschakeld in de functie-app. De PowerShell-host waarop de app wordt uitgevoerd, kan automatisch verifiëren bij Azure met behulp van deze identiteit, waardoor functies acties kunnen ondernemen in Azure-services waartoe de identiteit toegang heeft. In deze zelfstudie verleent u de beheerde identiteit machtigingen om resources te maken in de resourcegroep van de functie-app. 

[Een identiteit toevoegen](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1) aan de functie-app:

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Wijs de rol Inzender toe aan de identiteit op het niveau van de resourcegroep:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>HttpTrigger-functie wijzigen

Wijzig de PowerShell-code voor de **HttpTrigger**-functie om een containergroep te maken. Ga in bestand `run.ps1` voor de functie naar het volgende codeblok. Met deze code wordt een naamwaarde weergegeven, indien een waarde is doorgegeven als een querytekenreeks in de functie-URL:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Vervang deze code door het volgende voorbeeldblok. Als hier een naamwaarde is doorgegeven in de querytekenreeks, wordt deze gebruikt om een containergroep te maken en een naam te geven met behulp van de cmdlet [New-AzContainerGroup][new-azcontainergroup]. Zorg ervoor dat u de naam van de resourcegroep *myfunctionapp* vervangt door de naam van de resourcegroep voor uw functie-app:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

In dit voorbeeld wordt een containergroep gemaakt die bestaat uit één containerinstantie waarop de installatiekopie van `alpine` wordt uitgevoerd. Op de container wordt één opdracht `echo` uitgevoerd, waarna deze wordt beëindigd. In een praktijkvoorbeeld kunt u het maken van een of meer containergroepen activeren om een batchtaak uit te voeren.
 
## <a name="test-function-app-locally"></a>Functie-app lokaal testen

Zorg ervoor dat de functie lokaal wordt uitgevoerd voordat u het functie-app-project opnieuw publiceert in Azure. Wanneer de functie lokaal wordt uitgevoerd, maakt deze geen Azure-resources. U kunt de functiestroom echter testen met en zonder het doorgeven van een naamwaarde in een querytekenreeks. Zie [Lokaal fouten opsporen in PowerShell Azure Functions](../azure-functions/functions-debug-powershell-local.md) om fouten met de functie op te sporen.

## <a name="republish-azure-function-app"></a>Azure-functie-app opnieuw publiceren

Nadat u hebt gecontroleerd of de functie lokaal wordt uitgevoerd, publiceert u het project opnieuw in de bestaande functie-app in Azure.

1. Open het Opdrachtpalet in Visual Studio Code. Zoek en selecteer `Azure Functions: Deploy to Function App...`.
1. Selecteer de huidige werkmap die moet worden gezipt en geïmplementeerd.
1. Selecteer het abonnement en vervolgens de naam van de bestaande functie-app (*myfunctionapp*). Bevestig dat u de vorige implementatie wilt overschrijven.

Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. Selecteer in deze melding de optie **Uitvoer weergeven** om de resultaten van het maken en implementeren te bekijken, inclusief de Azure-resources die u hebt bijgewerkt.

## <a name="run-the-function-in-azure"></a>De functie in Azure uitvoeren

Wanneer de implementatie is voltooid, kunt u de functie-URL ophalen. Gebruik als voorbeeld het gebied **Azure: Functions** in Visual Studio Code om de **HttpTrigger**-functie-URL te kopiëren, of haal de functie-URL op in de [Azure-portal](../azure-functions/functions-get-started.md).

De functie-URL heeft de volgende vorm:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Functie uitvoeren zonder een naam door te geven

Als eerste test voert u de opdracht `curl` uit en geeft u de functie-URL door zonder een queryreeks `name` toe te voegen. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

De functie retourneert de statuscode 200 en de tekst `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response`:

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Functie uitvoeren en de naam van een containergroep doorgeven

Voer nu de opdracht `curl` uit en voeg de naam van een containergroep (*mycontainergroup*) toe als een querytekenreeks `?name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

De functie retourneert statuscode 200 en activeert het maken van de containergroep:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Controleer of de container is uitgevoerd met de opdracht [Get-AzContainerInstanceLog][get-azcontainerinstancelog]:

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Voorbeelduitvoer:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u in deze zelfstudie hebt gemaakt, niet meer nodig hebt, kunt u de opdracht [az group delete][az-group-delete] uitvoeren om de resourcegroep en alle resources hierin te verwijderen. Met deze opdracht verwijdert u de functie-app die u hebt gemaakt, evenals de actieve container en alle gerelateerde resources.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-functie gemaakt die reageert op een HTTP-aanvraag en de implementatie van een containergroep activeert. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Visual Studio Code gebruikt met de Azure Functions-extensie, om een eenvoudige, met HTTP geactiveerde PowerShell-functie te maken.
> * Een identiteit in de functie-app inschakelen, en deze machtigingen verlenen om Azure-resources te maken.
> * De PowerShell-functie wijzigt om de implementatie van een containergroep met één container te automatiseren.
> * De met HTTP geactiveerde implementatie van de container controleren.

Lees de blogpost [Gebeurtenisgestuurde serverloze containers met PowerShell Azure Functions en Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) en het bijbehorende [codevoorbeeld](https://github.com/anthonychu/functions-powershell-run-aci) voor een gedetailleerd voorbeeld van het starten en bewaken van een containertaak.

Raadpleeg de [Azure Functions-documentatie](../azure-functions/index.yml) voor gedetailleerde instructies voor het maken van Azure-functies en het publiceren van een functieproject. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
