---
title: Fouten opsporen in een Java-app in een lokale Service Fabric-cluster
description: In deze zelfstudie vindt u informatie over het opsporen van fouten en logboeken ophalen uit een Service Fabric Java-toepassing die wordt uitgevoerd op een lokaal cluster.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 801702d43bae6d925fa4f7fbc37cf44bf585fa6d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91531987"
---
# <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>Zelfstudie: Fouten opsporen in een Java-toepassing die is geïmplementeerd op een lokaal Service Fabric-cluster

Deze zelfstudie is deel twee van een serie. U leert hoe u een extern foutopsporingsprogramma koppelt met gebruik van Eclipse voor de Service Fabric-toepassing. Bovendien leert u hoe u logboeken moet omleiden van de actieve toepassingen naar een locatie die praktisch is voor de ontwikkelaar.

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Java Service Fabric Reliable Services-toepassing maken](service-fabric-tutorial-create-java-app.md)
> * De toepassing implementeren en er foutopsporing op toepassen in een lokaal cluster
> * [De toepassing implementeren in een Azure-cluster](service-fabric-tutorial-java-deploy-azure.md)
> * [Controle en diagnostische gegevens voor de toepassing instellen](service-fabric-tutorial-java-elk.md)
> * [CI/CD instellen](service-fabric-tutorial-java-jenkins.md)


In deel twee van de serie leert u het volgende:
> [!div class="checklist"]
> * Fouten opsporen in de Java-toepassing met behulp van Eclipse
> * Logboeken omleiden naar een configureerbare locatie


## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Stel uw ontwikkelomgeving in voor [Mac](service-fabric-get-started-mac.md) of [Linux](service-fabric-get-started-linux.md). Volg de instructies voor het installeren van de Eclipse-invoegtoepassing, Gradle, de Service Fabric SDK en de Service Fabric CLI (sfctl).

## <a name="download-the-voting-sample-application"></a>De voorbeeldtoepassing om te stemmen downloaden

Als u in [deel één van deze zelfstudiereeks](service-fabric-tutorial-create-java-app.md) niet de voorbeeldtoepassing om te stemmen hebt gemaakt, kunt u deze downloaden. Voer in een opdrachtvenster de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen op de lokale computer.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

[Bouw en implementeer](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster) de toepassing in het cluster voor lokale ontwikkeling.

## <a name="debug-java-application-using-eclipse"></a>Fouten opsporen in de Java-toepassing met behulp van Eclipse

1. Open de Eclipse IDE op uw computer en klik op **File -> Import...**

2. Selecteer in het pop-upvenster de optie **General -> Existing Projects into Workspace** en druk op Next.

3. Kies in het venster Import Projects de optie **Select root directory** en selecteer de map **Voting**. Als u zelfstudie reeks één hebt gevolgd, bevindt de map **Voting** zich in de map **Eclipse-workspace**.

4. Werk entryPoint.sh bij van de service waar u fouten wilt opsporen, zodat dit het Java-proces met de parameters voor foutopsporing op afstand begint. Voor deze zelfstudie wordt de stateless front-end gebruikt: *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*. Poort 8001 is in dit voorbeeld voor foutopsporing ingesteld.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. Werk het Manifest van de toepassing bij door het op één instellen van het aantal instanties of het aantal replica's voor de service, waarop de foutopsporing wordt uitgevoerd. Deze instelling voorkomt conflicten voor de poort die wordt gebruikt voor het opsporen van fouten. Stel bijvoorbeeld voor stateless services ``InstanceCount="1"`` in en stel voor stateful services de doel- en min-replicasetgrootten als volgt in op 1: ``TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

6. Selecteer in de Eclipse IDE **Run -> Debug Configurations -> Remote Java Application**, druk op de knop **New**, stel de eigenschappen als volgt in en klik op **Apply**.

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. Voeg een onderbrekingspunt toe aan regel 109 van het bestand *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*.

8. Klik in de Package Explorer voor Eclipse met de rechtermuisknop op het project **Voting** en klik op **Service Fabric -> Publish Application ...**

9. Selecteer in het venster **Publish Application** de optie **Local.json** in de vervolgkeuzelijst en klik op **Publish**.

10. Selecteer in de Eclipse IDE **Run -> Debug Configurations -> Remote Java Application**, klik op de **Voting**-configuratie die u hebt gemaakt en klik op **Debug**.

11. Ga naar uw webbrowser en open **localhost: 8080**. Hiermee wordt automatisch het onderbrekingspunt bereikt en Eclipse schakelt over naar **perspectief voor foutopsporing**.

U kunt nu dezelfde stappen toepassen om foutopsporing uit te voeren op een Service Fabric-toepassing in Eclipse.

## <a name="redirect-application-logs-to-custom-location"></a>Toepassingslogboeken omleiden naar aangepaste locatie

De volgende stappen doorlopen het omleiden van de toepassingslogboeken van de standaardlocatie */var/log/syslog* naar een aangepaste locatie.

1. Op dit moment kunnen toepassingen die worden uitgevoerd in Service Fabric Linux-clusters het ophalen van slechts één logboekbestand ondersteunen. Als u een toepassing wilt instellen dat de logboeken altijd worden opgeslagen op */tmp/mysfapp0.0.log*, maakt u een bestand met de naam logging.properties op de locatie *Voting/VotingApplication/VotingWebPkg/Code/logging.properties* en voegt u de volgende inhoud toe.

    ```
    handlers = java.util.logging.FileHandler

    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter

    # This value specifies your custom location.
    # You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Voeg de volgende parameter in het *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* toe voor de Java-uitvoeropdracht:

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```

    In het volgende voorbeeld wordt een voorbeelduitvoering weergegeven waarbij het foutopsporingsprogramma is gekoppeld, vergelijkbaar met de uitvoering in de vorige sectie.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

U hebt nu geleerd hoe u fouten kunt opsporen en toegang kunt krijgen tot uw toepassingslogboeken tijdens het ontwikkelen van uw Service Fabric Java-toepassingen.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Fouten opsporen in de Java-toepassing met behulp van Eclipse
> * Logboeken omleiden naar een configureerbare locatie

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [De toepassing implementeren in Azure](service-fabric-tutorial-java-deploy-azure.md)