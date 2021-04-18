---
title: Beleid voor claimtoewijzing
titleSuffix: Microsoft identity platform
description: Meer informatie over het beleidstype voor claimtoewijzing, dat wordt gebruikt voor het wijzigen van de claims die worden uitgegeven in tokens die zijn uitgegeven voor specifieke toepassingen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 04/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 56e855bafa70360711f3e30a7c4527091af7b34c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601237"
---
# <a name="claims-mapping-policy-type"></a>Beleidstype claimtoewijzing

In Azure AD vertegenwoordigt **een beleidsobject** een set regels die worden afgedwongen voor afzonderlijke toepassingen of voor alle toepassingen in een organisatie. Elk type beleid heeft een unieke structuur, met een set eigenschappen die vervolgens worden toegepast op objecten waaraan ze zijn toegewezen.

Een claimtoewijzingsbeleid is een type **beleidsobject** dat de claims wijzigt die worden uitgegeven [in tokens die](active-directory-claims-mapping.md) zijn uitgegeven voor specifieke toepassingen.

## <a name="claim-sets"></a>Claimsets

Er zijn bepaalde sets claims die bepalen hoe en wanneer ze worden gebruikt in tokens.

| Claimset | Description |
|---|---|
| Core-claimset | Zijn aanwezig in elk token, ongeacht het beleid. Deze claims worden ook beschouwd als beperkt en kunnen niet worden gewijzigd. |
| Basisclaimset | Bevat de claims die standaard worden ingediend voor tokens (naast de basisclaimset). U kunt basisclaims weglaten of wijzigen met behulp van het beleid voor claimtoewijzing. |
| Beperkte claimset | Kan niet worden gewijzigd met behulp van beleid. De gegevensbron kan niet worden gewijzigd en er wordt geen transformatie toegepast bij het genereren van deze claims. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabel 1: JSON Web Token (JWT) beperkte claimset

| Claimtype (naam) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Acteur |
| actortoken |
| Aio |
| altsecid |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| Appid |
| appidacr |
| Bewering |
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
| Cnf |
| code |
| besturingselementen |
| credential_keys |
| Mvo |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolids |
| Exp |
| expires_on |
| grant_type |
| Grafiek |
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
| Iat |
| identityprovider |
| Idp |
| in_corp |
| Exemplaar |
| Ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| Nonce |
| Oid |
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
| puid |
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
| Scp |
| Sid |
| Handtekening |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| Tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| gebruikersnaam |
| Uti |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabel 2: beperkte SAML-claimset

| Claimtype (URI) |
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

## <a name="claims-mapping-policy-properties"></a>Eigenschappen van het beleid voor claimtoewijzing

Als u wilt bepalen welke claims worden uitgezonden en waar de gegevens vandaan komen, gebruikt u de eigenschappen van een claimtoewijzingsbeleid. Als er geen beleid is ingesteld, geeft het systeem tokens uit die de basisclaimset, de basisclaimset en eventuele optionele [claims](active-directory-optional-claims.md) bevatten die de toepassing heeft gekozen te ontvangen.

> [!NOTE]
> Claims in de basisclaimset zijn aanwezig in elk token, ongeacht waarvoor deze eigenschap is ingesteld.

### <a name="include-basic-claim-set"></a>Basisclaimset opnemen

**Tekenreeks:** IncludeBasicClaimSet

**Gegevenstype:** Booleaanse booleaanse naam (waar of onwaar)

**Samenvatting:** Deze eigenschap bepaalt of de basisclaimset is opgenomen in tokens die worden beïnvloed door dit beleid.

- Als deze is ingesteld op True, worden alle claims in de basisclaimset in tokens die door het beleid worden beïnvloed, uitgezonden.
- Als dit is ingesteld op Onwaar, worden claims in de basisclaimset niet in de tokens weergegeven, tenzij ze afzonderlijk worden toegevoegd in de claimschema-eigenschap van hetzelfde beleid.



### <a name="claims-schema"></a>Claimschema

**Tekenreeks:** ClaimsSchema

**Gegevenstype:** JSON-blob met een of meer claimschema-vermeldingen

**Samenvatting:** Deze eigenschap definieert welke claims aanwezig zijn in de tokens die worden beïnvloed door het beleid, naast de basisclaimset en de basisclaimset.
Voor elke vermelding in het claimschema die in deze eigenschap is gedefinieerd, is bepaalde informatie vereist. Geef op waar de gegevens vandaan komen (**Waarde,** **Bron/ID-paar** of **Bron/ExtensionID-paar**) en die claimen dat de gegevens worden ingediend als (**Claimtype).**

### <a name="claim-schema-entry-elements"></a>Invoerelementen van claimschema

**Waarde:** Het element Waarde definieert een statische waarde als de gegevens die in de claim moeten worden ingediend.

**Bron-id-paar:** De elementen Bron en Id bepalen waar de gegevens in de claim vandaan komen.

**Bron/ExtensionID-paar:** De elementen Source en ExtensionID definiëren het kenmerk directory schema extension waaruit de gegevens in de claim afkomstig zijn. Zie Using [directory schema extension attributes in claims (Kenmerken van directoryschema-extensies gebruiken in claims) voor meer informatie.](active-directory-schema-extensions.md)

Stel het bronelement in op een van de volgende waarden:

- "gebruiker": de gegevens in de claim is een eigenschap van het object Gebruiker.
- "application": De gegevens in de claim zijn een eigenschap van de service-principal van de toepassing (client).
- "resource": De gegevens in de claim zijn een eigenschap van de resourceservice-principal.
- 'doelgroep': De gegevens in de claim zijn een eigenschap van de service-principal die de doelgroep van het token is (de client- of resourceservice-principal).
- "company": De gegevens in de claim zijn een eigenschap van het bedrijfsobject van de resource-tenant.
- "transformatie": De gegevens in de claim zijn afkomstig van claimtransformatie (zie de sectie Claimtransformatie verder in dit artikel).

Als de bron transformatie is, moet het **element TransformationID** ook worden opgenomen in deze claimdefinitie.

Het element ID identificeert welke eigenschap van de bron de waarde voor de claim levert. De volgende tabel bevat de waarden van id die geldig zijn voor elke waarde van Bron.

#### <a name="table-3-valid-id-values-per-source"></a>Tabel 3: Geldige id-waarden per bron

| Bron | Id | Description |
|-----|-----|-----|
| Gebruiker | surname | Familienaam |
| Gebruiker | givenname | Voornaam |
| Gebruiker | displayname | Weergavenaam |
| Gebruiker | objectid | ObjectID |
| Gebruiker | mail | E-mailadres |
| Gebruiker | userprincipalname | User Principal Name |
| Gebruiker | department|Afdeling|
| Gebruiker | onpremisessamaccountname | On-premises SAM-accountnaam |
| Gebruiker | netbiosname| NetBios-naam |
| Gebruiker | dnsdomainname | DNS-domeinnaam |
| Gebruiker | onpremisesecurityidentifier | On-premises beveiligings-id |
| Gebruiker | Bedrijfsnaam| Organisatienaam |
| Gebruiker | streetaddress | Adres |
| Gebruiker | postalcode | Postcode |
| Gebruiker | preferredlanguage | Voorkeurstaal |
| Gebruiker | onpremisesuserprincipalname | On-premises UPN |
| Gebruiker | mailnickname | Bijnaam van e-mail |
| Gebruiker | extensionattribute1 | Extensiekenmerk 1 |
| Gebruiker | extensionattribute2 | Extensiekenmerk 2 |
| Gebruiker | extensionattribute3 | Extensiekenmerk 3 |
| Gebruiker | extensionattribute4 | Extensiekenmerk 4 |
| Gebruiker | extensionattribute5 | Extensiekenmerk 5 |
| Gebruiker | extensionattribute6 | Extensiekenmerk 6 |
| Gebruiker | extensionattribute7 | Extensiekenmerk 7 |
| Gebruiker | extensionattribute8 | Extensiekenmerk 8 |
| Gebruiker | extensionattribute9 | Extensiekenmerk 9 |
| Gebruiker | extensionattribute10 | Extensiekenmerk 10 |
| Gebruiker | extensionattribute11 | Extensiekenmerk 11 |
| Gebruiker | extensionattribute12 | Extensiekenmerk 12 |
| Gebruiker | extensionattribute13 | Extensiekenmerk 13 |
| Gebruiker | extensionattribute14 | Extensiekenmerk 14 |
| Gebruiker | extensionattribute15 | Extensiekenmerk 15 |
| Gebruiker | othermail | Andere e-mail |
| Gebruiker | country | Land/regio |
| Gebruiker | city | Plaats |
| Gebruiker | staat | Staat |
| Gebruiker | jobtitle | Functie |
| Gebruiker | employeeid | Werknemers-id |
| Gebruiker | facsimiletelephonenumber | Facsimile-telefoonnummer |
| Gebruiker | toegewezenrollen | lijst met app-rollen die zijn toegewezen aan de gebruiker|
| toepassing, resource, doelgroep | displayname | Weergavenaam |
| toepassing, resource, doelgroep | objectid | ObjectID |
| toepassing, resource, doelgroep | tags | Service Principal Tag |
| Bedrijf | tenantcountry | Land/regio van tenant |

**TransformationID:** Het element TransformationID moet alleen worden opgegeven als het bronelement is ingesteld op 'transformatie'.

- Dit element moet overeenkomen met het id-element van de transformatie-vermelding in de **eigenschap ClaimsTransformation** die definieert hoe de gegevens voor deze claim worden gegenereerd.

**Claimtype:** De **elementen JwtClaimType** **en SamlClaimType** definiëren naar welke claim deze claimschema-vermelding verwijst.

- Het JwtClaimType moet de naam bevatten van de claim die moet worden ingediend in JWT's.
- Het SamlClaimType moet de URI bevatten van de claim die moet worden ingediend in SAML-tokens.

* **kenmerk onPremisesUserPrincipalName:** Wanneer u een alternatieve id gebruikt, wordt het on-premises kenmerk userPrincipalName gesynchroniseerd met het Azure AD-kenmerk onPremisesUserPrincipalName. Dit kenmerk is alleen beschikbaar wanneer alternatieve id is geconfigureerd, maar is ook beschikbaar via MS Graph Beta: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Namen en URI's van claims in de beperkte claimset kunnen niet worden gebruikt voor de claimtype-elementen. Zie de sectie Uitzonderingen en beperkingen verder op dit artikel voor meer informatie.

### <a name="claims-transformation"></a>Claimstransformatie

**Tekenreeks:** ClaimsTransformation

**Gegevenstype:** JSON-blob, met een of meer transformatie-vermeldingen

**Samenvatting:** Gebruik deze eigenschap om algemene transformaties toe te passen op brongegevens om de uitvoergegevens te genereren voor claims die zijn opgegeven in het claimschema.

**Id:** Gebruik het element ID om te verwijzen naar deze transformatie-vermelding in de vermelding TransformationID Claims Schema. Deze waarde moet uniek zijn voor elke transformatie-vermelding in dit beleid.

**TransformationMethod:** Het element TransformationMethod identificeert welke bewerking wordt uitgevoerd om de gegevens voor de claim te genereren.

Op basis van de gekozen methode wordt een set invoer en uitvoer verwacht. Definieer de invoer en uitvoer met behulp van de elementen **InputClaims,** **InputParameters** en **OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabel 4: Transformatiemethoden en verwachte invoer en uitvoer

|TransformationMethod|Verwachte invoer|Verwachte uitvoer|Description|
|-----|-----|-----|-----|
|Deelnemen|string1, string2, scheidingsteken|outputClaim|Voegt invoerreeksen samen met behulp van een scheidingsteken ertussen. Bijvoorbeeld: string1:" foo@bar.com " , string2:"sandbox" , scheidingsteken:"." resulteert in outputClaim:" foo@bar.com.sandbox " "|
|ExtractMailPrefix|E-mail of UPN|geëxtraheerde tekenreeks|ExtensionAttributes 1-15 of andere schema-extensies die een UPN- of e-mailadreswaarde voor de gebruiker opslaan, bijvoorbeeld johndoe@contoso.com . Extraheert het lokale deel van een e-mailadres. Bijvoorbeeld: mail:" foo@bar.com " results in outputClaim:"foo". Als er \@ geen teken aanwezig is, wordt de oorspronkelijke invoertekenreeks geretourneerd zoals deze is.|

**InputClaims:** Gebruik het element InputClaims om de gegevens van een claimschema-vermelding door te geven aan een transformatie. Deze heeft twee kenmerken: **ClaimTypeReferenceId** en **TransformationClaimType.**

- **ClaimTypeReferenceId** is samengevoegd met het id-element van de vermelding in het claimschema om de juiste invoerclaim te vinden.
- **TransformationClaimType** wordt gebruikt om deze invoer een unieke naam te geven. Deze naam moet overeenkomen met een van de verwachte invoer voor de transformatiemethode.

**InputParameters:** Gebruik het element InputParameters om een constante waarde door te geven aan een transformatie. Deze heeft twee kenmerken: **Waarde** en **Id.**

- **Waarde** is de werkelijke constante waarde die moet worden doorgegeven.
- **De** id wordt gebruikt om de invoer een unieke naam te geven. De naam moet overeenkomen met een van de verwachte invoer voor de transformatiemethode.

**OutputClaims:** Gebruik een OutputClaims-element om de gegevens op te houden die door een transformatie worden gegenereerd en deze te binden aan een claimschema-vermelding. Deze heeft twee kenmerken: **ClaimTypeReferenceId** en **TransformationClaimType.**

- **ClaimTypeReferenceId** is samengevoegd met de id van de vermelding van het claimschema om de juiste uitvoerclaim te vinden.
- **TransformationClaimType** wordt gebruikt om de uitvoer een unieke naam te geven. De naam moet overeenkomen met een van de verwachte uitvoer voor de transformatiemethode.

### <a name="exceptions-and-restrictions"></a>Uitzonderingen en beperkingen

**SAML NameID en UPN:** De kenmerken van waaruit u de waarden NameID en UPN als bron hebt, en de claimtransformaties die zijn toegestaan, zijn beperkt. Zie tabel 5 en tabel 6 om de toegestane waarden te bekijken.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabel 5: Kenmerken die zijn toegestaan als gegevensbron voor SAML NameID

|Bron|Id|Description|
|-----|-----|-----|
| Gebruiker | mail|E-mailadres|
| Gebruiker | userprincipalname|User Principal Name|
| Gebruiker | onpremisessamaccountname|On-premises Sam-accountnaam|
| Gebruiker | employeeid|Werknemers-id|
| Gebruiker | extensionattribute1 | Extensiekenmerk 1 |
| Gebruiker | extensionattribute2 | Extensiekenmerk 2 |
| Gebruiker | extensionattribute3 | Extensiekenmerk 3 |
| Gebruiker | extensionattribute4 | Extensiekenmerk 4 |
| Gebruiker | extensionattribute5 | Extensiekenmerk 5 |
| Gebruiker | extensionattribute6 | Extensiekenmerk 6 |
| Gebruiker | extensionattribute7 | Extensiekenmerk 7 |
| Gebruiker | extensionattribute8 | Extensiekenmerk 8 |
| Gebruiker | extensionattribute9 | Extensiekenmerk 9 |
| Gebruiker | extensionattribute10 | Extensiekenmerk 10 |
| Gebruiker | extensionattribute11 | Extensiekenmerk 11 |
| Gebruiker | extensionattribute12 | Extensiekenmerk 12 |
| Gebruiker | extensionattribute13 | Extensiekenmerk 13 |
| Gebruiker | extensionattribute14 | Extensiekenmerk 14 |
| Gebruiker | extensionattribute15 | Extensiekenmerk 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabel 6: Toegestane transformatiemethoden voor SAML NameID

| TransformationMethod | Beperkingen |
| ----- | ----- |
| ExtractMailPrefix | Geen |
| Deelnemen | Het achtervoegsel dat wordt samengevoegd, moet een geverifieerd domein van de resource-tenant zijn. |

### <a name="cross-tenant-scenarios"></a>Scenario's voor meerdere tenants

Beleidsregels voor claimtoewijzing zijn niet van toepassing op gastgebruikers. Als een gastgebruiker toegang probeert te krijgen tot een toepassing met een claimtoewijzingsbeleid dat is toegewezen aan de service-principal, wordt het standaard-token uitgegeven (het beleid heeft geen effect).


## <a name="next-steps"></a>Volgende stappen

- Zie How [to: Customize claims emitted in tokens for a specific app in a tenant (Preview)](active-directory-claims-mapping.md) (Claims aanpassen die worden ingediend in tokens voor een specifieke app in een tenant) voor meer informatie over het aanpassen van de claims die worden ingediend in tokens voor een specifieke toepassing in hun tenant met behulp van PowerShell.
- Zie How to: Customize claims issued in the SAML token for enterprise applications (Claims aanpassen die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen) voor meer informatie over het aanpassen van claims die zijn uitgegeven in het [SAML-token](active-directory-saml-claims-customization.md) via de Azure Portal
- Zie Using directory schema extension attributes in claims (Kenmerken van [directoryschema-extensies](active-directory-schema-extensions.md)gebruiken in claims) voor meer informatie over extensiekenmerken.
