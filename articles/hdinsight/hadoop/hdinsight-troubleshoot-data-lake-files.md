---
title: Geen toegang tot Data Lake-opslag bestanden in azure HDInsight
description: Geen toegang tot Data Lake-opslag bestanden in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.openlocfilehash: f4c5a23b604334952730fcc4cf1fcb3fcbed6237
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944401"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Geen toegang tot Data Lake-opslag bestanden in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue-acl-verification-failed"></a>Probleem: ACL-verificatie is mislukt

Er wordt een fout bericht met de volgende strekking weer gegeven:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Oorzaak

De gebruiker heeft mogelijk machtigingen voor de Service-Principal (SP) ingetrokken voor bestanden/mappen.

### <a name="resolution"></a>Oplossing

1. Controleer of de SP ' x ' machtigingen heeft om langs het pad te bladeren. Zie [machtigingen](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html)voor meer informatie. Voorbeeld `dfs` opdracht voor het controleren van de toegang tot bestanden/mappen in data Lake Storage-account:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Stel de vereiste machtigingen in voor toegang tot het pad op basis van de lees-en schrijf bewerking die wordt uitgevoerd. Hier vindt u de machtigingen die zijn vereist voor verschillende bestandssysteem bewerkingen.

---

## <a name="issue-service-principal-certificate-expiry"></a>Probleem: verval datum van Service-Principal-certificaat

Er wordt een fout bericht met de volgende strekking weer gegeven:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Oorzaak

Het certificaat dat is gegeven voor toegang tot de Service-Principal is mogelijk verlopen.

1. SSH in hoofd knooppunt. Controleer de toegang tot het opslag account met behulp van de volgende `dfs` opdracht:

    ```
    hdfs dfs -ls /
    ```

1. Controleer of het fout bericht overeenkomt met de volgende uitvoer:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Haal een van de url's van op `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls` .

1. Voer de volgende krul opdracht uit om het OAuth-token op te halen.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. De uitvoer voor een geldige Service-Principal moet er ongeveer als volgt uitzien:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Als het certificaat van de Service-Principal is verlopen, ziet de uitvoer er ongeveer als volgt uit:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Andere Azure Active Directory gerelateerde fouten/aan het certificaat gerelateerde fouten kunnen worden herkend door de gateway-URL te pingen om het OAuth-token op te halen.

1. Als er een fout optreedt bij het openen van ADLS uit het HDI-cluster. Controleer of het certificaat is verlopen door de hierboven genoemde stappen te volgen.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Oplossing

Maak een nieuw certificaat of wijs een bestaand certificaat toe met behulp van het volgende Power shell-script:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Als u een bestaand certificaat wilt toewijzen, moet u een certificaat maken, het pfx-bestand en het wacht woord gereed hebben. Koppel het certificaat aan de service-principal waarmee het cluster is gemaakt, met behulp van de AppId Ready.

Voer de Power shell-opdracht uit nadat u de para meters hebt vervangen door de werkelijke waarden.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]