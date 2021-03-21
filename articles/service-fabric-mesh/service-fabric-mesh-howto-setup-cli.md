---
title: De Azure Service Fabric mesh-CLI instellen
description: De Service Fabric Mesh CLI (opdrachtregelinterface) is vereist voor het implementeren en beheren van resources, zowel lokaal als in Azure Service Fabric Mesh. Zo kunt u het instellen.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: f8a9c26e65ef911ad85806c72c7946947379ab72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613338"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI instellen

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

De Service Fabric Mesh CLI (opdrachtregelinterface) is vereist voor het implementeren en beheren van resources, zowel lokaal als in Azure Service Fabric Mesh. Zo kunt u het instellen.

Er kunnen drie typen CLI worden gebruikt. Ze staan in de tabel hieronder.

| CLI Module | Doelomgeving |  Beschrijving | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | De primaire CLI, waarmee u uw toepassingen kunt implementeren en resources beheren in de Azure Service Fabric Mesh-omgeving. 
| sfctl | Lokale clusters | De Service Fabric CLI waarmee u Service Fabric-resources in lokale clusters kunt implementeren en testen.  
| Maven CLI | Lokale clusters en Azure Service Fabric Mesh | Een wrapper rond `az mesh` en `sfctl` die Java-Ontwikkel aars in staat stelt een bekende opdracht regel ervaring te gebruiken voor lokale en Azure-ontwikkel ervaring.  

Voor de preview is de Azure Service Fabric-NET CLI geschreven als een uitbreiding voor Azure CLI. U kunt deze installeren in de Azure Cloud Shell of een lokale installatie van Azure CLI. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.67 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>De Azure Service Fabric Mesh CLI installeren

Als u dit nog niet hebt gedaan, installeert u de Azure Service Fabric mesh CLI-extensie module met de volgende opdracht: 
 
```azurecli-interactive
az extension add --name mesh
```

Als deze al is geïnstalleerd, werkt u uw bestaande Azure Service Fabric mesh CLI-module bij met de volgende opdracht:

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>Service Fabric CLI (sfctl) installeren 

Volg de instructies in [Service Fabric CLI instellen](../service-fabric/service-fabric-cli.md). De module **sfctl** kan worden gebruikt om toepassingen op basis van het resourcemodel in Service Fabric-clusters op de lokale computer te implementeren. 

## <a name="install-the-maven-cli"></a>Maven CLI installeren 

Als u de Maven CLI wilt gebruiken, moet het volgende op de computer zijn geïnstalleerd: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh): voor Azure Service Fabric Mesh 
* SFCTL (sfctl): voor lokale clusters 

De Maven CLI voor Service Fabric is nog in preview. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Lees het [naslagmateriaal over Maven CLI](service-fabric-mesh-reference-maven.md) voor uitgebreide informatie over het gebruik.

## <a name="next-steps"></a>Volgende stappen

U kunt ook uw [Windows-ontwikkelomgeving](service-fabric-mesh-howto-setup-developer-environment-sdk.md) instellen.

Zoek antwoorden op [veelgestelde vragen en problemen](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
