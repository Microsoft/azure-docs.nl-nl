---
title: Veelgestelde vragen over self-service voor wachtwoord herstel-Azure Active Directory
description: Veelgestelde vragen over de self-service voor wachtwoord herstel van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: b79eb842105734f0f6c145884d73f886a02dbbbc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348155"
---
# <a name="self-service-password-reset-frequently-asked-questions"></a>Veelgestelde vragen over self-service voor wachtwoord herstel

Hier volgen enkele veelgestelde vragen (FAQ) voor alle zaken met betrekking tot de selfservice voor wachtwoord herstel.

Als u een algemene vraag hebt over Azure Active Directory (Azure AD) en self-service voor het opnieuw instellen van wacht woorden (SSPR) die hier niet wordt beantwoord, kunt u de Community vragen om hulp op de [pagina micro soft Q&een vraag voor Azure Active Directory](/answers/topics/azure-active-directory.html). Leden van de Community bevatten technici, product managers, Mvp's en andere IT-professionals.

Deze veelgestelde vragen zijn onderverdeeld in de volgende secties:

* [Vragen over registratie van wacht woord opnieuw instellen](#password-reset-registration)
* [Vragen over wachtwoord herstel](#password-reset)
* [Vragen over wachtwoord wijziging](#password-change)
* [Vragen over rapporten voor wachtwoord beheer](#password-management-reports)
* [Vragen over wacht woord terugschrijven](#password-writeback)

## <a name="password-reset-registration"></a>Registratie van het opnieuw ingestelde wachtwoord

* **V: kunnen mijn gebruikers hun eigen wachtwoord herstel gegevens registreren?**

  > **A:** Klikt. Zolang het wacht woord opnieuw moet worden ingesteld en de licenties zijn ingeschakeld, kunnen gebruikers naar de registratie portal voor het opnieuw instellen van het wacht woord gaan ( https://aka.ms/ssprsetup) om hun verificatie gegevens te registreren. Gebruikers kunnen zich ook registreren via het toegangs venster ( https://myapps.microsoft.com) . Als u zich wilt registreren via het toegangs venster, moeten ze hun profiel afbeelding selecteren, **profiel** selecteren en vervolgens de optie **registreren voor wacht woord opnieuw instellen** selecteren.
  >
  > Als u [gecombineerde registratie](concept-registration-mfa-sspr-combined.md)inschakelt, kunnen gebruikers zich op hetzelfde moment registreren voor zowel SSPR als Azure AD multi-factor Authentication.
* **V: als ik het opnieuw instellen van het wacht woord inschakel voor een groep en vervolgens besluit dit voor iedereen in te scha kelen, moeten mijn gebruikers zich opnieuw registreren?**

  > **A:** Geen. Gebruikers met ingevulde verificatie gegevens hoeven zich niet opnieuw te registreren.
  >
  >
* **V: kan ik gegevens voor het opnieuw instellen van een wacht woord definiëren namens mijn gebruikers?**

  > **A:** Ja, u kunt dit doen met Azure AD Connect, Power shell, de [Azure Portal](https://portal.azure.com)of het [Microsoft 365 beheer centrum](https://admin.microsoft.com). Zie [gegevens die worden gebruikt door Azure AD self-service voor wachtwoord herstel](howto-sspr-authenticationdata.md)voor meer informatie.
  >
  >
* **V: kan ik gegevens voor beveiligings vragen van on-premises synchroniseren?**

  > **A:** Nee, dit is vandaag niet mogelijk.
  >
  >
* **V: kunnen mijn gebruikers gegevens op een zodanige manier registreren dat andere gebruikers deze gegevens niet kunnen zien?**

  > **A:** Klikt. Wanneer gebruikers gegevens registreren met behulp van de registratie portal voor het opnieuw instellen van het wacht woord, worden de gegevens opgeslagen in particuliere verificatie velden die alleen zichtbaar zijn voor globale beheerders en de gebruiker.
  >
  >
* **V: moeten mijn gebruikers worden geregistreerd voordat ze het opnieuw instellen van het wacht woord kunnen gebruiken?**

  > **A:** Geen. Als u in hun naam voldoende verificatie gegevens definieert, hoeven gebruikers zich niet te registreren. Het opnieuw instellen van wacht woorden werkt zo lang dat de gegevens die zijn opgeslagen in de juiste velden in de map juist zijn ingedeeld.
  >
  >
* **V: kan ik het e-mail adres voor verificatie of telefoon, verificatie of alternatieve telefoon velden in naam van mijn gebruikers synchroniseren of instellen?**

  > **A:** De velden die door een globale beheerder kunnen worden ingesteld, worden gedefinieerd in het artikel [SSPR gegevens vereisten](howto-sspr-authenticationdata.md).
  >
  >
* **V: hoe bepaalt de registratie portal welke opties mijn gebruikers weer geven?**

  > **A:** In de portal voor registratie van wacht woord opnieuw instellen worden alleen de opties weer gegeven die u voor uw gebruikers hebt ingeschakeld. Deze opties vindt u in het gedeelte beleid voor het **opnieuw instellen van gebruikers wachtwoorden** van het tabblad **configureren** van uw Directory. Als u beveiligings vragen bijvoorbeeld niet inschakelt, kunnen gebruikers zich niet voor die optie registreren.
  >
  >
* **V: wanneer wordt een geregistreerde gebruiker geregistreerd?**

  > **A:** Een gebruiker wordt beschouwd als geregistreerd voor SSPR wanneer ze ten minste het aantal methoden hebben geregistreerd dat **nodig is voor het opnieuw instellen** van een wacht woord dat u hebt ingesteld in de [Azure Portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Wachtwoord opnieuw instellen

* **V: wilt u voor komen dat gebruikers een wacht woord in korte tijd opnieuw proberen in te stellen?**

  > **A:** Ja, er zijn beveiligings functies ingebouwd in het opnieuw instellen van het wacht woord om deze te beschermen tegen misbruik. 
  >
  > Gebruikers kunnen binnen een periode van 24 uur slechts vijf keer opnieuw proberen om het wacht woord opnieuw in te stellen voordat ze 24 uur worden vergrendeld. 
  >
  > Gebruikers kunnen proberen om een telefoon nummer te valideren, een SMS te verzenden of beveiligings vragen te valideren en alleen vijf keer binnen een uur te antwoorden voordat ze gedurende 24 uur worden vergrendeld. 
  >
  > Gebruikers kunnen Maxi maal 10 keer binnen een periode van tien minuten een e-mail bericht verzenden voordat ze 24 uur worden vergrendeld.
  >
  > De tellers worden opnieuw ingesteld zodra een gebruiker het wacht woord opnieuw instelt.
  >
  >
* **V: hoe lang moet ik wachten op het ontvangen van een e-mail, SMS of telefoon gesprek vanaf het opnieuw instellen van het wacht woord?**

  > **A:** E-mails, SMS-berichten en telefoon gesprekken moeten binnen een minuut binnenkomen. Het normale geval is 5 tot 20 seconden.
  > Als u de melding in dit tijds bestek niet ontvangt:
  > * Controleer de map Ongewenste e-mail.
  > * Controleer of het nummer of het e-mail adres waarmee u bent gecontactt, het account is dat u verwacht.
  > * Controleer of de verificatie gegevens in de Directory correct zijn ingedeeld, bijvoorbeeld + 1 4255551234 of *gebruiker \@ contoso.com*. 
* **V: welke talen worden ondersteund door het wacht woord opnieuw instellen?**

  > **A:** De gebruikers interface voor het opnieuw instellen van wacht woorden, SMS-berichten en telefoon gesprekken worden gelokaliseerd in dezelfde talen die worden ondersteund in Microsoft 365.
  >
  >
* **V: welke onderdelen van de ervaring voor het opnieuw instellen van wacht woorden worden in huis gezet wanneer ik de huismerk items van de organisatie Stel in het tabblad configureren van mijn directory heb ingesteld?**

  > **A:** De portal voor het opnieuw instellen van wacht woorden toont het logo van uw organisatie en stelt u in staat om de koppeling ' Neem contact op met uw beheerder ' om naar een aangepast e-mail adres of URL te verwijzen. E-mail berichten die worden verzonden met wacht woord opnieuw instellen, bevatten het logo, de kleuren en de naam van uw organisatie in de hoofd tekst van het e-mail bericht en worden aangepast op basis van de instellingen voor die specifieke naam.
  >
  >
* **V: hoe kan ik mijn gebruikers laten weten waar ze hun wacht woorden opnieuw kunnen instellen?**

  > **A:** Probeer enkele suggesties in ons SSPR- [implementatie](howto-sspr-deployment.md#plan-communications) artikel.
  >
  >
* **V: kan ik deze pagina vanaf een mobiel apparaat gebruiken?**

  > **A:** Ja, deze pagina werkt op mobiele apparaten.
  >
  >
* **V: hebt u ondersteuning voor het ontgrendelen van lokale Active Directory-accounts wanneer gebruikers hun wacht woord opnieuw instellen?**

  > **A:** Klikt. Wanneer een gebruiker het wacht woord opnieuw instelt en het terugschrijven van wacht woorden via Azure AD Connect is geïmplementeerd, wordt het account van die gebruiker automatisch ontgrendeld wanneer ze hun wacht woord opnieuw instellen.
  >
  >
* **V: hoe kan ik het opnieuw instellen van het wacht woord rechtstreeks integreren in de aanmeld procedure van mijn gebruiker?**

  > **A:** Als u een Azure AD Premium klant bent, kunt u Microsoft Identity Manager zonder extra kosten installeren en de on-premises oplossing voor het opnieuw instellen van wacht woorden implementeren.
  >
  >
* **V: kan ik verschillende beveiligings vragen instellen voor verschillende land instellingen?**

  > **A:** Nee, dit is vandaag niet mogelijk.
  >
  >
* **V: hoeveel vragen kan ik configureren voor de verificatie optie beveiligings vragen?**

  > **A:** U kunt Maxi maal 20 aangepaste beveiligings vragen configureren in de [Azure Portal](https://portal.azure.com).
  >
  >
* **V: hoe lang kunnen beveiligings vragen?**

  > **A:** Beveiligings vragen kunnen 3 tot 200 tekens lang zijn.
  >
  >
* **V: hoe lang kunnen de antwoorden op beveiligings vragen worden beantwoord?**

  > **A:** Antwoorden kunnen 3 tot 40 tekens lang zijn.
  >
  >
* **V: worden dubbele antwoorden op beveiligings vragen afgewezen?**

  > **A:** Ja, we wijzen dubbele antwoorden op beveiligings vragen af.
  >
  >
* **V: kan een gebruiker dezelfde beveiligings vraag meerdere keren registreren?**

  > **A:** Geen. Nadat een gebruiker een bepaalde vraag heeft geregistreerd, kan deze een tweede keer niet meer worden geregistreerd.
  >
  >
* **V: is het mogelijk een minimale limiet van beveiligings vragen voor registratie en opnieuw instellen in te stellen?**

  > **A:** Ja, er kan één limiet worden ingesteld voor registratie en een andere voor opnieuw instellen. Er zijn drie tot vijf beveiligings vragen vereist voor registratie en er kunnen drie tot vijf vragen worden gesteld om opnieuw in te stellen.
  >
  >
* **V: Ik heb mijn beleid geconfigureerd zodat gebruikers beveiligings vragen moeten gebruiken om opnieuw in te stellen, maar de Azure-beheerders lijken anders te worden geconfigureerd.**

  > **A:** Dit is het verwachte gedrag. Microsoft dwingt voor elke Azure-beheerdersrol standaard een sterk beleid met twee-staps-verificatie af voor het opnieuw instellen van wachtwoorden. Zo voor komt u dat beheerders beveiligings vragen kunnen gebruiken. Meer informatie over dit beleid vindt u in de [beleids regels voor wacht woorden en beperkingen in azure Active Directory](concept-sspr-policy.md) -artikel.
  >
  >
* **V: als een gebruiker meer dan het maximum aantal vragen heeft geregistreerd dat moet worden ingesteld, hoe worden de beveiligings vragen geselecteerd tijdens het opnieuw instellen?**

  > **A:** *n* aantal beveiligings vragen worden geselecteerd op wille keurig van het totale aantal vragen dat een gebruiker heeft geregistreerd voor, waarbij *N* de hoeveelheid is die is ingesteld voor het **aantal vragen dat is vereist om de optie opnieuw in te stellen** . Als een gebruiker bijvoorbeeld vijf beveiligings vragen heeft geregistreerd, maar er slechts drie nood zakelijk zijn om een wacht woord opnieuw in te stellen, zijn drie van de vijf vragen wille keurig geselecteerd en worden deze weer gegeven bij het opnieuw instellen. Als de gebruiker de antwoorden op de vragen heeft gereageerd, wordt het selectie proces opnieuw gestart om te voor komen dat de vraag wordt gehamerd.
  >
  >
* **V: hoe lang zijn het e-mail adres en de eenmalige wachtwoord code van SMS geldig?**

  > **A:** De duur van de sessie voor het opnieuw instellen van wacht woorden is 15 minuten. Vanaf het begin van de bewerking voor het opnieuw instellen van het wacht woord heeft de gebruiker 15 minuten om hun wacht woord opnieuw in te stellen. De eenmalige wachtwoord code voor e-mail en SMS is 5 minuten geldig tijdens de sessie voor het opnieuw instellen van het wacht woord.
  >
  >
* **V: kan ik voor komen dat gebruikers hun wacht woord opnieuw instellen?**

  > **A:** Ja, als u een groep gebruikt om SSPR in te scha kelen, kunt u een afzonderlijke gebruiker uit de groep verwijderen waarmee gebruikers hun wacht woord opnieuw kunnen instellen. Als de gebruiker een globale beheerder is, behouden ze de mogelijkheid om hun wacht woord opnieuw in te stellen. Dit kan niet worden uitgeschakeld.
  >
  >

## <a name="password-change"></a>Wachtwoord wijzigen

* **V: waar moeten mijn gebruikers hun wacht woorden wijzigen?**

  > **A:** Gebruikers kunnen hun wacht woord wijzigen op een plek waar ze hun profiel afbeelding of-pictogram zien, zoals in de rechter bovenhoek van hun [Office 365](https://portal.office.com) -portal of-ervaring in het [toegangs venster](https://myapps.microsoft.com) . Gebruikers kunnen hun wacht woord wijzigen via de [profiel pagina van het toegangs paneel](https://account.activedirectory.windowsazure.com/r#/profile). Gebruikers kunnen ook worden gevraagd hun wacht woord automatisch te wijzigen op de aanmeldings pagina van Azure AD als hun wacht woord is verlopen. Ten slotte kunnen gebruikers rechtstreeks naar de [Azure AD-portal voor wachtwoord wijzigingen](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) bladeren als ze hun wacht woord willen wijzigen.
  >
  >
* **V: kunnen mijn gebruikers op de hoogte worden gesteld in de Office-Portal wanneer hun on-premises wacht woord verloopt?**

  > **A:** Ja, dit is mogelijk op dit moment als u Active Directory Federation Services (AD FS) gebruikt. Als u AD FS gebruikt, volgt u de instructies in de [claim wachtwoord beleid verzenden met AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artikel. Als u synchronisatie met wacht woord-hash gebruikt, is dit niet mogelijk vandaag. Wachtwoord beleid van on-premises directory's wordt niet gesynchroniseerd, dus het is niet mogelijk om verlopen meldingen te verzenden naar Cloud ervaringen. In beide gevallen is het ook mogelijk om [gebruikers te informeren waarvan de wacht woorden bijna verlopen via Power shell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **V: kan ik voor komen dat gebruikers hun wacht woord wijzigen?**

  > **A:** Voor Cloud gebruikers kunnen wachtwoord wijzigingen niet worden geblokkeerd. Voor on-premises gebruikers kunt u instellen dat de **gebruiker de wachtwoord optie niet kan wijzigen** in geselecteerd. De geselecteerde gebruikers kunnen hun wacht woord niet wijzigen.
  >
  >

## <a name="password-management-reports"></a>Rapporten voor wachtwoord beheer

* **V: hoe lang duurt het voordat gegevens worden weer gegeven in de rapporten voor wachtwoord beheer?**

  > **A:** Gegevens moeten binnen 5 tot 10 minuten in de rapporten voor wachtwoord beheer worden weer gegeven. In sommige gevallen kan het een uur duren voordat het wordt weer gegeven.
  >
  >
* **V: hoe kan ik de rapporten voor wachtwoord beheer filteren?**

  > **A:** Als u de rapporten voor wachtwoord beheer wilt filteren, selecteert u het kleine vergroot glas tot het uiterst rechts van de kolomlabels, in de buurt van de bovenkant van het rapport. Als u uitgebreide filters wilt Toep assen, kunt u het rapport downloaden naar Excel en een draai tabel maken.
  >
  >
* **V: wat is het maximum aantal gebeurtenissen dat is opgeslagen in de rapporten voor wachtwoord beheer?**

  > **A:** Er worden Maxi maal 75.000 wacht woord-en registratie gebeurtenissen voor het opnieuw instellen van wacht woorden opgeslagen in de rapporten voor wachtwoord beheer, met een spanning van Maxi maal 30 dagen. We werken eraan om dit nummer uit te breiden om meer gebeurtenissen op te nemen.
  >
  >
* **V: hoe ver terug de rapporten voor wachtwoord beheer gaan?**

  > **A:** In de rapporten voor wachtwoord beheer worden bewerkingen weer gegeven die in de afgelopen 30 dagen zijn opgetreden. Als u deze gegevens nu wilt archiveren, kunt u de rapporten periodiek downloaden en op een andere locatie opslaan.
  >
  >
* **V: is er een maximum aantal rijen dat kan worden weer gegeven in de rapporten voor wachtwoord beheer?**

  > **A:** Klikt. Er kunnen Maxi maal 75.000 rijen worden weer gegeven op een van de wachtwoord beheer rapporten, ongeacht of ze worden weer gegeven in de gebruikers interface of worden gedownload.
  >
  >
* **V: is er een API voor toegang tot de gegevens voor het opnieuw instellen van het wacht woord of registratie rapport?**

  > **A:** Klikt. Zie voor meer informatie over hoe u de rapportage gegevens voor het opnieuw instellen van het wacht woord kunt gebruiken de [referentie voor Azure Log Analytics rest API](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>Wachtwoord terugschrijven

* **V: Hoe werkt het terugschrijven van wacht woorden achter de schermen?**

  > **A:** Zie het artikel [over het terugschrijven van wacht woorden](./tutorial-enable-sspr-writeback.md) voor een uitleg van wat er gebeurt wanneer u wacht woord terugschrijven inschakelt en hoe gegevens via het systeem weer naar uw on-premises omgeving worden gepasseerd.
  >
  >
* **V: hoe lang duurt het terugschrijven van wacht woorden? Is er sprake van een synchronisatie vertraging, zoals bij een wachtwoord-hash-synchronisatie?**

  > **A:** Het terugschrijven van wacht woorden is direct. Het is een synchrone pijp lijn die fundamenteel anders werkt dan synchronisatie met een wacht woord-hash. Met wacht woord terugschrijven kunnen gebruikers realtime feedback ontvangen over het succes van het opnieuw instellen van het wacht woord of de wijzigings bewerking. De gemiddelde tijd voor een geslaagde terugschrijf actie van een wacht woord is minder dan 500 MS.
  >
  >
* **V: als mijn on-premises account is uitgeschakeld, hoe worden mijn Cloud account en toegang beïnvloed?**

  > **A:** Als uw on-premises ID is uitgeschakeld, worden uw Cloud-ID en-toegang ook bij het volgende synchronisatie-interval via Azure AD Connect uitgeschakeld. Deze synchronisatie is standaard elke 30 minuten.
  >
  >
* **V: als mijn on-premises account wordt beperkt door een on-premises Active Directory wachtwoord beleid, SSPR dit beleid dan wanneer ik mijn wacht woord Wijzig?**

  > **A:** Ja, SSPR is afhankelijk van en houdt zich aan op de on-premises Active Directory wachtwoord beleid. Dit beleid bevat de gebruikelijke Active Directory wachtwoord beleid voor het domein, evenals alle gedefinieerde, verfijnde wachtwoord beleidsregels die zijn gericht op een gebruiker.
  >
  >
* **V: voor welke typen accounts is wacht woord terugschrijven werk voor?**

  > **A:** Het terugschrijven van wacht woorden werkt voor gebruikers accounts die zijn gesynchroniseerd van on-premises Active Directory naar Azure AD, waaronder federatieve, wacht woord-hash gesynchroniseerd en gebruikers Pass-Through authenticatie.
  >
  >
* **V: wacht woord terugschrijven dwingt het wachtwoord beleid van mijn domein af?**

  > **A:** Klikt. Wacht woord terugschrijven afdwingt wachtwoord leeftijd, geschiedenis, complexiteit, filters en eventuele andere beperkingen die u mogelijk in wacht woorden in uw lokale domein plaatst.
  >
  >
* **V: is het terugschrijven van wacht woorden veilig?  Hoe weet ik zeker dat ik niet gekraakt kan worden?**

  > **A:** Ja, het terugschrijven van wacht woorden is veilig. Voor meer informatie over de meerdere beveiligings lagen die worden geïmplementeerd door de service voor het terugschrijven van wacht woorden, raadpleegt u de sectie [wacht woord terugschrijven](concept-sspr-writeback.md#password-writeback-security) in het artikel voor het [terugschrijven van wacht woorden](./tutorial-enable-sspr-writeback.md) .
  >
  >

## <a name="next-steps"></a>Volgende stappen

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wacht woord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor het opnieuw instellen van een wachtwoord](../user-help/active-directory-passwords-reset-register.md)
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](./tutorial-enable-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets is verbroken. Hoe kan ik problemen met SSPR oplossen?](./troubleshoot-sspr.md)