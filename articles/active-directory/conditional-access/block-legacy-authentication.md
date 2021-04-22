---
title: Verouderde verificatie blokkeren - Azure Active Directory
description: Meer informatie over het verbeteren van uw beveiligingsstatus door verouderde verificatie te blokkeren met behulp van voorwaardelijke toegang van Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84c8b82219f2b2aea39bbcd23f030243d9ea8635
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861801"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedure: verouderde verificatie bij Azure AD met voorwaardelijke toegang blokkeren   

Om uw gebruikers eenvoudig toegang te geven tot uw cloud-apps, ondersteunt Azure Active Directory (Azure AD) een breed scala aan verificatieprotocollen, waaronder verouderde verificatie. Verouderde protocollen bieden echter geen ondersteuning voor Meervoudige verificatie (MFA). MFA is in veel omgevingen een veelvoorkomende vereiste voor identiteitsdiefstal. 

Alex Weinert, Director of Identity Security bij Microsoft, in zijn blogpost van 12 maart 2020 Nieuwe hulpprogramma's om verouderde verificatie [in](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) uw organisatie te blokkeren benadrukt waarom organisaties verouderde verificatie moeten blokkeren en welke andere hulpprogramma's Microsoft biedt om deze taak uit te voeren:

> Om MFA effectief te laten zijn, moet u ook verouderde verificatie blokkeren. Dit komt doordat verouderde verificatieprotocollen zoals POP, SMTP, IMAP en MAPI MFA niet kunnen afdwingen, waardoor ze voorkeursinvoerpunten worden voor aanvallers die uw organisatie aanvallen...
> 
>... De getallen over verouderde verificatie uit een analyse van Azure Active Directory (Azure AD)-verkeer zijn:
> 
> - Meer dan 99 procent van de wachtwoordsprayaanvallen maakt gebruik van verouderde verificatieprotocollen
> - Meer dan 97 procent van de aanvallen met referentiegegevens gebruikt verouderde verificatie
> - Azure AD-accounts in organisaties die verouderde verificatie hebben uitgeschakeld, hebben 67 procent minder compromissen dan wanneer verouderde verificatie is ingeschakeld
>

Als uw omgeving gereed is om verouderde verificatie te blokkeren om de beveiliging van uw tenant te verbeteren, kunt u dit doel bereiken met voorwaardelijke toegang. In dit artikel wordt uitgelegd hoe u beleid voor voorwaardelijke toegang kunt configureren dat verouderde verificatie voor uw tenant blokkeert. Klanten zonder licenties die voorwaardelijke toegang bevatten, kunnen gebruikmaken van standaardinstellingen voor [beveiliging](../fundamentals/concept-fundamentals-security-defaults.md)) om verouderde verificatie te blokkeren.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgenomen dat u bekend bent met de [basisconcepten](overview.md) van voorwaardelijke toegang van Azure AD.

## <a name="scenario-description"></a>Scenariobeschrijving

Azure AD ondersteunt een aantal van de meest gebruikte verificatie- en autorisatieprotocollen, waaronder verouderde verificatie. Verouderde verificatie verwijst naar protocollen die gebruikmaken van basisverificatie. Normaal gesproken kunnen met deze protocollen geen tweede-factorverificatie worden afgedwongen. Voorbeelden voor apps die zijn gebaseerd op verouderde verificatie zijn:

- Oudere Microsoft Office apps
- Apps die gebruikmaken van e-mailprotocollen zoals POP, IMAP en SMTP

Enkelvoudige verificatie (bijvoorbeeld gebruikersnaam en wachtwoord) is tegenwoordig niet voldoende. Wachtwoorden zijn slecht omdat ze gemakkelijk te raden zijn en wij (mensen) slecht zijn in het kiezen van goede wachtwoorden. Wachtwoorden zijn ook kwetsbaar voor verschillende aanvallen, zoals phishing en wachtwoordspray. Een van de eenvoudigste dingen die u kunt doen om te beveiligen tegen wachtwoordbedreigingen is het implementeren van Meervoudige verificatie (MFA). Zelfs als een aanvaller met MFA het wachtwoord van een gebruiker in bezit krijgt, is het wachtwoord alleen niet voldoende om de gegevens te verifiëren en te openen.

Hoe kunt u voorkomen dat apps die gebruikmaken van verouderde verificatie toegang hebben tot de resources van uw tenant? Het wordt aanbevolen om ze te blokkeren met een beleid voor voorwaardelijke toegang. Indien nodig, staat u alleen bepaalde gebruikers en specifieke netwerklocaties toe om apps te gebruiken die zijn gebaseerd op verouderde verificatie.

Beleid voor voorwaardelijke toegang wordt afgedwongen nadat de verificatie van de eerste factor is voltooid. Voorwaardelijke toegang is daarom niet bedoeld als een eerste verdedigingslinie voor scenario's zoals DoS-aanvallen (Denial of Service), maar kan gebruikmaken van signalen van deze gebeurtenissen (bijvoorbeeld het aanmeldingsrisiconiveau, de locatie van de aanvraag, en dergelijke) om de toegang te bepalen.

## <a name="implementation"></a>Implementatie

In deze sectie wordt uitgelegd hoe u beleid voor voorwaardelijke toegang configureert om verouderde verificatie te blokkeren. 

### <a name="legacy-authentication-protocols"></a>Verouderde verificatieprotocollen

De volgende opties worden beschouwd als verouderde verificatieprotocollen

- Geverifieerde SMTP: wordt gebruikt door POP- en IMAP-clients om e-mailberichten te verzenden.
- Automatisch ontdekken: wordt gebruikt door Outlook- en EAS-clients om postvakken in Exchange Online te zoeken en er verbinding mee te maken.
- Exchange ActiveSync (EAS) : wordt gebruikt om verbinding te maken met postvakken in Exchange Online.
- Exchange Online PowerShell: wordt gebruikt om verbinding te maken met Exchange Online via externe PowerShell. Als u basisverificatie voor Exchange Online PowerShell blokkeert, moet u de Exchange Online PowerShell-module gebruiken om verbinding te maken. Zie Verbinding maken met [Exchange Online PowerShell met behulp van meervoudige verificatie voor instructies.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
- Exchange Web Services (EWS) : een programmeerinterface die wordt gebruikt door Outlook, Outlook voor Mac en apps van derden.
- IMAP4: wordt gebruikt door IMAP-e-mail-clients.
- MAPI via HTTP (MAPI/HTTP) : wordt gebruikt door Outlook 2010 en hoger.
- Offline Adresboek (OAB) : een kopie van adreslijstverzamelingen die worden gedownload en gebruikt door Outlook.
- Outlook Anywhere (RPC via HTTP) : wordt gebruikt door Outlook 2016 en eerder.
- Outlook-service: wordt gebruikt door de app Mail en Agenda voor Windows 10.
- POP3: wordt gebruikt door POP-e-mail-clients.
- Reporting Web Services: wordt gebruikt om rapportgegevens op te halen in Exchange Online.
- Andere clients: andere protocollen die zijn geïdentificeerd als het gebruik van verouderde verificatie.

Zie Aanmeldactiviteitenrapporten in de Azure Active Directory portal voor meer informatie [over deze verificatieprotocollen en -services.](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)

### <a name="identify-legacy-authentication-use"></a>Verouderd verificatiegebruik identificeren

Voordat u verouderde verificatie in uw directory kunt blokkeren, moet u eerst weten of uw gebruikers apps hebben die gebruikmaken van verouderde verificatie en hoe dit van invloed is op uw algehele directory. Aanmeldingslogboeken van Azure AD kunnen worden gebruikt om te begrijpen of u verouderde verificatie gebruikt.

1. Navigeer **naar Azure Portal**  >  **Azure Active Directory**  >  **Aanmeldingen**.
1. Voeg de kolom Client-app toe als deze niet wordt weergegeven door te klikken op **Columns**  >  **Client App.**
1. **Filters toevoegen**  >  **Client->** alle verouderde verificatieprotocollen. Selecteer buiten het filterdialoogvenster om uw selecties toe te passen en sluit het dialoogvenster.
1. Als u de preview [van](../reports-monitoring/concept-all-sign-ins.md)de nieuwe aanmeldactiviteitenrapporten hebt geactiveerd, herhaalt u de bovenstaande stappen ook op het tabblad Gebruikersaanmeldingen **(niet-interactief).**

Filteren geeft alleen aanmeldingspogingen weer die zijn gedaan door verouderde verificatieprotocollen. Als u op elke afzonderlijke aanmeldingspoging klikt, ziet u meer informatie. In **het veld Client-app** op **het tabblad Basisgegevens** wordt aangegeven welk verouderde verificatieprotocol is gebruikt.

Deze logboeken geven aan welke gebruikers nog steeds afhankelijk zijn van verouderde verificatie en welke toepassingen gebruikmaken van verouderde protocollen om verificatieaanvragen te maken. Voor gebruikers die niet worden weergegeven in deze logboeken en waarvan wordt bevestigd dat ze geen verouderde verificatie gebruiken, implementeert u een beleid voor voorwaardelijke toegang alleen voor deze gebruikers.

## <a name="block-legacy-authentication"></a>Verouderde verificatie blokkeren 

Er zijn twee manieren om beleid voor voorwaardelijke toegang te gebruiken om verouderde verificatie te blokkeren.

- [Verouderde verificatie rechtstreeks blokkeren](#directly-blocking-legacy-authentication)
- [Verouderde verificatie indirect blokkeren](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Verouderde verificatie rechtstreeks blokkeren

De eenvoudigste manier om verouderde verificatie in uw hele organisatie te blokkeren, is door een beleid voor voorwaardelijke toegang te configureren dat specifiek van toepassing is op clients met verouderde verificatie en toegang blokkeert. Wanneer u gebruikers en toepassingen toewijst aan het beleid, moet u gebruikers en serviceaccounts uitsluiten die zich nog steeds moeten aanmelden met verouderde verificatie. Configureer de voorwaarde voor client-apps door **Exchange ActiveSync-clients en** **Andere clients te selecteren.** Als u de toegang voor deze client-apps wilt blokkeren, configureert u het toegangsbesturingselement op Toegang blokkeren.

![Voorwaarde voor client-apps geconfigureerd om verouderde auth te blokkeren](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Verouderde verificatie indirect blokkeren

Zelfs als uw organisatie niet gereed is om verouderde verificatie in de hele organisatie te blokkeren, moet u ervoor zorgen dat aanmeldingen met verouderde verificatie geen beleidsregels omzeilen waarvoor toekenningscontroles zijn vereist, zoals meervoudige verificatie of compatibele/hybride Azure AD-apparaten. Tijdens de verificatie bieden verouderde verificatie-clients geen ondersteuning voor het verzenden van MFA, apparaat naleving of statusinformatie aan Azure AD. Pas daarom beleid met toekenningsbesturingselementen toe op alle clienttoepassingen, zodat aanmeldingen op basis van verouderde verificatie die niet voldoen aan de toekenningsbesturingselementen, worden geblokkeerd. Met de algemene beschikbaarheid van de voorwaarde voor client-apps in augustus 2020 zijn nieuw gemaakte beleidsregels voor voorwaardelijke toegang standaard van toepassing op alle client-apps.

![Standaardconfiguratie voor client-apps](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Wat u moet weten

Het blokkeren van toegang **met andere clients** blokkeert ook Exchange Online PowerShell en Dynamics 365 met behulp van basisvereening.

Het configureren van een beleid **voor andere clients** blokkeert de hele organisatie van bepaalde clients, zoals SPConnect. Dit blok teert omdat oudere clients op onverwachte manieren worden geverifieerd. Het probleem is niet van toepassing op grote Office-toepassingen zoals de oudere Office-clients.

Het kan tot 24 uur duren voordat het beleid van kracht wordt.

U kunt alle beschikbare toekenningsbesturingselementen selecteren voor de **voorwaarde Andere clients;** De eindgebruikerservaring is echter altijd hetzelfde: geblokkeerde toegang.

### <a name="sharepoint-online-and-b2b-guest-users"></a>SharePoint Online- en B2B-gastgebruikers

Als u B2B-gebruikerstoegang via verouderde verificatie voor SharePoint Online wilt blokkeren, moeten organisaties verouderde verificatie op SharePoint uitschakelen met behulp van de PowerShell-opdracht en de `Set-SPOTenant` `-LegacyAuthProtocolsEnabled` parameter instellen op `$false` . Meer informatie over het instellen van deze parameter vindt u in het SharePoint PowerShell-referentiedocument met [betrekking tot Set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant)

## <a name="next-steps"></a>Volgende stappen

- [Invloed bepalen met de rapportmodus Voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)
- Als u nog niet bekend bent met het configureren van beleid voor voorwaardelijke toegang, zie MFA vereisen voor specifieke [apps met Azure Active Directory voorwaardelijke](../authentication/tutorial-enable-azure-mfa.md) toegang voor een voorbeeld.
- Zie How [modern authentication works for Office 2013 and Office 2016 client apps (Hoe moderne verificatie werkt voor Office 2013- en Office 2016-client-apps)](/office365/enterprise/modern-auth-for-office-2013-and-2016) voor meer informatie over moderne verificatieondersteuning 
- [Een apparaat of toepassing met meerdere functies instellen voor het verzenden van e-mail met Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)