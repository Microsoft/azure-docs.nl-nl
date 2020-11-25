---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 946ff043828034340ae3273fc0629e32de755540
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025642"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Een zelfondertekend basis certificaat maken

Gebruik de cmdlet New-SelfSignedCertificate om een zelfondertekend basis certificaat te maken. Zie [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)voor meer informatie over para meters.

1. Open op een computer met Windows 10 of Windows Server 2016 een Windows Power shell-console met verhoogde bevoegdheden. Deze voor beelden werken niet in de Azure Cloud Shell ' Probeer het '. U moet deze voor beelden lokaal uitvoeren.
1. Gebruik het volgende voor beeld om het zelfondertekende basis certificaat te maken. In het volgende voor beeld wordt een zelfondertekend basis certificaat gemaakt met de naam ' P2SRootCert ', dat automatisch wordt geïnstalleerd in ' certificaten-huidige Gebruiker\persoonlijk\certificaten '. U kunt het certificaat weer geven door *certmgr. msc* te openen of *gebruikers certificaten te beheren*.

   Meld u aan met de `Connect-AzAccount` cmdlet. Voer vervolgens het volgende voor beeld uit met alle benodigde wijzigingen.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

1. Verlaat de Power shell-console en ga door met de volgende stappen voor het genereren van client certificaten.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Een clientcertificaat genereren

Op elke clientcomputer die via punt-naar-site verbinding maakt met een VNet, moet een clientcertificaat zijn geïnstalleerd. U genereert een client certificaat op basis van het zelfondertekende basis certificaat en exporteert en installeert vervolgens het client certificaat. Als het client certificaat niet is geïnstalleerd, mislukt de verificatie. 

De volgende stappen helpen u bij het genereren van een client certificaat van een zelfondertekend basis certificaat. U kunt meerdere client certificaten genereren uit hetzelfde basis certificaat. Wanneer u client certificaten genereert met behulp van de onderstaande stappen, wordt het client certificaat automatisch geïnstalleerd op de computer die u hebt gebruikt voor het genereren van het certificaat. Als u een client certificaat op een andere client computer wilt installeren, kunt u het certificaat exporteren.

In de voor beelden wordt de New-SelfSignedCertificate-cmdlet gebruikt om een client certificaat te genereren dat binnen een jaar verloopt. Zie [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)voor meer informatie over para meters, zoals het instellen van een andere verloop waarde voor het client certificaat.

### <a name="example-1---powershell-console-session-still-open"></a>Voor beeld 1: Power shell-console sessie is nog geopend

Gebruik dit voor beeld als u de Power shell-console niet hebt gesloten nadat u het zelfondertekende basis certificaat hebt gemaakt. In dit voor beeld wordt de vorige sectie voortgezet en wordt de gedeclareerde variabele ' $cert ' gebruikt. Als u de Power shell-console hebt gesloten nadat u het zelfondertekende basis certificaat hebt gemaakt of als u aanvullende client certificaten in een nieuwe Power shell-console sessie maakt, gebruikt u de stappen in [voor beeld 2](#ex2).

Wijzig en voer het voor beeld uit om een client certificaat te genereren. Als u het volgende voor beeld uitvoert zonder het te wijzigen, is het resultaat een client certificaat met de naam ' P2SChildCert '.  Als u het onderliggende certificaat iets anders wilt noemen, wijzigt u de CN-waarde. Wijzig de TextExtension niet wanneer u dit voor beeld uitvoert. Het client certificaat dat u genereert, wordt automatisch geïnstalleerd in ' certificaten-huidige Gebruiker\persoonlijk\certificaten ' op uw computer.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Voor beeld 2: nieuwe Power shell-console sessie

Als u aanvullende client certificaten maakt of de Power shell-sessie die u hebt gebruikt voor het maken van een zelfondertekend basis certificaat, gebruikt u de volgende stappen:

1. Identificeer het zelfondertekende basis certificaat dat op de computer is geïnstalleerd. Deze cmdlet retourneert een lijst met certificaten die op uw computer zijn geïnstalleerd.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```

1. Zoek de onderwerpnaam in de lijst die wordt weer gegeven en kopieer de vinger afdruk die zich ernaast bevindt, naar een tekst bestand. In het volgende voor beeld zijn er twee certificaten. De CN-naam is de naam van het zelfondertekende basis certificaat van waaruit u een onderliggend certificaat wilt genereren. In dit geval ' P2SRootCert '.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```

1. Declareer een variabele voor het basis certificaat met behulp van de vinger afdruk uit de vorige stap. Vervang de vinger afdruk door de vinger afdruk van het basis certificaat van waaruit u een onderliggend certificaat wilt genereren.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Als u bijvoorbeeld de vinger afdruk voor P2SRootCert in de vorige stap gebruikt, ziet de variabele er als volgt uit:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```

1. Wijzig en voer het voor beeld uit om een client certificaat te genereren. Als u het volgende voor beeld uitvoert zonder het te wijzigen, is het resultaat een client certificaat met de naam ' P2SChildCert '. Als u het onderliggende certificaat iets anders wilt noemen, wijzigt u de CN-waarde. Wijzig de TextExtension niet wanneer u dit voor beeld uitvoert. Het client certificaat dat u genereert, wordt automatisch geïnstalleerd in ' certificaten-huidige Gebruiker\persoonlijk\certificaten ' op uw computer.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>De open bare sleutel van het basis certificaat exporteren (. CER)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Het zelfondertekende basis certificaat en de persoonlijke sleutel exporteren om deze op te slaan (optioneel)

Mogelijk wilt u het zelfondertekende basis certificaat exporteren en veilig opslaan als back-up. Als dat het geval is, kunt u dit later op een andere computer installeren en meer client certificaten genereren. Als u het zelfondertekende basis certificaat wilt exporteren als. pfx, selecteert u het basis certificaat en gebruikt u dezelfde stappen zoals beschreven in [een client certificaat exporteren](#clientexport).

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Het clientcertificaat exporteren

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]