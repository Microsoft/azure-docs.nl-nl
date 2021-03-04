---
title: Voorwaardelijke toegang toevoegen aan een gebruikersstroom in Azure AD B2C
description: Meer informatie over het toevoegen van voorwaardelijke toegang aan uw Azure AD B2C-gebruikersstromen. Configureer instellingen voor meervoudige verificatie (MFA) en beleid voor voorwaardelijke toegang in uw gebruikersstromen om beleid af te dwingen en riskante aanmeldingen te herstellen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6325a890ea297a3aa2bdad76a1d95c10448a7b61
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033907"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Voorwaardelijke toegang toevoegen aan gebruikersstromen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Voorwaardelijke toegang kan worden toegevoegd aan uw Azure Active Directory B2C (Azure AD B2C) gebruikers stromen of aangepaste beleids regels voor het beheren van Risk ante aanmeldingen voor uw toepassingen. Azure Active Directory (Azure AD) voorwaardelijke toegang is het hulp programma dat door Azure AD B2C wordt gebruikt om signalen samen te brengen, beslissingen te nemen en organisatie beleid af te dwingen.

![Stroom voor voorwaardelijke toegang](media/conditional-access-user-flow/conditional-access-flow.png)

Als u een risico analyse met beleids voorwaarden automatiseert, worden Risk ante aanmeldingen onmiddellijk geïdentificeerd en vervolgens hersteld of geblokkeerd.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Overzicht van services

Azure AD B2C evalueert elke aanmeldings gebeurtenis en zorgt ervoor dat aan alle beleids vereisten wordt voldaan voordat de gebruikers toegang wordt verleend. Tijdens deze **evaluatie** fase evalueert de voorwaardelijke toegangs service de signalen die worden verzameld door risico detecties identiteits bescherming tijdens aanmeldings gebeurtenissen. Het resultaat van dit evaluatie proces is een set claims die aangeeft of de aanmelding moet worden verleend of geblokkeerd. Het Azure AD B2C-beleid gebruikt deze claims om een actie binnen de gebruikers stroom uit te voeren, zoals het blok keren van de toegang of de gebruiker met een specifieke herbemiddeling als multi-factor Authentication (MFA). Met ' toegang blok keren ' worden alle andere instellingen overschreven.

::: zone pivot="b2c-custom-policy"
In het volgende voor beeld wordt een technisch profiel voor voorwaardelijke toegang weer gegeven dat wordt gebruikt om de aanmeldings bedreiging te evalueren.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

In de **herstel** fase die volgt, wordt de gebruiker gevraagd met MFA. Zodra dit is voltooid, informeert Azure AD B2C identiteits beveiliging dat de geïdentificeerde aanmeldings bedreiging is hersteld en volgens welke methode. In dit voor beeld geeft Azure AD B2C aan dat de gebruiker de multi-factor Authentication-uitdaging heeft voltooid. 

::: zone pivot="b2c-custom-policy"

In het volgende voor beeld ziet u het technische profiel voor voorwaardelijke toegang dat wordt gebruikt om de geïdentificeerde dreiging te herstellen:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>Onderdelen van de oplossing

Dit zijn de onderdelen voor het inschakelen van voorwaardelijke toegang in Azure AD B2C:

- **Gebruikers stroom** of **aangepast beleid** waarmee de gebruiker wordt begeleid door het aanmeldings-en aanmeldings proces.
- **Beleid voor voorwaardelijke toegang** dat signalen samen brengt om beslissingen te nemen en organisatie beleid af te dwingen. Wanneer een gebruiker zich bij uw toepassing aanmeldt via een Azure AD B2C-beleid, gebruikt het beleid voor voorwaardelijke toegang Azure AD Identity Protection signalen om Risk ante aanmeldingen te identificeren en wordt de juiste herstel actie gepresenteerd.
- **Geregistreerde toepassing** die gebruikers doorstuurt naar de juiste Azure AD B2C gebruikers stroom of aangepast beleid.
- [Tor-Browser](https://www.torproject.org/download/) voor het simuleren van een Risk ante aanmelding.

## <a name="service-limitations-and-considerations"></a>Service beperkingen en overwegingen

Wanneer u de voorwaardelijke toegang van Azure AD gebruikt, moet u rekening houden met het volgende:

- Identiteits beveiliging is beschikbaar voor zowel lokale als sociale identiteiten, zoals Google of Facebook. Voor sociale identiteiten moet u de voorwaardelijke toegang hand matig activeren. Detectie is beperkt omdat de referenties van het sociale account worden beheerd door de externe ID-provider.
- In Azure AD B2C tenants is er slechts een subset van beleids regels voor [voorwaardelijke toegang van Azure AD](../active-directory/conditional-access/overview.md) beschikbaar.


## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Prijscategorie

Azure AD B2C **Premium P2** is vereist om Risk ante aanmeldings beleidsregels te maken. **Premium P1** -tenants kunnen een beleid maken dat is gebaseerd op locatie-, toepassings-, gebruikers-of groeps beleidsregels. Zie [uw Azure AD B2C prijs categorie wijzigen](billing.md#change-your-azure-ad-pricing-tier) voor meer informatie

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Uw Azure AD B2C-Tenant voorbereiden

Schakel de standaard instellingen voor beveiliging uit om een beleid voor voorwaardelijke toegang toe te voegen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
3. Onder **Azure-Services** selecteert u **Azure AD B2C**. Of gebruik het zoekvak om **Azure AD B2C** te zoeken en te selecteren.
4. Selecteer **Eigenschappen** en selecteer vervolgens **Standaardinstellingen voor beveiliging beheren**.

   ![Standaardinstellingen voor beveiliging uitschakelen](media/conditional-access-user-flow/disable-security-defaults.png)

5. Selecteer bij **standaard instellingen voor beveiliging inschakelen** de optie **Nee**.

   ![Stel de Standaardinstellingen voor beveiliging inschakelen in op Nee](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Een beleid voor voorwaardelijke toegang toevoegen

Een beleid voor voorwaardelijke toegang is een if-then-instructie van toewijzingen en toegangs beheer. Een beleid voor voorwaardelijke toegang brengt signalen samen om beslissingen te nemen en organisatie beleid af te dwingen. De logische operator tussen de toewijzingen is *en*. De operator in elke toewijzing is *of*.

![Voorwaardelijke toegang toewijzingen](media/conditional-access-user-flow/conditional-access-assignments.png)

Een beleid voor voorwaardelijke toegang toevoegen:

1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **Voorwaardelijke toegang (preview)** onder **Beveiliging**. De pagina **Beleid voor voorwaardelijke toegang** wordt geopend.
1. Selecteer **+ Nieuw beleid**.
1. Voer een naam in voor het beleid, zoals het *blok keren van Risk ante aanmeldingen*.
1. Kies onder **toewijzingen** de optie **gebruikers en groepen** en selecteer vervolgens een van de volgende ondersteunde configuraties:

    |Opnemen  |Licentie | Notities  |
    |---------|---------|---------|
    |**Alle gebruikers** | P1, P2 |Als u ervoor kiest om **alle gebruikers** op te neemt, geldt dit beleid voor alle gebruikers. Om ervoor te zorgen dat u niet zelf kunt vergren delen, sluit u uw beheerders account uit door **uitsluiten**, **adreslijst rollen** selecteren te kiezen en vervolgens **globale beheerder** te selecteren in de lijst. U kunt ook **gebruikers en groepen** selecteren en vervolgens uw account selecteren in de lijst met **uitgesloten gebruikers selecteren** .  | 
 
1. Selecteer **Cloud-apps of-acties** en **Selecteer vervolgens apps**. Blader naar uw [Relying Party-toepassing](tutorial-register-applications.md).

1. Selecteer **voor waarden** en selecteer vervolgens een van de volgende voor waarden. Selecteer bijvoorbeeld **aanmeldings risico** en **hoog**, **gemiddeld** en **laag** risico niveau.
    
    |Voorwaarde  |Licentie  |Notities  |
    |---------|---------|---------|
    |**Gebruikersrisico**|P2|Gebruikers risico duidt op de kans dat een bepaalde identiteit of een account is aangetast.|
    |**Aanmeldingsrisico**|P2|Aanmeld risico geeft aan dat de kans dat een bepaalde verificatie aanvraag niet is geautoriseerd door de eigenaar van de identiteit.|
    |**Apparaatplatformen**|Niet ondersteund| Gekenmerkt door het besturings systeem dat op een apparaat wordt uitgevoerd. Zie [platform platforms](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms)voor meer informatie.|
    |**Locaties**|P1, P2|Benoemde locaties kunnen de open bare IPv4-netwerk gegevens, het land of de regio of onbekende gebieden bevatten die niet aan specifieke landen of regio's zijn toegewezen. Zie [locaties](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations)voor meer informatie. |
 
1. Onder **Toegangscontroles** selecteert u **Verlenen**. Selecteer vervolgens of u toegang wilt blok keren of verlenen:
    
    |Optie  |Licentie |Notitie  |
    |---------|---------|---------|
    |**Toegang blokkeren**|P1, P2| Hiermee voor komt u toegang op basis van de voor waarden die zijn opgegeven in dit beleid voor voorwaardelijke toegang.|
    |**Toegang verlenen** met **vereisen multi-factor Authentication**|P1, P2|Op basis van de voor waarden die zijn opgegeven in dit beleid voor voorwaardelijke toegang, moet de gebruiker Azure AD B2C multi-factor Authentication door lopen.|

1. Onder **beleid inschakelen** selecteert u een van de volgende opties:
    
    |Optie  |Licentie |Notitie  |
    |---------|---------|---------|
    |**Alleen rapport**|P1, P2| Rapport: alleen beheerders kunnen de impact van beleids regels voor voorwaardelijke toegang evalueren voordat ze in hun omgeving worden ingeschakeld. U wordt aangeraden het beleid met deze status te controleren en de invloed op eind gebruikers te bepalen zonder multi-factor Authentication of gebruikers te blok keren. Zie [resultaten van voorwaardelijke toegang in het controle rapport controleren](#review-conditional-access-outcomes-in-the-audit-report) voor meer informatie.|
    | **Aan**| P1, P2| Het toegangs beleid wordt geëvalueerd en niet afgedwongen. |
    | **Uit** | P1, P2| Het toegangs beleid is niet geactiveerd en heeft geen invloed op de gebruikers. |

1. Schakel uw testbeleid voor voorwaardelijke toegang in door **Maken** te selecteren.

## <a name="add-conditional-access-to-a-user-flow"></a>Voorwaardelijke toegang toevoegen aan een gebruikers stroom

Nadat u het beleid voor voorwaardelijke toegang voor Azure AD hebt toegevoegd, schakelt u voorwaardelijke toegang in uw gebruikers stroom of aangepast beleid in. Wanneer u voorwaardelijke toegang inschakelt, hoeft u geen beleids naam op te geven.

Er kunnen op elk moment meerdere beleids regels voor voorwaardelijke toegang van toepassing zijn op een afzonderlijke gebruiker. In dit geval heeft het meest strikte toegangscontrole beleid prioriteit. Als voor één beleid bijvoorbeeld multi-factor Authentication (MFA) is vereist, terwijl de andere toegang blokkeert, wordt de gebruiker geblokkeerd.

## <a name="enable-multi-factor-authentication-optional"></a>Multi-factor Authentication inschakelen (optioneel)

Wanneer u voorwaardelijke toegang toevoegt aan een gebruikers stroom, overweeg dan het gebruik van **multi-factor Authentication (MFA)**. Gebruikers kunnen gebruikmaken van een eenmalige code via SMS of voice, of een eenmalig wacht woord via e-mail voor multi-factor Authentication. MFA-instellingen zijn onafhankelijk van de instellingen voor voorwaardelijke toegang. U kunt MFA instellen op **Altijd aan** zodat MFA altijd vereist is, ongeacht de instelling van de voorwaardelijke toegang. U kunt ook MFA instellen op **Voorwaardelijk**, zodat MFA alleen vereist is wanneer een beleid voor voorwaardelijke toegang vereist is.

> [!IMPORTANT]
> Als uw beleid voor voorwaardelijke toegang toegang verleent aan MFA, maar de gebruiker geen telefoon nummer heeft inge schreven, wordt de gebruiker mogelijk geblokkeerd.

::: zone pivot="b2c-user-flow"

Als u voorwaardelijke toegang voor een gebruikers stroom wilt inschakelen, moet u ervoor zorgen dat de versie ondersteuning biedt voor voorwaardelijke toegang. Deze gebruikersstroomversies zijn gelabeld **Aanbevolen**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

1. Onder **Azure-Services** selecteert u **Azure AD B2C**. Of gebruik het zoekvak om **Azure AD B2C** te zoeken en te selecteren.

1. Selecteer onder **Beleid** **Gebruikersstromen**. Selecteer daarna de gebruikersstroom.

1. Selecteer **Eigenschappen** en zorg ervoor dat de gebruikers stroom voorwaardelijke toegang ondersteunt door te zoeken naar de instelling met de naam **voorwaardelijke toegang**.
 
   ![MFA en voorwaardelijke toegang configureren in Eigenschappen](media/conditional-access-user-flow/add-conditional-access.png)

1. Selecteer in de sectie **multi-factor Authentication** de gewenste **MFA-methode** en selecteer vervolgens onder **MFA afdwingen** **voorwaardelijk (aanbevolen)**.
 
1. Schakel in de sectie **Voorwaardelijke toegang** het selectievakje **Beleid voor voorwaardelijke toegang afdwingen** in.

1. Selecteer **Opslaan**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Voorwaardelijke toegang toevoegen aan uw beleid

1. Bekijk het voor beeld van een beleid voor voorwaardelijke toegang op [github](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
1. Vervang in elk bestand de teken reeks door `yourtenant` de naam van uw Azure AD B2C-Tenant. Als de naam van uw B2C-Tenant bijvoorbeeld *contosob2c* is, worden alle exemplaren van `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. Upload de beleids bestanden.

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Selecteer de `B2C_1A_signup_signin_with_ca` pagina of het `B2C_1A_signup_signin_with_ca_whatif` beleid om het overzicht te openen. Selecteer vervolgens **gebruikers stroom uitvoeren**. Selecteer *webapp1* onder **Toepassing**. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Kopieer de URL onder **Eindpunt voor gebruikersstroom uitvoeren**.

1. Als u een Risk ante aanmelding wilt simuleren, opent u de [Tor-Browser](https://www.torproject.org/download/) en gebruikt u de URL die u in de vorige stap hebt gekopieerd om u aan te melden bij de geregistreerde app.

1. Voer de gevraagde informatie in op de aanmeldingspagina en probeer u aan te melden. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven. In het gedecodeerde token van jwt.ms moet u zien dat de aanmelding is geblokkeerd.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Uw gebruikers stroom testen

1. Selecteer de gebruikers stroom die u hebt gemaakt om de pagina overzicht te openen en selecteer vervolgens **gebruikers stroom uitvoeren**. Selecteer *webapp1* onder **Toepassing**. De **antwoord-URL** moet `https://jwt.ms` weergeven.

1. Kopieer de URL onder **Eindpunt voor gebruikersstroom uitvoeren**.

1. Als u een Risk ante aanmelding wilt simuleren, opent u de [Tor-Browser](https://www.torproject.org/download/) en gebruikt u de URL die u in de vorige stap hebt gekopieerd om u aan te melden bij de geregistreerde app.

1. Voer de gevraagde informatie in op de aanmeldingspagina en probeer u aan te melden. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven. In het gedecodeerde token van jwt.ms moet u zien dat de aanmelding is geblokkeerd.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Bekijk de resultaten voor voorwaardelijke toegang in het controle rapport

Het resultaat van een voorwaardelijke toegangsgebeurtenis bekijken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

3. Onder **Azure-Services** selecteert u **Azure AD B2C**. Of gebruik het zoekvak om **Azure AD B2C** te zoeken en te selecteren.

4. Onder **Activiteiten** selecteert u **Controlelogboeken**.

5. Filter het controlelogboek door **Categorie** in te stellen op **B2C** en **Activiteit-resourcetype** op **IdentityProtection**. Selecteer vervolgens **Toepassen**.

6. Controleer de controle activiteit tot de afgelopen zeven dagen. De volgende typen activiteiten zijn opgenomen:

   - **Beleidsregels voor voorwaardelijke toegang evalueren**: Deze vermelding in het controlelogboek geeft aan dat er een evaluatie van voorwaardelijke toegang is uitgevoerd tijdens een verificatie.
   - **Gebruiker herstellen**: deze vermelding geeft aan dat aan de subsidie of vereisten van een beleid voor voorwaardelijke toegang is voldaan door de eind gebruiker, en dat deze activiteit is gerapporteerd aan de risico-Engine om het risico van de gebruiker te beperken (verminderen).

7. Selecteer een **Beleid voor voorwaardelijke toegang evalueren**-logboekvermelding in de lijst om de details van **-activiteit te openen: Controlelogboek**-pagina, waarin de audit logboek-id's worden weer gegeven, samen met deze informatie in de sectie **Aanvullende details**:

   - **ConditionalAccessResult**: de toekenning die is vereist voor de evaluatie van het voorwaardelijke beleid.
   - **AppliedPolicies**: een lijst met alle beleids regels voor voorwaardelijke toegang waarin aan de voor waarden is voldaan en het beleid is ingeschakeld.
   - **ReportingPolicies**: een lijst met beleids regels voor voorwaardelijke toegang die zijn ingesteld op de modus alleen rapport en waar aan de voor waarden is voldaan.

## <a name="next-steps"></a>Volgende stappen

[Pas de gebruikersinterface aan in een Azure AD B2C-gebruikersstroom](customize-ui-with-html.md)
