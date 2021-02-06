---
title: Fouten opsporen in een Azure Service Fabric Mesh-web-app die lokaal wordt uitgevoerd
description: In deze zelfstudie gaat u fouten opsporen in een Azure Service Fabric Mesh-webtoepassing die op uw lokale cluster wordt uitgevoerd.
author: georgewallace
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 9e3dc16481340c0266cd398d0970e2147648e17f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625446"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Zelfstudie: Fouten opsporen in een Service Fabric Mesh-toepassing die in uw lokale ontwikkelcluster wordt uitgevoerd

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Deze zelfstudie is deel twee van een reeks en u leert hoe u een Azure Service Fabric Mesh-app bouwt en fouten opspoort in uw lokale ontwikkelcluster.

In deze zelfstudie leert u:

> [!div class="checklist"]
> * Wat er gebeurt wanneer u een Azure Service Fabric Mesh-toepassing maakt
> * Hoe u een onderbrekingspunt instelt om een aanroep tussen services waar te nemen

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Service Fabric Mesh-app maken in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Fouten opsporen in een Service Fabric Mesh-app die wordt uitgevoerd in de lokale ontwikkelcluster
> * [Een Service Fabric Mesh-app implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Een Service Fabric Mesh-app bijwerken](service-fabric-mesh-tutorial-upgrade.md)
> * [Service Fabric Mesh-bronnen opschonen](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Controleer of u [uw ontwikkelomgeving hebt ingesteld](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Hieronder valt het installeren van de Service Fabric-runtime, SDK, Docker en Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>De voorbeeldtoepassing voor taken downloaden

Als u in [deel één van deze zelfstudiereeks](service-fabric-mesh-tutorial-create-dotnetcore.md) niet de voorbeeldtoepassing hebt gemaakt, kunt u deze downloaden. Voer in een opdrachtvenster de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen op de lokale computer.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

U vindt de toepassing in de map `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Bouwen en fouten opsporen in uw lokale cluster

Zodra uw project is geladen, wordt er automatisch een Docker-installatiekopie gemaakt en op uw lokale cluster geïmplementeerd. Dit proces kan enige tijd duren. Als u de voortgang in het deelvenster **Uitvoer** van Visual Studio wilt volgen, stelt u de vervolgkeuzelijst van het deelvenster **Show output from:** in op **Service Fabric Tools**.

Druk op **F5** om de service lokaal te compileren en uit te voeren. Als het project lokaal wordt uitgevoerd en de fouten er lokaal in worden opgespoord, gebeurt het volgende:

* Er wordt gecontroleerd of Docker voor Windows wordt uitgevoerd en zodanig is ingesteld dat Windows wordt gebruikt als het besturingssysteem van de container.
* Download eventuele ontbrekende Docker-basisinstallatiekopieën. Dit onderdeel kan enige tijd in beslag nemen
* Bouw de Docker-installatiekopie (opnieuw) die is gebruikt voor het hosten van uw codeproject.
* Implementeer de container in het lokale Service Fabric-ontwikkelcluster en voer deze hierin uit.
* Voer de services uit en u komt terecht bij eventuele onderbrekingspunten die u hebt ingesteld.

Als de lokale implementatie is voltooid en uw app in Visual Studio wordt uitgevoerd, wordt een standaardvoorbeeld van een webpagina in een browservenster geopend.

## <a name="debugging-tips"></a>Tips voor foutopsporing

Maak de uitvoering van uw foutopsporing (F5) veel sneller door de instructies in [Optimize Visual Studio performance](service-fabric-mesh-howto-optimize-vs.md) (Prestaties van Visual Studio optimaliseren).

Er is momenteel een probleem waardoor `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` niet kan worden aangeroepen omdat er geen verbinding met de service kan worden gemaakt. Dit kan zich voordoen wanneer het IP-adres van de host verandert. De oplossing:

1. Verwijder de app uit het lokale cluster (in Visual Studio, **Build** > **Oplossing opschonen**).
2. Selecteer uit de Local Cluster Manager van Service Fabric, **Lokaal cluster stoppen** en vervolgens **Lokaal cluster starten**.
3. Implementeer de app opnieuw (in Visual Studio **F5**).

Als u de fout **No Service Fabric local cluster is running** ziet, controleert u of de LCM (Service Fabric Local Custer Manager) wordt uitgevoerd en klikt u met de rechtermuisknop op het LCM-pictogram op de taakbalk. Klik vervolgens op **Lokaal cluster starten**. Als het cluster gestart, gaat u terug naar Visual Studio en drukt u op **F5**.

Als u een **404**-fout krijgt als de app wordt gestart, betekent dit waarschijnlijk dat de omgevingsvariabelen in **service.yaml** onjuist zijn. Controleer of `ApiHostPort` en `ToDoServiceName` volgens de instructies in [Omgevingsvariabelen maken](./service-fabric-mesh-tutorial-create-dotnetcore.md#create-environment-variables) zijn ingesteld.

Als u opbouwfouten krijgt in **service.yaml**, controleert u of er spaties in plaats van tabs zijn gebruikt om de regels in te laten springen. Ook moet u de app op dit moment met Engelse landinstellingen maken.

### <a name="debug-in-visual-studio"></a>Fouten opsporen met Visual Studio

Als u in Visual Studio fouten opspoort in een Service Fabric Mesh-toepassing, gebruikt u daarvoor een lokaal ontwikkelcluster van Service Fabric. Als u wilt zien hoe taakitems van de back-endservice worden opgehaald, voert u foutopsporing uit in de OnGet()-methode.
1. Open **Pages** > **Index.cshtml** > **Index.cshtml.cs** in project **WebFrontEnd** en stel een onderbrekingspunt in de **OnGet**-methode in (regel 17).
2. Open **TodoController.cs** in project **ToDoService** en stel een onderbrekingspunt in de **Get**-methode in (regel 15).
3. Ga terug naar de browser en vernieuw de pagina. U komt uit bij het onderbrekingspunt in de `OnGet()`-methode van het web-front-end. U kunt de `backendUrl`-variabele inspecteren om te zien hoe de omgevingsvariabelen die u in het bestand **service.yaml** hebt gedefinieerd, worden gecombineerd in de URL die wordt gebruikt om contact te maken met de back-endservice.
4. Sla de `client.GetAsync(backendUrl).GetAwaiter().GetResult())`-aanroep over (F10) en u komt uit bij het `Get()`-onderbrekingspunt van de controller. Met deze methode ziet u hoe de lijst met taakitems uit de in-memory lijst wordt opgehaald.
5. Als u klaar bent, stopt u met het fouten opsporen van uw project in Visual Studio door op **Shift+F5** te drukken.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Wat er gebeurt wanneer u een Azure Service Fabric Mesh-toepassing maakt
> * Hoe u een onderbrekingspunt instelt om een aanroep tussen services waar te nemen

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Een Service Fabric Mesh-app implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
