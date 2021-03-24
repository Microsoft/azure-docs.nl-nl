---
title: Azure Event Grid Sdk's
description: Hierin worden de Sdk's voor Azure Event Grid beschreven. Deze Sdk's bieden beheer, publicatie en verbruik.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: aa53aaa89a703bb88df6611cdb0dd38341248a55
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955921"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid Sdk's voor beheer en publicatie

Event Grid biedt Sdk's waarmee u uw resources programmatisch kunt beheren en gebeurtenissen kunt plaatsen.

## <a name="management-sdks"></a>Management-Sdk's

Met de Sdk's voor beheer kunt u Event grid-onderwerpen en-abonnementen maken, bijwerken en verwijderen. Momenteel zijn de volgende Sdk's beschikbaar:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Knooppunt](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Gegevens vlak Sdk's

Met de Sdk's voor gegevens vlakken kunt u gebeurtenissen naar onderwerpen posten door te verifiëren, de gebeurtenis te vormen en asynchroon te boeken naar het opgegeven eind punt. U kunt er ook voor zorgen dat u gebeurtenissen van de eerste partij gebruikt. Momenteel zijn de volgende Sdk's beschikbaar:

| Programmeertaal | SDK | 
| -------------------- | ---------- | 
| .NET | Stabiele SDK: [micro soft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>Preview-SDK: [Azure. Messa ging. EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | Stabiele SDK: [Azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>Preview-SDK: [Azure-Messa ging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [Azure-eventgrid](https://pypi.org/project/azure-eventgrid/#history) (Zie de laatste stabiele en voorlopige versies op de pagina **release geschiedenis** ) |
| Javascript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) (Schakel over naar het tabblad **versies** om de nieuwste stabiele en bèta versie pakketten te bekijken). | 
| Go | [Azure SDK voor Go](https://github.com/Azure/azure-sdk-for-go) |
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) |


## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid code voorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid)voor voorbeeld toepassingen.
* Zie [Wat is Event grid?](overview.md) voor een inleiding tot Event grid.
* Zie [Azure cli](/cli/azure/eventgrid)voor Event grid-opdrachten in azure cli.
* Zie voor Event Grid-opdrachten in Power shell [Power shell](/powershell/module/az.eventgrid).
