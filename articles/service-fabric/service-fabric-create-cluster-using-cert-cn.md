---
title: Een cluster maken met een algemene certificaat naam
description: Meer informatie over het maken van een Service Fabric cluster met behulp van een algemene certificaat naam uit een sjabloon.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c852b40d35f936753d3c16420159676da239b6c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86246432"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Een Service Fabric cluster implementeren dat de algemene certificaat naam gebruikt in plaats van een vinger afdruk
Er kunnen niet twee certificaten dezelfde vinger afdruk hebben, waardoor de rollover van het cluster certificaat of het beheer lastig wordt. Meerdere certificaten kunnen echter dezelfde algemene naam of hetzelfde onderwerp hebben.  Een cluster met behulp van algemene namen van certificaten maakt certificaat beheer veel eenvoudiger. In dit artikel wordt beschreven hoe u een Service Fabric cluster implementeert voor het gebruik van de algemene naam van het certificaat in plaats van de vinger afdruk van het certificaat.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Een certificaat ophalen
Haal eerst een certificaat op bij een certificerings [instantie (CA)](https://wikipedia.org/wiki/Certificate_authority).  De algemene naam van het certificaat moet gelden voor het aangepaste domein dat u bezit en dat u hebt gekocht van een domein registratie service. Bijvoorbeeld ' azureservicefabricbestpractices.com '; personen die geen mede werkers van micro soft zijn, kunnen geen certificaten voor MS-domeinen inrichten, dus u kunt de DNS-namen van uw LB of Traffic Manager niet gebruiken als algemene namen voor uw certificaat, en u moet een [Azure DNS zone](../dns/dns-delegate-domain-azure-dns.md) inrichten als uw aangepaste domein kan worden omgezet in Azure. U moet ook uw aangepaste domein met de naam ' managementEndpoint ' declareren als u wilt dat de Portal de aangepaste domein alias voor uw cluster weergeeft.

Voor test doeleinden kunt u een door een CA ondertekend certificaat ontvangen van een gratis of open certificerings instantie.

> [!NOTE]
> Zelfondertekende certificaten, inclusief verbindingen die zijn gegenereerd bij het implementeren van een Service Fabric cluster in de Azure Portal, worden niet ondersteund. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Upload het certificaat naar een sleutel kluis
In azure wordt een Service Fabric cluster geïmplementeerd op een schaalset met virtuele machines.  Upload het certificaat naar een sleutel kluis.  Wanneer het cluster wordt geïmplementeerd, wordt het certificaat geïnstalleerd op de schaalset voor virtuele machines waarop het cluster wordt uitgevoerd.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Een voorbeeld sjabloon downloaden en bijwerken
In dit artikel worden de voorbeeld sjabloon en sjabloon parameters [voor het beveiligde cluster met 5 knoop punten](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) gebruikt. Down load de *azuredeploy.jsop* en *azuredeploy.parameters.jsop* bestanden naar uw computer.

### <a name="update-parameters-file"></a>Parameter bestand bijwerken
Open eerst de *azuredeploy.parameters.jsin* het bestand in een tekst editor en voeg de volgende parameter waarde toe:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Stel vervolgens de para meters *certificateCommonName*, *sourceVaultValue* en *certificateUrlValue* in op de parameter waarden die worden geretourneerd door het voor gaande script:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Het sjabloonbestand bijwerken
Open vervolgens de *azuredeploy.jsin* het bestand in een tekst editor en maak drie updates ter ondersteuning van de algemene naam van het certificaat.

1. Voeg in de sectie **para meters** een *certificateCommonName* -para meter toe:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    U kunt ook de *certificateThumbprint* verwijderen. Dit is mogelijk niet meer nodig.

2. Stel de waarde van de variabele *sfrpApiVersion* in op ' 2018-02-01 ':
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. In de resource **micro soft. Compute/virtualMachineScaleSets** werkt u de extensie van de virtuele machine bij voor het gebruik van de algemene naam in certificaat instellingen in plaats van de vinger afdruk.  In instellingen voor eigenschappen van **virtualMachineProfile** -> **extensionProfile** -> -**extensies** ->  ->  -> **certificaat** toevoegen 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    en verwijderen `"thumbprint": "[parameters('certificateThumbprint')]",` .

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. Werk in de resource **micro soft. ServiceFabric/clusters** de API-versie bij naar ' 2018-02-01 '.  Voeg ook een **certificateCommonNames** -instelling toe met een **commonNames** -eigenschap en verwijder de **certificaat** instelling (met de eigenschap vinger afdruk), zoals in het volgende voor beeld:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > In het veld ' certificateIssuerThumbprint ' kunnen de verwachte verleners van certificaten worden opgegeven met een algemene naam voor het onderwerp. Dit veld accepteert een door komma's gescheiden opsomming van SHA1-vinger afdrukken. Houd er rekening mee dat dit een versterking vormt van de validatie van het certificaat. in het geval dat de verlener niet is opgegeven of leeg is, wordt het certificaat geaccepteerd voor verificatie als de keten kan worden samengesteld en wordt deze in een hoofd niveau die wordt vertrouwd door de validator, beëindigd. Als de verlener is opgegeven, wordt het certificaat geaccepteerd als de vinger afdruk van de directe verlener overeenkomt met een van de waarden die zijn opgegeven in dit veld, ongeacht of de basis wordt vertrouwd of niet. Houd er rekening mee dat een PKI mogelijk verschillende certificerings instanties gebruikt voor het uitgeven van certificaten voor hetzelfde onderwerp. Daarom is het belang rijk om alle verwachte uitgevers vingerafdrukken op te geven voor een bepaald onderwerp.
   >
   > Het opgeven van de uitgever wordt beschouwd als een best practice; Als u weglaat, blijft het werken voor certificaten die zijn gekoppeld aan een vertrouwde basis. dit gedrag heeft beperkingen en kan in de nabije toekomst worden gespreid. Clusters die zijn geïmplementeerd in Azure en die zijn beveiligd met x509-certificaten die zijn uitgegeven door een persoonlijke PKI en die zijn gedeclareerd door het onderwerp, kunnen mogelijk niet worden gevalideerd door de Azure Service Fabric-service (voor communicatie tussen services), als het certificaat beleid van de PKI niet detecteerbaar, beschikbaar en toegankelijk is. 

## <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Implementeer de bijgewerkte sjabloon opnieuw nadat u de wijzigingen hebt aangebracht.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [cluster beveiliging](service-fabric-cluster-security.md).
* Meer informatie over het [overkantelen van een cluster certificaat](service-fabric-cluster-rollover-cert-cn.md)
* [Cluster certificaten bijwerken en beheren](service-fabric-cluster-security-update-certs-azure.md)
* Vereenvoudig het beheer van certificaten door [het cluster te wijzigen van de vinger afdruk van het certificaat in de algemene naam](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
