---
title: Evaluatie van doorlopende toegang in azure AD
description: Sneller reageren op wijzigingen in de gebruikers status met de evaluatie van continue toegang in azure AD
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
ms.openlocfilehash: fd14c7911676768a61d2b2c0e366d2c227455be8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678201"
---
# <a name="continuous-access-evaluation"></a>Continue toegangsevaluatie

Het verlopen van tokens en vernieuwen is een standaard mechanisme in de branche. Wanneer een client toepassing, zoals Outlook, verbinding maakt met een service zoals Exchange Online, worden de API-aanvragen geautoriseerd met OAuth 2,0-toegangs tokens. De toegangs tokens zijn standaard één uur geldig, wanneer ze verlopen. de client wordt vervolgens teruggeleid naar Azure AD om ze te vernieuwen. Deze vernieuwings periode biedt de mogelijkheid om beleid voor gebruikers toegang opnieuw te evalueren. Bijvoorbeeld: u kunt ervoor kiezen om het token niet te vernieuwen vanwege een beleid voor voorwaardelijke toegang, of omdat de gebruiker is uitgeschakeld in de map. 

Klanten hebben een probleem met de vertraging tussen wanneer de voor waarden van de gebruiker zijn gewijzigd, zoals netwerk locatie of dief stal van referenties, en wanneer beleids regels kunnen worden afgedwongen met betrekking tot die wijziging. We hebben experimenteren met de benadering ' Blunt object ' van een gereduceerde levens duur van tokens, maar ze kunnen de gebruikers ervaringen en betrouw baarheid verlagen zonder Risico's te elimineren.

Voor een tijdige reactie op beleids schendingen of beveiligings problemen is een ' conversatie ' vereist tussen de uitgever van het token, zoals Azure AD, en de Relying Party, zoals Exchange Online. Deze twee richtings conversatie biedt ons twee belang rijke mogelijkheden. De Relying Party kan zien wanneer dingen zijn gewijzigd, zoals een client die afkomstig is van een nieuwe locatie, en de uitgever van het token kenbaar maakt. Het biedt ook de token uitgever een manier om de Relying Party te laten weten dat tokens voor een bepaalde gebruiker niet meer worden geëerbiedigd vanwege inbreuk op het account, het uitschakelen of andere problemen. Het mechanisme voor deze conversatie is een voortdurende toegangs beoordeling (CAE). Het doel is om reactie bijna in realtime te zijn, maar in sommige gevallen kan een latentie van Maxi maal 15 minuten worden waargenomen als gevolg van de doorgifte tijd van de gebeurtenis.

De eerste implementatie van voortdurende toegangs beoordeling is gericht op Exchange, teams en share point online.

Als u uw toepassingen wilt voorbereiden op het gebruik van CAE, raadpleegt u [api's voor continue toegang gebruiken in uw toepassingen](../develop/app-resilience-continuous-access-evaluation.md).

### <a name="key-benefits"></a>Belangrijkste voordelen

- Gebruikers beëindiging of wacht woord wijzigen/opnieuw instellen: het intrekken van de gebruikers sessie wordt in bijna realtime afgedwongen.
- Netwerk locatie wijzigen: het beleid voor voorwaardelijke toegang locatie wordt in bijna realtime afgedwongen.
- Het exporteren van een token naar een computer buiten een vertrouwd netwerk kan worden voor komen met een locatie beleid voor voorwaardelijke toegang.

## <a name="scenarios"></a>Scenario's 

Er zijn twee scenario's die de evaluatie van de voortdurende toegang, de evaluatie van kritieke gebeurtenissen en het beleid voor voorwaardelijke toegang opleveren.

### <a name="critical-event-evaluation"></a>Evaluatie van kritieke gebeurtenissen

De evaluatie van voortdurende toegang wordt geïmplementeerd door services, zoals Exchange Online, share point online en teams, in te scha kelen voor kritieke gebeurtenissen in azure AD, zodat deze gebeurtenissen in de buurt van real-time kunnen worden geëvalueerd en afgedwongen. De evaluatie van kritieke gebeurtenissen is niet afhankelijk van het beleid voor voorwaardelijke toegang dat beschikbaar is in een wille keurige Tenant. De volgende gebeurtenissen worden momenteel geëvalueerd:

- Gebruikers account is verwijderd of uitgeschakeld
- Het wacht woord voor een gebruiker wordt gewijzigd of opnieuw ingesteld
- Multi-factor Authentication is ingeschakeld voor de gebruiker
- De beheerder trekt expliciet alle vernieuwings tokens voor een gebruiker in
- Er is een verhoogd gebruikers risico gedetecteerd door Azure AD Identity Protection

Dit proces maakt het scenario mogelijk dat gebruikers geen toegang meer hebben tot de share point online-bestanden, e-mail, agenda of taken van de organisatie, en teams van Microsoft 365 client-apps binnen minuten na een van deze kritieke gebeurtenissen. 

### <a name="conditional-access-policy-evaluation-preview"></a>Evaluatie van voorwaardelijk toegangs beleid (preview-versie)

Exchange en share point kunnen sleutels voor voorwaardelijk toegangs beleid synchroniseren zodat ze binnen de service zelf kunnen worden geëvalueerd.

Met dit proces wordt het scenario ingeschakeld waarbij gebruikers de toegang tot bedrijfs bestanden, e-mail, agenda of taken van Microsoft 365 client-apps of share point online direct na wijzigingen in de netwerk locatie kwijt raken.

> [!NOTE]
> Niet alle combi Naties van app-en resource providers worden ondersteund. Zie de tabel hieronder. Office heeft betrekking op Word, Excel en Power Point

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Ondersteund | Ondersteund | Niet ondersteund | Niet ondersteund | Ondersteund |
| **Exchange Online** | Ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |

| | Office Web apps | Office Win32-apps | Office voor iOS | Office voor Android | Office voor Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Niet ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |
| **Exchange Online** | Niet ondersteund | Ondersteund | Ondersteund | Ondersteund | Ondersteund |

### <a name="client-side-claim-challenge"></a>Claim vraag aan client zijde

Voordat de evaluatie doorlopend wordt gedetecteerd, proberen clients altijd het toegangs token opnieuw af te spelen uit de cache zolang het niet is verlopen. Met CAE wordt een nieuw geval geïntroduceerd dat een resource provider een token kan afwijzen, zelfs wanneer het niet is verlopen. Om ervoor te zorgen dat clients hun cache overs Laan, zelfs als de tokens in de cache niet zijn verlopen, introduceren we een mechanisme met de naam **claim Challenge** om aan te geven dat het token is afgewezen en dat er een nieuw toegangs token moet worden uitgegeven door Azure AD. Voor CAE is een client update vereist om de claim Challenge te begrijpen. De meest recente versie van de volgende toepassingen ondersteunen claim Challenge:

- Outlook-vensters
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Teams voor Windows (alleen voor de resource teams)
- Teams iOS (alleen voor resource teams)
- Teams Android (alleen voor teams resource)
- Teams Mac (alleen voor resource teams)
- Word/Excel/Power Point voor Windows
- Word/Excel/Power Point voor iOS
- Word/Excel/Power Point voor Android
- Word/Excel/Power Point voor Mac

### <a name="token-lifetime"></a>Levens duur van token

Omdat het risico en het beleid in realtime worden geëvalueerd, zullen clients die onderhandelen over continue toegang-evaluatie sessies, vertrouwen op CAE in plaats van het bestaande beleid voor de levens duur van een statisch toegangs token, wat betekent dat het Configureer bare token levensduur beleid niet meer wordt gehonoreerd voor CAE-compatibele clients die onderhandelen over CAE-compatibele sessies.

De levens duur van het token is langer dan 28 uur lang bewaard, in CAE-sessies. Intrekken wordt gereden door kritieke gebeurtenissen en beleids evaluatie, niet alleen een wille keurige tijds periode. Met deze wijziging wordt de stabiliteit van toepassingen verhoogd zonder dat dit van invloed is op de beveiligings postuur. 

Als u geen gebruik maakt van CAE-compatibele clients, blijft de standaard levensduur van het toegangs token 1 uur, tenzij u de levens duur van uw toegangs token hebt geconfigureerd met de preview-functie voor de [Configureer bare levens duur van de token (CTL)](../develop/active-directory-configurable-token-lifetimes.md) .

## <a name="example-flows"></a>Voorbeeld stromen

### <a name="user-revocation-event-flow"></a>Stroom voor het intrekken van de gebruiker:

![Stroom voor het intrekken van de gebruiker](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Een client met CAE-ondersteuning geeft referenties of een vernieuwings token door aan Azure AD die vraagt om een toegangs token voor een bepaalde resource.
1. Een toegangs token wordt samen met andere artefacten naar de client geretourneerd.
1. Een beheerder [trekt expliciet alle vernieuwings tokens voor de gebruiker in](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Er wordt een intrekkings gebeurtenis verzonden naar de resource provider vanuit Azure AD.
1. Er wordt een toegangs token aan de resource provider door gegeven. De resource provider evalueert de geldigheid van het token en controleert of er een intrekkings gebeurtenis voor de gebruiker is. De resource provider gebruikt deze informatie om te bepalen of u toegang wilt verlenen aan de resource.
1. In dit geval weigert de resource provider de toegang en stuurt een 401 + claim-Challenge terug naar de client.
1. De-client die geschikt is voor CAE, begrijpt de 401 + claim Challenge. Het omzeilt de caches en gaat terug naar stap 1, verzendt het vernieuwings token samen met de claim uitdaging terug naar Azure AD. Azure AD evalueert vervolgens alle voor waarden en vraagt de gebruiker in dit geval opnieuw te verifiëren.

### <a name="user-condition-change-flow-preview"></a>Wijzigings stroom voor de gebruikers voorwaarde (preview-versie):

In het volgende voor beeld heeft een beheerder van de voorwaardelijke toegang een op locatie gebaseerd beleid voor voorwaardelijke toegang geconfigureerd zodat alleen toegang vanaf specifieke IP-bereiken wordt toegestaan:

![Gebeurtenis stroom voor de gebruikers voorwaarde](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Een client met CAE-ondersteuning geeft referenties of een vernieuwings token door aan Azure AD die vraagt om een toegangs token voor een bepaalde resource.
1. Azure AD evalueert alle beleids regels voor voorwaardelijke toegang om te zien of de gebruiker en de client voldoen aan de voor waarden.
1. Een toegangs token wordt samen met andere artefacten naar de client geretourneerd.
1. De gebruiker verdwijnt uit een toegestaan IP-bereik
1. De client presenteert een toegangs token voor de resource provider van buiten een toegestaan IP-bereik.
1. De resource provider evalueert de geldigheid van het token en controleert het locatie beleid dat vanuit Azure AD is gesynchroniseerd.
1. In dit geval weigert de resource provider de toegang en stuurt een 401 + claim-Challenge terug naar de client omdat deze niet afkomstig is van het toegestane IP-bereik.
1. De-client die geschikt is voor CAE, begrijpt de 401 + claim Challenge. Het omzeilt de caches en gaat terug naar stap 1, verzendt het vernieuwings token samen met de claim uitdaging terug naar Azure AD. Azure AD evalueert alle voor waarden en weigert de toegang in dit geval.

## <a name="enable-or-disable-cae-preview"></a>CAE in-of uitschakelen (preview-versie)

1. Meld u aan bij de **Azure Portal** als beheerder voor voorwaardelijke toegang, beveiligings beheerder of globale beheerder
1. Blader naar **Azure Active Directory**  >    >  **evaluatie van continue toegang** van de beveiliging.
1. Kies **voor beeld inschakelen**.

Op deze pagina kunt u optioneel de gebruikers en groepen beperken die aan de preview-versie worden onderworpen.

![De preview-versie van CAE inschakelen in de Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="supported-location-policies"></a>Ondersteund locatie beleid

Voor CAE hebben we alleen inzicht in benoemde op IP gebaseerde benoemde locaties. We hebben geen inzicht in andere locatie-instellingen, zoals door [MFA vertrouwde IP-adressen](../authentication/howto-mfa-mfasettings.md#trusted-ips) of locaties op basis van een land. Wanneer de gebruiker afkomstig is van een door MFA vertrouwd IP-adres of vertrouwde locaties met door MFA vertrouwde Ip's of land locatie, wordt de CAE niet afgedwongen nadat de gebruiker naar een andere locatie is verplaatst. In dergelijke gevallen geven we een CAE-token van 1 uur zonder onmiddellijke controle op IP-afdwinging uit.

> [!IMPORTANT]
> Bij het configureren van locaties voor continue toegang, gebruikt u alleen de [voor waarde voor voorwaardelijke toegang op basis van een IP-](../conditional-access/location-condition.md#preview-features) adres en configureert u alle IP-adressen, **met inbegrip van IPv4 en IPv6**, die kunnen worden weer gegeven door uw ID-provider en bronnen provider. Gebruik geen land locatie voorwaarden of de functie voor vertrouwde IP-adressen die beschikbaar is op de pagina Service-instellingen van Azure AD Multi-Factor Authentication.

### <a name="ip-address-configuration"></a>IP-adresconfiguratie

Uw ID-provider en resource providers kunnen verschillende IP-adressen zien. Dit kan gebeuren als gevolg van implementaties van de netwerk proxy in uw organisatie of onjuiste IPv4/IPv6-configuraties tussen uw ID-provider en resource provider. Bijvoorbeeld:

- Uw ID-provider ziet één IP-adres van de client.
- De resource provider ziet een ander IP-adres dan de client na het door lopen van een proxy.
- Het IP-adres dat uw ID-provider ziet maakt deel uit van een toegestaan IP-bereik in het beleid, maar het IP-adres van de resource provider is niet.

Als dit scenario in uw omgeving bestaat om oneindige lussen te voor komen, geeft Azure AD een CAE-token van één uur op en wordt het wijzigen van de client locatie niet afgedwongen. Zelfs in dit geval is de beveiliging verbeterd ten opzichte van de traditionele tokens van één uur, omdat we nog steeds de [andere gebeurtenissen](#critical-event-evaluation) evalueren behalve client locatie wijzigings gebeurtenissen.

### <a name="office-and-web-account-manager-settings"></a>Instellingen voor Office en web-account beheer

| Kanaal voor Office-updates | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Semi-Annual Enter prise-kanaal | Als deze instelling is ingeschakeld of is ingesteld op 1, wordt CAE niet ondersteund. | Als deze instelling is ingeschakeld of is ingesteld op 1, wordt CAE niet ondersteund. |
| Huidig kanaal <br> of <br> Maandelijks bedrijfs kanaal | CAE wordt ondersteund ongeacht de instelling | CAE wordt ondersteund ongeacht de instelling |

Zie [overzicht van update kanalen voor Microsoft 365-apps](/deployoffice/overview-update-channels)voor een uitleg van de Office Update-kanalen. Het wordt aanbevolen dat organisaties web account manager (WAM) niet uitschakelen.

### <a name="policy-change-timing"></a>Timing van beleids wijzigingen

Als gevolg van de kans op replicatie vertraging tussen Azure AD en resource providers, kunnen beleids wijzigingen die door beheerders zijn aangebracht, tot twee uur effectief duren voor Exchange Online.

Voor beeld: de beheerder voegt een beleid toe om te voor komen dat een bereik van IP-adressen op 11:00 uur toegang krijgt tot e-mail, een gebruiker die afkomstig is van dat IP-bereik voordat de toegang tot 1:00 uur kan worden voortgezet.

### <a name="coauthoring-in-office-apps"></a>Cocreatie in Office-apps

Wanneer meerdere gebruikers tegelijkertijd samen werken aan hetzelfde document, wordt de toegang van de gebruiker tot het document mogelijk niet onmiddellijk ingetrokken door CAE op basis van gebruikers intrekking of beleids wijzigings gebeurtenissen. In dit geval verliest de gebruiker de toegang volledig na, sluit u het document, sluit u Word, Excel of Power Point, of na een periode van 10 uur.

Als u deze tijd wilt verkorten, kan een share point-beheerder de maximale levens duur voor documenten die zijn opgeslagen in share point online en OneDrive voor bedrijven, eventueel verlagen. hiervoor [configureert u een netwerk locatie beleid in share point online](/sharepoint/control-access-based-on-network-location). Zodra deze configuratie is gewijzigd, wordt de maximale levens duur van cocreatie sessies beperkt tot 15 minuten en kan verder worden aangepast met de share point online Power shell-opdracht ' set-SPOTenant – IPAddressWACTokenLifetime '

### <a name="enable-after-a-user-is-disabled"></a>Inschakelen nadat een gebruiker is uitgeschakeld

Als u een gebruikers recht inschakelt nadat dit is uitgeschakeld. Er is enige latentie voordat het account kan worden ingeschakeld. SPO en teams hebben een vertraging van 15 minuten. De vertraging is 35-40 minuten voor EXO.

## <a name="faqs"></a>Veelgestelde vragen

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hoe wordt het gebruik van CAE met de aanmeldings frequentie?

De aanmeldings frequentie wordt gerespecteerd met of zonder CAE.

## <a name="next-steps"></a>Volgende stappen

[Aangekondigde evaluatie van voortdurende toegang](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)