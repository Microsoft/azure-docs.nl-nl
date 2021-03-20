---
title: Een Service Fabric-cluster maken waarop Windows in Azure wordt uitgevoerd
description: In deze zelfstudie leert u hoe u een Windows Service Fabric-cluster implementeert in een virtueel Azure-netwerk en -netwerkbeveiligingsgroep met behulp van PowerShell.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a7390858e55a456ec5fb2f851be1a7443be97082
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245038"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Zelfstudie: Een Service Fabric-cluster met Windows implementeren in een virtueel Azure-netwerk

Deze zelfstudie is deel één van een serie. U leert hoe u een Azure Service Fabric-cluster met Windows in een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md) en een [netwerkbeveiligingsgroep](../virtual-network/virtual-network-vnet-plan-design-arm.md) implementeert met behulp van PowerShell en een sjabloon. Wanneer u klaar bent, wordt er in de cloud een cluster uitgevoerd waarin u toepassingen kunt implementeren. Zie [Een beveiligd Linux-cluster maken in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) als u een Linux-cluster wilt maken met behulp van Azure CLI.

In deze zelfstudie wordt een productiescenario beschreven. Zie [Een testcluster maken](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) als u voor testdoeleinden een kleiner cluster wilt maken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een VNET maken in Azure met behulp van PowerShell
> * Een sleutelkluis maken en een certificaat uploaden
> * Azure Active Directory-verificatie instellen
> * Verzameling met diagnostische gegevens configureren
> * EventStore-service instellen
> * Azure Monitor-logboeken instellen
> * Een beveiligd Service Fabric-cluster maken in Azure PowerShell
> * Het cluster beveiligen met een X.509-certificaat
> * Verbinding maken met het cluster met behulp van PowerShell
> * Een cluster verwijderen

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een beveiligd cluster maken in Azure
> * [Een cluster bewaken](service-fabric-tutorial-monitor-cluster.md)
> * [Een cluster in- of uitschalen](service-fabric-tutorial-scale-cluster.md)
> * [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)
> * [Een cluster verwijderen](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* Installeer de [Service Fabric SDK en PowerShell-module](service-fabric-get-started.md).
* Installeer [Azure PowerShell](/powershell/azure/install-az-ps).
* Bekijk de belangrijkste concepten van [Azure-clusters](service-fabric-azure-clusters-overview.md).
* Een implementatie van een productiecluster [plannen en voorbereiden](service-fabric-cluster-azure-deployment-preparation.md).

Met de volgende procedures wordt er een Service Fabric-cluster met zeven knooppunten gemaakt. Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te berekenen voor het uitvoeren van een Service Fabric-cluster in Azure.

## <a name="download-and-explore-the-template"></a>De sjabloon downloaden en verkennen

Download de volgende Azure Resource Manager-sjabloonbestanden:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Met deze sjabloon wordt een veilig cluster van zeven virtuele machines en drie knooppunttypen in een virtueel netwerk en een netwerkbeveiligingsgroep geïmplementeerd.  Andere voorbeeldsjablonen zijn te vinden op [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Met [azuredeploy.json][template] wordt een aantal resources geïmplementeerd, waaronder de volgende.

### <a name="service-fabric-cluster"></a>Service Fabric-cluster

In de resource **Microsoft.ServiceFabric/clusters** wordt een Windows-cluster geconfigureerd met de volgende kenmerken:

* Drie knooppunttypen.
* Vijf knooppunten van het primaire knooppunttype (te configureren in de sjabloonparameters) en een knooppunt van elk van de andere twee knooppunttypen.
* Besturingssysteem: Windows Server 2016 Datacenter met Containers (te configureren in de sjabloonparameters).
* Beveiligd met een certificaat (configureerbaar in de sjabloonparameters).
* [Omgekeerde proxy](service-fabric-reverseproxy.md) is ingeschakeld.
* [DNS-service](service-fabric-dnsservice.md) is ingeschakeld.
* Een bronzen [duurzaamheidsniveau](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) (configureerbaar in de sjabloonparameters).
* Een zilveren [betrouwbaarheidsniveau](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) (configureerbaar in de sjabloonparameters).
* Eindpunt van de clientverbinding: 19000 (configureerbaar in de sjabloonparameters).
* het eindpunt van de HTTP-gateway: 19080 (configureerbaar in de sjabloonparameters).

### <a name="azure-load-balancer"></a>Azure Load Balancer

In de resource **Microsoft.Network/loadBalancers** wordt een load balancer geconfigureerd. Er worden tests en regels ingesteld voor de volgende poorten:

* Eindpunt van de clientverbinding: 19000
* het eindpunt van de HTTP-gateway: 19080
* Toepassingspoort: 80
* Toepassingspoort: 443
* omgekeerde proxy van Service Fabric: 19081

Als er andere toepassingspoorten nodig zijn, moet u de resource **Microsoft.Network/loadBalancers** en de resource **Microsoft.Network/networkSecurityGroups** zo wijzigen dat verkeer kan binnenkomen.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtueel netwerk, subnet en netwerkbeveiligingsgroep

De namen van het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep zijn gedefinieerd in de sjabloonparameters. Adresruimten van het virtuele netwerk en subnet worden ook gedeclareerd in de sjabloonparameters en geconfigureerd in de resource **Microsoft.Network/virtualNetworks**:

* Adresruimte van virtueel netwerk: 172.16.0.0/20
* Service Fabric-subnetadresruimte: 172.16.2.0/23

De volgende regels voor binnenkomend verkeer worden ingeschakeld in de resource **Microsoft.Network/networkSecurityGroups**. U kunt de poortwaarden wijzigen door de sjabloonvariabelen te wijzigen.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Bereik van de kortstondige poort: 49152 tot 65534 (minimaal 256 poorten nodig).
* Poorten voor toepassingsgebruik: 80 en 443
* Bereik van de toepassingspoort: 49152 tot 65534 (voor service-naar-servicecommunicatie. Er zijn geen andere poorten geopend op de load balancer).
* Blokkeer alle andere poorten

Als er andere toepassingspoorten nodig zijn, moet u de resource **Microsoft.Network/loadBalancers** en de resource **Microsoft.Network/networkSecurityGroups** zo wijzigen dat verkeer kan binnenkomen.

### <a name="windows-defender"></a>Windows Defender
Het [Windows Defender-antivirusprogramma](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) is standaard geïnstalleerd en functioneert op Windows Server 2016. De gebruikersinterface is standaard geïnstalleerd op een aantal SKU's, maar is niet vereist. Voor elk knooppunttype dat/elke VM-schaalset die is aangegeven in de sjabloon, wordt de [Antimalware voor Azure-VM-extensie](../virtual-machines/extensions/iaas-antimalware-windows.md) gebruikt voor het uitsluiten van de Service Fabric-mappen en processen:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>De sjabloonparameters instellen

Het parameterbestand [azuredeploy.parameters.json][parameters] declareert veel waarden die worden gebruikt om het cluster en de bijbehorende resources te implementeren. De volgende parameters kunt u voor uw implementatie wijzigen:

**Parameter** | **Voorbeeldwaarde** | **Opmerkingen** 
|---|---|---|
|adminUserName|vmadmin| De gebruikersnaam van de beheerder van de cluster-VM's. [Vereisten voor gebruikersnaam voor VM](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Het wachtwoord van de beheerder van de cluster-VM's. [Vereisten voor wachtwoord voor VM](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| De naam van het cluster. Mag alleen letters en cijfers bevatten. De lengte kan minimaal 3 en maximaal 23 tekens zijn.|
|location|southcentralus| De locatie van het cluster. |
|certificateThumbprint|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de SHA1-waarde van de certificaatvingerafdruk in. Bijvoorbeeld 6190390162C988701DB5676EB81083EA608DCCF3.</p> |
|certificateUrlValue|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft. </p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de URL van het certificaat in. Bijvoorbeeld https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de waarde van de bronkluis in. Bijvoorbeeld /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Clientverificatie via Azure Active Directory instellen
Voor Service Fabric-clusters die zijn geïmplementeerd in een openbaar netwerk dat wordt gehost op Azure, is de aanbeveling voor wederzijdse verificatie van client-naar-knooppunt:
* Azure Active Directory gebruiken voor de clientidentiteit.
* Gebruik een certificaat voor de serveridentiteit en TLS-versleuteling van HTTP-communicatie.

Het instellen van Azure Active Directory (Azure AD) om clients te verifiëren voor een Service Fabric-cluster moet worden uitgevoerd voordat u [het cluster maakt](#createvaultandcert). Azure Active Directory maakt het beheren van toegang tot toepassingen door organisaties (bekend als tenants) mogelijk. 

Een Service Fabric-cluster biedt verschillende toegangspunten bij de management-functionaliteit, met inbegrip van de webconsoles [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) en [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Als gevolg hiervan, maakt u twee Azure Active Directory-toepassingen voor het beheren van toegang tot het cluster: een webtoepassing en een systeemeigen toepassing.  Nadat de toepassingen zijn gemaakt, wijst u gebruikers toe aan de rollen alleen-lezen en beheerder.

> [!NOTE]
> U moet de volgende stappen uitvoeren voordat u het cluster maakt. Omdat de scripts clusternamen en eindpunten verwachten, moeten de waarden worden gepland en geen waarden zijn die u al hebt gemaakt.

In dit artikel nemen we aan dat u al een tenant hebt gemaakt. Als u dit nog niet hebt gedaan, leest u eerst [Hoe kom ik aan een Azure Active Directory-tenant?](../active-directory/develop/quickstart-create-new-tenant.md)

Ter vereenvoudiging van de stappen voor het configureren van Azure Active Directory met een Service Fabric-cluster, is er een set Windows PowerShell-scripts gemaakt. [Download de scripts](https://github.com/Azure-Samples/service-fabric-aad-helpers) op uw computer.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure Active Directory-toepassingen maken en gebruikers toewijzen aan rollen
Maak twee Azure Active Directory-toepassingen voor het beheren van toegang tot het cluster: een webtoepassing en een systeemeigen toepassing. Nadat u de toepassingen voor uw cluster hebt gemaakt, wijst u uw gebruikers toe aan de [rollen die worden ondersteund door Service Fabric](service-fabric-cluster-security-roles.md): alleen-lezen en beheerder.

Voer `SetupApplications.ps1` uit, en geef de tenant-ID, de naam van het cluster en de antwoord-URL van de webtoepassing op als parameters. Geef gebruikersnamen en wachtwoorden voor de gebruikers op. Bijvoorbeeld:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Voor nationale clouds (bijvoorbeeld Azure Government, Azure China 21Vianet, Azure Duitsland), moet u ook parameter `-Location` opgeven.

U vindt uw *TenantId* ofwel de map-id in de [Azure-portal](https://portal.azure.com). Selecteer **Azure Active Directory** > **Eigenschappen** en kopieer de waarde **Map-id**.

De *Clusternaam* wordt gebruikt voor als prefix aan de Azure Active Directory-toepassingen die zijn gemaakt door het script. Deze hoeft niet precies overeen te komen met de naam van het daadwerkelijke cluster. Hierdoor is het alleen makkelijker om Azure AD-artefacten toe te wijzen aan de gebruikte Service Fabric-cluster.

*WebApplicationReplyUrl* wordt het standaardeindpunt dat Azure Active Directory retourneert naar uw gebruikers nadat ze klaar zijn aanmelden. Stel dit eindpunt in als het Service Fabric Explorer-eindpunt voor uw cluster, dit is standaard:

https://&lt;cluster_domain&gt;:19080/Explorer

U wordt gevraagd u aan te melden bij een account dat beheerdersrechten voor de Azure Active Directory-tenant heeft. Nadat u zich hebt aangemeld, maakt het script de web- en systeemeigen toepassingen voor uw Service Fabric-cluster. In de toepassingen van de tenant in [Azure Portal](https://portal.azure.com) ziet u twee nieuwe vermeldingen:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Met het script wordt de JSON afgedrukt die is vereist voor de Azure Resource Manager-sjabloon bij het maken van het cluster. Houd het PowerShell-venster dus geopend.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure Active Directory-configuratie toevoegen voor het gebruik van Azure Active Directory voor clienttoegang
Configureer in [azuredeploy.json][template] Azure Active Directory in de sectie **Microsoft.ServiceFabric/clusters**. Voeg parameters toe voor de tenant-id, de toepassings-id van het cluster en de clienttoepassings-id.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Voeg de parameterwaarden toe aan parameterbestand [azuredeploy.parameters.json][parameters]. Bijvoorbeeld:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Verzameling met diagnostische gegevens op het cluster configureren
Wanneer u een Service Fabric-cluster uitvoert, is het een goed idee om de logboeken van alle knooppunten op een centrale locatie te verzamelen. Met de logboeken op een centrale locatie kunt u problemen in uw cluster analyseren en oplossen, of problemen in de toepassingen en services die in dat cluster worden uitgevoerd.

Een van de methoden om logboeken te uploaden en te verzamelen, is door gebruik te maken van de uitbreiding Azure Diagnostics (WAD). Hiermee worden logboeken naar Azure Storage geüpload en het bevat tevens de mogelijkheid om logboeken te verzenden naar Azure Application Insights of Event Hubs. U kunt ook een extern proces gebruiken om de gebeurtenissen uit de opslag te lezen en ze in een analyseplatformproduct te plaatsen, zoals Azure Monitor-logboeken of een andere oplossing voor het parseren van logboeken.

Als u deze zelfstudie volgt, wordt de verzameling met diagnostische gegevens al geconfigureerd in de [sjabloon][template].

Als u een bestaand cluster hebt waarvoor geen diagnostische gegevens zijn geïmplementeerd, kunt u het toevoegen of bijwerken via de clustersjabloon. Wijzig de Resource Manager-sjabloon die wordt gebruikt voor het maken van het bestaande cluster of download de sjabloon vanuit de portal. Wijzig het bestand template.json door de volgende taken uit te voeren:

Voeg een nieuwe opslagresource toe aan de sectie met resources in de sjabloon:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Voeg vervolgens de parameters voor de naam en het type van het opslagaccount toe aan de sectie met parameters van de sjabloon. Vervang de tekst van de tijdelijke aanduiding 'storage account name goes here' door de gewenste naam voor het opslagaccount.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Voeg vervolgens de uitbreiding **IaaSDiagnostics** toe aan de uitbreidingenmatrix van de eigenschap **VirtualMachineProfile** van elke **Microsoft.Compute/virtualMachineScaleSets**-resource in het cluster.  Als u de [voorbeeldsjabloon][template] gebruikt, zijn er drie virtuele-machineschaalsets (één voor elk type knooppunt in het cluster).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>De EventStore-service configureren
De Event Store-service is een bewakingsoptie in Service Fabric. EventStore biedt een manier om op elk moment meer inzicht te krijgen in de status van uw cluster of workloads. EventStore is een stateful Service Fabric-service die gebeurtenissen van het cluster bijhoudt. De gebeurtenissen worden weergegeven via Service Fabric Explorer, REST en API's. EventStore voert rechtstreeks een query uit op het cluster om diagnostische gegevens op te halen uit een entiteit in uw cluster en moet worden gebruikt voor het volgende:

* Het vaststellen van problemen tijdens de ontwikkeling of het testen, of waar u een bewakingspijplijn zou kunnen gebruiken
* Bevestigen dat beheeracties die u op het cluster uitvoert correct worden verwerkt
* Een 'momentopname' verkrijgen van hoe Service Fabric met een bepaalde entiteit interactie aangaat



Als u de Event Store-service in uw cluster wilt inschakelen, voegt u het volgende toe aan de eigenschap **fabricSettings** van de **Microsoft.ServiceFabric/clusters**-resource:

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Azure Monitor-logboeken instellen voor het cluster

Azure Monitor-logboeken is onze aanbeveling voor het bewaken van gebeurtenissen op clusterniveau. Als u Azure Monitor-logboeken wilt instellen om uw cluster te bewaken, moet u [diagnostische gegevens hebben ingeschakeld om gebeurtenissen op cluster niveau weer te geven](#configure-diagnostics-collection-on-the-cluster).  

De werkruimte moet zijn verbonden met de diagnostische gegevens die afkomstig zijn van uw cluster.  Deze logboekgegevens worden opgeslagen in het opslagaccount *applicationDiagnosticsStorageAccountName* in de tabellen WADServiceFabric*EventTable, WADWindowsEventLogsTable en WADETWEventTable.

Voeg de Azure Log Analytics-werkruimte toe en voeg de oplossing toe aan de werkruimte:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Voeg vervolgens parameters toe
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Voeg vervolgens variabelen toe:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Voeg de uitbreiding van de Log Analytics-agent toe aan elke virtuele-machineschaalset in het cluster en verbind de agent met de Log Analytics-werkruimte. Hierdoor kunt u diagnostische gegevens over containers, toepassingen en prestatiebewaking verzamelen. Door de uitbreiding toe te voegen aan de virtuele-machineschaalset, zorgt Azure Resource Manager ervoor dat deze op elk knooppunt wordt geïnstalleerd, zelfs wanneer het cluster wordt geschaald.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Het virtuele netwerk en het cluster implementeren

Stel vervolgens de netwerktopologie in en implementeer het Service Fabric-cluster. De Resource Manager-sjabloon [azuredeploy.json][template] maakt een virtueel netwerk, een subnet en een netwerkbeveiligingsgroep voor Service Fabric. De sjabloon implementeert ook een cluster met certificaatbeveiliging ingeschakeld. Gebruik voor productieclusters een certificaat van een certificeringsinstantie als clustercertificaat. Een zelfondertekend certificaat kan worden gebruikt om testclusters te beveiligen.

De sjabloon in dit artikel implementeert een cluster dat gebruik maakt van de certificaatvingerafdruk voor het identificeren van het clustercertificaat. Geen twee certificaten kunnen dezelfde vingerafdruk hebben, waardoor certificaatbeheer moeilijker wordt. Het schakelen van een geïmplementeerd cluster van certificaatvingerafdrukken naar gewone namen voor certificaten maakt het beheer van certificaten eenvoudiger. Lees [Cluster wijzigen naar certificaatbeheer met gewone namen](service-fabric-cluster-change-cert-thumbprint-to-cn.md) voor informatie over het bijwerken van het cluster voor het gebruik van gewone namen voor certificaten voor het beheren van certificaten.

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Een cluster maken met behulp van een bestaand certificaat

Het volgende script maakt gebruik van de cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) en een sjabloon om een nieuw cluster in Azure te implementeren. De cmdlet maakt een nieuwe sleutelkluis in Azure en uploadt uw certificaat.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Een cluster maken met behulp van een nieuw, zelfondertekend certificaat

Het volgende script maakt gebruik van de cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) en een sjabloon om een nieuw cluster in Azure te implementeren. De cmdlet maakt een nieuwe sleutelkluis in Azure, voegt een nieuw zelfondertekend certificaat toe aan de sleutelkluis en downloadt het certificaatbestand lokaal.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Verbinding maken met het beveiligde cluster

Maak verbinding met het cluster met behulp van de Service Fabric PowerShell-module die bij de Service Fabric-SDK is geïnstalleerd.  Installeer eerst het certificaat in de persoonlijke opslag (Mijn opslag) van de huidige gebruiker op uw computer. Voer de volgende PowerShell-opdracht uit:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

U bent nu klaar om verbinding te maken met het beveiligde cluster.

De **Service Fabric** PowerShell-module biedt veel cmdlets voor het beheren van Service Fabric-clusters, toepassingen en services. Gebruik de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) om verbinding te maken met het beveiligde cluster. De SHA1-vingerafdruk van het certificaat en de eindpuntdetails van de verbinding vindt u in de uitvoer van de vorige stap.

Als u eerder een Azure AD-clientverificatie heeft ingesteld, voert u de volgende opdracht uit: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Als u geen Azure AD-clientverificatie heeft ingesteld, voert u de volgende opdracht uit:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Controleer of u bent verbonden en of het cluster goed werkt door de cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) te gebruiken.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Resources opschonen

De andere artikelen in deze zelfstudiereeks maken gebruik van het cluster dat u hebt gemaakt. Als u niet meteen verdergaat met het volgende artikel, is het wellicht een goed idee om [het cluster te verwijderen](./service-fabric-tutorial-delete-cluster.md). U bespaart dan kosten.

## <a name="next-steps"></a>Volgende stappen

Ga door naar de volgende zelfstudie om te leren hoe u uw cluster kunt schalen.

> [!div class="checklist"]
> * Een VNET maken in Azure met behulp van PowerShell
> * Een sleutelkluis maken en een certificaat uploaden
> * Azure Active Directory-verificatie instellen
> * Verzameling met diagnostische gegevens configureren
> * EventStore-service instellen
> * Azure Monitor-logboeken instellen
> * Een beveiligd Service Fabric-cluster maken in Azure PowerShell
> * Het cluster beveiligen met een X.509-certificaat
> * Verbinding maken met het cluster met behulp van PowerShell
> * Een cluster verwijderen

Ga door naar de volgende zelfstudie om te leren hoe u uw cluster kunt bewaken.
> [!div class="nextstepaction"]
> [Een cluster bewaken](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
