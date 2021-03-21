---
title: Problemen met hybride Azure Active Directory-gekoppelde apparaten oplossen
description: Problemen met hybride Azure Active Directory oplossen die zijn toegevoegd aan Windows 10-en Windows Server 2016-apparaten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 1eb4194354a07d5b580f07cfe5962785a4e100bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578018"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Problemen met hybride Azure Active Directory-gekoppelde apparaten oplossen

De inhoud van dit artikel is van toepassing op apparaten met Windows 10 of Windows Server 2016.

Zie voor andere Windows-clients het artikel [problemen oplossen met hybride Azure Active Directory gekoppelde apparaten](troubleshoot-hybrid-join-windows-legacy.md)op hetzelfde niveau.

In dit artikel wordt ervan uitgegaan dat u [hybride Azure Active Directory gekoppelde apparaten hebt geconfigureerd](hybrid-azuread-join-plan.md) om de volgende scenario's te ondersteunen:

- Voorwaardelijke toegang op basis van het apparaat
- [Zwervende instellingen voor ondernemingen](./enterprise-state-roaming-overview.md)
- [Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

Dit document bevat richt lijnen voor probleem oplossing om mogelijke problemen op te lossen.

Voor Windows 10 en Windows Server 2016, hybride Azure Active Directory-koppeling, worden de update voor Windows 10 november 2015 en hoger ondersteund.

## <a name="troubleshoot-join-failures"></a>Fouten bij samen voegen oplossen

### <a name="step-1-retrieve-the-join-status"></a>Stap 1: de status van de samen voeging ophalen

**De status van de samen voeging ophalen:**

1. Open een opdracht prompt als beheerder
2. Typ `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Stap 2: de status van de deelname evalueren

Controleer de volgende velden en zorg ervoor dat ze de verwachte waarden hebben:

#### <a name="domainjoined--yes"></a>DomainJoined: Ja

Dit veld geeft aan of het apparaat is gekoppeld aan een on-premises Active Directory of niet. Als de waarde **Nee** is, kan het apparaat geen hybride Azure AD-deelname uitvoeren.

#### <a name="workplacejoined--no"></a>WorkplaceJoined: Nee

Dit veld geeft aan of het apparaat is geregistreerd bij Azure AD als persoonlijk apparaat (gemarkeerd als *toegevoegd aan werk plek*). Deze waarde mag **niet** worden toegevoegd aan een computer die lid is van een domein en ook hybride Azure AD is toegevoegd. Als de waarde **Ja** is, is er een werk-of school account toegevoegd vóór het volt ooien van de deelname van de hybride Azure AD. In dit geval wordt het account genegeerd wanneer u Windows 10 versie 1607 of hoger gebruikt.

#### <a name="azureadjoined--yes"></a>AzureAdJoined: Ja

Dit veld geeft aan of het apparaat is gekoppeld. De waarde is **Ja** als het apparaat een gekoppeld Azure AD-apparaat of een hybride Azure AD-apparaat is.
Als de waarde **Nee** is, is de koppeling naar Azure AD nog niet voltooid.

Ga verder met de volgende stappen voor meer informatie over het oplossen van problemen.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Stap 3: de fase waarin de samen voeging is mislukt en de error code vinden

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 en hoger

Zoek naar de subsectie ' voorafgaande registratie ' in de sectie ' Diagnostische gegevens ' van de uitvoer van de samenvoeg status. Deze sectie wordt alleen weer gegeven als het apparaat lid is van een domein en niet kan hybride Azure AD join.
In het veld ' fout fase ' wordt de fase van de fout bij het samen voegen aangegeven, terwijl ' client code-error ' de fout code van de koppelings bewerking aanduidt.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Oudere versies van Windows 10

Gebruik Logboeken Logboeken om de fase en fout code voor het samen voegen van fouten te vinden.

1. Open de gebeurtenis logboeken voor **registratie van gebruikers apparaten** in Logboeken. Deze bevindt zich onder **toepassingen en services**  >  registratie van **micro soft**  >  **Windows**-  >  **gebruikers apparaten**
2. Zoek naar gebeurtenissen met de volgende eventIDs 304, 305, 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Scherm opname van de logboeken. Er wordt een gebeurtenis met I D 304 geselecteerd en de bijbehorende informatie wordt weer gegeven, waarbij de fout code en fase zijn gemarkeerd." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Scherm opname van de logboeken. Een gebeurtenis met I D 305 is zichtbaar en de informatie wordt weer gegeven, waarbij de fout code is gemarkeerd." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Stap 4: controleren op mogelijke oorzaken en oplossingen in de onderstaande lijsten

#### <a name="pre-check-phase"></a>Controle fase

Mogelijke oorzaken voor fout:

- Het apparaat heeft geen regel voor het gezichts vermogen van de domein controller.
   - Het apparaat moet zich in het interne netwerk van de organisatie of op VPN met netwerk regel bezien op een on-premises Active Directory (AD) domein controller.

#### <a name="discover-phase"></a>Detectie fase

Mogelijke oorzaken voor fout:

- SCP-object (Service Connection Point) is onjuist geconfigureerd/kan het SCP-object niet lezen van de domein controller.
   - Een geldig SCP-object is vereist in het AD-forest waartoe het apparaat behoort, dat verwijst naar een geverifieerde domein naam in azure AD.
   - Meer informatie vindt u in de sectie [een service verbindings punt configureren](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Kan geen verbinding maken met de meta gegevens van de detectie en ophalen van het detectie-eind punt.
   - Het apparaat moet `https://enterpriseregistration.windows.net` in de systeem context toegang hebben om de registratie-en autorisatie-eind punten te kunnen detecteren.
   - Als de on-premises omgeving een uitgaande proxy vereist, moet de IT-beheerder ervoor zorgen dat het computer account van het apparaat kan detecteren en op de achtergrond kan worden geverifieerd bij de uitgaande proxy.
- Kan geen verbinding maken met het gebruikers realm-eind punt en voert realm-detectie uit. (Alleen Windows 10 versie 1809 en hoger)
   - Het apparaat moet `https://login.microsoftonline.com` in de systeem context toegang hebben om realm-detectie uit te voeren voor het geverifieerde domein en het domein type (beheerd/federatief) te bepalen.
   - Als de on-premises omgeving een uitgaande proxy vereist, moet de IT-beheerder ervoor zorgen dat de systeem context op het apparaat kan detecteren en op de achtergrond kan worden geverifieerd bij de uitgaande proxy.

**Algemene fout codes:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Reden: kan het SCP-object niet lezen en ophalen van de gegevens van de Azure AD-Tenant.
   - Oplossing: Raadpleeg de sectie [een service verbindings punt configureren](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Reden: algemene detectie fout. Ophalen van de meta gegevens van de detectie van DRS is mislukt.
   - Oplossing: Zoek hieronder de subfout op om verder te onderzoeken.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Reden: er is een time-out opgetreden tijdens de bewerking tijdens het detecteren.
   - Oplossing: Zorg ervoor dat deze `https://enterpriseregistration.windows.net` toegankelijk is in de systeem context. Zie de sectie [netwerk connectiviteit vereisten](hybrid-azuread-join-managed-domains.md#prerequisites)voor meer informatie.
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Reden: algemene realm-detectie fout. Kan het domein type (beheerd/Federatie) niet bepalen vanuit de STS.
   - Oplossing: Zoek hieronder de subfout op om verder te onderzoeken.

**Algemene subfout codes:**

Gebruik een van de volgende methoden om de subfout code voor de fout code van de detectie te vinden.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 en hoger

Zoek naar ' DRS Discovery test ' in de sectie ' Diagnostische gegevens ' van de uitvoer van de samenvoeg status. Deze sectie wordt alleen weer gegeven als het apparaat lid is van een domein en niet kan hybride Azure AD join.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Oudere versies van Windows 10

Gebruik Logboeken Logboeken om de fase en fout code voor het samen voegen van fouten te vinden.

1. Open de gebeurtenis logboeken voor **registratie van gebruikers apparaten** in Logboeken. Deze bevindt zich onder **toepassingen en services**  >  registratie van **micro soft**  >  **Windows**-  >  **gebruikers apparaten**
2. Zoek naar gebeurtenissen met de volgende eventIDs 201

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Scherm opname van de logboeken. Er wordt een gebeurtenis met I D 201 geselecteerd en de bijbehorende informatie wordt weer gegeven, waarbij de fout code is gemarkeerd." border="false":::

###### <a name="network-errors"></a>Netwerk fouten

- **WININET_E_CANNOT_CONNECT** (0x80072EFD/-2147012867)
   - Reden: verbinding met de server is niet tot stand gebracht
   - Oplossing: Controleer of de netwerk verbinding met de vereiste micro soft-resources is. Zie [vereisten voor netwerk connectiviteit](hybrid-azuread-join-managed-domains.md#prerequisites)voor meer informatie.
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Reden: algemene time-out in het netwerk.
   - Oplossing: Controleer of de netwerk verbinding met de vereiste micro soft-resources is. Zie [vereisten voor netwerk connectiviteit](hybrid-azuread-join-managed-domains.md#prerequisites)voor meer informatie.
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Reden: netwerk stack kan de reactie van de server niet ontsleutelen.
   - Oplossing: Zorg ervoor dat de netwerk proxy de reactie van de server niet verstoort en wijzigt.

###### <a name="http-errors"></a>HTTP-fouten

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Reden: SCP-object is geconfigureerd met een onjuiste Tenant-ID. Of er zijn geen actieve abonnementen gevonden in de Tenant.
   - Oplossing: Zorg ervoor dat het SCP-object is geconfigureerd met de juiste Azure AD-Tenant-ID en actieve abonnementen of aanwezig is in de Tenant.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Reden: HTTP 503 van DRS-server.
   - Oplossing: de server is momenteel niet beschikbaar. toekomstige deelname pogingen zullen waarschijnlijk slagen zodra de server weer online is.

###### <a name="other-errors"></a>Overige fouten

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Reden: de JSON van het server antwoord kan niet worden geparseerd. Waarschijnlijk vanwege een proxy retour nering van HTTP 200 met een HTML-auth-pagina.
   - Oplossing: als de on-premises omgeving een uitgaande proxy vereist, moet de IT-beheerder ervoor zorgen dat de systeem context op het apparaat kan detecteren en op de achtergrond kan worden geverifieerd bij de uitgaande proxy.

#### <a name="authentication-phase"></a>Verificatie fase

Alleen van toepassing op federatieve domein accounts.

Redenen voor fout:

- Kan geen toegangs token op de achtergrond ophalen voor de DRS-resource.
   - Windows 10-apparaten verkrijgen verificatie token van de Federation-service met geïntegreerde Windows-verificatie naar een actief WS-Trust-eind punt. Details: [Federation service configuratie](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Algemene fout codes:**

Gebruik Logboeken Logboeken om de fout code, subfout code, fout code voor de server en het fout bericht van de server te vinden.

1. Open de gebeurtenis logboeken voor **registratie van gebruikers apparaten** in Logboeken. Deze bevindt zich onder **toepassingen en services**  >  registratie van **micro soft**  >  **Windows**-  >  **gebruikers apparaten**
2. Zoek naar gebeurtenissen met de volgende gebeurtenis-305

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Scherm opname van de logboeken. Een gebeurtenis met I D 305 is zichtbaar. De informatie wordt weer gegeven, met een D A L-fout codes en-status gemarkeerd." border="false":::

##### <a name="configuration-errors"></a>Configuratiefouten

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Reden: verificatie protocol is niet WS-Trusted.
   - Oplossing: de on-premises ID-provider moet WS-Trust ondersteunen
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Reden: on-premises Federation service heeft geen XML-antwoord geretourneerd.
   - Oplossing: Zorg ervoor dat MEX-eind punt een geldig XML-bestand retourneert. Zorg ervoor dat de proxy geen problemen ondervindt met het retour neren van niet-XML-antwoorden.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Reden: kan het eind punt voor de verificatie van gebruikers naam en wacht woord niet detecteren.
   - Oplossing: Controleer de instellingen van on-premises ID-provider. Zorg ervoor dat de WS-Trust-eind punten zijn ingeschakeld en zorg ervoor dat het MEX-antwoord deze juiste eind punten bevat.

##### <a name="network-errors"></a>Netwerk fouten

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Reden: algemene time-out in het netwerk.
   - Oplossing: Zorg ervoor dat deze `https://login.microsoftonline.com` toegankelijk is in de systeem context. Zorg ervoor dat de on-premises ID-provider toegankelijk is in de systeem context. Zie [vereisten voor netwerk connectiviteit](hybrid-azuread-join-managed-domains.md#prerequisites)voor meer informatie.
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Reden: verbinding met het verificatie-eind punt is afgebroken.
   - Oplossing: Probeer het over enige tijd opnieuw of probeer te koppelen vanaf een andere stabiele netwerk locatie.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Reden: de Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), certificaat dat door de server is verzonden, kan niet worden gevalideerd.
   - Oplossing: Controleer de client tijd scheefheid. Probeer het later opnieuw of probeer te koppelen vanaf een andere stabiele netwerk locatie.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Reden: de poging om verbinding te maken met `https://login.microsoftonline.com` is mislukt.
   - Oplossing: Controleer de netwerk verbinding met `https://login.microsoftonline.com` .

##### <a name="other-errors"></a>Overige fouten

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Reden: SAML-token van de on-premises ID-provider is niet geaccepteerd door Azure AD.
   - Oplossing: Controleer de instellingen van de Federatie server. Zoek naar de fout code van de server in de verificatie Logboeken.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Reden: het antwoord van de server WS-Trust heeft een fout uitzondering gerapporteerd en de bevestiging is niet opgehaald
   - Oplossing: Controleer de instellingen van de Federatie server. Zoek naar de fout code van de server in de verificatie Logboeken.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Reden: er is een fout opgetreden bij het ophalen van het toegangs token van het token eindpunt.
   - Oplossing: zoek naar de onderliggende fout in het ADAL-logboek.
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Reden: algemene ADAL-fout
   - Oplossing: zoek naar de code van de subfout of de fout code van de server uit de verificatie Logboeken.

#### <a name="join-phase"></a>Deelname fase

Redenen voor fout:

Zoek het registratie type en zoek naar de fout code in de onderstaande lijst.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 en hoger

Zoek naar de subsectie ' voorafgaande registratie ' in de sectie ' Diagnostische gegevens ' van de uitvoer van de samenvoeg status. Deze sectie wordt alleen weer gegeven als het apparaat lid is van een domein en niet kan hybride Azure AD join.
Het veld registratie type geeft het type samen voeging aan dat wordt uitgevoerd.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Oudere versies van Windows 10

Gebruik Logboeken Logboeken om de fase en fout code voor het samen voegen van fouten te vinden.

1. Open de gebeurtenis logboeken voor **registratie van gebruikers apparaten** in Logboeken. Deze bevindt zich onder **toepassingen en services**  >  registratie van **micro soft**  >  **Windows**-  >  **gebruikers apparaten**
2. Zoek naar gebeurtenissen met de volgende eventIDs 204

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Scherm opname van de logboeken. Informatie over een gebeurtenis met I D 204 wordt weer gegeven, met de fout code, H T/m P status en het bericht gemarkeerd." border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>HTTP-fouten geretourneerd door de DRS-server

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Reden: er is een fout antwoord ontvangen van DRS met error code: "Directory fout"
   - Oplossing: Raadpleeg de fout code van de server voor mogelijke redenen en oplossingen.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Reden: er is een fout antwoord ontvangen van DRS met error code: ' AuthenticationError ' en ErrorSubCode is niet ' DeviceNotFound '.
   - Oplossing: Raadpleeg de fout code van de server voor mogelijke redenen en oplossingen.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Reden: er is een fout antwoord ontvangen van DRS met error code: "Directory fout"
   - Oplossing: Raadpleeg de fout code van de server voor mogelijke redenen en oplossingen.

##### <a name="tpm-errors"></a>TPM-fouten

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Reden: TPM-bewerking is mislukt of is ongeldig
   - Oplossing: waarschijnlijk vanwege een onjuiste Sysprep-installatie kopie. Zorg ervoor dat de computer waarvan de Sysprep-installatie kopie is gemaakt, niet is toegevoegd aan Azure AD, aan hybride Azure AD is toegevoegd of dat Azure AD is geregistreerd.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Reden: algemene TPM-fout.
   - Oplossing: Schakel TPM op apparaten met deze fout uit. Windows 10 versie 1809 en hoger detecteert automatisch TPM-fouten en voert hybride Azure AD-deelname uit zonder de TPM te gebruiken.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Reden: TPM in FIPS-modus wordt momenteel niet ondersteund.
   - Oplossing: Schakel TPM op apparaten met deze fout uit. Windows 1809 detecteert automatisch TPM-fouten en voltooit hybride Azure AD-deelname zonder de TPM te gebruiken.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Reden: TPM is vergrendeld.
   - Oplossing: tijdelijke fout. Wacht tot de cooldown-periode. Probeer het na enige tijd opnieuw. Meer informatie vindt u in het artikel over de [grond beginselen van TPM](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Netwerk fouten

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Reden: time-out bij algemeen netwerk bij het registreren van het apparaat bij DRS
   - Oplossing: Controleer de netwerk verbinding met `https://enterpriseregistration.windows.net` .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072EE7/-2147012889)
   - Reden: de server naam of het adres kan niet worden omgezet.
   - Oplossing: Controleer de netwerk verbinding met `https://enterpriseregistration.windows.net` . Zorg ervoor dat de DNS-omzetting voor de hostnaam nauw keurig is in de n/w en op het apparaat.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Reden: de verbinding met de server is abnormaal beëindigd.
   - Oplossing: Probeer het over enige tijd opnieuw of probeer te koppelen vanaf een andere stabiele netwerk locatie.

##### <a name="federated-join-server-errors"></a>Fouten van federatieve lid server

| Fout code server | Fout bericht server | Mogelijke redenen | Oplossing |
| --- | --- | --- | --- |
| Directory fout | Uw aanvraag is tijdelijk beperkt. Probeer na 300 seconden een ogen blik. | Verwachte fout. Mogelijk omdat er snel meerdere registratie aanvragen worden gedaan. | Na de cooldown-periode opnieuw verbinding maken |

##### <a name="sync-join-server-errors"></a>Fouten bij het samen voegen van servers

| Fout code server | Fout bericht server | Mogelijke redenen | Oplossing |
| --- | --- | --- | --- |
| Directory fout | AADSTS90002: de Tenant is <UUID> niet gevonden. Deze fout kan optreden als er geen actieve abonnementen voor de Tenant zijn. Neem contact op met de beheerder van uw abonnement. | De Tenant-ID in het SCP-object is onjuist | Zorg ervoor dat het SCP-object is geconfigureerd met de juiste Azure AD-Tenant-ID en actieve abonnementen en aanwezig is in de Tenant. |
| Directory fout | Het apparaatobject met de opgegeven ID is niet gevonden. | Verwachte fout voor Sync-koppeling. Het apparaatobject is niet gesynchroniseerd van AD naar Azure AD | Wacht tot de Azure AD Connect synchronisatie is voltooid en de volgende stap na het synchroniseren van de synchronisatie het probleem heeft opgelost |
| AuthenticationError | De verificatie van de SID van de doel computer | Het certificaat op het Azure AD-apparaat komt niet overeen met het certificaat dat wordt gebruikt voor het ondertekenen van de BLOB tijdens de synchronisatie koppeling. Deze fout betekent meestal dat de synchronisatie nog niet is voltooid. |  Wacht tot de Azure AD Connect synchronisatie is voltooid en de volgende stap na het synchroniseren van de synchronisatie het probleem heeft opgelost |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Stap 5: Logboeken verzamelen en contact opnemen met Microsoft Ondersteuning

Down load het bestand Auth.zip van [https://github.com/CSS-Identity/DRS/tree/main/Auth](https://github.com/CSS-Identity/DRS/tree/main/Auth)

1. Pak de bestanden uit en wijzig de naam van de Inge sloten bestanden **start-auth.txt** en **stop-auth.txt** in **Start-auth. cmd** en **Stop-auth. cmd**.
1. Voer **Start-auth. cmd** uit vanaf een opdracht prompt met verhoogde bevoegdheid.
1. Gebruik een ander account om met de probleem gebruiker over te scha kelen naar een andere sessie.
1. Reproduceer het probleem.
1. Gebruik switch-account om terug te scha kelen naar de beheer sessie met de tracering.
1. Voer **Stop-auth. cmd** uit vanaf een opdracht prompt met verhoogde bevoegdheid.
1. Zip en verzend de map **Authlogs** uit de map waarin de scripts zijn uitgevoerd.

## <a name="troubleshoot-post-join-issues"></a>Problemen met post-deelname oplossen

### <a name="retrieve-the-join-status"></a>De status van de samen voeging ophalen

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: Ja en AzureADPrt: Ja

Deze velden geven aan of de gebruiker is geverifieerd bij Azure AD bij het aanmelden bij het apparaat.
Als de waarden **Nee** zijn, kan dit worden veroorzaakt door:

- Ongeldige opslag sleutel in de TPM die tijdens de registratie aan het apparaat is gekoppeld (Controleer de KeySignTest tijdens de uitvoering van de verhoogde bevoegdheid).
- Alternatieve aanmeldings-ID
- Kan de HTTP-proxy niet vinden

## <a name="known-issues"></a>Bekende problemen
- Onder instellingen-> accounts-> toegang tot werk of school kunnen hybride Azure AD gekoppelde apparaten twee verschillende accounts weer geven, één voor Azure AD en één voor on-premises AD, wanneer deze zijn verbonden met mobiele HOTS pots of externe WiFi-netwerken. Dit is slechts een UI-probleem en heeft geen invloed op de functionaliteit.

## <a name="next-steps"></a>Volgende stappen

Door gaan met [het oplossen van problemen met de dsregcmd-opdracht](troubleshoot-device-dsregcmd.md)

Zie [Veelgestelde vragen over Apparaatbeheer](faq.md) voor meer informatie.
