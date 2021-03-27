---
title: Certificaat vernieuwing voor Microsoft 365-en Azure AD-gebruikers | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u gebruikers Microsoft 365 om problemen met e-mail berichten op te lossen die hen op de hoogte stellen van het vernieuwen van een certificaat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e81cb9018d817fb206915a81fdc3bdd60f6b08c
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611885"
---
# <a name="renew-federation-certificates-for-microsoft-365-and-azure-active-directory"></a>Federatie certificaten voor Microsoft 365 en Azure Active Directory vernieuwen
## <a name="overview"></a>Overzicht
Voor een geslaagde Federatie tussen Azure Active Directory (Azure AD) en Active Directory Federation Services (AD FS), moeten de certificaten die worden gebruikt door AD FS beveiligings tokens ondertekenen met Azure AD, overeenkomen met wat er in azure AD is geconfigureerd. Niet-overeenkomend kan leiden tot een verbroken vertrouwens relatie. Azure AD zorgt ervoor dat deze informatie synchroon blijft wanneer u AD FS en Web Application proxy (voor extranet toegang) implementeert.

> [!NOTE]
> Dit artikel bevat informatie over het manging van uw Federatie cerficates.  Zie [nood draaiing van de AD FS-certificaten](how-to-connect-emergency-ad-fs-certificate-rotation.md) voor meer informatie.

In dit artikel vindt u meer informatie over het beheren van uw token handtekening certificaten en het synchroniseren met Azure AD in de volgende gevallen:

* U implementeert de webtoepassingsproxy niet en daarom is de federatieve meta gegevens niet beschikbaar in het extranet.
* U gebruikt niet de standaard configuratie van AD FS voor token handtekening certificaten.
* U gebruikt een id-provider van een derde partij.

> [!IMPORTANT]
> Micro soft raadt u ten zeerste aan een HSM (Hardware Security module) te gebruiken voor het beveiligen en beveiligen van certificaten.
> Zie [Hardware Security module](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) (aanbevolen procedures voor het beveiligen van AD FS) voor meer informatie.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Standaard configuratie van AD FS voor token handtekening certificaten
Het ondertekenen van tokens en certificaten voor het ontsleutelen van tokens zijn meestal zelfondertekende certificaten en zijn goed voor één jaar. AD FS bevat standaard een proces voor automatische vernieuwing, met de naam **AutoCertificateRollover**. Als u AD FS 2,0 of hoger gebruikt, wordt uw certificaat automatisch bijgewerkt door Microsoft 365 en Azure AD voordat het verloopt.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Melding over verlenging van het Microsoft 365-beheer centrum of een e-mail bericht
> [!NOTE]
> Als u een e-mail bericht of een portal melding hebt ontvangen waarin u wordt gevraagd uw certificaat voor Office te vernieuwen, raadpleegt u [wijzigingen in token handtekening certificaten beheren](#managecerts) om te controleren of u actie moet ondernemen. Micro soft is op de hoogte van een mogelijk probleem dat kan leiden tot meldingen voor het verzenden van certificaten, zelfs wanneer er geen actie is vereist.
>
>

Azure AD probeert de federatieve meta gegevens te bewaken en de token handtekening certificaten bij te werken zoals aangegeven door deze meta gegevens. 30 dagen voordat de certificaten voor token-ondertekening zijn verlopen, controleert Azure AD of er nieuwe certificaten beschikbaar zijn door de federatieve meta gegevens te pollen.

* Als de federatieve meta gegevens kunnen worden gecontroleerd en de nieuwe certificaten worden opgehaald, wordt er geen e-mail melding of waarschuwing in het Microsoft 365 beheer centrum aan de gebruiker uitgegeven.
* Als de nieuwe token handtekening certificaten niet kan worden opgehaald, omdat de federatieve meta gegevens niet bereikbaar zijn of de automatische rollover van het certificaat niet is ingeschakeld, geeft Azure AD een e-mail melding en een waarschuwing in het Microsoft 365-beheer centrum.

![Office 365-Portal melding](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Als u AD FS gebruikt om bedrijfs continuïteit te garanderen, moet u controleren of uw servers de volgende updates hebben zodat verificatie fouten voor bekende problemen niet optreden. Dit verkleint bekende problemen met de AD FS proxy server voor deze verlenging en toekomstige verlengings perioden:
>
> Server 2012 R2- [Windows Server mei 2014 Rollup](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 en 2012- [verificatie via proxy mislukt in Windows Server 2012 of windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Controleren of de certificaten moeten worden bijgewerkt <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Stap 1: Controleer de status van de AutoCertificateRollover
Open Power shell op uw AD FS-server. Controleer of de AutoCertificateRollover-waarde is ingesteld op True.

```azurepowershell-interactive
Get-Adfsproperties
```

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Als u AD FS 2,0 gebruikt, moet u eerst Add-Pssnapin Microsoft. ADFS. Power shell uitvoeren.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Stap 2: controleren of AD FS en Azure AD zijn gesynchroniseerd
Open de MSOnline Power shell-prompt op uw AD FS-server en maak verbinding met Azure AD.

> [!NOTE]
> MSOL-Cmdlets maakt deel uit van de MSOnline Power shell-module.
> U kunt de MSOnline Power shell-module rechtstreeks vanuit de PowerShell Gallery downloaden.
> 
>

```azurepowershell-interactive
Install-Module MSOnline
```

Maak verbinding met Azure AD met behulp van de MSOnline Power shell-module.

```azurepowershell-interactive
Import-Module MSOnline
Connect-MsolService
```

Controleer de certificaten die zijn geconfigureerd in AD FS en eigenschappen van de Azure AD-vertrouwens relatie voor het opgegeven domein.

```azurepowershell-interactive
Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate
```

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Als de vinger afdrukken in beide uitvoer overeenkomen, worden uw certificaten gesynchroniseerd met Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Stap 3: controleren of uw certificaat bijna is verlopen
Controleer in de uitvoer van Get-MsolFederationProperty of Get-AdfsCertificate voor de datum onder ' niet na '. Als de datum minder dan 30 dagen vrij is, moet u actie ondernemen.

| AutoCertificateRollover | Certificaten die zijn gesynchroniseerd met Azure AD | Federatieve meta gegevens zijn openbaar toegankelijk | Controleert | Bewerking |
|:---:|:---:|:---:|:---:|:---:|
| Ja |Ja |Ja |- |Geen actie nodig. Zie [token handtekening certificaat automatisch vernieuwen](#autorenew). |
| Ja |Nee |- |Minder dan 15 dagen |Vernieuw direct. Zie [token handtekening certificaat hand matig vernieuwen](#manualrenew). |
| No |- |- |Minder dan 30 dagen |Vernieuw direct. Zie [token handtekening certificaat hand matig vernieuwen](#manualrenew). |

\[-] Is niet van belang

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Het token handtekening certificaat automatisch vernieuwen (aanbevolen) <a name="autorenew"></a>
U hoeft geen hand matige stappen uit te voeren als aan beide volgende voor waarden wordt voldaan:

* U hebt Web Application proxy geïmplementeerd, waarmee toegang tot de federatieve meta gegevens kan worden ingeschakeld vanuit het extranet.
* U gebruikt de AD FS standaard configuratie (AutoCertificateRollover is ingeschakeld).

Controleer het volgende om te controleren of het certificaat automatisch kan worden bijgewerkt.

**1. de AD FS eigenschap AutoCertificateRollover moet zijn ingesteld op True.** Dit geeft aan dat AD FS automatisch nieuwe certificaten voor token-ondertekening en Token ontsleuteling genereert voordat de oude worden verlopen.

**2. de federatieve meta gegevens van AD FS zijn openbaar toegankelijk.** Controleer of uw federatieve meta gegevens openbaar toegankelijk zijn door te navigeren naar de volgende URL van een computer op het open bare Internet (van het bedrijfs netwerk):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

waar `(your_FS_name)` wordt vervangen door de naam van de Federation service-host die uw organisatie gebruikt, zoals FS.contoso.com.  Als u beide instellingen wel kunt controleren, hoeft u niets anders te doen.  

Voorbeeld: `https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml`
## <a name="renew-the-token-signing-certificate-manually"></a>Het token handtekening certificaat hand matig vernieuwen <a name="manualrenew"></a>
U kunt ervoor kiezen om de token handtekening certificaten hand matig te vernieuwen. De volgende scenario's kunnen bijvoorbeeld beter werken voor hand matige verlenging:

* Certificaten voor token-ondertekening zijn geen zelfondertekende certificaten. De meest voorkomende reden hiervoor is dat uw organisatie AD FS certificaten beheert die zijn Inge schreven bij een organisatie certificerings instantie.
* Met netwerk beveiliging kunnen de federatieve meta gegevens niet openbaar beschikbaar zijn.

In deze scenario's moet u elke keer dat u de certificaten voor token-ondertekening bijwerkt, ook uw Microsoft 365 domein bijwerken met behulp van de Power shell-opdracht update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Stap 1: Zorg ervoor dat AD FS nieuwe token handtekening certificaten heeft
**Niet-standaard configuratie**

Als u een niet-standaard configuratie van AD FS gebruikt (waarbij **AutoCertificateRollover** is ingesteld op **False**), gebruikt u waarschijnlijk aangepaste certificaten (niet zelf-ondertekend). Zie [certificaat vereisten voor federatieve servers](/windows-server/identity/ad-fs/design/certificate-requirements-for-federation-servers)voor meer informatie over het vernieuwen van de AD FS token handtekening certificaten.

**De federatieve meta gegevens zijn niet openbaar beschikbaar**

Als **AutoCertificateRollover** echter is ingesteld op **True**, maar uw federatieve meta gegevens niet openbaar toegankelijk zijn, moet u eerst controleren of nieuwe token handtekening certificaten zijn gegenereerd door AD FS. Ga als volgt te werk om te controleren of u nieuwe token handtekening certificaten hebt:

1. Controleer of u bent aangemeld bij de primaire AD FS-server.
2. Controleer de huidige handtekening certificaten in AD FS door een Power shell-opdracht venster te openen en de volgende opdracht uit te voeren:

    PS C: \> Get-ADFSCertificate – CertificateType token-ondertekening

   > [!NOTE]
   > Als u AD FS 2,0 gebruikt, moet u eerst Add-Pssnapin Microsoft. ADFS. Power shell uitvoeren.
   >
   >
3. Bekijk de uitvoer van de opdracht op alle certificaten die worden weer gegeven. Als AD FS een nieuw certificaat hebt gegenereerd, ziet u in de uitvoer twee certificaten: een waarvoor de **IsPrimary** -waarde **waar** is en de **NotAfter** -datum ligt binnen 5 dagen en een waarvoor **IsPrimary** **Onwaar** is en **NotAfter** ongeveer een jaar in de toekomst ligt.
4. Als u slechts één certificaat ziet en de **NotAfter** -datum binnen vijf dagen valt, moet u een nieuw certificaat genereren.
5. Als u een nieuw certificaat wilt genereren, voert u de volgende opdracht uit in een Power shell-opdracht prompt: `PS C:\Update-ADFSCertificate –CertificateType token-signing` .
6. Controleer de update door de volgende opdracht opnieuw uit te voeren: PS C: \> Get-ADFSCertificate – CertificateType token ondertekening

Twee certificaten moeten nu worden vermeld, waarvan een van de **NotAfter** datum ongeveer één jaar in de toekomst heeft en waarvoor de **IsPrimary** -waarde **False** is.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-microsoft-365-trust"></a>Stap 2: de nieuwe token handtekening certificaten bijwerken voor de vertrouwens relatie van de Microsoft 365
Werk Microsoft 365 met de nieuwe token handtekening certificaten die voor de vertrouwens relatie moeten worden gebruikt als volgt bij.

1. Open de Microsoft Azure Active Directory-module voor Windows PowerShell.
2. Voer $cred = Get-Credential. Wanneer u met deze cmdlet om referenties wordt gevraagd, typt u de referenties van uw account voor de Cloud service beheerder.
3. Voer Connect-MsolService – referentie $cred. Met deze cmdlet maakt u verbinding met de Cloud service. Het maken van een context waarmee u verbinding maakt met de Cloud service is vereist voor het uitvoeren van een van de extra cmdlets die door het hulp programma worden geïnstalleerd.
4. Als u deze opdrachten uitvoert op een computer die niet de AD FS primaire Federatie server is, voert u de Set-MSOLAdfscontext-computer &lt; AD FS primaire server uit &gt; , waarbij &lt; AD FS primaire server &gt; de interne FQDN-naam van de primaire AD FS server is. Met deze cmdlet wordt een context gemaakt waarmee u verbinding maakt met AD FS.
5. Voer Update-MSOLFederatedDomain – DomainName &lt; domein &gt; . Met deze cmdlet worden de instellingen van AD FS in de Cloud service bijgewerkt en wordt de vertrouwens relatie tussen de twee geconfigureerd.

> [!NOTE]
> Als u meerdere domeinen op het hoogste niveau wilt ondersteunen, zoals contoso.com en fabrikam.com, moet u de **SupportMultipleDomain** -switch gebruiken met cmdlets. Zie [ondersteuning voor meerdere domeinen op het hoogste niveau](how-to-connect-install-multiple-domains.md)voor meer informatie.
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Azure AD-vertrouwens relatie herstellen met behulp van Azure AD Connect <a name="connectrenew"></a>
Als u uw AD FS-Farm en Azure AD-vertrouwens relatie hebt geconfigureerd met behulp van Azure AD Connect, kunt u Azure AD Connect gebruiken om te detecteren of u actie moet ondernemen voor uw token handtekening certificaten. Als u de certificaten moet vernieuwen, kunt u hiervoor Azure AD Connect gebruiken.

Zie [de vertrouwens relatie herstellen](how-to-connect-fed-management.md)voor meer informatie.

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Stappen voor AD FS en Azure AD-certificaat updates
Certificaten voor token-ondertekening zijn standaard x509-certificaten die worden gebruikt voor het veilig ondertekenen van alle tokens die door de Federatie server worden uitgegeven. Certificaten voor het ontsleutelen van tokens zijn standaard x509-certificaten die worden gebruikt voor het ontsleutelen van binnenkomende tokens. 

AD FS is standaard geconfigureerd voor het automatisch genereren van certificaten voor token-ondertekening en Token-ontsleuteling, zowel op het moment dat de initiële configuratie tijd en wanneer de certificaten de verval datum nadert.

Azure AD probeert binnen 30 dagen vóór het verstrijken van het huidige certificaat een nieuw certificaat op te halen uit de meta gegevens van uw Federation service. Als er op dat moment geen nieuw certificaat beschikbaar is, zal Azure AD de meta gegevens op regel matige dagelijkse intervallen blijven bewaken. Zodra het nieuwe certificaat beschikbaar is in de meta gegevens, worden de Federatie-instellingen voor het domein bijgewerkt met de nieuwe certificaat gegevens. U kunt gebruiken `Get-MsolDomainFederationSettings` om te controleren of het nieuwe certificaat wordt weer geven in de NextSigningCertificate/SigningCertificate.

Zie [certificaten voor token-ondertekening en Token-ontsleuteling verkrijgen en configureren voor AD FS](/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs) voor meer informatie over token handtekening certificaten in AD FS.
