---
title: Netwerk modi configureren voor container Services
description: Meer informatie over het instellen van de verschillende netwerk modi die worden ondersteund door Azure Service Fabric.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: e6174f35bd54b3ca0b2c5240a663369350b30ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86241893"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric container netwerk modi

Een Azure Service Fabric-cluster voor container Services maakt standaard gebruik van **NAT** -netwerk modus. Wanneer er meer dan één container service op dezelfde poort luistert en de NAT-modus wordt gebruikt, kunnen er implementatie fouten optreden. Service Fabric biedt **Open** -netwerk modus (versie 5,7 en hoger) ter ondersteuning van meerdere container Services die Luis teren op dezelfde poort. In de modus open heeft elke container service een intern, dynamisch toegewezen IP-adres dat ondersteuning biedt voor meerdere services die op dezelfde poort Luis teren.  

Als u één container service met een statisch eind punt in uw service manifest hebt, kunt u nieuwe services maken en verwijderen met behulp van de open modus zonder implementatie fouten. Hetzelfde docker-Compose. yml-bestand kan ook worden gebruikt met statische poort toewijzingen voor het maken van meerdere services.

Wanneer een container service opnieuw wordt gestart of naar een ander knoop punt in het cluster wordt verplaatst, wordt het IP-adres gewijzigd. Daarom wordt het gebruik van het dynamisch toegewezen IP-adres niet aanbevolen voor het detecteren van container Services. Alleen de Service Fabric Naming Service of de DNS-service moet worden gebruikt voor service detectie. 

>[!WARNING]
>In Azure is een totaal van 65.356 IP-adressen per virtueel netwerk toegestaan. De som van het aantal knoop punten en het aantal container service-instanties (die gebruikmaken van de open modus) mag niet groter zijn dan 65.356 IP-adressen in een virtueel netwerk. Voor scenario's met hoge dichtheid wordt u aangeraden NAT-netwerk modus te activeren. Daarnaast hebben andere afhankelijkheden, zoals de load balancer, andere [beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) om rekening mee te houden. Momenteel zijn Maxi maal 50 Ip's per knoop punt getest en stabiel bewezen. 
>

## <a name="set-up-open-networking-mode"></a>Open-netwerk modus instellen

1. Stel de Azure Resource Manager sjabloon in. Schakel in de sectie **fabricSettings** van de cluster bron de DNS-service en de IP-provider in: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Stel de sectie netwerk profiel in van de resource voor de Schaalset van de virtuele machine. Hierdoor kunnen meerdere IP-adressen worden geconfigureerd op elk knoop punt van het cluster. In het volgende voor beeld worden vijf IP-adressen per knoop punt ingesteld voor een Windows-of Linux-Service Fabric cluster. U kunt vijf service-exemplaren laten Luis teren op de poort op elk knoop punt. Als u wilt dat de vijf Ip's toegankelijk zijn vanaf de Azure Load Balancer, moet u de vijf Ip's registreren in de Azure Load Balancer back-end-adres groep, zoals hieronder wordt weer gegeven.  U moet ook de variabelen toevoegen aan de bovenkant van uw sjabloon in de sectie variabelen.

    Deze sectie toevoegen aan variabelen:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Voeg deze sectie toe aan de resource voor de Schaalset voor virtuele machines:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Voor Windows-clusters moet u een Azure Network Security Group (NSG)-regel instellen waarmee poort-UDP/53 voor het virtuele netwerk wordt geopend met de volgende waarden:

   |Instelling |Waarde |
   | --- | --- |
   |Prioriteit |2000 |
   |Name |Custom_Dns  |
   |Bron |VirtualNetwork |
   |Doel | VirtualNetwork |
   |Service | DNS (UDP/53) |
   |Bewerking | Toestaan  |

4. Geef de netwerk modus op in het manifest van de toepassing voor elke service: `<NetworkConfig NetworkType="Open">` . De **Open** -netwerk modus resulteert in de service die een toegewezen IP-adres krijgt. Als er geen modus is opgegeven, wordt de standaard waarde **NAT** modus voor de service gebruikt. In het volgende voor beeld van een manifest kunnen de- `NodeContainerServicePackage1` en- `NodeContainerServicePackage2` Services elk Luis teren op dezelfde poort (beide services Luis teren `Endpoint1` ). Wanneer de open-netwerk modus is opgegeven, `PortBinding` kunnen er geen configuraties worden opgegeven.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    U kunt verschillende netwerk modi combi neren en vergelijken tussen services binnen een toepassing voor een Windows-cluster. Sommige services kunnen de modus openen gebruiken terwijl andere de NAT-modus gebruiken. Wanneer een service is geconfigureerd voor het gebruik van de NAT-modus, moet de poort waarop de service luistert, uniek zijn.

    >[!NOTE]
    >Op Linux-clusters wordt het combi neren van netwerk modi voor verschillende services niet ondersteund. 
    >

5. Als de modus **openen** is geselecteerd, moet de **eindpunt** definitie in het service manifest expliciet verwijzen naar het code pakket dat overeenkomt met het eind punt, zelfs als het service pakket slechts één code pakket bevat. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Voor Windows wordt het geopende netwerk opnieuw gemaakt door een VM opnieuw op te starten. Dit is om een onderliggend probleem in de netwerk stack te verhelpen. Het standaard gedrag is het opnieuw maken van het netwerk. Als dit gedrag moet worden uitgeschakeld, kan de volgende configuratie worden gebruikt, gevolgd door een configuratie-upgrade.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Volgende stappen
* [Inzicht krijgen in het Service Fabric-toepassingsmodel](service-fabric-application-model.md)
* [Meer informatie over de bronnen voor het Service Fabric-service manifest](./service-fabric-service-manifest-resources.md)
* [Een Windows-container implementeren voor het Service Fabric op Windows Server 2016](service-fabric-get-started-containers.md)
* [Een docker-container implementeren voor Service Fabric op Linux](service-fabric-get-started-containers-linux.md)
