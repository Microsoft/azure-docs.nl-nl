---
title: Uw Azure Service Fabric-Services container plaatsen in Windows
description: Meer informatie over het container plaatsen van uw Service Fabric Reliable Services en Reliable Actors Services in Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e0c5f3ecf0334ad2190d8542e54388a2a25d30fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999804"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Uw Reliable Services en Reliable Actors van Service Fabric in Windows in een container plaatsen

Service Fabric ondersteunt waarmee Service Fabric micro Services (Reliable Services en reliable actor-Services). Zie [service Fabric-containers](service-fabric-containers-overview.md)voor meer informatie.

In dit document vindt u informatie over het uitvoeren van uw service in een Windows-container.

> [!NOTE]
> Deze functie werkt momenteel alleen voor Windows. Om containers uit te voeren, moet het cluster worden uitgevoerd op Windows Server 2016 met containers.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Stappen voor het container plaatsen van uw Service Fabric-toepassing

1. Open uw Service Fabric-toepassing in Visual Studio.

2. Voeg Class [SFBinaryLoader. cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) toe aan uw project. De code in deze klasse is een helper om de binaire bestanden van Service Fabric runtime in uw toepassing correct te laden wanneer ze binnen een container worden uitgevoerd.

3. Voor elk code pakket dat u wilt container plaatsen, initialiseert u het laad programma op het toegangs punt van het programma. Voeg de statische constructor die wordt weer gegeven in het volgende code fragment toe aan het bestand van het programma-toegangs punt.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Bouw en [verpak](service-fabric-package-apps.md#Package-App) uw project. Als u een pakket wilt bouwen en maken, klikt u met de rechter muisknop op het toepassings project in Solution Explorer en kiest u de **pakket** opdracht.

5. Voer voor elk code pakket dat u nodig hebt container plaatsen het Power shell-script [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1)uit. Het gebruik is als volgt:

    Volledige .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Met het script maakt u een map met docker-artefacten op $dockerPackageOutputDirectoryPath. Wijzig de gegenereerde Dockerfile in `expose` poorten, Voer installatie scripts uit, enzovoort. op basis van uw behoeften.

6. Vervolgens moet u uw docker-container pakket [bouwen](service-fabric-get-started-containers.md#Build-Containers) en [pushen](service-fabric-get-started-containers.md#Push-Containers) naar uw opslag plaats.

7. Wijzig de ApplicationManifest.xml en ServiceManifest.xml om uw container installatie kopie, gegevens opslag plaats, register verificatie en poort-naar-host-toewijzing toe te voegen. Zie [een Azure service Fabric-container toepassing maken](service-fabric-get-started-containers.md)voor het wijzigen van de manifesten. De code pakket definitie in het service manifest moet worden vervangen door de bijbehorende container installatie kopie. Zorg ervoor dat u het toegangs punt wijzigt in een ContainerHost-type.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Voeg de poort-naar-host-toewijzing voor uw Replicator en service-eind punt toe. Omdat deze poorten zijn toegewezen tijdens runtime door Service Fabric, wordt ContainerPort ingesteld op nul om de toegewezen poort voor toewijzing te gebruiken.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Zie [isolatie modus configureren]( ./service-fabric-get-started-containers.md#configure-isolation-mode)voor meer informatie over het configureren van de isolatie modus voor containers. Windows ondersteunt twee isolatiemodi voor containers: proces en Hyper-V. De volgende fragmenten laten zien hoe de isolatie modus is opgegeven in het manifest bestand van de toepassing.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Standaard hebben Service Fabric-toepassingen toegang tot de Service Fabric runtime, in de vorm van een eind punt dat toepassingsspecifieke aanvragen accepteert. Overweeg deze toegang uit te scha kelen wanneer de toepassing niet-vertrouwde code host. Zie [Aanbevolen procedures voor beveiliging in service Fabric](service-fabric-best-practices-security.md#platform-isolation)voor meer informatie. Als u de toegang tot de Service Fabric runtime wilt uitschakelen, voegt u de volgende instelling toe in de sectie beleid van het toepassings manifest dat overeenkomt met het geïmporteerde service manifest, als volgt:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Als u deze toepassing wilt testen, moet u deze implementeren in een cluster waarop versie 5,7 of hoger wordt uitgevoerd. Voor runtime versie 6,1 of lager moet u de cluster instellingen bewerken en bijwerken om deze preview-functie in te scha kelen. Volg de stappen in dit [artikel](service-fabric-cluster-fabric-settings.md) om de instelling toe te voegen die hierna wordt weer gegeven.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. [Implementeer](service-fabric-deploy-remove-applications.md) daarna het bewerkte toepassings pakket naar dit cluster.

U hebt nu een container Service Fabric toepassing die uw cluster uitvoert.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het uitvoeren van [containers in Service Fabric](service-fabric-get-started-containers.md).
* Meer informatie over de [levenscyclus](service-fabric-application-lifecycle.md) van de Service Fabric-toepassing.