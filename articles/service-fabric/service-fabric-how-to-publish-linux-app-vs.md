---
title: A.Net core-app maken en publiceren naar een extern Linux-cluster
description: .Net Core-Apps maken en publiceren die gericht zijn op een extern Linux-cluster vanuit Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614346"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Visual Studio gebruiken om .net core-toepassingen te maken en te publiceren die gericht zijn op een extern Linux Service Fabric-cluster
Met Visual Studio-hulp middelen kunt u Service Fabric .net core-toepassingen ontwikkelen en publiceren die gericht zijn op een Linux Service Fabric-cluster. De SDK-versie moet 3,4 of hoger zijn om een .net core-toepassing te implementeren die is gericht op Linux Service Fabric-clusters vanuit Visual Studio.

> [!Note]
> Visual Studio biedt geen ondersteuning voor fout opsporing Service Fabric toepassingen die gericht zijn op Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Een Service Fabric-toepassing maken die gericht is op .net core
1. Start Visual Studio als **beheerder**.
2. Maak een project met **bestand->New->-project**.
3. Kies in het dialoog venster **Nieuw project** de optie **Cloud-> service Fabric toepassing**.
![maken-toepassing]
4. Geef de toepassing een naam en klik op **OK**.
5. Selecteer op de pagina **nieuwe service Fabric-service** het type service dat u wilt maken in de **sectie .net core**.
![Create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Implementeren in een extern Linux-cluster
1. Klik in de Solution Explorer met de rechter muisknop op de toepassing en selecteer **Build**.
![Build-toepassing]
2. Zodra het bouw proces voor de toepassing is voltooid, klikt u met de rechter muisknop op de service en selecteert u het **bestand csproj** bewerken.
![Edit-csproj]
3. Bewerk de eigenschap UpdateServiceFabricManifestEnabled van waar in **Onwaar** als de service een **actor-project type** is. Als uw toepassing geen actor service heeft, gaat u verder met stap 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Als UpdateServiceFabricManifestEnabled is ingesteld op False, worden updates voor de ServiceManifest.xml tijdens het bouwen uitgeschakeld. Wijzigingen, zoals toevoegen, verwijderen of wijzigen van de naam van de service, worden niet weer gegeven in de ServiceManifest.xml. Als er wijzigingen worden aangebracht, moet u de ServiceManifest hand matig bijwerken of UpdateServiceFabricManifestEnabled instellen op True en de service bouwen die de ServiceManifest.xml bijwerkt en deze vervolgens weer herstellen naar onwaar.
>

4. Werk de RuntimeIndetifier van Win7-x64 bij naar het doel platform in het service project.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. Werk in het ServiceManifest het entry point-programma bij om. exe te verwijderen. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. Klik in Solution Explorer met de rechter muisknop op de toepassing en selecteer **publiceren**. Het dialoogvenster **Publiceren** wordt weergegeven.
7. Selecteer in **verbindings eindpunt** het eind punt voor het externe service Fabric Linux-cluster dat u wilt instellen als doel.
![publiceren-toepassing]

<!--Image references-->
[maken-toepassing]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[Create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[Build-toepassing]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[Edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publiceren-toepassing]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [aan de slag met Service fabric met .net core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
