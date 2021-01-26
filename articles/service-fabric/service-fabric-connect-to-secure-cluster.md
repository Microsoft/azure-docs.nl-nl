---
title: Veilig verbinding maken met een Azure Service Fabric-cluster
description: Hierin wordt beschreven hoe u client toegang tot een Service Fabric cluster verifieert en hoe u communicatie tussen clients en een cluster beveiligt.
ms.topic: conceptual
ms.date: 01/29/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a669f7400eb2daf2d71f6caed41560020699c58
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791118"
---
# <a name="connect-to-a-secure-cluster"></a>Verbinding maken met een beveiligd cluster

Wanneer een client verbinding maakt met een Service Fabric cluster knooppunt, kan de client geverifieerde en beveiligde communicatie tot stand worden gebracht met behulp van certificaat beveiliging of Azure Active Directory (AAD). Deze verificatie zorgt ervoor dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en geïmplementeerde toepassingen en beheer taken kunnen uitvoeren.  Certificaten of AAD-beveiliging moet eerder zijn ingeschakeld op het cluster toen het cluster werd gemaakt.  Zie [cluster beveiliging](service-fabric-cluster-security.md)voor meer informatie over scenario's voor het beveiligen van het cluster. Als u verbinding maakt met een cluster dat is beveiligd met certificaten, [stelt u het client certificaat](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) in op de computer die verbinding maakt met het cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Verbinding maken met een beveiligd cluster met behulp van Azure Service Fabric CLI (sfctl)

Er zijn een aantal verschillende manieren om verbinding te maken met een beveiligd cluster met behulp van de Service Fabric CLI (sfctl). Als u ter verificatie een clientcertificaat gebruikt, moeten de certificaatgegevens overeenkomen met een certificaat dat is geïmplementeerd in de clusterknooppunten. Als uw certificaat certificerings instanties (Ca's) heeft, moet u ook de vertrouwde certificerings instanties opgeven.

U kunt verbinding maken met een cluster met behulp van de `sfctl cluster select` opdracht.

Client certificaten kunnen worden opgegeven op twee verschillende manieren, hetzij als een certificaat en sleutel paar, hetzij als één PFX-bestand. U wordt gevraagd om het wacht woord op te geven voor met wacht woord beveiligde PEM-bestanden. Als u het client certificaat als een PFX-bestand hebt verkregen, moet u het PFX-bestand eerst converteren naar een PEM-bestand met behulp van de volgende opdracht. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Als uw pfx-bestand niet is beveiligd met een wacht woord, gebruikt u-Passin Pass: voor de laatste para meter.

Geef het bestandspad op in het argument om het client certificaat op te geven als een PEM-bestand `--pem` . Bijvoorbeeld:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Met een wacht woord beveiligde PEM-bestanden wordt om een wacht woord gevraagd voordat een opdracht wordt uitgevoerd.

Als u een certificaat wilt opgeven, gebruikt u `--cert` de `--key` argumenten en en geeft u de bestands paden naar elk bestand op.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Soms certificaten die worden gebruikt voor het beveiligen van test-of dev-clusters, mislukken certificaat validatie. Als u certificaat verificatie wilt overs Laan, geeft u de `--no-verify` optie op. Bijvoorbeeld:

> [!WARNING]
> Gebruik de optie niet `no-verify` Wanneer u verbinding maakt met productie service Fabric clusters.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Daarnaast kunt u paden opgeven naar mappen van vertrouwde CA-certificaten of afzonderlijke certificaten. Als u deze paden wilt opgeven, gebruikt u het `--ca` argument. Bijvoorbeeld:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Nadat u verbinding hebt gemaakt, moet u [andere sfctl-opdrachten kunnen uitvoeren](service-fabric-cli.md) om met het cluster te communiceren.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Verbinding maken met een cluster met behulp van Power shell
Voordat u bewerkingen uitvoert op een cluster via Power shell, moet u eerst een verbinding met het cluster tot stand brengen. De cluster verbinding wordt gebruikt voor alle volgende opdrachten in de opgegeven Power shell-sessie.

### <a name="connect-to-an-unsecure-cluster"></a>Verbinding maken met een niet-beveiligd cluster

Als u verbinding wilt maken met een niet-beveiligd cluster, geeft u het cluster eindpunt adres op de opdracht **Connect-ServiceFabricCluster** :

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Verbinding maken met een beveiligd cluster met behulp van Azure Active Directory

Als u verbinding wilt maken met een beveiligd cluster dat Azure Active Directory gebruikt voor het autoriseren van de toegang van cluster beheerders, geeft u de vinger afdruk van het cluster certificaat op en gebruikt u de vlag *AzureActiveDirectory* .  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Verbinding maken met een beveiligd cluster met behulp van een client certificaat
Voer de volgende Power shell-opdracht uit om verbinding te maken met een beveiligd cluster dat client certificaten gebruikt om beheerders toegang te autoriseren. 

#### <a name="connect-using-certificate-common-name"></a>Verbinding maken met algemene naam van certificaat
Geef de algemene naam van het cluster certificaat en de algemene naam van het client certificaat op dat machtigingen voor cluster beheer heeft gekregen. De certificaat details moeten overeenkomen met een certificaat op de cluster knooppunten.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* is de algemene naam van het server certificaat dat op de cluster knooppunten is geïnstalleerd. *FindValue* is de algemene naam van het client certificaat voor de beheerder. Wanneer de para meters zijn ingevuld, ziet de opdracht eruit als in het volgende voor beeld:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Verbinding maken via de vinger afdruk van het certificaat
Geef de vinger afdruk van het cluster certificaat en de vinger afdruk van het client certificaat op dat is gemachtigd voor cluster beheer. De certificaat details moeten overeenkomen met een certificaat op de cluster knooppunten.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* is de vinger afdruk van het server certificaat dat is geïnstalleerd op de cluster knooppunten. *FindValue* is de vinger afdruk van het client certificaat voor de beheerder.  Wanneer de para meters zijn ingevuld, ziet de opdracht eruit als in het volgende voor beeld:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Verbinding maken met een beveiligd cluster met behulp van Windows Active Directory
Als uw zelfstandige cluster wordt geïmplementeerd met behulp van AD-beveiliging, maakt u verbinding met het cluster door de switch ' WindowsCredential ' toe te voegen.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Verbinding maken met een cluster met behulp van de FabricClient-Api's
De Service Fabric-SDK biedt de [FabricClient](/dotnet/api/system.fabric.fabricclient) -klasse voor cluster beheer. Als u de FabricClient-Api's wilt gebruiken, haalt u het micro soft. ServiceFabric NuGet-pakket op.

### <a name="connect-to-an-unsecure-cluster"></a>Verbinding maken met een niet-beveiligd cluster

Als u verbinding wilt maken met een extern niet-beveiligd cluster, maakt u een FabricClient-exemplaar en geeft u het cluster adres op:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Voor code die wordt uitgevoerd binnen een cluster, bijvoorbeeld in een betrouw bare service, maakt u een FabricClient *zonder* het cluster adres op te geven. FabricClient maakt verbinding met de lokale beheer gateway op het knoop punt waarop de code momenteel wordt uitgevoerd, waardoor een extra netwerk-hop wordt voor komen.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Verbinding maken met een beveiligd cluster met behulp van een client certificaat

De knoop punten in het cluster moeten geldige certificaten hebben waarvan de algemene naam of DNS-naam in SAN wordt weer gegeven in de [eigenschap RemoteCommonNames](/dotnet/api/system.fabric.x509credentials) die is ingesteld op [FabricClient](/dotnet/api/system.fabric.fabricclient). Als u dit proces volgt, wordt wederzijdse verificatie tussen de client en de cluster knooppunten ingeschakeld.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Interactief verbinding maken met een beveiligd cluster met behulp van Azure Active Directory

In het volgende voor beeld wordt Azure Active Directory gebruikt voor client identiteit en server certificaat voor de identiteit van de server.

Er wordt automatisch een dialoog venster weer gegeven voor interactief aanmelden bij het maken van verbinding met het cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Niet-interactief verbinding maken met een beveiligd cluster met behulp van Azure Active Directory

In het volgende voor beeld wordt gebruikgemaakt van micro soft. Identity model. clients. ActiveDirectory, versie: 2.19.208020213.

Zie [micro soft. Identity model. clients. ActiveDirectory](/dotnet/api/microsoft.identitymodel.clients.activedirectory)voor meer informatie over het verkrijgen van Aad-tokens.

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Verbinding maken met een beveiligd cluster zonder voorafgaande meta gegevens kennis met behulp van Azure Active Directory

In het volgende voor beeld wordt de niet-interactieve token aanschaf gebruikt, maar dezelfde benadering kan worden gebruikt voor het bouwen van een aangepaste interactieve token Acquisition-ervaring. De Azure Active Directory meta gegevens die nodig zijn voor het ophalen van tokens, worden gelezen uit de cluster configuratie.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Verbinding maken met een beveiligd cluster met behulp van Service Fabric Explorer
Als u [service Fabric Explorer](service-fabric-visualizing-your-cluster.md) voor een bepaald cluster wilt bereiken, wijst u uw browser aan:

`http://<your-cluster-endpoint>:19080/Explorer`

De volledige URL is ook beschikbaar in het deel venster cluster Essentials van de Azure Portal.

Als u verbinding wilt maken met een beveiligd cluster op Windows of OS X met een browser, kunt u het client certificaat importeren. in de browser wordt u gevraagd het certificaat te gebruiken om verbinding te maken met het cluster.  Op Linux-machines moet het certificaat worden geïmporteerd met geavanceerde browser instellingen (elke browser heeft verschillende mechanismen) en dit naar de certificaat locatie op schijf verwijzen. Lees [een client certificaat instellen](#connectsecureclustersetupclientcert) voor meer informatie.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Verbinding maken met een beveiligd cluster met behulp van Azure Active Directory

Als u verbinding wilt maken met een cluster dat is beveiligd met AAD, wijst u uw browser aan:

`https://<your-cluster-endpoint>:19080/Explorer`

U wordt automatisch gevraagd om u aan te melden met AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Verbinding maken met een beveiligd cluster met behulp van een client certificaat

Als u verbinding wilt maken met een cluster dat is beveiligd met certificaten, wijst u uw browser aan:

`https://<your-cluster-endpoint>:19080/Explorer`

U wordt automatisch gevraagd om een client certificaat te selecteren.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Een client certificaat instellen op de externe computer

Ten minste twee certificaten moeten worden gebruikt voor het beveiligen van het cluster, een voor het cluster en het server certificaat en een andere voor client toegang.  U wordt aangeraden ook extra secundaire certificaten en certificaten voor client toegang te gebruiken.  Als u de communicatie tussen een client en een cluster knooppunt wilt beveiligen met behulp van certificaat beveiliging, moet u eerst het client certificaat verkrijgen en installeren. Het certificaat kan worden geïnstalleerd in het persoonlijke archief (mijn opslag) van de lokale computer of de huidige gebruiker.  U hebt ook de vinger afdruk van het server certificaat nodig, zodat de client het cluster kan verifiëren.

* Voor Windows: dubbelklik op het PFX-bestand en volg de aanwijzingen om het certificaat in uw persoonlijke archief, `Certificates - Current User\Personal\Certificates`, te installeren. U kunt ook de Power shell-opdracht gebruiken:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Als het een zelfondertekend certificaat is, moet u het importeren in het archief Vertrouwde personen van uw computer voordat u dit certificaat kunt gebruiken om verbinding te maken met een beveiligd cluster.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Voor Mac: dubbelklik op het PFX-bestand en volg de aanwijzingen om het certificaat in uw Sleutelhanger te installeren.

## <a name="next-steps"></a>Volgende stappen

* [Upgrade proces en verwachtingen van de Cluster Service Fabric](service-fabric-cluster-upgrade.md)
* [Uw Service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Inleiding van Service Fabric status model](service-fabric-health-introduction.md)
* [Toepassings beveiliging en runas](service-fabric-application-runas-security.md)
* [Aan de slag met de Service Fabric-CLI](service-fabric-cli.md)
