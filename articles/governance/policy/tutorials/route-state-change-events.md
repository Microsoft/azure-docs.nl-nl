---
title: 'Zelf studie: gebeurtenissen voor het wijzigen van het routerings beleid naar Event Grid met Azure CLI'
description: In deze zelf studie configureert u Event Grid om te Luis teren naar beleids status wijzigings gebeurtenissen en een webhook aan te roepen.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735538"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Zelf studie: gebeurtenissen voor het wijzigen van het routerings beleid naar Event Grid met Azure CLI

In dit artikel vindt u informatie over het instellen van Azure Policy gebeurtenis abonnementen voor het verzenden van beleids status wijzigings gebeurtenissen naar een webeindpunt. Azure Policy gebruikers kunnen zich abonneren op gebeurtenissen die worden verzonden wanneer er wijzigingen in de beleids status optreden op resources. Deze gebeurtenissen kunnen webhooks, [Azure functions](../../../azure-functions/index.yml), [Azure Storage wacht rijen](../../../storage/queues/index.yml)of andere gebeurtenis-handlers activeren die door [Azure Event grid](../../../event-grid/index.yml)worden ondersteund. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. U kunt deze zelf studie echter vereenvoudigen door de gebeurtenissen te verzenden naar een web-app waarmee de berichten worden verzameld en weer gegeven.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Voor deze quickstart moet u versie 2.0.76 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

- Ook als u Azure Policy of Event Grid eerder hebt gebruikt, moet u de betreffende resource providers opnieuw registreren:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Event Grid-onderwerpen zijn Azure-resources en moeten in een Azure-resourcegroep worden geplaatst. De resourcegroep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). 

In het volgende voor beeld wordt een resource groep `<resource_group_name>` met de naam op de locatie _westus_ gemaakt. Vervang `<resource_group_name>` door een unieke naam voor uw resourcegroep.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Een Event Grid-systeem onderwerp maken

Nu we een resource groep hebben, maken we een [systeem onderwerp](../../../event-grid/system-topics.md). Een systeem onderwerp in Event Grid vertegenwoordigt een of meer gebeurtenissen die zijn gepubliceerd door Azure-Services, zoals Azure Policy en Azure Event Hubs. In dit systeem onderwerp wordt het `Microsoft.PolicyInsights.PolicyStates` type onderwerp gebruikt voor het wijzigen van de status van Azure Policy. Vervang `<SubscriptionID>` in de **bereik** parameter door de id van uw abonnement en `<resource_group_name>` in de para meter voor de **resource groep** met de eerder gemaakte resource groep.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op het onderwerp, gaan we het eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

Vervang `<your-site-name>` door een unieke naam voor de web-app. De naam van de web-app moet uniek zijn omdat deze deel uitmaakt van de DNS-vermelding.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

Op de site zouden momenteel geen berichten moeten wijn weergeven.

## <a name="subscribe-to-the-system-topic"></a>Abonneren op het onderwerp van het systeem

U abonneert u op een onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren en waar deze gebeurtenissen naartoe moeten worden gestuurd. In het volgende voor beeld wordt het systeem onderwerp dat u hebt gemaakt, geabonneerd en wordt de URL van uw web-app als het eind punt door gegeven om gebeurtenis meldingen te ontvangen. Vervang `<event_subscription_name>` door een naam voor het gebeurtenisabonnement. Gebruik voor `<resource_group_name>` en `<your-site-name>` de waarden die u eerder hebt gemaakt.

Het eindpunt voor uw web-app moet het achtervoegsel `/api/updates/` bevatten.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Scherm afbeelding van de validatie gebeurtenis voor het Event Grid-abonnement in de vooraf gebouwde web-app.":::

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze Quick Start maakt u een beleids toewijzing en wijst u de definitie **een tag vereist voor resource groepen** toe. Deze beleids definitie identificeert resource groepen waarvoor de tag ontbreekt die is geconfigureerd tijdens de beleids toewijzing.

Voer de volgende opdracht uit om een beleids toewijzings bereik te maken voor de resource groep die u hebt gemaakt om het onderwerp Event grid te bewaren:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

De voorgaande opdracht maakt gebruik van de volgende informatie:

- **Naam**: de werkelijke naam van de toewijzing. Voor dit voor beeld zijn _requiredtags-Events_ gebruikt.
- **Weergavenaam**: de weergavenaam voor de beleidstoewijzing. In dit geval gebruikt u _tag vereist op RG_.
- **Bereik**: een bereik bepaalt voor welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Dit kan variëren van een abonnement tot resourcegroepen. Vergeet niet om &lt;scope&gt; te vervangen door de naam van uw resourcegroep. De indeling voor een resource groeps bereik is `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` .
- **Beleid**: de id van de beleidsdefinitie, op basis waarvan u de toewijzing maakt. In dit geval is de ID van de beleids definitie _een tag voor resource groepen nodig_. Voer de volgende opdracht uit om de id van de beleidstoewijzing te verkrijgen: `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

Nadat u de beleids toewijzing hebt gemaakt, wacht u totdat een gebeurtenis melding van **micro soft. PolicyInsights. PolicyStateCreated wordt** weer gegeven in de web-app. De resource groep die we hebben gemaakt, toont een `data.complianceState` waarde van niet- _compatibel_ om te starten.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Scherm afbeelding van de gebeurtenis voor het maken van het beleid voor Event Grid voor de resource groep in de vooraf gebouwde web-app.":::

> [!NOTE]
> Als de resource groep andere beleids toewijzingen overneemt van de hiërarchie van het abonnement of de beheer groep, worden er ook gebeurtenissen voor elk beleid weer gegeven. Bevestig de gebeurtenis voor de toewijzing in deze zelf studie door de eigenschap te evalueren `data.policyDefinitionId` .

## <a name="trigger-a-change-on-the-resource-group"></a>Een wijziging in de resource groep activeren

Om de resource groep compatibel te maken, is een tag met de naam **EventTest** vereist. Voeg de tag toe aan de resource groep met de volgende opdracht die wordt vervangen `<SubscriptionID>` door uw abonnements-id en `<ResourceGroup>` met de naam van de resource groep:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Nadat u de vereiste tag aan de resource groep hebt toegevoegd, wacht u totdat een gebeurtenis melding van **micro soft. PolicyInsights. PolicyStateChanged** wordt weer gegeven in de web-app. Vouw de gebeurtenis uit en de `data.complianceState` waarde wordt nu als _compatibel_ weer gegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met deze web-app en Azure Policy gebeurtenis abonnement, moet u de resources die in dit artikel zijn gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende opdracht om de resources te verwijderen die u in dit artikel hebt gemaakt.

Vervang `<resource_group_name>` door de resourcegroep die u eerder hebt gemaakt.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u onderwerpen en gebeurtenis abonnementen voor Azure Policy maakt, leest u meer over de status wijzigings gebeurtenissen van het beleid en wat Event Grid u kunt helpen:

- [Reageren op Azure Policy status wijzigings gebeurtenissen](../concepts/event-overview.md)
- [Azure Policy schema Details voor Event Grid](../../../event-grid/event-schema-policy.md)
- [Over Event Grid](../../../event-grid/overview.md)