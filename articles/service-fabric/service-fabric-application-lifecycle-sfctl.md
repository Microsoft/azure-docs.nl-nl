---
title: Azure Service Fabric-toepassingen beheren met sfctl
description: Meer informatie over het implementeren en verwijderen van toepassingen vanuit een Azure Service Fabric-cluster met behulp van Azure Service Fabric CLI
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84711031"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Een Azure Service Fabric-toepassing beheren met behulp van Azure Service Fabric CLI (sfctl)

Meer informatie over het maken en verwijderen van toepassingen die worden uitgevoerd in een Azure Service Fabric-cluster.

## <a name="prerequisites"></a>Vereisten

* Installeer Service Fabric CLI. Selecteer vervolgens uw Service Fabric cluster. Zie [aan de slag met Service Fabric cli](service-fabric-cli.md)voor meer informatie.

* Een Service Fabric toepassings pakket kan worden geïmplementeerd. Meer informatie over het ontwerpen en inpakken van een toepassing vindt u in het [service Fabric toepassings model](service-fabric-application-model.md).

## <a name="overview"></a>Overzicht

Voer de volgende stappen uit om een nieuwe toepassing te implementeren:

1. Upload een toepassings pakket naar de opslag voor de Service Fabric-installatie kopie.
2. Richt een toepassings type in.
3. De inhoud van het installatie kopie archief verwijderen.
4. Een toepassing opgeven en maken.
5. Services opgeven en maken.

Voer de volgende stappen uit om een bestaande toepassing te verwijderen:

1. Verwijder de toepassing.
2. De inrichting van het bijbehorende toepassings type ongedaan maken.

## <a name="deploy-a-new-application"></a>Een nieuwe toepassing implementeren

Voer de volgende taken uit om een nieuwe toepassing te implementeren:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Een nieuw toepassings pakket uploaden naar de installatie kopie opslag

Voordat u een toepassing maakt, uploadt u het toepassings pakket naar het archief met de Service Fabric-installatie kopie.

Als uw toepassings pakket zich bijvoorbeeld in de directory bevindt `app_package_dir` , gebruikt u de volgende opdrachten om de map te uploaden:

```shell
sfctl application upload --path ~/app_package_dir
```

Voor grote toepassings pakketten kunt u de optie opgeven `--show-progress` om de voortgang van het uploaden weer te geven.

### <a name="provision-the-application-type"></a>Het toepassings type inrichten

Wanneer het uploaden is voltooid, moet de toepassing worden ingericht. Gebruik de volgende opdracht om de toepassing in te richten:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

De waarde voor `application-type-build-path` is de naam van de map waar u het toepassings pakket hebt geüpload.

### <a name="delete-the-application-package"></a>Het toepassings pakket verwijderen

Het is raadzaam het toepassings pakket te verwijderen nadat de toepassing is geregistreerd.  Als u toepassings pakketten uit de installatie kopie opslag verwijdert, worden de systeem bronnen vrijgemaakt.  Het houden van ongebruikte toepassings pakketten verbruikt schijf opslag en leidt tot prestatie problemen van toepassingen. 

Als u het toepassings pakket uit het archief met installatie kopieën wilt verwijderen, gebruikt u de volgende opdracht:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path` moet de naam zijn van de map die u hebt geüpload tijdens het maken van de toepassing.

### <a name="create-an-application-from-an-application-type"></a>Een toepassing maken op basis van een toepassings type

Nadat u de toepassing hebt ingericht, gebruikt u de volgende opdracht voor het benoemen en maken van uw toepassing:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` is de naam die u wilt gebruiken voor het toepassings exemplaar. U kunt aanvullende para meters ophalen uit het eerder ingerichte toepassings manifest.

De naam van de toepassing moet beginnen met het voor voegsel `fabric:/` .

### <a name="create-services-for-the-new-application"></a>Services voor de nieuwe toepassing maken

Nadat u een toepassing hebt gemaakt, maakt u services van de toepassing. In het volgende voor beeld maken we een nieuwe stateless service van onze toepassing. De services die u kunt maken op basis van een toepassing, worden gedefinieerd in een service manifest in het eerder ingerichte toepassings pakket.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Toepassings implementatie en-status controleren

Gebruik de volgende status opdrachten om te controleren of alles in orde is:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Als u wilt controleren of de service in orde is, gebruikt u vergelijk bare opdrachten om de status van de service en de toepassing op te halen:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Gezonde Services en toepassingen hebben een `HealthState` waarde van `Ok` .

## <a name="remove-an-existing-application"></a>Een bestaande toepassing verwijderen

Als u een toepassing wilt verwijderen, voert u de volgende taken uit:

### <a name="delete-the-application"></a>De toepassing verwijderen

Als u de toepassing wilt verwijderen, gebruikt u de volgende opdracht:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Inrichting van het toepassings type ongedaan maken

Nadat u de toepassing hebt verwijderd, kunt u de inrichting van het toepassings type ongedaan maken als u het niet meer nodig hebt. Gebruik de volgende opdracht om de inrichting van het toepassings type ongedaan te maken:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

De type naam en type versie moeten overeenkomen met de naam en versie in het eerder ingerichte toepassings manifest.

## <a name="upgrade-application"></a>Toepassing bijwerken

Nadat u de toepassing hebt gemaakt, kunt u dezelfde reeks stappen herhalen om een tweede versie van uw toepassing in te richten. Vervolgens kunt u met een Service Fabric-toepassings upgrade overstappen op het uitvoeren van de tweede versie van de toepassing. Zie de documentatie over [service Fabric toepassings upgrades](service-fabric-application-upgrade.md)voor meer informatie.

Als u een upgrade wilt uitvoeren, moet u eerst de volgende versie van de toepassing inrichten met dezelfde opdrachten als vóór:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Het wordt aanbevolen om een bewaakte automatische upgrade uit te voeren door de volgende opdracht uit te voeren:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Upgrades overschrijven bestaande para meters met welke set is opgegeven. Toepassings parameters moeten, indien nodig, worden door gegeven als argumenten voor de upgrade-opdracht. Toepassings parameters moeten worden gecodeerd als een JSON-object.

Als u eerder opgegeven para meters wilt ophalen, kunt u de `sfctl application info` opdracht gebruiken.

Wanneer een upgrade van een toepassing wordt uitgevoerd, kan de status worden opgehaald met behulp van de `sfctl application upgrade-status` opdracht.

Ten slotte, als er een upgrade wordt uitgevoerd en moet worden geannuleerd, kunt u de `sfctl application upgrade-rollback` upgrade terugdraaien.

## <a name="next-steps"></a>Volgende stappen

* [Basis beginselen van Service Fabric CLI](service-fabric-cli.md)
* [Aan de slag met Service Fabric in Linux](service-fabric-get-started-linux.md)
* [Een upgrade van een Service Fabric-toepassing starten](service-fabric-application-upgrade.md)
