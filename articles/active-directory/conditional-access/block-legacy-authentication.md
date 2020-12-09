---
title: Verouderde verificatie blok keren-Azure Active Directory
description: Meer informatie over het verbeteren van uw beveiligings postuur door verouderde verificatie te blok keren met voorwaardelijke toegang van Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8bc1a96109eda9cc2ee1f86cec892cc5ce9308d
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860317"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedure: verouderde verificatie naar Azure AD blok keren met voorwaardelijke toegang   

Om uw gebruikers eenvoudig toegang te geven tot uw Cloud-apps, ondersteunt Azure Active Directory (Azure AD) een breed scala aan verificatie protocollen, waaronder verouderde verificatie. Verouderde protocollen bieden echter geen ondersteuning voor multi-factor Authentication (MFA). MFA is in veel omgevingen een algemene vereiste om identiteits diefstal te verhelpen. 

Alex Weinert, directeur van identiteits beveiliging bij micro soft, in zijn 12 maart 2020 blog post [nieuwe hulp middelen voor het blok keren van verouderde verificatie in uw organisatie benadrukt u](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) waarom organisaties verouderde verificatie moeten blok keren en welke extra Hulpprogram Ma's micro soft biedt om deze taak uit te voeren:

> Voor MFA moet u ook verouderde verificatie blok keren. Dit komt doordat verouderde verificatie protocollen, zoals POP, SMTP, IMAP en MAPI, geen MFA afdwingen, waardoor ze voorkeurs toegangs punten voor aanvallers aanvallen van uw organisatie...
> 
>... De aantallen van verouderde verificatie van een analyse van Azure Active Directory verkeer (Azure AD) zijn Stark:
> 
> - Meer dan 99 procent van de aanvallen voor wachtwoord spray gebruiken verouderde verificatie protocollen
> - Meer dan 97 procent van de referentie aanvallen gebruiken verouderde verificatie
> - Azure AD-accounts in organisaties met een uitgeschakelde verouderde verificatie-ervaring van 67 procent minder dan die waar oudere authenticatie is ingeschakeld
>

Als uw omgeving klaar is om verouderde verificatie te blok keren om de beveiliging van uw Tenant te verbeteren, kunt u dit doel bereiken met voorwaardelijke toegang. In dit artikel wordt uitgelegd hoe u beleid voor voorwaardelijke toegang kunt configureren waarmee verouderde verificatie voor uw Tenant wordt geblokkeerd.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de [basis concepten](overview.md) van voorwaardelijke toegang tot Azure AD.

## <a name="scenario-description"></a>Scenariobeschrijving

Azure AD biedt ondersteuning voor een aantal van de meest gebruikte verificatie-en autorisatie protocollen, inclusief verouderde verificatie. Verouderde verificatie verwijst naar protocollen die gebruikmaken van basis verificatie. Normaal gesp roken kunnen deze protocollen geen type verificatie van de tweede factor afdwingen. Voor beelden voor apps die zijn gebaseerd op verouderde verificatie zijn:

- Oudere Microsoft Office-apps
- Apps die e-mail protocollen zoals POP, IMAP en SMTP gebruiken

Verificatie met één factor (bijvoorbeeld gebruikers naam en wacht woord) is niet voldoende dagen. Wacht woorden zijn ongeldig omdat ze gemakkelijk te raden zijn en wij (mens) zijn slecht bij het kiezen van goede wacht woorden. Wacht woorden zijn ook kwetsbaar voor diverse aanvallen zoals phishing en wachtwoord spray. Een van de eenvoudigste dingen die u kunt doen om te beschermen tegen wachtwoord dreigingen is het implementeren van multi-factor Authentication (MFA). Met MFA, zelfs als een aanvaller in bezit is van het wacht woord van een gebruiker, is het wacht woord alleen niet voldoende om te verifiëren en toegang te krijgen tot de gegevens.

Hoe kunt u voor komen dat apps die verouderde verificatie gebruiken, toegang krijgen tot de resources van uw Tenant? De aanbeveling is om ze alleen te blok keren met een beleid voor voorwaardelijke toegang. Als dat nodig is, kunt u alleen bepaalde gebruikers en specifieke netwerk locaties gebruiken voor het gebruik van apps die zijn gebaseerd op verouderde verificatie.

Beleid voor voorwaardelijke toegang wordt afgedwongen nadat de verificatie van de eerste factor is voltooid. Daarom is voorwaardelijke toegang niet bedoeld als een eerste verdedigings linie voor scenario's als denial-of-service-aanvallen, maar kunnen ook signalen van deze gebeurtenissen (bijvoorbeeld het risico niveau van de aanmelding, de locatie van de aanvraag enzovoort) worden gebruikt om de toegang te bepalen.

## <a name="implementation"></a>Implementatie

In deze sectie wordt uitgelegd hoe u een beleid voor voorwaardelijke toegang configureert om verouderde verificatie te blok keren. 

### <a name="legacy-authentication-protocols"></a>Verouderde verificatie protocollen

De volgende opties worden beschouwd als verouderde verificatie protocollen

- Geverifieerde SMTP: wordt gebruikt door POP-en IMAP-clients voor het verzenden van e-mail berichten.
- Automatische detectie: wordt door Outlook-en EAS-clients gebruikt om post vakken in Exchange Online te vinden en er verbinding mee te maken.
- Exchange ActiveSync (EAS): wordt gebruikt om verbinding te maken met post vakken in Exchange Online.
- Exchange Online Power shell: wordt gebruikt om verbinding te maken met Exchange Online met externe Power shell. Als u basis verificatie voor Exchange Online Power shell blokkeert, moet u de Exchange Online Power shell-module gebruiken om verbinding te maken. Zie [verbinding maken met Exchange Online Power shell met multi-factor Authentication](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)voor instructies.
- Exchange Web Services (EWS): een programmeer interface die wordt gebruikt door Outlook, Outlook voor Mac en apps van derden.
- IMAP4: wordt gebruikt door IMAP-e-mailclients.
- MAPI via HTTP (MAPI/HTTP): wordt gebruikt door Outlook 2010 en hoger.
- Offline adresboek (OAB): een kopie van de adres lijst verzamelingen die worden gedownload en gebruikt door Outlook.
- Outlook Anywhere (RPC via HTTP): wordt gebruikt door Outlook 2016 en eerder.
- Outlook-service: wordt gebruikt door de mail-en agenda-app voor Windows 10.
- POP3: wordt gebruikt door POP-e-mailclients.
- Reporting Web Services: wordt gebruikt voor het ophalen van rapport gegevens in Exchange Online.
- Andere clients-andere protocollen die zijn geïdentificeerd als het gebruik van verouderde verificatie.

Zie voor meer informatie over deze verificatie protocollen en-services [aanmeldings activiteiten rapporten in de Azure Active Directory Portal](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Gebruik van verouderde authenticatie identificeren

Voordat u verouderde verificatie in uw Directory kunt blok keren, moet u eerst begrijpen of uw gebruikers apps hebben die gebruikmaken van verouderde verificatie en hoe dit van invloed is op uw algemene Directory. Aanmeld logboeken van Azure AD kunnen worden gebruikt om te begrijpen of u gebruikmaakt van verouderde verificatie.

1. Navigeer naar het **Azure Portal**  >  **Azure Active Directory**  >  **-Azure Active Directory aanmeldingen**.
1. Voeg de kolom client toepassing toe als deze niet wordt weer gegeven door te klikken op de client-app **Columns**  >  **Client App**.
1. **Filters toevoegen**  >  **Client-App** > alle verouderde verificatie protocollen selecteren. Selecteer buiten het dialoog venster filteren om uw selecties toe te passen en sluit het dialoog venster.

Bij filteren worden alleen de aanmeldings pogingen weer gegeven die zijn gemaakt door verouderde verificatie protocollen. Als u op elke afzonderlijke aanmeldings poging klikt, wordt er meer informatie weer gegeven. In het veld **client-app** onder het tabblad **basis informatie** wordt aangegeven welk verouderde verificatie protocol is gebruikt.

In deze logboeken wordt aangegeven welke gebruikers nog steeds afhankelijk zijn van verouderde verificatie en welke toepassingen verouderde protocollen gebruiken om verificatie aanvragen uit te voeren. Voor gebruikers die niet in deze logboeken worden weer gegeven en worden bevestigd dat ze geen verouderde authenticatie gebruiken, implementeert alleen het beleid voor voorwaardelijke toegang voor deze gebruikers.

## <a name="block-legacy-authentication"></a>Verouderde verificatie blokkeren 

Er zijn twee manieren om het beleid voor voorwaardelijke toegang te gebruiken om verouderde verificatie te blok keren.

- [Verouderde verificatie rechtstreeks blok keren](#directly-blocking-legacy-authentication)
- [Verouderde verificatie indirect blok keren](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Verouderde verificatie rechtstreeks blok keren

De eenvoudigste manier om verouderde verificatie voor uw hele organisatie te blok keren, is door een beleid voor voorwaardelijke toegang te configureren dat specifiek van toepassing is op verouderde authenticatie clients en de toegang blokkeert. Wanneer u gebruikers en toepassingen aan het beleid toewijst, moet u ervoor zorgen dat u gebruikers en service accounts uitsluit die nog moeten worden aangemeld met verouderde verificatie. Configureer de client-apps voor waarde door **Exchange ActiveSync-clients** en **andere clients** te selecteren. Als u de toegang voor deze client-Apps wilt blok keren, configureert u de toegangs elementen om de toegang te blok keren.

![Voor waarde voor client-apps geconfigureerd om verouderde auth te blok keren](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Verouderde verificatie indirect blok keren

Zelfs als uw organisatie niet gereed is voor het blok keren van verouderde verificatie voor de hele organisatie, moet u ervoor zorgen dat aanmeldingen met behulp van verouderde verificatie geen beleids regels passeren waarvoor toekennings controles nodig zijn, zoals het vereisen van multi-factor Authentication of compatibele/hybride Azure AD-apparaten. Tijdens de verificatie bieden verouderde verificatie-clients geen ondersteuning voor het verzenden van MFA, apparaatcompatibiliteit of status informatie aan Azure AD. Daarom moet u beleids regels met granting Controls Toep assen op alle client toepassingen, zodat verouderde op verificatie gebaseerde aanmeldingen die niet aan de toekennings besturings elementen kunnen voldoen, worden geblokkeerd. Met de algemene Beschik baarheid van de voor waarde client-apps in augustus 2020 is nieuw gemaakt beleid voor voorwaardelijke toegang standaard van toepassing op alle client-apps.

![Standaard configuratie van client-apps voor waarde](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Wat u moet weten

Het blok keren van toegang met **andere clients** blokkeert ook Exchange Online Power shell en Dynamics 365 met behulp van basis verificatie.

Het configureren van een beleid voor **andere clients** blokkeert de hele organisatie van bepaalde clients, zoals SPConnect. Dit blok treedt op omdat oudere clients op onverwachte wijze worden geverifieerd. Het probleem is niet van toepassing op grote Office-toepassingen zoals de oudere Office-clients.

Het kan tot 24 uur duren voordat het beleid van kracht wordt.

U kunt alle beschik bare granting-besturings elementen voor de andere voor waarden van de **clients** selecteren. de ervaring van de eind gebruiker is echter altijd dezelfde toegang die wordt geblokkeerd.

### <a name="sharepoint-online-and-b2b-guest-users"></a>Share point online en B2B-gast gebruikers

Om B2B-gebruikers toegang te blok keren via verouderde verificatie naar share point online, moeten organisaties verouderde verificatie uitschakelen in share point met de `Set-SPOTenant` Power shell-opdracht en de `-LegacyAuthProtocolsEnabled` para meter instellen op `$false` . Meer informatie over het instellen van deze para meter vindt u in het referentie document van share point Power shell over [set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant)

## <a name="next-steps"></a>Volgende stappen

- [Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)
- Als u nog niet bekend bent met het configureren van beleid voor voorwaardelijke toegang, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](../authentication/tutorial-enable-azure-mfa.md) voor een voor beeld.
- Zie voor meer informatie over ondersteuning voor moderne authenticatie [hoe moderne verificatie werkt voor office 2013-en office 2016-client-apps](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [Een multifunctioneel apparaat of toepassing instellen om e-mail te verzenden met Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
