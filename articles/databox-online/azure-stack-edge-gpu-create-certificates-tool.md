---
title: Certificaten maken met behulp Microsoft Azure hulpprogramma Voor gereedheidscontrole van Stack Hub | Microsoft Docs
description: Beschrijft hoe u certificaataanvragen maakt en vervolgens certificaten op uw GPU-apparaat Azure Stack Edge Pro installeren met behulp van Azure Stack Hub gereedheidscontroleprogramma.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 8316dd0abfa437d4bf88e8268dfe034344c6614c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389329"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Certificaten maken voor uw Azure Stack Edge Pro met Azure Stack Hub Gereedheidscontroleprogramma 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u certificaten voor uw Azure Stack Edge Pro met behulp Azure Stack Hub gereedheidscontroleprogramma. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Het Azure Stack Hub gereedheidscontrole gebruiken

Gebruik het hulpprogramma Azure Stack Hub gereedheidscontrole om aanvragen voor certificaat ondertekening (CSP's) te maken voor een Azure Stack Edge Pro-apparaatimplementatie. U kunt deze aanvragen maken nadat u een bestelling voor het Azure Stack Edge Pro en wacht tot het apparaat is ontvangen.

> [!NOTE]
> Gebruik dit hulpprogramma alleen voor test- of ontwikkelingsdoeleinden en niet voor productieapparaten. 

U kunt het hulpprogramma Azure Stack Hub Gereedheidscontrole (AzsReadinessChecker) gebruiken om de volgende certificaten aan te vragen:

- Azure Resource Manager certificaat
- Lokaal UI-certificaat
- Knooppuntcertificaat
- Blobcertificaat
- VPN-certificaat


## <a name="prerequisites"></a>Vereisten

Als u CSP's wilt maken voor Azure Stack Edge Pro implementatie van een apparaat, moet u ervoor zorgen dat: 

- U hebt een client met Windows 10 of Windows Server 2016 of hoger. 
- U hebt het hulpprogramma Microsoft Azure Stack Hub Readiness Checker gedownload van [de PowerShell Gallery](https://aka.ms/AzsReadinessChecker) op dit systeem.
- U hebt de volgende informatie voor de certificaten:
  - Apparaatnaam
  - Serienummer van knooppunt
  - Externe FQDN (Fully Qualified Domain Name)

## <a name="generate-certificate-signing-requests"></a>Aanvragen voor certificaat-ondertekening genereren

Gebruik deze stappen om de Azure Stack Edge Pro voor te bereiden:

1. Voer PowerShell uit als beheerder (5.1 of hoger).
2. Installeer het hulpprogramma Azure Stack Hub gereedheidscontrole. Typ het volgende bij de PowerShell-prompt: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Typ het volgende om de geïnstalleerde versie op te halen:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Maak een map voor alle certificaten als u er nog geen hebt. Type: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Geef de volgende informatie op om een certificaataanvraag te maken. Als u een VPN-certificaat genereert, zijn sommige van deze invoer niet van toepassing.
    
    |Invoer |Beschrijving  |
    |---------|---------|
    |`OutputRequestPath`|Het bestandspad op de lokale client waar u de certificaataanvragen wilt maken.        |
    |`DeviceName`|De naam van uw apparaat op de **pagina Apparaten** in de lokale webinterface van uw apparaat. <br> Dit veld is niet vereist voor een VPN-certificaat.         |
    |`NodeSerialNumber`|Het serienummer van het apparaat-knooppunt op **de pagina Netwerk** in de lokale webinterface van uw apparaat. <br> Dit veld is niet vereist voor een VPN-certificaat.       |
    |`ExternalFQDN`|De waarde DNSDomain op de **pagina Apparaten** in de lokale webinterface van uw apparaat.         |
    |`RequestType`|Het aanvraagtype kan zijn voor - verschillende certificaten voor de verschillende eindpunten, of - één certificaat `MultipleCSR` `SingleCSR` voor alle eindpunten. <br> Dit veld is niet vereist voor een VPN-certificaat.     |

    Voor alle certificaten, met uitzondering van het VPN-certificaat, typt u: 
    
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

    
5. U vindt de certificaataanvraagbestanden in de map die u hebt opgegeven in de parameter OutputRequestPath hierboven. Wanneer u de `MultipleCSR` parameter gebruikt, ziet u de volgende vier bestanden met de `.req` extensie :

    
    |Bestandsnamen  |Type certificaataanvraag  |
    |---------|---------|
    |Beginnen met uw `DeviceName`     |Certificaataanvraag voor lokale webinterface      |
    |Beginnen met uw `NodeSerialNumber`     |Aanvraag voor knooppuntcertificaat         |
    |Beginnend met `login`     |Azure Resource Manager-eindpuntcertificaataanvraag       |
    |Beginnend met `wildcard`     |Blob Storage-certificaataanvraag. Het bevat een jokerteken omdat het alle opslagaccounts dekt die u op het apparaat kunt maken.          |
    |Beginnend met `AzureStackEdgeVPNCertificate`     |Aanvraag voor VPN-clientcertificaat.         |

    U ziet ook een INF-map. Dit bevat een management.<edge-devicename> informatiebestand in duidelijke tekst waarin de certificaatdetails worden uitgelegd.  


6. Verzend deze bestanden naar uw certificeringsinstantie (intern of openbaar). Zorg ervoor dat uw CA certificaten genereert met behulp van uw gegenereerde aanvraag, [](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)die voldoen aan de Azure Stack Edge Pro-certificaatvereisten voor knooppuntcertificaten, [](azure-stack-edge-gpu-manage-certificates.md#node-certificates)eindpuntcertificaten en lokale [UI-certificaten.](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates)

## <a name="prepare-certificates-for-deployment"></a>Certificaten voorbereiden voor implementatie

De certificaatbestanden die u van uw certificeringsinstantie (CA) krijgt, moeten worden geïmporteerd en geëxporteerd met eigenschappen die overeenkomen met de certificaatvereisten van het Azure Stack Edge Pro apparaat. Voltooi de volgende stappen op hetzelfde systeem waar u de aanvragen voor certificaat-ondertekening hebt gegenereerd.

- Als u de certificaten wilt importeren, volgt u de stappen in Certificaten importeren op [de clients die toegang hebben](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)tot Azure Stack Edge Pro apparaat .

- Als u de certificaten wilt exporteren, volgt u de stappen in Certificaten exporteren van de client die [toegang heeft tot Azure Stack Edge Pro apparaat](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Certificaten valideren

Eerst genereert u een juiste mapstructuur en zet u de certificaten in de bijbehorende mappen. Alleen dan valideert u de certificaten met behulp van het hulpprogramma.

1. Voer PowerShell uit als beheerder.

2. Als u de juiste mapstructuur wilt genereren, typt u bij de prompt:

    `New-AzsCertificateFolder -CertificateType AzureStackEdgeDevice -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Converteer het PFX-wachtwoord naar een beveiligde tekenreeks. Type:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Valideer vervolgens de certificaten. Type:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdgeDevice -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Volgende stappen

[Uw apparaat Azure Stack Edge Pro implementeren](azure-stack-edge-gpu-deploy-prep.md)
