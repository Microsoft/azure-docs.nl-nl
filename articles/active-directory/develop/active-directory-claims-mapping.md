---
title: Azure AD-Tenant-app claims aanpassen (Power shell)
titleSuffix: Microsoft identity platform
description: Op deze pagina wordt Azure Active Directory claim toewijzing beschreven.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 2d65889a841655fe27994d3855f30f7a7e20e1ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647593"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Procedure: claims aanpassen die worden verzonden in tokens voor een specifieke app in een Tenant (preview-versie)

> [!NOTE]
> Deze functie vervangt en vervangt de [claim aanpassing](active-directory-saml-claims-customization.md) die momenteel wordt aangeboden via de portal. Als u op dezelfde toepassing claims aanpast met behulp van de Graph/Power shell-methode die in dit document wordt beschreven, wordt de configuratie in de portal genegeerd door tokens die zijn uitgegeven voor die toepassing. Configuraties die zijn gemaakt via de methoden die in dit document worden beschreven, worden niet weer gegeven in de portal.

Deze functie wordt door Tenant beheerders gebruikt voor het aanpassen van de claims die worden verzonden in tokens voor een specifieke toepassing in hun Tenant. U kunt beleids regels voor claim toewijzing gebruiken voor het volgende:

- Selecteer welke claims worden opgenomen in tokens.
- Maak claim typen die nog niet bestaan.
- Kies of wijzig de bron van gegevens die in specifieke claims worden verzonden.

> [!NOTE]
> Deze functie is momenteel beschikbaar als open bare preview. Wees voorbereid om terug te keren of eventuele wijzigingen te verwijderen. De functie is beschikbaar in een Azure Active Directory-abonnement (Azure AD) tijdens de open bare preview. Wanneer de functie echter algemeen beschikbaar wordt, is voor sommige aspecten van de functie mogelijk een Azure AD Premium-abonnement vereist. Deze functie biedt ondersteuning voor het configureren van claim toewijzings beleid voor WS-insluitings-, SAML-, OAuth-en OpenID Connect-Connect-protocollen.

## <a name="claims-mapping-policy-type"></a>Claim toewijzings beleid type

In azure AD vertegenwoordigt een **beleids** object een set regels die worden afgedwongen voor afzonderlijke toepassingen of voor alle toepassingen in een organisatie. Elk type beleid heeft een unieke structuur, met een set eigenschappen die vervolgens worden toegepast op objecten waaraan ze zijn toegewezen.

Een claim toewijzings beleid is een type **beleids** object dat de claims wijzigt die worden verzonden in tokens die zijn uitgegeven voor specifieke toepassingen.

## <a name="claim-sets"></a>Claimsets

Er zijn bepaalde sets claims die bepalen hoe en wanneer ze worden gebruikt in tokens.

| Claimset | Beschrijving |
|---|---|
| Kern claim ingesteld | Zijn aanwezig in elke token, ongeacht het beleid. Deze claims worden ook beschouwd als beperkt en kunnen niet worden gewijzigd. |
| Basis claim instellen | Bevat de claims die standaard worden verzonden voor tokens (naast de kern claim set). U kunt basis claims weglaten of wijzigen met behulp van het beleid voor claim toewijzing. |
| Beperkte claim sets | Kan niet worden gewijzigd met het beleid. De gegevens bron kan niet worden gewijzigd en er wordt geen trans formatie toegepast wanneer deze claims worden gegenereerd. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabel 1: beperkte claim sets JSON Web Token (JWT)

| Claim type (naam) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| AIO |
| altsecid |
| AMR |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| AppID |
| appidacr |
| Assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| besturingselementen |
| credential_keys |
| CSR |
| csr_type |
| DeviceID |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolids |
| geldig |
| expires_on |
| grant_type |
| Graph |
| group_sids |
| groepen |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| Identity provider |
| IDP |
| in_corp |
| exemplaar |
| ipaddr |
| isbrowserhostedapp |
| ISS |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| meid |
| NBF |
| netbios_name |
| nonce |
| nogmaals |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| wachtwoord |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| PUID |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| role |
| rollen |
| scope |
| verbindings |
| geval |
| ondertekening |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| TID |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| gebruikersnaam |
| uti's |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabel 2: door SAML beperkte claim set

| Claim type (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Eigenschappen van beleids regels voor claim toewijzing

Als u wilt bepalen welke claims worden verzonden en waar de gegevens vandaan komen, gebruikt u de eigenschappen van een claim toewijzings beleid. Als er geen beleid is ingesteld, worden er door het systeem tokens uitgegeven die de kern claimset, de basis claimset en eventuele [optionele claims](active-directory-optional-claims.md) bevatten die de toepassing heeft ontvangen.

> [!NOTE]
> Claims in de set met kern claims zijn aanwezig in elk token, ongeacht waar deze eigenschap is ingesteld.

### <a name="include-basic-claim-set"></a>Basis claimset toevoegen

**Teken reeks:** IncludeBasicClaimSet

**Gegevens type:** Boole-waarde (waar of onwaar)

**Samen vatting:** Deze eigenschap bepaalt of de set basis claims is opgenomen in tokens die door dit beleid worden beïnvloed.

- Als deze eigenschap is ingesteld op True, worden alle claims in de set basis claims verzonden in tokens die door het beleid worden beïnvloed.
- Als deze eigenschap is ingesteld op False, bevinden claims in de set basis claims zich niet in de tokens, tenzij ze afzonderlijk worden toegevoegd in de eigenschappen van het claim schema van hetzelfde beleid.



### <a name="claims-schema"></a>Claim schema

**Teken reeks:** ClaimsSchema

**Gegevens type:** JSON-blob met een of meer claim schema vermeldingen

**Samen vatting:** Met deze eigenschap wordt gedefinieerd welke claims aanwezig zijn in de tokens die door het beleid worden beïnvloed, naast de set met basis claims en de kern claimset.
Voor elke claim schema vermelding die in deze eigenschap is gedefinieerd, is bepaalde informatie vereist. Geef op waar de gegevens vandaan komen (**waarde**, **bron/id-paar** of **bron-ExtensionID**) en welke claim de gegevens worden verzonden als (**claim type**).

### <a name="claim-schema-entry-elements"></a>Elementen van claim schema vermeldingen

**Waarde:** Het element waarde definieert een statische waarde als de gegevens die moeten worden verzonden in de claim.

**Bron/ID-paar:** De bron-en ID-elementen bepalen waar de gegevens in de claim worden gebrond.

**Bron-ExtensionID-paar:** De bron-en ExtensionID-elementen definiëren het kenmerk Directory schema-uitbrei ding waaruit de gegevens in de claim afkomstig zijn. Zie [kenmerken van Directory-schema uitbreidingen gebruiken in claims](active-directory-schema-extensions.md)voor meer informatie.

Stel het bron element in op een van de volgende waarden:

- gebruiker: de gegevens in de claim zijn een eigenschap van het gebruikers object.
- "toepassing": de gegevens in de claim zijn een eigenschap van de service-principal van de toepassing (client).
- resource: de gegevens in de claim zijn een eigenschap van de resource Service-Principal.
- "doel groep": de gegevens in de claim zijn een eigenschap van de service-principal die de doel groep is van het token (de client-of resource service-principal).
- ' bedrijf ': de gegevens in de claim zijn een eigenschap van het bedrijfs object van de resource-Tenant.
- "trans formatie": de gegevens in de claim zijn afkomstig van trans formatie van claims (Zie de sectie ' claims transformeren ' verderop in dit artikel).

Als de bron trans formatie is, moet het **TransformationID** -element ook worden opgenomen in deze claim definitie.

Het ID-element identificeert welke eigenschap van de bron de waarde voor de claim levert. De volgende tabel geeft een lijst van de waarden van ID die geldig zijn voor elke bron waarde.

#### <a name="table-3-valid-id-values-per-source"></a>Tabel 3: geldige ID-waarden per bron

| Bron | Id | Beschrijving |
|-----|-----|-----|
| Gebruiker | surname | Familie naam |
| Gebruiker | givenname | Voornaam |
| Gebruiker | displayname | Weergavenaam |
| Gebruiker | id | ObjectID |
| Gebruiker | mail | E-mailadres |
| Gebruiker | userPrincipalName | User Principal Name |
| Gebruiker | department|Afdeling|
| Gebruiker | onpremisessamaccountname | Naam van on-premises SAM-account |
| Gebruiker | netbiosname| NetBios-naam |
| Gebruiker | DNS | DNS-domeinnaam |
| Gebruiker | onpremisesecurityidentifier | Lokale beveiligings-id |
| Gebruiker | CompanyName| Organisatienaam |
| Gebruiker | streetaddress | Adres |
| Gebruiker | postalcode | Postcode |
| Gebruiker | preferredlanguage | Voorkeurstaal |
| Gebruiker | onpremisesuserprincipalname | On-premises UPN |*
| Gebruiker | mailNickname | E-mail bijnaam |
| Gebruiker | extensionattribute1 | Uitbreidings kenmerk 1 |
| Gebruiker | extensionattribute2 | Uitbreidings kenmerk 2 |
| Gebruiker | extensionattribute3 | Uitbreidings kenmerk 3 |
| Gebruiker | extensionattribute4 | Uitbreidings kenmerk 4 |
| Gebruiker | extensionattribute5 | Uitbreidings kenmerk 5 |
| Gebruiker | extensionattribute6 | Uitbreidings kenmerk 6 |
| Gebruiker | extensionattribute7 | Uitbreidings kenmerk 7 |
| Gebruiker | extensionattribute8 | Uitbreidings kenmerk 8 |
| Gebruiker | extensionattribute9 | Uitbreidings kenmerk 9 |
| Gebruiker | extensionattribute10 | Uitbreidings kenmerk 10 |
| Gebruiker | extensionattribute11 | Uitbreidings kenmerk 11 |
| Gebruiker | extensionattribute12 | Uitbreidings kenmerk 12 |
| Gebruiker | extensionattribute13 | Uitbreidings kenmerk 13 |
| Gebruiker | extensionattribute14 | Uitbreidings kenmerk 14 |
| Gebruiker | extensionattribute15 | Uitbreidings kenmerk 15 |
| Gebruiker | othermail | Andere E-mail |
| Gebruiker | country | Land/regio |
| Gebruiker | city | Plaats |
| Gebruiker | staat | Staat |
| Gebruiker | jobtitle | Functie |
| Gebruiker | employeeid | Werknemers-id |
| Gebruiker | facsimiletelephonenumber | Telefoon nummer Fax |
| Gebruiker | assignedroles | lijst met app-rollen die aan de gebruiker zijn toegewezen|
| toepassing, resource, doel groep | displayname | Weergavenaam |
| toepassing, resource, doel groep | id | ObjectID |
| toepassing, resource, doel groep | tags | Service-Principal-tag |
| Bedrijf | tenantcountry | Land/regio van de Tenant |

**TransformationID:** Het TransformationID-element moet alleen worden opgegeven als het bron element is ingesteld op trans formatie.

- Dit element moet overeenkomen met het ID-element van de transformatie vermelding in de eigenschap **ClaimsTransformation** die bepaalt hoe de gegevens voor deze claim worden gegenereerd.

**Claim type:** De **JwtClaimType** -en **SamlClaimType** -elementen bepalen welke claim deze claim schema vermelding verwijst.

- De JwtClaimType moet de naam van de claim bevatten die moet worden verzonden in JWTs.
- De SamlClaimType moet de URI bevatten van de claim die moet worden verzonden in SAML-tokens.

* **kenmerk onPremisesUserPrincipalName:** Wanneer u een alternatieve ID gebruikt, wordt het on-premises kenmerk userPrincipalName gesynchroniseerd met het Azure AD-kenmerk onPremisesUserPrincipalName. Dit kenmerk is alleen beschikbaar wanneer alternatieve ID is geconfigureerd, maar is ook beschikbaar via MS Graph Beta: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Namen en Uri's van claims in de beperkte claim sets kunnen niet worden gebruikt voor de claim type-elementen. Zie de sectie ' uitzonde ringen en beperkingen ' verderop in dit artikel voor meer informatie.

### <a name="claims-transformation"></a>Claimstransformatie

**Teken reeks:** ClaimsTransformation

**Gegevens type:** JSON-blob, met een of meer transformatie vermeldingen

**Samen vatting:** Gebruik deze eigenschap om veelvoorkomende trans formaties toe te passen op Bron gegevens om de uitvoer gegevens te genereren voor claims die zijn opgegeven in het claims-schema.

**Id:** Gebruik het element ID om te verwijzen naar deze transformatie vermelding in de TransformationID claims schema vermelding. Deze waarde moet uniek zijn voor elk transformatie vermelding in dit beleid.

**TransformationMethod:** Het TransformationMethod-element identificeert welke bewerking wordt uitgevoerd om de gegevens voor de claim te genereren.

Op basis van de gekozen methode wordt een set invoer en uitvoer verwacht. Definieer de invoer en uitvoer met behulp van de elementen **InputClaims**, **Input parameters** en **OutputClaims** .

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabel 4: transformatie methoden en verwachte invoer en uitvoer

|TransformationMethod|Verwachte invoer|Verwachte uitvoer|Beschrijving|
|-----|-----|-----|-----|
|Deelnemen|tekenreeks1, tekenreeks2, scheidings teken|Output claim|Voegt invoer teken reeksen samen met behulp van een scheidings teken tussen. Bijvoorbeeld: tekenreeks1: " foo@bar.com ", tekenreeks2: "sandbox", scheidings teken: "." resulteert in output claim: " foo@bar.com.sandbox "|
|ExtractMailPrefix|E-mail of UPN|geëxtraheerde teken reeks|ExtensionAttributes 1-15 of andere schema-uitbrei dingen die een UPN-of e-mailadres waarde voor de gebruiker opslaan, bijvoorbeeld johndoe@contoso.com . Extraheert het lokale deel van een e-mail adres. Bijvoorbeeld: mail: " foo@bar.com " resulteert in output claim: "foo". Als er geen \@ teken aanwezig is, wordt de oorspronkelijke invoer teken reeks geretourneerd als is.|

**InputClaims:** Gebruik een InputClaims-element om de gegevens van een claim schema vermelding door te geven aan een trans formatie. Het heeft twee kenmerken: **ClaimTypeReferenceId** en **TransformationClaimType**.

- **ClaimTypeReferenceId** is gekoppeld aan het id-element van de claim schema vermelding om de juiste invoer claim te vinden.
- **TransformationClaimType** wordt gebruikt om een unieke naam te geven aan deze invoer. Deze naam moet overeenkomen met een van de verwachte invoer waarden voor de transformatie methode.

**Invoer parameters:** Gebruik een input parameters-element om een constante waarde door te geven aan een trans formatie. Het heeft twee kenmerken: **waarde** en **id**.

- **Waarde** is de daad werkelijke constante waarde die moet worden door gegeven.
- De **id** wordt gebruikt om een unieke naam te geven aan de invoer. De naam moet overeenkomen met een van de verwachte invoer waarden voor de transformatie methode.

**OutputClaims:** Gebruik een OutputClaims-element om de gegevens op te slaan die zijn gegenereerd door een trans formatie en deze te koppelen aan een claim schema vermelding. Het heeft twee kenmerken: **ClaimTypeReferenceId** en **TransformationClaimType**.

- **ClaimTypeReferenceId** wordt gekoppeld aan de id van de claim schema vermelding om de juiste uitvoer claim te vinden.
- **TransformationClaimType** wordt gebruikt om een unieke naam te geven aan de uitvoer. De naam moet overeenkomen met een van de verwachte uitvoer voor de transformatie methode.

### <a name="exceptions-and-restrictions"></a>Uitzonde ringen en beperkingen

**SAML NameID en UPN:** De kenmerken van waaruit u de NameID-en UPN-waarden hebt gebrond en de claim transformaties die zijn toegestaan, zijn beperkt. Zie tabel 5 en tabel 6 voor een overzicht van de toegestane waarden.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabel 5: kenmerken die zijn toegestaan als gegevens bron voor SAML NameID

|Bron|Id|Beschrijving|
|-----|-----|-----|
| Gebruiker | mail|E-mailadres|
| Gebruiker | userPrincipalName|User Principal Name|
| Gebruiker | onpremisessamaccountname|Naam van on-premises SAM-account|
| Gebruiker | employeeid|Werknemers-id|
| Gebruiker | extensionattribute1 | Uitbreidings kenmerk 1 |
| Gebruiker | extensionattribute2 | Uitbreidings kenmerk 2 |
| Gebruiker | extensionattribute3 | Uitbreidings kenmerk 3 |
| Gebruiker | extensionattribute4 | Uitbreidings kenmerk 4 |
| Gebruiker | extensionattribute5 | Uitbreidings kenmerk 5 |
| Gebruiker | extensionattribute6 | Uitbreidings kenmerk 6 |
| Gebruiker | extensionattribute7 | Uitbreidings kenmerk 7 |
| Gebruiker | extensionattribute8 | Uitbreidings kenmerk 8 |
| Gebruiker | extensionattribute9 | Uitbreidings kenmerk 9 |
| Gebruiker | extensionattribute10 | Uitbreidings kenmerk 10 |
| Gebruiker | extensionattribute11 | Uitbreidings kenmerk 11 |
| Gebruiker | extensionattribute12 | Uitbreidings kenmerk 12 |
| Gebruiker | extensionattribute13 | Uitbreidings kenmerk 13 |
| Gebruiker | extensionattribute14 | Uitbreidings kenmerk 14 |
| Gebruiker | extensionattribute15 | Uitbreidings kenmerk 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabel 6: transformatie methoden die zijn toegestaan voor SAML NameID

| TransformationMethod | Beperkingen |
| ----- | ----- |
| ExtractMailPrefix | Geen |
| Deelnemen | Het achtervoegsel dat wordt gekoppeld, moet een geverifieerd domein zijn van de resource Tenant. |

### <a name="custom-signing-key"></a>Aangepaste handtekening sleutel

Een aangepaste ondertekeningssleutel moet worden toegewezen aan het Service-Principal-object om een claim toewijzings beleid van kracht te laten worden. Dit zorgt ervoor dat de bevestiging van tokens door de maker van het beleid voor claim toewijzing is gewijzigd en dat toepassingen worden beschermd tegen beleids regels voor claim toewijzing die door kwaad aardige Actors zijn gemaakt. Als u een aangepaste ondertekeningssleutel wilt toevoegen, kunt u de cmdlet Azure PowerShell gebruiken [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) om een certificaat sleutel referentie voor uw toepassings object te maken.

Apps waarvoor claim toewijzing is ingeschakeld, moeten hun token handtekening sleutels valideren door `appid={client_id}` toe te voegen aan hun [OpenID Connect Connect-aanvragen voor meta gegevens](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Hieronder ziet u de indeling van het OpenID Connect Connect-meta gegevens document dat u moet gebruiken:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Scenario's voor meerdere tenants

Beleids regels voor claim toewijzing zijn niet van toepassing op gast gebruikers. Als een gast gebruiker toegang probeert te krijgen tot een toepassing met een claim toewijzings beleid dat is toegewezen aan de Service-Principal, wordt het standaard token uitgegeven (het beleid heeft geen effect).

## <a name="claims-mapping-policy-assignment"></a>Toewijzing van beleids regels voor claim toewijzing

Beleids regels voor claim toewijzing kunnen alleen worden toegewezen aan Service-Principal-objecten.

### <a name="example-claims-mapping-policies"></a>Voor beeld van beleid voor claim toewijzing

In azure AD zijn veel scenario's mogelijk wanneer u claims kunt aanpassen die worden verzonden in tokens voor specifieke service-principals. In deze sectie worden enkele algemene scenario's besproken die u kunnen helpen bij het begrijpt van het gebruik van het claim toewijzings beleid type.

Bij het maken van een claim toewijzings beleid kunt u ook een claim van een uitbreidings kenmerk van een Directory-schema in tokens verzenden. Gebruik *ExtensionID* voor het kenmerk extension in plaats van id in het *-* `ClaimsSchema` element.  Zie [kenmerken van Directory-schema uitbreiding gebruiken](active-directory-schema-extensions.md)voor meer informatie over extensie kenmerken.

#### <a name="prerequisites"></a>Vereisten

In de volgende voor beelden maakt, bijwerkt, koppelt en verwijdert u beleids regels voor service-principals. Als u geen ervaring hebt met Azure AD, raden we u aan [meer te weten te komen over het verkrijgen van een Azure AD-Tenant](quickstart-create-new-tenant.md) voordat u verdergaat met deze voor beelden.

Voer de volgende stappen uit om aan de slag te gaan:

1. Down load de nieuwste [open bare preview-versie van Azure AD Power shell-module](https://www.powershellgallery.com/packages/AzureADPreview).
1. Voer de opdracht Connect uit om u aan te melden bij uw Azure AD-beheerders account. Voer deze opdracht telkens uit wanneer u een nieuwe sessie start.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Voer de volgende opdracht uit om alle beleids regels weer te geven die in uw organisatie zijn gemaakt. We raden u aan deze opdracht na de meeste bewerkingen uit te voeren in de volgende scenario's om te controleren of uw beleid op de verwachte manier wordt gemaakt.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Voor beeld: een beleid maken en toewijzen voor het weglaten van de basis claims van tokens die zijn uitgegeven aan een Service-Principal

In dit voor beeld maakt u een beleid waarmee de set basis claims wordt verwijderd uit tokens die zijn uitgegeven aan gekoppelde service-principals.

1. Maak een claim toewijzings beleid. Dit beleid, dat is gekoppeld aan specifieke service-principals, verwijdert de basis claimset van tokens.
   1. Voer de volgende opdracht uit om het beleid te maken:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Voer de volgende opdracht uit om het nieuwe beleid te bekijken en de beleids-ObjectId op te halen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw service-principal. U moet ook de ObjectId voor uw Service-Principal ophalen.
   1. Als u de service-principals van uw organisatie wilt weer geven, kunt u [een query uitvoeren op de Microsoft Graph-API](/graph/traverse-the-graph). Of Meld u in [Microsoft Graph Verkenner](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure ad-account.
   2. Wanneer u de ObjectId van uw Service-Principal hebt, voert u de volgende opdracht uit:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Voor beeld: een beleid maken en toewijzen om de werk nemer-en TenantCountry op te geven als claims in tokens die zijn uitgegeven aan een Service-Principal

In dit voor beeld maakt u een beleid waarmee de werk nemer-en TenantCountry worden toegevoegd aan tokens die zijn uitgegeven aan gekoppelde service-principals. De werk nemer-aanvraag wordt verzonden als het naam claim type in SAML-tokens en JWTs. De TenantCountry wordt verzonden als het land/de regio claim type in zowel de SAML-tokens als de JWTs. In dit voor beeld blijven de basis claims in de tokens worden toegevoegd.

1. Maak een claim toewijzings beleid. Met dit beleid, dat is gekoppeld aan specifieke service-principals, worden de werk nemer-en TenantCountry claims toegevoegd aan tokens.
   1. Voer de volgende opdracht uit om het beleid te maken:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Voer de volgende opdracht uit om het nieuwe beleid te bekijken en de beleids-ObjectId op te halen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw service-principal. U moet ook de ObjectId voor uw Service-Principal ophalen.
   1. Als u de service-principals van uw organisatie wilt weer geven, kunt u [een query uitvoeren op de Microsoft Graph-API](/graph/traverse-the-graph). Of Meld u in [Microsoft Graph Verkenner](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure ad-account.
   2. Wanneer u de ObjectId van uw Service-Principal hebt, voert u de volgende opdracht uit:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Voor beeld: een beleid maken en toewijzen dat gebruikmaakt van een claim transformatie in tokens die zijn uitgegeven aan een Service-Principal

In dit voor beeld maakt u een beleid dat een aangepaste claim ' JoinedData ' uitgeeft aan JWTs die zijn verleend aan gekoppelde service-principals. Deze claim bevat een waarde die is gemaakt door de gegevens die zijn opgeslagen in het kenmerk extensionAttribute1 van het gebruikers object te koppelen aan de sandbox. In dit voor beeld sluiten we de basis claims uit die zijn ingesteld in de tokens.

1. Maak een claim toewijzings beleid. Met dit beleid, dat is gekoppeld aan specifieke service-principals, worden de werk nemer-en TenantCountry claims toegevoegd aan tokens.
   1. Voer de volgende opdracht uit om het beleid te maken:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Voer de volgende opdracht uit om het nieuwe beleid te bekijken en de beleids-ObjectId op te halen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw service-principal. U moet ook de ObjectId voor uw Service-Principal ophalen.
   1. Als u de service-principals van uw organisatie wilt weer geven, kunt u [een query uitvoeren op de Microsoft Graph-API](/graph/traverse-the-graph). Of Meld u in [Microsoft Graph Verkenner](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure ad-account.
   2. Wanneer u de ObjectId van uw Service-Principal hebt, voert u de volgende opdracht uit:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Zie ook

- Zie [How to: claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen voor](active-directory-saml-claims-customization.md) meer informatie over het aanpassen van claims die zijn uitgegeven in het SAML-token via de Azure Portal.
- Zie voor meer informatie over extensie kenmerken de [kenmerken van Directory-schema-extensies gebruiken in claims](active-directory-schema-extensions.md).
