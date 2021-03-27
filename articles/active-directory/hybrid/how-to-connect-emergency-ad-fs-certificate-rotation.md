---
title: Nood draaiing van de AD FS-certificaten | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u AD FS certificaten direct intrekt en bijwerkt.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613049"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Nood draaiing van de AD FS certificaten
In het geval dat u de AD FS certificaten direct moet draaien, kunt u de stappen volgen die hieronder in deze sectie worden beschreven.

> [!IMPORTANT]
> Als u de onderstaande stappen in de AD FS omgeving uitvoert, worden de oude certificaten onmiddellijk ingetrokken.  Omdat dit onmiddellijk wordt gedaan, is de normale tijd die doorgaans is toegestaan voor uw Federatie partners om uw nieuwe certificaat te gebruiken, door gegeven. Dit kan leiden tot een onderbreking van de service als de vertrouwens relatie van de nieuwe certificaten wordt gebruikt.  Dit moet worden opgelost zodra alle federatieve partners over de nieuwe certificaten beschikken.

> [!NOTE]
> Micro soft raadt u ten zeerste aan een HSM (Hardware Security module) te gebruiken voor het beveiligen en beveiligen van certificaten.
> Zie [Hardware Security module](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) (aanbevolen procedures voor het beveiligen van AD FS) voor meer informatie.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>De vinger afdruk van het certificaat voor token ondertekening bepalen
Als u het oude token handtekening certificaat wilt intrekken dat AD FS momenteel gebruikt, moet u de vinger afdruk van het certificaat voor token-sigining bepalen.  Gebruik hiervoor de volgende stappen:

 1. Verbinding maken met de online service van micro soft `PS C:\>Connect-MsolService`
 2. Documenteer zowel uw on-premises als Cloud token handtekening certificaat vinger afdruk en verloop datums.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Kopieer de vinger afdruk naar beneden.  Het wordt later gebruikt om de bestaande certificaten te verwijderen.

U kunt de vinger afdruk ook ophalen met behulp van AD FS beheer, naar service/certificaten navigeren, met de rechter muisknop op het certificaat klikken, certificaat weer geven selecteren en vervolgens Details selecteren. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Bepalen of AD FS de certificaten automatisch vernieuwt
AD FS is standaard geconfigureerd voor het automatisch genereren van certificaten voor token-ondertekening en Token-ontsleuteling, zowel op het moment dat de initiële configuratie tijd en wanneer de certificaten de verval datum nadert.

U kunt de volgende Windows Power shell-opdracht uitvoeren: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

De eigenschap AutoCertificateRollover beschrijft of AD FS is geconfigureerd voor het vernieuwen van token-ondertekening en tokens die automatisch worden ontsleuteld.  Als AutoCertificateRollover is ingesteld op TRUE, volgt u de onderstaande instructies in [nieuw zelfondertekend certificaat genereren als AutoCertificateRollover is ingesteld op TRUE].  Als AutoCertificateRollover is ingesteld op FALSE, volgt u de onderstaande instructies in [nieuwe certificaten hand matig genereren als AutoCertificateRollover is ingesteld op FALSE]


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Het nieuwe zelfondertekende certificaat wordt gegenereerd als AutoCertificateRollover is ingesteld op TRUE
In deze sectie maakt u **twee** certificaten voor token-ondertekening.  De eerste heeft de vlag **-dringende** gebruikt, waardoor het huidige primaire certificaat direct wordt vervangen.  De tweede wordt gebruikt voor het secundaire certificaat.  

>[!IMPORTANT]
>De reden voor het maken van twee certificaten is omdat Azure wordt bewaard op informatie over het vorige certificaat.  Als er een tweede wordt gemaakt, wordt Azure gedwongen informatie over het oude certificaat vrij te geven en vervangen door informatie over het tweede certificaat.
>
>Als u het tweede certificaat niet maakt en Azure niet bijwerkt, is het mogelijk dat het oude certificaat voor token-ondertekening gebruikers kan verifiëren.

U kunt de volgende stappen gebruiken om de nieuwe certificaten voor token-ondertekening te genereren.

 1. Zorg ervoor dat u bent aangemeld bij de primaire AD FS-server.
 2. Open Windows PowerShell als beheerder. 
 3. Controleer of de AutoCertificateRollover is ingesteld op waar.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Een nieuw certificaat voor token-ondertekening genereren: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Controleer de update door de volgende opdracht uit te voeren: `Get-ADFSCertificate –CertificateType token-signing`
 6. Genereer nu het tweede certificaat voor token-ondertekening: `Update-ADFSCertificate –CertificateType token-signing` .
 7. U kunt de update controleren door de volgende opdracht opnieuw uit te voeren: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Nieuwe certificaten hand matig genereren als AutoCertificateRollover is ingesteld op FALSE
Als u de standaard automatisch gegenereerde certificaten voor token-ondertekening en Token ontsleuteling niet gebruikt, moet u deze certificaten hand matig verlengen en configureren.  Dit omvat het maken van twee nieuwe certificaten voor token-ondertekening en het importeren hiervan.  Vervolgens moet u één naar primair promo veren, het oude certificaat intrekken en het tweede certificaat configureren als het secundaire certificaat.

Eerst moet u een van de twee nieuwe certificaten van uw certificerings instantie verkrijgen en deze importeren in het persoonlijke certificaat archief van de lokale computer op elke Federatie server. Zie het artikel [een certificaat importeren](https://technet.microsoft.com/library/cc754489.aspx) voor instructies.

>[!IMPORTANT]
>De reden voor het maken van twee certificaten is omdat Azure wordt bewaard op informatie over het vorige certificaat.  Als er een tweede wordt gemaakt, wordt Azure gedwongen informatie over het oude certificaat vrij te geven en vervangen door informatie over het tweede certificaat.
>
>Als u het tweede certificaat niet maakt en Azure niet bijwerkt, is het mogelijk dat het oude certificaat voor token-ondertekening gebruikers kan verifiëren.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Een nieuw certificaat configureren als een secundair certificaat
Vervolgens moet u een certificaat configureren als het AD FS secundaire token voor het ondertekenen of ontsleutelen van het certificaat en het vervolgens promo veren naar de primaire

1. Nadat u het certificaat hebt geïmporteerd. Open de **AD FS-beheer** console.
2. Vouw **service** uit en selecteer vervolgens **certificaten**.
3. Klik in het deel venster acties op **Token-Signing certificaat toevoegen**.
4. Selecteer het nieuwe certificaat in de lijst met weer gegeven certificaten en klik vervolgens op OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Het nieuwe certificaat promo veren van secundair naar primair
Nu het nieuwe certificaat is geïmporteerd en geconfigureerd in AD FS, moet u het primaire certificaat instellen.
1. Open de **AD FS-beheer** console.
2. Vouw **service** uit en selecteer vervolgens **certificaten**.
3. Klik op het secundaire certificaat voor token-ondertekening.
4. Klik in het deel venster **acties** op **instellen als primair**. Klik op Ja bij de bevestigings prompt.
5. Wanneer u het nieuwe certificaat als primair certificaat hebt gepromoveerd, moet u het oude certificaat verwijderen omdat het nog steeds kan worden gebruikt. Zie de sectie [uw oude certificaten verwijderen](#remove-your-old-certificates) hieronder.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>Het tweede certificaat als een secundair certificaat configureren
Nu u het eerste certificaat hebt toegevoegd en primair hebt gemaakt en het oude hebt verwijderd, importeert u het tweede certificaat.  Vervolgens moet u het certificaat configureren als het secundaire certificaat voor het ondertekenen van tokens AD FS

1. Nadat u het certificaat hebt geïmporteerd. Open de **AD FS-beheer** console.
2. Vouw **service** uit en selecteer vervolgens **certificaten**.
3. Klik in het deel venster acties op **Token-Signing certificaat toevoegen**.
4. Selecteer het nieuwe certificaat in de lijst met weer gegeven certificaten en klik vervolgens op OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Azure AD bijwerken met het nieuwe certificaat voor token-ondertekening
Open de Microsoft Azure Active Directory-module voor Windows PowerShell. U kunt ook Windows Power shell openen en vervolgens de opdracht uitvoeren `Import-Module msonline`

Maak verbinding met Azure AD door de volgende opdracht uit te voeren: `Connect-MsolService` en voer vervolgens uw globale beheerders referenties in.

>[!Note]
> Als u deze opdrachten uitvoert op een computer die niet de primaire Federatie server is, voert u eerst de volgende opdracht in: `Set-MsolADFSContext –Computer <servername>` . Vervang door <servername> de naam van de AD FS-server. Voer vervolgens de beheerders referenties voor de AD FS-server in wanneer u hierom wordt gevraagd.

U kunt ook controleren of een update is vereist door de huidige certificaat gegevens in azure AD te controleren. Voer hiervoor de volgende opdracht uit: `Get-MsolFederationProperty` . Voer de naam van het federatieve domein in wanneer u hierom wordt gevraagd.

Als u de certificaat gegevens in azure AD wilt bijwerken, voert u de volgende opdracht uit: `Update-MsolFederatedDomain` en voert u de domein naam in wanneer u hierom wordt gevraagd.

>[!Note]
> Als er een fout wordt weer gegeven bij het uitvoeren van deze opdracht, voert u de volgende opdracht uit: `Update-MsolFederatedDomain –SupportMultipleDomain` en voert u de domein naam in wanneer u hierom wordt gevraagd.

## <a name="replace-ssl-certificates"></a>SSL-certificaten vervangen
Als u het certificaat voor token-ondertekening wilt vervangen door een inbreuk, moet u ook de SSL-certificaten voor AD FS en uw WAP-servers intrekken en vervangen.  

Het intrekken van de SSL-certificaten moet worden uitgevoerd bij de certificerings instantie (CA) die het certificaat heeft uitgegeven.  Deze certificaten worden vaak uitgegeven door externe providers, zoals GoDaddy.  Zie voor een voor beeld (een certificaat intrekken | SSL-certificaten-GoDaddy Help ons).  Zie [hoe het intrekken van certificaten werkt](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN)voor meer informatie.

Zodra het oude SSL-certificaat is ingetrokken en er een nieuw item is uitgegeven, kunt u de SSL-certificaten vervangen. Zie [vervangen van het SSL-certificaat voor AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)voor meer informatie.


## <a name="remove-your-old-certificates"></a>Oude certificaten verwijderen
Als u de oude certificaten hebt vervangen, moet u het oude certificaat verwijderen omdat het nog steeds kan worden gebruikt. . Volg hiervoor de volgende stappen:.  Volg hiervoor de volgende stappen:

1. Zorg ervoor dat u bent aangemeld bij de primaire AD FS-server.
2. Open Windows PowerShell als beheerder. 
4. Het oude certificaat voor token-ondertekening verwijderen: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Federatie partners bijwerken die federatieve meta gegevens kunnen gebruiken
Als u een nieuw certificaat voor token-ondertekening of tokenontsleuteling hebt vernieuwd en geconfigureerd, moet u ervoor zorgen dat de nieuwe certificaten zijn opgenomen in alle federatieve partners (resource organisatie of organisatie partners die in uw AD FS worden weer gegeven door Relying Party vertrouwens relaties en vertrouwens relaties met claim providers).

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Federatie partners bijwerken die geen federatieve meta gegevens kunnen gebruiken
Als uw Federatie partners uw federatieve meta gegevens niet kunnen gebruiken, moet u ze hand matig verzenden naar de open bare sleutel van het nieuwe certificaat voor token-ondertekening/token-ontsleuteling. Verzend uw nieuwe open bare sleutel van het certificaat (. cer-bestand of. p7b als u de hele keten wilt opnemen) aan al uw resource organisatie-of account organisatie partners (die worden weer gegeven in uw AD FS door Relying Party vertrouwens relaties en vertrouwens relaties met claim providers). Laat de partners wijzigingen aan hun zijde implementeren om de nieuwe certificaten te vertrouwen.



## <a name="revoke-refresh-tokens-via-powershell"></a>Vernieuwings tokens intrekken via Power shell
Nu wilt u de vernieuwings tokens intrekken voor gebruikers die ze kunnen hebben en ze ertoe verplichten om zich opnieuw aan te melden en nieuwe tokens op te halen.  Dit meldt gebruikers af op hun telefoon, met de huidige webmail-sessies, samen met andere items die gebruikmaken van tokens en vernieuwings tokens.  Informatie vindt u [hier](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) en u kunt ook verwijzen naar het [intrekken van de gebruikers toegang in azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Volgende stappen

- [SSL-certificaten beheren in AD FS en WAP in Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Certificaten voor token-ondertekening en Token ontsleuteling verkrijgen en configureren voor AD FS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Federatie certificaten voor Microsoft 365 en Azure Active Directory vernieuwen](how-to-connect-fed-o365-certs.md)



















