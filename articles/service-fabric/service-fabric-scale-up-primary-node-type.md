---
title: Een Azure Service Fabric primaire knooppunt type omhoog schalen
description: Uw Service Fabric cluster verticaal schalen door een nieuw knooppunt type toe te voegen en de vorige te verwijderen.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251170"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Een primair knooppunttype voor Service Fabric-clusters omhoog schalen

In dit artikel wordt beschreven hoe u een primair knooppunt type van een Service Fabric cluster omhoog kunt schalen met minimale downtime. De algemene strategie voor het upgraden van een Service Fabric cluster knooppunt type is:

1. Voeg een nieuw knooppunt type toe aan uw Service Fabric cluster, dat wordt ondersteund door uw bijgewerkte (of gewijzigde) SKU en configuratie van virtuele-machine schaal sets. Bij deze stap moet u ook een nieuw load balancer, subnet en open bare IP-adres instellen voor de schaalset.

1. Als zowel de oorspronkelijke als de bijgewerkte schaal sets gelijktijdig worden uitgevoerd, schakelt u de oorspronkelijke knooppunt exemplaren één keer uit zodat de systeem services (of replica's van stateful Services) worden gemigreerd naar de nieuwe schaalset.

1. Controleer of het cluster en de nieuwe knoop punten in orde zijn en verwijder vervolgens de oorspronkelijke schaalset (en gerelateerde resources) en de status van het knoop punt voor de verwijderde knoop punten.

Hieronder vindt u een overzicht van het proces voor het bijwerken van de VM-grootte en het besturings systeem van de virtuele machines van het primaire knoop punt van een voorbeeld cluster met [Silver duurzaamheid](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), ondersteund door één schaalset met vijf knoop punten. Het primaire knooppunt type wordt bijgewerkt:

- Van VM-grootte *Standard_D2_V2* naar *standaard D4_V2* en
- Van het VM-besturings systeem *Windows server 2016 Data Center met containers* naar *Windows Server 2019 Data Center met containers*.

> [!WARNING]
> Voordat u deze procedure uitvoert op een productie cluster, raden we u aan de voorbeeld sjablonen te bestuderen en het proces te controleren op basis van een test cluster. Het cluster kan ook korte tijd niet beschikbaar zijn.
>
> Probeer niet een procedure van een primair knooppunt type scale up uit te voeren als de cluster status niet in orde is, omdat hierdoor het cluster alleen verder wordt beschadigd.

Dit zijn de stapsgewijze sjablonen voor Azure-implementaties die we gebruiken om dit voorbeeld scenario voor de upgrade te volt ooien: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>Het test cluster instellen

Laten we de eerste Service Fabric test cluster instellen. [Down load](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) eerst de Azure Resource Manager-voorbeeld sjablonen die we gaan gebruiken om dit scenario te volt ooien.

Meld u vervolgens aan bij uw Azure-account.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Open vervolgens de [*parameters.jsin*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) het bestand en werk de waarde voor `clusterName` in iets uniek (in Azure).

De volgende opdrachten begeleiden u bij het genereren van een nieuw zelfondertekend certificaat en het implementeren van het test cluster. Als u al een certificaat hebt dat u wilt gebruiken, gaat u naar [een bestaand certificaat gebruiken om het cluster te implementeren](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Een zelfondertekend certificaat genereren en het cluster implementeren

Wijs eerst de variabelen toe die u nodig hebt voor Service Fabric cluster implementatie. Pas de waarden voor `resourceGroupName` ,  `certSubjectName` ,, `parameterFilePath` en `templateFilePath` voor uw specifieke account en omgeving aan:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> Zorg ervoor dat de `certOutputFolder` locatie aanwezig is op de lokale computer voordat u de opdracht uitvoert om een nieuw service Fabric-cluster te implementeren.

Implementeer vervolgens het Service Fabric test cluster:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Zodra de implementatie is voltooid, gaat u naar het *PFX* -bestand ( `$certPfx` ) op de lokale computer en importeert u het in uw certificaat archief:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

De bewerking retourneert de vinger afdruk van het certificaat, die u nu kunt gebruiken om [verbinding te maken met het nieuwe cluster](#connect-to-the-new-cluster-and-check-health-status) en de integriteits status te controleren. (Sla de volgende sectie over, een andere benadering van de cluster implementatie.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Een bestaand certificaat gebruiken om het cluster te implementeren

U kunt ook een bestaand Azure Key Vault certificaat gebruiken om het test cluster te implementeren. Hiervoor moet u [referenties voor de Key Vault en de](#obtain-your-key-vault-references) vinger afdruk van het certificaat verkrijgen.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Wijs vervolgens de naam van een resource groep voor het cluster aan en stel de `templateFilePath` locaties en in `parameterFilePath` :

> [!NOTE]
> De aangewezen resource groep moet al bestaan en zich in dezelfde regio bevinden als uw Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Ten slotte voert u de volgende opdracht uit om het eerste test cluster te implementeren:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Verbinding maken met het nieuwe cluster en de status controleren

Maak verbinding met het cluster en zorg ervoor dat alle vijf knoop punten in orde zijn (Vervang de `clusterName` variabelen en door `thumb` uw eigen waarden):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Met deze procedure kunt u nu beginnen met de upgrade.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Een nieuw primair knooppunt type implementeren met een bijgewerkte schaalset

Als u een upgrade (verticaal schalen) wilt uitvoeren voor een knooppunt type, moet u eerst een nieuw knooppunt type implementeren dat door een nieuwe schaalset en ondersteunende bronnen wordt ondersteund. De nieuwe schaalset wordt gemarkeerd als primair ( `isPrimary: true` ), net als de oorspronkelijke schaalset (tenzij u een upgrade uitvoert voor een niet-primair knooppunt type). De resources die in de volgende sectie worden gemaakt, worden uiteindelijk het nieuwe primaire knooppunt type in het cluster en de oorspronkelijke bronnen van het primaire knooppunt type worden verwijderd.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>De cluster sjabloon bijwerken met de bijgewerkte schaalset

Hier vindt u de sectie-voor-sectie wijzigingen van de oorspronkelijke cluster implementatie sjabloon voor het toevoegen van een nieuw primair knooppunt type en ondersteunende resources.

De vereiste wijzigingen voor deze stap zijn al doorgevoerd in de [*Step1-AddPrimaryNodeType.jsvan*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) het sjabloon bestand. in het volgende worden deze wijzigingen in detail uitgelegd. Als u wilt, kunt u de uitleg overs Laan en door gaan met het [verkrijgen van uw Key Vault verwijzingen](#obtain-your-key-vault-references) en [de bijgewerkte sjabloon implementeren](#deploy-the-updated-template) waarmee een nieuw primair knooppunt type aan uw cluster wordt toegevoegd.

> [!Note]
> Zorg ervoor dat u namen gebruikt die uniek zijn voor het oorspronkelijke knooppunt type, de schaalset, load balancer, het open bare IP-adres en het subnet van het oorspronkelijke primaire knooppunt type, omdat deze resources in een latere stap in het proces worden verwijderd.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Een nieuw subnet in het bestaande virtuele netwerk maken

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Een nieuw openbaar IP-adres maken met een unieke domeinnaam label

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Een nieuwe load balancer maken voor het open bare IP-adres

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Een nieuwe schaalset voor virtuele machines maken (met bijgewerkte VM-en OS-Sku's)

Verwijzing van knooppunt type

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM-SKU

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU VAN BESTURINGS SYSTEEM

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Zorg er ook voor dat u extra uitbrei dingen opneemt die vereist zijn voor uw werk belasting.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Een nieuw type primair knoop punt toevoegen aan het cluster

Nu het nieuwe knooppunt type (vmNodeType1Name) een eigen naam, subnet, IP, load balancer en schaalset heeft, kan het het opnieuw gebruiken van alle andere variabelen van het oorspronkelijke knooppunt type (zoals `nt0applicationEndPort` , `nt0applicationStartPort` en `nt0fabricTcpGatewayPort` ):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Zodra u alle wijzigingen in de sjabloon en de parameter bestanden hebt geïmplementeerd, gaat u verder met de volgende sectie om uw Key Vault verwijzingen te verkrijgen en de updates te implementeren in uw cluster.

### <a name="obtain-your-key-vault-references"></a>Uw Key Vault referenties verkrijgen

Als u de bijgewerkte configuratie wilt implementeren, hebt u verschillende verwijzingen nodig naar het cluster certificaat dat is opgeslagen in uw Key Vault. De eenvoudigste manier om deze waarden te vinden, is via Azure Portal. U hebt het volgende nodig:

* **De Key Vault URL van uw cluster certificaat.** Selecteer vanuit uw Key Vault in azure Portal **certificaten**  >  *uw gewenste geheime certificaat*-  >  **id**:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **De vinger afdruk van het cluster certificaat.** (Waarschijnlijk hebt u dit al als u [verbinding hebt gemaakt met het eerste cluster](#connect-to-the-new-cluster-and-check-health-status) om de status te controleren.)   >  Kopieer de **X. 509 SHA-1-vinger afdruk (in hex)** op dezelfde Blade voor certificaten (certificaten *uw gewenste certificaat*) in azure portal:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **De resource-ID van uw Key Vault.** Selecteer vanuit uw Key Vault in azure Portal **Eigenschappen**  >  **resource-id**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren

Pas de `templateFilePath` indien nodig aan en voer de volgende opdracht uit:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Wanneer de implementatie is voltooid, controleert u de cluster status opnieuw en zorgt u ervoor dat alle knoop punten in beide knooppunt typen in orde zijn.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Seed-knoop punten migreren naar het nieuwe knooppunt type

We kunnen het oorspronkelijke knooppunt type nu bijwerken als niet-primair en beginnen met het uitschakelen van de knoop punten. Als de knoop punten worden uitgeschakeld, worden de systeem services en Seed-knoop punten van het cluster naar de nieuwe schaalset gemigreerd.

### <a name="unmark-the-original-node-type-as-primary"></a>De markering van het oorspronkelijke knooppunt type als primair opheffen

Verwijder eerst de `isPrimary` aanduiding in de sjabloon uit het oorspronkelijke knooppunt type.

```json
{
    "isPrimary": false,
}
```

Implementeer vervolgens de sjabloon met de update. Hiermee wordt de migratie van Seed-knoop punten naar de nieuwe schaalset gestart.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Het kan enige tijd duren om de migratie van het Seed-knoop punt naar de nieuwe schaalset te volt ooien. Om consistentie van gegevens te garanderen, kan slechts één Seed-knoop punt tegelijk worden gewijzigd. Voor elke wijziging van het Seed-knoop punt is een cluster update vereist; voor het vervangen van een Seed-knoop punt zijn twee cluster upgrades vereist (één voor het toevoegen en verwijderen van knoop punten). Het bijwerken van de vijf Seed-knoop punten in dit voorbeeld scenario resulteert in tien cluster upgrades.

Gebruik Service Fabric Explorer om de migratie van Seed-knoop punten naar de nieuwe schaalset te bewaken. De knoop punten van het oorspronkelijke knooppunt type (nt0vm) moeten allemaal *False* zijn in de kolom **is Seed node** en die van het nieuwe knooppunt type (nt1vm) is *waar*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>De knoop punten in de oorspronkelijke schaalset van het knooppunt type uitschakelen

Zodra alle Seed-knoop punten zijn gemigreerd naar de nieuwe schaalset, kunt u de knoop punten van de oorspronkelijke schaalset uitschakelen.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Gebruik Service Fabric Explorer om de voortgang van knoop punten in de oorspronkelijke schaalset te bewaken van *uitschakelen* naar *uitgeschakeld* status.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Service Fabric Explorer de status van uitgeschakelde knoop punten wordt weer gegeven":::

Voor de duurzaamheid van zilver en goud wordt een aantal knoop punten uitgeschakeld, terwijl anderen mogelijk de status *uitschakelen* kunnen behouden. Ga in Service Fabric Explorer naar het tabblad **Details** van knoop punten in de status uitschakelen. Als er een *veiligheids controle in behandeling* wordt weer gegeven van de soort *EnsurePartitionQuorem* (quorum voor infrastructuur service-partities waarborgt), is het veilig om door te gaan.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="U kunt door gaan met het stoppen van gegevens en het verwijderen van knoop punten die zijn vastgelopen in de status uitschakelen als ze een in behandeling zijnde veiligheids controle van het type ' EnsurePartitionQuorum ' weer gegeven.":::

Als uw cluster bronzen duurzaamheid is, wacht u totdat alle knoop punten de status *uitgeschakeld* hebben.

### <a name="stop-data-on-the-disabled-nodes"></a>Gegevens op de uitgeschakelde knoop punten stoppen

U kunt nu gegevens op de uitgeschakelde knoop punten stoppen.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Het oorspronkelijke knooppunt type verwijderen en de bijbehorende resources opruimen

We zijn klaar om het oorspronkelijke knooppunt type en de bijbehorende resources te verwijderen om de verticale schaal procedure te sluiten.

### <a name="remove-the-original-scale-set"></a>De oorspronkelijke schaalset verwijderen

Verwijder eerst de schaalset voor de back-upreeks van het knooppunt type.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>De oorspronkelijke IP-en load balancer resources verwijderen

U kunt nu het oorspronkelijke IP-adres en de load balancer resources verwijderen. In deze stap werkt u ook de DNS-naam bij.

> [!Note]
> Deze stap is optioneel als u al een open bare IP-adres voor de *standaard* -SKU gebruikt en Load Balancer. In dit geval kunt u meerdere schaal sets/knooppunt typen onder hetzelfde load balancer hebben.

Voer de volgende opdrachten uit en wijzig de `$lbname` waarde naar wens.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Knooppunt status verwijderen uit het oorspronkelijke knooppunt type

Voor de knoop punten van het oorspronkelijke knooppunt type wordt nu een *fout* weer gegeven voor de **status.** Verwijder de knooppunt status uit het cluster.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer moet nu alleen de vijf knoop punten van het nieuwe knooppunt type (nt1vm) weer spie gelen, allemaal met de status waarden *OK*. In de status van het cluster wordt de *fout* nog steeds weer gegeven. We zullen dit oplossen door de sjabloon bij te werken om de meest recente wijzigingen te reflecteren en opnieuw te implementeren.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>De implementatie sjabloon bijwerken zodat deze overeenkomt met het nieuw geschaalde primaire knooppunt type

De vereiste wijzigingen voor deze stap zijn al doorgevoerd in de [*Step3-CleanupOriginalPrimaryNodeType.jsvan*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) het sjabloon bestand. in de volgende secties wordt deze sjabloon gedetailleerd beschreven. Als u wilt, kunt u de uitleg overs Laan en door gaan met het [implementeren van de bijgewerkte sjabloon](#deploy-the-finalized-template) en de zelf studie volt ooien.

#### <a name="update-the-cluster-management-endpoint"></a>Het eind punt van het cluster beheer bijwerken

Werk het cluster `managementEndpoint` op de implementatie sjabloon bij om te verwijzen naar het nieuwe IP-adres (door *vmNodeType0Name* bij te werken met *vmNodeType1Name*).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>De verwijzing naar het oorspronkelijke knooppunt type verwijderen

Verwijder de oorspronkelijke verwijzing naar het type knoop punt uit de Service Fabric resource in de implementatie sjabloon:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Status beleid configureren om bestaande fouten te negeren

U kunt alleen voor Silver-en hogere duurzaamheids clusters de cluster bron in de sjabloon bijwerken en het status beleid configureren om de `fabric:/System` status van de toepassing te negeren door *applicationDeltaHealthPolicies* toe te voegen onder de eigenschappen van de cluster resource, zoals hieronder wordt vermeld. Met het onderstaande beleid worden bestaande fouten genegeerd, maar worden nieuwe status fouten niet toegestaan.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Ondersteunende bronnen voor het oorspronkelijke knooppunt type verwijderen

Verwijder alle andere resources met betrekking tot het oorspronkelijke knooppunt type uit de ARM-sjabloon en het parameter bestand. Verwijder het volgende:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>De voltooide sjabloon implementeren

Implementeer ten slotte de gewijzigde Azure Resource Manager sjabloon.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Deze stap kan enige tijd duren, meestal Maxi maal twee uur.

Tijdens de upgrade worden de instellingen gewijzigd in de *InfrastructureService*. Daarom is het opnieuw opstarten van een knoop punt vereist. In dit geval wordt *forceRestart* genegeerd. De para meter `upgradeReplicaSetCheckTimeout` bepaalt de maximale tijds duur dat service Fabric wacht tot een partitie een veilige status heeft, als deze niet al een veilige status heeft. Zodra de controle van de veiligheid is geslaagd voor alle partities op een knoop punt, wordt Service Fabric door gegeven aan de upgrade op dat knoop punt. De waarde voor de para meter `upgradeTimeout` kan worden teruggebracht tot 6 uur, maar er moet een maximale beveiliging van 12 uur worden gebruikt.

Nadat de implementatie is voltooid, controleert u in Azure Portal dat de Service Fabric resource status *gereed* is. Controleer of u het nieuwe Service Fabric Explorer-eind punt kunt bereiken, de status van het **cluster** is *OK* en of alle geïmplementeerde toepassingen goed werken.

Hiermee hebt u een primaire cluster knooppunt verticaal geschaald.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [toevoegen van een knooppunt type aan een cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Meer informatie over [schaal baarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in-of uitschalen](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met behulp van de Fluent Azure Compute SDK.
* [Een zelfstandige cluster in-of uitschalen](service-fabric-cluster-windows-server-add-remove-nodes.md).
