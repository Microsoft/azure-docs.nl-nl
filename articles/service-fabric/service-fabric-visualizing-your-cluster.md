---
title: Uw cluster visualiseren met Azure Service Fabric Explorer
description: Service Fabric Explorer is een toepassing voor het inspecteren en beheren van Cloud toepassingen en knoop punten in een Microsoft Azure Service Fabric cluster.
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a45aff305f97610cb2660c2e3f4b4427b905d7d4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574052"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualisern van uw cluster met Service Fabric Explorer

Service Fabric Explorer (SFX) is een open source-hulp programma voor het inspecteren en beheren van Azure Service Fabric-clusters. Service Fabric Explorer is een bureaublad toepassing voor Windows, macOS en Linux.

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer downloaden

Gebruik de volgende koppelingen om Service Fabric Explorer te downloaden als een bureaublad toepassing:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> De bureaublad versie van Service Fabric Explorer kan meer of minder functies hebben dan het cluster ondersteunt. U kunt terugvallen op de Service Fabric Explorer-versie die is geïmplementeerd op het cluster om de volledige functie compatibiliteit te garanderen.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Service Fabric Explorer uitvoeren vanuit het cluster

Service Fabric Explorer wordt ook gehost in het HTTP-beheer eindpunt van een Service Fabric cluster. Als u SFX in een webbrowser wilt starten, bladert u naar het HTTP-beheer eindpunt van het cluster vanuit een browser, bijvoorbeeld https://clusterFQDN: 19080.

Voor het installatie programma voor ontwikkel aars kunt u Service Fabric Explorer op uw lokale cluster starten door naar te navigeren https://localhost:19080/Explorer . Bekijk dit artikel om [uw ontwikkel omgeving voor te bereiden](service-fabric-get-started.md).

> [!NOTE]
> Als uw cluster wordt beveiligd met een zelfondertekend certificaat, ontvangt u een fout bericht van de webbrowser ' deze site is niet veilig '. U kunt gewoon door gaan met de meeste moderne webbrowsers door de waarschuwing te vervangen. In een productie omgeving moet uw cluster worden beveiligd met een algemene naam en een certificaat certificerings instantie. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Verbinding maken met een Service Fabric-cluster
Als u verbinding wilt maken met een Service Fabric cluster, hebt u het knoop punt voor cluster beheer (FQDN/IP) en de HTTP-beheer eindpunt poort (standaard 19080) nodig. Bijvoorbeeld https \: //mysfcluster.westus.cloudapp.Azure.com:19080. Gebruik het selectie vakje ' verbinding maken met localhost ' om verbinding te maken met een lokaal cluster op uw werk station.

### <a name="connect-to-a-secure-cluster"></a>Verbinding maken met een beveiligd cluster
U kunt de client toegang tot uw Service Fabric cluster beheren met certificaten of met behulp van Azure Active Directory (AAD).

Als u probeert verbinding te maken met een beveiligd cluster, is afhankelijk van de configuratie van het cluster dat u nodig hebt om een client certificaat aan te bieden of u aan te melden met AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>De indeling van Service Fabric Explorer begrijpen
U kunt door Service Fabric Explorer navigeren met behulp van de structuur aan de linkerkant. In de hoofdmap van de structuur biedt het cluster Dashboard een overzicht van het cluster, inclusief een samen vatting van de status van toepassingen en knoop punten.

![Cluster dashboard Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>De indeling van het cluster weer geven
Knoop punten in een Service Fabric cluster worden geplaatst in een tweedimensionale raster met fout domeinen en upgrade domeinen. Deze plaatsing zorgt ervoor dat uw toepassingen beschikbaar blijven in de aanwezigheid van hardwarefouten en toepassings upgrades. U kunt zien hoe het huidige cluster wordt gevormd door gebruik te maken van de cluster toewijzing.

![Cluster toewijzing Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Toepassingen en services weer geven
Het cluster bevat twee substructuren: één voor toepassingen en een andere voor knoop punten.

U kunt de weer gave toepassing gebruiken om te navigeren door de logische hiërarchie van Service Fabric: toepassingen, services, partities en replica's.

In het onderstaande voor beeld bestaat de toepassing **MyApp** uit twee services: **MyStatefulService** en **webservice**. Aangezien **MyStatefulService** is stateful, bevat het een partitie met één primaire en twee secundaire replica's. WebSvcService is daarentegen stateless en bevat één exemplaar.

![Toepassings weergave Service Fabric Explorer][sfx-application-tree]

Op elk niveau van de structuur bevat het hoofd venster relevante informatie over het item. U kunt bijvoorbeeld de status en versie van de status voor een bepaalde service weer geven.

![Service Fabric Explorer deel venster Essentials][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>De knoop punten van het cluster weer geven
In de knooppuntweergave ziet u de fysieke indeling van het cluster. Voor elk knooppunt kunt u controleren voor welke toepassingen er op het knooppunt code is geïmplementeerd. Meer specifiek kunt u zien welke replica's momenteel worden uitgevoerd.

## <a name="actions"></a>Acties
Service Fabric Explorer biedt een snelle manier om acties op knoop punten, toepassingen en services in uw cluster aan te roepen.

Als u bijvoorbeeld een toepassings exemplaar wilt verwijderen, kiest u de toepassing in de structuur aan de linkerkant en kiest u vervolgens **acties**  >  **toepassing verwijderen**.

![Een toepassing in Service Fabric Explorer verwijderen][sfx-delete-application]

> [!TIP]
> U kunt dezelfde acties uitvoeren door op het weglatings teken naast elk element te klikken.
>
> Elke actie die via Service Fabric Explorer kan worden uitgevoerd, kan ook worden uitgevoerd via Power shell of een REST API om Automation in te scha kelen.
>
>

U kunt ook Service Fabric Explorer gebruiken om toepassings exemplaren te maken voor een bepaald toepassings type en-versie. Kies het toepassings type in de structuur weergave en klik vervolgens op de koppeling **app-exemplaar maken** naast de versie die u wilt weer geven in het rechterdeel venster.

![Een instantie van een toepassing maken in Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer biedt geen ondersteuning voor para meters bij het maken van toepassings exemplaren. Toepassings exemplaren gebruiken standaard parameter waarden.
>
>

## <a name="event-store"></a>Gebeurtenis archief
Event Store is een functie die wordt aangeboden door het platform dat Service Fabric platform gebeurtenissen bevat die beschikbaar zijn in de Service Fabric Explorer en via REST API. Op basis van de tijd van de gebeurtenis ziet u een moment opname van wat er in uw cluster gebeurt voor elke entiteit, bijvoorbeeld knoop punt, service, toepassing en query. Meer informatie over de Event Store vindt u in het [overzicht van Event Store](service-fabric-diagnostics-eventstore.md).   

![Scherm afbeelding toont het deel venster knoop punten waarvoor gebeurtenissen zijn geselecteerd.][sfx-eventstore]

>[!NOTE]
>Vanaf Service Fabric versie 6,4. Event Store is standaard niet ingeschakeld en moet worden ingeschakeld in de Resource Manager-sjabloon

>[!NOTE]
>Vanaf Service Fabric versie 6,4. de Event Store-Api's zijn alleen beschikbaar voor Windows-clusters die alleen op Azure worden uitgevoerd. We werken aan het porteren van deze functionaliteit naar Linux en met de zelfstandige clusters.

## <a name="image-store-viewer"></a>Image Store viewer
Viewer voor afbeeldings archief is een functie die wordt aangeboden als u gebruikmaakt van systeem eigen Image Store waarmee de huidige inhoud van de installatie kopie wordt weer gegeven en informatie over bestanden en mappen kan worden opgehaald, samen met het verwijderen van bestanden/mappen.

![Scherm opname van de Image Store viewer.][sfx-imagestore]

## <a name="backup-and-restore"></a>Back-up en herstel
Service Fabric Explorer biedt de mogelijkheid om een interface te [maken voor back-up en herstel](./service-fabric-reliable-services-backup-restore.md). Als u back-up-en herstel functies wilt zien in SFX, moet geavanceerde modus zijn ingeschakeld.

![Geavanceerde modus inschakelen][0]
 
De volgende bewerkingen zijn mogelijk:

* Een back-upbeleid maken, bewerken en verwijderen.
* Back-ups voor een toepassing, service of partitie inschakelen en uitschakelen.
* Back-ups voor een toepassing, service of partitie opschorten en hervatten.
* Een back-up van een partitie activeren en bijhouden.
* Herstel voor een partitie activeren en bijhouden.

Zie voor meer informatie over de service voor back-up en herstel de [rest API verwijzing](/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Volgende stappen
* [Uw Service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Implementatie van Service Fabric-toepassing met behulp van Power shell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png
