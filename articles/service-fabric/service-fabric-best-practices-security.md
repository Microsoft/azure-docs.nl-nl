---
title: Best practices voor Azure Service Fabric-beveiliging
description: Aanbevolen procedures en ontwerp overwegingen voor het beveiligen van Azure Service Fabric-clusters en-toepassingen.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b7af0a4c26a47644973e936eb37e221853d74c03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784660"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric-beveiliging 

Lees voor meer informatie over [Aanbevolen procedures voor Azure-beveiliging](../security/index.yml)de [Aanbevolen procedures voor Azure service Fabric Security](../security/fundamentals/service-fabric-best-practices.md)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](../key-vault/index.yml) is de aanbevolen Service voor het beheer van geheimen voor Azure-service Fabric toepassingen en-clusters.
> [!NOTE]
> Als certificaten/geheimen van een Key Vault worden geïmplementeerd op een Schaalset voor virtuele machines als een geheim voor virtuele-machine schaal sets, moeten de Key Vault en de virtuele-machine Schaalset op hetzelfde niveau zijn opgeslagen.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Een door een certificerings instantie uitgegeven Service Fabric certificaat maken

Een Azure Key Vault certificaat kan worden gemaakt of geïmporteerd in een Key Vault. Wanneer er een Key Vault certificaat wordt gemaakt, wordt de persoonlijke sleutel in de Key Vault gemaakt en nooit zichtbaar voor de eigenaar van het certificaat. U kunt op de volgende manieren een certificaat maken in Key Vault:

- Maak een zelfondertekend certificaat voor het maken van een combi natie van open bare en persoonlijke sleutels en koppel deze aan een certificaat. Het certificaat wordt door een eigen sleutel ondertekend. 
- Maak hand matig een nieuw certificaat om een openbaar persoonlijk sleutel paar te maken en Genereer een X. 509-aanvraag voor certificaat ondertekening. De handtekening aanvraag kan worden ondertekend door uw registratie-instantie of certificerings instantie. Het ondertekende x509-certificaat kan worden samengevoegd met het sleutel paar in behandeling om het KV-certificaat in Key Vault te volt ooien. Hoewel deze methode meer stappen vereist, biedt dit u een betere beveiliging omdat de persoonlijke sleutel wordt gemaakt in en beperkt tot Key Vault. Dit wordt uitgelegd in het onderstaande diagram. 

Raadpleeg de methoden voor het [maken van Azure](../key-vault/certificates/create-certificate.md) -sleutel kluis voor meer informatie.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Key Vault certificaten implementeren voor het Service Fabric van virtuele-machine schaal sets voor clusters

Als u certificaten van een met co-locatie bewerkings sleutel wilt implementeren in een Schaalset met virtuele machines, gebruikt u [osProfile](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)van de virtuele machine Scale set. Hieronder vindt u de eigenschappen van de Resource Manager-sjabloon:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> De kluis moet zijn ingeschakeld voor de implementatie van Resource Manager-sjablonen.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Een Access Control-lijst (ACL) Toep assen op uw certificaat voor uw Service Fabric cluster

[Extensies voor virtuele-machine schaal sets](/cli/azure/vmss/extension) Publisher micro soft. Azure. ServiceFabric wordt gebruikt voor het configureren van de beveiliging van uw knoop punten.
Als u een ACL wilt Toep assen op uw certificaten voor uw Service Fabric cluster processen, gebruikt u de volgende eigenschappen van de Resource Manager-sjabloon:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Een Service Fabric cluster certificaat beveiligen op basis van algemene naam

Als u uw Service Fabric-cluster op basis van certificaten wilt beveiligen `Common Name` , gebruikt u de Resource Manager-sjabloon eigenschap [certificateCommonNames](/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), als volgt:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service Fabric clusters gebruiken het eerste geldige certificaat dat wordt gevonden in het certificaat archief van de host. In Windows is dit het certificaat met de laatste verval datum die overeenkomt met uw algemene naam en de vinger afdruk van de verlener.

Azure-domeinen, zoals * \<YOUR SUBDOMAIN\> . cloudapp.Azure.com of \<YOUR SUBDOMAIN\> . trafficmanager.net, zijn eigendom van micro soft. Certificerings instanties geven geen certificaten voor domeinen aan niet-gemachtigde gebruikers. De meeste gebruikers moeten een domein aanschaffen bij een registratie service of een geautoriseerde domein beheerder zijn, zodat een certificerings instantie u een certificaat met deze algemene naam kan uitgeven.

Lees voor meer informatie over het configureren van DNS-service voor het omzetten van uw domein naar een micro soft IP-adres hoe u [Azure DNS configureert om uw domein te hosten](../dns/dns-delegate-domain-azure-dns.md).

> [!NOTE]
> Voeg de volgende twee records toe aan uw DNS-zone nadat u de domein naam servers hebt gedelegeerd naar uw Azure DNS zone naam servers:
> - Een A-record voor domein APEX die niet van `Alias record set` alle IP-adressen van uw aangepaste domein is, wordt omgezet.
> - Een C-record voor micro soft-subdomeinen die u hebt ingericht en die geen zijn `Alias record set` . U kunt bijvoorbeeld uw Traffic Manager of de DNS-naam van Load Balancer gebruiken.

Als u uw portal wilt bijwerken om een aangepaste DNS-naam voor uw Service Fabric-cluster weer te geven `"managementEndpoint"` , werkt u de eigenschappen van de volgende service Fabric cluster resource manager-sjabloon bij:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>De geheime waarden van Service Fabric pakket worden versleuteld

Veelvoorkomende waarden die zijn versleuteld in Service Fabric-pakketten zijn onder andere Azure Container Registry (ACR) referenties, omgevings variabelen, instellingen en Azure volume plugin opslag account-sleutels.

Voor [het instellen van een versleutelings certificaat en het versleutelen van geheimen op Windows-clusters](./service-fabric-application-secret-management-windows.md):

Een zelfondertekend certificaat genereren voor het versleutelen van uw geheim:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Gebruik de instructies in [Deploy Key Vault Certificates to service Fabric Scale sets van virtuele cluster machines](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) om Key Vault certificaten te implementeren op de Virtual Machine Scale sets van uw service Fabric-cluster.

Versleutel uw geheim met behulp van de volgende Power shell-opdracht en werk vervolgens uw Service Fabric toepassings manifest bij met de versleutelde waarde:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Voor [het instellen van een versleutelings certificaat en het versleutelen van geheimen op Linux-clusters](./service-fabric-application-secret-management-linux.md):

Een zelfondertekend certificaat genereren voor het versleutelen van uw geheimen:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Gebruik de instructies in [Deploy Key Vault Certificates to service Fabric Scale sets van virtuele cluster machines](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) naar de Virtual Machine Scale sets van uw service Fabric-cluster.

Versleutel uw geheim met de volgende opdrachten en werk vervolgens uw Service Fabric toepassings manifest bij met de versleutelde waarde:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Nadat u uw beveiligde waarden hebt versleuteld, [geeft u versleutelde geheimen op in service Fabric toepassing](./service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application)en ontsleutelt u [versleutelde geheimen van service code](./service-fabric-application-secret-management.md#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Certificaat in Service Fabric toepassingen toevoegen

Als u uw toepassing toegang wilt geven tot geheimen, neemt u het certificaat op door een **SecretsCertificate** -element toe te voegen aan het toepassings manifest.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Service Fabric toepassingen verifiëren voor Azure-resources met behulp van Managed Service Identity (MSI)

Zie [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten voor Azure-resources.
Azure Service Fabric-clusters worden gehost op Virtual Machine Scale Sets, die [Managed Service Identity](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)ondersteunen.
Zie [Azure-Services die ondersteuning bieden voor Azure Active Directory-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)voor een lijst met services die door MSI kunnen worden gebruikt voor verificatie.


Voor het inschakelen van door het systeem toegewezen beheerde identiteit tijdens het maken van een schaalset voor virtuele machines of een bestaande schaalset voor virtuele machines declareert u de volgende `"Microsoft.Compute/virtualMachinesScaleSets"` eigenschap:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss.md#system-assigned-managed-identity) voor meer informatie.

Als u een door de  [gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)hebt gemaakt, declareert u de volgende resource in uw sjabloon om deze toe te wijzen aan de schaalset van de virtuele machine. Vervang door `\<USERASSIGNEDIDENTITYNAME\>` de naam van de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Voordat uw Service Fabric-toepassing gebruik kan maken van een beheerde identiteit, moeten er machtigingen worden verleend aan de Azure-resources die moeten worden geverifieerd bij.
Met de volgende opdrachten wordt toegang verleend aan een Azure-resource:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

In uw Service Fabric-toepassings code [kunt u een toegangs token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) voor Azure Resource Manager verkrijgen door het volgende te doen:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Uw Service Fabric-app kan vervolgens het toegangs token gebruiken om te verifiëren bij Azure-resources die Active Directory ondersteunen.
In het volgende voor beeld ziet u hoe u dit kunt doen voor Cosmos DB resource:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows-beveiligings basislijnen
[U wordt aangeraden een industrie standaard configuratie te implementeren die algemeen bekend en goed getest is, zoals micro soft-beveiligings basislijnen, in tegens telling tot het maken van een basis lijn](/windows/security/threat-protection/windows-security-baselines). een optie voor het inrichten van deze op uw Virtual Machine Scale Sets is het gebruik van de extensie-handler voor Azure desired state Configuration (DSC) om de virtuele machines te configureren zodra deze online zijn, zodat ze de productie software uitvoeren.

## <a name="azure-firewall"></a>Azure Firewall
[Azure firewall is een beheerde, Cloud service voor netwerk beveiliging die uw Azure Virtual Network-Resources beveiligt. Het is een volledig stateful firewall als een service met ingebouwde hoge Beschik baarheid en een onbeperkte schaal baarheid in de Cloud.](../firewall/overview.md) Hierdoor kan uitgaande HTTP/S-verkeer worden beperkt tot een opgegeven lijst met FQDN-namen (FULLy Qualified Domain names), inclusief joker tekens. Voor deze functie is geen TLS/SSL-beëindiging vereist. Het is raadzaam om [Azure firewall FQDN-Tags](../firewall/fqdn-tags.md) te gebruiken voor Windows-updates en om netwerk verkeer in te scha kelen naar micro soft Windows Update-eind punten door uw firewall kunnen stromen. [Azure firewall implementeren met behulp van een sjabloon](../firewall/deploy-template.md) biedt een voor beeld van de definitie van de resource sjabloon micro soft. Network/azureFirewalls. Firewall regels die gemeen schappelijk zijn voor Service Fabric toepassingen, zijn het volgende toe te staan voor uw clusters virtueel netwerk:

- * download.microsoft.com
- * servicefabric.azure.com
- *.core.windows.net

Deze firewall regels vormen een aanvulling op uw toegestane uitgaande netwerk beveiligings groepen, waaronder ServiceFabric en opslag, zoals toegestane bestemmingen van uw virtuele netwerk.

## <a name="tls-12"></a>TLS 1.2

Micro soft [Azure raadt](https://azure.microsoft.com/updates/azuretls12/) aan alle klanten de migratie te volt ooien naar oplossingen die TLS (trans port Layer security) 1,2 ondersteunen en ervoor te zorgen dat TLS 1,2 standaard wordt gebruikt.

Azure-Services, waaronder [service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/microsoft-azure-service-fabric-6-3-refresh-release-cu1-notes/ba-p/791493), hebben het technische werk voor het verwijderen van de afhankelijkheid van TLS 1.0/1.1-protocollen voltooid en bieden volledige ondersteuning voor klanten die hun werk belasting moeten configureren om alleen TLS 1,2-verbindingen te accepteren en te initiëren.

Klanten moeten hun door Azure gehoste workloads en on-premises toepassingen met Azure-Services configureren om TLS 1,2 standaard te gebruiken. Hier leest u hoe u [service Fabric cluster knooppunten en toepassingen kunt configureren](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md) voor het gebruik van een specifieke TLS-versie.

## <a name="windows-defender"></a>Windows Defender 

Windows Defender anti virus is standaard geïnstalleerd op Windows Server 2016. Zie [Windows Defender anti virus op Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)voor meer informatie. De gebruikersinterface wordt standaard geïnstalleerd op een aantal SKU's, maar is niet vereist. Als u de prestaties van het systeem en de overhead van het Resource verbruik dat door Windows Defender wordt gemaakt, wilt verminderen, en als uw beveiligings beleid toestaat dat u processen en paden voor open source-software uitsluit, declareert u de volgende eigenschappen van de extensie van de bron beheer sjabloon van de virtuele machine om uw Service Fabric cluster uit te sluiten van scans:


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
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
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

> [!NOTE]
> Raadpleeg de documentatie van uw anti-malware voor configuratie regels als u geen gebruik maakt van Windows Defender. Windows Defender wordt niet ondersteund in Linux.

## <a name="platform-isolation"></a>Platform isolatie
Service Fabric-toepassingen krijgen standaard toegang tot de Service Fabric runtime zelf, die zich in verschillende vormen bevindt: [omgevings variabelen](service-fabric-environment-variables-reference.md) die verwijzen naar bestands paden op de host die overeenkomt met toepassings-en infrastructuur bestanden, een interproces communicatie-eind punt dat toepassingsspecifieke aanvragen accepteert, en het client certificaat dat door de toepassing wordt gebruikt om zichzelf te verifiëren. Als de service zichzelf niet-vertrouwde code host, is het raadzaam om deze toegang uit te scha kelen naar de SF-runtime, tenzij dit expliciet nodig is. Toegang tot de runtime wordt verwijderd met behulp van de volgende verklaring in de sectie beleid van het toepassings manifest: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op Vm's of computers waarop Windows Server wordt uitgevoerd: [service Fabric cluster maken voor Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Een cluster maken op Vm's of computers waarop Linux wordt uitgevoerd: [een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md).
* Meer informatie over [service Fabric ondersteunings opties](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
