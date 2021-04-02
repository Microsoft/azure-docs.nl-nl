---
title: 'Zelfstudie: Een Java-app maken in Azure Service Fabric'
description: In deze zelfstudie leert u hoe u een betrouwbare Java-servicetoepassing met een front-end en een betrouwbare stateful back-endservice maakt en de toepassing in een cluster implementeert.
ms.topic: tutorial
ms.date: 09/01/2018
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java, devx-track-js
ms.openlocfilehash: d1f66177b1939d612a5ee80c99109c397b56cb37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91532190"
---
# <a name="tutorial-create-an-application-with-a-java-api-front-end-service-and-a-stateful-back-end-service-on-azure-service-fabric"></a>Zelfstudie: Een toepassing met een Java-API front-endservice en een stateful back-endservice maken voor Azure Service Fabric

Deze zelfstudie is deel één van een serie. Wanneer u klaar bent, hebt u een stemtoepassing met een web-front-end in Java die stemresultaten opslaat in een stateful back-endservice op Azure Service Fabric. Voor deze zelfstudie hebt u een werkende Mac OSX- of Linux-ontwikkelaarsmachine nodig. Als u de stemtoepassing niet handmatig wilt maken, kunt u [de broncode downloaden](https://github.com/Azure-Samples/service-fabric-java-quickstart) voor de voltooide toepassing en verdergaan met [Het voorbeeld van een stemtoepassing doorlopen](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application). Overweeg ook de [quickstart voor betrouwbare Java-services](service-fabric-quickstart-java-reliable-services.md) te volgen.

![Voorbeeld van een Service Fabric-stemtoepassing](./media/service-fabric-tutorial-create-java-app/service-fabric-java-voting-app-sample.png)

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een Java Service Fabric Reliable Services-toepassing maken
> * [De toepassing implementeren en er foutopsporing op toepassen in een lokaal cluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * [De toepassing implementeren in een Azure-cluster](service-fabric-tutorial-java-deploy-azure.md)
> * [Controle en diagnostische gegevens voor de toepassing instellen](service-fabric-tutorial-java-elk.md)
> * [CI/CD instellen](service-fabric-tutorial-java-jenkins.md)


In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Een stateful betrouwbare service in Java maken
> * Een stateless webtoepassingsservice in Java maken
> * Externe service gebruiken om te communiceren met de stateful service
> * De toepassing in een lokaal Service Fabric-cluster implementeren

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* Stel uw ontwikkelomgeving in voor [Mac](service-fabric-get-started-mac.md) of [Linux](service-fabric-get-started-linux.md). Volg de instructies voor het installeren van de Eclipse-invoegtoepassing, Gradle, de Service Fabric SDK en de Service Fabric CLI (sfctl).

## <a name="create-the-front-end-java-stateless-service"></a>De front-end stateless Java-service maken

Maak eerst de webfront-end van de stemtoepassing. Een web-UI aangedreven door AngularJS verzendt aanvragen naar de stateless Java-service, die op een lichtgewicht HTTP-server wordt uitgevoerd. Deze service verwerkt elke aanvraag en stuurt een externe procedureaanroep naar de stateful service voor het opslaan van de stemmen. 

1. Open Eclipse.

2. Maak een project via **File** > **New** > **Other** > **Service Fabric** > **Service Fabric Project**.

    ![Nieuw Service Fabric-project in Eclipse](./media/service-fabric-tutorial-create-java-app/service-fabric-project-wizard.png)

3. Geef in het dialoogvenster **ServiceFabric Project Wizard** het project de naam **Voting** en selecteer **Next**.

    ![Stateless Java-service kiezen in het dialoogvenster voor een nieuwe service](./media/service-fabric-tutorial-create-java-app/name-service-fabric-project-wizard.png) 

4. Selecteer op de pagina **Add Service** de optie **Stateless Service** en noem uw service **VotingWeb**. Selecteer **Finish** om het project te voltooien.

    ![Een stateless service maken voor uw Service Fabric-project]( ./media/service-fabric-tutorial-create-java-app/add-service-fabric-votingweb-service.png)

    Eclipse maakt een toepassing en een serviceproject en geeft deze weer in Package Explorer.

    ![Eclipse Package Explorer na het maken van toepassing]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

De tabel bevat een korte beschrijving van elk item in de Package Explorer van de vorige schermafbeelding. 

| **Item in Package Explorer** | **Beschrijving** |
| --- | --- |
| PublishProfiles | Bevat JSON-bestanden met een beschrijving van profielgegevens van lokale en Azure Service Fabric-clusters. De inhoud van deze bestanden wordt gebruikt door de invoegtoepassing bij het implementeren van de toepassing. |
| Scripts | Bevat de helperscripts die kunnen worden gebruikt vanaf de opdrachtregel om uw toepassing snel met een cluster te beheren. |
| VotingApplication | Bevat de Service Fabric-toepassing die naar het Service Fabric-cluster wordt gepusht. |
| VotingWeb | Bevat de bronbestanden van de front-end stateless service samen met het gerelateerde gradle-buildbestand. |
| build.gradle | Gradle-bestand dat wordt gebruikt voor het beheren van het project. |
| settings.gradle | Bevat de namen van Gradle-projecten in deze map. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>HTML en Javascript toevoegen aan de VotingWeb-service

Als u een gebruikersinterface wilt toevoegen die kan worden weergegeven door de stateless service, voeg dan een HTML-bestand toe. Dit HTML-bestand wordt vervolgens weergegeven door de lichtgewicht HTTP-server die is ingesloten in de stateless Java-service.

1. Vouw de map *VotingApplication* uit en ga naar de map *VotingWebPkg-VotingApplication-Code*.

2. Klik met de rechtermuisknop op de map *Code* en selecteer **New** > **Folder**.

3. Noem de map *wwwroot* en selecteer **Finish**.

    ![Een wwwroot-map maken voor Eclipse](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. Voeg een bestand toe aan **wwwroot** met de naam **index.html** en plak de volgende inhoud in dit bestand.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
            {},
            ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebjava-file"></a>Het bestand VotingWeb.java bijwerken

Open in het subproject **VotingWeb** het bestand *VotingWeb/src/statelessservice/VotingWeb.java*. De service **VotingWeb** is de gateway naar de stateless service en is verantwoordelijk voor het instellen van de communicatielistener voor de front-end-API.

Vervang de inhoud van de methode **createServiceInstanceListeners** in het bestand door de volgende en sla de wijzigingen op.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();

    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>Het bestand HTTPCommunicationListener.java toevoegen

De HTTP-communicatielistener fungeert als een domeincontroller die de HTTP-server instelt en de API's die stemacties definiëren weergeeft. Klik met de rechtermuisknop op het pakket *statelessservice* in de map *VotingWeb/src/statelessservice* en selecteer **New** > **File**.  Noem het bestand *HttpCommunicationListener.java* en selecteer **Finish**.

Vervang de bestandsinhoud door het volgende en sla uw wijzigingen op.  Verderop, in Het bestand HttpCommunicationListener.java bijwerken, wordt dit bestand gewijzigd voor het weergeven, lezen en schrijven van stemgegevens vanaf de back-endservice.  Op dit moment retourneert de listener eenvoudigweg de statische HTML-code voor de stem-app.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());

    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404;
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";
    private static final String ENCODING = "UTF-8";

    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0);
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }

        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();

                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }

                      fs.close();
                      os.close();
                    }
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */

        server.setExecutor(null);
        server.start();
    }

    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>De luisterpoort configureren

Wanneer de front-endservice VotingWeb-service is gemaakt, selecteert Service Fabric een poort voor de service om op te luisteren.  De VotingWeb-service fungeert als de front-end voor deze toepassing en accepteert extern verkeer, dus gaan we die service met een vaste en bekende poort verbinden. Open in Package Explorer *VotingApplication/VotingWebPkg/ServiceManifest.xml*.  Zoek de resource **Eindpunt** in de sectie **Resources** en wijzig de **Poort**-waarde naar 8080 (we blijven deze poort in de zelfstudie gebruiken). Als u de toepassing lokaal wilt implementeren en uitvoeren, moet de luisterende poort van de toepassing open zijn en beschikbaar zijn op uw computer. Plak het volgende codefragment in het element **ServiceManifest** (dat wil zeggen pal onder het element ```<DataPackage>```).

```xml
<Resources>
    <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to
            listen. Please note that if your service is partitioned, this port is shared with
            replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Een stateful back-endservice toevoegen aan de toepassing

Nu de basis van de Java web-API-service is voltooid, gaan we de stateful back-endservice voltooien.

Service Fabric biedt u de mogelijkheid om uw gegevens consistent en betrouwbaar rechtstreeks in uw service op te slaan met behulp van betrouwbare verzamelingen. Betrouwbare verzamelingen zijn een set maximaal beschikbare en betrouwbare verzamelingsklassen. Het gebruik van deze klassen is bekend bij iedereen die Java-verzamelingen gebruikt.

1. Klik in Package Explorer met de rechtermuisknop op **Voting** binnen het toepassingsproject en selecteer **Service Fabric** > **Add Service Fabric Service**.

2. Selecteer in het dialoogvenster **Add Service** de optie **Stateful Service**, geef de service de naam **VotingDataService** en selecteer **Add Service**.

    Nadat uw serviceproject is gemaakt, hebt u twee services in uw toepassing. Als u doorgaat met het bouwen van uw toepassing, kunt u op dezelfde manier meer services toevoegen. Elk kan onafhankelijke versies en upgrades hebben.

3. Eclipse maakt een serviceproject en geeft dit weer in Package Explorer.

    ![Eclipse Project Explorer](./media/service-fabric-tutorial-create-java-app/service-fabric-package-explorer-java.png)

### <a name="add-the-votingdataservicejava-file"></a>Het bestand VotingDataService.java toevoegen

Het bestand *VotingDataService.java* bevat de methoden die logica bevatten voor het ophalen, toevoegen en verwijderen van stemmen uit de betrouwbare verzamelingen. Voeg de volgende **VotingDataService**-klassemethoden toe aan het bestand *VotingDataService/src/statefulservice/VotingDataService.java*.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext;

import rpcmethods.VotingRPC;

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;

    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();

        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));

        return listeners;
    }

    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue());
            }

            tx.close();


        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(tempMap);
    }

    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1);

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;
                    return Integer.toString(numVotes);
                }
            }).get();

            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1);
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

}
```

De basis voor de front-end stateless service en de back-endservice is nu gemaakt.

## <a name="create-the-communication-interface-to-your-application"></a>De communicatie-interface naar uw toepassing maken

 De basis voor de front-end stateless service en de back-endservice is nu gemaakt. De front-end- en back-endservices maken beide gebruik van een interface met de naam VotingRPC die de bewerkingen van de stemtoepassing definieert. Deze interface is geïmplementeerd door de front-end- en back-endservice om externe procedureaanroepen (RPC) tussen de twee services in te schakelen. Omdat Eclipse geen ondersteuning biedt voor het toevoegen van Gradle-subprojecten, moet het pakket met deze interface handmatig worden toegevoegd.

1. Klik met de rechtermuisknop op het project **Voting** in de Package Explorer en selecteer **New** > **Folder**. Noem de map **VotingRPC/src/rpcmethods**.

    ![Een VotingRPC-pakket maken in Eclipse Package Explorer](./media/service-fabric-tutorial-create-java-app/create-voting-rpc-package-java.png)

3. Maak onder *Voting/VotingRPC/src/rpcmethods* een bestand met de naam *VotingRPC.java* en plak het volgende in het bestand **VotingRPC.java**. 

    ```java
    package rpcmethods;
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ```

4. Maak een leeg bestand met de naam *build.gradle* in de map *Voting/VotingRPC* en plak het volgende erin. Dit gradle-bestand wordt gebruikt om het jar-bestand te bouwen dat door de andere services wordt geïmporteerd. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}

        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }

    defaultTasks 'clean', 'jar'
    ```

5. Voeg aan het bestand *Voting/settings.gradle* een regel toe om het nieuw gemaakte project in de build op te nemen. 

    ```gradle
    include ':VotingRPC'
    ```

6. Vervang in het bestand *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* het opmerkingenblok door het volgende.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
    
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");

                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
    
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");

                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. Voeg de juiste importinstructie boven aan het bestand *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* toe. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

In deze fase wordt de functionaliteit voor de front-end-, back-end en RPC-interfaces voltooid. In de volgende fase worden de Gradle-scripts op de juiste wijze geconfigureerd voordat u een Service Fabric-cluster implementeert. 

## <a name="walk-through-the-voting-sample-application"></a>Stapsgewijs door de voorbeeldstemtoepassing gaan
De stemtoepassing bestaat uit twee services:
- Web-front-endservice (VotingWeb): een Java web-front-endservice die de webpagina ondersteunt en API's beschikbaar stelt om te communiceren met de back-endservice.
- Back-endservice (VotingDataService): een Java-webservice die methoden definieert die worden aangeroepen via externe procedureaanroepen (RPC) om stemmen vast te leggen.

![Voorbeel van Voting-diagram](./media/service-fabric-tutorial-create-java-app/walkthrough-java-voting.png)

Wanneer u een actie uitvoert in de toepassing (item toevoegen, stemmen, item verwijderen), gebeurt het volgende:
1. Een JavaScript verzendt de juiste aanvraag als een HTTP-aanvraag naar de web-API in de web-front-endservice.

2. De front-endwebservice maakt gebruik van de functionaliteit van de ingebouwde service voor externe toegang van Service Fabric om de aanvraag te zoeken en door te sturen naar de back-endservice. 

3. De back-endservice definieert methoden waarmee het resultaat in een betrouwbare woordenlijst wordt bijgewerkt. De inhoud van deze betrouwbare woordenlijst wordt gerepliceerd naar meerdere knooppunten binnen het cluster en opgeslagen op schijf. Alle gegevens van de toepassing worden opgeslagen in het cluster. 

## <a name="configure-gradle-scripts"></a>Gradle-scripts configureren 

In deze sectie worden de Gradle-scripts voor het project geconfigureerd. 

1. Vervang de inhoud van het bestand *Voting/build.gradle* door het volgende.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. Vervang de inhoud van het bestand *Voting/VotingWeb/build.gradle* door het volgende.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

3. Vervang de inhoud van het bestand *Voting/VotingDataService/build.gradle*. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>De toepassing implementeren in het lokale cluster

De toepassing is nu gereed om te worden geïmplementeerd in een lokaal Service Fabric-cluster.

1. Klik met de rechtermuisknop op het project **Voting** in de Package Explorer en selecteer **Service Fabric** > **Build Application** om uw toepassing te bouwen.

2. Voer uw lokale Service Fabric-cluster uit. Deze stap is afhankelijk van uw ontwikkelomgeving (Mac of Linux).

    Als u een Mac gebruikt, voert u het lokale cluster uit met de volgende opdracht. Vervang de opdracht uit de parameter **-v** door het pad naar uw eigen werkruimte.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
    ```
    Zie meer gedetailleerde instructies in de [Handleiding voor het instellen van OS X.](service-fabric-get-started-mac.md)

    Als u met een Linux-machine werkt, start u het lokale cluster met de volgende opdracht: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Zie meer gedetailleerde instructies in de [Handleiding voor het instellen van Linux.](service-fabric-get-started-linux.md)

4. Klik in de Package Explorer voor Eclipse met de rechtermuisknop op het project **Voting** en selecteer **Service Fabric** > **Publish Application** 
5. Selecteer in het venster **Publish Application** de optie **Local.json** in de vervolgkeuzelijst en selecteer **Publish**.
6. Ga naar uw webbrowser en open \/http://localhost:8080 om uw toepassing die wordt uitgevoerd in het lokale Service Fabric-cluster weer te geven. 

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Java-service als betrouwbare stateful service maken
> * Java-service als betrouwbare stateless service maken
> * Een Java-interface toevoegen voor het afhandelen van de externe procedureaanroepen (RPC) tussen uw services
> * Uw Gradle-scripts configureren
> * Uw toepassing bouwen en implementeren naar een lokaal Service Fabric-cluster

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Fouten in toepassingen opsporen en vastleggen in een lokaal cluster](service-fabric-tutorial-debug-log-local-cluster.md)
