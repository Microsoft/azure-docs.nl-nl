---
title: Gebeurtenissen publiceren met gebeurtenis domeinen met Azure Event Grid
description: Laat zien hoe u grote sets met onderwerpen beheert in Azure Event Grid en gebeurtenissen hierop publiceert met behulp van gebeurtenis domeinen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e6861e89def10eec391bf302b1ddc726b38bb98c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109892"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Onderwerpen beheren en gebeurtenissen publiceren met behulp van gebeurtenis domeinen

In dit artikel wordt beschreven hoe u:

* Een Event Grid domein maken
* Onderwerpen over het abonneren op Event grid
* Sleutels vermelden
* Gebeurtenissen publiceren naar een domein

Zie voor meer informatie over gebeurtenis domeinen [begrijpen gebeurtenis domeinen voor het beheren van Event grid onderwerpen](event-domains.md).

## <a name="create-an-event-domain"></a>Een gebeurtenis domein maken

Als u grote sets met onderwerpen wilt beheren, maakt u een gebeurtenis domein.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
New-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

Het maken is voltooid met de volgende waarden:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Let `endpoint` op de en de vereisten die `id` nodig zijn voor het beheren van het domein en het publiceren van gebeurtenissen.

## <a name="manage-access-to-topics"></a>Toegang tot onderwerpen beheren

Het beheren van toegang tot onderwerpen geschiedt via [roltoewijzing](../role-based-access-control/role-assignments-cli.md). De roltoewijzing maakt gebruik van Azure op rollen gebaseerd toegangs beheer om bewerkingen op Azure-resources te beperken tot geautoriseerde gebruikers binnen een bepaald bereik.

Event Grid heeft twee ingebouwde rollen, die u kunt gebruiken om bepaalde gebruikers toegang te verlenen voor verschillende onderwerpen binnen een domein. Deze rollen zijn `EventGrid EventSubscription Contributor (Preview)` , waarmee u abonnementen kunt maken en verwijderen, en `EventGrid EventSubscription Reader (Preview)` waarmee u alleen gebeurtenis abonnementen kunt weer geven.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
De volgende Azure CLI-opdracht beperkt het `alice@contoso.com` maken en verwijderen van gebeurtenis abonnementen alleen in het onderwerp `demotopic1` :

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
De volgende Power shell-opdracht is beperkt `alice@contoso.com` tot het maken en verwijderen van alleen gebeurtenis abonnementen in het onderwerp `demotopic1` :

```azurepowershell-interactive
New-AzRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Zie voor meer informatie over het beheren van toegang voor Event Grid-bewerkingen [Event grid beveiliging en verificatie](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Onderwerpen en abonnementen maken

De Event Grid-Service maakt en beheert automatisch het bijbehorende onderwerp in een domein op basis van de aanroep voor het maken van een gebeurtenis abonnement voor een domein onderwerp. Er is geen afzonderlijke stap voor het maken van een onderwerp in een domein. Op dezelfde manier wordt het onderwerp ook verwijderd wanneer het laatste gebeurtenis abonnement voor een onderwerp wordt verwijderd.

Abonneren op een onderwerp in een domein is hetzelfde als abonneren op een andere Azure-resource. Geef voor de bron Resource-ID de gebeurtenis domein-ID op die wordt geretourneerd bij het maken van het domein. Als u het onderwerp wilt opgeven waarop u zich wilt abonneren, voegt u toe `/topics/<my-topic>` aan het einde van de bron resource-id. Als u een gebeurtenis abonnement voor een domein scope wilt maken dat alle gebeurtenissen in het domein ontvangt, geeft u de gebeurtenis domein-ID op zonder onderwerpen op te geven.

Normaal gesp roken maakt de gebruiker aan wie u toegang hebt verleend in de voor gaande sectie het abonnement. U kunt dit artikel vereenvoudigen door het abonnement te maken. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Als u een eind punt nodig hebt om uw gebeurtenissen te abonneren op, kunt u altijd een [vooraf gemaakte web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarin de binnenkomende gebeurtenissen worden weer gegeven. U kunt uw evenementen verzenden naar uw test website op `https://<your-site-name>.azurewebsites.net/api/updates` .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>

Machtigingen die zijn ingesteld voor een onderwerp worden opgeslagen in Azure Active Directory en moeten expliciet worden verwijderd. Als u een gebeurtenis abonnement verwijdert, wordt de toegang van gebruikers tot het maken van gebeurtenis abonnementen niet ingetrokken als de gebruiker schrijf toegang heeft voor een onderwerp.


## <a name="publish-events-to-an-event-grid-domain"></a>Gebeurtenissen publiceren naar een Event Grid domein

Het publiceren van gebeurtenissen naar een domein is hetzelfde als het [publiceren naar een aangepast onderwerp](./post-to-custom-topic.md). In plaats van naar het aangepaste onderwerp te publiceren, publiceert u echter alle gebeurtenissen naar het domein eindpunt. In de gegevens van de JSON-gebeurtenis geeft u het onderwerp op waarnaar u de gebeurtenissen wilt laten gaan. De volgende matrix met gebeurtenissen zou ertoe leiden dat de gebeurtenis met het onderwerp en de `"id": "1111"` `demotopic1` gebeurtenis met `"id": "2222"` zou worden verzonden naar het onderwerp `demotopic2` :

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Als u het domein eindpunt wilt ophalen met Azure CLI, gebruikt u

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Als u de sleutels voor een domein wilt ophalen, gebruikt u:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Als u het domein eindpunt wilt ophalen met Power shell, gebruikt u

```azurepowershell-interactive
Get-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Als u de sleutels voor een domein wilt ophalen, gebruikt u:

```azurepowershell-interactive
Get-AzEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

En gebruik vervolgens uw favoriete methode om een HTTP POST te maken voor het publiceren van uw gebeurtenissen in uw Event Grid domein.

## <a name="search-lists-of-topics-or-subscriptions"></a>Lijsten met onderwerpen of abonnementen zoeken

Als u een groot aantal onderwerpen of abonnementen wilt doorzoeken en beheren, ondersteunen de Api's van Event Grid de vermelding en paginering.

### <a name="using-cli"></a>CLI gebruiken
Met de volgende opdracht worden bijvoorbeeld alle onderwerpen weer gegeven met de naam met `mytopic` . 

```azurecli-interactive
az eventgrid topic list --odata-query "contains(name, 'mytopic')"
```

Zie voor meer informatie over deze opdracht [`az eventgrid topic list`](/cli/azure/eventgrid/topic?#az_eventgrid_topic_list) . 


## <a name="next-steps"></a>Volgende stappen

* Zie het [Conceptueel overzicht van gebeurtenis domeinen](event-domains.md)voor meer informatie over concepten op hoog niveau in gebeurtenis domeinen en waarom ze nuttig zijn.
