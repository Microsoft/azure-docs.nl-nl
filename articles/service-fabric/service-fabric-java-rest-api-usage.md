---
title: Api's van Azure Service Fabric Java-client
description: Service Fabric Java-client-Api's genereren en gebruiken met behulp van Service Fabric client REST API specificatie
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 24ee4a3d8109626bf93e01fbd10c00140762c9c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87324621"
---
# <a name="azure-service-fabric-java-client-apis"></a>Api's van Azure Service Fabric Java-client

Met Service Fabric-client-Api's kunt u op micro Services gebaseerde toepassingen en containers implementeren en beheren in een Service Fabric cluster op Azure, on-premises, lokale ontwikkel machine of in een andere cloud. In dit artikel wordt beschreven hoe u Service Fabric Java client-Api's kunt genereren en gebruiken boven op de Service Fabric client REST Api's

## <a name="generate-the-client-code-using-autorest"></a>Genereer de client code met behulp van auto rest

Auto [rest](https://github.com/Azure/autorest) is een hulp programma waarmee client bibliotheken worden gegenereerd voor toegang tot gereste webservices. Invoer voor auto rest is een specificatie die de REST API beschrijft met de OpenAPI-specificatie-indeling. [Service Fabric-client rest api's](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) volgen deze specificatie.

Volg de onderstaande stappen om Service Fabric Java-client code te genereren met behulp van het gereedschap auto rest.

1. nodejs en NPM installeren op uw computer

    Als u Linux gebruikt, gaat u als volgt te werkt:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Als u Mac OS X gebruikt, gaat u als volgt te werkt:
    ```bash
    brew install node
    ```

2. Installeer autorest met behulp van NPM.
    ```bash
    npm install -g autorest
    ```

3. Fork en kloon de opslag plaats [Azure-rest-API-specificaties](https://github.com/Azure/azure-rest-api-specs)  op uw lokale machine en ga naar de gekloonde locatie vanaf de terminal van uw computer.


4. Ga naar de hieronder vermelde locatie in de gekloonde opslag plaats.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Als uw cluster versie niet 6,0 is, gaat u naar de juiste map in de map stabiel.
    >   

5. Voer de volgende auto rest-opdracht uit om de Java-client code te genereren.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Hieronder ziet u een voor beeld van het gebruik van auto rest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Met de volgende opdracht wordt een ``servicefabric.json`` specificatie bestand gebruikt als invoer en wordt Java-client code in ``java-rest-api-     code`` map gegenereerd en wordt de code in de  ``servicefabricrest`` naam ruimte Inge sloten. Na deze stap vindt u twee mappen ``models`` ``implementation`` en twee bestanden die ``ServiceFabricClientAPIs.java`` ``package-info.java`` in de map zijn gegenereerd ``java-rest-api-code`` .


## <a name="include-and-use-the-generated-client-in-your-project"></a>De gegenereerde client in uw project insluiten en gebruiken

1. Voeg de gegenereerde code op de juiste wijze toe aan uw project. We raden u aan om een bibliotheek te maken met behulp van de gegenereerde code en deze bibliotheek op te nemen in uw project.
2. Als u een bibliotheek maakt, neemt u de volgende afhankelijkheden op in het project van uw bibliotheek. Als u een andere benadering volgt, neemt u de afhankelijkheid op de juiste manier op.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Als u bijvoorbeeld maven build-systeem gebruikt, kunt u het volgende in het ``pom.xml`` bestand gebruiken:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Maak een RestClient met de volgende code:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Gebruik het client object en breng de juiste aanroepen als dat nodig is. Hier volgen enkele voor beelden van het gebruik van client-object. We gaan ervan uit dat het toepassings pakket is gebouwd en geüpload naar het archief met installatie kopieën voordat u de onderstaande API'S gebruikt.
    * Een toepassing inrichten
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Een app maken

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Uitleg over de gegenereerde code
Voor elke API vindt u vier overbelasting van de implementatie. Als er optionele para meters zijn, kunt u nog vier variaties vinden, inclusief deze optionele para meters. Denk bijvoorbeeld aan de API ``removeReplica`` .
 1. **openbaar void removeReplica (String nodenaam, UUID partitionId, String replicaId, Boolean forceRemove, lange time-out)**
    * Dit is de synchrone variant van de API-aanroep van removeReplica
 2. **open bare ServiceFuture \<Void> removeReplicaAsync (String nodenaam, uuid partitionId, String replicaId, Boolean forceRemove, Long timeout, Final ServiceCallback \<Void> ServiceCallback)**
    * Deze variant van de API-aanroep kan worden gebruikt als u asynchrone programmering wilt gebruiken en retour aanroepen wilt gebruiken
 3. **openbaar waarneem bare \<Void> removeReplicaAsync (String nodenaam, uuid partitionId, String replicaId)**
    * Deze variant van de API-aanroep kan worden gebruikt als u een reactieve asynchrone programmering wilt gebruiken
 4. **openbaar waarneem bare \<ServiceResponse\<Void>> removeReplicaWithServiceResponseAsync (String nodenaam, uuid partitionId, String replicaId)**
    * Deze variant van de API-aanroep kan worden gebruikt als u asynchrone programmering wilt gebruiken en wilt omgaan met onbewerkt rest-antwoord

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [service Fabric rest-api's](/rest/api/servicefabric/)
