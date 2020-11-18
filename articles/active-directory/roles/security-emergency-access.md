---
title: Beheerders accounts voor nood toegang beheren-Azure AD | Microsoft Docs
description: In dit artikel wordt beschreven hoe u accounts voor toegang tot nood gevallen kunt gebruiken om te voor komen dat uw Azure Active Directory-organisatie (Azure AD) per ongeluk wordt vergrendeld.
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10d93b92f3bb0adfe734ad439079afdfcaa6270e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834435"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Accounts voor nood toegang beheren in azure AD

Het is belang rijk dat u voor komt dat u per ongeluk uw Azure Active Directory-organisatie (Azure AD) hebt vergrendeld, omdat u zich niet kunt aanmelden of een ander gebruikers account wilt activeren als beheerder. U kunt de gevolgen van onopzettelijke beheerders toegang beperken door twee of meer *accounts voor toegang tot een nood geval* in uw organisatie te maken.

Accounts voor toegang in nood gevallen zijn zeer privileged en ze zijn niet toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt. We raden u aan om het gebruik van het nood-account te beperken tot alleen de tijden waarop dit absoluut nood zakelijk is.

Dit artikel bevat richt lijnen voor het beheren van accounts voor toegang tot nood gevallen in azure AD.

## <a name="why-use-an-emergency-access-account"></a>Waarom een account voor toegang tot nood gevallen gebruiken

Een organisatie moet mogelijk een account voor nood toegang gebruiken in de volgende situaties:

- De gebruikers accounts federatief zijn en Federatie is momenteel niet beschikbaar vanwege een onderbreking van het netwerk of een storing in de identiteits provider. Als de host van de identiteits provider in uw omgeving is uitgeschakeld, kunnen gebruikers zich mogelijk niet aanmelden wanneer Azure AD wordt omgeleid naar hun ID-provider.
- De beheerders zijn geregistreerd via Azure AD Multi-Factor Authentication en alle afzonderlijke apparaten zijn niet beschikbaar of de service is niet beschikbaar. Gebruikers kunnen de Multi-Factor Authentication mogelijk niet volt ooien om een rol te activeren. Een storing in een mobiel netwerk zorgt er bijvoorbeeld voor dat ze geen telefoon gesprekken kunnen beantwoorden of SMS-berichten kunnen ontvangen, de enige twee verificatie mechanismen die ze voor hun apparaat hebben geregistreerd.
- De persoon met de meest recente globale beheerders toegang heeft de organisatie verlaten. Azure AD voor komt dat het account van de laatste globale beheerder wordt verwijderd, maar dit voor komt niet dat het account on-premises wordt verwijderd of uitgeschakeld. Een van beide situaties kan ervoor zorgen dat de organisatie het account niet kan herstellen.
- Onvoorziene omstandigheden, zoals een natuur ramp, waarbij een mobiele telefoon of andere netwerken mogelijk niet beschikbaar zijn. 

## <a name="create-emergency-access-accounts"></a>Nood toegangs accounts maken

Maak twee of meer nood toegangs accounts. Deze accounts moeten alleen Cloud accounts zijn die het onmicrosoft.com- \* domein gebruiken en die niet federatief of gesynchroniseerd zijn vanuit een on-premises omgeving.

Bij het configureren van deze accounts moet aan de volgende vereisten worden voldaan:

- De accounts voor toegang in nood gevallen mogen niet worden gekoppeld aan een afzonderlijke gebruiker in de organisatie. Zorg ervoor dat uw accounts niet zijn verbonden met mobiele telefoons die door werk nemers worden geleverd, hardware-tokens die met afzonderlijke werk nemers reizen of andere specifieke referenties voor werk nemers. Deze voorzorgsmaatregel geldt voor instanties waarbij een individuele werk nemer onbereikbaar is wanneer de referentie nodig is. Het is belang rijk om ervoor te zorgen dat geregistreerde apparaten worden bewaard op een bekende, veilige locatie met meerdere manieren om te communiceren met Azure AD.
- Het authenticatie mechanisme dat wordt gebruikt voor een account voor toegang in nood gevallen moet verschillend zijn van de accounts die worden gebruikt door uw andere Administrator, waaronder andere accounts voor toegang tot een nood geval.  Als uw normale beheerder zich bijvoorbeeld via on-premises MFA bevindt, is Azure AD MFA een ander mechanisme.  Als Azure AD MFA echter het primaire deel van de verificatie is voor uw beheerders accounts, kunt u hiervoor een andere benadering gebruiken, zoals het gebruik van voorwaardelijke toegang met een externe MFA-provider via aangepaste besturings elementen.
- Het apparaat of de referentie mag niet verlopen of een automatische opschoning hebben vanwege een gebrek aan gebruik.  
- U moet de globale beheerdersrol permanent maken voor uw accounts voor toegang voor nood gevallen. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Ten minste één account uitsluiten van multi-factor Authentication op basis van een telefoon

Om het risico te verkleinen dat een aanval wordt veroorzaakt door een wacht woord, raadt Azure AD u aan om multi-factor Authentication voor alle afzonderlijke gebruikers te vereisen. Deze groep bevat beheerders en alle andere (bijvoorbeeld financiële functionarissen) waarvan het aangetaste account een belang rijke invloed zou hebben.

Ten minste één van uw accounts voor toegang in nood gevallen moet echter niet hetzelfde multi-factor Authentication-mechanisme hebben als uw andere niet-nood-accounts. Dit geldt ook voor multi-factor Authentication-oplossingen van derden. Als u een beleid voor voorwaardelijke toegang hebt om [multi-factor Authentication te vereisen voor elke beheerder](../authentication/howto-mfa-userstates.md) van Azure AD en andere SaaS-apps (Software as a Service), moet u nood toegangs accounts uit deze vereiste uitsluiten en in plaats daarvan een ander mechanisme configureren. Bovendien moet u ervoor zorgen dat de accounts geen multi-factor Authentication-beleid per gebruiker hebben.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Ten minste één account uitsluiten van beleid voor voorwaardelijke toegang

Tijdens een nood geval is het niet mogelijk dat een beleid uw toegang blokkeert om een probleem op te lossen. Ten minste één nood toegangs account moet worden uitgesloten van alle beleids regels voor voorwaardelijke toegang.

## <a name="federation-guidance"></a>Federatie richtlijnen

Een extra optie voor organisaties die gebruik maken van AD Domain Services en ADFS of een vergelijk bare ID-provider om te communiceren met Azure AD, is het configureren van een account voor toegang met nood gevallen waarvoor MFA-claim kan worden geleverd door die id-provider.  Zo kan het account voor toegang in nood gevallen worden ondersteund door een certificaat en sleutel paar, zoals het is opgeslagen op een Smart Card.  Wanneer deze gebruiker is geverifieerd voor AD, kan ADFS een claim leveren aan Azure AD die aangeeft dat de gebruiker voldoet aan MFA-vereisten.  Zelfs bij deze benadering moeten organisaties nog steeds toegang hebben tot de toegangs accounts voor de cloud in het geval dat Federatie niet tot stand kan worden gebracht. 

## <a name="store-account-credentials-safely"></a>Account referenties veilig opslaan

Organisaties moeten ervoor zorgen dat de referenties voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die gemachtigd zijn om ze te gebruiken. Sommige klanten gebruiken een Smart Card en andere gebruiken wacht woorden. Een wacht woord voor een account voor toegang in nood gevallen wordt doorgaans onderverdeeld in twee of drie delen, geschreven op afzonderlijke stukjes papier en opgeslagen in veilige, brand bare veilige, beveiligde locaties.

Als u wacht woorden gebruikt, moet u ervoor zorgen dat de accounts sterke wacht woorden hebben die het wacht woord niet laten verlopen. In het ideale geval moeten de wacht woorden ten minste 16 tekens lang zijn en wille keurig worden gegenereerd.

## <a name="monitor-sign-in-and-audit-logs"></a>Aanmeld-en audit logboeken bewaken

Organisaties moeten de activiteiten voor aanmelding en controle logboeken van de nood accounts controleren en meldingen aan andere beheerders activeren. Wanneer u de activiteit bewaken op de afbreek glazen accounts, kunt u controleren of deze accounts alleen worden gebruikt voor het testen of voor de daad werkelijke nood gevallen. U kunt Azure Log Analytics gebruiken om de aanmeldings logboeken te bewaken en e-mail-en SMS-meldingen te activeren aan uw beheerders wanneer het beëindigen van glazen accounts zich aanmeldt.

### <a name="prerequisites"></a>Vereisten

1. [Verzend logboeken van Azure AD-aanmelding](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) naar Azure monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Object-Id's van de afbreek glazen accounts ophalen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een account dat is toegewezen aan de rol gebruikers beheerder.
1. Selecteer **Azure Active Directory**  >  **gebruikers**.
1. Zoek het afbreek glas account en selecteer de naam van de gebruiker.
1. Kopieer het kenmerk object ID en sla het op, zodat u het later kunt gebruiken.
1. Herhaal de vorige stappen voor het tweede account voor het afbreek glas.

### <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een account dat is toegewezen aan de rol voor het controleren van inzender in azure monitor.
1. Selecteer **alle services**', voer ' Log Analytics ' in de zoek opdracht in en selecteer vervolgens **log Analytics-werk ruimten**.
1. Selecteer een werkruimte.
1. Selecteer in uw werk ruimte **waarschuwingen**  >  **nieuwe waarschuwings regel**.
    1. Controleer onder **resource** of het abonnement is waarmee u de waarschuwings regel wilt koppelen.
    1. Selecteer onder **voor waarde** de optie **toevoegen**.
    1. Selecteer **aangepaste zoek opdracht in logboek** onder **signaal naam**.
    1. Voer onder **Zoek query** de volgende query in en voeg de object-id's van de twee afbreek glazen accounts in.
        > [!NOTE]
        > Voor elk extra account voor een afbreek glazen dat u wilt toevoegen, voegt u een andere ' or UserId = = ' ObjectGuid ' ' toe aan de query.

        ![De object-Id's van de afbreek glazen accounts toevoegen aan een waarschuwings regel](./media/security-emergency-access/query-image1.png)

    1. Onder **waarschuwings logica** voert u het volgende in:

        - Gebaseerd op: aantal resultaten
        - Operator: groter dan
        - Drempel waarde: 0

    1. Onder **geëvalueerd op basis van** selecteert u de **periode (in minuten)** voor hoe lang de query moet worden uitgevoerd en de **frequentie (in minuten)** voor hoe vaak de query moet worden uitgevoerd. De frequentie moet kleiner dan of gelijk aan de periode zijn.

        ![waarschuwings logica](./media/security-emergency-access/alert-image2.png)

    1. Selecteer **Gereed**. U kunt nu de geschatte maandelijkse kosten van deze waarschuwing weer geven.
1. Selecteer een actie groep van gebruikers die door de waarschuwing moeten worden gewaarschuwd. Zie [een actie groep maken](#create-an-action-group)als u er een wilt maken.
1. Als u de e-mail melding die wordt verzonden naar de leden van de actie groep wilt aanpassen, selecteert u acties onder **acties aanpassen**.
1. Geef onder **waarschuwings Details** de naam van de waarschuwings regel op en voeg een optionele beschrijving toe.
1. Stel het **Ernst niveau** van de gebeurtenis in. U kunt het beste instellen op **kritiek (Ernst 0)**.
1. Onder **regel inschakelen bij het maken**, blijft de optie ingesteld op **Ja**.
1. Als u waarschuwingen voor een tijdje wilt uitschakelen, schakelt u het selectie vakje **waarschuwingen onderdrukken** in en voert u de wacht tijd in voordat u opnieuw een waarschuwing krijgt en selecteert u vervolgens **Opslaan**.
1. Klik op **Waarschuwingsregel maken**.

### <a name="create-an-action-group"></a>Een actiegroep maken

1. Selecteer **een actie groep maken**.

    ![een actie groep maken voor meldings acties](./media/security-emergency-access/action-group-image3.png)

1. Voer de naam van de actie groep en een korte naam in.
1. Controleer het abonnement en de resource groep.
1. Onder actie Type selecteert u **e-mail/SMS/push/Voice**.
1. Voer een actie naam in, zoals **globale beheerder melden**.
1. Selecteer het **actie type** als **e-mail/SMS/push/Voice**.
1. Selecteer **Details bewerken** om de meldings methoden te selecteren die u wilt configureren, voer de vereiste contact gegevens in en selecteer **OK** om de gegevens op te slaan.
1. Voeg aanvullende acties toe die u wilt activeren.
1. Selecteer **OK**.

## <a name="validate-accounts-regularly"></a>Regel matig accounts valideren

Wanneer u mede werkers traint om accounts voor toegang in nood gevallen te gebruiken en de accounts voor nood toegang te valideren, moet u Mini maal de volgende stappen uitvoeren met regel matige tussen pozen:

- Zorg ervoor dat werk nemers met een beveiligings controle op de hoogte zijn van de activiteit account controleren.
- Zorg ervoor dat het proces voor nood gevallen waarin deze accounts worden gebruikt, wordt gedocumenteerd en actueel is.
- Zorg ervoor dat beheerders en beveiligings ambtenaren die mogelijk deze stappen moeten uitvoeren tijdens een nood geval worden getraind voor het proces.
- Werk de account referenties bij, met name wacht woorden, voor uw accounts voor toegang tot nood gevallen en controleer vervolgens of de accounts voor toegang in nood gevallen zich kunnen aanmelden en beheer taken uitvoeren.
- Zorg ervoor dat gebruikers niet Multi-Factor Authentication of selfservice voor het opnieuw instellen van wacht woorden (SSPR) hebben geregistreerd op het apparaat of persoonlijke gegevens van een individuele gebruiker. 
- Als de accounts zijn geregistreerd voor Multi-Factor Authentication op een apparaat, voor gebruik tijdens het activeren of rollen, moet u ervoor zorgen dat het apparaat toegankelijk is voor alle beheerders die dit mogelijk moeten gebruiken tijdens een nood geval. Controleer ook of het apparaat kan communiceren via ten minste twee netwerk paden die geen algemene fout modus delen. Het apparaat kan bijvoorbeeld communiceren met Internet via het draadloze netwerk van een faciliteit en een netwerk van een mobiele provider.

Deze stappen moeten met regel matige tussen pozen en voor belang rijke wijzigingen worden uitgevoerd:

- Ten minste elke 90 dagen
- Wanneer er een recente wijziging is opgetreden in IT-mede werkers, zoals een wijziging in de taak, een vertrek of een nieuwe indienstneming
- Wanneer de Azure AD-abonnementen in de organisatie zijn gewijzigd

## <a name="next-steps"></a>Volgende stappen

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](security-planning.md)
- [Gebruikers toevoegen met Azure AD](../fundamentals/add-users-azure-active-directory.md) en [de nieuwe gebruiker toewijzen aan de rol van globale beheerder](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Meld u aan voor Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)als u zich nog niet hebt aangemeld
- [Verificatie in twee stappen vereisen voor een gebruiker](../authentication/howto-mfa-userstates.md)
- [Extra beveiligingen voor globale beheerders in Microsoft 365 configureren](/office365/enterprise/protect-your-global-administrator-accounts), als u Microsoft 365 gebruikt
- [Een toegangs beoordeling van globale beheerders starten](../privileged-identity-management/pim-how-to-start-security-review.md) en [bestaande globale beheerders overstappen naar meer specifieke beheerders rollen](permissions-reference.md)