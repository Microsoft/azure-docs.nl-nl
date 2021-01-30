---
title: Cluster knooppunten upgraden om Azure Managed disks te gebruiken
description: U kunt als volgt een upgrade uitvoeren van een bestaand Service Fabric cluster om Azure Managed disks te gebruiken met weinig of geen uitval tijd van uw cluster.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: c374c4536309a13abcf8c882b041a9c5357878e5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090651"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Cluster knooppunten upgraden om Azure Managed disks te gebruiken

[Azure Managed disks](../virtual-machines/managed-disks-overview.md) is de aanbevolen schijf opslag voor gebruik met Azure virtual machines voor permanente opslag van gegevens. U kunt de tolerantie van uw Service Fabric-workloads verbeteren door de schaal sets voor virtuele machines te upgraden waarmee underlie uw knooppunt typen worden gebruikt voor het gebruik van beheerde schijven. U kunt als volgt een upgrade uitvoeren van een bestaand Service Fabric cluster om Azure Managed disks te gebruiken met weinig of geen uitval tijd van uw cluster.

De algemene strategie voor het upgraden van een Service Fabric cluster knooppunt voor het gebruik van beheerde schijven is:

1. Implementeer een andere dubbele schaalset voor virtuele machines van dat knooppunt type, maar met het object [managedDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) toegevoegd aan de `osDisk` sectie van de implementatie sjabloon voor virtuele-machine schaal sets. De nieuwe schaalset moet worden gebonden aan hetzelfde load balancer/IP-adres als het origineel, zodat uw klanten geen service-onderbrekingen ondervinden tijdens de migratie.

2. Als zowel de oorspronkelijke als de bijgewerkte schaal sets gelijktijdig worden uitgevoerd, schakelt u de oorspronkelijke knooppunt exemplaren één keer uit zodat de systeem services (of replica's van stateful Services) worden gemigreerd naar de nieuwe schaalset.

3. Controleer of het cluster en de nieuwe knoop punten in orde zijn en verwijder vervolgens de oorspronkelijke schaalset en de status van het knoop punt voor de verwijderde knoop punten.

Dit artikel begeleidt u stapsgewijs door de stappen voor het upgraden van het primaire knooppunt type van een voorbeeld cluster om beheerde schijven te gebruiken, terwijl u geen uitval tijd van het cluster kunt vermijden (zie opmerking hieronder). De aanvankelijke status van het test cluster bestaat uit één knooppunt type van [Silver duurzaamheid](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), dat wordt ondersteund door een enkele schaalset met vijf knoop punten.

> [!NOTE]
> De beperkingen van een basis-SKU load balancer voor komen dat een extra schaalset wordt toegevoegd. Het is raadzaam om in plaats daarvan de standaard SKU-load balancer te gebruiken. Zie [een vergelijking van de twee sku's](../load-balancer/skus.md)voor meer informatie.

> [!CAUTION]
> U ondervindt alleen een onderbreking met deze procedure als u afhankelijkheden hebt op de cluster-DNS (bijvoorbeeld wanneer u [service Fabric Explorer](service-fabric-visualizing-your-cluster.md)opent). [Best Practice van de front-end-services](/azure/architecture/microservices/design/gateway) van de architectuur heeft een soort [Load Balancer](/azure/architecture/guide/technology-choices/load-balancing-overview) voor de knooppunt typen om het wisselen van knoop punten mogelijk te maken zonder storingen.

Hier vindt u de [sjablonen en cmdlets](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) voor Azure Resource Manager die we gaan gebruiken om het upgrade scenario te volt ooien. De sjabloon wijzigingen worden uitgelegd in [een geüpgradede schaalset implementeren voor het primaire knooppunt type](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  hieronder.

## <a name="set-up-the-test-cluster"></a>Het test cluster instellen

Laten we de eerste Service Fabric test cluster instellen. [Down load](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) eerst de Azure Resource Manager-voorbeeld sjablonen die we gaan gebruiken om dit scenario te volt ooien.

Meld u vervolgens aan bij uw Azure-account.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

De volgende opdrachten begeleiden u bij het genereren van een nieuw zelfondertekend certificaat en het implementeren van het test cluster. Als u al een certificaat hebt dat u wilt gebruiken, gaat u naar [een bestaand certificaat gebruiken om het cluster te implementeren](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Een zelfondertekend certificaat genereren en het cluster implementeren

Wijs eerst de variabelen toe die u nodig hebt voor Service Fabric cluster implementatie. Pas de waarden voor `resourceGroupName` ,  `certSubjectName` ,, `parameterFilePath` en `templateFilePath` voor uw specifieke account en omgeving aan:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Zorg ervoor dat de `certOutputFolder` locatie aanwezig is op de lokale computer voordat u de opdracht uitvoert om een nieuw service Fabric-cluster te implementeren.

Open vervolgens de [*Initial-1NodeType-UnmanagedDisks.parameters.jsin*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) het bestand en wijzig de waarden voor `clusterName` en, `dnsName` zodat deze overeenkomen met de dynamische waarden die u hebt ingesteld in Power shell en sla uw wijzigingen op.

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

De bewerking retourneert de vinger afdruk van het certificaat, die u gebruikt om [verbinding te maken met het nieuwe cluster](#connect-to-the-new-cluster-and-check-health-status) en de integriteits status te controleren. (Sla de volgende sectie over, een andere benadering van de cluster implementatie.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Een bestaand certificaat gebruiken om het cluster te implementeren

U kunt ook een bestaand Azure Key Vault certificaat gebruiken om het test cluster te implementeren. Hiervoor moet u [referenties voor de Key Vault en de](#obtain-your-key-vault-references) vinger afdruk van het certificaat verkrijgen.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Open de [*Initial-1NodeType-UnmanagedDisks.parameters.jsin*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) het bestand en wijzig de waarden voor `clusterName` en `dnsName` in iets uniek.

Wijs tot slot de naam van een resource groep voor het cluster aan en stel de `templateFilePath` en de `parameterFilePath` locaties van de *eerste 1NodeType-UnmanagedDisks-* bestanden in:

> [!NOTE]
> De aangewezen resource groep moet al bestaan en zich in dezelfde regio bevinden als uw Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Ten slotte voert u de volgende opdracht uit om het eerste test cluster te implementeren:

```powershell
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

Maak verbinding met het cluster en zorg ervoor dat alle vijf knoop punten in orde zijn (Vervang de `clusterName` `thumb` variabelen en voor uw cluster):

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

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Een bijgewerkte schaalset implementeren voor het primaire knooppunt type

Als u een upgrade, of *verticaal schalen*, een knooppunt type wilt uitvoeren, moet u een kopie van de virtuele-machine schaalset van dat knooppunt type implementeren, die anders identiek is aan de oorspronkelijke schaalset (inclusief verwijzing naar dezelfde `nodeTypeRef` , `subnet` en `loadBalancerBackendAddressPools` ), behalve dat deze de gewenste upgrade/wijzigingen en een eigen afzonderlijke subnet en de binnenkomende NAT-adres groep bevat. Omdat er een upgrade wordt uitgevoerd voor een primair knooppunt type, wordt de nieuwe schaalset gemarkeerd als primair ( `isPrimary: true` ), net als bij de oorspronkelijke schaalset. (Als u een upgrade wilt uitvoeren voor niet-primaire knooppunt typen, laat u dit gewoon weg.)

Voor het gemak zijn de vereiste wijzigingen al voor u gemaakt in de sjabloon *upgrade-1NodeType-2ScaleSets-ManagedDisks* [](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) en de [parameter](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) bestanden.

In de volgende secties worden de sjabloon wijzigingen in detail uitgelegd. Als u wilt, kunt u de uitleg overs Laan en door gaan met [de volgende stap van de upgrade procedure](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>De cluster sjabloon bijwerken met de bijgewerkte schaalset

Hier vindt u de sectie-voor-sectie wijzigingen van de originele cluster implementatie sjabloon voor het toevoegen van een bijgewerkte schaalset voor het primaire knooppunt type.

#### <a name="parameters"></a>Parameters

Voeg para meters toe voor de exemplaar naam, het aantal en de grootte van de nieuwe schaalset. Merk `vmNodeType1Name` op dat uniek is voor de nieuwe schaalset, terwijl de waarden voor aantal en grootte identiek zijn aan de oorspronkelijke schaalset.

**Sjabloonbestand**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Parameterbestand**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Variabelen

Voeg in de sectie implementatie sjabloon `variables` een vermelding toe voor de binnenkomende NAT-adres groep van de nieuwe schaalset.

**Sjabloonbestand**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Resources

Voeg in de sectie *resources* van de implementatie sjabloon de nieuwe schaalset voor virtuele machines toe en houd daarbij de volgende dingen:

* De nieuwe schaalset verwijst naar hetzelfde knooppunt type als het origineel:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* De nieuwe schaalset verwijst naar dezelfde load balancer back-end-adres en-subnet (maar gebruikt een andere load balancer binnenkomende NAT-groep):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Net als de oorspronkelijke schaalset wordt de nieuwe schaalset gemarkeerd als het primaire knooppunt type. (Als u niet-primaire knooppunt typen bijwerkt, laat u deze wijziging weg.)

    ```json
    "isPrimary": true,
    ```

* In tegens telling tot de oorspronkelijke schaalset wordt de nieuwe schaalset geüpgraded om beheerde schijven te gebruiken.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Zodra u alle wijzigingen in de sjabloon en de parameter bestanden hebt geïmplementeerd, gaat u verder met de volgende sectie om uw Key Vault verwijzingen te verkrijgen en de updates te implementeren in uw cluster.

### <a name="obtain-your-key-vault-references"></a>Uw Key Vault referenties verkrijgen

Als u de bijgewerkte configuratie wilt implementeren, moet u eerst verschillende verwijzingen naar uw cluster certificaat ophalen die zijn opgeslagen in uw Key Vault. De eenvoudigste manier om deze waarden te vinden, is via Azure Portal. U hebt het volgende nodig:

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

Pas de `parameterFilePath` en `templateFilePath` zo nodig aan en voer de volgende opdracht uit:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Wanneer de implementatie is voltooid, controleert u de cluster status opnieuw en zorgt u ervoor dat alle tien knoop punten (vijf op het origineel en vijf op de nieuwe schaalset) in orde zijn.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Seed-knoop punten migreren naar de nieuwe schaalset

U kunt nu beginnen met het uitschakelen van de knoop punten van de oorspronkelijke schaalset. Wanneer deze knoop punten worden uitgeschakeld, worden de systeem services en Seed-knoop punten gemigreerd naar de virtuele machines van de nieuwe schaalset, omdat deze ook als primair knooppunt type is gemarkeerd.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Gebruik Service Fabric Explorer om de migratie van Seed-knoop punten te bewaken naar de nieuwe schaalset en de voortgang van knoop punten in de oorspronkelijke schaalset uit te *scha kelen* naar *Uitgeschakelde* status.

![Service Fabric Explorer de status van uitgeschakelde knoop punten wordt weer gegeven](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Het kan enige tijd duren voordat de bewerking is uitgeschakeld op alle knoop punten van de oorspronkelijke schaalset. Om consistentie van gegevens te garanderen, kan slechts één Seed-knoop punt tegelijk worden gewijzigd. Voor elke wijziging van het Seed-knoop punt is een cluster update vereist; voor het vervangen van een Seed-knoop punt zijn twee cluster upgrades vereist (één voor het toevoegen en verwijderen van knoop punten). Het bijwerken van de vijf Seed-knoop punten in dit voorbeeld scenario resulteert in tien cluster upgrades.

## <a name="remove-the-original-scale-set"></a>De oorspronkelijke schaalset verwijderen

Wanneer het uitschakelen is voltooid, verwijdert u de schaalset.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

In Service Fabric Explorer wordt de verwijderde knoop punten (en dus de status van het *cluster*) nu weer gegeven met de status *fout* .

![Service Fabric Explorer weer gave van uitgeschakelde knoop punten in fout status](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Verwijder de verouderde knoop punten uit het Service Fabric cluster om de status van het cluster te herstellen naar *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer met lagere knoop punten in de fout status verwijderd](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Volgende stappen

In dit scenario hebt u geleerd hoe u de schaal sets van virtuele machines van een Service Fabric cluster bijwerkt om Managed disks te gebruiken terwijl er geen service storingen optreden tijdens het proces. Bekijk de volgende bronnen voor meer informatie over verwante onderwerpen.

Leer hoe u het volgende doet:

* [Een primair knooppunttype voor Service Fabric-clusters omhoog schalen](service-fabric-scale-up-primary-node-type.md)

* [Een sjabloon voor schaal sets converteren voor het gebruik van beheerde schijven](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Een Service Fabric knooppunt type verwijderen](service-fabric-how-to-remove-node-type.md)

Zie ook:

* [Voor beeld: cluster knooppunten upgraden om Azure Managed disks te gebruiken](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade)

* [Overwegingen voor verticaal schalen](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)