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
ms.openlocfilehash: 0e6a872891f09f60ea963fa783e6f49dc4e94a54
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861837"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Voorwaardelijke toegang toevoegen aan gebruikersstromen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Voorwaardelijke toegang kan worden toegevoegd aan Azure Active Directory B2C (Azure AD B2C) gebruikersstromen of aangepaste beleidsregels voor het beheren van riskante aanmeldingen voor uw toepassingen. Azure Active Directory (Azure AD) Voorwaardelijke toegang is het hulpprogramma dat wordt gebruikt door Azure AD B2C om signalen samen te brengen, beslissingen te nemen en organisatiebeleid af te dwingen.

![Stroom voor voorwaardelijke toegang](media/conditional-access-user-flow/conditional-access-flow.png)

Het automatiseren van een risicoanalyse met beleidsvoorwaarden betekent dat riskante aanmeldingen onmiddellijk worden geïdentificeerd en vervolgens worden verwijderd of geblokkeerd.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Overzicht van services

Azure AD B2C elke aanmeldingsgebeurtenis evalueert en ervoor zorgt dat aan alle beleidsvereisten wordt voldaan voordat de gebruiker toegang wordt verleent. Tijdens deze **evaluatiefase** evalueert de service voor voorwaardelijke toegang de signalen die tijdens aanmeldingsgebeurtenissen zijn verzameld door identity protection-risicodetecties. Het resultaat van dit evaluatieproces is een set claims die aangeeft of de aanmelding moet worden verleend of geblokkeerd. Het Azure AD B2C maakt gebruik van deze claims om actie te ondernemen binnen de gebruikersstroom, zoals het blokkeren van toegang of het uitdagen van de gebruiker met een specifiek herstel, zoals meervoudige verificatie (MFA). Toegang blokkeren overschrijven alle andere instellingen.

::: zone pivot="b2c-custom-policy"
In het volgende voorbeeld ziet u een technisch profiel voor voorwaardelijke toegang dat wordt gebruikt om de aanmeldingsbedreiging te evalueren.

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

In de **herstelfase** die volgt, krijgt de gebruiker MFA te zien. Zodra dit is voltooid, Azure AD B2C Identity Protection melden dat de geïdentificeerde aanmeldingsbedreiging is verijdelen en met welke methode. In dit voorbeeld geeft Azure AD B2C aan dat de gebruiker de meervoudige verificatie-uitdaging heeft voltooid. 

::: zone pivot="b2c-custom-policy"

In het volgende voorbeeld ziet u een technisch profiel voor voorwaardelijke toegang dat wordt gebruikt om de geïdentificeerde bedreiging te herstellen:

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

Dit zijn de onderdelen die voorwaardelijke toegang inschakelen in Azure AD B2C:

- **Gebruikersstroom** of **aangepast beleid dat** de gebruiker door het aanmeldings- en aanmeldingsproces leidt.
- **Beleid voor voorwaardelijke** toegang dat signalen samen brengt om beslissingen te nemen en organisatiebeleid af te dwingen. Wanneer een gebruiker zich via een Azure AD B2C-beleid bij uw toepassing meldt, gebruikt het beleid voor voorwaardelijke toegang Azure AD Identity Protection-signalen om riskante aanmeldingen te identificeren en wordt de juiste herstelactie gegeven.
- **Geregistreerde toepassing** die gebruikers naar het juiste Azure AD B2C gebruikersstroom of aangepast beleid leidt.
- [TOR Browser](https://www.torproject.org/download/) voor het simuleren van een riskante aanmelding.

## <a name="service-limitations-and-considerations"></a>Servicebeperkingen en overwegingen

Wanneer u de voorwaardelijke toegang van Azure AD gebruikt, moet u rekening houden met het volgende:

- Identity Protection is beschikbaar voor zowel lokale als sociale identiteiten, zoals Google of Facebook. Voor sociale identiteiten moet u handmatig voorwaardelijke toegang activeren. Detectie is beperkt omdat referenties voor sociale netwerken worden beheerd door de externe id-provider.
- In Azure AD B2C tenants is slechts een subset van het beleid voor voorwaardelijke toegang [van Azure AD](../active-directory/conditional-access/overview.md) beschikbaar.


## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Prijscategorie

Azure AD B2C Premium **P2** is vereist voor het maken van beleid voor riskante aanmeldingen. **Premium P1-tenants** kunnen een beleid maken dat is gebaseerd op locatie-, toepassings-, gebruikers- of groepsbeleid. Zie Uw Azure AD B2C [wijzigen voor meer informatie](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Uw tenant Azure AD B2C voorbereiden

Als u een beleid voor voorwaardelijke toegang wilt toevoegen, schakelt u de standaardinstellingen voor beveiliging uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
3. Selecteer **onder Azure-services** **de Azure AD B2C**. Of gebruik het zoekvak om te zoeken en selecteer **Azure AD B2C.**
4. Selecteer **Eigenschappen** en selecteer vervolgens **Standaardinstellingen voor beveiliging beheren**.

   ![Standaardinstellingen voor beveiliging uitschakelen](media/conditional-access-user-flow/disable-security-defaults.png)

5. Selecteer **onder Standaardinstellingen voor beveiliging inschakelen** de **optie Nee.**

   ![Stel de Standaardinstellingen voor beveiliging inschakelen in op Nee](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Een beleid voor voorwaardelijke toegang toevoegen

Een beleid voor voorwaardelijke toegang is een if-then-instructie van toewijzingen en toegangsbesturingselementen. Een beleid voor voorwaardelijke toegang brengt signalen samen om beslissingen te nemen en organisatiebeleid af te dwingen. De logische operator tussen de toewijzingen is *En*. De operator in elke toewijzing is *Of*.

![Toewijzingen voor voorwaardelijke toegang](media/conditional-access-user-flow/conditional-access-assignments.png)

Een beleid voor voorwaardelijke toegang toevoegen:

1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **Voorwaardelijke toegang (preview)** onder **Beveiliging**. De pagina **Beleid voor voorwaardelijke toegang** wordt geopend.
1. Selecteer **+ Nieuw beleid.**
1. Voer een naam in voor het beleid, zoals *Riskante aanmelding blokkeren.*
1. Kies **onder Toewijzingen** **de optie Gebruikers en groepen** en selecteer vervolgens een van de volgende ondersteunde configuraties:

    |Opnemen  |Licentie | Notities  |
    |---------|---------|---------|
    |**Alle gebruikers** | P1, P2 |Als u ervoor kiest om Alle **gebruikers op te nemen,** is dit beleid van invloed op al uw gebruikers. Sluit uzelf niet af door uw beheerdersaccount uit te sluiten door Uitsluiten te **kiezen,** **Directoryrollen** te selecteren en vervolgens Globale beheerder te **selecteren** in de lijst. U kunt ook Gebruikers **en groepen selecteren en** vervolgens uw account selecteren in de lijst Uitgesloten **gebruikers** selecteren.  | 
 
1. Selecteer **Cloud-apps of -acties** en vervolgens **Apps selecteren.** Blader naar uw [relying party-toepassing](tutorial-register-applications.md).

1. Selecteer **Voorwaarden** en selecteer vervolgens een van de volgende voorwaarden. Selecteer bijvoorbeeld **Aanmeldingsrisico en** **Hoog,** **Gemiddeld** en **Laag** risiconiveau.
    
    |Voorwaarde  |Licentie  |Notities  |
    |---------|---------|---------|
    |**Gebruikersrisico**|P2|Gebruikersrisico vertegenwoordigt de kans dat een bepaalde identiteit of account is aangetast.|
    |**Aanmeldingsrisico**|P2|Aanmeldingsrisico geeft de waarschijnlijkheid aan dat een bepaalde verificatieaanvraag niet is geautoriseerd door de identiteitseigenaar.|
    |**Apparaatplatformen**|Niet ondersteund| Wordt gekenmerkt door het besturingssysteem dat wordt uitgevoerd op een apparaat. Zie Apparaatplatformen voor [meer informatie.](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms)|
    |**Locaties**|P1, P2|Benoemde locaties kunnen de openbare IPv4-netwerkgegevens, het land of de regio of onbekende gebieden bevatten die niet zijn toe te staan aan specifieke landen of regio's. Zie Locaties voor [meer informatie.](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations) |
 
1. Onder **Toegangscontroles** selecteert u **Verlenen**. Selecteer vervolgens of u toegang wilt blokkeren of verlenen:
    
    |Optie  |Licentie |Notitie  |
    |---------|---------|---------|
    |**Toegang blokkeren**|P1, P2| Hiermee voorkomt u toegang op basis van de voorwaarden die zijn opgegeven in dit beleid voor voorwaardelijke toegang.|
    |**Toegang verlenen** met **Meervoudige verificatie vereisen**|P1, P2|Op basis van de voorwaarden die zijn opgegeven in dit beleid voor voorwaardelijke toegang, moet de gebruiker de Azure AD B2C multi-factor authentication door te voeren.|

1. Selecteer **onder Beleid inschakelen** een van de volgende opties:
    
    |Optie  |Licentie |Notitie  |
    |---------|---------|---------|
    |**Alleen rapport**|P1, P2| Met Alleen rapport kunnen beheerders de impact van beleid voor voorwaardelijke toegang evalueren voordat ze deze in hun omgeving inschakelen. U wordt aangeraden het beleid met deze status te controleren en de impact op eindgebruikers te bepalen zonder meervoudige verificatie of blokkerende gebruikers te vereisen. Zie Resultaten van voorwaardelijke toegang controleren in het [controlerapport voor meer informatie](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Aan**| P1, P2| Het toegangsbeleid wordt geëvalueerd en niet afgedwongen. |
    | **Uit** | P1, P2| Het toegangsbeleid is niet geactiveerd en heeft geen invloed op de gebruikers. |

1. Schakel uw testbeleid voor voorwaardelijke toegang in door **Maken** te selecteren.

## <a name="add-conditional-access-to-a-user-flow"></a>Voorwaardelijke toegang toevoegen aan een gebruikersstroom

Nadat u het beleid voor voorwaardelijke toegang van Azure AD hebt toegevoegd, moet u voorwaardelijke toegang inschakelen in uw gebruikersstroom of aangepast beleid. Wanneer u voorwaardelijke toegang inschakelen, hoeft u geen beleidsnaam op te geven.

Meerdere beleidsregels voor voorwaardelijke toegang kunnen op elk moment van toepassing zijn op een afzonderlijke gebruiker. In dit geval heeft het strengste beleid voor toegangsbeheer voorrang. Als voor één beleid bijvoorbeeld Meervoudige verificatie (MFA) is vereist, wordt de gebruiker geblokkeerd terwijl de toegang wordt geblokkeerd door het andere beleid.

## <a name="conditional-access-template-1-sign-in-risk-based-conditional-access"></a>Sjabloon 1 voor voorwaardelijke toegang: voorwaardelijke toegang op basis van aanmeldingsrisico

De meeste gebruikers vertonen normaal gedrag dat kan worden getraceerd. Wanneer ze buiten de norm hiervoor vallen, zou het riskant kunnen zijn om hen toe te staan zich zomaar aan te melden. U kunt die gebruiker blokkeren of alleen vragen om meervoudige verificatie uit te voeren om te bewijzen dat ze echt zijn wie ze zeggen te zijn.

Een aanmeldingsrisico vertegenwoordigt de waarschijnlijkheid dat een bepaalde verificatieaanvraag niet is geautoriseerd door de identiteitseigenaar. Organisaties met P2-licenties kunnen beleid voor voorwaardelijke toegang maken met Azure AD Identity Protection [detectie van aanmeldingsrisico's.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk) Houd rekening met [de beperkingen voor Identity Protection-detecties voor B2C](https://docs.microsoft.com/azure/active-directory-b2c/identity-protection-investigate-risk?pivots=b2c-user-flow#service-limitations-and-considerations).

Als er een risico wordt gedetecteerd, kunnen gebruikers meervoudige verificatie uitvoeren om de riskante aanmeldingsgebeurtenis zelf te herstellen en te sluiten om onnodige ruis voor beheerders te voorkomen.

Organisaties moeten een van de volgende opties kiezen om een beleid voor voorwaardelijke toegang op basis van aanmeldingsrisico's in te schakelen waarvoor meervoudige verificatie (MFA) is vereist wanneer het aanmeldingsrisico gemiddeld of hoog is.

### <a name="enable-with-conditional-access-policy"></a>Inschakelen met beleid voor voorwaardelijke toegang

1. Meld u aan bij **Azure Portal**.
2. Blader naar **Azure AD B2C**  >  **beveiliging voor**  >  **voorwaardelijke toegang.**
3. Selecteer **Nieuw beleid.**
4. Geef uw beleid een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
5. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
   1. Selecteer **onder Opnemen** de optie Alle **gebruikers.**
   2. Selecteer **onder Uitsluiten** de optie Gebruikers en **groepen** en kies de accounts voor noodtoegang of break-glass van uw organisatie. 
   3. Selecteer **Gereed**.
6. Selecteer **onder Cloud-apps of**  >  **-acties Opnemen** **de optie Alle cloud-apps.**
7. Stel **onder**  >  **Aanmeldingsrisicovoorwaarden** configureren **in op** **Ja.** Onder **Selecteer het aanmeldingsrisiconiveau is dit beleid van toepassing op** 
   1. Selecteer **Hoog** en **Gemiddeld.**
   2. Selecteer **Gereed**.
8. Selecteer **onder**  >  **Toegangsbesturingselementen** **Verlenen de optie Toegang verlenen,** **Meervoudige verificatie vereisen** en selecteer **Selecteren.**
9. Bevestig uw instellingen en stel **Beleid inschakelen in** op **Aan.**
10. Selecteer **Maken om** uw beleid in te stellen.

### <a name="enable-with-conditional-access-apis"></a>Inschakelen met API's voor voorwaardelijke toegang

Raadpleeg de documentatie voor API's voor voorwaardelijke toegang voor het maken van een beleid voor voorwaardelijke toegang op basis van [aanmeldingsrisico's met API's voor voorwaardelijke toegang.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-apis#graph-api)

De volgende sjabloon kan worden gebruikt voor het maken van een beleid voor voorwaardelijke toegang met de weergavenaam 'CA002: MFA vereisen voor gemiddeld+ aanmeldingsrisico' in de modus alleen-rapporteren.

```json
{
    "displayName": "Template 1: Require MFA for medium+ sign-in risk",
    "state": "enabledForReportingButNotEnforced",
    "conditions": {
        "signInRiskLevels": [ "high" ,
            "medium"
        ],
        "applications": {
            "includeApplications": [
                "All"
            ]
        },
        "users": {
            "includeUsers": [
                "All"
            ],
            "excludeUsers": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ]
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ]
    }
}
```

## <a name="enable-multi-factor-authentication-optional"></a>Meervoudige verificatie inschakelen (optioneel)

Wanneer u voorwaardelijke toegang toevoegt aan een gebruikersstroom, kunt u het gebruik van **Multi-Factor Authentication (MFA) overwegen.** Gebruikers kunnen een eendimensionale code gebruiken via sms of spraak, of een een keer wachtwoord via e-mail voor meervoudige verificatie. MFA-instellingen zijn onafhankelijk van de instellingen voor voorwaardelijke toegang. U kunt MFA instellen op **Altijd aan** zodat MFA altijd vereist is, ongeacht de instelling van de voorwaardelijke toegang. U kunt ook MFA instellen op **Voorwaardelijk**, zodat MFA alleen vereist is wanneer een beleid voor voorwaardelijke toegang vereist is.

> [!IMPORTANT]
> Als uw beleid voor voorwaardelijke toegang toegang verleent met MFA, maar de gebruiker geen telefoonnummer heeft geregistreerd, wordt de gebruiker mogelijk geblokkeerd.

::: zone pivot="b2c-user-flow"

Als u voorwaardelijke toegang voor een gebruikersstroom wilt inschakelen, moet u ervoor zorgen dat de versie voorwaardelijke toegang ondersteunt. Deze gebruikersstroomversies zijn gelabeld **Aanbevolen**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

1. Selecteer **onder Azure-services** **de optie Azure AD B2C**. Of gebruik het zoekvak om te zoeken en te **Azure AD B2C.**

1. Selecteer onder **Beleid** **Gebruikersstromen**. Selecteer daarna de gebruikersstroom.

1. Selecteer **Eigenschappen** en zorg ervoor dat de gebruikersstroom voorwaardelijke toegang ondersteunt door te zoeken naar de instelling met het label **Voorwaardelijke toegang.**
 
   ![MFA en voorwaardelijke toegang configureren in Eigenschappen](media/conditional-access-user-flow/add-conditional-access.png)

1. Selecteer in **de sectie Meervoudige verificatie** de gewenste **MFA-methode** en selecteer vervolgens onder **MFA** afdwingen de optie **Voorwaardelijk (aanbevolen).**
 
1. Schakel in de sectie **Voorwaardelijke toegang** het selectievakje **Beleid voor voorwaardelijke toegang afdwingen** in.

1. Selecteer **Opslaan**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Voorwaardelijke toegang toevoegen aan uw beleid

1. Haal het voorbeeld van een beleid voor voorwaardelijke toegang op [GitHub op.](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access)
1. Vervang in elk bestand de tekenreeks `yourtenant` door de naam van uw Azure AD B2C tenant. Als de naam van uw B2C-tenant *bijvoorbeeld contosob2c* is, worden alle exemplaren `yourtenant.onmicrosoft.com` van `contosob2c.onmicrosoft.com` .
1. Upload de beleidsbestanden.

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Selecteer het `B2C_1A_signup_signin_with_ca` beleid of om de `B2C_1A_signup_signin_with_ca_whatif` overzichtspagina te openen. Selecteer vervolgens **Gebruikersstroom uitvoeren.** Selecteer *webapp1* onder **Toepassing**. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Kopieer de URL onder **Eindpunt voor gebruikersstroom uitvoeren**.

1. Als u een riskante aanmelding wilt simuleren, opent u [de Tor Browser](https://www.torproject.org/download/) en gebruikt u de URL die u in de vorige stap hebt gekopieerd om u aan te melden bij de geregistreerde app.

1. Voer de gevraagde informatie in op de aanmeldingspagina en probeer u aan te melden. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven. In het jwt.ms gedecodeerde token ziet u dat de aanmelding is geblokkeerd.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Uw gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **vervolgens Gebruikersstroom uitvoeren.** Selecteer *webapp1* onder **Toepassing**. De **antwoord-URL** moet `https://jwt.ms` weergeven.

1. Kopieer de URL onder **Eindpunt voor gebruikersstroom uitvoeren**.

1. Als u een riskante aanmelding wilt simuleren, opent u [de Tor Browser](https://www.torproject.org/download/) en gebruikt u de URL die u in de vorige stap hebt gekopieerd om u aan te melden bij de geregistreerde app.

1. Voer de gevraagde informatie in op de aanmeldingspagina en probeer u aan te melden. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven. In het jwt.ms gedecodeerde token ziet u dat de aanmelding is geblokkeerd.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Bekijk de resultaten van voorwaardelijke toegang in het controlerapport

Het resultaat van een voorwaardelijke toegangsgebeurtenis bekijken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

3. Selecteer **onder Azure-services** **de Azure AD B2C**. Of gebruik het zoekvak om te zoeken en selecteer **Azure AD B2C.**

4. Onder **Activiteiten** selecteert u **Controlelogboeken**.

5. Filter het controlelogboek door **Categorie** in te stellen op **B2C** en **Activiteit-resourcetype** op **IdentityProtection**. Selecteer vervolgens **Toepassen**.

6. Controleer de controleactiviteit voor de afgelopen zeven dagen. De volgende typen activiteiten zijn opgenomen:

   - **Beleidsregels voor voorwaardelijke toegang evalueren**: Deze vermelding in het controlelogboek geeft aan dat er een evaluatie van voorwaardelijke toegang is uitgevoerd tijdens een verificatie.
   - **Gebruiker** herstellen: deze vermelding geeft aan dat aan de toekenning of vereisten van een beleid voor voorwaardelijke toegang is voldaan door de eindgebruiker en dat deze activiteit is gerapporteerd aan de risico-engine om het risico van de gebruiker te beperken.

7. Selecteer een **Beleid voor voorwaardelijke toegang evalueren**-logboekvermelding in de lijst om de details van **-activiteit te openen: Controlelogboek**-pagina, waarin de audit logboek-id's worden weer gegeven, samen met deze informatie in de sectie **Aanvullende details**:

   - **ConditionalAccessResult:** de toekenning die is vereist voor de evaluatie van het voorwaardelijke beleid.
   - **AppliedPolicies:** een lijst met alle beleidsregels voor voorwaardelijke toegang waarbij aan de voorwaarden is voldaan en het beleid IS AAN.
   - **ReportingPolicies:** een lijst met de beleidsregels voor voorwaardelijke toegang die zijn ingesteld op de modus Alleen rapporteren en waar aan de voorwaarden is voldaan.

## <a name="next-steps"></a>Volgende stappen

[Pas de gebruikersinterface aan in een Azure AD B2C-gebruikersstroom](customize-ui-with-html.md)
