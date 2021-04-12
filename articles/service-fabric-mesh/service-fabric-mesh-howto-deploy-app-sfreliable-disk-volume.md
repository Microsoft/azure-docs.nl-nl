---
title: Betrouw bare schijf volume Service Fabric met Service Fabric net
description: Meer informatie over het opslaan van de status in een Azure Service Fabric mesh-toepassing door Service Fabric betrouw bare schijf volume in de container te koppelen met behulp van de Azure CLI.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: ac65693f2513338695e07cd8a19acb13333e7281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625783"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Maxi maal beschik bare Service Fabric betrouw bare schijf op basis van schijven koppelen in een Service Fabric mesh-toepassing 

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

De algemene methode voor het persistent maken van de status met container-apps is het gebruik van externe opslag, zoals Azure File Storage of Data Base, zoals Azure Cosmos DB. Dit resulteert in een aanzienlijke Lees-en schrijf latentie voor de externe opslag.

In dit artikel wordt beschreven hoe u de status opslaat in een Maxi maal beschik bare Service Fabric betrouw bare schijf door een volume in de container van een Service Fabric mesh-toepassing te koppelen.
Service Fabric betrouw bare schijf biedt volumes voor lokale Lees bewerkingen waarbij schrijf acties binnen het Service Fabric cluster worden gerepliceerd voor hoge Beschik baarheid. Hiermee verwijdert u netwerk aanroepen voor lees bewerkingen en vermindert u de netwerk latentie voor schrijf bewerkingen. Als de container opnieuw wordt opgestart of naar een ander knoop punt wordt verplaatst, ziet u in het nieuwe container exemplaar hetzelfde volume als in een oudere versie. Het is dus zowel efficiënt als Maxi maal beschikbaar.

In dit voor beeld heeft de teller-toepassing een ASP.NET Core Service met een webpagina waarop de item waarde in een browser wordt weer gegeven.

De `counterService` waarde van een item wordt periodiek gelezen uit een bestand, verhoogd en teruggeschreven naar het bestand. Het bestand wordt opgeslagen in een map die is gekoppeld aan het volume dat wordt ondersteund door Service Fabric betrouw bare schijf.

## <a name="prerequisites"></a>Vereisten

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om deze taak te volt ooien. Als u de Azure CLI wilt gebruiken met dit artikel, moet u ervoor zorgen dat u `az --version` ten minste een waarde retourneert `azure-cli (2.0.43)` .  Installeer de module Azure Service Fabric mesh CLI (of werk deze bij) door deze [instructies](service-fabric-mesh-howto-setup-cli.md)te volgen.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure en stel uw abonnement in.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep waarin u de toepassing wilt implementeren. Met de volgende opdracht maakt u een resource groep met de naam `myResourceGroup` op een locatie in het Eastern-Verenigde Staten. Als u de naam van de resource groep in de onderstaande opdracht wijzigt, vergeet dan niet om deze te wijzigen in alle opdrachten die volgen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

>[!NOTE]
> Met ingang van 2 november 2020 zijn [snelheidslimieten voor downloaden van toepassing](https://docs.docker.com/docker-hub/download-rate-limit/) op anonieme en geverifieerde aanvragen bij Docker Hub, vanuit accounts van gratis Docker-abonnementen. Deze aanvragen worden afgedwongen op IP-adres. 
> 
> Deze sjabloon maakt gebruik van open bare installatie kopieën van docker hub. Houd er rekening mee dat er limieten kunnen gelden. Zie [Verifiëren met Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub) voor meer informatie.

Met de volgende opdracht wordt een Linux-toepassing geïmplementeerd met behulp [ van decounter.sfreliablevolume.linux.jsop de sjabloon](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Als u een Windows-toepassing wilt implementeren, gebruikt u de [counter.sfreliablevolume.windows.jsop sjabloon](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Houd er rekening mee dat grotere container installatie kopieën langer kunnen worden geïmplementeerd.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

U kunt ook de status van de implementatie bekijken met de opdracht

```azurecli-interactive
az deployment group show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Let op de naam van de gateway resource die het resource type heeft als `Microsoft.ServiceFabricMesh/gateways` . Dit wordt gebruikt om het open bare IP-adres van de app op te halen.

## <a name="open-the-application"></a>De toepassing openen

Zodra de toepassing is geïmplementeerd, haalt u het IP-adres van de gateway bron op voor de app. Gebruik de naam van de gateway die u hebt gezien in de bovenstaande sectie.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

De uitvoer moet een eigenschap hebben `ipAddress` die het open bare IP-adres van het service-eind punt is. Open het bestand vanuit een browser. Er wordt een webpagina weer gegeven met de item waarde die elke seconde wordt bijgewerkt.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Controleer of de toepassing het volume kan gebruiken

De toepassing maakt een bestand `counter.txt` met de naam in het volume in de `counter/counterService` map. De inhoud van dit bestand is de item waarde die op de webpagina wordt weer gegeven.

## <a name="delete-the-resources"></a>De resources verwijderen

Verwijder regel matig de resources die u niet meer gebruikt in Azure. Als u de resources die zijn gerelateerd aan dit voor beeld wilt verwijderen, verwijdert u de resource groep waarin ze zijn geïmplementeerd (waarmee alles wordt verwijderd die aan de resource groep is gekoppeld) met de volgende opdracht:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk de Service Fabric betrouw bare voorbeeld toepassing voor volume schijf in [github](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Zie [Inleiding tot Service Fabric Resource-model](service-fabric-mesh-service-fabric-resources.md) voor meer informatie over het Service Fabric Resource-model.
- Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.