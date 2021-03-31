---
title: Uw apps bewaken in Service Fabric met ELK in Azure
description: In deze zelfstudie vindt u Informatie over het instellen van ELK en het controleren van uw Service Fabric-toepassingen.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 5faf26230618161a5b908c9a544a43ec5c33b807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91532019"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>Zelfstudie: Uw Service Fabric-toepassingen bewaken met ELK

Deze zelfstudie is deel vier een serie. Er wordt weergegeven hoe ELK (Elasticsearch, Logstash en Kibana) moet worden gebruikt voor het bewaken van Service Fabric-toepassingen die worden uitgevoerd in Azure.

In deel vier van de serie leert u het volgende:
> [!div class="checklist"]
> * Een ELK-server in Azure instellen
> * Logstash configureren voor het ontvangen van logboeken van Event Hubs
> * Platform- en toepassingslogboeken in Kibana visualiseren

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Java Service Fabric Reliable Services-toepassing maken](service-fabric-tutorial-create-java-app.md)
> * [De toepassing implementeren en er foutopsporing op toepassen in een lokaal cluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * [De toepassing implementeren in een Azure-cluster](service-fabric-tutorial-java-deploy-azure.md)
> * Controle en diagnostische gegevens voor de toepassing instellen
> * [CI/CD instellen](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Stel uw toepassing in voor het verzenden van logboeken naar de locatie die is opgegeven in [deel twee](service-fabric-tutorial-debug-log-local-cluster.md).
* Voltooi [deel drie](service-fabric-tutorial-java-deploy-azure.md) en configureer een actief Service Fabric-cluster om logboeken te verzenden naar Event Hubs.
* Het beleid in Event Hubs met de machtiging Luisteren en de bijbehorende primaire sleutel van reeks drie.

## <a name="download-the-voting-sample-application"></a>De voorbeeldtoepassing om te stemmen downloaden

Als u in [deel één van deze zelfstudiereeks](service-fabric-tutorial-create-java-app.md) niet de voorbeeldtoepassing om te stemmen hebt gemaakt, kunt u deze downloaden. Voer in een opdrachtvenster de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen op de lokale computer.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Een ELK-server in Azure instellen

U kunt een vooraf geconfigureerde ELK-omgeving gebruiken voor deze zelfstudie en als u deze al hebt, gaat u naar de sectie **Logstash instellen**. Als u er nog geen hebt, maakt u deze met de volgende stappen in Azure.

1. Een door [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) gecertificeerde ELK maken in Azure. Voor de doeleinden van de zelfstudie zijn er geen bijzondere specificaties die moeten worden gevolgd om deze server te maken.

2. Ga naar uw resource in Azure Portal en ga naar het tabblad **Diagnostische gegevens over opstarten** onder de sectie **Ondersteuning + probleemoplossing**. Klik op het tabblad **Serieel logboek**.

    ![Diagnostische gegevens over opstarten](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. Voer een zoekopdracht uit op de logboeken voor het wachtwoord dat is vereist voor toegang tot het Kibana-exemplaar. Deze lijkt op het volgende codefragment:

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Druk op de knop Verbinding maken op het paginaoverzicht van de server in Azure Portal om de aanmeldinformatie te krijgen.

    ![VM-verbinding](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. Voeg SSH toe aan de server die als host fungeert voor de ELK-installatiekopie met de volgende opdracht

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>ELK instellen

1. De eerste stap is het laden van de ELK-omgeving

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. Als u van een bestaande omgeving gebruikmaakt, moet u de volgende opdracht uitvoeren om de Logstash-service te stoppen

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Voer de volgende opdracht uit om de invoegtoepassing Logstash voor Event Hubs te installeren.

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. Maak of wijzig uw bestaande Logstash config-bestand met de volgende inhoud: als u het bestand maakt, moet het op ```/opt/bitnami/logstash/conf/access-log.conf``` worden gemaakt als u de installatiekopie voor ELK Bitnami in Azure gebruikt.

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. Om de configuratie te controleren, moet u de volgende opdracht uitvoeren:

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Start de Logstash-service

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Controleer uw Elasticsearch om ervoor te zorgen dat u gegevens ontvangt

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. Ga naar uw Kibana-dashboard op **http:\//SERVER-IP** en voer uw gebruikersnaam en wachtwoord in voor Kibana. Als u de installatiekopie voor ELK in Azure gebruikt, is de standaardgebruikersnaam 'user' en het wachtwoord hetgeen dat is verkregen van de **Diagnostische gegevens over opstarten**.

    ![Schermopname toont een Kibana-dashboard voor het visualiseren van platform-en toepassingslogboeken.](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een ELK-server actief en werkend krijgen in Azure
> * De server voor het ontvangen van diagnostische gegevens van uw Service Fabric-cluster configureren

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [CI/CD met Jenkins instellen](service-fabric-tutorial-java-jenkins.md)
