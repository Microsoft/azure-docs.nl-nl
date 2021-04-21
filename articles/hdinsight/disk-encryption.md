---
title: Dubbele versleuteling voor data-at-rest
titleSuffix: Azure HDInsight
description: In dit artikel worden de twee lagen van versleuteling beschreven die beschikbaar zijn voor data-at-rest op Azure HDInsight clusters.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 226516b1178f14789570b45b68cfdbf56f63bbd7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775147"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Azure HDInsight dubbele versleuteling voor data-at-rest

In dit artikel worden methoden besproken voor versleuteling van data-at-rest in Azure HDInsight clusters. Versleuteling van data-at-rest verwijst naar versleuteling op beheerde schijven (gegevensschijven, besturingssysteemschijven en tijdelijke schijven) die zijn gekoppeld aan virtuele hdInsight-clustermachines. 

Dit document bevat geen gegevens die zijn opgeslagen in uw Azure Storage account. Uw clusters kunnen een of meer gekoppelde Azure Storage-accounts hebben waar de versleutelingssleutels ook door Microsoft kunnen worden beheerd of door de klant worden beheerd, maar de versleutelingsservice is anders. Zie voor meer informatie Azure Storage versleuteling Azure Storage [versleuteling voor data-at-rest.](../storage/common/storage-service-encryption.md)

## <a name="introduction"></a>Introductie

Er zijn drie hoofdrollen voor beheerde schijven in Azure: de gegevensschijf, de besturingssysteemschijf en de tijdelijke schijf. Zie Inleiding tot azure managed disks voor meer informatie over verschillende [typen beheerde schijven.](../virtual-machines/managed-disks-overview.md) 

HDInsight ondersteunt meerdere typen versleuteling in twee verschillende lagen:

- Server Side Encryption (SSE) - SSE wordt uitgevoerd door de opslagservice. In HDInsight wordt SSE gebruikt om besturingssysteemschijven en gegevensschijven te versleutelen. Deze is standaard ingeschakeld. SSE is een laag 1-versleutelingsservice.
- Versleuteling op de host met behulp van een door het platform beheerde sleutel: net als bij SSE wordt dit type versleuteling uitgevoerd door de opslagservice. Het is echter alleen voor tijdelijke schijven en is niet standaard ingeschakeld. Versleuteling op de host is ook een laag 1-versleutelingsservice.
- Versleuteling in rust met behulp van door de klant beheerde sleutel: dit type versleuteling kan worden gebruikt op gegevens en tijdelijke schijven. Deze functie is niet standaard ingeschakeld en vereist dat de klant zijn eigen sleutel op geeft via Azure Key Vault. Versleuteling-at-rest is een laag 2-versleutelingsservice.

Deze typen worden samengevat in de volgende tabel.

|Clustertype |Besturingssysteemschijf (beheerde schijf) |Gegevensschijf (beheerde schijf) |Tijdelijke gegevensschijf (lokale SSD) |
|---|---|---|---|
|Kafka, HBase met versnelde schrijf-|Layer1: [SSE-versleuteling](../virtual-machines/managed-disks-overview.md#encryption) standaard|Layer1: [SSE-versleuteling](../virtual-machines/managed-disks-overview.md#encryption) standaard Layer2: Optionele versleuteling-at-rest met cmk|Layer1: Optional Encryption at host using PMK, Layer2: Optional encryption at rest using CMK|
|Alle andere clusters (Spark, Interactive, Hadoop, HBase zonder versnelde schrijf schrijft)|Layer1: [SSE-versleuteling](../virtual-machines/managed-disks-overview.md#encryption) standaard|N.v.t.|Layer1: Optional Encryption at host using PMK, Layer2: Optional encryption at rest using CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Versleuteling in rust met door de klant beheerde sleutels

Door de klant beheerde sleutelversleuteling is een proces dat in één stap wordt verwerkt tijdens het maken van het cluster, zonder extra kosten. U hoeft alleen maar een beheerde identiteit te autoreren met Azure Key Vault en de versleutelingssleutel toe te voegen wanneer u uw cluster maakt.

Zowel gegevensschijven als tijdelijke schijven op elk knooppunt van het cluster worden versleuteld met een symmetrische gegevensversleutelingssleutel (DEK). De DEK wordt beveiligd met behulp van de Sleutelversleutelingssleutel (KEK) van uw sleutelkluis. De versleutelings- en ontsleutelingsprocessen worden volledig verwerkt door Azure HDInsight.

Voor besturingssysteemschijven die zijn gekoppeld aan de cluster-VM's is slechts één versleutelingslaag (PMK) beschikbaar. Het wordt aanbevolen dat klanten voorkomen dat ze gevoelige gegevens naar besturingssysteemschijven kopiëren als een CMK-versleuteling is vereist voor hun scenario's.

Als de firewall van de sleutelkluis is ingeschakeld op de sleutelkluis waar de schijfversleutelingssleutel is opgeslagen, moeten de regionale IP-adressen van de HDInsight-resourceprovider voor de regio waarin het cluster wordt geïmplementeerd, worden toegevoegd aan de firewallconfiguratie van de sleutelkluis. Dit is nodig omdat HDInsight geen vertrouwde Azure Key Vault-service is.

U kunt de Azure Portal of Azure CLI gebruiken om de sleutels veilig te roteren in de sleutelkluis. Wanneer een sleutel wordt geroteerd, begint het HDInsight-cluster binnen enkele minuten met het gebruik van de nieuwe sleutel. Schakel de functies [voor de beveiliging van](../key-vault/general/soft-delete-overview.md) de sleutel voor soft delete in om u te beschermen tegen ransomwarescenario's en onbedoeld verwijderen. Sleutelkluizen zonder deze beveiligingsfunctie worden niet ondersteund.

### <a name="get-started-with-customer-managed-keys"></a>Aan de slag met door de klant beheerde sleutels

Voor het maken van een HDInsight-cluster met door de klant beheerde sleutel gaan we de volgende stappen doorlopen:

1. Beheerde identiteiten maken voor Azure-resources
1. Een Azure Key Vault
1. Sleutel maken
1. Toegangsbeleid maken
1. HDInsight-cluster maken met door de klant beheerde sleutel ingeschakeld
1. De versleutelingssleutel roteren

Elke stap wordt uitgebreid beschreven in een van de volgende secties.

### <a name="create-managed-identities-for-azure-resources"></a>Beheerde identiteiten maken voor Azure-resources

Maak een door de gebruiker toegewezen beheerde identiteit om te verifiëren bij Key Vault.

Zie [Een door de gebruiker toegewezen beheerde identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) voor specifieke stappen. Zie Beheerde identiteiten in Azure HDInsight voor meer informatie over hoe beheerde [identiteiten in](hdinsight-managed-identities.md)Azure HDInsight. Sla de resource-id van de beheerde identiteit op voor wanneer u deze toevoegt aan het Key Vault toegangsbeleid.

### <a name="create-azure-key-vault"></a>Een Azure Key Vault

Een sleutelkluis maken. Zie [Create Azure Key Vault](../key-vault/general/quick-create-portal.md) for specific steps (Een Azure Key Vault maken voor specifieke stappen).

HDInsight ondersteunt alleen Azure Key Vault. Als u uw eigen sleutelkluis hebt, kunt u uw sleutels importeren in Azure Key Vault. Houd er rekening mee dat voor de sleutelkluis **De functie Voor verwijderen** moet zijn ingeschakeld. Ga naar Over sleutels, geheimen en certificaten voor meer informatie over het importeren [van bestaande sleutels.](../key-vault/general/about-keys-secrets-certificates.md)

### <a name="create-key"></a>Sleutel maken

1. Navigeer vanuit uw nieuwe sleutelkluis naar  >    >  **Instellingensleutels + Genereren/importeren.**

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Een nieuwe sleutel genereren in Azure Key Vault":::

1. Geef een naam op en selecteer vervolgens **Maken.** Behoud het **standaardsleuteltype** **van RSA**.

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="genereert de sleutelnaam":::

1. Wanneer u terugkeert naar de **pagina Sleutels,** selecteert u de sleutel die u hebt gemaakt.

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="sleutellijst voor sleutelkluis":::

1. Selecteer de versie om de pagina **Sleutelversie te** openen. Wanneer u uw eigen sleutel gebruikt voor HDInsight-clusterversleuteling, moet u de sleutel-URI verstrekken. Kopieer de **sleutel-id** en sla deze ergens op totdat u klaar bent om uw cluster te maken.

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="sleutel-id op te halen":::

### <a name="create-access-policy"></a>Toegangsbeleid maken

1. Navigeer vanuit uw nieuwe sleutelkluis naar **Instellingen**  >    >  **Toegangsbeleid + Toegangsbeleid toevoegen.**

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="Nieuw toegangsbeleid voor Azure Key Vault maken":::

1. Geef op **de pagina Toegangsbeleid** toevoegen de volgende informatie op:

    |Eigenschap |Beschrijving|
    |---|---|
    |Sleutelmachtigingen|Selecteer **Get**, **Unwrap Key** en Wrap **Key.**|
    |Geheime machtigingen|Selecteer **Get**, **Set** en **Delete.**|
    |Principal selecteren|Selecteer de door de gebruiker toegewezen beheerde identiteit die u eerder hebt gemaakt.|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="Principal selecteren instellen voor Azure Key Vault toegangsbeleid":::

1. Selecteer **Toevoegen**.

1. Selecteer **Opslaan**.

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Toegangsbeleid Azure Key Vault opslaan":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Cluster maken met schijfversleuteling met door de klant beheerde sleutel

U bent nu klaar om een nieuw HDInsight-cluster te maken. Door de klant beheerde sleutels kunnen alleen worden toegepast op nieuwe clusters tijdens het maken van het cluster. Versleuteling kan niet worden verwijderd uit door de klant beheerde sleutelclusters en door de klant beheerde sleutels kunnen niet worden toegevoegd aan bestaande clusters.

Vanaf de release van november 2020 biedt HDInsight ondersteuning voor het maken van clusters met zowel URI's met versies als sleutel-URI's zonder versie. Als u het cluster maakt met een sleutel-URI zonder versie, probeert het HDInsight-cluster automatische rotatie van sleutels uit te voeren wanneer de sleutel wordt bijgewerkt in uw Azure Key Vault. Als u het cluster met een versie-URI maakt, moet u een handmatige sleutelrotatie uitvoeren, zoals besproken in De versleutelingssleutel [roteren.](#rotating-the-encryption-key)

Voor clusters die zijn gemaakt vóór de release van november 2020, moet u sleutelrotatie handmatig uitvoeren met behulp van de sleutel-URI met versie-versie.

#### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Tijdens het maken van het cluster kunt u een versiesleutel of een versieloze sleutel op de volgende manier gebruiken:

- **Versieversie:** geef tijdens het maken van het cluster de volledige **sleutel-id** op, inclusief de sleutelversie. Bijvoorbeeld `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- **Versieloos:** geef tijdens het maken van het cluster alleen de **sleutel-id op.** Bijvoorbeeld `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

U moet ook de beheerde identiteit toewijzen aan het cluster.

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="Nieuw cluster maken":::

#### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voorbeeld ziet u hoe u Azure CLI gebruikt om een nieuw cluster Apache Spark maken met schijfversleuteling ingeschakeld. Zie Azure [CLI az hdinsight create voor meer informatie.](/cli/azure/hdinsight#az_hdinsight_create) De parameter `encryption-key-version` is optioneel.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

In het volgende voorbeeld ziet u hoe u een Azure Resource Manager gebruikt om een nieuw cluster Apache Spark maken met schijfversleuteling ingeschakeld. Zie Wat zijn [ARM-sjablonen? voor meer informatie.](../azure-resource-manager/templates/overview.md) De eigenschap Resource Manager-sjabloon `diskEncryptionKeyVersion` is optioneel.

In dit voorbeeld wordt PowerShell gebruikt om de sjabloon aan te roepen.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

De inhoud van de resourcebeheersjabloon, `azuredeploy.json` :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>De versleutelingssleutel roteren

U kunt de versleutelingssleutels die worden gebruikt op het cluster dat wordt uitgevoerd, wijzigen met behulp van de Azure Portal of Azure CLI. Voor deze bewerking moet het cluster toegang hebben tot zowel de huidige sleutel als de beoogde nieuwe sleutel, anders mislukt de draaibewerking van de sleutel. Voor clusters die zijn gemaakt na de release van november 2020, kunt u kiezen of u wilt dat uw nieuwe sleutel een versie heeft of niet. Voor clusters die zijn gemaakt vóór de release van november 2020, moet u een versiesleutel gebruiken bij het roteren van de versleutelingssleutel.

#### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u de sleutel wilt roteren, hebt u de basissleutelkluis-URI nodig. Zodra u dat hebt gedaan, gaat u naar de sectie Eigenschappen van HDInsight-cluster in de portal en klikt u op Sleutel wijzigen **onder** URL van **schijfversleutelingssleutel.** Voer de URL van de nieuwe sleutel in en verzend deze om de sleutel te draaien.

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="schijfversleutelingssleutel roteren":::

#### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voorbeeld ziet u hoe u de schijfversleutelingssleutel voor een bestaand HDInsight-cluster roteert. Zie Azure [CLI az hdinsight rotate-disk-encryption-key voor meer informatie.](/cli/azure/hdinsight#az_hdinsight_rotate_disk_encryption_key)

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Veelgestelde vragen over door de klant beheerde sleutelversleuteling

**Hoe krijgt het HDInsight-cluster toegang tot mijn sleutelkluis?**

HDInsight heeft toegang tot uw Azure Key Vault met behulp van de beheerde identiteit die u aan het HDInsight-cluster koppelt. Deze beheerde identiteit kan vóór of tijdens het maken van het cluster worden gemaakt. U moet de beheerde identiteit ook toegang verlenen tot de sleutelkluis waarin de sleutel is opgeslagen.

**Is deze functie beschikbaar voor alle clusters in HDInsight?**

Door de klant beheerde sleutelversleuteling is beschikbaar voor alle clustertypen, met uitzondering van Spark 2.1 en 2.2.

**Kan ik meerdere sleutels gebruiken om verschillende schijven of mappen te versleutelen?**

Nee, alle beheerde schijven en resourceschijven worden versleuteld met dezelfde sleutel.

**Wat gebeurt er als het cluster geen toegang meer heeft tot de sleutelkluis of de sleutel?**

Als het cluster geen toegang meer heeft tot de sleutel, worden waarschuwingen weergegeven in de Apache Ambari-portal. In deze status mislukt de bewerking **Sleutel** wijzigen. Zodra de sleuteltoegang is hersteld, verdwijnen Ambari-waarschuwingen en kunnen bewerkingen zoals sleutelrotatie met succes worden uitgevoerd.

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="Ambari-waarschuwing voor sleuteltoegang":::

**Hoe kan ik het cluster herstellen als de sleutels worden verwijderd?**

Omdat alleen voor 'Soft Delete' ingeschakelde sleutels worden ondersteund, moet het cluster weer toegang krijgen tot de sleutels als de sleutels worden hersteld in de sleutelkluis. Zie [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) of [az-keyvault-key-recover](/cli/azure/keyvault/key#az_keyvault_key_recover)als u een Azure Key Vault sleutel wilt herstellen.


**Als een cluster omhoog wordt geschaald, ondersteunen de nieuwe knooppunten dan naadloos door de klant beheerde sleutels?**

Ja. Het cluster moet toegang hebben tot de sleutel in de sleutelkluis tijdens het omhoog schalen. Dezelfde sleutel wordt gebruikt om zowel beheerde schijven als resourceschijven in het cluster te versleutelen.

**Zijn door de klant beheerde sleutels beschikbaar op mijn locatie?**

Door de klant beheerde sleutels van HDInsight zijn beschikbaar in alle openbare clouds en nationale clouds.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Versleuteling op de host met behulp van door het platform beheerde sleutels

### <a name="enable-in-the-azure-portal"></a>Inschakelen in Azure Portal

Versleuteling op de host kan worden ingeschakeld tijdens het maken van het cluster in Azure Portal.

> [!Note]
> Wanneer versleuteling op de host is ingeschakeld, kunt u vanuit Azure Marketplace geen toepassingen toevoegen aan uw HDInsight-cluster.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Versleuteling op de host inschakelen.":::

Met deze optie schakelt [u versleuteling op de host](../virtual-machines/disks-enable-host-based-encryption-portal.md) in voor tijdelijke gegevensschijven van HDInsight-VM's met behulp van PMK. Versleuteling op de host wordt alleen [ondersteund op bepaalde VM-SKU's in](../virtual-machines/disks-enable-host-based-encryption-portal.md) beperkte regio's en HDInsight ondersteunt de volgende [knooppuntconfiguratie en SKU's.](./hdinsight-supported-node-configuration.md)

Zie De juiste VM-grootte selecteren voor uw Azure HDInsight cluster voor meer informatie over de juiste [VM-grootte voor uw HDInsight-cluster.](hdinsight-selecting-vm-size.md) De standaard-VM-SKU voor Zookeeper-knooppunt wanneer versleuteling op de host is ingeschakeld, is DS2V2.

### <a name="enable-using-powershell"></a>Inschakelen met PowerShell

Het volgende codefragment laat zien hoe u een nieuw cluster Azure HDInsight met versleuteling op de host ingeschakeld met behulp van PowerShell. Hierbij wordt de parameter gebruikt `-EncryptionAtHost $true` om de functie in teschakelen.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Inschakelen met behulp van Azure CLI

Het volgende codefragment laat zien hoe u een nieuw cluster Azure HDInsight maken met versleuteling op host ingeschakeld, met behulp van Azure CLI. Hierbij wordt de parameter gebruikt `--encryption-at-host true` om de functie in teschakelen.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Volgende stappen

* Zie Wat is Azure Key Vault voor [meer informatie over Azure Key Vault.](../key-vault/general/overview.md)
* [Overzicht van bedrijfsbeveiliging in Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
