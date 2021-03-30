---
title: Container logboeken weer geven in azure Service Fabric
description: Hierin wordt beschreven hoe u container Logboeken kunt weer geven voor een actieve Service Fabric container Services met behulp van Service Fabric Explorer.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75458182"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Logboeken voor een Service Fabric container service weer geven
Azure Service Fabric is een container Orchestrator en ondersteunt zowel [Linux-als Windows-containers](service-fabric-containers-overview.md).  In dit artikel wordt beschreven hoe u container Logboeken weergeeft van een actieve container service of een Dead-container, zodat u problemen kunt vaststellen en oplossen.

## <a name="access-the-logs-of-a-running-container"></a>Toegang tot de logboeken van een actieve container
Container logboeken kunnen worden geopend met behulp van [service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Open in een webbrowser Service Fabric Explorer van het beheer eindpunt van het cluster door naar te navigeren `http://mycluster.region.cloudapp.azure.com:19080/Explorer` .  

Container logboeken bevinden zich op het cluster knooppunt waarop het container service-exemplaar wordt uitgevoerd. Zo kunt u de logboeken van de web-front-end-container van de [Linux-stem voorbeeld toepassing](service-fabric-quickstart-containers-linux.md)ophalen. Vouw in de structuur weergave **cluster** > **toepassingen** > **VotingType** > **Fabric:/stem/azurevotefront** uit.  Vouw vervolgens de partitie (d1aa737e-f22a-e347-be16-eec90be24bc1, in dit voor beeld) uit en controleer of de container wordt uitgevoerd op het cluster knooppunt *_lnxvm_0*.

Zoek in de structuur weergave het code pakket op het knoop punt *_lnxvm_0* door **knoop punten** uit te breiden > **_lnxvm_0** > **Fabric:/stem** > **azurevotfrontPkg** > **code packages** code > .  Selecteer vervolgens de optie **container logboeken** om de container logboeken weer te geven.

![Service Fabric-platform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Toegang tot de logboeken van een dood of crashed container
Vanaf v 6.2 kunt u de logboeken ook ophalen voor een Dead of crashed container met behulp van [rest-api's](/rest/api/servicefabric/sfclient-index) of [service Fabric cli-opdrachten (SFCTL)](service-fabric-cli.md) .

### <a name="set-container-retention-policy"></a>Bewaarbeleid voor containers instellen
Om gemakkelijker opstartfouten bij containers te analyseren, ondersteunt Service Fabric (versie 6.1 of hoger) het bewaren van containers die zijn gestopt of niet kunnen starten. Dit beleid kan worden ingesteld in het bestand **ApplicationManifest.xml**, zoals u in het volgende fragment ziet:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

De instelling **ContainersRetentionCount** geeft aan hoeveel containers er moeten worden bewaard wanneer ze fouten genereren. Als er een negatieve waarde wordt opgegeven, worden alle niet goed werkende containers bewaard. Wanneer het kenmerk **ContainersRetentionCount** niet wordt opgegeven, worden er geen containers bewaard. Het kenmerk **ContainersRetentionCount** ondersteunt ook toepassingsparameters, zodat gebruikers verschillende waarden kunnen opgeven voor test- en productieclusters. Bij het gebruik van deze functies dient u plaatsingsbeperkingen in te stellen om de containerservice op een bepaald knooppunt te richten. Zo voorkomt u dat de containerservice naar een ander knooppunt wordt verplaatst. Containers die met behulp van deze functie zijn bewaard, moeten handmatig worden verwijderd.

De instelling **RunInteractive** komt overeen met de behorende en vlaggen van docker `--interactive` `tty` [](https://docs.docker.com/engine/reference/commandline/run/#options). Als deze instelling is ingesteld op True in het manifest bestand, worden deze vlaggen gebruikt om de container te starten.  

### <a name="rest"></a>REST
Gebruik de [container logboeken ophalen die zijn geïmplementeerd op knooppunt](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) bewerking om de logboeken voor een vastgelopen container op te halen. Geef de naam op van het knoop punt waarop de container wordt uitgevoerd, toepassings naam, naam van service manifest en de naam van het code pakket.  Geef `&Previous=true` op. Het antwoord bevat de container logboeken voor de dode container van het code pakket exemplaar.

De URI van de aanvraag heeft de volgende vorm:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Voorbeeld aanvraag:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200-antwoord tekst:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Gebruik de opdracht [sfctl service Get-container-logs](service-fabric-sfctl-service.md) om de logboeken voor een vastgelopen container op te halen.  Geef de naam op van het knoop punt waarop de container wordt uitgevoerd, toepassings naam, naam van service manifest en de naam van het code pakket. Geef de `--previous` vlag op.  Het antwoord bevat de container logboeken voor de dode container van het code pakket exemplaar.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Reactie:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Volgende stappen
- Werk met de [zelf studie een Linux-container toepassing maken](service-fabric-tutorial-create-container-images.md).
- Meer informatie over [service Fabric en containers](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
