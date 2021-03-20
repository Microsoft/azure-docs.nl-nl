---
title: Problemen oplossen met behulp van de dsregcmd-opdracht-Azure Active Directory
description: De uitvoer van dsregcmd gebruiken om inzicht te krijgen in de status van apparaten in azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 614b3f927dc22bc534168f8fe95cc2f97031b621
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725520"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Problemen met apparaten oplossen met behulp van de dsregcmd-opdracht

Het hulp programma dsregcmd/status moet worden uitgevoerd als een domein gebruikers account.

## <a name="device-state"></a>Apparaatstatus

In deze sectie vindt u de para meters voor de status van de apparaten. In de volgende tabel ziet u de criteria voor het apparaat in verschillende statussen voor samen voegen.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Apparaatstatus |
| ---   | ---   | ---   | ---   |
| JA | NO | NO | Toegevoegd aan Azure AD |
| NO | NO | JA | Domein toegevoegd |
| JA | NO | JA | Lid van hybride AD |
| NO | JA | JA | On-premises DRS toegevoegd |

> [!NOTE]
> De status Workplace Join (geregistreerd bij Azure AD) wordt weer gegeven in de sectie gebruikers status

- **AzureAdJoined:** -ingesteld op Ja als het apparaat is gekoppeld aan Azure AD. ' Nee ' anders.
- **EnterpriseJoined:** -ingesteld op Ja als het apparaat is gekoppeld aan een on-premises drs. Een apparaat kan niet zowel EnterpriseJoined als AzureAdJoined zijn.
- **DomainJoined:** -ingesteld op Ja als het apparaat is gekoppeld aan een domein (AD).
- **Domein naam:** -Stel in op de naam van het domein als het apparaat is toegevoegd aan een domein.

### <a name="sample-device-state-output"></a>Voor beeld van status uitvoer van apparaat

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Apparaatgegevens

Alleen weer gegeven wanneer het apparaat is toegevoegd aan Azure AD of hybride Azure AD (niet geregistreerd voor Azure AD). In deze sectie vindt u informatie over apparaten die zijn opgeslagen in de Cloud.

- **DeviceID:** -unieke id van het apparaat in de Azure AD-Tenant
- **Vinger afdruk:** -vinger afdruk van het certificaat van apparaat 
- **DeviceCertificateValidity:** -geldigheid van het certificaat van het apparaat
- **KeyContainerId:** -ContainerId van de persoonlijke sleutel van het apparaat dat is gekoppeld aan het certificaat van het apparaat
- Sleutel **leveren:** -sleutel (hardware/software) die wordt gebruikt voor het opslaan van de persoonlijke apparaatgegevens.
- **TpmProtected:** -"ja" als de persoonlijke sleutel van het apparaat wordt opgeslagen in een hardware-TPM.

### <a name="sample-device-details-output"></a>Voor beeld van uitvoer van Details van apparaat

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Tenantgegevens

Alleen weer gegeven wanneer het apparaat is toegevoegd aan Azure AD of hybride Azure AD (niet geregistreerd voor Azure AD). In deze sectie vindt u de algemene Tenant Details wanneer een apparaat wordt toegevoegd aan Azure AD.

> [!NOTE]
> Als de MDM-Url's in deze sectie leeg zijn, geeft dit aan dat de MDM-service niet is geconfigureerd of dat de huidige gebruiker niet binnen het bereik van MDM-inschrijving valt. Controleer de mobiliteits instellingen in azure AD om uw MDM-configuratie te controleren.

> [!NOTE]
> Zelfs als u MDM-Url's ziet, betekent dit niet dat het apparaat wordt beheerd door een MDM. De informatie wordt weer gegeven als de Tenant de MDM-configuratie voor automatische inschrijving heeft, zelfs als het apparaat zelf niet wordt beheerd. 

### <a name="sample-tenant-details-output"></a>Uitvoer van voorbeeld gegevens van de Tenant

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Gebruikers status

In deze sectie vindt u de status van de verschillende kenmerken van de gebruiker die momenteel is aangemeld bij het apparaat.

> [!NOTE]
> De opdracht moet in een gebruikers context worden uitgevoerd om een geldige status op te halen.

- **NgcSet:** -stel deze waarde in op Ja als een Windows hello-sleutel is ingesteld voor de huidige aangemelde gebruiker.
- **NgcKeyId:** -id van de Windows hello-sleutel als er een is ingesteld voor de huidige aangemelde gebruiker.
- **Op:** -geeft aan of de Windows hello-sleutel opnieuw kan worden ingesteld door de gebruiker. 
- **Mogelijke waarden:** -DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive of unknown if error. 
- **WorkplaceJoined:** -stel deze waarde in op Ja als er geregistreerde Azure AD-accounts zijn toegevoegd aan het apparaat in de huidige Ntuser-context.
- **WamDefaultSet:** -stel deze waarde in op Ja als er een WAM-standaard webaccount is gemaakt voor de aangemelde gebruiker. Dit veld kan een fout weer geven als dsreg/status wordt uitgevoerd vanaf een opdracht prompt met verhoogde bevoegdheid. 
- **WamDefaultAuthority:** -ingesteld op ' organisaties ' voor Azure AD.
- **WamDefaultId:** -always " https://login.microsoft.com " voor Azure AD.
- **WamDefaultGUID:** -de GUID van de WAM-provider (Azure AD/Microsoft-account) voor het standaard WAM-webaccount. 

### <a name="sample-user-state-output"></a>Voorbeeld uitvoer van gebruikers status

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO-status

Deze sectie kan worden genegeerd voor geregistreerde Azure AD-apparaten.

> [!NOTE]
> De opdracht moet in een gebruikers context worden uitgevoerd om een geldige status voor die gebruiker op te halen.

- **AzureAdPrt:** -stel deze waarde in op Ja als er een PRT op het apparaat aanwezig is voor de aangemelde gebruiker.
- **AzureAdPrtUpdateTime:** -instellen op de tijd in UTC waarop de PRT voor het laatst is bijgewerkt.
- **AzureAdPrtExpiryTime:** -Stel in op de tijd in UTC wanneer de PRT verloopt als deze niet wordt vernieuwd.
- **AzureAdPrtAuthority:** -URL van Azure AD-instantie
- **EnterprisePrt:** -stel deze waarde in op Ja als het apparaat PRT heeft van on-premises ADFS. Voor hybride apparaten die zijn toegevoegd aan Azure AD, kan het apparaat PRT van Azure AD en on-premises AD tegelijk hebben. On-premises aangesloten apparaten hebben alleen een Enter prise-PRT.
- **EnterprisePrtUpdateTime:** -instellen op de tijd in UTC waarop de Enter prise PRT voor het laatst is bijgewerkt.
- **EnterprisePrtExpiryTime:** -Stel in op de tijd in UTC wanneer de PRT verloopt als deze niet wordt vernieuwd.
- **EnterprisePrtAuthority:** -URL van ADFS-instantie

### <a name="sample-sso-state-output"></a>Voor beeld van SSO-status uitvoer

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Diagnostische gegevens

### <a name="pre-join-diagnostics"></a>Diagnostische gegevens vooraf samen voegen

Deze sectie wordt alleen weer gegeven als het apparaat lid is van een domein en niet kan hybride Azure AD join.

In deze sectie worden verschillende tests uitgevoerd voor het opsporen van fouten bij samen voeging. Deze sectie bevat ook de details van de vorige (?). Deze informatie omvat de fout fase, de fout code, de server aanvraag-ID, de HTTP-status van het server antwoord, het fout bericht van de server reactie.

- **Gebruikers context:** -de context waarin de diagnostische gegevens worden uitgevoerd. Mogelijke waarden: systeem, niet-verhoogde gebruikers, verhoogde gebruiker. 

   > [!NOTE]
   > Omdat de daad werkelijke samen voeging wordt uitgevoerd in de systeem context, wordt het uitvoeren van de diagnose in systeem context het dichtst bij het werkelijke deelname scenario. Als u Diagnostische gegevens in de systeem context wilt uitvoeren, moet u de opdracht dsregcmd/status uitvoeren vanaf een opdracht prompt met verhoogde bevoegdheid.

- **Client tijd:** -de systeem tijd in UTC.
- **Ad-connectiviteits test:** -test voert een connectiviteits test naar de domein controller. De fout bij deze test resulteert waarschijnlijk in samenvoeg fouten in de controle fase.
- **Ad-configuratie test:** -test leest en controleert of het SCP-object juist is geconfigureerd in het on-PREMISes AD-forest. Fouten in deze test resulteren waarschijnlijk in samenvoeg fouten in de Discover-fase met de fout code 0x801c001d.
- **Detectie test voor Drs:** -test haalt de Drs-eind punten van het detectie-eind punt van de meta gegevens en voert een gebruikers realm-aanvraag uit. Fouten in deze test resulteren waarschijnlijk in samenvoeg fouten in de Discover-fase.
- **Drs-connectiviteits test:** -test voert een basis connectiviteits test uit op het Drs-eind punt.
- **Token Acquisition test:** -test probeert een Azure AD-verificatie token op te halen als de Tenant van de gebruiker federatief is. Fouten in deze test resulteren waarschijnlijk in samenvoeg fouten in de verificatie fase. Als verificatie is mislukt, wordt de synchronisatie poging uitgevoerd als terugval, tenzij terugval expliciet wordt uitgeschakeld met de onderstaande instellingen voor register sleutels.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Terugval voor synchroniseren-samen voegen:** -Stel in op ingeschakeld als de bovenstaande register sleutel, om te voor komen dat de terugval op het moment dat er verificatie fouten worden gevonden, niet aanwezig is. Deze optie is beschikbaar in Windows 10 1803 en hoger.
- **Vorige registratie:** -tijd waarop de vorige poging tot samen voeging heeft plaatsgevonden. Alleen mislukte deelname pogingen worden geregistreerd.
- **Fout fase:** -het stadium van de samen voeging waarin het is afgebroken. Mogelijke waarden zijn controle vooraf, Discover, auth, samen voegen.
- Fout code van **client:** -client fout geretourneerd (HRESULT).
- **Server error code:** -server fout nummer als een aanvraag is verzonden naar de server en de server een fout code heeft beantwoord. 
- **Server bericht:** -server bericht wordt samen met de fout code geretourneerd.
- **Https-status:** -de HTTP-status die is geretourneerd door de server.
- **Aanvraag-id:** -de client-aanvraag is verzonden naar de server. Dit is handig om te correleren met logboeken aan de server zijde.

### <a name="sample-pre-join-diagnostics-output"></a>Voor beeld van diagnostische gegevens voor voor deel samenvoeging

In het volgende voor beeld wordt de diagnostische test mislukt met een detectie fout.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

In het volgende voor beeld ziet u dat diagnostische tests worden door gegeven, maar de registratie poging is mislukt met een directory fout, die wordt verwacht voor een synchronisatie koppeling. Zodra de synchronisatie taak Azure AD Connect is voltooid, kan het apparaat aan de slag gaan.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnostische gegevens na deelname

In deze sectie wordt de uitvoer weer gegeven van Sanity controles die zijn uitgevoerd op een apparaat dat is gekoppeld aan de Cloud.

- **AadRecoveryEnabled:** -als Ja is ingesteld, zijn de sleutels die zijn opgeslagen op het apparaat niet bruikbaar en is het apparaat gemarkeerd voor herstel. De volgende keer dat u zich aanmeldt, wordt de herstel stroom geactiveerd en moet het apparaat opnieuw worden geregistreerd.
- **KeySignTest:** -als ' door gegeven ' de sleutels van het apparaat in goede staat zijn. Als KeySignTest mislukt, wordt het apparaat meestal gemarkeerd voor herstel. De volgende keer dat u zich aanmeldt, wordt de herstel stroom geactiveerd en moet het apparaat opnieuw worden geregistreerd. Voor Hybrid Azure AD gekoppelde apparaten is het herstel stil. Terwijl Azure AD of Azure AD is geregistreerd, wordt gevraagd om gebruikers verificatie om het apparaat te herstellen en opnieuw te registreren, indien nodig. **De KeySignTest vereist verhoogde bevoegdheden.**

#### <a name="sample-post-join-diagnostics-output"></a>Voor beeld van de uitvoer van diagnostische gegevens

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC-vereisten controle

In deze sectie worden de vereisten controles uitgevoerd voor het inrichten van Windows hello voor bedrijven (WHFB). 

> [!NOTE]
> U kunt de details van de NGC-vereisten controle niet zien in dsregcmd/status als de gebruiker WHFB al correct heeft geconfigureerd.

- **IsDeviceJoined:** -ingesteld op Ja als het apparaat is gekoppeld aan Azure AD.
- **IsUserAzureAD:** -stel deze waarde in op Ja als de aangemelde gebruiker aanwezig is in azure AD.
- **PolicyEnabled:** -stel deze waarde in op Ja als het WHFB-beleid is ingeschakeld op het apparaat.
- **PostLogonEnabled:** -ingesteld op Ja als WHFB-inschrijving systeem eigen wordt geactiveerd door het platform. Als deze is ingesteld op Nee, betekent dit dat de registratie van Windows hello voor bedrijven wordt geactiveerd door een aangepast mechanisme
- **DeviceEligible:** -stel deze waarde in op Ja als het apparaat voldoet aan de hardwarevereisten voor registratie bij WHFB.
- **SessionIsNotRemote:** -stel deze waarde in op Ja als de huidige gebruiker rechtstreeks is aangemeld bij het apparaat en niet op afstand.
- **CertEnrollment:** -specifiek voor WHFB certificaat vertrouwens implementatie, met vermelding van de certificerings instantie voor certificaat inschrijving voor WHFB. Ingesteld op "inschrijvings instantie" als de bron van WHFB-beleid is groepsbeleid, "Mobile Device Management" als de bron MDM is. ' geen ' anders
- **AdfsRefreshToken:** -specifiek voor WHFB-implementatie van certificaat vertrouwen. Alleen aanwezig als CertEnrollment de registratie-instantie is. Hiermee wordt aangegeven of het apparaat een Enter prise-PRT heeft voor de gebruiker.
- **AdfsRaIsReady:** -specifiek voor WHFB-implementatie van certificaat vertrouwen.  Alleen aanwezig als CertEnrollment de registratie-instantie is. Ingesteld op Ja als ADFS is aangegeven in de detectie-meta gegevens die door WHFB worden ondersteund *en* als de aanmeldings certificaat sjabloon beschikbaar is.
- **LogonCertTemplateReady:** -specifiek voor WHFB-implementatie van certificaat vertrouwen. Alleen aanwezig als CertEnrollment de registratie-instantie is. Ingesteld op Ja als de status van de aanmeldings certificaat sjabloon geldig is en u helpt bij het oplossen van problemen met ADFS RA.
- **PreReqResult:** -levert het resultaat van de evaluatie van de WHFB-vereisten. Ingesteld op ' wordt ingericht ' als de WHFB-inschrijving wordt gestart als een taak na het aanmelden wanneer de gebruiker de volgende keer aanmeldt.

### <a name="sample-ngc-prerequisite-check-output"></a>Voorbeeld uitvoer van NGC-vereisten controle

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Volgende stappen

Zie [Veelgestelde vragen over Apparaatbeheer](faq.md) voor meer informatie.
