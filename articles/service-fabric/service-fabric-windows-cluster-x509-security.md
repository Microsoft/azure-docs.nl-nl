---
title: Een cluster op Windows beveiligen met behulp van certificaten
description: Beveiligde communicatie binnen een Azure-Service Fabric zelfstandig of op een on-premises cluster, en tussen clients en het cluster.
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: d75c644be47ea44f6a8a6ccac91b785af0132833
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791034"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Een zelfstandige cluster in Windows beveiligen met behulp van X. 509-certificaten
In dit artikel wordt beschreven hoe u de communicatie tussen de verschillende knoop punten van uw zelfstandige Windows-cluster kunt beveiligen. Ook wordt beschreven hoe u clients verifieert die verbinding maken met dit cluster met behulp van X. 509-certificaten. Verificatie zorgt ervoor dat alleen gemachtigde gebruikers toegang hebben tot het cluster en de geïmplementeerde toepassingen en beheer taken uitvoeren. Certificaat beveiliging moet worden ingeschakeld op het cluster als het cluster wordt gemaakt.  

Zie [scenario's voor cluster beveiliging](service-fabric-cluster-security.md)voor meer informatie over de beveiliging van het cluster, zoals de beveiliging van knoop punt naar knoop punt, beveiliging van client naar knoop punt en toegangs beheer op basis van rollen.

## <a name="which-certificates-do-you-need"></a>Welke certificaten hebt u nodig?
Om te beginnen, moet u [het service Fabric voor Windows Server-pakket downloaden](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) naar een van de knoop punten in uw cluster. In het gedownloade pakket vindt u een ClusterConfig.X509.MultiMachine.jsin het bestand. Open het bestand en controleer de sectie voor beveiliging in het gedeelte Eigenschappen:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

In deze sectie worden de certificaten beschreven die u nodig hebt om uw zelfstandige Windows-cluster te beveiligen. Als u een cluster certificaat opgeeft, stelt u de waarde van ClusterCredentialType in op _x509_. Als u een server certificaat voor externe verbindingen opgeeft, stelt u de ServerCredentialType in op _x509_. Hoewel dit niet verplicht is, raden we u aan beide certificaten te hebben voor een goed beveiligd cluster. Als u deze waarden instelt op *x509*, moet u ook de bijbehorende certificaten opgeven of service Fabric een uitzonde ring genereert. In sommige scenario's wilt u misschien alleen de _ClientCertificateThumbprints_ of de _ReverseProxyCertificate_ opgeven. In deze scenario's hoeft u _ClusterCredentialType_ of _ServerCredentialType_ niet in te stellen op _x509_.


> [!NOTE]
> Een [vinger afdruk](https://en.wikipedia.org/wiki/Public_key_fingerprint) is de primaire identiteit van een certificaat. Zie [een vinger afdruk van een certificaat ophalen](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)voor informatie over de vinger afdruk van de certificaten die u maakt.
> 
> 

De volgende tabel geeft een lijst van de certificaten die u nodig hebt voor de installatie van het cluster:

| **CertificateInformation-instelling** | **Beschrijving** |
| --- | --- |
| ClusterCertificate |Aanbevolen voor een test omgeving. Dit certificaat is vereist voor het beveiligen van de communicatie tussen de knoop punten in een cluster. U kunt twee verschillende certificaten, een primaire en een secundaire, voor upgrades gebruiken. Stel de vinger afdruk van het primaire certificaat in in het gedeelte vinger afdruk en de secundaire in de ThumbprintSecondary variabelen. |
| ClusterCertificateCommonNames |Aanbevolen voor een productie omgeving. Dit certificaat is vereist voor het beveiligen van de communicatie tussen de knoop punten in een cluster. U kunt een of twee algemene namen van cluster certificaten gebruiken. De CertificateIssuerThumbprint komt overeen met de vinger afdruk van de verlener van dit certificaat. Als er meer dan één certificaat met dezelfde algemene naam wordt gebruikt, kunt u meerdere verleners vingerafdrukken opgeven.|
| ClusterCertificateIssuerStores |Aanbevolen voor een productie omgeving. Dit certificaat komt overeen met de uitgever van het cluster certificaat. U kunt de algemene naam van de certificaat verlener en de bijbehorende winkel naam onder deze sectie opgeven, in plaats van de vinger afdruk van de verlener onder ClusterCertificateCommonNames.  Dit maakt het eenvoudig om certificaten van de cluster uitgever te rolloveren. Meerdere verleners kunnen worden opgegeven als meer dan één cluster certificaat wordt gebruikt. Een lege IssuerCommonName staat alle certificaten in de bijbehorende archieven toe die zijn opgegeven onder X509StoreNames.|
| ServerCertificate |Aanbevolen voor een test omgeving. Dit certificaat wordt weer gegeven aan de client wanneer deze probeert verbinding te maken met dit cluster. Voor het gemak kunt u ervoor kiezen om hetzelfde certificaat te gebruiken voor ClusterCertificate en ServerCertificate. U kunt twee verschillende server certificaten, een primaire en een secundaire, voor upgrades gebruiken. Stel de vinger afdruk van het primaire certificaat in in het gedeelte vinger afdruk en de secundaire in de ThumbprintSecondary variabelen. |
| ServerCertificateCommonNames |Aanbevolen voor een productie omgeving. Dit certificaat wordt weer gegeven aan de client wanneer deze probeert verbinding te maken met dit cluster. De CertificateIssuerThumbprint komt overeen met de vinger afdruk van de verlener van dit certificaat. Als er meer dan één certificaat met dezelfde algemene naam wordt gebruikt, kunt u meerdere verleners vingerafdrukken opgeven. Voor het gemak kunt u ervoor kiezen om hetzelfde certificaat te gebruiken voor ClusterCertificateCommonNames en ServerCertificateCommonNames. U kunt een of twee algemene namen voor server certificaten gebruiken. |
| ServerCertificateIssuerStores |Aanbevolen voor een productie omgeving. Dit certificaat komt overeen met de uitgever van het server certificaat. U kunt de algemene naam van de certificaat verlener en de bijbehorende winkel naam onder deze sectie opgeven, in plaats van de vinger afdruk van de verlener onder ServerCertificateCommonNames.  Dit maakt het eenvoudig om certificaten van de server verlener te rolloveren. Meerdere verleners kunnen worden opgegeven als er meerdere server certificaten worden gebruikt. Een lege IssuerCommonName staat alle certificaten in de bijbehorende archieven toe die zijn opgegeven onder X509StoreNames.|
| ClientCertificateThumbprints |Installeer deze reeks certificaten op de geverifieerde clients. U kunt een aantal verschillende client certificaten hebben geïnstalleerd op de computers die u toegang wilt geven tot het cluster. Stel de vinger afdruk van elk certificaat in de variabele CertificateThumbprint in. Als u IsAdmin instelt op *True*, kan de client waarop dit certificaat is geïnstalleerd, beheer activiteiten op het cluster uitvoeren. Als IsAdmin *False* is, kan de client met dit certificaat de acties uitvoeren die alleen worden toegestaan voor gebruikers toegangs rechten, meestal alleen-lezen. Zie [service Fabric op rollen gebaseerd toegangs beheer](service-fabric-cluster-security.md#service-fabric-role-based-access-control)voor meer informatie over rollen. |
| ClientCertificateCommonNames |Stel de algemene naam in van het eerste client certificaat voor de CertificateCommonName. De CertificateIssuerThumbprint is de vinger afdruk voor de verlener van dit certificaat. Zie [werken met certificaten](/dotnet/framework/wcf/feature-details/working-with-certificates)voor meer informatie over algemene namen en de uitgever. |
| ClientCertificateIssuerStores |Aanbevolen voor een productie omgeving. Dit certificaat komt overeen met de verlener van het client certificaat (zowel admin-als niet-beheerders rollen). U kunt de algemene naam van de certificaat verlener en de bijbehorende winkel naam onder deze sectie opgeven, in plaats van de vinger afdruk van de verlener onder ClientCertificateCommonNames.  Dit maakt het eenvoudig om certificaten van client Issues te laten overschakelen. Meerdere verleners kunnen worden opgegeven als er meerdere client certificaten worden gebruikt. Een lege IssuerCommonName staat alle certificaten in de bijbehorende archieven toe die zijn opgegeven onder X509StoreNames.|
| ReverseProxyCertificate |Aanbevolen voor een test omgeving. Dit optionele certificaat kan worden opgegeven als u de [omgekeerde proxy](service-fabric-reverseproxy.md)wilt beveiligen. Zorg ervoor dat reverseProxyEndpointPort is ingesteld in nodeTypes als u dit certificaat gebruikt. |
| ReverseProxyCertificateCommonNames |Aanbevolen voor een productie omgeving. Dit optionele certificaat kan worden opgegeven als u de [omgekeerde proxy](service-fabric-reverseproxy.md)wilt beveiligen. Zorg ervoor dat reverseProxyEndpointPort is ingesteld in nodeTypes als u dit certificaat gebruikt. |

Hier volgt een voor beeld van een cluster configuratie waarin de cluster-, server-en client certificaten zijn opgenomen. Voor cluster/server-reverseProxy certificaten kan de vinger afdruk en de algemene naam niet samen worden geconfigureerd voor hetzelfde certificaat type.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Certificaat overschakeling
Wanneer u een algemene certificaat naam gebruikt in plaats van een vinger afdruk, is voor certificaat overschakeling geen cluster configuratie-upgrade vereist. Zorg ervoor dat de nieuwe lijst met vinger afdruk voor de upgrade van de certificaat verleners samen met de oude wordt weer geven. U moet eerst een configuratie-upgrade uitvoeren met de nieuwe vinger afdrukken van de verlener, en vervolgens de nieuwe certificaten (cluster/server certificaat en certificaten van certificerings instanties) installeren in de Store. Bewaar het oude certificaat van de certificaat houder ten minste twee uur na de installatie van het nieuwe certificaat van de certificerings instantie.
Als u de archiefen van de uitgever gebruikt, moet er geen configuratie-upgrade worden uitgevoerd voor de certificaat overschakeling van de verlener. Installeer het nieuwe certificaat van de certificerings instantie met een laatste verval datum in het bijbehorende certificaat archief en verwijder het oude certificaat van de certificerings instantie na een paar uur.

## <a name="acquire-the-x509-certificates"></a>De X. 509-certificaten ophalen
Als u de communicatie binnen het cluster wilt beveiligen, moet u eerst X. 509-certificaten voor uw cluster knooppunten verkrijgen. Als u de verbinding met dit cluster met geautoriseerde computers/gebruikers wilt beperken, moet u bovendien certificaten voor de client computers verkrijgen en installeren.

Voor clusters met productie-workloads gebruikt u een door de [certificerings instantie (CA)](https://en.wikipedia.org/wiki/Certificate_authority)ondertekend X. 509-certificaat om het cluster te beveiligen. Zie [How to Schaf a Certificate (een certificaat verkrijgen](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf)) voor meer informatie over het verkrijgen van deze certificaten. 

Er zijn een aantal eigenschappen die het certificaat moet hebben om goed te kunnen functioneren:

* De provider van het certificaat moet **micro soft Enhanced RSA en AES Cryptographic Provider** zijn

* Wanneer u een RSA-sleutel maakt, moet u ervoor zorgen dat de sleutel **2048 bits** is.

* De uitbrei ding voor sleutel gebruik heeft de waarde **digitale hand tekening, sleutel codering (a0)**

* De uitbrei ding voor uitgebreid sleutel gebruik heeft waarden voor **Server verificatie** (OID: 1.3.6.1.5.5.7.3.1) en **client verificatie** (OID: 1.3.6.1.5.5.7.3.2)

Voor clusters die u voor test doeleinden gebruikt, kunt u ervoor kiezen om een zelfondertekend certificaat te gebruiken.

Raadpleeg [Veelgestelde vragen over certificaten](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions)voor aanvullende vragen.

## <a name="optional-create-a-self-signed-certificate"></a>Optioneel: een zelfondertekend certificaat maken
Eén manier om een zelfondertekend certificaat te maken dat goed kan worden beveiligd, is door het CertSetup.ps1 script te gebruiken in de map Service Fabric SDK in de map C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Bewerk dit bestand om de standaard naam van het certificaat te wijzigen. (Zoek naar de waarde CN = ServiceFabricDevClusterCert.) Voer dit script uit als `.\CertSetup.ps1 -Install` .

Exporteer het certificaat nu naar een. pfx-bestand met een beveiligd wacht woord. Haal eerst de vinger afdruk van het certificaat op. 
1. Voer in het menu **Start** de optie **computer certificaten beheren** uit. 

2. Ga naar de **lokale map Computer\Personal** en zoek het certificaat dat u hebt gemaakt. 

3. Dubbel klik op het certificaat om het te openen, selecteer het tabblad **Details** en schuif omlaag naar het veld **vinger afdruk** . 

4. Verwijder de spaties en kopieer de waarde van de vinger afdruk naar de volgende Power shell-opdracht. 

5. Wijzig de `String` waarde in een geschikt beveiligd wacht woord om het te beveiligen en voer het volgende uit in Power shell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Voer de volgende Power shell-opdracht uit om de details te zien van een certificaat dat op de computer is geïnstalleerd:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Als u een Azure-abonnement hebt, volgt u de stappen in [een service Fabric-cluster maken met behulp van Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>De certificaten installeren
Nadat u certificaten hebt, kunt u deze installeren op de cluster knooppunten. Op uw knoop punten moet de meest recente Windows Power Shell 3. x zijn geïnstalleerd. Herhaal deze stappen voor elk knoop punt voor cluster-en server certificaten en eventuele secundaire certificaten.

1. Kopieer het pfx-bestand of de bestanden naar het knoop punt.

2. Open een Power shell-venster als beheerder en voer de volgende opdrachten in. Vervang *$Pswd* door het wacht woord dat u hebt gebruikt om dit certificaat te maken. Vervang *$PfxFilePath* door het volledige pad van het pfx-bestand dat is gekopieerd naar dit knoop punt.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Stel nu het toegangs beheer in voor dit certificaat zodat het Service Fabric proces, dat wordt uitgevoerd onder het netwerk service account, het kan gebruiken door het volgende script uit te voeren. Geef de vinger afdruk van het certificaat en de **netwerk service** voor het service account op. U kunt controleren of de acl's op het certificaat juist zijn door het certificaat te openen in **Start**  >  **computer certificaten beheren** en te kijken naar **alle taken**  >  **persoonlijke sleutels beheren**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Herhaal de vorige stappen voor elk server certificaat. U kunt deze stappen ook gebruiken om de client certificaten te installeren op de computers die u toegang wilt geven tot het cluster.

## <a name="create-the-secure-cluster"></a>Het beveiligde cluster maken
Nadat u de sectie beveiliging van de ClusterConfig.X509.MultiMachine.jsin het bestand hebt geconfigureerd, kunt u door gaan naar de sectie [het cluster maken](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) voor het configureren van de knoop punten en het maken van het zelfstandige cluster. Vergeet niet om de ClusterConfig.X509.MultiMachine.jsin het bestand te gebruiken terwijl u het cluster maakt. Uw opdracht kan er bijvoorbeeld als volgt uitzien:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Nadat u het beveiligde zelfstandige Windows-cluster hebt uitgevoerd en de geverifieerde clients hebt ingesteld om er verbinding mee te maken, volgt u de stappen in de sectie [verbinding maken met een cluster met behulp van Power shell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) om verbinding mee te maken. Bijvoorbeeld:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

U kunt vervolgens andere Power shell-opdrachten uitvoeren om met dit cluster te werken. U kunt bijvoorbeeld [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) uitvoeren om een lijst met knoop punten op dit beveiligde cluster weer te geven.


Als u het cluster wilt verwijderen, maakt u verbinding met het knoop punt op het cluster waar u het Service Fabric-pakket hebt gedownload, opent u een opdracht regel en gaat u naar de map pakket. Voer nu de volgende opdracht uit:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Onjuiste certificaat configuratie kan verhinderen dat het cluster tijdens de implementatie komt. Als u beveiligings problemen zelf wilt vaststellen, kijkt u in de logboeken groep **toepassingen en-services**  >  **micro soft-service Fabric**.
> 
> 
