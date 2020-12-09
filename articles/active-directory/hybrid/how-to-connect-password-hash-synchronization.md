---
title: Wachtwoord hash-synchronisatie implementeren met Azure AD Connect Sync | Microsoft Docs
description: Bevat informatie over hoe wachtwoord-hash synchronisatie werkt en hoe u deze kunt instellen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47d7d541ed7d9805641ffdfde381d482c8700006
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858736"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Wachtwoord-hashsynchronisatie implementeren met Azure AD Connect-synchronisatie
In dit artikel vindt u informatie die u nodig hebt om uw gebruikers wachtwoorden te synchroniseren vanuit een on-premises Active Directory-exemplaar naar een op de cloud gebaseerde Azure Active Directory (Azure AD)-exemplaar.

## <a name="how-password-hash-synchronization-works"></a>Hoe synchronisatie van wachtwoord-hashes werkt
De Active Directory domein service slaat wacht woorden op in de vorm van een hash-waarde weer geven, van het werkelijke gebruikers wachtwoord. Een hash-waarde is een resultaat van een reken kundige functie ( *hash-algoritme*). Er bestaat geen methode het resultaat van een eenzijdige functie terug te draaien naar de versie in tekst zonder opmaak van een wachtwoord. 

Als u uw wacht woord wilt synchroniseren, haalt Azure AD Connect Sync uw wacht woord-hash uit het on-premises Active Directory exemplaar. Er wordt extra beveiligings verwerking toegepast op de wacht woord-hash voordat deze wordt gesynchroniseerd met de Azure Active Directory Authentication-service. Wacht woorden worden op basis van elke gebruiker en in chronologische volg orde gesynchroniseerd.

De werkelijke gegevens stroom van het synchronisatie proces voor wachtwoord hash is vergelijkbaar met de synchronisatie van gebruikers gegevens. Wacht woorden worden echter vaker gesynchroniseerd dan het standaard venster voor adreslijst synchronisatie voor andere kenmerken. Het synchronisatie proces voor wacht woord-hash wordt elke twee minuten uitgevoerd. U kunt de frequentie van dit proces niet wijzigen. Wanneer u een wacht woord synchroniseert, wordt het bestaande Cloud wachtwoord overschreven.

De eerste keer dat u de functie voor het synchroniseren van wacht woord-hashes inschakelt, voert het een initiële synchronisatie uit van de wacht woorden van alle gebruikers in het bereik. U kunt niet expliciet een subset van gebruikers wachtwoorden definiëren die u wilt synchroniseren. Als er echter meerdere connectors zijn, is het mogelijk om wachtwoord hash-synchronisatie uit te scha kelen voor sommige connectors, maar niet voor andere met de cmdlet [set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) .

Wanneer u een on-premises wacht woord wijzigt, wordt het bijgewerkte wacht woord gesynchroniseerd, meestal binnen een paar minuten.
Met de functie voor synchronisatie van wacht woord-hashes worden mislukte synchronisatie pogingen automatisch opnieuw geprobeerd. Als er een fout optreedt tijdens een poging om een wacht woord te synchroniseren, wordt er een fout geregistreerd in de logboeken.

De synchronisatie van een wacht woord heeft geen invloed op de gebruiker die momenteel is aangemeld.
De huidige Cloud service sessie wordt niet onmiddellijk beïnvloed door een gesynchroniseerde wachtwoord wijziging die plaatsvindt tijdens het aanmelden bij een Cloud service. Wanneer u de Cloud service echter opnieuw moet verifiëren, moet u uw nieuwe wacht woord opgeven.

Een gebruiker moet hun bedrijfs referenties een tweede keer invoeren om te verifiëren bij Azure AD, ongeacht of ze zijn aangemeld bij het bedrijfs netwerk. Dit patroon kan worden geminimaliseerd, maar als de gebruiker het selectie vakje aangemeld blijven (KMSI) inschakelt bij het aanmelden. Met deze selectie wordt een sessie cookie ingesteld die de verificatie gedurende 180 dagen omzeilt. KMSI-gedrag kan worden ingeschakeld of uitgeschakeld door de Azure AD-beheerder. Daarnaast kunt u wacht woorden verminderen door [naadloze SSO](how-to-connect-sso.md)in te scha kelen, waarbij gebruikers automatisch worden ondertekend wanneer ze zich op hun bedrijfs apparaten bevinden die zijn verbonden met uw bedrijfs netwerk.

> [!NOTE]
> Wachtwoord synchronisatie wordt alleen ondersteund voor de object type gebruiker in Active Directory. Dit wordt niet ondersteund voor het object type iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Gedetailleerde beschrijving van de werking van wachtwoord-hash-synchronisatie

In de volgende sectie wordt uitgelegd hoe de synchronisatie van wachtwoord-hashes werkt tussen Active Directory en Azure AD.

![Gedetailleerde wachtwoord stroom](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Op de AD Connect-server wordt elke twee minuten om de wacht woord-hashes (het kenmerk unicodePwd) van een DC aangevraagd.  Deze aanvraag is via het standaard [MS-DRSR-](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) replicatie protocol dat wordt gebruikt voor het synchroniseren van gegevens tussen dc's. Het service account moet repliceren Directory-wijzigingen hebben en Directory repliceren wijzigt alle AD-machtigingen (die standaard worden verleend tijdens de installatie) om de wacht woord-hashes te verkrijgen.
2. Voordat de domein controller wordt verzonden, wordt de MD4-wachtwoord-hash versleuteld met behulp van een sleutel die een [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) -hash van de RPC-sessie sleutel en een Salt is. Vervolgens wordt het resultaat verzonden naar de synchronisatie agent voor wacht woord-hash via RPC. De domein controller geeft het zout ook door aan de synchronisatie agent door gebruik te maken van het DC-replicatie protocol, zodat de agent de envelop kan ontsleutelen.
3. Nadat de synchronisatie agent voor wacht woord-hash de versleutelde envelop heeft, gebruikt [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) en het zout om een sleutel te genereren voor het ontsleutelen van de ontvangen gegevens naar de oorspronkelijke MD4-indeling. De synchronisatie agent voor wacht woord-hashes heeft nooit toegang tot het wacht woord voor lees bare tekst. Het gebruik van MD5 van de wacht woord-hash-synchronisatie agent is strikt voor compatibiliteit van replicatie protocollen met de domein controller, en wordt alleen lokaal gebruikt tussen de domein controller en de synchronisatie agent voor wacht woord-hashes.
4. De wacht woord-hash synchronisatie agent breidt de hash van het binaire wacht woord van 16 bytes naar 64 bytes door eerst de hash naar een hexadecimale teken reeks van 32 bytes te converteren en vervolgens deze teken reeks terug te converteren naar binair met UTF-16-code ring.
5. De synchronisatie agent voor wacht woord-hashes voegt een per gebruiker zout toe, bestaande uit een Salt van 10 bytes lengte, tot het binaire 64-byte om de oorspronkelijke hash verder te beveiligen.
6. De synchronisatie agent voor wacht woord-hash combineert vervolgens de MD4-hash plus het per gebruiker zout en voert deze in de functie [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) . 1000 iteraties van de hash-algoritme voor het [HMAC-sha256-](/dotnet/api/system.security.cryptography.hmacsha256) versleutelen worden gebruikt. 
7. De wacht woord-hash synchronisatie agent neemt de resulterende 32-byte hash, voegt zowel het zouten per gebruiker als het aantal SHA256-iteraties toe (voor gebruik door Azure AD). vervolgens verzendt de teken reeks van Azure AD Connect naar Azure AD via TLS.</br> 
8. Wanneer een gebruiker zich probeert aan te melden bij Azure AD en het wacht woord invoert, wordt het wacht woord uitgevoerd via hetzelfde MD4 + Salt + PBKDF2 + HMAC-SHA256-proces. Als de resulterende hash overeenkomt met de hash die is opgeslagen in azure AD, wordt het juiste wacht woord door de gebruiker ingevoerd en geverifieerd.

> [!NOTE]
> De oorspronkelijke MD4-hash wordt niet verzonden naar Azure AD. In plaats daarvan wordt de SHA256-hash van de oorspronkelijke MD4-hash verzonden. Als de hash die is opgeslagen in azure AD wordt opgehaald, kan deze niet worden gebruikt in een on-premises Pass-the-hash-aanval.

### <a name="security-considerations"></a>Beveiligingsoverwegingen

Bij het synchroniseren van wacht woorden wordt de niet-gecodeerde versie van uw wacht woord niet weer gegeven aan de synchronisatie functie voor wachtwoord hash, naar Azure AD of een van de gekoppelde services.

Gebruikers verificatie vindt plaats op basis van Azure AD in plaats van op de eigen Active Directory-instantie van de organisatie. De SHA256-wachtwoord gegevens die zijn opgeslagen in azure AD: een hash van de oorspronkelijke MD4-hash--is veiliger dan wat is opgeslagen in Active Directory. Omdat deze SHA256-hash niet kan worden ontsleuteld, kan deze niet worden teruggebracht naar de Active Directory omgeving van de organisatie en worden weer gegeven als een geldig gebruikers wachtwoord in een Pass-the-hash-aanval.

### <a name="password-policy-considerations"></a>Overwegingen voor wachtwoord beleid

Er zijn twee typen wachtwoord beleidsregels die worden beïnvloed door het inschakelen van wachtwoord hash-synchronisatie:

* Beleid voor wachtwoord complexiteit
* Beleid voor wachtwoord verloop

#### <a name="password-complexity-policy"></a>Beleid voor wachtwoord complexiteit

Als wachtwoord hash-synchronisatie is ingeschakeld, worden de beleids regels voor wachtwoord complexiteit in uw on-premises Active Directory-exemplaar de complexiteits beleidsregels in de Cloud overschreven voor gesynchroniseerde gebruikers. U kunt alle geldige wacht woorden van uw on-premises Active Directory-exemplaar gebruiken om toegang te krijgen tot Azure AD-Services.

> [!NOTE]
> Wacht woorden voor gebruikers die rechtstreeks in de Cloud zijn gemaakt, zijn nog steeds onderworpen aan wachtwoord beleid zoals gedefinieerd in de Cloud.

#### <a name="password-expiration-policy"></a>Beleid voor wachtwoord verloop

Als een gebruiker zich in het bereik van de wachtwoord-hash-synchronisatie bevindt, wordt het wacht woord voor het Cloud account standaard ingesteld op *nooit verlopen*.

U kunt zich blijven aanmelden bij uw Cloud Services met behulp van een gesynchroniseerd wacht woord dat is verlopen in uw on-premises omgeving. Het wacht woord voor de Cloud wordt bijgewerkt de volgende keer dat u het wacht woord in de on-premises omgeving wijzigt.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Als er gesynchroniseerde gebruikers zijn die alleen communiceren met Azure AD Integrated Services en moeten voldoen aan het beleid voor het verlopen van wacht woorden, kunt u ervoor zorgen dat ze voldoen aan het verloop beleid van uw Azure AD-wacht woord door de functie *EnforceCloudPasswordPolicyForPasswordSyncedUsers* in te scha kelen.

Wanneer *EnforceCloudPasswordPolicyForPasswordSyncedUsers* is uitgeschakeld (dit is de standaard instelling), stelt Azure AD Connect het kenmerk PasswordPolicies van gesynchroniseerde gebruikers in op "DisablePasswordExpiration". Dit gebeurt telkens wanneer het wacht woord van een gebruiker wordt gesynchroniseerd en geeft Azure AD het verloop beleid voor Cloud wachtwoord voor die gebruiker te negeren. U kunt de waarde van het kenmerk controleren met behulp van de Azure AD Power shell-module met de volgende opdracht:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Als u de functie EnforceCloudPasswordPolicyForPasswordSyncedUsers wilt inschakelen, voert u de volgende opdracht uit met behulp van de MSOnline Power shell-module, zoals hieronder wordt weer gegeven. U moet ja typen voor de para meter Enable zoals hieronder wordt weer gegeven:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Als deze functie is ingeschakeld, gaat Azure AD niet naar elke gesynchroniseerde gebruiker om de `DisablePasswordExpiration` waarde uit het kenmerk PasswordPolicies te verwijderen. In plaats daarvan `DisablePasswordExpiration` wordt de waarde uit PasswordPolicies verwijderd tijdens de volgende wachtwoord hash-synchronisatie voor elke gebruiker, bij de volgende wachtwoord wijziging in on-premises AD.

Het is raadzaam om EnforceCloudPasswordPolicyForPasswordSyncedUsers in te scha kelen voordat u wachtwoord-hash-synchronisatie inschakelt, zodat de eerste synchronisatie van wacht woord-hashes de waarde niet toevoegt `DisablePasswordExpiration` aan het kenmerk PasswordPolicies voor de gebruikers.

Het standaard wachtwoord beleid voor Azure AD vereist dat gebruikers elke 90 dagen hun wacht woord wijzigen. Als uw beleid in AD ook 90 dagen is, moeten de twee beleids regels overeenkomen. Als het AD-beleid echter niet 90 dagen is, kunt u het Azure AD-wachtwoord beleid bijwerken zodat dit overeenkomt met de Set-MsolPasswordPolicy Power shell-opdracht.

Azure AD biedt ondersteuning voor een afzonderlijk beleid voor wachtwoord verloop per geregistreerd domein.

Voor behoud: als er gesynchroniseerde accounts zijn die niet-verlopende wacht woorden moeten hebben in azure AD, moet u de waarde expliciet toevoegen `DisablePasswordExpiration` aan het kenmerk PasswordPolicies van het gebruikers object in azure AD.  U kunt dit doen door de volgende opdracht uit te voeren.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> De Set-MsolPasswordPolicy Power shell-opdracht werkt niet op federatieve domeinen. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Tijdelijke wacht woorden synchroniseren en wacht woord wijzigen bij volgende aanmelding afdwingen

Het is gebruikelijk dat een gebruiker het wacht woord tijdens de eerste aanmelding wijzigt, met name nadat een beheerders wachtwoord opnieuw is ingesteld.  Het is vaak bekend als het instellen van een ' tijdelijk ' wacht woord en wordt uitgevoerd door de vlag ' gebruiker moet wacht woord bij volgende aanmelding wijzigen ' op een gebruikers object in Active Directory (AD) in te scha kelen.
  
De functionaliteit van het tijdelijke wacht woord helpt ervoor te zorgen dat de overdracht van eigendom van de referentie wordt voltooid bij het eerste gebruik, om zo de tijd te beperken dat meer dan één persoon kennis van die referentie heeft.

Ter ondersteuning van tijdelijke wacht woorden in azure AD voor gesynchroniseerde gebruikers, kunt u de functie *ForcePasswordChangeOnLogOn* inschakelen door de volgende opdracht uit te voeren op uw Azure AD Connect-server:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Als u het wacht woord voor de volgende aanmelding wilt wijzigen, moet u het wacht woord op hetzelfde moment wijzigen.  Azure AD Connect wordt de vlag voor het wijzigen van het wacht woord niet automatisch opgehaald. het is een aanvulling op de gedetecteerde wachtwoord wijziging die optreedt tijdens de wachtwoord-hash-synchronisatie.

> [!CAUTION]
> U moet deze functie alleen gebruiken wanneer SSPR en wacht woord terugschrijven zijn ingeschakeld op de Tenant.  Dit betekent dat als een gebruiker het wacht woord wijzigt via SSPR, deze wordt gesynchroniseerd met Active Directory.

#### <a name="account-expiration"></a>Account verloop tijd

Als uw organisatie gebruikmaakt van het kenmerk accountExpires als onderdeel van het beheer van gebruikers accounts, wordt dit kenmerk niet gesynchroniseerd met Azure AD. Als gevolg hiervan is een verlopen Active Directory-account in een omgeving geconfigureerd voor het synchroniseren van wachtwoord-hashes nog steeds actief in azure AD. Het is raadzaam dat als het account is verlopen, een werk stroom actie een Power shell-script moet activeren waarmee het Azure AD-account van de gebruiker wordt uitgeschakeld (gebruik de cmdlet [set-AzureADUser](/powershell/module/azuread/set-azureaduser) ). Als het account is ingeschakeld, moet de Azure AD-instantie daarentegen zijn ingeschakeld.

### <a name="overwrite-synchronized-passwords"></a>Gesynchroniseerde wacht woorden overschrijven

Een beheerder kan uw wacht woord hand matig opnieuw instellen met behulp van Windows Power shell.

In dit geval overschrijft het nieuwe wacht woord uw gesynchroniseerde wacht woord en worden alle wachtwoord beleidsregels die in de Cloud zijn gedefinieerd, toegepast op het nieuwe wacht woord.

Als u uw on-premises wacht woord opnieuw wijzigt, wordt het nieuwe wacht woord gesynchroniseerd met de Cloud en wordt het hand matig bijgewerkte wacht woord overschreven.

De synchronisatie van een wacht woord heeft geen invloed op de Azure-gebruiker die is aangemeld. De huidige Cloud service sessie wordt niet onmiddellijk beïnvloed door een gesynchroniseerde wachtwoord wijziging die optreedt wanneer u bent aangemeld bij een Cloud service. KMSI breidt de duur van dit verschil uit. Wanneer u de Cloud service opnieuw moet verifiëren, moet u uw nieuwe wacht woord opgeven.

### <a name="additional-advantages"></a>Aanvullende voor delen

- Over het algemeen is wachtwoord hash-synchronisatie eenvoudiger te implementeren dan een Federation-service. Er zijn geen extra servers nodig en elimineert de afhankelijkheid van een Maxi maal beschik bare Federation service om gebruikers te verifiëren.
- Wachtwoord hash-synchronisatie kan ook naast Federatie worden ingeschakeld. Het kan worden gebruikt als terugval als uw Federation service een storing ondervindt.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Wacht woord-hash-synchronisatie proces voor Azure AD Domain Services

Als u Azure AD Domain Services gebruikt om verouderde verificatie te bieden voor toepassingen en services waarvoor Kerberos, LDAP of NTLM moet worden gebruikt, maken sommige extra processen deel uit van de synchronisatie stroom voor wacht woord-hashes. Azure AD Connect gebruikt het volgende proces om wacht woord-hashes te synchroniseren met Azure AD voor gebruik in Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect moet alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Het installeren van Azure AD Connect in een beheerd Azure AD DS-domein om objecten weer naar Azure AD te synchroniseren, wordt niet ondersteund.
>
> Azure AD Connect synchroniseert alleen verouderde wachtwoord-hashes wanneer u Azure AD DS voor uw Azure AD-Tenant inschakelt. De volgende stappen worden niet gebruikt als u alleen Azure AD Connect gebruikt om een on-premises AD DS omgeving te synchroniseren met Azure AD.
>
> Als uw verouderde toepassingen geen gebruikmaken van NTLM-verificatie of LDAP-eenvoudige bindingen, raden we u aan NTLM-wachtwoord hash-synchronisatie voor Azure AD DS uit te scha kelen. Zie voor meer informatie [zwak coderings suites en hash-synchronisatie van NTLM-referenties uitschakelen](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect haalt de open bare sleutel op voor het exemplaar van de Tenant van Azure AD Domain Services.
1. Wanneer een gebruiker het wacht woord wijzigt, slaat de on-premises domein controller het resultaat van het wijzigen van het wacht woord (hashes) op in twee kenmerken:
    * *unicodePwd* voor de NTLM-wachtwoord-hash.
    * *supplementalCredentials* voor de Kerberos-wachtwoord-hash.
1. Azure AD Connect detecteert wachtwoord wijzigingen via het Directory replicatie kanaal (kenmerk wijzigingen die moeten worden gerepliceerd naar andere domein controllers).
1. Azure AD Connect voert de volgende stappen uit voor elke gebruiker waarvan het wacht woord is gewijzigd:
    * Hiermee wordt een wille keurige AES 256-bits symmetrische sleutel gegenereerd.
    * Hiermee wordt een wille keurige initialisatie vector gegenereerd die nodig is voor de eerste afronding van versleuteling.
    * Extraheert Kerberos-wachtwoord-hashes uit de *supplementalCredentials* -kenmerken.
    * Hiermee wordt de Azure AD Domain Services instelling beveiligings configuratie *SyncNtlmPasswords* gecontroleerd.
        * Als deze instelling is uitgeschakeld, wordt een wille keurige NTLM-hash met een hoge entropie gegenereerd (anders dan het wacht woord van de gebruiker). Deze hash wordt vervolgens gecombineerd met de exacte Kerberos-wachtwoord-hashes van het kenmerk *supplementalCrendetials* in één gegevens structuur.
        * Bij inschakeling wordt de waarde van het kenmerk *unicodePwd* gecombineerd met het geëxtraheerde Kerberos-wacht woord hashes van het kenmerk *supplementalCredentials* in één gegevens structuur.
    * Hiermee versleutelt u de afzonderlijke gegevens structuur met de AES symmetrische sleutel.
    * Hiermee versleutelt u de AES symmetrische sleutel met de open bare sleutel van de Tenant Azure AD Domain Services.
1. Azure AD Connect verzendt de versleutelde AES symmetrische sleutel, de versleutelde gegevens structuur met de wacht woord-hashes en de initialisatie vector naar Azure AD.
1. In azure AD worden de versleutelde AES symmetrische sleutel, de versleutelde gegevens structuur en de initialisatie vector voor de gebruiker opgeslagen.
1. Azure AD duwt de versleutelde AES symmetrische sleutel, de versleutelde gegevens structuur en de initialisatie vector met behulp van een intern synchronisatie mechanisme via een versleutelde HTTP-sessie tot Azure AD Domain Services.
1. Azure AD Domain Services haalt de persoonlijke sleutel voor het exemplaar van de Tenant op uit de Azure-sleutel kluis.
1. Azure AD Domain Services voert de volgende stappen uit voor elke versleutelde set gegevens (die de wachtwoord wijziging van één gebruiker vertegenwoordigen):
    * Maakt gebruik van de persoonlijke sleutel voor het ontsleutelen van de AES symmetrische sleutel.
    * Maakt gebruik van de AES symmetrische sleutel met de initialisatie vector voor het ontsleutelen van de versleutelde gegevens structuur die de wacht woord-hashes bevat.
    * Schrijft de Kerberos-wachtwoord hashes die worden ontvangen naar de Azure AD Domain Services domein controller. De hashes worden opgeslagen in het kenmerk *supplementalCredentials* van het gebruikers object dat is versleuteld met de open bare sleutel van de Azure AD Domain Services domein controller.
    * Azure AD Domain Services schrijft de NTLM-wachtwoord-hash die is ontvangen naar de Azure AD Domain Services-domein controller. De hash wordt opgeslagen in het kenmerk *unicodePwd* van het gebruikers object dat is versleuteld met de open bare sleutel van de Azure AD Domain Services domein controller.

## <a name="enable-password-hash-synchronization"></a>Wachtwoord-hashsynchronisatie inschakelen

>[!IMPORTANT]
>Als u migreert van AD FS (of andere Federatie technologieën) naar de synchronisatie van wacht woord-hashes, wordt u ten zeerste aangeraden onze gedetailleerde implementatie handleiding te volgen die u [hier](https://aka.ms/adfstophsdpdownload)hebt gepubliceerd.

Wanneer u Azure AD Connect installeert met de optie **snelle instellingen** , wordt de synchronisatie van wacht woord-hash automatisch ingeschakeld. Zie [aan de slag met Azure AD Connect met behulp van snelle instellingen](how-to-connect-install-express.md)voor meer informatie.

Als u aangepaste instellingen gebruikt wanneer u Azure AD Connect installeert, is wachtwoord hash-synchronisatie beschikbaar op de aanmeldings pagina van de gebruiker. Zie [aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)voor meer informatie.

![Wachtwoord-hashsynchronisatie inschakelen](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Wachtwoord hash-synchronisatie en FIPS
Als uw server is vergrendeld volgens de Federal Information Processing Standard (FIPS), wordt MD5 uitgeschakeld.

**Voer de volgende stappen uit om MD5 in te scha kelen voor wachtwoord hash-synchronisatie:**

1. Ga naar%programfiles%\Azure AD Sync\Bin.
2. Open miiserver.exe.config.
3. Ga naar het knoop punt configuratie/runtime aan het einde van het bestand.
4. Voeg het volgende knoop punt toe: `<enforceFIPSPolicy enabled="false"/>`
5. Sla uw wijzigingen op.

Ter referentie is dit fragment er als volgt uit te zien:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Zie [Azure AD Password Hash Sync, Encryption en FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)(Engelstalig) voor meer informatie over beveiliging en FIPS.

## <a name="troubleshoot-password-hash-synchronization"></a>Problemen met wachtwoord synchronisatie oplossen
Zie [problemen met wachtwoord synchronisatie oplossen](tshoot-connect-password-hash-synchronization.md)als u problemen ondervindt met het synchroniseren van wachtwoord-hashes.

## <a name="next-steps"></a>Volgende stappen
* [Azure AD Connect synchronisatie: synchronisatie opties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
* [Een stap-voor-stap implementatie plan voor het migreren van ADFS naar wachtwoord-hash-synchronisatie](https://aka.ms/authenticationDeploymentPlan)
