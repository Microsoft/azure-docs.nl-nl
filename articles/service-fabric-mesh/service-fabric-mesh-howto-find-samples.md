---
title: Azure Service Fabric mesh-voor beelden zoeken
description: Hier volgt een index van beschik bare Service Fabric mesh-voorbeeld toepassingen. De broncode in die voorbeelden laten zien hoe u een bepaald scenario kunt uitvoeren met behulp van het Service Fabric-resourcemodel.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 2b38cd6a6c0f3aaab4ff5b77be82aee242afef23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627025"
---
# <a name="find-service-fabric-mesh-samples"></a>Service Fabric Mesh-voorbeelden zoeken

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Deze tabel biedt een overzicht van de beschikbare Service Fabric Mesh-voorbeeldtoepassingen. De broncode in die voorbeelden laten zien hoe u een bepaald scenario kunt uitvoeren met behulp van het Service Fabric-resourcemodel.

Zie de [GitHub-pagina met voorbeeldsjablonen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md) voor meer informatie over het direct implementeren van sjablonen naar Azure.

|Voorbeeldsjabloon|Scenariobeschrijving|Broncode|Ontwikkelhulpprogramma's|
|------------|--------------------|----------|----------------------|
| [Hallo wereld-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statische webpagina die wordt gehost in een container. Voor Linux wordt nginx gebruikt, voor Windows wordt IIS gebruikt. | [Broncode](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Geen vereisten |
| [Teller-app voor Azure Files-volumes](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Sla de status op door een koppeling aan te brengen met op Azure Files gebaseerde volumes in de container. <br><br> **Opmerking:** voor deze sjabloon is vereist dat er al een Azure Files-bestandsshare is ingericht. Zie hiervoor de [instructies](../storage/files/storage-how-to-create-file-share.md). | [Broncode](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Maak een toepassing met een front-end- en back-endservice die gebruikmaken van DNS-omzetting. Als zelfstudie [hier](./service-fabric-mesh-tutorial-create-dotnetcore.md) te vinden. | [Broncode](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh Tooling |
| [Visuele objecten-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Microservices upgraden en de schaal ervan in een toepassing aanpassen. | [Broncode](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh Tooling |
| [Stem-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Maak een toepassing met een front-end- en back-endservice die gebruikmaken van DNS-omzetting. | [Broncode](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio net-Hulpprogram Ma's voor de Windows-versie VS code/DotNet CLI kunnen worden gebruikt voor de Linux-versie |
| [Teller-app voor betrouwbare Service Fabric-volumes](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Sla de status op door een koppeling aan te brengen met op Service Fabric Reliable Disk gebaseerde volumes in de container.| [Broncode](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
