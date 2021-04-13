---
title: Configureerbare levensduur van token
titleSuffix: Microsoft identity platform
description: Meer informatie over het instellen van levensduur voor toegangs-, SAML- en id-tokens die zijn uitgegeven door het Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363968"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Configureerbare levensduur van token in het Microsoft Identity Platform (preview)

U kunt de levensduur opgeven van een toegangs-, id- of SAML-token dat is uitgegeven door het Microsoft Identity Platform. U kunt de levensduur van een token instellen voor alle apps in uw organisatie, voor een multitenanttoepassing (voor meerdere organisaties) of voor een specifieke service-principal in uw organisatie. We bieden momenteel echter geen ondersteuning voor het configureren van de levensduur van het token voor [service-principals voor beheerde identiteiten.](../managed-identities-azure-resources/overview.md)

In Azure AD vertegenwoordigt een beleidsobject een set regels die worden afgedwongen voor afzonderlijke toepassingen of voor alle toepassingen in een organisatie. Elk beleidstype heeft een unieke structuur, met een set eigenschappen die worden toegepast op objecten waaraan ze zijn toegewezen.

U kunt een beleid aanwijzen als het standaardbeleid voor uw organisatie. Het beleid wordt toegepast op elke toepassing in de organisatie, zolang het niet wordt overgenomen door een beleid met een hogere prioriteit. U kunt ook een beleid toewijzen aan specifieke toepassingen. De volgorde van de prioriteit varieert per beleidstype.

Lees voorbeelden van het [configureren van levensduur van token voor voorbeelden.](configure-token-lifetimes.md)

> [!NOTE]
> Configureerbaar levensduurbeleid voor token is alleen van toepassing op mobiele clients en desktop-clients die toegang hebben tot SharePoint Online en OneDrive voor Bedrijven-resources, en is niet van toepassing op webbrowsersessies.
> Als u de levensduur van webbrowsersessies voor SharePoint Online en OneDrive voor Bedrijven wilt beheren, gebruikt u de functie sessielevensduur voor [voorwaardelijke](../conditional-access/howto-conditional-access-session-lifetime.md) toegang. Raadpleeg de [SharePoint Online-blog voor](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) meer informatie over het configureren van time-outs voor inactieve sessies.

## <a name="license-requirements"></a>Licentievereisten

Voor deze functie hebt u een Azure AD Premium P1-licentie nodig. Zie Comparing generally available features of the Free and Premium editions (Algemeen beschikbare functies van de gratis en [Premium-edities](https://azure.microsoft.com/pricing/details/active-directory/)vergelijken) voor meer informatie over de juiste licentie voor uw vereisten.

Klanten met een [Microsoft 365 Business-licentie](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) hebben ook toegang tot de functies voor voorwaardelijke toegang.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Beleid voor levensduur van tokens voor toegang, SAML en id-tokens

U kunt levensduurbeleid voor tokens, SAML-tokens en id-tokens instellen.

### <a name="access-tokens"></a>Toegangstokens

Clients gebruiken toegangstokens voor toegang tot een beveiligde resource. Een toegangs token kan alleen worden gebruikt voor een specifieke combinatie van gebruiker, client en resource. Toegangstokens kunnen niet worden ingetrokken en zijn geldig tot de vervaldatum. Een kwaadwillende actor die een toegangs token heeft verkregen, kan dit gebruiken voor de levensduur. Het aanpassen van de levensduur van een toegangtoken is een afweging tussen het verbeteren van de systeemprestaties en het verlengen van de tijd die de client toegang behoudt nadat het account van de gebruiker is uitgeschakeld. Verbeterde systeemprestaties worden bereikt door het aantal keren te verminderen dat een client een nieuw toegang token moet verkrijgen.  De standaardwaarde is afhankelijk van de clienttoepassing die het token aanvraagt. Clients die met CAE (Continue toegangsevaluatie) kunnen onderhandelen over CAE-aware sessies, zien bijvoorbeeld een levensduur van een token met een lange levensduur (maximaal 28 uur). Nadat het token is verlopen, moet de client het vernieuwings-token gebruiken om (meestal op de stille manier) een nieuw vernieuwings-token en toegangs token te verkrijgen.

### <a name="saml-tokens"></a>SAML-tokens

SAML-tokens worden gebruikt door veel webgebaseerde SaaS-toepassingen en worden verkregen Azure Active Directory het SAML2-protocol-eindpunt van de Azure Active Directory. Ze worden ook gebruikt door toepassingen die gebruikmaken van WS-Federation. De standaardlevensduur van het token is 1 uur. Vanuit het perspectief van een toepassing wordt de geldigheidsperiode van het token opgegeven door de waarde NotOnOrAfter van het `<conditions …>` element in het token. Nadat de geldigheidsperiode van het token is beëindigd, moet de client een nieuwe verificatieaanvraag initiëren, die vaak wordt voldaan zonder interactieve aanmelding als gevolg van het token voor eenmalige aanmelding (SSO) Session.

De waarde van NotOnOrAfter kan worden gewijzigd met behulp van `AccessTokenLifetime` de parameter in een `TokenLifetimePolicy` . Deze wordt ingesteld op de levensduur die is geconfigureerd in het beleid, indien van invloed, plus een scheefheidsfactor van vijf minuten.

De onderwerpbevestiging NotOnOrAfter opgegeven in het `<SubjectConfirmationData>` element wordt niet beïnvloed door de token levensduur configuratie. 

### <a name="id-tokens"></a>Id-tokens

Id-tokens worden doorgegeven aan websites en native clients. Id-tokens bevatten profielgegevens over een gebruiker. Een id-token is gebonden aan een specifieke combinatie van gebruiker en client. Id-tokens worden beschouwd als geldig totdat ze verlopen. Normaal gesproken komt een webtoepassing overeen met de sessielevensduur van een gebruiker in de toepassing met de levensduur van het id-token dat voor de gebruiker is uitgegeven. U kunt de levensduur van een id-token aanpassen om te bepalen hoe vaak de toepassingssessie verloopt en hoe vaak de gebruiker opnieuw moet worden geverifieerd bij het Microsoft Identity Platform (op de hoogte of interactief).

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Levensduurbeleid voor tokens en sessietokens vernieuwen

U kunt geen beleidsregels voor de levensduur van tokens voor tokens voor vernieuwen en sessietokens instellen.

> [!IMPORTANT]
> Vanaf 30 januari 2021 kunt u de levensduur van het vernieuwings- en sessie-token niet meer configureren. Azure Active Directory de configuratie van het vernieuwings- en sessie-token in bestaande beleidsregels niet langer in ere houden.  Nieuwe tokens die zijn uitgegeven nadat bestaande tokens zijn verlopen, zijn nu ingesteld op de [standaardconfiguratie](#configurable-token-lifetime-properties). U kunt nog steeds de levensduur van de toegangs-, SAML- en ID-token configureren na de configuratie van het vernieuwings- en sessie-token.
>
> De levensduur van een bestaand token wordt niet gewijzigd. Nadat deze zijn verlopen, wordt er een nieuw token uitgegeven op basis van de standaardwaarde.
>
> Als u de periode wilt blijven definiëren voordat een gebruiker wordt gevraagd zich opnieuw aan te melden, configureert u de aanmeldingsfrequentie in Voorwaardelijke toegang. Lees Configure authentication session management with Conditional Access (Beheer [van verificatiesessies configureren met voorwaardelijke toegang) voor meer informatie over voorwaardelijke toegang.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="configurable-token-lifetime-properties"></a>Configureerbare levensduureigenschappen van token
Een levensduurbeleid voor een token is een type beleidsobject dat levensduurregels voor het token bevat. Met dit beleid bepaalt u hoe lang toegangs-, SAML- en id-tokens voor deze resource als geldig worden beschouwd. Beleidsregels voor de levensduur van tokens kunnen niet worden ingesteld voor vernieuwings- en sessietokens. Als er geen beleid is ingesteld, dwingt het systeem de standaard levensduurwaarde af.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Eigenschappen van het beleid voor toegang, id en levensduur van SAML2-token

Het verminderen van de eigenschap Levensduur van toegangs token vermindert het risico dat een toegangs token of id-token wordt gebruikt door een kwaadwillende actor voor een langere periode. (Deze tokens kunnen niet worden ingetrokken.) Het afweging is dat de prestaties nadelig worden beïnvloed, omdat de tokens vaker moeten worden vervangen.

Zie Create [a policy for web sign-in (Een beleid maken voor web-aanmelding) voor een voorbeeld.](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)

Toegang, id en SAML2-tokenconfiguratie worden beïnvloed door de volgende eigenschappen en hun respectievelijke setwaarden:

- **Eigenschap:** Levensduur van toegangs token
- **Tekenreeks van beleids eigenschap:** AccessTokenLifetime
- **Beïnvloedt:** toegangstokens, id-tokens, SAML2-tokens
- **Standaard:**
    - Toegangstokens: varieert, afhankelijk van de clienttoepassing die het token aanvraagt. Clients die geschikt zijn voor CAE(Continue toegangsevaluatie) die cae-aware sessies onderhandelen, zien bijvoorbeeld een levensduur van een token met een lange levensduur (maximaal 28 uur).
    - Id-tokens, SAML2-tokens: 1 uur
- **Minimaal**: 10 minuten
- **Maximum:** 1 dag

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Eigenschappen van beleid voor vernieuwing en levensduur van sessie-token

De configuratie van het vernieuwings- en sessie-token wordt beïnvloed door de volgende eigenschappen en hun respectievelijke in te stellen waarden. Na het stoppen van de configuratie van het vernieuwings- en sessie-token op 30 januari 2021, worden in Azure AD alleen de standaardwaarden die hieronder worden beschreven, in ere gehouden. Als u besluit [](../conditional-access/howto-conditional-access-session-lifetime.md) om voorwaardelijke toegang niet te gebruiken voor het beheren van de aanmeldingsfrequentie, worden uw vernieuwings- en sessietokens ingesteld op de standaardconfiguratie op die datum en kunt u hun levensduur niet meer wijzigen.  

|Eigenschap   |Tekenreeks van beleids eigenschap    |Beïnvloedt |Standaard |
|----------|-----------|------------|------------|
|Maximum inactieve tijd token vernieuwen |MaxInactiveTime  |Tokens vernieuwen |90 dagen  |
|Single-Factor maximale leeftijd van token vernieuwen  |MaxAgeSingleFactor  |Tokens vernieuwen (voor alle gebruikers)  |Tot ingetrokken  |
|Maximale leeftijd van Multi-Factor Refresh-token  |MaxAgeMultiFactor  |Tokens vernieuwen (voor alle gebruikers) |Tot ingetrokken  |
|Single-Factor maximale leeftijd van sessie-token  |MaxAgeSessionSingleFactor |Sessietokens (permanent en niet-persistent)  |Tot ingetrokken |
|Maximale leeftijd van Multi-Factor Session-token  |MaxAgeSessionMultiFactor  |Sessietokens (permanent en niet-persistent)  |Tot ingetrokken |

U kunt PowerShell gebruiken om de beleidsregels te vinden die worden beïnvloed door de pensioen.  Gebruik de [PowerShell-cmdlets](configure-token-lifetimes.md#get-started) om alle beleidsregels te zien die in uw organisatie zijn gemaakt, of om te zien welke apps en service-principals zijn gekoppeld aan een specifiek beleid.

## <a name="policy-evaluation-and-prioritization"></a>Beleidsevaluatie en prioritering
U kunt beleid voor de levensduur van een token maken en vervolgens toewijzen aan een specifieke toepassing, aan uw organisatie en aan service-principals. Er kunnen meerdere beleidsregels van toepassing zijn op een specifieke toepassing. Het beleid voor de levensduur van het token dat van kracht wordt, volgt deze regels:

* Als een beleid expliciet wordt toegewezen aan de service-principal, wordt dit afgedwongen.
* Als er geen beleid expliciet is toegewezen aan de service-principal, wordt een beleid afgedwongen dat expliciet is toegewezen aan de bovenliggende organisatie van de service-principal.
* Als er geen beleid expliciet wordt toegewezen aan de service-principal of aan de organisatie, wordt het beleid afgedwongen dat aan de toepassing is toegewezen.
* Als er geen beleid is toegewezen aan de service-principal, de organisatie of het toepassingsobject, worden de standaardwaarden afgedwongen. (Zie de tabel in [Configurable token lifetime properties](#configurable-token-lifetime-properties).)

Zie Application and service principal objects in Azure Active Directory voor meer informatie over de relatie tussen toepassingsobjecten [en service-principalobjecten.](app-objects-and-service-principals.md)

De geldigheid van een token wordt geëvalueerd op het moment dat het token wordt gebruikt. Het beleid met de hoogste prioriteit voor de toepassing die wordt gebruikt, wordt van kracht.

Alle periodes die hier worden gebruikt, worden opgemaakt volgens het C# [TimeSpan-object](/dotnet/api/system.timespan) - D.HH:MM:SS.  80 dagen en 30 minuten is dus `80.00:30:00` .  De vooraanstaand D kan worden weggevallen als nul, dus 90 minuten zou `00:90:00` zijn.  

## <a name="cmdlet-reference"></a>Cmdlet-naslaginformatie

Dit zijn de cmdlets in de [module Azure Active Directory PowerShell voor Graph Preview.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)

### <a name="manage-policies"></a>Beleid beheren

U kunt de volgende cmdlets gebruiken om beleid te beheren.

| Cmdlet | Beschrijving | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee maakt u een nieuw beleid. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee haalt u alle Azure AD-beleidsregels of een opgegeven beleid op. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Haalt alle apps en service-principals op die zijn gekoppeld aan een beleid. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Werkt een bestaand beleid bij. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee verwijdert u het opgegeven beleid. |

### <a name="application-policies"></a>Toepassingsbeleid
U kunt de volgende cmdlets gebruiken voor toepassingsbeleid.</br></br>

| Cmdlet | Beschrijving | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee koppelt u het opgegeven beleid aan een toepassing. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Haalt het beleid op dat is toegewezen aan een toepassing. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee verwijdert u een beleid uit een toepassing. |

### <a name="service-principal-policies"></a>Beleid voor service-principals
U kunt de volgende cmdlets gebruiken voor beleidsregels voor service-principals.

| Cmdlet | Beschrijving | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee koppelt u het opgegeven beleid aan een service-principal. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee haalt u een beleid dat is gekoppeld aan de opgegeven service-principal.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee verwijdert u het beleid van de opgegeven service-principal.|

## <a name="next-steps"></a>Volgende stappen

Lees voorbeelden van het [configureren van levensduur](configure-token-lifetimes.md)van token voor meer informatie.
