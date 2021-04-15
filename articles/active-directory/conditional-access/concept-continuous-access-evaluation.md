---
title: Continue toegangsevaluatie in Azure AD
description: Sneller reageren op wijzigingen in de gebruikerstoestand met continue toegangsevaluatie in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74009759bb9ca2a0516148fc1387b150b67452ab
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387901"
---
# <a name="continuous-access-evaluation"></a>Continue toegangsevaluatie

Verloop en vernieuwing van token is een standaardmechanisme in de branche. Wanneer een clienttoepassing zoals Outlook verbinding maakt met een service zoals Exchange Online, worden de API-aanvragen geautoriseerd met behulp van OAuth 2.0-toegangstokens. Standaard zijn deze toegangstokens één uur geldig. Wanneer ze verlopen, wordt de client teruggeleid naar Azure AD om ze te vernieuwen. Deze vernieuwingsperiode biedt de mogelijkheid om beleidsregels voor gebruikerstoegang opnieuw te beoordelen. Bijvoorbeeld: we kunnen ervoor kiezen om het token niet te vernieuwen vanwege een beleid voor voorwaardelijke toegang of omdat de gebruiker is uitgeschakeld in de directory. 

Klanten hebben hun zorgen uitgedrukt over de vertraging tussen het wijzigen van voorwaarden voor de gebruiker, zoals netwerklocatie of diefstal van referenties, en wanneer beleidsregels kunnen worden afgedwongen met betrekking tot die wijziging. We hebben geëxperimenteerd met de 'object object'-benadering van een gereduceerde levensduur van het token, maar hebben ontdekt dat ze gebruikerservaringen en betrouwbaarheid kunnen verminderen zonder risico's te elimineren.

Tijdige reactie op beleidsschendingen of beveiligingsproblemen vereist echt een 'gesprek' tussen de tokenuitgever, zoals Azure AD, en de relying party, zoals Exchange Online. Dit gesprek in twee gevallen biedt ons twee belangrijke mogelijkheden. De relying party kan zien wanneer er dingen zijn gewijzigd, zoals een client die afkomstig is van een nieuwe locatie, en de tokenuitgever op de melding geven. Het biedt de tokenvergever ook een manier om de relying party te laten stoppen met het respecteren van tokens voor een bepaalde gebruiker vanwege accountcompromitteerd, uitgeschakeld of andere problemen. Het mechanisme voor dit gesprek is continue toegangsevaluatie (CAE). Het doel is dat de reactie bijna in realtime is, maar in sommige gevallen kan een latentie van maximaal 15 minuten worden waargenomen als gevolg van de tijd voor het doorgeven van gebeurtenissen.

De eerste implementatie van continue toegangsevaluatie is gericht op Exchange, Teams en SharePoint Online.

Zie How to use Continue toegangsevaluatie [enabled APIs in your applications](../develop/app-resilience-continuous-access-evaluation.md)(Api's Continue toegangsevaluatie in uw toepassingen gebruiken) om uw toepassingen voor te bereiden op het gebruik van CAE.

### <a name="key-benefits"></a>Belangrijkste voordelen

- Gebruikersbeëindiging of wachtwoord wijzigen/opnieuw instellen: het intrekken van gebruikerssessies wordt bijna in realtime afgedwongen.
- Netwerklocatiewijziging: Het beleid voor de locatie van voorwaardelijke toegang wordt bijna in realtime afgedwongen.
- Tokenexport naar een computer buiten een vertrouwd netwerk kan worden voorkomen met locatiebeleid voor voorwaardelijke toegang.

## <a name="scenarios"></a>Scenario's 

Er zijn twee scenario's waarin continue toegangsevaluatie bestaat: evaluatie van kritieke gebeurtenissen en evaluatie van het beleid voor voorwaardelijke toegang.

### <a name="critical-event-evaluation"></a>Evaluatie van kritieke gebeurtenissen

Continue toegangsevaluatie wordt geïmplementeerd doordat services, zoals Exchange Online, SharePoint Online en Teams, zich kunnen abonneren op kritieke gebeurtenissen in Azure AD, zodat deze gebeurtenissen in bijna realtime kunnen worden geëvalueerd en afgedwongen. Evaluatie van kritieke gebeurtenissen is niet afhankelijk van beleid voor voorwaardelijke toegang, dus is beschikbaar in elke tenant. De volgende gebeurtenissen worden momenteel geëvalueerd:

- Gebruikersaccount is verwijderd of uitgeschakeld
- Het wachtwoord voor een gebruiker wordt gewijzigd of opnieuw ingesteld
- Meervoudige verificatie is ingeschakeld voor de gebruiker
- De beheerder trekt alle vernieuwingstokens voor een gebruiker expliciet in
- Hoog gebruikersrisico gedetecteerd door Azure AD Identity Protection

Dit proces maakt het mogelijk dat gebruikers binnen enkele minuten na een van deze kritieke gebeurtenissen geen toegang meer hebben tot SharePoint Online-bestanden, e-mail, agenda of taken van organisaties en Teams vanuit Microsoft 365-client-apps. 

> [!NOTE] 
> Teams en SharePoint Online bieden nog geen ondersteuning voor gebruikersrisicogebeurtenissen.

### <a name="conditional-access-policy-evaluation-preview"></a>Evaluatie van beleid voor voorwaardelijke toegang (preview)

Exchange en SharePoint kunnen belangrijke beleidsregels voor voorwaardelijke toegang synchroniseren, zodat ze in de service zelf kunnen worden geëvalueerd.

Dit proces maakt het mogelijk dat gebruikers de toegang tot organisatiebestanden, e-mail, agenda of taken van Microsoft 365 client-apps of SharePoint Online direct verliezen nadat de netwerklocatie is gewijzigd.

> [!NOTE]
> Niet alle combinatie van app en resourceprovider wordt ondersteund. Zie de onderstaande tabel. Office verwijst naar Word, Excel en PowerPoint.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |
| **Exchange Online** | Ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |

| | Office-web-apps | Office Win32-apps | Office voor iOS | Office voor Android | Office voor Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Niet ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |
| **Exchange Online** | Niet ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |

| | OneDrive-web | OneDrive Win32 | OneDrive iOS | OneDrive Android | OneDrive Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |

### <a name="client-side-claim-challenge"></a>Claim-uitdaging aan de clientzijde

Vóór continue toegangsevaluatie proberen clients het toegangs token altijd opnieuw af te spelen vanuit de cache, zolang het niet is verlopen. Met CAE introduceren we een nieuwe case waarin een resourceprovider een token kan afwijzen, zelfs wanneer het niet is verlopen. Om clients te informeren dat ze hun cache moeten omzeilen, zelfs als de tokens in de cache niet zijn verlopen, introduceren we een mechanisme met de naam **claimuitdaging** om aan te geven dat het token is afgewezen en een nieuw toegangstoken moet worden uitgegeven door Azure AD. CAE vereist een clientupdate om claim-uitdaging te begrijpen. De nieuwste versie van de volgende toepassingen hieronder biedt ondersteuning voor claim-vraag:

| | Web | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |
| **Teams** | Ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |
| **Office** | Niet ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |
| **OneDrive** | Ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |

### <a name="token-lifetime"></a>Levensduur van token

Omdat risico's en beleid in realtime worden geëvalueerd, vertrouwen clients die met continue toegangsevaluaties onderhandelen, op CAE in plaats van bestaande beleidsregels voor levensduur van statische toegangs tokens, wat betekent dat het beleid voor de levensduur van configureerbare token niet meer wordt nageleefd voor clients die geschikt zijn voor CAE.

De levensduur van het token wordt in CAE-sessies verhoogd tot een levensduur van maximaal 28 uur. Intrekking wordt aangestuurd door kritieke gebeurtenissen en beleidsevaluatie, niet alleen een willekeurige periode. Deze wijziging verhoogt de stabiliteit van toepassingen zonder dat dit van invloed is op de beveiligingsstatus. 

Als u geen clients gebruikt die geschikt zijn voor CAE, blijft de standaardlevensduur van het toegangtoken 1 uur, tenzij u de levensduur van het toegangtoken hebt geconfigureerd met de [preview-functie Configurable Token Lifetime (CTL).](../develop/active-directory-configurable-token-lifetimes.md)

## <a name="example-flows"></a>Voorbeeldstromen

### <a name="user-revocation-event-flow"></a>Gebeurtenisstroom voor intrekken van gebruiker:

![Gebeurtenisstroom gebruikers intrekken](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Een client die geschikt is voor CAE geeft referenties of een vernieuwings-token aan Azure AD door te vragen om een toegangsken voor een bepaalde resource.
1. Een toegangs token wordt samen met andere artefacten naar de client geretourneerd.
1. Een beheerder trekt alle [vernieuwingstokens voor de gebruiker expliciet in.](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) Vanuit Azure AD wordt een intrekkingsgebeurtenis verzonden naar de resourceprovider.
1. Er wordt een toegangs token weergegeven aan de resourceprovider. De resourceprovider evalueert de geldigheid van het token en controleert of er een intrekkingsgebeurtenis voor de gebruiker is. De resourceprovider gebruikt deze informatie om al dan niet toegang te verlenen tot de resource.
1. In dit geval geeft de resourceprovider geen toegang en stuurt deze een 401+-claimuitdaging terug naar de client.
1. De cae-client begrijpt de 401+-claim-uitdaging. Het slaat de caches over en gaat terug naar stap 1, en stuurt het vernieuwings-token samen met de claimuitdaging terug naar Azure AD. In Azure AD worden vervolgens alle voorwaarden opnieuw geëvalueerd en wordt de gebruiker gevraagd om zich in dit geval opnieuw te authenticeren.

### <a name="user-condition-change-flow-preview"></a>Stroom voor wijziging van gebruikersvoorwaarde (preview):

In het volgende voorbeeld heeft een beheerder van voorwaardelijke toegang een beleid voor voorwaardelijke toegang op basis van een locatie geconfigureerd om alleen toegang vanuit specifieke IP-bereiken toe te staan:

![Gebeurtenisstroom voor gebruikersvoorwaarde](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Een client die geschikt is voor CAE geeft referenties of een vernieuwings-token aan Azure AD door te vragen naar een toegangs token voor een bepaalde resource.
1. Azure AD evalueert alle beleidsregels voor voorwaardelijke toegang om te zien of de gebruiker en client aan de voorwaarden voldoen.
1. Er wordt een toegangs token geretourneerd samen met andere artefacten naar de client.
1. Gebruiker verplaatst zich buiten een toegestaan IP-bereik
1. De client geeft een toegangs token aan de resourceprovider van buiten een toegestaan IP-bereik.
1. De resourceprovider evalueert de geldigheid van het token en controleert het locatiebeleid dat is gesynchroniseerd vanuit Azure AD.
1. In dit geval geeft de resourceprovider geen toegang en stuurt deze een 401+-claimuitdaging terug naar de client omdat deze niet afkomstig is uit het toegestane IP-bereik.
1. De cae-client begrijpt de 401+-claim-uitdaging. Het slaat de caches over en gaat terug naar stap 1, en stuurt het vernieuwings-token samen met de claimuitdaging terug naar Azure AD. In Azure AD worden alle voorwaarden opnieuw geëvalueerd en wordt in dit geval de toegang ontzegd.

## <a name="enable-or-disable-cae-preview"></a>CAE in- of uitschakelen (preview)

1. Meld u aan bij **Azure Portal** beheerder van voorwaardelijke toegang, beveiligingsbeheerder of globale beheerder
1. Blader **naar** Azure Active Directory security continuous access evaluation  >  **(Doorlopende** toegang tot  >  **beveiliging).**
1. Kies **Preview inschakelen.**

Op deze pagina kunt u eventueel de gebruikers en groepen beperken die onderhevig zijn aan de preview.

![De CAE-preview inschakelen in Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="supported-location-policies"></a>Ondersteund locatiebeleid

Voor CAE hebben we alleen inzicht in benoemde, op IP gebaseerde benoemde locaties. We hebben geen inzicht in andere locatie-instellingen, zoals [vertrouwde MFA-IP's](../authentication/howto-mfa-mfasettings.md#trusted-ips) of locaties op basis van landen. Wanneer de gebruiker afkomstig is van een vertrouwd IP-adres voor MFA of vertrouwde locaties met vertrouwde IP-adressen voor MFA of de locatie van het land, wordt CAE niet afgedwongen nadat de gebruiker naar een andere locatie is verplaatst. In die gevallen geven we een CAE-token van 1 uur uit zonder directe controle van IP-afdwinging.

> [!IMPORTANT]
> Wanneer u locaties configureert voor continue toegangsevaluatie, gebruikt u alleen de locatievoorwaarde voor voorwaardelijke toegang op basis van [IP](../conditional-access/location-condition.md) en configureert u alle IP-adressen, met inbegrip van **zowel IPv4 als IPv6,** die kunnen worden gezien door uw id-provider en resourcesprovider. Gebruik geen landlocatievoorwaarden of de vertrouwde IPS-functie die beschikbaar is op de pagina service-instellingen van Azure AD Multi-Factor Authentication.

### <a name="ip-address-configuration"></a>IP-adresconfiguratie

Uw id-provider en resourceproviders kunnen verschillende IP-adressen zien. Dit komt mogelijk niet overeen vanwege implementaties van netwerkproxy's in uw organisatie of onjuiste IPv4-/IPv6-configuraties tussen uw id-provider en resourceprovider. Bijvoorbeeld:

- Uw id-provider ziet één IP-adres van de client.
- Uw resourceprovider ziet een ander IP-adres van de client nadat deze via een proxy is door geven.
- Het IP-adres dat uw id-provider ziet, maakt deel uit van een toegestaan IP-bereik in het beleid, maar het IP-adres van de resourceprovider niet.

Als dit scenario in uw omgeving bestaat om oneindige lussen te voorkomen, geeft Azure AD een CAE-token van één uur uit en wordt er geen wijziging van de clientlocatie afgedwongen. Zelfs in dit geval is de beveiliging verbeterd in vergelijking met [](#critical-event-evaluation) traditionele tokens van één uur, omdat we nog steeds de andere gebeurtenissen evalueren, behalve gebeurtenissen voor het wijzigen van de clientlocatie.

### <a name="office-and-web-account-manager-settings"></a>Office- en webaccountbeheer instellingen

| Office-updatekanaal | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Semi-Annual Enterprise-kanaal | Als deze is ingesteld op ingeschakeld of 1, wordt CAE niet ondersteund. | Als deze is ingesteld op ingeschakeld of 1, wordt CAE niet ondersteund. |
| Huidig kanaal <br> of <br> Maandelijks Enterprise-kanaal | CAE wordt ondersteund, ongeacht de instelling | CAE wordt ondersteund, ongeacht de instelling |

Zie Overzicht van updatekanalen voor Microsoft 365 Apps voor een uitleg van de [office-updatekanalen.](/deployoffice/overview-update-channels) Het wordt aanbevolen dat organisaties niet uitschakelen webaccountbeheer (WAM).

### <a name="group-membership-and-policy-update-effective-time"></a>Groepslidmaatschap en beleidsupdate effectieve tijd

Het kan tot één dag duren voor groepslidmaatschap en beleidsupdates door beheerders zijn bijgewerkt. Er is enige optimalisatie uitgevoerd voor beleidsupdates, waardoor de vertraging wordt beperkt tot twee uur. Dit geldt echter nog niet voor alle scenario's. 

Als er sprake is van een noodgeval en u uw beleid moet laten aanpassen of als u het groepslidmaatschap wilt wijzigen om onmiddellijk toe te passen op bepaalde gebruikers, moet u deze [PowerShell-opdracht](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) of 'Sessie intrekken' op de gebruikersprofielpagina gebruiken om de gebruikerssessie in te trekken. Hiermee zorgt u ervoor dat het bijgewerkte beleid onmiddellijk wordt toegepast.

### <a name="coauthoring-in-office-apps"></a>Co-autorering in Office-apps

Wanneer meerdere gebruikers op hetzelfde moment aan hetzelfde document samenwerken, wordt de toegang van de gebruiker tot het document mogelijk niet onmiddellijk ingetrokken door CAE op basis van gebruikersintrekken of beleidswijzigingsgebeurtenissen. In dit geval verliest de gebruiker de toegang volledig na het sluiten van het document, het sluiten van Word, Excel of PowerPoint, of na een periode van 10 uur.

Om deze tijd te beperken, kan een SharePoint-beheerder eventueel de maximale levensduur van co-autoreringssessies verminderen voor documenten die zijn opgeslagen in SharePoint Online en OneDrive voor Bedrijven, door een netwerklocatiebeleid [in SharePoint Online](/sharepoint/control-access-based-on-network-location)te configureren. Zodra deze configuratie is gewijzigd, wordt de maximale levensduur van co-autoridingsessies gereduceerd tot 15 minuten en kan verder worden aangepast met behulp van de SharePoint Online PowerShell-opdracht 'Set-SPOTenant –IPAddressWACTokenLifetime'

### <a name="enable-after-a-user-is-disabled"></a>Inschakelen nadat een gebruiker is uitgeschakeld

Als u een gebruiker inschakelen direct nadat deze is uitgeschakeld. Er is enige latentie voordat het account kan worden ingeschakeld. SPO en Teams hebben een vertraging van 15 minuten. De vertraging is 35-40 minuten voor DEN.

## <a name="faqs"></a>Veelgestelde vragen

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hoe werkt CAE met de aanmeldingsfrequentie?

Aanmeldingsfrequentie wordt gehonoreerd met of zonder CAE.

## <a name="next-steps"></a>Volgende stappen

[Aankondiging van continue toegangsevaluatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
