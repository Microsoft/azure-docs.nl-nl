---
title: 'Quickstart: Een Java-app maken in Azure Service Fabric'
description: In deze snelstart maakt u een Java-toepassing voor Azure met behulp van een voorbeeldtoepassing van de betrouwbare Service Fabric-services.
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom:
- mvc
- devcenter
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 199fe459ec8fd58d05b4946dda4291c46a4881c3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530915"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Quickstart:  Een Java-app implementeren in Azure Service Fabric op Linux

In deze quickstart implementeert u een Java-toepassing in Azure Service Fabric implementeert met behulp van de Eclipse IDE op een Linux-machine voor ontwikkelaars. Wanneer u klaar bent, hebt u een stemtoepassing met een web-front-end in Java die stemresultaten opslaat in een stateful back-endservice in het cluster.

Azure Service Fabric is een platform voor gedistribueerde systemen voor het implementeren en distribueren van microservices en containers.

## <a name="prerequisites"></a>Vereisten

- [Java-omgeving](./service-fabric-get-started-linux.md#set-up-java-development) en [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+](https://www.eclipse.org/downloads/packages/) en [Eclipse-plug-in voor Service Fabric](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Service Fabric-SDK en CLI (opdrachtregelinterface)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een opdrachtvenster de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen op de lokale computer.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

1. Start het lokale cluster door de volgende opdracht uit te voeren:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Het starten van het lokale cluster kan enige tijd duren. Open Service Fabric Explorer op `http://localhost:19080` om te controleren of het cluster volledig naar behoren functioneert. Als de vijf knooppunten in orde zijn, is het lokale cluster actief.

    ![Azure Service Fabric Explorer toont goede knooppunten](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Open Eclipse.
3. Selecteer **Bestand** > **Importeren** > **Gradle** > **Bestaand Gradle-project** en volg de wizard.
4. Selecteer **Map** en kies de map **Stemmen** in de map **service-fabric-java-quickstart** die u hebt gekloond vanuit GitHub. Selecteer **Finish**.

    ![Gradle-project importeren in Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. U hebt nu het project `Voting` in de pakketverkenner voor Eclipse.
6. Klik met de rechtermuisknop op het project en selecteer **Toepassing publiceren** in de vervolgkeuzelijst **Service Fabric**. Kies **PublishProfiles/Local.json** als doelprofiel en selecteer **Publiceren**.

    ![Lokale JSON publiceren met Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Open uw favoriete webbrowser en open de toepassing op `http://localhost:8080`.

    ![Lokale host van Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

U kunt nu een reeks stemmingsopties toevoegen en beginnen met het verzamelen van stemmen. De toepassing wordt uitgevoerd en alle gegevens worden opgeslagen in het Service Fabric-cluster, zonder dat hiervoor een aparte database nodig is.

![Voorbeeld van een Azure Service Fabric-stemtoepassing](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Toepassingen en services voor schalen in een cluster

Services kunnen eenvoudig worden geschaald in een cluster om een wijziging in de belasting voor de services aan te kunnen. U schaalt een service door het aantal exemplaren te wijzigen dat wordt uitgevoerd in het cluster. Er bestaan veel manieren om uw services omhoog te schalen. U kunt bijvoorbeeld scripts of opdrachten van Service Fabric CLI (`sfctl`) gebruiken. In de volgende stappen wordt Service Fabric Explorer gebruikt.

Service Fabric Explorer kan worden uitgevoerd in alle Service Fabric-clusters en is toegankelijk door vanuit een browser te bladeren naar de HTTP-beheerpoort (19080) van de cluster. Bijvoorbeeld `http://localhost:19080`.

Voer de volgende stappen uit om de web-front-endservice te schalen:

1. Open Service Fabric Explorer in het cluster. Bijvoorbeeld `https://localhost:19080`.
2. Selecteer het beletselteken ( **...** ) naast het knooppunt **fabric:/Voting/VotingWeb** in de structuurweergave en kies **Service schalen**.

    ![Een service omhoog schalen in Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    U kunt er nu voor kiezen om het aantal exemplaren van de web-front-endservice te schalen.

3. Wijzig het aantal in **2** en selecteer **Service schalen**.
4. Selecteer het knooppunt **fabric:/Voting/VotingWeb** in de structuurweergave en vouw het partitieknooppunt uit (vertegenwoordigd door een GUID).

    ![Geschaalde service in Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    U ziet nu dat er twee exemplaren van de service zijn. In de structuurweergave ziet u op welke knooppunten de exemplaren worden uitgevoerd.

Met deze eenvoudige beheertaak hebt u het aantal beschikbare resources voor het verwerken van gebruikersbelasting voor de front-endservice verdubbeld. Het is belangrijk te weten dat u niet meerdere exemplaren van een service nodig hebt om ervoor te zorgen dat deze op betrouwbare wijze wordt uitgevoerd. Als de service mislukt, wordt in Service Fabric een nieuw exemplaar van de service uitgevoerd in het cluster.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

* Eclipse gebruiken als hulpmiddel voor uw Java-toepassingen in Service Fabric
* Java-toepassingen implementeren in het lokale cluster
* De toepassing uitschalen over meerdere knooppunten

Meer informatie over het werken met Java-apps in Service Fabric vindt u in de zelfstudie voor Java-apps.

> [!div class="nextstepaction"]
> [Een Java-app implementeren](./service-fabric-tutorial-create-java-app.md)
