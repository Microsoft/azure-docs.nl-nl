---
title: Dubbele versleuteling voor Data-at-rest
titleSuffix: Azure HDInsight
description: In dit artikel worden de twee coderings lagen beschreven die beschikbaar zijn voor Data-at-rest in azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: a9a90fbb2eedd6db2873d4ac2a5fea94c05c7eed
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005653"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Dubbele versleuteling van Azure HDInsight voor Data-at-rest

In dit artikel worden methoden beschreven voor het versleutelen van gegevens in rust in azure HDInsight-clusters. Gegevens versleuteling in rust verwijst naar versleuteling op beheerde schijven (gegevens schijven, besturingssysteem schijven en tijdelijke schijven) die zijn gekoppeld aan virtuele machines van het HDInsight-cluster. 

Dit document bevat geen informatie over de gegevens die zijn opgeslagen in uw Azure Storage-account. Uw clusters kunnen een of meer gekoppelde Azure Storage-accounts bevatten waarbij de versleutelings sleutels ook door micro soft worden beheerd of door de klant worden beheerd, maar de versleutelings service verschilt. Zie [Azure Storage encryption for Data at rest](../storage/common/storage-service-encryption.md)(Engelstalig) voor meer informatie over Azure Storage versleuteling.

## <a name="introduction"></a>Introductie

Er zijn drie hoofd rollen voor beheerde schijven in Azure: de gegevens schijf, de besturingssysteem schijf en de tijdelijke schijf. Zie [Introduction to Azure Managed disks](../virtual-machines/managed-disks-overview.md)(Engelstalig) voor meer informatie over verschillende typen Managed disks. 

HDInsight ondersteunt meerdere typen versleuteling in twee verschillende lagen:

- Server side Encryption (SSE): SSE wordt uitgevoerd door de opslag service. In HDInsight wordt SSE gebruikt voor het versleutelen van besturingssysteem schijven en gegevens schijven. Het is standaard ingeschakeld. SSE is een laag 1-versleutelings service.
- Versleuteling op de host met een door een platform beheerde sleutel, vergelijkbaar met SSE, wordt dit type versleuteling uitgevoerd door de opslag service. Het is echter alleen voor tijdelijke schijven en is standaard niet ingeschakeld. Versleuteling op host is ook een laag 1-versleutelings service.
- Versleuteling in rust met door de klant beheerde sleutel: dit type versleuteling kan worden gebruikt voor gegevens en tijdelijke schijven. Het is standaard niet ingeschakeld en vereist dat de klant hun eigen sleutel verstrekt via Azure Key kluis. Versleuteling op rest is een Layer 2-versleutelings service.

Deze typen worden in de volgende tabel samenvatten.

|Clustertype |BESTURINGSSYSTEEM schijf (beheerde schijf) |Gegevens schijf (beheerde schijf) |Tijdelijke gegevens schijf (lokale SSD) |
|---|---|---|---|
|Kafka, HBase met versnelde schrijf bewerkingen|Layer1: standaard instelling voor [SSE-versleuteling](../virtual-machines/managed-disks-overview.md#encryption)|Layer1: [SSE-versleuteling](../virtual-machines/managed-disks-overview.md#encryption) standaard, Layer2: optionele versleuteling bij rest met behulp van CMK|Layer1: optionele versleuteling op host met behulp van PMK, Layer2: optionele versleuteling bij rest met behulp van CMK|
|Alle andere clusters (Spark, Interactive, Hadoop, HBase zonder versnelde schrijf bewerkingen)|Layer1: standaard instelling voor [SSE-versleuteling](../virtual-machines/managed-disks-overview.md#encryption)|N.v.t.|Layer1: optionele versleuteling op host met behulp van PMK, Layer2: optionele versleuteling bij rest met behulp van CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Versleuteling in rust met door de klant beheerde sleutels

Door de klant beheerde sleutel versleuteling is een proces dat uit één stap wordt verwerkt tijdens het maken van het cluster zonder extra kosten. U hoeft alleen maar een beheerde identiteit te autoriseren met Azure Key Vault en de versleutelings sleutel toe te voegen wanneer u uw cluster maakt.

Zowel gegevens schijven als tijdelijke schijven op elk knoop punt van het cluster worden versleuteld met een symmetrische gegevens versleutelings sleutel (DEK). De DEK wordt beschermd met behulp van de sleutel versleutelings sleutel (KEK) van uw sleutel kluis. De processen voor versleuteling en ontsleuteling worden volledig verwerkt door Azure HDInsight.

Voor besturingssysteem schijven die aan de cluster-Vm's zijn gekoppeld, is er slechts één laag van versleuteling (PMK) beschikbaar. Het is raadzaam om te voor komen dat klanten gevoelige gegevens kopiëren naar besturingssysteem schijven als er een CMK-versleuteling is vereist voor de scenario's.

Als de sleutel kluis firewall is ingeschakeld op de sleutel kluis waarop de schijf versleutelings sleutel is opgeslagen, moeten de IP-adressen van de HDInsight regionale resource provider voor de regio waar het cluster wordt geïmplementeerd, worden toegevoegd aan de firewall configuratie van de sleutel kluis. Dit is nodig omdat HDInsight geen vertrouwde Azure Key kluis-service is.

U kunt de Azure Portal of Azure CLI gebruiken om de sleutels in de sleutel kluis veilig te draaien. Wanneer een sleutel draait, begint het HDInsight-cluster binnen enkele minuten met de nieuwe sleutel. Schakel de beveiliging van de functie voor het [voorlopig verwijderen](../key-vault/general/soft-delete-overview.md) van sleutels in voor beveiliging tegen Ransomware-scenario's en onbedoeld verwijderen. Sleutel kluizen zonder deze beveiligings functie worden niet ondersteund.

### <a name="get-started-with-customer-managed-keys"></a>Aan de slag met door de klant beheerde sleutels

Als u een HDInsight-cluster met door de klant beheerde sleutels wilt maken, gaan we verder met de volgende stappen:

1. Beheerde identiteiten maken voor Azure-resources
1. Azure Key Vault maken
1. Sleutel maken
1. Toegangs beleid maken
1. HDInsight-cluster maken met door de klant beheerde sleutel ingeschakeld
1. De versleutelings sleutel draaien

Elke stap wordt uitvoerig beschreven in een van de volgende secties.

### <a name="create-managed-identities-for-azure-resources"></a>Beheerde identiteiten maken voor Azure-resources

Maak een door de gebruiker toegewezen beheerde identiteit om te verifiëren bij Key Vault.

Zie [een door de gebruiker toegewezen beheerde identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) voor specifieke stappen. Zie [beheerde identiteiten in azure hdinsight](hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight. Zorg ervoor dat u de resource-ID van de beheerde ID opslaat wanneer u deze toevoegt aan het Key Vault toegangs beleid.

### <a name="create-azure-key-vault"></a>Azure Key Vault maken

Een sleutelkluis maken. Zie [Azure Key Vault maken](../key-vault/general/quick-create-portal.md) voor specifieke stappen.

HDInsight ondersteunt alleen Azure Key Vault. Als u uw eigen sleutel kluis hebt, kunt u uw sleutels importeren in Azure Key Vault. Houd er rekening mee dat voor de sleutel kluis **voorlopig verwijderen** moet zijn ingeschakeld. Ga voor meer informatie over het importeren van bestaande sleutels naar [sleutels, geheimen en certificaten](../key-vault/general/about-keys-secrets-certificates.md).

### <a name="create-key"></a>Sleutel maken

1. Navigeer vanuit uw nieuwe sleutel kluis naar **instellingen**  >  **sleutels**  >  **+ genereren/importeren**.

    ![Een nieuwe sleutel in Azure Key Vault genereren](./media/disk-encryption/create-new-key.png "Een nieuwe sleutel in Azure Key Vault genereren")

1. Geef een naam op en selecteer **maken**. Het standaard **sleutel type** van **RSA** onderhouden.

    ![genereert sleutel naam](./media/disk-encryption/create-key.png "Sleutel naam genereren")

1. Wanneer u terugkeert naar de pagina **sleutels** , selecteert u de sleutel die u hebt gemaakt.

    ![sleutel kluis sleutel lijst](./media/disk-encryption/key-vault-key-list.png)

1. Selecteer de versie om de pagina met de **sleutel versie** te openen. Wanneer u uw eigen sleutel voor HDInsight-cluster versleuteling gebruikt, moet u de sleutel-URI opgeven. Kopieer de **sleutel-id** en sla deze ergens op totdat u klaar bent om uw cluster te maken.

    ![sleutel-id ophalen](./media/disk-encryption/get-key-identifier.png)

### <a name="create-access-policy"></a>Toegangs beleid maken

1. Ga vanuit de nieuwe sleutel kluis naar **instellingen**  >  **toegangs beleid**  >  **+ toegangs beleid toevoegen**.

    ![Nieuw toegangs beleid voor Azure Key Vault maken](./media/disk-encryption/key-vault-access-policy.png)

1. Geef op de pagina **toegangs beleid toevoegen** de volgende informatie op:

    |Eigenschap |Beschrijving|
    |---|---|
    |Sleutel machtigingen|Selecteer **ophalen**, **deterugloop sleutel** en **Terugloop sleutel**.|
    |Geheime machtigingen|Selecteer **ophalen**, **instellen** en **verwijderen**.|
    |Principal selecteren|Selecteer de door de gebruiker toegewezen beheerde identiteit die u eerder hebt gemaakt.|

    ![Selecteer de principal voor Azure Key Vault toegangs beleid instellen](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Selecteer **Toevoegen**.

1. Selecteer **Opslaan**.

    ![Azure Key Vault toegangs beleid opslaan](./media/disk-encryption/add-key-vault-access-policy-save.png)

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Een cluster maken met door de klant beheerde sleutel schijf versleuteling

U bent nu klaar om een nieuw HDInsight-cluster te maken. Door de klant beheerde sleutels kunnen alleen worden toegepast op nieuwe clusters tijdens het maken van het cluster. Versleuteling kan niet worden verwijderd uit door de klant beheerde sleutel clusters en door de klant beheerde sleutels kunnen niet worden toegevoegd aan bestaande clusters.

#### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Geef tijdens het maken van het cluster de volledige **sleutel-id** op, met inbegrip van de sleutel versie. Bijvoorbeeld `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. U moet ook de beheerde identiteit toewijzen aan het cluster en de sleutel-URI opgeven.

![Nieuw cluster maken](./media/disk-encryption/create-cluster-portal.png)

#### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voor beeld ziet u hoe u Azure CLI gebruikt om een nieuw Apache Spark-cluster te maken waarvoor schijf versleuteling is ingeschakeld. Zie [Azure cli AZ hdinsight Create](/cli/azure/hdinsight#az-hdinsight-create)voor meer informatie.

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

In het volgende voor beeld ziet u hoe u een Azure Resource Manager sjabloon gebruikt om een nieuw Apache Spark-cluster te maken waarvoor schijf versleuteling is ingeschakeld. Zie [Wat zijn arm-sjablonen?](../azure-resource-manager/templates/overview.md)voor meer informatie.

In dit voor beeld wordt Power shell gebruikt om de sjabloon aan te roepen.

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

De inhoud van de resource beheer sjabloon, `azuredeploy.json` :

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

### <a name="rotating-the-encryption-key"></a>De versleutelings sleutel draaien

Er zijn mogelijk scenario's waarin u mogelijk de versleutelings sleutels wilt wijzigen die door het HDInsight-cluster worden gebruikt nadat het is gemaakt. Dit kan eenvoudig zijn via de portal. Voor deze bewerking moet het cluster toegang hebben tot zowel de huidige sleutel als de beoogde nieuwe sleutel, anders kan de bewerking voor het draaien van de sleutel niet worden uitgevoerd.

#### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u de sleutel wilt draaien, hebt u de URL van de basis sleutel kluis nodig. Als u dat hebt gedaan, gaat u naar de sectie eigenschappen van HDInsight-cluster in de portal en klikt u op de **sleutel wijzigen** onder URL voor de **schijf versleutelings sleutel**. Voer in de nieuwe sleutel-URL in en verzend de sleutel om deze te draaien.

![schijf versleutelings sleutel draaien](./media/disk-encryption/change-key.png)

#### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voor beeld ziet u hoe u de schijf versleutelings sleutel voor een bestaand HDInsight-cluster roteert. Zie [Azure cli AZ hdinsight Rotate-Disk-Encryption-Key](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key)voor meer informatie.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Veelgestelde vragen over versleuteling door de klant beheerde sleutel

**Hoe opent het HDInsight-cluster mijn sleutel kluis?**

HDInsight opent uw Azure Key Vault-exemplaar met behulp van de beheerde identiteit die u aan het HDInsight-cluster koppelt. Deze beheerde identiteit kan worden gemaakt vóór of tijdens het maken van het cluster. U moet ook de beheerde identiteits toegang verlenen aan de sleutel kluis waar de sleutel wordt opgeslagen.

**Is deze functie beschikbaar voor alle clusters in HDInsight?**

Door de klant beheerde sleutel versleuteling is beschikbaar voor alle cluster typen behalve Spark 2,1 en 2,2.

**Kan ik meerdere sleutels gebruiken voor het versleutelen van verschillende schijven of mappen?**

Nee, alle beheerde schijven en bron schijven worden versleuteld met dezelfde sleutel.

**Wat gebeurt er als het cluster geen toegang meer heeft tot de sleutel kluis of de sleutel?**

Als het cluster de toegang tot de sleutel verliest, worden waarschuwingen weer gegeven in de Apache Ambari-Portal. In deze status mislukt de **wijzigings sleutel** bewerking. Zodra de toegang tot de sleutel is hersteld, worden de Ambari-waarschuwingen verwijderd en worden bewerkingen, zoals het draaien van sleutels, kunnen worden uitgevoerd.

![Ambari-waarschuwing voor Key Access](./media/disk-encryption/ambari-alert.png)

**Hoe kan ik het cluster herstellen als de sleutels worden verwijderd?**

Omdat alleen de sleutels "voorlopig verwijderen" worden ondersteund en de sleutels worden hersteld in de sleutel kluis, moet het cluster weer toegang krijgen tot de sleutels. Als u een Azure Key Vault sleutel wilt herstellen, raadpleegt u [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) of [AZ-sleutel kluis-Key-Recover](/cli/azure/keyvault/key#az-keyvault-key-recover).


**Als een cluster omhoog wordt geschaald, worden door de nieuwe knoop punten naadloos door de klant beheerde sleutels ondersteund?**

Ja. Het cluster moet toegang hebben tot de sleutel in de sleutel kluis tijdens het omhoog schalen. Dezelfde sleutel wordt gebruikt voor het versleutelen van zowel beheerde schijven als bron schijven in het cluster.

**Zijn de door de klant beheerde sleutels beschikbaar op mijn locatie?**

Door de klant beheerde sleutels van HDInsight zijn beschikbaar in alle open bare Clouds en nationale Clouds.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Versleuteling op de host met door het platform beheerde sleutels

### <a name="enable-in-the-azure-portal"></a>Inschakelen in de Azure Portal

Versleuteling op host kan worden ingeschakeld tijdens het maken van het cluster in de Azure Portal.

> [!Note]
> Wanneer versleuteling op host is ingeschakeld, kunt u geen toepassingen toevoegen aan uw HDInsight-cluster via Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Versleuteling inschakelen op de host.":::

Met deze optie schakelt u [versleuteling op de host in](../virtual-machines/disks-enable-host-based-encryption-portal.md) voor virtuele HDInsight-vm's tijdelijke gegevens schijven met behulp van PMK. Versleuteling op host wordt alleen [ondersteund op bepaalde VM-sku's in een beperkt aantal regio's](../virtual-machines/disks-enable-host-based-encryption-portal.md) en HDInsight ondersteunt de [volgende knooppunt configuratie en sku's](./hdinsight-supported-node-configuration.md).

Zie [de juiste VM-grootte voor uw Azure hdinsight-cluster selecteren](hdinsight-selecting-vm-size.md)als u meer wilt weten over de juiste VM-grootte voor uw hdinsight-cluster. De standaard-VM-SKU voor het Zookeeper-knoop punt wanneer versleuteling op host is ingeschakeld, wordt DS2V2.

### <a name="enable-using-powershell"></a>Inschakelen met PowerShell

Het volgende code fragment laat zien hoe u een nieuw Azure HDInsight-cluster kunt maken dat versleuteling op host heeft met behulp van Power shell. De para meter wordt gebruikt `-EncryptionAtHost $true` om de functie in te scha kelen.

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

Het volgende code fragment laat zien hoe u een nieuw Azure HDInsight-cluster kunt maken dat versleuteling op host heeft ingeschakeld, met behulp van Azure CLI. De para meter wordt gebruikt `--encryption-at-host true` om de functie in te scha kelen.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Azure Key Vault](../key-vault/general/overview.md)voor meer informatie over Azure Key Vault.
* [Overzicht van ENTER prise Security in azure HDInsight](./domain-joined/hdinsight-security-overview.md).
