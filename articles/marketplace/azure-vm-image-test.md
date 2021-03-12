---
title: Een installatie kopie van een virtuele Azure-machine voor Azure Marketplace testen
description: Meer informatie over het testen en verzenden van een Azure virtual machine-aanbieding in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: github-2407
ms.author: krsh
ms.date: 02/19/2020
ms.openlocfilehash: 48e41e870676bf6939ccaf5268c462f885e67572
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630177"
---
# <a name="test-a-virtual-machine-image"></a>Een installatie kopie van een virtuele machine testen

Dit onderwerp bevat de stappen voor het testen van een installatie kopie van een virtuele machine (VM) voor implementatie op Azure Marketplace.

## <a name="deploy-an-azure-vm"></a>Een Azure-VM implementeren

Een virtuele machine implementeren vanuit de afbeelding van de galerie met gedeelde afbeeldingen:

1. Ga naar de afbeeldings versie van de galerie met gedeelde afbeeldingen
1. Klik op VM maken
1. Geef een naam op voor de virtuele machine en selecteer een VM-grootte
1. Klik op beoordeling + maken. Nadat de validatie is voltooid, klikt u op maken

> [!NOTE]
> Als u een virtuele machine uit een VHD-bestand moet maken, volgt u de instructies in de volgende artikelen, [bereidt u een Azure Resource Manager sjabloon](https://docs.microsoft.com/azure/marketplace/azure-vm-image-test#prepare-an-azure-resource-manager-template) voor of [implementeert u een Azure-VM met behulp van Power shell](https://docs.microsoft.com/azure/marketplace/azure-vm-image-test#deploy-an-azure-vm-using-powershell).

In dit artikel wordt beschreven hoe u een installatie kopie van een virtuele machine (VM) in de commerciële Marketplace kunt testen en verzenden om te controleren of deze voldoet aan de meest recente publicatie vereisten voor Azure Marketplace.

Voer de volgende stappen uit voordat u uw VM-aanbieding verzendt:

- Implementeer een Azure-VM met behulp van uw gegeneraliseerde installatie kopie. Zie [een virtuele machine maken met behulp van een goedgekeurde basis](azure-vm-create-using-approved-base.md) of [Maak een virtuele machine met behulp van uw eigen installatie kopie](azure-vm-create-using-own-image.md).
- Voer validaties uit.

## <a name="run-validations"></a>Validaties uitvoeren

Er zijn twee manieren om validaties uit te voeren op de geïmplementeerde installatie kopie.

### <a name="use-certification-test-tool-for-azure-certified"></a>Certificerings test hulpprogramma gebruiken voor Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Het hulp programma voor certificerings test downloaden en uitvoeren

Het certificerings test hulpprogramma voor Azure Certified wordt uitgevoerd op een lokale Windows-computer, maar test een op Azure gebaseerde Windows-of Linux-VM. Het verklaart dat uw VM-installatie kopie van uw gebruiker kan worden gebruikt met Microsoft Azure en dat aan de richt lijnen en vereisten rond het voorbereiden van uw VHD is voldaan.

1. Down load en installeer het meest recente [certificerings test programma voor Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Open het certificerings programma en selecteer vervolgens **nieuwe test starten**.
3. Voer in het scherm test informatie een **test naam** in voor de test uitvoering.
4. Selecteer het platform voor uw virtuele machine, ofwel **Windows Server** of **Linux**. De platform keuze is van invloed op de resterende opties.
5. Als uw virtuele machine gebruikmaakt van deze database service, schakelt u het selectie vakje **testen voor Azure SQL database** in.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Het certificerings hulpprogramma verbinden met een VM-installatie kopie

1. Selecteer de SSH-verificatie modus: wachtwoord verificatie of sleutel bestands verificatie.
2. Als u verificatie op basis van wacht woorden gebruikt, voert u waarden in voor de **DNS-naam van de virtuele machine**, de **gebruikers naam** en het **wacht woord**. U kunt ook het standaard SSH-poort nummer wijzigen.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Toont de selectie van VM-test gegevens.":::

3. Als u gebruikmaakt van verificatie op basis van een sleutel bestand, voert u waarden in voor de DNS-naam van de virtuele machine, de gebruikers naam en de locatie van de persoonlijke sleutel. U kunt ook een wachtwoordzin toevoegen of het standaard-SSH-poort nummer wijzigen.
4. Voer de volledig gekwalificeerde VM-DNS-naam in (bijvoorbeeld MyVMName.Cloudapp.net).
5. Voer de **gebruikers naam** en het **wacht woord** in.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Toont de selectie van de gebruikers naam en het wacht woord van de VM.":::

6. Selecteer **Next**.

#### <a name="run-a-certification-test"></a>Een certificerings test uitvoeren

Nadat u de parameter waarden voor uw VM-installatie kopie in het certificerings programma hebt opgegeven, selecteert u verbinding testen om een geldige verbinding met uw virtuele machine te maken. Nadat een verbinding is geverifieerd, selecteert u **volgende** om de test te starten. Wanneer de test is voltooid, worden de resultaten weer gegeven in een tabel. In de kolom Status wordt voor elke test weer gegeven (pass/fail/Warning). Als een van de tests mislukt, is uw installatie kopie niet gecertificeerd. In dit geval raadpleegt u de vereisten en fout berichten, brengt u de voorgestelde wijzigingen aan en voert u de test opnieuw uit.

Nadat de geautomatiseerde test is voltooid, geeft u aanvullende informatie over de VM-installatie kopie op de twee tabbladen van het vragenlijst scherm, algemene evaluatie en aanpassing van de kernel en selecteert u volgende.

In het laatste scherm kunt u meer informatie opgeven, zoals SSH-toegangs gegevens voor een Linux VM-installatie kopie, en een uitleg voor eventuele mislukte beoordelingen als u op zoek bent naar uitzonde ringen.

Selecteer ten slotte rapport genereren om de test resultaten en logboek bestanden voor de uitgevoerde test cases samen met uw antwoorden op de vragen lijst te downloaden.
> [!Note]
> Enkele uitgevers hebben scenario's waarbij Vm's moeten worden vergrendeld omdat ze software hebben, zoals firewalls die zijn geïnstalleerd op de virtuele machine. In dit geval kunt u het [gecertificeerde test programma](https://aka.ms/AzureCertificationTestTool) hier downloaden en het rapport verzenden bij Partner Center- [ondersteuning](https://aka.ms/marketplacepublishersupport).

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Power shell gebruiken voor het gebruik van de Self-Test-API

### <a name="on-linux-os"></a>Op Linux-besturings systeem

De API aanroepen in Power shell:

1. Gebruik de Invoke-WebRequest opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code voorbeeld en scherm opname.
3. Geef de hoofd tekst parameters op in JSON-indeling.

In dit volgende voor beeld ziet u een Power shell-aanroep voor de API:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Hier volgt een voor beeld van het aanroepen van de API in Power shell:

[![Scherm voorbeeld voor het aanroepen van de API in Power shell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In dit voorbeeld scherm, dat toont `$res.Content` , worden de details van de test resultaten weer gegeven in JSON-indeling:

[![Scherm voorbeeld voor het aanroepen van de API in Power shell met details van test resultaten.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Hier volgt een voor beeld van resultaten van JSON-testen die worden weer gegeven in een online JSON-Viewer (zoals [code beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Meer test resultaten in een online JSON-viewer.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Op Windows-besturings systeem

De API aanroepen in Power shell:

1. Gebruik de Invoke-WebRequest opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code voorbeeld en voor beeld scherm.
3. Maak de hoofd tekst-para meters in JSON-indeling.

Dit code voorbeeld toont een Power shell-aanroep van de API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Deze voorbeeld schermen tonen voor beeld voor het aanroepen van de API in Power shell:

**Met SSH-sleutel**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Het aanroepen van de API in Power shell met een SSH-sleutel.":::

**Met wacht woord**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Het aanroepen van de API in Power shell met een wacht woord.":::

<br>In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In dit scherm ziet `$res.Content` u de details van de test resultaten in JSON-indeling:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Details van test resultaten in JSON-indeling.":::

<br>Hier volgt een voor beeld van test resultaten die worden weer gegeven in een online JSON-Viewer (zoals [code beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Test resultaten in een online JSON-viewer.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>KRUL gebruiken om de Self-Test-API in Linux-besturings systeem te verbruiken

In dit voor beeld wordt krul gebruikt om een POST-API-aanroep naar Azure Active Directory en de Self-Host virtuele machine te maken.

1. Een Azure AD-token aanvragen om te verifiëren bij een self-host-VM

   Zorg ervoor dat de juiste waarden worden vervangen in het krul-verzoek.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Hier volgt een voor beeld van het antwoord van de aanvraag:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Een aanvraag indienen voor de zelf test-VM

   Zorg ervoor dat het Bearer-token en de-para meters worden vervangen door de juiste waarden.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Voorbeeld reactie van de API-aanroep zelf test-VM

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Volgende stappen

- Meld u aan bij [Partner Center](https://partner.microsoft.com/).
