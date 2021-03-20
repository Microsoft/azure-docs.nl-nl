---
title: Beveiligde Azure Service Fabric-cluster verbindingen configureren
description: Meer informatie over het gebruik van Visual Studio voor het configureren van beveiligde verbindingen die worden ondersteund door het Azure Service Fabric-cluster.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: d4d6b781d97d481793e69cf2ca97cca5b93ce432
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008528"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Beveiligde verbindingen met een Service Fabric-cluster configureren vanuit Visual Studio
Meer informatie over hoe u Visual Studio kunt gebruiken om veilig toegang te krijgen tot een Azure Service Fabric-cluster met beleids regels voor toegangs beheer die zijn geconfigureerd.

## <a name="cluster-connection-types"></a>Cluster verbindings typen
Er worden twee typen verbindingen ondersteund door het Azure Service Fabric-cluster: **niet-beveiligde** verbindingen en beveiligde verbindingen **op basis van x509-certificaten** . (Voor Service Fabric clusters die on-premises worden gehost, worden ook **Windows** -en **dSTS** -verificaties ondersteund.) U moet het verbindings type van het cluster configureren wanneer het cluster wordt gemaakt. Zodra de app is gemaakt, kan het verbindings type niet worden gewijzigd.

De Visual Studio Service Fabric-hulpprogram ma's ondersteunen alle verificatie typen voor het maken van verbinding met een cluster voor publicatie. Zie [een service Fabric cluster instellen vanuit de Azure Portal](service-fabric-cluster-creation-via-portal.md) voor instructies over het instellen van een beveiligd service Fabric-cluster.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Cluster verbindingen configureren in publicatie profielen
Als u een Service Fabric project publiceert vanuit Visual Studio, gebruikt u het dialoog venster **service Fabric toepassing publiceren** om een Azure service Fabric-cluster te kiezen. Onder **verbindings eindpunt** selecteert u een bestaand cluster onder uw abonnement.

![Het dialoog venster * * Service Fabric toepassing publiceren * * wordt gebruikt om een Service Fabric verbinding te configureren.][publishdialog]

In het dialoog venster **service Fabric toepassing publiceren** wordt de cluster verbinding automatisch gevalideerd. Meld u aan bij uw Azure-account als u hierom wordt gevraagd. Als de validatie is geslaagd, betekent dit dat het systeem de juiste certificaten heeft geïnstalleerd om veilig verbinding te maken met het cluster, of dat uw cluster niet veilig is. Validatie fouten kunnen worden veroorzaakt door netwerk problemen of doordat uw systeem niet op de juiste wijze is geconfigureerd om verbinding te maken met een beveiligd cluster.

![Het dialoog venster * * Service Fabric toepassing publiceren * * valideert een bestaande, correct geconfigureerde Service Fabric cluster verbinding.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Verbinding maken met een beveiligd cluster
1. Zorg ervoor dat u toegang hebt tot een van de client certificaten die door het doel cluster worden vertrouwd. Het certificaat wordt meestal gedeeld als een pfx-bestand (Personal Information Exchange). Zie [een service Fabric cluster instellen van de Azure Portal](service-fabric-cluster-creation-via-portal.md) voor het configureren van de server om toegang te verlenen aan een client.
2. Installeer het vertrouwde certificaat. Om dit te doen, dubbelklikt u op het pfx-bestand of gebruikt u het Power shell-script Import-PfxCertificate om de certificaten te importeren. Installeer het certificaat op **certificaat: \ LocalMachine\My**. Het is OK om alle standaard instellingen te accepteren tijdens het importeren van het certificaat.
3. Kies in het snelmenu van het project de opdracht **publiceren...** om het dialoog venster **publiceren Azure-toepassing** te openen en selecteer vervolgens het doel cluster. Het hulp programma lost de verbinding automatisch op en slaat de para meters voor de beveiligde verbinding op in het publicatie profiel.
4. Optioneel: u kunt het publicatie profiel bewerken om een beveiligde cluster verbinding op te geven.
   
   Omdat u het XML-bestand voor het publicatie profiel hand matig bewerkt om de certificaat gegevens op te geven, noteert u de naam van het certificaat archief, de opslag locatie en de vinger afdruk van het certificaat. U moet deze waarden opgeven voor de archief naam en opslag locatie van het certificaat. Zie [How to: de vinger afdruk van een certificaat ophalen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) voor meer informatie.
   
   U kunt de para meters *ClusterConnectionParameters* gebruiken om de Power shell-para meters op te geven die moeten worden gebruikt om verbinding te maken met het service Fabric cluster. Geldige para meters worden geaccepteerd door de cmdlet Connect-ServiceFabricCluster. Zie [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) voor een lijst met beschik bare para meters.
   
   Als u naar een extern cluster publiceert, moet u de juiste para meters voor dat specifieke cluster opgeven. Hier volgt een voor beeld van het maken van een verbinding met een niet-beveiligd cluster:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Hier volgt een voor beeld voor het maken van een verbinding met een op een x509-certificaat gebaseerd beveiligd cluster:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Bewerk alle andere nood zakelijke instellingen, zoals upgrade parameters en de locatie van het toepassings parameter bestand, en publiceer uw toepassing vanuit het dialoog venster **service Fabric toepassing publiceren** in Visual Studio.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het openen van Service Fabric-clusters [uw cluster visualiseren met behulp van service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
