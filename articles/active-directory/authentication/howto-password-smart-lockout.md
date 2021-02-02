---
title: Aanvallen met slimme vergren deling voor komen-Azure Active Directory
description: Lees hoe u met Azure Active Directory slim vergren delen uw organisatie kunt beschermen tegen aanvallen waarbij gebruikers wacht woorden proberen te raden.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255370"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Gebruikers accounts beveiligen tegen aanvallen met Azure Active Directory slimme vergren deling

Slimme vergren deling helpt bij het vergren delen van ongeldige actors waarmee de wacht woorden van gebruikers worden geraden of methoden voor het afdwingen van brute kracht worden gebruikt om in te komen. Slimme vergren delingen kunnen aanmeldingen herkennen die afkomstig zijn van geldige gebruikers en ze anders behandelen dan gebruikers van aanvallers en andere onbekende bronnen. Aanvallers worden vergrendeld, terwijl uw gebruikers hun accounts blijven gebruiken en productief kunnen zijn.

## <a name="how-smart-lockout-works"></a>Hoe slim vergren delen werkt

Standaard vergrendelt Smart Lock het account van aanmeldings pogingen voor één minuut na 10 mislukte pogingen. Het account vergrendelt na elke volgende mislukte aanmeldings poging, gedurende één minuut om de eerste keer en langer bij de volgende pogingen. Om te voor komen dat een aanvaller dit gedrag kan omzeilen, wordt de snelheid waarmee de vergrendelings periode toeneemt, niet in mindering gespreid over extra mislukte aanmeldings pogingen.

Slimme vergren deling houdt de laatste drie ongeldige hashes met een onjuist wacht woord bij om te voor komen dat de vergrendelings teller voor hetzelfde wacht woord wordt verhoogd. Als iemand hetzelfde onjuiste wacht woord meermaals invoert, wordt het account niet langer vergrendeld.

> [!NOTE]
> De functionaliteit voor het bijhouden van hashes is niet beschikbaar voor klanten waarbij Pass-Through-verificatie is ingeschakeld omdat verificatie on-premises plaatsvindt, niet in de Cloud.

Federatieve implementaties die gebruikmaken van AD FS 2016 en AF FS 2019 kunnen vergelijk bare voor delen bieden met behulp van [AD FS extranet vergrendeling en extranet slimme vergren deling](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Slimme vergren deling is altijd ingeschakeld voor alle Azure AD-klanten, met deze standaard instellingen die de juiste combi natie van beveiliging en bruikbaarheid bieden. Aanpassing van de instellingen voor slim vergren delen, met waarden die specifiek zijn voor uw organisatie, vereist Azure AD Premium P1 of hogere licenties voor uw gebruikers.

Het gebruik van slimme vergren deling garandeert niet dat een authentiekte gebruiker nooit is vergrendeld. Wanneer Slim vergren delen een gebruikers account vergrendelt, proberen we ons het beste om de legitieme gebruiker te vergren delen. De vergrendelings service probeert ervoor te zorgen dat ongeldige actors geen toegang krijgen tot een authentiek gebruikers account. De volgende overwegingen zijn van toepassing:

* Elk Azure AD-Data Center houdt de vergren deling onafhankelijk bij. Een gebruiker heeft (*threshold_limit * datacenter_count*) aantal pogingen als de gebruiker aan elk Data Center komt.
* Slimme vergren deling maakt gebruik van de bekende locatie versus onbekende locatie om onderscheid te maken tussen een ongeldige actor en de legitieme gebruiker. Onbekende en bekende locaties hebben beide afzonderlijke vergrendelings tellers.

Slimme vergren deling kan worden geïntegreerd met hybride implementaties die gebruikmaken van wachtwoord-hash-synchronisatie of Pass Through-verificatie om te voor komen dat accounts voor on-premises Active Directory Domain Services (AD DS) worden vergrendeld door aanvallers. Door slim vergrendelings beleid in azure AD op de juiste wijze in te stellen, kunnen aanvallen worden gefilterd voordat ze on-premises AD DS bereiken.

Bij het gebruik van [Pass-Through-verificatie](../hybrid/how-to-connect-pta.md)gelden de volgende overwegingen:

* De drempel waarde voor vergren delingen van Azure AD is **lager** dan de drempel waarde voor het vergren delen van AD DS Stel de waarden zo in dat de drempel waarde voor het vergren delen van AD DS-account ten minste twee of drie keer langer duurt dan de drempel waarde voor vergren deling van Azure AD.
* De Azure AD-vergrendelings duur moet langer worden ingesteld dan de AD DS account vergrendelings teller opnieuw instellen na de duur. De duur van Azure AD wordt in seconden ingesteld, terwijl de duur van de advertentie binnen enkele minuten is ingesteld.

Als u bijvoorbeeld wilt dat uw Azure AD-teller hoger is dan AD DS, zou Azure AD 120 seconden (2 minuten) zijn, terwijl uw on-premises AD is ingesteld op 1 minuut (60 seconden).

> [!IMPORTANT]
> Op dit moment kan een beheerder de Cloud accounts van gebruikers niet ontgrendelen als ze zijn vergrendeld door de mogelijkheid van slim vergren delen. De beheerder moet wachten tot de vergrendelings duur is verlopen. De gebruiker kan echter worden ontgrendeld met behulp van selfservice voor wachtwoord herstel (SSPR) van een vertrouwd apparaat of locatie.

## <a name="verify-on-premises-account-lockout-policy"></a>On-premises account vergrendelings beleid controleren

Als u wilt controleren of uw on-premises AD DS account vergrendelings beleid, voert u de volgende stappen uit vanaf een systeem dat is gekoppeld aan een domein met beheerders bevoegdheden:

1. Open het groepsbeleid-beheer programma.
2. Bewerk het groeps beleid dat het account vergrendelings beleid van uw organisatie bevat, zoals het **standaard domein beleid**.
3. Bladeren naar **computer configuratie**  >  **beleid**  >  **Windows-instellingen**  >  **beveiligings instellingen**  >  **account beleid** account  >  **vergrendelings beleid**.
4. Controleer de **drempel waarde voor account vergrendeling** en **Stel de teller voor account vergrendeling na waarden opnieuw** in.

![Het vergrendelings beleid voor on-premises Active Directory-account wijzigen](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Slimme vergrendelings waarden voor Azure AD beheren

Op basis van de vereisten van uw organisatie kunt u de waarden voor de slimme vergren deling van Azure AD aanpassen. Aanpassing van de instellingen voor slim vergren delen, met waarden die specifiek zijn voor uw organisatie, vereist Azure AD Premium P1 of hogere licenties voor uw gebruikers.

Voer de volgende stappen uit om de slimme vergrendelings waarden voor uw organisatie te controleren of te wijzigen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek en selecteer *Azure Active Directory* en selecteer **beveiligings**  >  **verificatie methoden**  >  **wachtwoord beveiliging**.
1. Stel de **drempel waarde voor vergren deling** in op basis van het aantal mislukte aanmeldingen dat is toegestaan voor een account voordat het voor het eerst wordt vergrendeld.

    De standaard waarde is 10.

1. Stel de **vergrendelings duur in seconden in**, tot de lengte in seconden van elke vergren deling.

    De standaard waarde is 60 seconden (een minuut).

> [!NOTE]
> Als de eerste aanmelding na een vergren deling ook mislukt, wordt het account opnieuw vergrendeld. Als een account herhaaldelijk wordt vergrendeld, neemt de vergrendelings duur toe.

![Pas het beleid voor slimme vergren deling van Azure AD aan in het Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Bepalen of de functie voor slim vergren delen werkt

Wanneer de drempel waarde voor slim vergren delen wordt geactiveerd, wordt het volgende bericht weer gegeven wanneer het account is vergrendeld:

*Uw account is tijdelijk vergrendeld om onbevoegd gebruik te voor komen. Probeer het later opnieuw. Neem contact op met de beheerder als u nog steeds problemen ondervindt.*

Wanneer u slim vergren delen test, worden uw aanmeldings aanvragen mogelijk verwerkt door verschillende data centers als gevolg van de geografische gedistribueerde en taak verdeling van de Azure AD-verificatie service. In dat scenario, omdat elk Azure AD-Data Center de vergren deling onafhankelijk spoort, kan het langer duren dan het gedefinieerde vergrendelings drempelnummer van pogingen om een vergren deling te veroorzaken. Een gebruiker heeft (*threshold_limit * datacenter_count*) aantal mislukte pogingen als de gebruiker aan elk Data Center komt voordat een vergren deling optreedt.

## <a name="next-steps"></a>Volgende stappen

Als u de ervaring verder wilt aanpassen, kunt u [aangepaste verboden wacht woorden configureren voor Azure AD-wachtwoord beveiliging](tutorial-configure-custom-password-protection.md).

Om gebruikers te helpen hun wacht woord opnieuw in te stellen of te wijzigen vanuit een webbrowser, kunt u de [selfservice voor wachtwoord herstel van Azure AD configureren](tutorial-enable-sspr.md).
