---
title: Verificatie op basis van een certificaat-Azure Active Directory
description: Meer informatie over het configureren van op certificaten gebaseerde verificatie in uw omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 6db7037cbcad335db77784ecfa624f08e88b1e83
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744428"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Aan de slag met verificatie op basis van certificaten in Azure Active Directory

Met verificatie op basis van certificaten kunt u door Azure Active Directory met een client certificaat op een Windows-, Android-of iOS-apparaat verifiëren wanneer u uw Exchange Online-account verbindt met:

- Mobiele micro soft-toepassingen, zoals micro soft Outlook en micro soft Word
- Exchange ActiveSync-clients (EAS)

Als u deze functie configureert, hoeft u geen combi natie van gebruikers naam en wacht woord op te geven in bepaalde mail-en Microsoft Office toepassingen op uw mobiele apparaat.

Dit onderwerp:

- Biedt u de stappen voor het configureren en gebruiken van verificatie op basis van certificaten voor gebruikers van tenants in Office 365 Enter prise, Business, Education en Amerikaanse overheids plannen. Deze functie is beschikbaar als preview-versie van Office 365 China, Amerikaanse overheids verdediging en Amerikaanse overheids instanties.
- Er wordt van uitgegaan dat u al een [open bare-sleutel infrastructuur (PKI)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831740(v=ws.11)) en [AD FS](../hybrid/how-to-connect-fed-whatis.md) geconfigureerd.

## <a name="requirements"></a>Vereisten

Als u verificatie op basis van certificaten wilt configureren, moeten de volgende instructies waar zijn:

- Verificatie op basis van een certificaat (dit) wordt alleen ondersteund voor federatieve omgevingen voor browser toepassingen, native clients die gebruikmaken van moderne verificatie (ADAL) of MSAL-bibliotheken. De enige uitzonde ring is Exchange Active Sync (EAS) voor Exchange Online (EXO), die kan worden gebruikt voor federatieve en beheerde accounts.
- De basis certificerings instantie en alle tussenliggende certificerings instanties moeten worden geconfigureerd in Azure Active Directory.
- Elke certificerings instantie moet een certificaatintrekkingslijst (CRL) hebben waarnaar kan worden verwezen via een Internet gerichte URL.
- U moet ten minste één certificerings instantie configureren in Azure Active Directory. U vindt verwante stappen in de sectie [de certificaat instanties configureren](#step-2-configure-the-certificate-authorities) .
- Voor Exchange ActiveSync-clients moet het client certificaat het e-mail adres van de gebruiker in Exchange Online hebben in de principal-naam of de naam waarde RFC822 van het veld alternatieve naam voor onderwerp. Azure Active Directory wijst de waarde RFC822 toe aan het kenmerk proxy adres in de Directory.
- Uw client apparaat moet toegang hebben tot ten minste één certificerings instantie die client certificaten uitgeeft.
- Er moet een client certificaat voor client verificatie worden verleend aan de client.

>[!IMPORTANT]
>De maximale grootte van een CRL voor Azure Active Directory het downloaden en opslaan van de cache is 20 MB, en de tijd die nodig is om de CRL te downloaden, mag niet langer zijn dan 10 seconden.  Als Azure Active Directory geen CRL kan downloaden, zullen op certificaten gebaseerde verificaties die gebruikmaken van certificaten die zijn uitgegeven door de bijbehorende CA, mislukken. Aanbevolen procedures om ervoor te zorgen dat CRL-bestanden zich in grootte beperkingen bevinden, is om de levens duur van certificaten binnen redelijke limieten te houden en verlopen certificaten op te schonen.

## <a name="step-1-select-your-device-platform"></a>Stap 1: het platform van het apparaat selecteren

Als eerste stap, voor het platform dat u bevalt, moet u het volgende controleren:

- Ondersteuning voor mobiele Office-toepassingen
- De specifieke implementatie vereisten

De verwante informatie bestaat voor de volgende platformen:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Stap 2: de certificerings instanties configureren

Als u uw certificerings instanties in Azure Active Directory wilt configureren, uploadt u voor elke certificerings instantie het volgende:

* Het open bare gedeelte van het certificaat in *. CER* -indeling
* De Internet gerichte Url's waar de certificaatintrekkingslijsten zich bevinden

Het schema voor een certificerings instantie ziet er als volgt uit:

```csharp
    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }
```

Voor de configuratie kunt u de [Azure Active Directory Power shell versie 2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)gebruiken:

1. Start Windows Power shell met beheerders bevoegdheden.
2. Installeer de Azure AD-module versie [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) of hoger.

```powershell
    Install-Module -Name AzureAD –RequiredVersion 2.0.0.33
```

Als eerste configuratie stap moet u een verbinding maken met uw Tenant. Zodra er een verbinding met uw Tenant bestaat, kunt u de vertrouwde certificerings instanties die in uw Directory zijn gedefinieerd, bekijken, toevoegen, verwijderen en wijzigen.

### <a name="connect"></a>Verbinding maken

Gebruik de cmdlet [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) om verbinding te maken met uw Tenant:

```azurepowershell
    Connect-AzureAD
```

### <a name="retrieve"></a>Ophalen

Gebruik de cmdlet [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) om de vertrouwde certificerings instanties op te halen die in uw Directory zijn gedefinieerd.

```azurepowershell
    Get-AzureADTrustedCertificateAuthority
```

### <a name="add"></a>Toevoegen

Als u een vertrouwde certificerings instantie wilt maken, gebruikt u de cmdlet [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) en stelt u het kenmerk **crlDistributionPoint** in op een juiste waarde:

```azurepowershell
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca
```

### <a name="remove"></a>Verwijderen

Als u een vertrouwde certificerings instantie wilt verwijderen, gebruikt u de cmdlet [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) :

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]
```

### <a name="modify"></a>Wijzigen

Als u een vertrouwde certificerings instantie wilt wijzigen, gebruikt u de cmdlet [set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) :

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]
```

## <a name="step-3-configure-revocation"></a>Stap 3: intrekking configureren

Als u een client certificaat wilt intrekken, wordt Azure Active Directory de certificaatintrekkingslijst (CRL) opgehaald van de Url's die zijn geüpload als onderdeel van de gegevens van de certificerings instantie en opgeslagen in de cache. De laatste publicatie-Time Stamp (**ingangs datum** eigenschap) in de CRL wordt gebruikt om ervoor te zorgen dat de CRL nog geldig is. Er wordt regel matig naar de CRL verwezen om de toegang tot certificaten die deel uitmaken van de lijst in te trekken.

Als een meer directe intrekking is vereist (bijvoorbeeld wanneer een gebruiker een apparaat kwijtraakt), kan het autorisatie token van de gebruiker ongeldig worden gemaakt. Als u het autorisatie token ongeldig wilt maken, stelt u het veld **StsRefreshTokenValidFrom** voor deze bepaalde gebruiker in met behulp van Windows Power shell. U moet het veld **StsRefreshTokenValidFrom** bijwerken voor elke gebruiker waarvoor u de toegang wilt intrekken.

Om ervoor te zorgen dat het intrekken persistent blijft, moet u de **ingangs datum** van de CRL instellen op een datum na de waarde die is ingesteld door **StsRefreshTokenValidFrom** en ervoor zorgen dat het betreffende certificaat zich in de CRL bevindt.

De volgende stappen beschrijven het proces voor het bijwerken en ongeldig valideren van het autorisatie token door het veld **StsRefreshTokenValidFrom** in te stellen.

**Intrekking configureren:**

1. Verbinding maken met beheerders referenties voor de MSOL-service:

```powershell
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
```

2. De huidige StsRefreshTokensValidFrom-waarde voor een gebruiker ophalen:

```powershell
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom
```

3. Configureer een nieuwe StsRefreshTokensValidFrom-waarde voor de gebruiker die gelijk is aan de huidige tijds tempel:

```powershell
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")
```

De datum die u instelt, moet in de toekomst liggen. Als de datum niet in de toekomst ligt, is de eigenschap **StsRefreshTokensValidFrom** niet ingesteld. Als de datum in de toekomst ligt, wordt **StsRefreshTokensValidFrom** ingesteld op de huidige tijd (niet de datum die wordt aangegeven door Set-MsolUser opdracht).

## <a name="step-4-test-your-configuration"></a>Stap 4: uw configuratie testen

### <a name="testing-your-certificate"></a>Uw certificaat testen

Als eerste configuratie test moet u zich proberen aan te melden bij [Outlook Web Access](https://outlook.office365.com) of [share point online](https://microsoft.sharepoint.com) met behulp **van uw browser op het apparaat**.

Als uw aanmelding is geslaagd, weet u dat:

- Het gebruikers certificaat is ingericht voor uw test apparaat
- AD FS is correct geconfigureerd

### <a name="testing-office-mobile-applications"></a>Mobiele Office-toepassingen testen

**Verificatie op basis van certificaten testen voor uw mobiele Office-toepassing:**

1. Installeer een mobiele Office-toepassing (bijvoorbeeld OneDrive) op uw test apparaat.
3. Start de toepassing.
4. Voer uw gebruikers naam in en selecteer vervolgens het gebruikers certificaat dat u wilt gebruiken.

U moet zijn aangemeld.

### <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync-client toepassingen testen

Voor toegang tot Exchange ActiveSync (EAS) via verificatie op basis van certificaten moet er een EAS-profiel met het client certificaat beschikbaar zijn voor de toepassing.

Het EAS-profiel moet de volgende informatie bevatten:

- Het gebruikers certificaat dat voor verificatie moet worden gebruikt

- Het EAS-eind punt (bijvoorbeeld outlook.office365.com)

Een EAS-profiel kan worden geconfigureerd en op het apparaat worden geplaatst met behulp van het gebruik van Mobile Device Management (MDM), zoals intune, of door het certificaat hand matig in het EAS-profiel op het apparaat te plaatsen.

### <a name="testing-eas-client-applications-on-android"></a>EAS-client toepassingen testen op Android

**Verificatie van certificaten testen:**

1. Configureer een EAS-profiel in de toepassing die voldoet aan de vereisten in de vorige sectie.
2. Open de toepassing en controleer of het e-mail bericht is gesynchroniseerd.

## <a name="next-steps"></a>Volgende stappen

[Aanvullende informatie over verificatie op basis van certificaten op Android-apparaten.](active-directory-certificate-based-authentication-android.md)

[Aanvullende informatie over verificatie op basis van certificaten op iOS-apparaten.](active-directory-certificate-based-authentication-ios.md)