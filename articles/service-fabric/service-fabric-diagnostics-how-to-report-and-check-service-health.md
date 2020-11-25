---
title: Status rapporteren en controleren met Azure Service Fabric
description: Meer informatie over het verzenden van status rapporten uit uw service code en het controleren van de status van uw service met behulp van de status controle hulpprogramma's die door Azure Service Fabric worden geboden.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 59c8202b03bf1be2be5a68b75a1d7c7404b2213d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998240"
---
# <a name="report-and-check-service-health"></a>Servicestatus rapporteren en controleren
Wanneer uw services problemen ondervinden, is het mogelijk om te reageren op incidenten en problemen op te lossen. Als u problemen en storingen in de Azure Service Fabric Health manager in uw service code rapporteert, kunt u gebruikmaken van de standaard hulpprogram ma's voor status controle die Service Fabric biedt om de status te controleren.

Er zijn drie manieren waarop u de status van de service kunt rapporteren:

* Gebruik [partitie](/dotnet/api/system.fabric.istatefulservicepartition) -of [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) -objecten.  
  U kunt de- `Partition` en- `CodePackageActivationContext` objecten gebruiken om de status van elementen te rapporteren die deel uitmaken van de huidige context. Bijvoorbeeld: code die wordt uitgevoerd als onderdeel van een replica kan alleen de status rapporteren voor die replica, de partitie waarvan deze deel uitmaakt en de toepassing waarvan deze deel uitmaakt.
* Gebruik `FabricClient`.   
  U kunt gebruiken `FabricClient` om de status van de service code te rapporteren als het cluster niet is [beveiligd](service-fabric-cluster-security.md) of als de service wordt uitgevoerd met beheerders bevoegdheden. De meeste praktijk scenario's maken geen gebruik van niet-beveiligde clusters of bieden beheerders bevoegdheden. Met `FabricClient` kunt u de status rapporteren voor elke entiteit die deel uitmaakt van het cluster. In het ideale geval moet Service code echter alleen rapporten verzenden die betrekking hebben op de eigen status.
* Gebruik de REST-Api's op de cluster-, toepassings-, geïmplementeerde toepassing, service, service pakket, partitie, replica of knooppunt niveau. Dit kan worden gebruikt voor het rapporteren van de status in een container.

In dit artikel wordt een voor beeld gegeven van de status van de service code. In het voor beeld ziet u ook hoe de hulpprogram ma's van Service Fabric kunnen worden gebruikt om de status te controleren. Dit artikel is bedoeld als een snelle inleiding tot de status bewakings mogelijkheden van Service Fabric. Meer gedetailleerde informatie vindt u in de reeks uitgebreide artikelen over de status die begint met de koppeling aan het einde van dit artikel.

## <a name="prerequisites"></a>Vereisten
U moet het volgende hebben geïnstalleerd:

* Visual Studio 2015 of Visual Studio 2019
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Een lokaal beveiligd ontwikkelaars cluster maken
* Open Power shell met beheerders bevoegdheden en voer de volgende opdrachten uit:

![Opdrachten die laten zien hoe u een beveiligd ontwikkelaars cluster maakt](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Een toepassing implementeren en de status ervan controleren
1. Open Visual Studio als beheerder.
1. Maak een project met behulp van de **stateful service** sjabloon.
   
    ![Een Service Fabric-toepassing maken met stateful service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Druk op **F5** om de toepassing uit te voeren in de foutopsporingsmodus. De toepassing wordt geïmplementeerd naar het lokale cluster.
1. Nadat de toepassing is uitgevoerd, klikt u met de rechter muisknop op het pictogram lokaal Cluster beheer in het systeemvak en selecteert u **lokaal cluster beheren** in het snelmenu om service Fabric Explorer te openen.
   
    ![Service Fabric Explorer openen vanuit systeemvak](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. De status van de toepassing moet worden weer gegeven als in deze afbeelding. Op dit moment moet de toepassing in orde zijn zonder fouten.
   
    ![Goede toepassing in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. U kunt de status ook controleren met behulp van Power shell. U kunt gebruiken ```Get-ServiceFabricApplicationHealth``` om de status van een toepassing te controleren en kunt u gebruiken ```Get-ServiceFabricServiceHealth``` om de status van een service te controleren. Het status rapport voor dezelfde toepassing in Power Shell bevindt zich in deze installatie kopie.
   
    ![Goede toepassing in Power shell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Aangepaste status gebeurtenissen toevoegen aan uw service code
De Service Fabric project sjablonen in Visual Studio bevatten voorbeeld code. De volgende stappen laten zien hoe u aangepaste status gebeurtenissen vanuit uw service code kunt rapporteren. Dergelijke rapporten worden automatisch weer gegeven in de standaard hulpprogramma's voor status controle die Service Fabric biedt, zoals Service Fabric Explorer, Azure Portal status weergave en Power shell.

1. Open de toepassing die u eerder hebt gemaakt in Visual Studio of maak een nieuwe toepassing met behulp van de **stateful service** Visual Studio-sjabloon.
1. Open het Stateful1.cs-bestand en zoek de `myDictionary.TryGetValueAsync` aanroep in de- `RunAsync` methode. U kunt zien dat deze methode retourneert `result` dat de huidige waarde van de teller bevat, omdat de sleutel logica in deze toepassing een telling moet blijven uitvoeren. Als deze toepassing een echte toepassing is, en als het ontbreken van het resultaat een fout veroorzaakt, wilt u die gebeurtenis markeren.
1. Als u een status gebeurtenis wilt rapporteren wanneer het ontbrekende resultaat een fout vertegenwoordigt, voegt u de volgende stappen toe.
   
    a. Voeg de `System.Fabric.Health` naam ruimte toe aan het Stateful1.CS-bestand.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Voeg de volgende code toe na de `myDictionary.TryGetValueAsync` aanroep
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Er wordt een replica status gerapporteerd, omdat deze wordt gemeld vanuit een stateful service. De `HealthInformation` para meter bevat informatie over het status probleem dat wordt gerapporteerd.
   
    Als u een stateless service hebt gemaakt, gebruikt u de volgende code
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Als uw service wordt uitgevoerd met beheerders bevoegdheden of als het cluster niet is [beveiligd](service-fabric-cluster-security.md), kunt u ook gebruiken `FabricClient` om de status te rapporteren, zoals wordt weer gegeven in de volgende stappen.  
   
    a. Maak het `FabricClient` exemplaar na de `var myDictionary` declaratie.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Voeg de volgende code toe na de `myDictionary.TryGetValueAsync` aanroep.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Laten we deze fout simuleren en weer geven in de hulpprogram ma's voor status controle. Als u de fout wilt simuleren, maakt u een opmerking bij de eerste regel in de status rapportage code die u eerder hebt toegevoegd. Nadat u een opmerking bij de eerste regel hebt opgesteld, ziet de code eruit als in het volgende voor beeld.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Met deze code wordt het status rapport geactiveerd elke keer dat wordt `RunAsync` uitgevoerd. Nadat u de wijziging hebt aangebracht, drukt u op **F5** om de toepassing uit te voeren.
1. Nadat de toepassing is uitgevoerd, opent u Service Fabric Explorer om de status van de toepassing te controleren. Deze keer ziet Service Fabric Explorer dat de toepassing een slechte status heeft. De toepassing wordt weer gegeven als beschadigd omdat de fout is gerapporteerd uit de code die we eerder hebben toegevoegd.
   
    ![Beschadigde toepassing in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Als u de primaire replica in de structuur weergave van Service Fabric Explorer selecteert, ziet u dat de **status** ervan een fout aangeeft. Service Fabric Explorer kunt ook de status rapport gegevens weer geven die zijn toegevoegd aan de `HealthInformation` para meter in de code. U kunt dezelfde status rapporten bekijken in Power shell en het Azure Portal.
   
    ![Replica status in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Dit rapport blijft in de Health Manager totdat het wordt vervangen door een ander rapport of totdat de replica wordt verwijderd. Omdat we `TimeToLive` Dit status rapport niet in het object hebben ingesteld `HealthInformation` , verloopt het rapport nooit.

We raden aan dat de status wordt gerapporteerd op het meest gedetailleerde niveau, in dit geval de replica. U kunt ook de status rapporteren op `Partition` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Als u de status wilt rapporteren over `Application` , `DeployedApplication` en, gebruikt u `DeployedServicePackage`  `CodePackageActivationContext` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Volgende stappen
* [Diep gaande over Service Fabric status](service-fabric-health-introduction.md)
* [REST API voor Reporting service Health](/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API voor de rapportage van de status van de toepassing](/rest/api/servicefabric/report-the-health-of-an-application)
