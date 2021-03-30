---
title: Installatie kopie Archief van Azure Service Fabric connection string
description: Meer informatie over de connection string voor het opslaan van afbeeldingen, inclusief het gebruik en de toepassingen naar een Service Fabric cluster.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 8fc0239dd18fc7071823a129a7dbc4f102023d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86246194"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Meer informatie over de instelling ImageStoreConnectionString

In sommige documentatie wordt het bestaan van een ' ImageStoreConnectionString-para meter in het kort vermeld, zonder te beschrijven wat het echt betekent. En nadat u een artikel hebt door lopen zoals het [implementeren en verwijderen van toepassingen met behulp van Power shell][10], lijkt het alsof het kopiëren/plakken van de waarde zoals wordt weer gegeven in het cluster manifest van het doel cluster. De instelling moet dus per cluster kunnen worden geconfigureerd, maar wanneer u een cluster maakt via de [Azure Portal][11], is er geen optie om deze instelling te configureren en is het altijd ' Fabric: installatie kopie opslag '. Wat is het doel van deze instelling?

![Cluster manifest][img_cm]

Service Fabric is gestart als een platform voor intern micro soft-gebruik door veel verschillende teams, waardoor sommige aspecten van IT zeer aanpasbaar zijn: de ' Image Store ' is een van de voor beelden. Het Image Store is in wezen een pluggable opslag plaats voor het opslaan van toepassings pakketten. Wanneer uw toepassing wordt geïmplementeerd naar een knoop punt in het cluster, wordt de inhoud van het toepassings pakket door het knoop punt gedownload van de Image Store. De ImageStoreConnectionString is een instelling die alle benodigde informatie voor clients en knoop punten bevat om de juiste Image Store voor een bepaald cluster te vinden.

Er zijn momenteel drie mogelijke soorten Image Store providers en de bijbehorende verbindings reeksen:

1. Image Store-service: ' Fabric: installatie kopie opslag '

2. Bestands systeem: ' bestand: [bestandssysteempad] '

3. Azure Storage: "xstore: DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Container = [...]

Het provider type dat in productie wordt gebruikt, is de Image Store-service. Dit is een stateful persistente systeem service die u vanuit Service Fabric Explorer kunt zien. 

![Image Store-service][img_is]

Als host fungeert voor de Image Store in een systeem service binnen het cluster zelf, worden externe afhankelijkheden voor de pakket opslagplaats geëlimineerd en hebt u meer controle over de lokale opslag ruimte. Toekomstige verbeteringen rond de Image Store zijn waarschijnlijk eerst gericht op de Image Store provider, indien niet alleen. De connection string voor de Image Store-service provider heeft geen unieke informatie omdat de client al is verbonden met het doel cluster. De client hoeft alleen te weten dat de protocollen die zijn gericht op de systeem service moeten worden gebruikt.

De bestandssysteem provider wordt gebruikt in plaats van de Image Store-service voor lokale clusters met één vak tijdens de ontwikkeling om het cluster iets sneller te Boots trappen. Het verschil is doorgaans klein, maar het is een handige Optima Lise ring voor de meeste mensen tijdens de ontwikkeling. Het is ook mogelijk om een lokaal cluster met één doos met de andere typen opslag providers te implementeren, maar er is meestal geen reden om dit te doen omdat de werk stroom ontwikkelen/testen hetzelfde blijft, ongeacht de provider. De Azure Storage provider bestaat alleen voor oudere ondersteuning van oude clusters die zijn geïmplementeerd voordat de Image Store-service provider werd geïntroduceerd.

Daarnaast moet de provider van het bestands systeem of de Azure Storage niet worden gebruikt als methode voor het delen van een Image Store tussen meerdere clusters. Dit leidt ertoe dat de cluster configuratie gegevens beschadigd raken, omdat elk cluster conflicterende gegevens naar de Image Store kan schrijven. Als u de ingerichte toepassings pakketten tussen meerdere clusters wilt delen, gebruikt u [sfpkg][12] -bestanden in plaats daarvan, die kunnen worden geüpload naar een externe opslag met een download-URI.

Dus wanneer de ImageStoreConnectionString is geconfigureerd, gebruikt u gewoon de standaard instelling. Wanneer u naar Azure publiceert via Visual Studio, wordt de para meter automatisch voor u ingesteld. Voor een programmatische implementatie naar clusters die worden gehost in azure, is het connection string altijd ' Fabric: installatie kopie opslag '. Hoewel de waarde ervan onzeker is, kan deze altijd worden geverifieerd door het cluster manifest op te halen door [Power shell](/powershell/module/servicefabric/get-servicefabricclustermanifest), [.net](/previous-versions/azure/reference/mt161375(v=azure.100))of [rest](/rest/api/servicefabric/get-a-cluster-manifest). Zowel on-premises test-als productie clusters moeten altijd worden geconfigureerd voor het gebruik van de Image Store-service provider.

### <a name="next-steps"></a>Volgende stappen
[Toepassingen implementeren en verwijderen met behulp van Power shell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
