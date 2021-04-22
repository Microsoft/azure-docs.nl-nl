---
title: Wachtwoord-hashsynchronisatie implementeren met Azure AD Connect synchronisatie | Microsoft Docs
description: Bevat informatie over hoe wachtwoord-hashsynchronisatie werkt en hoe u deze in kunt stellen.
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
ms.openlocfilehash: ee22ba3816e667bc58247fa81142e54587124fd6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865293"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Wachtwoord-hashsynchronisatie implementeren met Azure AD Connect-synchronisatie
Dit artikel bevat informatie die u nodig hebt om uw gebruikerswachtwoorden van een on-premises Active Directory-exemplaar te synchroniseren met een cloud-Azure Active Directory -exemplaar (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Hoe synchronisatie van wachtwoord-hashes werkt
De Active Directory-domeinservice slaat wachtwoorden op in de vorm van een hash-waardeweergave, van het werkelijke gebruikerswachtwoord. Een hash-waarde is een resultaat van een een way mathematische functie (het *hash-algoritme*). Er bestaat geen methode het resultaat van een eenzijdige functie terug te draaien naar de versie in tekst zonder opmaak van een wachtwoord. 

Als u uw wachtwoord wilt synchroniseren, Azure AD Connect synchronisatie uw wachtwoordhash uit het on-premises Active Directory extraheren. Extra beveiligingsverwerking wordt toegepast op de wachtwoordhash voordat deze wordt gesynchroniseerd met Azure Active Directory verificatieservice. Wachtwoorden worden gesynchroniseerd per gebruiker en in chronologische volgorde.

De werkelijke gegevensstroom van het wachtwoord-hashsynchronisatieproces is vergelijkbaar met de synchronisatie van gebruikersgegevens. Wachtwoorden worden echter vaker gesynchroniseerd dan het standaardvenster voor adreslijstsynchronisatie voor andere kenmerken. Het synchronisatieproces voor wachtwoordhash wordt elke 2 minuten uitgevoerd. U kunt de frequentie van dit proces niet wijzigen. Wanneer u een wachtwoord synchroniseert, wordt het bestaande cloudwachtwoord overschreven.

De eerste keer dat u de functie voor wachtwoord-hashsynchronisatie inschakelen, wordt een initiële synchronisatie van de wachtwoorden van alle gebruikers binnen het bereik uitgevoerd. U kunt niet expliciet een subset gebruikerswachtwoorden definiëren die u wilt synchroniseren. Als er echter meerdere connectors zijn, is het mogelijk om wachtwoordhashsynchronisatie uit te schakelen voor sommige connectors, maar niet voor andere connectors met behulp van de cmdlet [Set-ADSyncAADPasswordSyncConfiguration.](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md)

Wanneer u een on-premises wachtwoord wijzigt, wordt het bijgewerkte wachtwoord gesynchroniseerd, meestal binnen enkele minuten.
Met de functie voor wachtwoord-hashsynchronisatie worden mislukte synchronisatiepogingen automatisch opnieuw geprobeerd. Als er een fout optreedt tijdens een poging om een wachtwoord te synchroniseren, wordt er een fout geregistreerd in uw gebeurtenisviewer.

De synchronisatie van een wachtwoord heeft geen invloed op de gebruiker die momenteel is aangemeld.
Uw huidige cloudservicesessie wordt niet onmiddellijk beïnvloed door een gesynchroniseerde wachtwoordwijziging die plaatsvindt wanneer u bent aangemeld bij een cloudservice. Wanneer de cloudservice echter vereist dat u zich opnieuw verifieert, moet u uw nieuwe wachtwoord opgeven.

Een gebruiker moet de referenties van het bedrijf een tweede keer invoeren om zich te verifiëren bij Azure AD, ongeacht of deze zijn aangemeld bij het bedrijfsnetwerk. Dit patroon kan echter worden geminimaliseerd als de gebruiker het selectievakje Aangemeld blijven (KMSI) incheckt bij het aanmelden. Met deze selectie stelt u een sessiecookie in die verificatie 180 dagen overzeilt. KMSI-gedrag kan worden ingeschakeld of uitgeschakeld door de Azure AD-beheerder. Bovendien kunt u het aantal wachtwoordprompts verminderen door Naadloze [eenmalige](how-to-connect-sso.md)aanmelding in te stellen, waarmee gebruikers automatisch worden aangegeven wanneer ze op hun bedrijfsapparaten zijn verbonden met uw bedrijfsnetwerk.

> [!NOTE]
> Wachtwoordsynchronisatie wordt alleen ondersteund voor het objecttype gebruiker in Active Directory. Het wordt niet ondersteund voor het objecttype iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Gedetailleerde beschrijving van hoe wachtwoord-hashsynchronisatie werkt

In de volgende sectie wordt uitgebreid beschreven hoe wachtwoord-hashsynchronisatie werkt tussen Active Directory en Azure AD.

![Gedetailleerde wachtwoordstroom](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Om de twee minuten vraagt de synchronisatieagent voor wachtwoordhashes op de AD Connect-server opgeslagen wachtwoordhashes (het unicodePwd-kenmerk) aan bij een dc.  Deze aanvraag vindt plaats via het standaard [MS-DRSR-replicatieprotocol](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) dat wordt gebruikt om gegevens tussen DC's te synchroniseren. Het serviceaccount moet Directorywijzigingen repliceren en Directorywijzigingen repliceren Alle AD-machtigingen hebben (standaard verleend bij installatie) om de wachtwoordhashes te verkrijgen.
2. Voordat de dc wordt verzonden, versleutelt de MD4-wachtwoordhash met behulp van een sleutel die een [MD5-hash](https://www.rfc-editor.org/rfc/rfc1321.txt) is van de RPC-sessiesleutel en een salt. Vervolgens wordt het resultaat via RPC naar de synchronisatieagent voor wachtwoordhashs verzendt. De DC geeft ook de salt door aan de synchronisatieagent met behulp van het DC-replicatieprotocol, zodat de agent de envelop kan ontsleutelen.
3. Nadat de agent voor wachtwoordhashsynchronisatie de versleutelde envelop heeft, gebruikt deze [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) en de salt om een sleutel te genereren om de ontvangen gegevens weer te ontsleutelen naar de oorspronkelijke MD4-indeling. De agent voor wachtwoordhashsynchronisatie heeft nooit toegang tot het wachtwoord voor niet-gewete tekst. Het gebruik van MD5 door de agent voor wachtwoordhashsynchronisatie is uitsluitend voor replicatieprotocolcompatibiliteit met de dc en wordt alleen on-premises gebruikt tussen de DC en de synchronisatieagent voor wachtwoordhashs.
4. De agent voor wachtwoord-hashsynchronisatie breidt de binaire wachtwoordhash van 16 bytes uit naar 64 bytes door eerst de hash te converteren naar een hexadecimale tekenreeks van 32 bytes, en deze tekenreeks vervolgens weer te converteren naar binair met UTF-16-codering.
5. De agent voor wachtwoordhashsynchronisatie voegt een salt per gebruiker, bestaande uit een salt van 10 byte lengte, toe aan het binaire bestand van 64 byte om de oorspronkelijke hash verder te beveiligen.
6. De agent voor wachtwoordhashsynchronisatie combineert vervolgens de MD4-hash plus de salt per gebruiker en invoert deze in de [PBKDF2-functie.](https://www.ietf.org/rfc/rfc2898.txt) Er worden 1000 iteraties van het [hash-algoritme met HMAC-SHA256-sleutels](/dotnet/api/system.security.cryptography.hmacsha256) gebruikt. 
7. De agent voor wachtwoordhashsynchronisatie neemt de resulterende hash van 32 byte, samenvoegen zowel de salt per gebruiker als het aantal SHA256-iteraties naar de agent (voor gebruik door Azure AD) en verzendt vervolgens de tekenreeks van Azure AD Connect naar Azure AD via TLS.</br> 
8. Wanneer een gebruiker zich probeert aan te melden bij Azure AD en het wachtwoord invoert, wordt het wachtwoord uitgevoerd via hetzelfde MD4+salt+PBKDF2+HMAC-SHA256-proces. Als de resulterende hash overeenkomt met de hash die is opgeslagen in Azure AD, heeft de gebruiker het juiste wachtwoord ingevoerd en wordt deze geverifieerd.

> [!NOTE]
> De oorspronkelijke MD4-hash wordt niet verzonden naar Azure AD. In plaats daarvan wordt de SHA256-hash van de oorspronkelijke MD4-hash verzonden. Als gevolg hiervan, als de hash die is opgeslagen in Azure AD wordt verkregen, kan deze niet worden gebruikt in een on-premises pass-the-hash-aanval.

### <a name="security-considerations"></a>Beveiligingsoverwegingen

Bij het synchroniseren van wachtwoorden wordt de versie zonder tekst van uw wachtwoord niet blootgesteld aan de functie voor wachtwoord-hashsynchronisatie, met Azure AD of een van de bijbehorende services.

Gebruikersverificatie vindt plaats op basis van Azure AD in plaats van op basis van het eigen Active Directory-exemplaar van de organisatie. De SHA256-wachtwoordgegevens die zijn opgeslagen in Azure AD, een hash van de oorspronkelijke MD4-hash, zijn veiliger dan wat er in Active Directory is opgeslagen. Omdat deze SHA256-hash niet kan worden ontsleuteld, kan deze niet worden teruggebracht naar de Active Directory-omgeving van de organisatie en worden weergegeven als een geldig gebruikerswachtwoord bij een pass-the-hash-aanval.

### <a name="password-policy-considerations"></a>Overwegingen voor wachtwoordbeleid

Er zijn twee soorten wachtwoordbeleid die worden beïnvloed door het inschakelen van wachtwoord-hashsynchronisatie:

* Beleid voor wachtwoordcomplexiteit
* Beleid voor wachtwoordverloop

#### <a name="password-complexity-policy"></a>Beleid voor wachtwoordcomplexiteit

Wanneer wachtwoord-hashsynchronisatie is ingeschakeld, overschrijven het beleid voor wachtwoordcomplexiteit in uw on-premises Active Directory-exemplaar het complexiteitsbeleid in de cloud voor gesynchroniseerde gebruikers. U kunt alle geldige wachtwoorden van uw on-premises Active Directory gebruiken voor toegang tot Azure AD-services.

> [!NOTE]
> Wachtwoorden voor gebruikers die rechtstreeks in de cloud zijn gemaakt, zijn nog steeds onderworpen aan wachtwoordbeleid zoals gedefinieerd in de cloud.

#### <a name="password-expiration-policy"></a>Beleid voor wachtwoordverloop

Als een gebruiker binnen het bereik van wachtwoord-hashsynchronisatie valt, is het wachtwoord voor het cloudaccount standaard ingesteld op *Nooit verlopen.*

U kunt zich blijven aanmelden bij uw cloudservices met behulp van een gesynchroniseerd wachtwoord dat is verlopen in uw on-premises omgeving. Uw cloudwachtwoord wordt bijgewerkt wanneer u het wachtwoord de volgende keer wijzigt in de on-premises omgeving.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Als er gesynchroniseerde gebruikers zijn die alleen interactie hebben met geïntegreerde Azure AD-services en ook moeten voldoen aan een wachtwoordverloopbeleid, kunt u afdwingen dat ze voldoen aan het verloopbeleid voor Azure AD-wachtwoorden door de functie *EnforceCloudPasswordPolicyForPasswordSyncedUsers* in te stellen.

Wanneer *EnforceCloudPasswordPolicyForPasswordSyncedUsers* is uitgeschakeld (dit is de standaardinstelling), stelt Azure AD Connect het kenmerk PasswordPolicies van gesynchroniseerde gebruikers in op DisablePasswordExpiration. Dit gebeurt telkens als het wachtwoord van een gebruiker wordt gesynchroniseerd en geeft Azure AD de instructie om het verloopbeleid voor cloudwachtwoord voor die gebruiker te negeren. U kunt de waarde van het kenmerk controleren met behulp van de Azure AD PowerShell-module met de volgende opdracht:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Als u de functie EnforceCloudPasswordPolicyForPasswordSyncedUsers wilt inschakelen, moet u de volgende opdracht uitvoeren met behulp van de MSOnline PowerShell-module, zoals hieronder wordt weergegeven. U moet ja typen voor de parameter Inschakelen, zoals hieronder wordt weergegeven:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Als deze functie is ingeschakeld, gaat Azure AD niet naar elke gesynchroniseerde gebruiker om de waarde uit het `DisablePasswordExpiration` kenmerk PasswordPolicies te verwijderen. In plaats daarvan wordt de waarde verwijderd uit PasswordPolicies tijdens de volgende wachtwoordhashsynchronisatie voor elke gebruiker, bij de volgende wachtwoordwijziging `DisablePasswordExpiration` in on-premises AD.

U kunt het beste EnforceCloudPasswordPolicyForPasswordSyncedUsers inschakelen voordat u wachtwoordhashsynchronisatie inschakelen, zodat de eerste synchronisatie van wachtwoordhashes niet de waarde toevoegt aan het kenmerk PasswordPolicies voor de `DisablePasswordExpiration` gebruikers.

Het standaardwachtwoordbeleid van Azure AD vereist dat gebruikers hun wachtwoord elke 90 dagen wijzigen. Als uw beleid in AD ook 90 dagen is, moeten de twee beleidsregels overeenkomen. Als het AD-beleid echter niet 90 dagen is, kunt u het Azure AD-wachtwoordbeleid bijwerken zodat het overeen komt met behulp van de Set-MsolPasswordPolicy PowerShell-opdracht.

Azure AD ondersteunt een afzonderlijk beleid voor het verlopen van wachtwoorden per geregistreerd domein.

Waarschuwing: als er gesynchroniseerde accounts zijn die niet-verlopende wachtwoorden moeten hebben in Azure AD, moet u de waarde expliciet toevoegen aan het kenmerk PasswordPolicies van het gebruikersobject `DisablePasswordExpiration` in Azure AD.  U kunt dit doen door de volgende opdracht uit te voeren.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> De Set-MsolPasswordPolicy PowerShell-opdracht werkt niet in federatief domeinen. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Tijdelijke wachtwoorden synchroniseren en Wachtwoordwijziging forceren bij volgende aanmelding

Het is gebruikelijk om gebruikers te dwingen hun wachtwoord te wijzigen tijdens de eerste aanmelding, met name nadat het beheerderswachtwoord opnieuw is ingesteld.  Dit wordt meestal aangeduid als het instellen van een 'tijdelijk' wachtwoord en wordt voltooid door de vlag 'Gebruiker moet wachtwoord wijzigen bij volgende aanmelding' te controleren op een gebruikersobject in Active Directory (AD).
  
De tijdelijke wachtwoordfunctionaliteit zorgt ervoor dat de overdracht van het eigendom van de referentie bij het eerste gebruik wordt voltooid, om de tijdsduur te minimaliseren waarin meer dan één persoon kennis van die referentie heeft.

Ter ondersteuning van tijdelijke wachtwoorden in Azure AD voor gesynchroniseerde gebruikers, kunt u de functie *ForcePasswordChangeOnLogOn* inschakelen door de volgende opdracht uit te voeren op uw Azure AD Connect server:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Het afdwingen van een gebruiker om het wachtwoord bij de volgende aanmelding te wijzigen, vereist een wachtwoordwijziging op hetzelfde moment.  Azure AD Connect wordt de vlag voor het wijzigen van het wachtwoord niet zelf opgehaald; het is een aanvulling op de gedetecteerde wachtwoordwijziging die plaatsvindt tijdens het synchroniseren van wachtwoordhashs.

> [!CAUTION]
> U moet deze functie alleen gebruiken wanneer SSPR en Wachtwoord terugschrijven zijn ingeschakeld op de tenant.  Dit betekent dat als een gebruiker het wachtwoord via SSPR wijzigt, het wordt gesynchroniseerd met Active Directory.

#### <a name="account-expiration"></a>Verloopdatum van account

Als uw organisatie het kenmerk accountExpires gebruikt als onderdeel van gebruikersaccountbeheer, wordt dit kenmerk niet gesynchroniseerd met Azure AD. Als gevolg hiervan is een verlopen Active Directory-account in een omgeving die is geconfigureerd voor wachtwoord-hashsynchronisatie nog steeds actief in Azure AD. Als het account is verlopen, wordt door een werkstroomactie een PowerShell-script activeren dat het Azure AD-account van de gebruiker uit schakelt (gebruik de cmdlet [Set-AzureADUser).](/powershell/module/azuread/set-azureaduser) Als het account daarentegen is ingeschakeld, moet het Azure AD-exemplaar worden ingeschakeld.

### <a name="overwrite-synchronized-passwords"></a>Gesynchroniseerde wachtwoorden overschrijven

Een beheerder kan uw wachtwoord handmatig opnieuw instellen met behulp van Windows PowerShell.

In dit geval overschrijven de nieuwe wachtwoorden uw gesynchroniseerde wachtwoord en worden alle wachtwoordbeleidsregels die in de cloud zijn gedefinieerd, toegepast op het nieuwe wachtwoord.

Als u uw on-premises wachtwoord opnieuw wijzigt, wordt het nieuwe wachtwoord gesynchroniseerd met de cloud en wordt het handmatig bijgewerkte wachtwoord overschrijven.

De synchronisatie van een wachtwoord heeft geen invloed op de Azure-gebruiker die is aangemeld. Uw huidige cloudservicesessie wordt niet onmiddellijk beïnvloed door een gesynchroniseerde wachtwoordwijziging die optreedt wanneer u bent aangemeld bij een cloudservice. KMSI breidt de duur van dit verschil uit. Wanneer u voor de cloudservice opnieuw moet worden geverifieerd, moet u uw nieuwe wachtwoord opgeven.

### <a name="additional-advantages"></a>Aanvullende voordelen

- Over het algemeen is wachtwoord-hashsynchronisatie eenvoudiger te implementeren dan een federatieservice. Er zijn geen extra servers vereist en de afhankelijkheid van een federatieservice met hoge beschikbare toegang om gebruikers te verifiëren, is niet meer nodig.
- Wachtwoord-hashsynchronisatie kan naast federatie ook worden ingeschakeld. Deze kan worden gebruikt als terugval als uw federatieservice een storing ervaart.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Synchronisatieproces van wachtwoordhash voor Azure AD Domain Services

Als u Azure AD Domain Services gebruikt om verouderde verificatie te bieden voor toepassingen en services die Kerberos, LDAP of NTLM moeten gebruiken, maken sommige extra processen deel uit van de synchronisatiestroom voor wachtwoordhashes. Azure AD Connect gebruikt het volgende extra proces om wachtwoordhashes te synchroniseren met Azure AD voor gebruik in Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect moet alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Het installeren van Azure AD Connect in een beheerd Azure AD DS-domein om objecten weer naar Azure AD te synchroniseren, wordt niet ondersteund.
>
> Azure AD Connect worden verouderde wachtwoordhashes alleen gesynchroniseerd wanneer u Azure AD DS voor uw Azure AD-tenant inschakelen. De volgende stappen worden niet gebruikt als u alleen gebruik Azure AD Connect om een on-premises AD DS te synchroniseren met Azure AD.
>
> Als uw oudere toepassingen geen NTLM-verificatie of eenvoudige LDAP-bindingen gebruiken, wordt u aangeraden NTLM-wachtwoordhashsynchronisatie uit te schakelen voor Azure AD DS. Zie Zwakke coderingssuites en [NTLM-referentie-hashsynchronisatie uitschakelen voor meer informatie.](../../active-directory-domain-services/secure-your-domain.md)

1. Azure AD Connect haalt de openbare sleutel op voor het exemplaar van de tenant van Azure AD Domain Services.
1. Wanneer een gebruiker het wachtwoord wijzigt, slaat de on-premises domeincontroller het resultaat van de wachtwoordwijziging (hashes) op in twee kenmerken:
    * *unicodePwd* voor de NTLM-wachtwoordhash.
    * *supplementalCredentials* voor de Kerberos-wachtwoordhash.
1. Azure AD Connect detecteert wachtwoordwijzigingen via het directoryreplicatiekanaal (kenmerkwijzigingen die moeten worden gerepliceerd naar andere domeincontrollers).
1. Voor elke gebruiker waarvan het wachtwoord is gewijzigd, voert Azure AD Connect volgende stappen uit:
    * Genereert een willekeurige 256-bits symmetrische AES-sleutel.
    * Genereert een willekeurige initialisatievector die nodig is voor de eerste ronde van de versleuteling.
    * Extraheert Kerberos-wachtwoordhashes uit de *kenmerken supplementalCredentials.*
    * Controleert de Azure AD Domain Services de beveiligingsconfiguratie *SyncNtlmPasswords.*
        * Als deze instelling is uitgeschakeld, genereert een willekeurige NTLM-hash met hoge entropie (anders dan het wachtwoord van de gebruiker). Deze hash wordt vervolgens gecombineerd met de exacte Kerberos-wachtwoordhashes van het *kenmerk supplementalCrendetials* in één gegevensstructuur.
        * Als deze functie is ingeschakeld, combineert de waarde van het *kenmerk unicodePwd* met de geëxtraheerde Kerberos-wachtwoordhashes uit het kenmerk *supplementalCredentials* in één gegevensstructuur.
    * Versleutelt de enkelvoudige gegevensstructuur met behulp van de symmetrische AES-sleutel.
    * Versleutelt de symmetrische AES-sleutel met behulp van de Azure AD Domain Services openbare sleutel van de tenant.
1. Azure AD Connect verzendt de versleutelde symmetrische AES-sleutel, de versleutelde gegevensstructuur met de wachtwoordhashes en de initialisatievector naar Azure AD.
1. Azure AD slaat de versleutelde symmetrische AES-sleutel, de versleutelde gegevensstructuur en de initialisatievector voor de gebruiker op.
1. Azure AD pusht de versleutelde symmetrische AES-sleutel, de versleutelde gegevensstructuur en de initialisatievector met behulp van een intern synchronisatiemechanisme via een versleutelde HTTP-sessie naar Azure AD Domain Services.
1. Azure AD Domain Services haalt de persoonlijke sleutel voor het exemplaar van de tenant op uit Azure Key Vault.
1. Voor elke versleutelde gegevensset (die staat voor de wachtwoordwijziging van één gebruiker), voert Azure AD Domain Services volgende stappen uit:
    * Gebruikt de persoonlijke sleutel om de symmetrische AES-sleutel te ontsleutelen.
    * Maakt gebruik van de symmetrische AES-sleutel met de initialisatievector voor het ontsleutelen van de versleutelde gegevensstructuur die de wachtwoordhashes bevat.
    * Schrijft de Kerberos-wachtwoordhashes die het ontvangt naar Azure AD Domain Services domeincontroller. De hashes worden opgeslagen in het kenmerk *supplementalCredentials* van het gebruikersobject dat is versleuteld met de openbare sleutel van Azure AD Domain Services domeincontroller.
    * Azure AD Domain Services schrijft de NTLM-wachtwoordhash die het heeft ontvangen naar Azure AD Domain Services domeincontroller. De hash wordt opgeslagen in het *unicodePwd-kenmerk* van het gebruikersobject dat is versleuteld met de openbare Azure AD Domain Services van de domeincontroller.

## <a name="enable-password-hash-synchronization"></a>Wachtwoord-hashsynchronisatie inschakelen

>[!IMPORTANT]
>Als u migreert van AD FS (of andere federatietechnologieën) naar Wachtwoord-hashsynchronisatie, raden we u ten zeerste aan onze gedetailleerde implementatiehandleiding te volgen die hier is [gepubliceerd.](https://aka.ms/adfstophsdpdownload)

Wanneer u een Azure AD Connect met behulp van de **optie Snelle** instellingen, wordt synchronisatie van wachtwoordhash automatisch ingeschakeld. Zie Aan de slag met Azure AD Connect [express-instellingen voor meer informatie.](how-to-connect-install-express.md)

Als u aangepaste instellingen gebruikt bij het installeren van Azure AD Connect, is wachtwoordhashsynchronisatie beschikbaar op de aanmeldingspagina van de gebruiker. Zie Aangepaste installatie [van](how-to-connect-install-custom.md)de Azure AD Connect.

![Wachtwoord-hashsynchronisatie inschakelen](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Wachtwoord-hashsynchronisatie en FIPS
Als uw server is vergrendeld op basis van Federal Information Processing Standard (FIPS), wordt MD5 uitgeschakeld.

**Als u MD5 wilt inschakelen voor wachtwoord-hashsynchronisatie, moet u de volgende stappen uitvoeren:**

1. Ga naar %programfiles%\Microsoft Azure AD Sync\Bin.
2. Open miiserver.exe.config.
3. Ga naar het knooppunt configuration/runtime aan het einde van het bestand.
4. Voeg het volgende knooppunt toe: `<enforceFIPSPolicy enabled="false"/>`
5. Sla uw wijzigingen op.

Dit fragment moet er als referentie uitzien:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Zie Synchronisatie, versleuteling en FIPS-naleving van [Azure AD-wachtwoordhashes](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)voor meer informatie over beveiliging en FIPS.

## <a name="troubleshoot-password-hash-synchronization"></a>Problemen met wachtwoord-hashsynchronisatie oplossen
Zie Problemen met wachtwoord-hashsynchronisatie oplossen als u problemen hebt met [wachtwoord-hashsynchronisatie.](tshoot-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>Volgende stappen
* [Azure AD Connect synchronisatie: Synchronisatieopties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
* [Een stapsgewijs implementatieplan voor migratie van ADFS naar wachtwoord-hashsynchronisatie](https://aka.ms/authenticationDeploymentPlan)
