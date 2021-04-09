---
title: Naslag informatie over Azure Service Fabric mesh maven
description: Bevat de referentie voor het gebruik van de Maven-invoeg toepassing voor Service Fabric mesh
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.custom: devx-track-java
ms.openlocfilehash: dd42752dc7ef2c7686073b7a2d780fe9356425c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625580"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven-invoeg toepassing voor Service Fabric mesh

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Java-SDK
- Maven
- Azure CLI met net-extensie
- Service Fabric-CLI

## <a name="goals"></a>Doelstellingen

### `azure-sfmesh:init`
- Hiermee maakt u een `servicefabric` map met een `appresources` map die het `application.yaml` bestand bevat. 

### `azure-sfmesh:addservice`
- Hiermee maakt u een map in de `servicefabric` map met de naam van de service en maakt u het yaml-bestand van de service. 

### `azure-sfmesh:addnetwork`
- Hiermee wordt een `network` yaml met de gegeven netwerk naam in de `appresources` map gegenereerd 

### `azure-sfmesh:addgateway`
- Hiermee wordt een `gateway` yaml met de naam van de door gegeven gateway in de `appresources` map gegenereerd 

#### `azure-sfmesh:addvolume`
- Hiermee wordt een `volume` yaml met de naam van het volume in de `appresources` map gegenereerd.

### `azure-sfmesh:addsecret`
- Hiermee wordt een `secret` yaml met de naam van het geheim in de `appresources` map gegenereerd 

### `azure-sfmesh:addsecretvalue`
- Hiermee wordt een `secretvalue` yaml met de naam van de geheime en geheime waarde in de `appresources` map gegenereerd 

### `azure-sfmesh:deploy`
- Hiermee worden de yamls samengevoegd vanuit de `servicefabric` map en wordt een Azure Resource Manager-sjabloon JSON gemaakt in de huidige map.
- Hiermee worden alle resources naar de Azure Service Fabric-netwerk omgeving geïmplementeerd 

### `azure-sfmesh:deploytocluster`
- Hiermee maakt u een map ( `meshDeploy` ) die de implementatie-jsons bevat die zijn gegenereerd op basis van yamls die van toepassing zijn op service Fabric clusters
- Hiermee worden alle resources naar het Service Fabric cluster geïmplementeerd
 

## <a name="usage"></a>Gebruik

Als u de Maven-invoegtoepassing in uw Maven Java-app wilt gebruiken, voegt u het volgende codefragment aan het bestand pom.xml toe:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Algemene configuratie

De Maven-invoeg toepassing biedt momenteel geen ondersteuning voor algemene configuraties van Maven-invoeg toepassingen voor Azure.

## <a name="how-to"></a>Uitleg:

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Maven-project initialiseren voor Azure Service Fabric mesh
Voer de volgende opdracht uit om het YAML-bestand van de toepassings bron te maken.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Hiermee maakt u een map met `servicefabric->appresources` de naam in de hoofdmap die een toepassings yaml bevat `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Resource toevoegen aan uw toepassing

#### <a name="add-a-new-network-to-your-application"></a>Een nieuw netwerk aan uw toepassing toevoegen
Voer de onderstaande opdracht uit om een netwerk bron yaml te maken. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Hiermee maakt u een netwerk-YAML in de map met de `servicefabric->appresources` naam `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Een nieuwe service toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een service yaml te maken. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Hiermee maakt u een service-YAML in `servicefabric->helloworldservice` `service_helloworldservice` de map met de naam die verwijst naar `helloworldservicenetwork` & de `helloworldserver` app
- De service luistert op poort 8080
- De service gebruikt ***helloworldserver: nieuwste*** als container installatie kopie.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Een nieuwe gateway resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een gateway resource yaml te maken. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Hiermee maakt u een nieuwe gateway YAML in de map met de `servicefabric->appresources` naam `gateway_helloworldgateway`
- Verwijst naar `helloworldservicelistener` de service-listener die luistert naar aanroepen vanaf deze gateway. Verwijst ook naar de `helloworldservice` als de service, `helloworldservicenetwork` als het netwerk en `helloworldserver` als de toepassing. 
- Luistert naar aanvragen op poort 80

#### <a name="add-a-new-volume-to-your-application"></a>Een nieuw volume toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een volume resource yaml te maken. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Hiermee maakt u een volume YAML in de map met de `servicefabric->appresources` naam `volume_vol1`
- Hiermee stelt u de eigenschappen in voor de vereiste para meters, `volumeAccountKey` en `volumeShareName` als hierboven
- Voor meer informatie over het verwijzen naar dit gemaakte volume gaat u naar de volgende, [implementeert u de app met Azure files volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Een nieuwe geheime resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een geheime resource yaml te maken. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Hiermee maakt u een geheime YAML in de map met de `servicefabric->appresources` naam `secret_secret1`
- Voor meer informatie over het verwijzen naar dit gemaakte geheim gaat u naar het volgende: [geheimen beheren](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Een nieuwe secretvalue-resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een secretvalue-resource yaml te maken. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Maak een secretvalue-YAML in de map met de `servicefabric->appresources` naam `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

Met behulp van doel stellingen `azure-sfmesh:deploytocluster` kunt u de toepassing lokaal uitvoeren met behulp van de volgende opdracht:

```cmd
mvn azure-sfmesh:deploytocluster
```

Standaard implementeert dit doel resources voor het lokale cluster. Als u implementeert naar een lokaal cluster, wordt ervan uitgegaan dat u een lokale Service Fabric cluster actief hebt. Het lokale Service Fabric cluster voor bronnen wordt momenteel alleen ondersteund in [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Maakt JSONs van yamls die van toepassing zijn op Service Fabric clusters
- Implementeert vervolgens naar het cluster eindpunt

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Toepassing implementeren in azure Service Fabric net

U kunt met behulp van doel einden `azure-sfmesh:deploy` implementeren naar service Fabric-netwerk omgeving door de onderstaande opdracht uit te voeren:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Hiermee maakt u een resource groep met de naam `todoapprg` als deze niet bestaat.
- Hiermee maakt u een Azure Resource Manager sjabloon JSON door de YAMLs samen te voegen. 
- Implementeert de JSON naar de Azure Service Fabric-netwerk omgeving.
