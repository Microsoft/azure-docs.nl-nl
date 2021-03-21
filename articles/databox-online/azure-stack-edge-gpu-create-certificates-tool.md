---
title: Certificaten maken met het hulp programma voor het uitvoeren van Microsoft Azure Stack hub-gereedheids controlepunt | Microsoft Docs
description: Hierin wordt beschreven hoe u certificaat aanvragen maakt en vervolgens certificaten op uw Azure Stack Edge Pro GPU-apparaat ontvangt en installeert met behulp van het hulp programma Azure Stack hub Readiness Checker.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: a04243093b89b6a2498efc48f80cbd7a47d57337
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437720"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Certificaten maken voor uw Azure Stack Edge Pro met behulp van Azure Stack hub-gereedheids controleprogramma 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u certificaten voor uw Azure Stack Edge Pro maakt met behulp van de Azure Stack hub Readiness Checker. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack hub-hulp programma voor de gereedheids controle gebruiken

Gebruik het hulp programma voor de Azure Stack hub-gereedheids controle voor het maken van aanvragen voor certificaat ondertekening (Csr's) voor een Azure Stack Edge Pro-implementatie van apparaten. U kunt deze aanvragen maken nadat u een bestelling voor het Azure Stack Edge Pro-apparaat hebt geplaatst en wacht totdat het apparaat is aangekomen.

> [!NOTE]
> Gebruik dit hulp programma alleen voor test-of ontwikkelings doeleinden en niet voor productie apparaten. 

U kunt het hulp programma Azure Stack hub Readiness Checker (AzsReadinessChecker) gebruiken om de volgende certificaten aan te vragen:

- Azure Resource Manager certificaat
- Lokaal gebruikers interface certificaat
- Knooppunt certificaat
- BLOB-certificaat
- VPN-certificaat


## <a name="prerequisites"></a>Vereisten

Als u Csr's wilt maken voor de implementatie van Azure Stack Edge Pro-apparaat, moet u het volgende doen: 

- U hebt een-client met Windows 10 of Windows Server 2016 of hoger. 
- U hebt het hulp programma Microsoft Azure Stack hub-gereedheids controlepunt gedownload [van de PowerShell Gallery](https://aka.ms/AzsReadinessChecker) op dit systeem.
- U hebt de volgende informatie voor de certificaten:
  - Apparaatnaam
  - Serie nummer van knoop punt
  - Externe Fully Qualified Domain Name (FQDN)

## <a name="generate-certificate-signing-requests"></a>Aanvragen voor certificaat ondertekening genereren

Volg deze stappen om de Azure Stack Edge Pro-apparaten voor te bereiden:

1. Voer Power shell uit als Administrator (5,1 of hoger).
2. Installeer het hulp programma voor de Azure Stack hub-gereedheids controle. Typ het volgende bij de Power shell-prompt: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Als u de geïnstalleerde versie wilt ophalen, typt u:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Maak een map voor alle certificaten als u deze nog niet hebt. Type: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Als u een certificaat aanvraag wilt maken, geeft u de volgende informatie op. Als u een VPN-certificaat genereert, zijn sommige van deze invoer niet van toepassing.
    
    |Invoer |Beschrijving  |
    |---------|---------|
    |`OutputRequestPath`|Het bestandspad op de lokale client waar u de certificaat aanvragen wilt maken.        |
    |`DeviceName`|De naam van uw apparaat op de pagina **apparaten** in de lokale webgebruikersinterface van uw apparaat. <br> Dit veld is niet vereist voor een VPN-certificaat.         |
    |`NodeSerialNumber`|Het serie nummer van het knoop punt van het apparaat op de pagina **netwerk** in de lokale webgebruikersinterface van uw apparaat. <br> Dit veld is niet vereist voor een VPN-certificaat.       |
    |`ExternalFQDN`|De DNSDomain-waarde op de pagina **apparaten** in de lokale webgebruikersinterface van uw apparaat.         |
    |`RequestType`|Het aanvraag type kan voor `MultipleCSR` verschillende certificaten voor de verschillende eind punten of `SingleCSR` één certificaat voor alle eind punten zijn. <br> Dit veld is niet vereist voor een VPN-certificaat.     |

    Voor alle certificaten, met uitzonde ring van het VPN-certificaat, typt u: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Als u een VPN-certificaat maakt, typt u: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. U vindt de certificaat aanvraag bestanden in de map die u in de bovenstaande OutputRequestPath-para meter hebt opgegeven. Wanneer u de `MultipleCSR` para meter gebruikt, ziet u de volgende vier bestanden met de `.req` extensie:

    
    |Bestandsnamen  |Type certificaat aanvraag  |
    |---------|---------|
    |Beginnen met uw `DeviceName`     |Lokale web UI-certificaat aanvraag      |
    |Beginnen met uw `NodeSerialNumber`     |Knooppunt certificaat aanvraag         |
    |Beginnen met `login`     |Certificaat aanvraag van Azure Resource Manager-eind punt       |
    |Beginnen met `wildcard`     |Certificaat aanvraag van Blob-opslag. Het bevat een Joker teken omdat deze alle opslag accounts omvat die u op het apparaat kunt maken.          |
    |Beginnen met `AzureStackEdgeVPNCertificate`     |Aanvraag voor VPN-client certificaat.         |

    U ziet ook een map INF. Dit bevat een beheer. <Edge-apparaatnaam> informatie bestand in Lees bare tekst met uitleg over de details van het certificaat.  


6. Deze bestanden verzenden naar uw certificerings instantie (intern of openbaar). Zorg ervoor dat uw CA certificaten genereert met behulp van de gegenereerde aanvraag die voldoet aan de Azure Stack Edge Pro-certificaat vereisten voor [knooppunt certificaten](azure-stack-edge-gpu-manage-certificates.md#node-certificates), [eindpunt certificaten](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)en [lokale UI-certificaten](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates).

## <a name="prepare-certificates-for-deployment"></a>Certificaten voorbereiden voor implementatie

De certificaat bestanden die u van uw certificerings instantie (CA) ontvangt, moeten worden geïmporteerd en geëxporteerd met eigenschappen die overeenkomen met de certificaat vereisten van het Azure Stack Edge Pro-apparaat. Voer de volgende stappen uit op hetzelfde systeem waarop u de aanvragen voor certificaat ondertekening hebt gegenereerd.

- Volg de stappen in [certificaten importeren op de clients die toegang hebben tot uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)om de certificaten te importeren.

- Als u de certificaten wilt exporteren, volgt u de stappen in [certificaten exporteren van de client die toegang hebben tot het Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Certificaten valideren

Eerst genereert u een juiste mappen structuur en plaatst u de certificaten in de bijbehorende mappen. Alleen vervolgens valideert u de certificaten met het hulp programma.

1. Voer Power shell uit als Administrator.

2. Als u de juiste mapstructuur wilt genereren, typt u bij de prompt type:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Converteer het PFX-wacht woord naar een veilige teken reeks. Type:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Valideer vervolgens de certificaten. Type:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Volgende stappen

[Uw Azure Stack Edge Pro-apparaat implementeren](azure-stack-edge-gpu-deploy-prep.md)
