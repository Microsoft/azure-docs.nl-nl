---
title: Azure Service Fabric mesh-toepassings geheimen opslaan en gebruiken
description: Service Fabric Mesh biedt ondersteuning voor geheimen als Azure-resources. Hier vindt u informatie over het opslaan en beheren van geheimen met uw Service Fabric-mesh-toepassingen.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625529"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-toepassingsgeheimen

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Service Fabric Mesh biedt ondersteuning voor geheimen als Azure-resources. Een Service Fabric Mesh-geheim kan bestaan uit gevoelige informatie in tekstvorm, zoals opslagverbindingsreeksen, wachtwoorden of andere waarden die veilig moeten worden opgeslagen en verzonden.

![Overzicht van Mesh-geheimen][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh-geheimenresources
Een Mesh-toepassingsgeheim bestaat uit:
* Een **Geheimen**-resource. Dit is een container die geheimen in tekstvorm bevat. Geheimen die zijn opgenomen in de **Geheimen**-resource, worden veilig opgeslagen en verzonden.
* Een of meer **Geheimen/waarden**-resources die zijn opgeslagen in de **Geheimen**-resourcecontainer. Elke **Geheimen/waarden**-resource wordt gekenmerkt door een uniek versienummer.

## <a name="next-steps"></a>Volgende stappen 
Voor meer informatie over Service Fabric Mesh-geheimen zie:
- [Service Fabric Mesh-toepassingsgeheimen beheren](service-fabric-mesh-howto-manage-secrets.md)
- [Inleiding tot het Service Fabric-resourcemodel](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
