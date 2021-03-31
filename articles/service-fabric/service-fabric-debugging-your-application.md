---
title: Fouten opsporen in uw toepassing in Visual Studio
description: Verbeter de betrouw baarheid en prestaties van uw services door deze te ontwikkelen en fouten op te sporen in Visual Studio op een lokaal ontwikkelings cluster.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b7d08d610c883240abedc66c55abba64a74c8e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576312"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Foutopsporing uitvoeren in uw Service Fabric-toepassing met behulp van Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio-CSharp](service-fabric-debugging-your-application.md) 
> * [Eclips/java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Fouten opsporen in een lokale Service Fabric-toepassing
U kunt tijd en geld besparen door uw Azure Service Fabric-toepassing te implementeren en op te sporen in een cluster voor lokale computer ontwikkeling. Visual Studio 2019 of 2015 kan de toepassing implementeren in het lokale cluster en de Debugger automatisch verbinden met alle exemplaren van uw toepassing. Visual Studio moet als beheerder worden uitgevoerd om verbinding te maken met het fout opsporingsprogramma.

1. Start een lokaal ontwikkel cluster door de stappen in [uw service Fabric-ontwikkel omgeving](service-fabric-get-started.md)in te stellen.
2. Druk op **F5** of klik op **fout**  >  **opsporing starten**.
   
    ![Scherm opname van het menu fout opsporing.][startdebugging]
3. Stel onderbrekings punten in uw code in en door loop de toepassing door te klikken op opdrachten in het menu **fout opsporing** .
   
   > [!NOTE]
   > Visual Studio wordt gekoppeld aan alle exemplaren van uw toepassing. Terwijl u code stapsgewijs doorloopt, kunnen onderbrekings punten worden bereikt door meerdere processen die in gelijktijdige sessies worden gegenereerd. Probeer de onderbrekings punten uit te scha kelen nadat deze zijn bereikt, door elk onderbrekings punt voor de thread-ID of door diagnostische gebeurtenissen te gebruiken.
   > 
   > 
4. Het venster **diagnostische gebeurtenissen** wordt automatisch geopend, zodat u in realtime diagnostische gebeurtenissen kunt weer geven.
   
    ![Diagnostische gebeurtenissen in realtime weer geven][diagnosticevents]
5. U kunt ook het venster **diagnostische gebeurtenissen** openen in Cloud Explorer.  Klik onder **service Fabric** met de rechter muisknop op een knoop punt en kies **streaming-traceringen weer geven**.
   
    ![Het venster diagnostische gebeurtenissen openen][viewdiagnosticevents]
   
    Als u uw traceringen wilt filteren op een bepaalde service of toepassing, schakelt u streaming traceringen in op die specifieke service of toepassing.
6. De diagnostische gebeurtenissen kunnen worden weer gegeven in het automatisch gegenereerde bestand **ServiceEventSource. cs** en worden aangeroepen vanuit toepassings code.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Het venster **diagnostische gebeurtenissen** ondersteunt het filteren, onderbreken en inspecteren van gebeurtenissen in realtime.  Het filter is een eenvoudige teken reeks zoekactie van het gebeurtenis bericht, inclusief de inhoud ervan.
   
    ![Gebeurtenissen in realtime filteren, onderbreken en hervatten of controleren][diagnosticeventsactions]
8. Fout opsporing van Services is vergelijkbaar met het opsporen van fouten in andere toepassingen. Normaal gesp roken stelt u onderbrekings punten in met Visual Studio voor eenvoudige fout opsporing. Hoewel betrouw bare verzamelingen worden gerepliceerd op meerdere knoop punten, implementeren ze nog steeds IEnumerable. Deze implementatie houdt in dat u de resultaten weergave in Visual Studio kunt gebruiken terwijl u fouten opspoort om te zien wat u hebt opgeslagen in. U doet dit door een onderbrekings punt in uw code in te stellen.
   
    ![Fout opsporing in een toepassing starten][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Een script uitvoeren als onderdeel van fout opsporing
In bepaalde scenario's moet u mogelijk een script uitvoeren als onderdeel van het starten van een foutopsporingssessie (bijvoorbeeld wanneer geen standaard services worden gebruikt).

In Visual Studio kunt u een bestand met de naam **Start-Service.ps1** toevoegen in de map **Scripts** van het service Fabric toepassings project (. sfproj). Dit script wordt aangeroepen nadat de toepassing is gemaakt in het lokale cluster.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Fouten opsporen in een toepassing voor externe Service Fabric
Als uw Service Fabric toepassingen worden uitgevoerd op een Service Fabric cluster in azure, kunt u deze toepassingen rechtstreeks vanuit Visual Studio op afstand opsporen.

> [!NOTE]
> Voor de functie is [service Fabric SDK 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) en [Azure sdk voor .net 2,9](https://azure.microsoft.com/downloads/)vereist.    

<!-- -->
> [!WARNING]
> Externe fout opsporing is bedoeld voor ontwikkel-en test scenario's en niet voor gebruik in productie omgevingen, vanwege de invloed op de actieve toepassingen.

1. Navigeer naar uw cluster in **Cloud Explorer**. Klik met de rechter muisknop en kies **fout opsporing inschakelen**
   
    ![Fout opsporing op afstand inschakelen][enableremotedebugging]
   
    Met deze actie wordt het proces van het inschakelen van de uitbrei ding voor externe fout opsporing op uw cluster knooppunten en de vereiste netwerk configuraties gestart.
2. Klik met de rechter muisknop op het cluster knooppunt in **Cloud Explorer** en kies **fout opsporing koppelen**
   
    ![Fout opsporingsprogramma koppelen][attachdebugger]
3. Kies in het dialoog venster **aan proces koppelen** het proces dat u wilt opsporen en klik op **koppelen**
   
    ![Proces kiezen][chooseprocess]
   
    De naam van het proces dat u wilt koppelen, is gelijk aan de naam van de naam van uw service project-assembly.
   
    Het fout opsporingsprogramma wordt gekoppeld aan alle knoop punten waarop het proces wordt uitgevoerd.
   
   * Als u fouten opspoort voor een stateless service, maken alle exemplaren van de service op alle knoop punten deel uit van de foutopsporingssessie.
   * Als u fouten opspoort voor een stateful service, wordt alleen de primaire replica van een partitie actief en daarom onderschept door de debugger. Als de primaire replica tijdens de foutopsporingssessie wordt verplaatst, blijft de verwerking van die replica deel uitmaken van de foutopsporingssessie.
   * Als u alleen relevante partities of exemplaren van een bepaalde service wilt inventariseren, kunt u voorwaardelijke onderbrekings punten gebruiken om alleen een bepaalde partitie of een specifiek exemplaar te kraken.
     
     ![Voorwaardelijk onderbrekings punt][conditionalbreakpoint]
     
     > [!NOTE]
     > Op dit moment wordt er geen ondersteuning geboden voor het opsporen van fouten in een Service Fabric cluster met meerdere exemplaren van dezelfde naam voor het uitvoer bare bestand.
     > 
     > 
4. Wanneer u de fout opsporing van uw toepassing hebt voltooid, kunt u de uitbrei ding voor externe fout opsporing uitschakelen door met de rechter muisknop op het cluster in **Cloud Explorer** te klikken en **fout opsporing uitschakelen** te kiezen
   
    ![Fout opsporing op afstand uitschakelen][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streaming traceringen van een extern cluster knooppunt
U kunt ook traceringen rechtstreeks vanuit een extern cluster knooppunt streamen naar Visual Studio. Met deze functie kunt u ETW-tracerings gebeurtenissen streamen die zijn geproduceerd op een Service Fabric cluster knooppunt.

> [!NOTE]
> Voor deze functie is [service Fabric SDK 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) en [Azure sdk voor .net 2,9](https://azure.microsoft.com/downloads/)vereist.
> Deze functie biedt alleen ondersteuning voor clusters die worden uitgevoerd in Azure.
> 
> 

<!-- -->
> [!WARNING]
> Streaming-traceringen zijn bedoeld voor ontwikkel-en test scenario's en kunnen niet worden gebruikt in productie omgevingen, vanwege de invloed op de actieve toepassingen.
> In een productie scenario moet u vertrouwen op het door sturen van gebeurtenissen met behulp van Azure Diagnostics.

1. Navigeer naar uw cluster in **Cloud Explorer**. Klik met de rechter muisknop en kies **streaming-traceringen inschakelen**
   
    ![Externe streaming-traceringen inschakelen][enablestreamingtraces]
   
    Met deze actie wordt het proces van het inschakelen van de uitbrei ding streaming-traceringen op uw cluster knooppunten en de vereiste netwerk configuraties gestart.
2. Vouw het element **knoop punten** in **Cloud Explorer** uit, klik met de rechter muisknop op het knoop punt waarnaar u traceringen wilt streamen en kies **streaming-traceringen weer geven**
   
    ![Externe streaming-traceringen weer geven][viewremotestreamingtraces]
   
    Herhaal stap 2 voor alle knoop punten waarnaar u traceringen wilt zien. Elke knoop punten stroom wordt weer gegeven in een toegewezen venster.
   
    U kunt nu de traceringen zien die worden verzonden door Service Fabric, en uw services. Als u de gebeurtenissen wilt filteren zodat alleen een bepaalde toepassing wordt weer gegeven, typt u de naam van de toepassing in het filter.
   
    ![Streaming-traceringen weer geven][viewingstreamingtraces]
3. Zodra u het streamen van traceringen vanuit uw cluster hebt uitgevoerd, kunt u externe streaming traces uitschakelen door met de rechter muisknop op het cluster in **Cloud Explorer** te klikken en **streaming-traceringen uitschakelen** te kiezen.
   
    ![Traceringen voor externe streaming uitschakelen][disablestreamingtraces]

## <a name="next-steps"></a>Volgende stappen
* [Een service Fabric-service testen](service-fabric-testability-overview.md).
* [Uw service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
