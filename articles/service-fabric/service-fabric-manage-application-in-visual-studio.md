---
title: Toepassingen beheren in Visual Studio
description: Met Visual Studio kunt u Azure Service Fabric-toepassingen en-services maken, ontwikkelen, inpakken, implementeren en fouten opsporen.
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574544"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Visual Studio gebruiken om het schrijven en beheren van uw Service Fabric-toepassingen te vereenvoudigen
U kunt uw Azure Service Fabric-toepassingen en-services beheren via Visual Studio. Nadat u [uw ontwikkel omgeving hebt ingesteld](service-fabric-get-started.md), kunt u Visual Studio gebruiken voor het maken van service Fabric toepassingen, het toevoegen van services of het inpakken, registreren en implementeren van toepassingen in uw lokale ontwikkel cluster.

## <a name="deploy-your-service-fabric-application"></a>Uw Service Fabric-toepassing implementeren
Bij het implementeren van een toepassing worden de volgende stappen standaard gecombineerd tot één eenvoudige bewerking:

1. Het toepassings pakket maken
2. Het toepassings pakket uploaden naar de installatie kopie opslag
3. Het toepassings type registreren
4. Alle actieve toepassings exemplaren verwijderen
5. Een toepassings exemplaar maken

In Visual Studio kunt u op **F5** drukken om uw toepassing te implementeren en de debugger te koppelen aan alle toepassings exemplaren. U kunt **CTRL + F5** gebruiken om een toepassing te implementeren zonder fout opsporing of u kunt publiceren naar een lokaal of extern cluster met behulp van het publicatie profiel.

### <a name="application-debug-mode"></a>Foutopsporingsmodus
Visual Studio biedt een eigenschap met de naam **foutopsporingsmodus**, waarmee wordt bepaald hoe u wilt dat Visual Studios de toepassings implementatie afhandelt als onderdeel van fout opsporing.

#### <a name="to-set-the-application-debug-mode-property"></a>De eigenschap debug mode van de toepassing instellen
1. Kies **Eigenschappen** in het snelmenu van service Fabric toepassings project (*. sfproj) of druk op de toets **F4** .
2. Stel in het venster **Eigenschappen** de eigenschap **debug mode** van de toepassing in.

![Eigenschap debug mode van toepassing instellen][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modi toepassings fout opsporing

1. **Toepassing vernieuwen** In deze modus kunt u snel uw code wijzigen en fouten opsporen en de bewerking van statische Webbe standen ondersteunen tijdens het opsporen van fouten. Deze modus werkt alleen als uw lokale ontwikkel cluster zich in de modus met één knoop punt bevindt. Dit is de standaard modus voor het opsporen van toepassingen.
2. **Toepassing verwijderen** zorgt ervoor dat de toepassing wordt verwijderd wanneer de foutopsporingssessie wordt beëindigd.
3. **Automatische upgrade** De toepassing blijft actief wanneer de foutopsporingssessie wordt beëindigd. Bij de volgende foutopsporingssessie wordt de implementatie als een upgrade beschouwd. Met het upgrade proces worden alle gegevens bewaard die u in een eerdere foutopsporingssessie hebt ingevoerd.
4. **Toepassing blijven** De toepassing wordt uitgevoerd in het cluster wanneer de foutopsporingssessie wordt beëindigd. Aan het begin van de volgende foutopsporingssessie, wordt de toepassing verwijderd.

De gegevens voor **automatische upgrade** blijven behouden door de toepassings upgrade mogelijkheden van service Fabric toe te passen. Zie [service Fabric Application upgrade](service-fabric-application-upgrade.md)(Engelstalig) voor meer informatie over het upgraden van toepassingen en over hoe u een upgrade kunt uitvoeren in een echte omgeving.

## <a name="add-a-service-to-your-service-fabric-application"></a>Een service toevoegen aan uw Service Fabric-toepassing
U kunt nieuwe services toevoegen aan uw toepassing om de functionaliteit ervan uit te breiden. Om ervoor te zorgen dat de service is opgenomen in uw toepassings pakket, voegt u de service toe via het menu-item **nieuwe Fabric-service..** ..

![Een nieuwe Service Fabric-service toevoegen][newservice]

Selecteer een Service Fabric project type om toe te voegen aan uw toepassing en geef een naam op voor de service.  Zie [een framework kiezen voor uw service](service-fabric-choose-framework.md) om u te helpen bepalen welk service type u moet gebruiken.

![Selecteer een Service Fabric service project type dat u aan uw toepassing wilt toevoegen][addserviceproject]

De nieuwe service wordt toegevoegd aan uw oplossing en het bestaande toepassings pakket. De service verwijzingen en een standaard service-exemplaar worden toegevoegd aan het toepassings manifest, waardoor de service wordt gemaakt en gestart de volgende keer dat u de toepassing implementeert.

![De nieuwe service wordt toegevoegd aan het manifest van de toepassing][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Uw Service Fabric-toepassing inpakken
Als u de toepassing en de bijbehorende services wilt implementeren in een cluster, moet u een toepassings pakket maken.  Het pakket organiseert het toepassings manifest, service manifesten en andere nood zakelijke bestanden in een specifieke indeling.  Visual Studio stelt het pakket in en beheert het in de map van het toepassings project, in de map ' pak '.  Als u op **pakket** klikt in het context menu van de **toepassing** , wordt het toepassings pakket gemaakt of bijgewerkt.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Toepassingen en toepassings typen verwijderen met Cloud Explorer
U kunt elementaire Cluster beheer bewerkingen uitvoeren vanuit Visual Studio met behulp van Cloud Explorer, die u kunt starten vanuit het menu **weer gave** . U kunt bijvoorbeeld toepassingen verwijderen en de inrichting van toepassings typen ongedaan maken op lokale of externe clusters.

![Een toepassing verwijderen][removeapplication]

> [!TIP]
> Zie [uw cluster visualiseren met Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)voor een uitgebreide functionaliteit voor cluster beheer.
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen
* [Service Fabric toepassings model](service-fabric-application-model.md)
* [Implementatie van Service Fabric-toepassing](service-fabric-deploy-remove-applications.md)
* [Toepassings parameters voor meerdere omgevingen beheren](service-fabric-manage-multiple-environment-app-configuration.md)
* [Fouten opsporen in uw Service Fabric-toepassing](service-fabric-debugging-your-application.md)
* [Uw cluster visualiseren met behulp van Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
