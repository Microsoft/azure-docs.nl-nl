---
title: Automatisch back-ups maken van sleutel waarden van Azure-app configuratie archieven
description: Meer informatie over het instellen van een automatische back-up van sleutel waarden tussen app-configuratie archieven.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b3e0bcad7beccc31e1772fbb24ffad7f502b8140
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454240"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Automatisch back-ups maken van app-configuratie archieven

In dit artikel leert u hoe u een automatische back-up van sleutel waarden kunt instellen vanuit een primaire Azure-app-configuratie archief naar een secundair archief. De automatische back-up maakt gebruik van de integratie van Azure Event Grid met app-configuratie. 

Nadat u de automatische back-up hebt ingesteld, publiceert de app-configuratie gebeurtenissen naar Azure Event Grid voor alle wijzigingen die zijn aangebracht in sleutel waarden in een configuratie opslag. Event Grid ondersteunt een groot aantal Azure-Services waarvan gebruikers zich kunnen abonneren op de gebeurtenissen die worden verzonden wanneer sleutel waarden worden gemaakt, bijgewerkt of verwijderd.

## <a name="overview"></a>Overzicht

In dit artikel gebruikt u Azure Queue-opslag om gebeurtenissen van Event Grid te ontvangen en een timer trigger van Azure Functions te gebruiken voor het verwerken van gebeurtenissen in de wachtrij in batches. 

Wanneer een functie wordt geactiveerd, op basis van de gebeurtenissen, worden de meest recente waarden opgehaald van de sleutels die zijn gewijzigd in het configuratie archief van de primaire app en worden de secundaire opslag dienovereenkomstig bijgewerkt. Deze instelling helpt bij het combi neren van meerdere wijzigingen die zich in een korte periode in één back-upbewerking voordoen, waardoor overmatige aanvragen voor uw app-configuratie archieven worden voor komen.

![Diagram waarin de architectuur van de back-up van het app-configuratie archief wordt weer gegeven.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Inrichten van resources

De motivatie achter back-ups van app-configuratie archieven is het gebruik van meerdere configuratie archieven in verschillende Azure-regio's om de geo-tolerantie van uw toepassing te verg Roten. Om dit te kunnen doen, moeten uw primaire en secundaire winkels zich in verschillende Azure-regio's bevinden. Alle andere resources die u in deze zelf studie hebt gemaakt, kunnen in elke gewenste regio worden ingericht. Dit komt doordat als de primaire regio niet beschikbaar is, er geen nieuwe back-ups kunnen worden gemaakt totdat de primaire regio weer toegankelijk is.

In deze zelf studie maakt u een secundair archief in de `centralus` regio en alle andere resources in de `westus` regio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>Vereisten 

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de werk belasting Azure Development.

- [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor deze zelf studie is versie 2.3.1 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

De resourcegroep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met de opdracht [az group create](/cli/azure/group).

In het volgende voorbeeld wordt een resourcegroep met de naam `<resource_group_name>` gemaakt op de locatie `westus`.  Vervang `<resource_group_name>` door een unieke naam voor uw resourcegroep.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>App-configuratie archieven maken

Maak uw primaire en secundaire app-configuratie archieven in verschillende regio's.
Vervang `<primary_appconfig_name>` en `<secondary_appconfig_name>` door unieke namen voor uw configuratie archieven. Elke archief naam moet uniek zijn omdat deze wordt gebruikt als een DNS-naam.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>Een wachtrij maken

Maak een opslag account en een wachtrij voor het ontvangen van de gebeurtenissen die worden gepubliceerd door Event Grid.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Abonneren op gebeurtenissen in het configuratie archief van uw app

U abonneert u op deze twee gebeurtenissen vanuit het configuratie archief van de primaire app:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Met de volgende opdracht maakt u een Event Grid-abonnement voor de twee gebeurtenissen die naar uw wachtrij worden verzonden. Het eindpunt type is ingesteld op `storagequeue` en het eind punt wordt ingesteld op de wachtrij-id. Vervang door `<event_subscription_name>` de naam van uw keuze voor het gebeurtenis abonnement.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Functies maken voor het verwerken van gebeurtenissen uit de wachtrij opslag

### <a name="set-up-with-ready-to-use-functions"></a>Instellen met kant-en-klare functies

In dit artikel maakt u gebruik van C#-functies die de volgende eigenschappen hebben:
- Runtime stack .NET Core 3,1
- Azure Functions runtime versie 3. x
- De functie wordt elke 10 minuten geactiveerd door de timer

We hebben [een functie getest en gepubliceerd](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) die u kunt gebruiken zonder wijzigingen aan te brengen in de code om het voor u gemakkelijker te maken om een back-up van uw gegevens te starten. Down load de project bestanden en [Publiceer deze naar uw eigen Azure function-app vanuit Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Breng geen wijzigingen aan in de omgevings variabelen in de code die u hebt gedownload. U maakt de vereiste app-instellingen in de volgende sectie.
>

### <a name="build-your-own-function"></a>Bouw uw eigen functie

Als de hierboven vermelde voorbeeld code niet aan uw vereisten voldoet, kunt u ook uw eigen functie maken. De functie moet in staat zijn om de volgende taken uit te voeren om de back-up te volt ooien:
- Lees de inhoud van uw wachtrij regel matig om te zien of deze meldingen van Event Grid bevat. Raadpleeg de [opslag wachtrij-SDK](../storage/queues/storage-quickstart-queues-dotnet.md) voor meer informatie over de implementatie.
- Als uw wachtrij [gebeurtenis meldingen bevat van Event grid](./concept-app-configuration-event.md#event-schema), extraheert u alle unieke `<key, label>` gegevens uit gebeurtenis berichten. De combi natie van sleutel en label is de unieke id voor wijzigingen in de sleutel waarde in het primaire archief.
- Alle instellingen van het primaire archief lezen. Werk alleen de instellingen in het secundaire archief bij die een corresponderende gebeurtenis in de wachtrij hebben. Verwijder alle instellingen uit het secundaire archief die aanwezig waren in de wachtrij, maar niet in het primaire archief. U kunt de [app-configuratie-SDK](https://github.com/Azure/AppConfiguration#sdks) gebruiken om via een programma toegang te krijgen tot uw configuratie archieven.
- Berichten uit de wachtrij verwijderen als er geen uitzonde ringen zijn tijdens de verwerking.
- Implementeer fout afhandeling volgens uw behoeften. Raadpleeg het voor gaande code voorbeeld voor een aantal veelvoorkomende uitzonde ringen die u mogelijk wilt verwerken.

Zie voor meer informatie over het maken van een functie: [een functie in azure maken die wordt geactiveerd door een timer](../azure-functions/functions-create-scheduled-function.md) en [Azure functions ontwikkelen met behulp van Visual Studio](../azure-functions/functions-develop-vs.md).


> [!IMPORTANT]
> Gebruik uw beste beoordeling om het timer schema te kiezen op basis van hoe vaak u wijzigingen aanbrengt in uw primaire configuratie opslag. Als u de functie te vaak uitvoert, worden er mogelijk aanvragen voor het beperken van uw Store beëindigd.
>


## <a name="create-function-app-settings"></a>Instellingen voor de functie-app maken

Als u gebruikmaakt van een functie die we hebben gegeven, hebt u de volgende app-instellingen nodig in de functie-app:
- `PrimaryStoreEndpoint`: Het eind punt voor het configuratie archief van de primaire app. Een voorbeeld is `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Het eind punt voor de configuratie opslag van de secundaire app. Een voorbeeld is `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: Wachtrij-URI. Een voorbeeld is `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

Met de volgende opdracht worden de vereiste app-instellingen gemaakt in uw functie-app. Vervang door `<function_app_name>` de naam van uw functie-app.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Toegang verlenen tot de beheerde identiteit van de functie-app

Gebruik de volgende opdracht of de [Azure Portal](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) om een door het systeem toegewezen beheerde identiteit voor uw functie-app toe te voegen.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Voor het uitvoeren van de vereiste functie voor het maken van resources en het beheer van rollen moet uw account `Owner` machtigingen hebben op het juiste bereik (uw abonnement of resource groep). Als u hulp nodig hebt bij roltoewijzing, leert u [hoe u Azure-functies toewijst met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md).

Gebruik de volgende opdrachten of de [Azure Portal](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) om de beheerde identiteit van uw functie-app toegang te verlenen tot uw app-configuratie archieven. Gebruik deze rollen:
- Wijs de `App Configuration Data Reader` rol toe aan het configuratie archief van de primaire app.
- Wijs de `App Configuration Data Owner` rol toe aan het configuratie archief van de secundaire app.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Gebruik de volgende opdracht of de [Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) om de beheerde identiteit van uw functie-app toegang tot uw wachtrij te verlenen. Wijs de `Storage Queue Data Contributor` rol toe aan de wachtrij.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Een configuratie gebeurtenis voor een app activeren

Als u wilt testen of alles werkt, kunt u een sleutel waarde uit het primaire archief maken, bijwerken of verwijderen. U moet deze wijziging in het secundaire archief een paar seconden na de timer activeren Azure Functions.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

U hebt de gebeurtenis geactiveerd. Na enkele ogen blikken wordt de gebeurtenis melding door Event Grid naar uw wachtrij verzonden. *Nadat de volgende geplande uitvoering van de functie is uitgevoerd*, bekijkt u de configuratie-instellingen in het secundaire archief om te zien of deze de bijgewerkte sleutel waarde uit het primaire archief bevat.

> [!NOTE]
> U kunt [de functie hand matig activeren](../azure-functions/functions-manually-run-non-http.md) tijdens het testen en oplossen van problemen zonder te wachten op de geplande timer trigger.

Nadat u hebt gecontroleerd of de back-upfunctie is uitgevoerd, kunt u zien dat de sleutel nu aanwezig is in uw secundaire archief.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Problemen oplossen

Als u de nieuwe instelling niet ziet in uw secundaire archief:

- Zorg ervoor dat de back-upfunctie is geactiveerd *nadat* u de instelling in uw primaire archief hebt gemaakt.
- Het is mogelijk dat Event Grid de gebeurtenis melding niet op tijd kon verzenden naar de wachtrij. Controleer of de wachtrij nog steeds de gebeurtenis melding van uw primaire archief bevat. Als dit het geval is, moet u de back-upfunctie opnieuw activeren.
- Controleer [Azure functions logboeken](../azure-functions/functions-create-scheduled-function.md#test-the-function) op fouten of waarschuwingen.
- Gebruik de [Azure Portal](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) om ervoor te zorgen dat de Azure function-app de juiste waarden bevat voor de toepassings instellingen die Azure functions probeert te lezen.
- U kunt ook bewaking en waarschuwingen voor Azure Functions instellen met behulp van [Azure-toepassing Insights](../azure-functions/functions-monitoring.md?tabs=cmd). 


## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent om verder te gaan met deze app-configuratie en het gebeurtenis abonnement, kunt u de resources die in dit artikel zijn gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende opdracht om de resources te verwijderen die u in dit artikel hebt gemaakt.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u een automatische back-up van uw sleutel waarden instelt, vindt u meer informatie over hoe u de geo-tolerantie van uw toepassing kunt verhogen:

- [Tolerantie en herstel na noodgevallen](concept-disaster-recovery.md)
