---
title: Gebruikers beheren die zijn uitgesloten van beleid voor voorwaardelijke toegang - Azure AD
description: Meer informatie over het gebruik Azure Active Directory (Azure AD) voor het beheren van gebruikers die zijn uitgesloten van beleid voor voorwaardelijke toegang
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b2ac36ad1140968fd17db0bed0b60a8aca6a02
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532669"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Azure AD-toegangsbeoordelingen gebruiken om gebruikers te beheren die zijn uitgesloten van beleid voor voorwaardelijke toegang

In een ideale wereld volgen alle gebruikers het toegangsbeleid om de toegang tot de resources van uw organisatie te beveiligen. Soms zijn er echter zakelijke cases die uitzonderingen vereisen. In dit artikel worden enkele voorbeelden van situaties beschreven waarin uitsluitingen mogelijk nodig zijn. Als IT-beheerder kunt u deze taak beheren, toezicht op beleidsuitzonderingen voorkomen en auditors bewijzen dat deze uitzonderingen regelmatig worden gecontroleerd met behulp van toegangsbeoordelingen van Azure Active Directory (Azure AD).

>[!NOTE]
> Een geldige Azure AD Premium P2, Enterprise Mobility + Security E5 betaalde licentie of proeflicentie is vereist voor het gebruik van Azure AD-toegangsbeoordelingen. Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md) voor meer informatie.

## <a name="why-would-you-exclude-users-from-policies"></a>Waarom zou u gebruikers uitsluiten van beleid?

Stel dat u als beheerder besluit om voorwaardelijke toegang van [Azure AD](../conditional-access/concept-conditional-access-policy-common.md) te gebruiken om meervoudige verificatie (MFA) te vereisen en verificatieaanvragen te beperken tot specifieke netwerken of apparaten. Tijdens de implementatieplanning realiseert u zich dat niet alle gebruikers aan deze vereisten kunnen voldoen. U kunt bijvoorbeeld gebruikers hebben die vanuit externe kantoren werken en geen deel uitmaken van uw interne netwerk. Mogelijk moet u gebruikers ook ruimte bieden om verbinding te maken met niet-ondersteunde apparaten terwijl ze wachten tot deze apparaten worden vervangen. Kortom, het bedrijf heeft deze gebruikers nodig om zich aan te melden en hun werk te doen, zodat u ze uitsluit van beleid voor voorwaardelijke toegang.

Een ander voorbeeld: u gebruikt benoemde locaties [in](../conditional-access/location-condition.md) Voorwaardelijke toegang om een set landen en regio's op te geven waaruit u gebruikers geen toegang wilt geven tot hun tenant.

![Benoemde locaties in Voorwaardelijke toegang](./media/conditional-access-exclusion/named-locations.png)

Helaas hebben sommige gebruikers mogelijk nog steeds een geldige reden om zich aan te melden vanuit deze geblokkeerde landen/regio's. Gebruikers kunnen bijvoorbeeld op reis zijn voor hun werk en toegang nodig hebben tot bedrijfsresources. In dit geval kan het beleid voor voorwaardelijke toegang om deze landen/regio's te blokkeren, een cloudbeveiligingsgroep gebruiken voor de uitgesloten gebruikers van het beleid. Gebruikers die tijdens het reizen toegang nodig hebben, kunnen zichzelf aan de groep toevoegen met behulp van [selfservice voor groepsbeheer van Azure AD.](../enterprise-users/groups-self-service-management.md)

Een ander voorbeeld kan zijn dat u een beleid voor voorwaardelijke toegang hebt dat verouderde verificatie blokkeert voor het overgrote [deel van uw gebruikers.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) Als u echter een aantal gebruikers hebt die verouderde verificatiemethoden moeten gebruiken om toegang te krijgen tot uw resources via Office 2010- of IMAP/SMTP/POP-clients, kunt u deze gebruikers uitsluiten van het beleid dat verouderde verificatiemethoden blokkeert.

>[!NOTE]
>Microsoft raadt u ten zeerste aan het gebruik van verouderde protocollen in uw tenant te blokkeren om uw beveiligingsstatus te verbeteren.

## <a name="why-are-exclusions-challenging"></a>Waarom zijn uitsluitingen lastig?

In Azure AD kunt u een beleid voor voorwaardelijke toegang instellen op een set gebruikers. U kunt uitsluitingen ook configureren door Azure AD-rollen, afzonderlijke gebruikers of gasten te selecteren. Houd er rekening mee dat wanneer uitsluitingen worden geconfigureerd, de beleidsintentie niet kan worden afgedwongen voor uitgesloten gebruikers. Als uitsluitingen worden geconfigureerd met behulp van een lijst met gebruikers of met verouderde on-premises beveiligingsgroepen, hebt u beperkte zichtbaarheid in de uitsluitingen. Als gevolg hiervan:

- Gebruikers weten mogelijk niet dat ze zijn uitgesloten.

- Gebruikers kunnen lid worden van de beveiligingsgroep om het beleid over te nemen.

- Uitgesloten gebruikers hebben zich mogelijk al eerder gekwalificeerd voor uitsluiting, maar komen er mogelijk niet meer voor in aanmerking.

Wanneer u voor het eerst een uitsluiting configureert, is er vaak een lijst met gebruikers die het beleid omzeilen. Na een periode worden steeds meer gebruikers toegevoegd aan de uitsluiting en neemt de lijst toe. Op een bepaald moment moet u de lijst bekijken en bevestigen dat elk van deze gebruikers nog steeds in aanmerking komt voor uitsluiting. Het beheren van de uitsluitingslijst, vanuit technisch oogpunt, kan relatief eenvoudig zijn, maar wie neemt de zakelijke beslissingen en hoe zorgt u ervoor dat alles kan worden gecontroleerd? Als u de uitsluiting echter configureert met behulp van een Azure AD-groep, kunt u toegangsbeoordelingen gebruiken als een compenserend besturingselement om de zichtbaarheid te vergroten en het aantal uitgesloten gebruikers te verminderen.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Een uitsluitingsgroep maken in een beleid voor voorwaardelijke toegang

Volg deze stappen om een nieuwe Azure AD-groep en een beleid voor voorwaardelijke toegang te maken dat niet van toepassing is op die groep.

### <a name="create-an-exclusion-group"></a>Een uitsluitingsgroep maken

1. Meld u aan bij Azure Portal.

2. Klik in het linkernavigatievenster **op Azure Active Directory** klik vervolgens op **Groepen.**

3. Klik in het bovenste menu op **Nieuwe groep om** het deelvenster Groep te openen.

4. In de lijst **Groepstype** selecteert u **Beveiliging**. Geef een naam en beschrijving op.

5. Zorg ervoor dat u het **lidmaatschapstype** in stelt **op Toegewezen.**

6. Selecteer de gebruikers die deel moeten uitmaken van deze uitsluitingsgroep en klik vervolgens op **Maken.**

![Deelvenster Nieuwe groep in Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Een beleid voor voorwaardelijke toegang maken dat de groep uitsluit

U kunt nu een beleid voor voorwaardelijke toegang maken dat gebruikmaakt van deze uitsluitingsgroep.

1. Klik in het linkernavigatievenster **op Azure Active Directory** klik vervolgens op **Voorwaardelijke toegang om** de blade Beleid **te** openen.

2. Klik **op Nieuw beleid** om het deelvenster Nieuw **te** openen.

3. Geef een naam op.

4. Klik onder Toewijzingen op **Gebruikers en groepen.**

5. Selecteer op **het** tabblad Opnemen **de optie Alle gebruikers.**

6. Voeg op **het tabblad** Uitsluiten een vinkje toe aan Gebruikers **en groepen** en klik vervolgens op Uitgesloten **gebruikers selecteren.**

7. Selecteer de uitsluitingsgroep die u hebt gemaakt.

   > [!NOTE] 
   > Als een best practice is het raadzaam om ten minste één beheerdersaccount uit te sluiten van het beleid bij het testen om ervoor te zorgen dat u niet bent vergrendeld van uw tenant.

8. Ga door met het instellen van het beleid voor voorwaardelijke toegang op basis van de vereisten van uw organisatie.

![Deelvenster Uitgesloten gebruikers selecteren in Voorwaardelijke toegang](./media/conditional-access-exclusion/select-excluded-users.png)
  
Laten we twee voorbeelden bekijken waarin u toegangsbeoordelingen kunt gebruiken voor het beheren van uitsluitingen in beleid voor voorwaardelijke toegang.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Voorbeeld 1: Toegangsbeoordeling voor gebruikers die toegang hebben vanuit geblokkeerde landen/regio's

Stel dat u een beleid voor voorwaardelijke toegang hebt dat toegang vanuit bepaalde landen/regio's blokkeert. Het bevat een groep die is uitgesloten van het beleid. Hier is een aanbevolen toegangsbeoordeling waarbij leden van de groep worden beoordeeld.

> [!NOTE] 
> Een Globale beheerder of gebruikersbeheerdersrol is vereist om toegangsbeoordelingen te maken.

1. De beoordeling vindt elke week plaats.

2. Zal nooit eindigen om ervoor te zorgen dat u deze uitsluitingsgroep het meest up-to-date houdt.

3. Alle leden van deze groep vallen binnen het bereik van de beoordeling.

4. Elke gebruiker moet zelf bevestigen dat hij of zij nog steeds toegang nodig heeft vanuit deze geblokkeerde landen/regio's. Daarom moet hij of zij nog steeds lid zijn van de groep.

5. Als de gebruiker niet op de beoordelingsaanvraag reageert, wordt deze automatisch uit de groep verwijderd en heeft deze geen toegang meer tot de tenant tijdens het reizen naar deze landen/regio's.

6. Schakel e-mailmeldingen in om gebruikers op de hoogte te stellen van het begin en de voltooiing van de toegangsbeoordeling.

    ![Een deelvenster voor toegangsbeoordeling maken, bijvoorbeeld 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Voorbeeld 2: Toegangsbeoordeling voor gebruikers die toegang hebben met verouderde verificatie

Stel dat u een beleid voor voorwaardelijke toegang hebt dat de toegang blokkeert voor gebruikers die gebruikmaken van verouderde verificatie en oudere clientversies en dat het een groep bevat die is uitgesloten van het beleid. Hier is een aanbevolen toegangsbeoordeling waarbij leden van de groep worden beoordeeld.

1. Deze beoordeling moet een terugkerende beoordeling zijn.

2. Iedereen in de groep moet worden beoordeeld.

3. Deze kan worden geconfigureerd om de eigenaren van bedrijfseenheden weer te maken als de geselecteerde beoordelaars.

4. Pas de resultaten automatisch toe en verwijder gebruikers die niet zijn goedgekeurd om verouderde verificatiemethoden te blijven gebruiken.

5. Het kan nuttig zijn om aanbevelingen in te stellen, zodat revisoren van grote groepen eenvoudig hun beslissingen kunnen nemen.

6. Schakel e-mailmeldingen in, zodat gebruikers op de hoogte worden gesteld van het begin en de voltooiing van de toegangsbeoordeling.

    ![Een deelvenster voor toegangsbeoordeling maken, bijvoorbeeld 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Als u veel uitsluitingsgroepen hebt en daarom meerdere toegangsbeoordelingen moet maken, hebben we nu een API in het Microsoft Graph beta-eindpunt waarmee u ze programmatisch kunt maken en beheren. Als u aan de slag wilt gaan, bekijkt u de API-naslaginformatie voor Azure AD-toegangsbeoordelingen en Voorbeeld van het ophalen van [Azure AD-toegangsbeoordelingen via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096). [](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

## <a name="access-review-results-and-audit-logs"></a>Toegang tot controleresultaten en auditlogboeken

Nu u alles hebt, groep, beleid voor voorwaardelijke toegang en toegangsbeoordelingen hebt, is het tijd om de resultaten van deze beoordelingen te controleren en bij te houden.

1. Open in Azure Portal blade **Toegangsbeoordelingen.**

2. Open het besturingselement en programma dat u hebt gemaakt voor het beheren van de uitsluitingsgroep.

3. Klik **op Resultaten** om te zien wie is goedgekeurd om in de lijst te blijven en wie is verwijderd.

    ![Resultaten van toegangsbeoordelingen geven aan wie is goedgekeurd](./media/conditional-access-exclusion/access-reviews-results.png)

4. Klik vervolgens **op Auditlogboeken** om de acties te zien die tijdens deze beoordeling zijn uitgevoerd.

    ![Auditlogboeken voor toegangsbeoordelingen met acties](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Als IT-beheerder weet u dat het beheer van uitsluitingsgroepen voor uw beleid soms onvermijdelijk is. Het onderhouden van deze groepen, het regelmatig controleren van deze groepen door de bedrijfseigenaar of de gebruikers zelf en het controleren van deze wijzigingen kan echter eenvoudiger worden gemaakt met Azure AD-toegangsbeoordelingen.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)