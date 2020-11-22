---
title: Groeps claims configureren voor toepassingen met Azure Active Directory | Microsoft Docs
description: Informatie over het configureren van groepclaims voor gebruik met Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: cb828eeb408a170b93ffc73b58f14b3f7a883cc4
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95247231"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Groeps claims configureren voor toepassingen met Azure Active Directory

Azure Active Directory kunt een groepslid maatschap voor gebruikers gegevens opgeven in tokens voor gebruik in toepassingen.  Er worden twee hoofd patronen ondersteund:

- Groepen die zijn geïdentificeerd met het kenmerk OID (Azure Active Directory object-id)
- Groepen die zijn geïdentificeerd door sAMAccountName-of GroupSID-kenmerken voor door Active Directory (AD) gesynchroniseerde groepen en gebruikers

> [!IMPORTANT]
> Er zijn een aantal opmerkingen voor deze functionaliteit:
>
>- Ondersteuning voor het gebruik van sAMAccountName-en SID-kenmerken (Security Identifier) die vanaf on-premises worden gesynchroniseerd, is ontworpen om bestaande toepassingen van AD FS en andere id-providers te verplaatsen. Groepen die worden beheerd in azure AD bevatten niet de kenmerken die nodig zijn om deze claims te verzenden.
>- In grotere organisaties mag het aantal groepen waarvan een gebruiker lid is, de limiet overschrijden die Azure Active Directory zal toevoegen aan een token. 150 groepen voor een SAML-token en 200 voor een JWT. Dit kan leiden tot onvoorspelbare resultaten. Als uw gebruikers grote aantallen groepslid maatschappen hebben, raden we u aan de optie te gebruiken om de groepen die worden verzonden in claims te beperken tot de relevante groepen voor de toepassing.  
>- Voor het ontwikkelen van nieuwe toepassingen, of in gevallen waarin de toepassing kan worden geconfigureerd en de ondersteuning voor geneste groepen is niet vereist, wordt aangeraden in-app-autorisatie gebaseerd op toepassings rollen in plaats van groepen.  Hiermee beperkt u de hoeveelheid gegevens die in het token moeten worden gezet, veiliger en scheidt u de gebruikers toewijzing van de app-configuratie.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Claims groeperen voor toepassingen die worden gemigreerd van AD FS en andere id-providers

Veel toepassingen die zijn geconfigureerd voor verificatie met AD FS zijn afhankelijk van de gegevens van groepslid maatschappen in de vorm van Windows AD-groeps kenmerken.   Deze kenmerken zijn de groep sAMAccountName, die kunnen worden gekwalificeerd door-Domain name of de Windows-groep beveiligings-id (GroupSID).  Wanneer de toepassing is federatieve met AD FS, gebruikt AD FS de functie TokenGroups om de groepslid maatschappen voor de gebruiker op te halen.

Een app die is verplaatst van AD FS heeft claims in dezelfde indeling nodig. Groeps-en rol claims kunnen worden verzonden vanuit Azure Active Directory met het domein gekwalificeerde sAMAccountName of het GroupSID dat is gesynchroniseerd vanuit Active Directory in plaats van de Azure Active Directory objectID van de groep.

De ondersteunde indelingen voor groepclaims zijn:

- **ObjectId voor Azure Active Directory groep** (beschikbaar voor alle groepen)
- **sAMAccountName** (beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **NetbiosDomain\sAMAccountName** (beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **DNSDomainName\sAMAccountName** (beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **Beveiligings-id van on-premises groep** (beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)

> [!NOTE]
> sAMAccountName-en on-premises groep SID-kenmerken zijn alleen beschikbaar voor groeps objecten die zijn gesynchroniseerd vanuit Active Directory.   Ze zijn niet beschikbaar voor groepen die zijn gemaakt in Azure Active Directory of Office365.   Toepassingen die in Azure Active Directory zijn geconfigureerd om gesynchroniseerde on-premises groeps kenmerken te krijgen, krijgen ze alleen voor gesynchroniseerde groepen.

## <a name="options-for-applications-to-consume-group-information"></a>Opties voor het verbruik van groeps gegevens in toepassingen

Toepassingen kunnen het eind punt van de MS-grafiek groepen aanroepen om groeps informatie te verkrijgen voor de geverifieerde gebruiker. Deze aanroep zorgt ervoor dat alle groepen waarvan een gebruiker lid is, beschikbaar zijn, zelfs wanneer er sprake is van een groot aantal groepen.  Groeps inventarisatie wordt vervolgens onafhankelijk van beperkingen voor de token grootte.

Als een bestaande toepassing echter groeps gegevens per claim verwacht te verbruiken, kan Azure Active Directory worden geconfigureerd met een aantal verschillende indelingen voor claims.  Houd rekening met de volgende opties:

- Wanneer u groepslid maatschap gebruikt voor autorisatie doeleinden in de toepassing, is het raadzaam om de groeps-ObjectID te gebruiken. De ObjectID van de groep is onveranderbaar en uniek in Azure Active Directory en is beschikbaar voor alle groepen.
- Als de on-premises groep sAMAccountName voor autorisatie gebruikt, gebruikt u domein gekwalificeerde namen.  Er is minder kans op conflicterende namen. sAMAccountName kan uniek zijn in een Active Directory domein, maar als meer dan één Active Directory domein is gesynchroniseerd met een Azure Active Directory-Tenant, kan er meer dan één groep dezelfde naam hebben.
- U kunt [toepassings rollen](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) gebruiken om een laag van omleiding te bieden tussen het groepslid maatschap en de toepassing.   De toepassing brengt vervolgens interne autorisatie beslissingen op basis van de functie-en schelpen in het token.
- Als de toepassing is geconfigureerd voor het ophalen van groeps kenmerken die zijn gesynchroniseerd vanuit Active Directory en een groep deze kenmerken niet bevat, wordt deze niet opgenomen in de claims.
- Groepeer claims in tokens bevatten geneste groepen, behalve wanneer u de optie gebruikt om de groeps claims te beperken tot groepen die zijn toegewezen aan de toepassing.  Als een gebruiker lid is van GroupB en GroupB lid is van GroepA, bevatten de groeps claims voor de gebruiker zowel GroepA als GroupB. Wanneer de gebruikers van een organisatie grote aantallen groepslid maatschappen hebben, kan het aantal groepen dat in het token wordt vermeld, de token grootte verg Roten.  Azure Active Directory beperkt het aantal groepen dat in een token wordt meezenden naar 150 voor SAML-bevestigingen en 200 voor JWT.  Als een gebruiker lid is van een groter aantal groepen, worden de groepen wegge laten en vindt u in plaats daarvan een koppeling naar het eind punt van de grafiek om groeps gegevens op te halen.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Vereisten voor het gebruik van groeps kenmerken gesynchroniseerd vanuit Active Directory

Claims voor groepslid maatschappen kunnen worden verzonden in tokens voor elke groep als u de ObjectId-indeling gebruikt. Als u groepclaims wilt gebruiken in andere indelingen dan de groeps-ObjectId, moeten de groepen worden gesynchroniseerd vanuit Active Directory met behulp van Azure AD Connect.

Er zijn twee stappen voor het configureren van Azure Active Directory voor het verzenden van groeps namen voor Active Directory groepen.

1. **Groeps namen synchroniseren vanuit Active Directory** Voordat Azure Active Directory de groeps namen of on-premises groeps-SID in groeps-of Rolgroepen kunt verzenden, moeten de vereiste kenmerken worden gesynchroniseerd vanuit Active Directory.  U moet Azure AD Connect versie 1.2.70 of hoger uitvoeren.   Eerdere versies van Azure AD Connect dan 1.2.70 zullen de groeps objecten vanaf Active Directory synchroniseren, maar bevat niet de vereiste kenmerken van de groeps naam.  Voer een upgrade uit naar de huidige versie.

2. **De registratie van de toepassing in azure Active Directory zo configureren dat groeps claims worden toegevoegd aan tokens** Groepclaims kunnen worden geconfigureerd in de sectie bedrijfs toepassingen van de portal of met behulp van het manifest van de toepassing in de sectie toepassings registraties.  Zie voor het configureren van groeps claims in het manifest van de toepassing ' de Azure Active Directory toepassings registratie voor groeps kenmerken configureren ' hieronder.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Groeps claims toevoegen aan tokens voor SAML-toepassingen met SSO-configuratie

Als u groeps claims wilt configureren voor een SAML-toepassing in een galerie of niet-galerie, opent u **zakelijke toepassingen**, klikt u op de toepassing in de lijst, selecteert u **configuratie van eenmalige aanmelding** en selecteert u vervolgens **gebruikers kenmerken & claims**.

Klik op **een groepclaim toevoegen**  

![Scherm opname waarin de pagina ' gebruikers kenmerken & claims ' wordt weer gegeven met een groeps claim toevoegen geselecteerd.](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Gebruik de keuze rondjes om te selecteren welke groepen in het token moeten worden opgenomen

![Scherm opname van het venster ' groeps claims ' met ' beveiligings groepen ' geselecteerd.](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Selectie | Beschrijving |
|----------|-------------|
| **Alle groepen** | Beveiligings groepen en distributie lijsten en-rollen meedeelt.  |
| **Beveiligingsgroepen** | Verzendt beveiligings groepen waarvan de gebruiker lid is in de groeps claim |
| **Directory-rollen** | Als er adreslijst rollen aan de gebruiker zijn toegewezen, worden ze verzonden als een ' WIDS-claim (groeps claim wordt niet verzonden) |
| **Groepen die zijn toegewezen aan de toepassing** | Verzendt alleen de groepen die expliciet zijn toegewezen aan de toepassing en waarvan de gebruiker lid is |

Als u bijvoorbeeld alle beveiligings groepen wilt genereren waarvan de gebruiker lid is, selecteert u beveiligings groepen

![Scherm opname van het venster ' groeps claims ' met ' beveiligings groepen ' geselecteerd en de vervolg keuzelijst source attribute open.](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Als u groepen wilt verzenden met Active Directory kenmerken die zijn gesynchroniseerd vanuit Active Directory in plaats van met Azure AD Objectid's, selecteert u de gewenste indeling in de vervolg keuzelijst. Alleen groepen die zijn gesynchroniseerd vanuit Active Directory worden opgenomen in de claims.

![Scherm afbeelding met de vervolg keuzelijst source attribute open.](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Als u alleen groepen wilt verzenden die aan de toepassing zijn toegewezen, selecteert u **groepen die zijn toegewezen aan de toepassing**

![Scherm opname van het venster ' groepclaims ' met ' groepen die zijn toegewezen aan de toepassing ' geselecteerd.](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Groepen die zijn toegewezen aan de toepassing, worden opgenomen in het token.  Andere groepen waarvan de gebruiker lid is, worden wegge laten.  Met deze optie geneste groepen worden niet opgenomen en de gebruiker moet een direct lid zijn van de groep die is toegewezen aan de toepassing.

Als u de groepen wilt wijzigen die aan de toepassing zijn toegewezen, selecteert u de toepassing in de lijst **ondernemings toepassingen** en klikt u vervolgens op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

Zie het document [een gebruiker of groep toewijzen aan een bedrijfs-app](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) voor meer informatie over het beheren van groeps toewijzing aan toepassingen.

### <a name="advanced-options"></a>Geavanceerde opties

De manier waarop groeps claims worden verzonden, kunnen worden gewijzigd door de instellingen onder Geavanceerde opties

De naam van de groepclaim aanpassen: als deze is geselecteerd, kan een ander claim type worden opgegeven voor groepclaims.   Geef het claim type op in het veld naam en de optionele naam ruimte voor de claim in het veld naam ruimte.

![Scherm afbeelding met de sectie ' Geavanceerde opties ' waarbij ' de naam van de groeps claim ' geselecteerd en ' naam ' en ' namespace ' worden opgegeven.](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Voor sommige toepassingen moet de gegevens van het groepslid maatschap worden weer gegeven in de claim van de rol. U kunt desgewenst de gebruikers groepen als rollen verzenden door het selectie vakje groepen een rol claimen verzenden in te scha kelen.

![Scherm afbeelding met de sectie ' Geavanceerde opties ' waarbij ' de naam van de groeps claim aanpassen ' en ' groepen verzenden als roldefinities ' is geselecteerd.](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Als de optie voor het verzenden van groeps gegevens als rollen wordt gebruikt, worden alleen groepen weer gegeven in de rol claim.  Alle toepassings rollen waaraan de gebruiker is toegewezen, worden niet weer gegeven in de claim.

### <a name="edit-the-group-claims-configuration"></a>De configuratie van de groeps claims bewerken

Wanneer een groeps claim configuratie is toegevoegd aan de gebruikers kenmerken & claim configuratie, wordt de optie voor het toevoegen van een groeps claim grijs weer gegeven.  Als u de configuratie van de groeps claim wilt wijzigen, klikt u op de groepclaim in de lijst **aanvullende claims** .

![claim GEBRUIKERSINTERFACE](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>De registratie van de Azure AD-toepassing voor groeps kenmerken configureren

Groepclaims kunnen ook worden geconfigureerd in de sectie [optionele claims](../../active-directory/develop/active-directory-optional-claims.md) van het [toepassings manifest](../../active-directory/develop/reference-app-manifest.md).

1. In de portal->Azure Active Directory-> toepassings registraties->Selecteer toepassing->manifest

2. Claims voor groepslid maatschap inschakelen door de groupMembershipClaim te wijzigen

Geldige waarden zijn:

| Selectie | Beschrijving |
|----------|-------------|
| **Hele** | Beveiligings groepen, distributie lijsten en rollen meedeelt |
| **Beveiligings groep** | Verzendt beveiligings groepen waarvan de gebruiker lid is in de groeps claim |
| **"DirectoryRole"** | Als er adreslijst rollen aan de gebruiker zijn toegewezen, worden ze verzonden als een ' WIDS-claim (groeps claim wordt niet verzonden) |
| **Variabele applicationgroup** | Verzendt alleen de groepen die expliciet zijn toegewezen aan de toepassing en waarvan de gebruiker lid is |
| **Geen** | Er zijn geen groepen geretourneerd. (De niet-sensetive is dus niet mogelijk en kan ook rechtstreeks in het toepassings manifest worden ingesteld.) |

   Bijvoorbeeld:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Standaard wordt groeps Objectid's verzonden in de claim waarde van de groep.  Als u de claim waarde wilt wijzigen zodat deze lokale groeps kenmerken bevat, of als u het claim type wilt wijzigen in rol, gebruikt u de OptionalClaims-configuratie als volgt:

3. Stel groeps naam configuratie optionele claims in.

   Als u wilt dat de groepen in het token de on-premises AD-groeps kenmerken bevatten, geeft u op welk token type optionele claim moet worden toegepast in de sectie optionele claims.  Meerdere token typen kunnen worden weer gegeven:

   - idToken voor het OIDC-ID-token
   - accessToken voor het OAuth/OIDC-toegangs token
   - Saml2Token voor SAML-tokens.

   > [!NOTE]
   > Het type Saml2Token is van toepassing op zowel SAML 1.1-als SAML 2.0-indelings tokens

   Wijzig voor elk relevant token type de OptionalClaims-sectie in het manifest. Het OptionalClaims-schema is als volgt:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Optioneel claim schema | Waarde |
   |----------|-------------|
   | **naam** | Moet ' groups ' zijn |
   | **Bron** | Niet gebruikt. Null-waarde weglaten of opgeven |
   | **downloaden** | Niet gebruikt. Weglaten of onwaar opgeven |
   | **additionalProperties:** | Lijst met aanvullende eigenschappen.  Geldige opties zijn ' sam_account_name ', ' dns_domain_and_sam_account_name ', ' netbios_domain_and_sam_account_name ', ' emit_as_roles ' |

   In additionalProperties is slechts een van de sam_account_name, dns_domain_and_sam_account_name, netbios_domain_and_sam_account_name vereist.  Als er meer dan een aanwezig is, wordt de eerste gebruikt en worden alle andere genegeerd.

   Voor sommige toepassingen is groeps informatie over de gebruiker in de rol claim vereist.  Als u het claim type wilt wijzigen van een groepclaim in een groepclaim, voegt u ' emit_as_roles ' toe aan extra eigenschappen.  De groeps waarden worden verzonden in de rol claim.

   > [!NOTE]
   > Als ' emit_as_roles ' wordt gebruikt, worden de toepassings rollen die zijn geconfigureerd dat de gebruiker is toegewezen, niet weer gegeven in de rol claim

### <a name="examples"></a>Voorbeelden

Groepen verzenden als groeps namen in OAuth-toegangs tokens in de dnsDomainName\SAMAccountName-indeling

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Groeps namen verzenden die moeten worden geretourneerd in netbiosDomain\samAccountName-indeling als de rol claim in SAML-en OIDC-ID-tokens:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Volgende stappen

- [Autorisatie toevoegen met behulp van groepen & groepen claims aan een ASP.NET Core web-app (code voorbeeld)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Een gebruiker of groep toewijzen aan een bedrijfs-app](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Rolclaims configureren](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
