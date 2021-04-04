---
title: Zelf studie voor upgrade van app Service Fabric
description: In dit artikel wordt uitgelegd hoe u een Service Fabric toepassing implementeert, hoe u de code wijzigt en een upgrade uitvoert met behulp van Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: acde2f4e51bee29d2eefb0d5fbb54fbe421a41f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996233"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Zelf studie voor de upgrade van toepassingen Service Fabric met Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric vereenvoudigt het proces van het upgraden van Cloud toepassingen door ervoor te zorgen dat alleen gewijzigde services worden bijgewerkt, en dat de status van de toepassing gedurende het upgrade proces wordt bewaakt. Er wordt ook automatisch een back-up van de toepassing naar de vorige versie hersteld wanneer er problemen optreden. Service Fabric toepassings upgrades zijn geen *uitval tijd*, omdat de toepassing zonder downtime kan worden bijgewerkt. In deze zelf studie wordt beschreven hoe u een rolling upgrade van Visual Studio kunt volt ooien.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Stap 1: het voor beeld van Visual Objects maken en publiceren
Down load eerst de toepassing [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) van github. Vervolgens bouwt en publiceert u de toepassing door met de rechter muisknop te klikken op het toepassings project **VisualObjects** en de opdracht **publiceren** te selecteren in de menu opdracht service Fabric.

![Context menu voor een Service Fabric toepassing][image1]

Als u **publiceren** selecteert, wordt een pop-upvenster geopend en kunt u het **doel profiel** instellen op **PublishProfiles\Local.xml**. Het venster moet er als volgt uitzien voordat u op **publiceren** klikt.

![Een Service Fabric-toepassing publiceren][image2]

Nu kunt u op **publiceren** klikken in het dialoog venster. U kunt [service Fabric Explorer gebruiken om het cluster en de toepassing weer te geven](service-fabric-visualizing-your-cluster.md). De Visual Objects-toepassing heeft een webservice waarnaar u kunt gaan door te typen `http://localhost:8081/visualobjects/` in de adres balk van uw browser.  U ziet dat er 10 zwevende visuele objecten worden weer gegeven op het scherm.

**Opmerking:** Als u implementeert in `Cloud.xml` Profiel (Azure service Fabric), moet de toepassing beschikbaar zijn op **http://{ServiceFabricName}. { Regio}. cloudapp. Azure. com: 8081/visualobjects/**. Zorg ervoor dat u hebt `8081/TCP` geconfigureerd in de Load Balancer (Zoek de Load Balancer in dezelfde resource groep als de service Fabric instantie).

## <a name="step-2-update-the-visual-objects-sample"></a>Stap 2: het voor beeld van Visual Objects bijwerken
U zult kunnen merken dat met de versie die is geïmplementeerd in stap 1, de visuele objecten niet draaien. We gaan deze toepassing upgraden naar een waar ook de visuele objecten draaien.

Selecteer het project VisualObjects. ActorService in de VisualObjects-oplossing en open het bestand **VisualObjectActor. cs** . Ga in dat bestand naar de methode `MoveObject` , commentaar uit `visualObject.Move(false)` en verwijder de opmerking `visualObject.Move(true)` . Deze code wijziging roteert de objecten nadat de service is bijgewerkt.  **Nu kunt u de oplossing bouwen (niet opnieuw samen stellen)**, waardoor de gewijzigde projecten worden gebouwd. Als u *alles opnieuw samen stellen* selecteert, moet u de versies van alle projecten bijwerken.

We moeten onze toepassing ook versie. Als u de versie wijzigingen wilt aanbrengen nadat u met de rechter muisknop op het **VisualObjects** -project hebt geklikt, kunt u de optie Visual Studio- **manifest versies bewerken** gebruiken. Als u deze optie selecteert, wordt het dialoog venster voor editie versies als volgt geopend:

![Het dialoog venster versie beheer][image3]

Werk de versies voor de gewijzigde projecten en hun code pakketten bij, samen met de toepassing naar versie 2.0.0. Nadat de wijzigingen zijn aangebracht, moet het Manifest er als volgt uitzien (vetgedrukte gedeelten tonen de wijzigingen):

![Versies bijwerken][image4]

De Visual Studio-hulpprogram ma's kunnen automatische update versies uitvoeren bij het selecteren van de **toepassing en de service versies automatisch bijwerken**. Als u [SemVer](http://www.semver.org)gebruikt, moet u de code en/of de configuratie pakket versie alleen bijwerken als deze optie is geselecteerd.

Sla de wijzigingen op en controleer nu het vak **de toepassing bijwerken** .

## <a name="step-3--upgrade-your-application"></a>Stap 3: een upgrade van uw toepassing uitvoeren
Raadpleeg de [para meters](service-fabric-application-upgrade-parameters.md) voor de upgrade van de toepassing en het [upgrade proces](service-fabric-application-upgrade.md) om een goed beeld te krijgen van de verschillende para meters voor upgrades, time-outs en status criteria die kunnen worden toegepast. Voor dit scenario is het evaluatie criterium voor service status ingesteld op de standaard instelling (niet-bewaakte modus). U kunt deze instellingen configureren door de **instellingen voor de upgrade configureren** te selecteren en de para meters vervolgens naar wens aan te passen.

Nu gaan we alle instellen om de upgrade van de toepassing te starten door **publiceren** te selecteren. Met deze optie wordt uw toepassing bijgewerkt naar versie 2.0.0, waarin de objecten draaien. Service Fabric werkt één update domein per keer bij (sommige objecten worden eerst bijgewerkt, gevolgd door anderen) en de service blijft toegankelijk tijdens de upgrade. De toegang tot de service kan worden gecontroleerd via uw client (browser).  

Als de upgrade van de toepassing wordt uitgevoerd, kunt u deze bewaken met Service Fabric Explorer, met behulp van het tabblad **upgrades op voortgang** onder de toepassingen.

In een paar minuten moeten alle update domeinen worden bijgewerkt (voltooid). in het Visual Studio-uitvoer venster moet ook worden vermeld dat de upgrade is voltooid. U ziet dat *alle* visuele objecten in uw browser venster nu draaien.

U kunt proberen om de versies te wijzigen en van versie 2.0.0 te verplaatsen naar versie 3.0.0 als oefening, of zelfs van versie 2.0.0 terug naar versie 1.0.0. Speel met time-outs en status beleidsregels om u vertrouwd te maken met hen. Bij het implementeren van een Azure-cluster in plaats van een lokaal cluster, moeten de gebruikte para meters anders zijn. We raden u aan om de time-outs zo veel mogelijk in te stellen.

## <a name="next-steps"></a>Volgende stappen
Als u uw toepassing bijwerkt [met Power shell](service-fabric-application-upgrade-tutorial-powershell.md) , kunt u een toepassings upgrade uitvoeren met behulp van Power shell.

Bepalen hoe uw toepassing wordt bijgewerkt met behulp van [upgrade parameters](service-fabric-application-upgrade-parameters.md).

Maak uw toepassings upgrades compatibel door te leren hoe u [gegevens serialisatie](service-fabric-application-upgrade-data-serialization.md)gebruikt.

Meer informatie over het gebruik van geavanceerde functionaliteit bij het upgraden van uw toepassing door te verwijzen naar [Geavanceerde onderwerpen](service-fabric-application-upgrade-advanced.md).

Corrigeer veelvoorkomende problemen in toepassings upgrades door te verwijzen naar de stappen in [Troubleshooting Application upgrades](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
