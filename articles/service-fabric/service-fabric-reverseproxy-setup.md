---
title: Reverse proxy voor Azure Service Fabric instellen
description: Meer informatie over het instellen en configureren van de reverse proxy service voor een Azure Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: f8a9025a50b2815f0e6030e7baf317b261c8c462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86256340"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Een omgekeerde proxy instellen en configureren in azure Service Fabric
Omgekeerde proxy is een optionele Azure Service Fabric-service die micro services die worden uitgevoerd in een Service Fabric cluster kan detecteren en communiceren met andere services met http-eind punten. Zie [reverse proxy in Azure service Fabric](service-fabric-reverseproxy.md)voor meer informatie. In dit artikel wordt beschreven hoe u een omgekeerde proxy in uw cluster instelt en configureert. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Omgekeerde proxy inschakelen met behulp van Azure Portal

Azure Portal biedt een optie om omgekeerde proxy in te scha kelen wanneer u een nieuw Service Fabric-cluster maakt. U kunt een bestaand cluster niet upgraden voor het gebruik van een reverse proxy via de portal. 

Voor het configureren van een omgekeerde proxy wanneer u [een cluster met Azure Portal maakt](./service-fabric-cluster-creation-via-portal.md), moet u het volgende doen:

1. Selecteer in **stap 2: cluster configuratie**, onder **configuratie van knooppunt type** de optie **omgekeerde proxy inschakelen**.

   ![Omgekeerde proxy inschakelen op de portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. Beschrijving Als u een beveiligde omgekeerde proxy wilt configureren, moet u een TLS/SSL-certificaat configureren. In **stap 3: beveiliging**, op **cluster beveiligings instellingen configureren** onder **configuratie type**, selecteert u **aangepast**. Selecteer vervolgens onder **omgekeerde proxy SSL-certificaat** de optie **een SSL-certificaat voor reverse proxy toevoegen** en voer uw certificaat details in.

   ![Beveiligde omgekeerde proxy configureren op de portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Als u ervoor kiest om de omgekeerde proxy niet te configureren met een certificaat wanneer u het cluster maakt, kunt u dit later doen via de Resource Manager-sjabloon voor de resource groep van het cluster. Zie voor meer informatie [reverse proxy inschakelen via Azure Resource Manager sjablonen](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Omgekeerde proxy inschakelen via Azure Resource Manager sjablonen

Voor clusters op Azure kunt u de sjabloon Azure Resource Manager gebruiken om de omgekeerde proxy in Service Fabric in te scha kelen. U kunt omgekeerde proxy inschakelen wanneer u het cluster maakt of inschakelt door het cluster op een later tijdstip bij te werken. 

Voor een nieuw cluster kunt u [een aangepaste Resource Manager-sjabloon maken](service-fabric-cluster-creation-via-arm.md) , maar u kunt ook een voorbeeld sjabloon gebruiken. 

U kunt voor beelden van Resource Manager-sjablonen vinden die u kunnen helpen bij het configureren van een beveiligde omgekeerde proxy voor een Azure-cluster in de voor [beelden van beveiligde reverse proxy-voorbeeld sjablonen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) op github. Raadpleeg [https reverse proxy configureren in een beveiligd cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) in het Leesmij-bestand voor instructies en de sjablonen die moeten worden gebruikt voor het configureren van een beveiligde reverse proxy met een certificaat en voor het afhandelen van certificaat overschakeling.

Voor een bestaand cluster kunt u de Resource Manager-sjabloon voor de resource groep van het cluster exporteren met behulp van de [Azure Portal](../azure-resource-manager/templates/export-template-portal.md), [Power shell](../azure-resource-manager/management/manage-resources-powershell.md)of de [Azure cli](../azure-resource-manager/management/manage-resources-cli.md).

Nadat u een resource manager-sjabloon hebt, kunt u de reverse proxy inschakelen met de volgende stappen:

1. Definieer een poort voor de omgekeerde proxy in het [gedeelte para meters](../azure-resource-manager/templates/template-syntax.md) van de sjabloon.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Geef de poort voor elk van de NodeType-objecten op in de [sectie resource type](../azure-resource-manager/templates/template-syntax.md) [**micro soft. ServiceFabric/clusters**](/azure/templates/microsoft.servicefabric/clusters) .

    De poort wordt aangeduid met de parameter naam reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Als u TLS/SSL-certificaten op de poort voor de omgekeerde proxy wilt configureren, voegt u het certificaat toe aan de eigenschap ***reverseProxyCertificate** _ in de sectie van het bron type *micro soft. ServiceFabric/clusters* *  [](../azure-resource-manager/templates/template-syntax.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Ondersteuning voor een omgekeerd proxy certificaat dat verschilt van het cluster certificaat
 Als het omgekeerde proxy certificaat afwijkt van het certificaat dat het cluster beveiligt, moet het eerder opgegeven certificaat zijn geïnstalleerd op de virtuele machine en worden toegevoegd aan de toegangs beheer lijst (ACL), zodat Service Fabric het kan openen. Dit kunt u doen in het [gedeelte resource type](../azure-resource-manager/templates/template-syntax.md) [**micro soft. Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets) . Voeg het certificaat toe aan de osProfile voor installatie. In de sectie uitbrei ding van de sjabloon kan het certificaat in de ACL worden bijgewerkt.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Wanneer u certificaten gebruikt die verschillen van het cluster certificaat om de omgekeerde proxy in een bestaand cluster in te scha kelen, installeert u het omgekeerde proxy certificaat en werkt u de ACL bij op het cluster voordat u de omgekeerde proxy inschakelt. Voltooi de implementatie van de [Azure Resource Manager-sjabloon](service-fabric-cluster-creation-via-arm.md) met behulp van de eerder genoemde instellingen voordat u een implementatie start om de omgekeerde proxy in stap 1-3 in te scha kelen.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Omgekeerde proxy inschakelen op zelfstandige clusters

Voor zelfstandige clusters schakelt u omgekeerde proxy in de ClusterConfig.jsin. U kunt omgekeerde proxy inschakelen bij het maken van een cluster of door de configuratie van een bestaand cluster bij te werken. Zie [zelfstandige cluster instellingen](./service-fabric-cluster-manifest.md)voor meer informatie over de instellingen die beschikbaar zijn in ClusterConfig.jsop bestanden.

In de volgende stappen ziet u de instellingen die u moet gebruiken voor het inschakelen van omgekeerde proxy en, optioneel, voor het beveiligen van de omgekeerde proxy met een X. 509-certificaat. 

1. Als u omgekeerde proxy wilt inschakelen, stelt u de waarde **reverseProxyEndpointPort** in voor het knooppunt type onder **Eigenschappen** in de cluster configuratie. In de volgende JSON wordt het instellen van de omgekeerde proxy eindpunt poort ingesteld op 19081 voor knoop punten met het type ' NodeType0 ':

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. Beschrijving Configureer voor een beveiligde omgekeerde proxy een certificaat in het gedeelte **beveiliging** onder **Eigenschappen**. 
   - Voor een ontwikkel-of test omgeving kunt u de instelling **ReverseProxyCertificate** gebruiken:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Voor een productie omgeving wordt de **ReverseProxyCertificateCommonNames** -instelling aanbevolen:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Zie voor meer informatie over het configureren en beheren van certificaten voor een zelfstandig cluster, en meer details over het configureren van certificaten die worden gebruikt voor het beveiligen van reverse proxy, de [beveiliging op basis van x509-certificaten](./service-fabric-windows-cluster-x509-security.md).

Nadat u uw ClusterConfig.jsin het bestand hebt gewijzigd om omgekeerde proxy in te scha kelen, volgt u de instructies in [een upgrade van de cluster configuratie](service-fabric-cluster-config-upgrade-windows-server.md) om de wijzigingen in uw cluster te pushen.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Reverse proxy beschikbaar maken op een open bare poort via Azure Load Balancer

Als u de omgekeerde proxy van buiten een Azure-cluster wilt adresseren, stelt u Azure Load Balancer regels en een Azure-status test in voor de omgekeerde proxy poort. Deze stappen kunnen worden uitgevoerd op elk gewenst moment nadat u het cluster hebt gemaakt met behulp van de Azure Portal of de Resource Manager-sjabloon. 

> [!WARNING]
> Wanneer u de poort van de reverse proxy in Load Balancer configureert, zijn alle micro Services in het cluster die een HTTP-eind punt beschikbaar maken, adresseerbaar van buiten het cluster. Dit betekent dat de micro services die intern bedoeld zijn, kunnen worden gedetecteerd door een bepaalde kwaadwillende gebruiker. Dit biedt mogelijk ernstige beveiligings problemen die kunnen worden misbruikt. bijvoorbeeld:
>
> * Een kwaadwillende gebruiker kan een DOS-aanval (Denial of service) starten door herhaaldelijk een interne service aan te roepen die geen voldoende harde kwets baarheid heeft.
> * Een kwaadwillende gebruiker kan misvormde pakketten leveren aan een interne service, wat leidt tot onbedoeld gedrag.
> * Een service die als intern is bedoeld, kan persoonlijke of gevoelige informatie retour neren die niet bedoeld is om te worden blootgesteld aan services buiten het cluster, waardoor deze gevoelige informatie beschikbaar wordt gesteld aan een kwaadwillende gebruiker. 
>
> Zorg ervoor dat u de mogelijke beveiligings implicaties voor uw cluster en de apps die erop worden uitgevoerd, volledig begrijpt en verhelpt voordat u de omgekeerde proxy poort openbaar maakt. 
>

Als u een omgekeerde proxy openbaar wilt maken voor een zelfstandig cluster, is de manier waarop u dit doet, afhankelijk van het systeem dat als host fungeert voor het cluster en valt buiten het bereik van dit artikel. De voorafgaande waarschuwing over het openbaar maken van een omgekeerde proxy is echter nog steeds van toepassing.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>De omgekeerde proxy beschikbaar maken met behulp van Azure Portal 

1. Klik op de Azure Portal op de resource groep voor uw cluster en klik vervolgens op de load balancer voor uw cluster.
2. Als u een status test wilt toevoegen voor de omgekeerde proxy poort, klikt u in het linkerdeel venster van het load balancer venster onder **instellingen** op **status controles**. Klik vervolgens op **toevoegen** boven in het venster status controles en voer Details in voor de omgekeerde proxy poort en klik vervolgens op **OK**. De omgekeerde proxy poort is standaard 19081, tenzij u deze hebt gewijzigd tijdens het maken van het cluster.

   ![Health probe reverse proxy configureren](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Als u een Load Balancer regel wilt toevoegen om de omgekeerde proxy poort zichtbaar te maken, klikt u in het linkerdeel venster van het venster load balancer onder **instellingen** op taakverdelings **regels**. Klik vervolgens boven aan het venster taakverdelings regels op **toevoegen** en voer Details in voor de omgekeerde proxy poort. Zorg ervoor dat u de **poort** waarde instelt op de poort waarop u de omgekeerde proxy wilt weer geven, de waarde van de **back-endserver** naar de poort die u instelt bij het inschakelen van omgekeerde proxy en de **status test** waarde voor de status test die u in de vorige stap hebt geconfigureerd. Stel zo nodig andere velden in en klik op **OK**.

   ![load balancer regel voor reverse proxy configureren](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>De omgekeerde proxy beschikbaar stellen via Resource Manager-sjablonen

De volgende JSON verwijst naar dezelfde sjabloon die wordt gebruikt bij het inschakelen van een [omgekeerde proxy via Azure Resource Manager sjablonen](#enable-reverse-proxy-via-azure-resource-manager-templates). Raadpleeg die sectie van het document voor informatie over het maken van een resource manager-sjabloon of het exporteren van een sjabloon voor een bestaand cluster.  De wijzigingen worden doorgevoerd in de [sectie resource type](../azure-resource-manager/templates/template-syntax.md) [**micro soft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers) .

```json
{
    "apiVersion": "[variables('lbApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    ...
    ...
    "loadBalancingRules": [
        ...
        {
            "name": "LBSFReverseProxyRule",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('SFReverseProxyPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"
                },
                "frontendPort": "[parameters('SFReverseProxyPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                },
                "protocol": "tcp"
            }
        }
    ],
    "probes": [
        ...
        {
            "name": "SFReverseProxyProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port":     "[parameters('SFReverseProxyPort')]",
                "protocol": "tcp"
            }
        }  
    ]
}
```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Het gedrag van omgekeerde proxy's aanpassen met behulp van infrastructuur instellingen

U kunt het gedrag van reverse proxy aanpassen via infrastructuur instellingen in de Resource Manager-sjabloon voor clusters die worden gehost in azure of in het ClusterConfig.jsbestand voor zelfstandige clusters. Instellingen voor het beheer van het gedrag van omgekeerde proxy's bevinden zich in de [**toepassings Gateway/http-**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sectie in de sectie **fabricSettings** van het gedeelte cluster **Eigenschappen** . 

U kunt bijvoorbeeld de waarde van **DefaultHttpRequestTimeout** instellen om de time-out voor aanvragen voor de omgekeerde proxy in te stellen op 180 seconden, zoals in de volgende JSON:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Zie [cluster instellingen aanpassen met Resource Manager-sjablonen](service-fabric-cluster-config-upgrade-azure.md)voor meer informatie over het bijwerken van infrastructuur instellingen voor Azure-clusters. Zie [cluster instellingen voor zelfstandige clusters aanpassen](service-fabric-cluster-config-upgrade-windows-server.md)voor zelfstandige clusters. 

Verschillende infrastructuur instellingen worden gebruikt voor het opzetten van beveiligde communicatie tussen reverse proxy en services. Zie [verbinding maken met een beveiligde service met de reverse proxy](service-fabric-reverseproxy-configure-secure-communication.md)voor meer informatie over deze instellingen.

## <a name="next-steps"></a>Volgende stappen
* [Door sturen naar beveiligde HTTP-service instellen met de omgekeerde proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Zie [toepassings Gateway/http in het gedeelte customize service Fabric cluster Settings](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)(Engelstalig) voor meer informatie over de configuratie opties voor omgekeerde proxy's.
