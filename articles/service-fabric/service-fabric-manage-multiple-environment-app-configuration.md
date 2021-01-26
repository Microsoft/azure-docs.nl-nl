---
title: Apps voor meerdere omgevingen beheren
description: Azure Service Fabric-toepassingen kunnen worden uitgevoerd op clusters die omvangen van één computer tot duizenden computers. In sommige gevallen moet u uw toepassing anders configureren voor deze uiteenlopende omgevingen. In dit artikel wordt beschreven hoe u verschillende toepassings parameters per omgeving definieert.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: c907540c03788ab5f4087a96e301f18ab7ced4ca
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787974"
---
# <a name="manage-applications-for-multiple-environments"></a>Toepassingen voor meerdere omgevingen beheren

Met Azure Service Fabric-clusters kunt u clusters maken met behulp van een tot veel duizenden computers. In de meeste gevallen kunt u uw toepassing ook implementeren in meerdere cluster configuraties: uw lokale ontwikkel cluster, een gedeeld ontwikkelings cluster en uw productie cluster. Al deze clusters worden beschouwd als verschillende omgevingen waarvoor uw code moet worden uitgevoerd. Binaire toepassings bestanden kunnen zonder aanpassing worden uitgevoerd voor dit brede spectrum, maar u wilt de toepassing meestal op een andere manier configureren.

Overweeg twee eenvoudige voor beelden:
  - uw service luistert op een gedefinieerde poort, maar u hebt die poort nodig om in de omgevingen te verschillen
  - u moet verschillende bindings referenties opgeven voor een data base in de omgevingen

## <a name="specifying-configuration"></a>Configuratie opgeven

De configuratie die u opgeeft, kan worden onderverdeeld in twee categorieën:

- Configuratie die van toepassing is op hoe uw services worden uitgevoerd
  - Bijvoorbeeld het poort nummer voor een eind punt of het aantal exemplaren van een service
  - Deze configuratie is opgegeven in het manifest bestand van de toepassing of service
- Configuratie die van toepassing is op uw toepassings code
  - Bijvoorbeeld bindings gegevens voor een Data Base
  - Deze configuratie kan worden gegeven via configuratie bestanden of omgevings variabelen

> [!NOTE]
> Niet alle kenmerken in de toepassing en het service manifest bestand ondersteunen para meters.
> In die gevallen moet u vertrouwen op het vervangen van teken reeksen als onderdeel van de implementatie werk stroom. In azure DevOps kunt u een uitbrei ding gebruiken zoals tokens vervangen: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens of in Jenkins u een script taak zou kunnen uitvoeren om de waarden te vervangen.
>

## <a name="specifying-parameters-during-application-creation"></a>Para meters opgeven tijdens het maken van een toepassing

Wanneer u een benoemde instantie van een toepassing maakt in Service Fabric, kunt u de para meters door geven. Hoe u dit doet, hangt af van de manier waarop u het toepassings exemplaar maakt.

  - In Power shell [`New-ServiceFabricApplication`](/powershell/module/servicefabric/new-servicefabricapplication) neemt de cmdlet de toepassings parameters op als een hash.
  - Met behulp van sfctl [`sfctl application create`](./service-fabric-sfctl-application.md#sfctl-application-create) krijgt de opdracht para meters als een JSON-teken reeks. Het install.sh-script maakt gebruik van sfctl.
  - Visual Studio biedt een set parameter bestanden in de map para meters in het toepassings project. Deze parameter bestanden worden gebruikt bij het publiceren vanuit Visual Studio met behulp van Azure DevOps Services of Azure DevOps Server. In Visual Studio worden de parameter bestanden door gegeven aan het Deploy-FabricApplication.ps1 script.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen ziet u hoe u een aantal van de concepten kunt gebruiken die hier worden beschreven:

- [Omgevings variabelen opgeven voor services in Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Het poort nummer van een service opgeven met behulp van para meters in Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Configuratie bestanden para meters](service-fabric-how-to-parameterize-configuration-files.md)

- [Naslag informatie over omgevings variabelen](service-fabric-environment-variables-reference.md)
