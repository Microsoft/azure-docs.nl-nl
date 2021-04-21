---
title: Deep dive over selfservice voor wachtwoord opnieuw instellen - Azure Active Directory
description: Hoe werkt selfservice voor wachtwoord opnieuw instellen?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94077a1c6329aa1fecf9593f2df41fa77afc8a44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765913"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>How it works: Azure AD self-service password reset (Hoe het werkt: selfservice voor wachtwoordherstel in Azure AD)

Self-service voor wachtwoordherstel (SSPR) voor Azure Active Directory (Azure AD) biedt gebruikers de mogelijkheid hun wachtwoord te wijzigen of opnieuw in te stellen zonder tussenkomst van een beheerder of helpdesk. Als het account van een gebruiker is vergrendeld of als deze zijn of haar wachtwoord is vergeten, kan de gebruiker de vergrendeling aan de hand van instructies zelf ongedaan maken en weer aan het werk gaan. Op deze manier wordt het aantal telefoontjes naar de helpdesk en productieverlies verminderd wanneer een gebruiker zich niet kan aanmelden bij een apparaat of toepassing.

> [!IMPORTANT]
> In dit conceptuele artikel wordt aan een beheerder uitgelegd hoe selfservice voor wachtwoord opnieuw instellen werkt. Als u een eindgebruiker bent die al is geregistreerd voor selfservice voor wachtwoord opnieuw instellen en u weer toegang moet krijgen tot uw account, gaat u naar [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

## <a name="how-does-the-password-reset-process-work"></a>Hoe werkt het proces voor het opnieuw instellen van het wachtwoord?

Een gebruiker kan het wachtwoord opnieuw instellen of wijzigen met behulp van [de SSPR-portal.](https://aka.ms/sspr) Ze moeten eerst hun gewenste verificatiemethoden hebben geregistreerd. Wanneer een gebruiker de SSPR-portal gebruikt, worden de volgende factoren door het Azure-platform in overweging neem:

* Hoe moet de pagina worden gelokaliseerd?
* Is het gebruikersaccount geldig?
* Tot welke organisatie behoort de gebruiker?
* Waar wordt het wachtwoord van de gebruiker beheerd?
* Heeft de gebruiker een licentie om de functie te gebruiken?

Wanneer een gebruiker de koppeling Kan geen **toegang krijgen** tot uw account selecteert vanuit een toepassing of pagina, of rechtstreeks naar gaat, is de taal die wordt gebruikt in de SSPR-portal gebaseerd op de [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) volgende opties:

* Standaard wordt de taal van de browser gebruikt om de SSPR in de juiste taal weer te geven. De ervaring voor wachtwoord opnieuw instellen is gelokaliseerd in dezelfde talen die [Microsoft 365 ondersteunt.](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)
* Als u een koppeling wilt maken naar de SSPR in een specifieke gelokaliseerde taal, moet u toevoegen aan het einde van de URL voor het opnieuw instellen van het wachtwoord, samen met de `?mkt=` vereiste taal.
    * Als u bijvoorbeeld de Spaans *es-us-taal* wilt opgeven, gebruikt u `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Nadat de SSPR-portal wordt weergegeven in de vereiste taal, wordt de gebruiker gevraagd een gebruikers-id in te voeren en een captcha door te geven. Azure AD controleert nu of de gebruiker SSPR kan gebruiken door de volgende controles uit te voeren:

* Controleert of SSPR is ingeschakeld voor de gebruiker en of er een Azure AD-licentie is toegewezen.
  * Als de gebruiker niet is ingeschakeld voor SSPR of als er geen licentie is toegewezen, wordt de gebruiker gevraagd contact op te nemen met de beheerder om het wachtwoord opnieuw in te stellen.
* Controleert of de gebruiker de juiste verificatiemethoden voor zijn account heeft gedefinieerd in overeenstemming met het beheerdersbeleid.
  * Als het beleid slechts één methode vereist, controleert u of de gebruiker de juiste gegevens heeft gedefinieerd voor ten minste één van de verificatiemethoden die door het beheerdersbeleid zijn ingeschakeld.
    * Als de verificatiemethoden niet zijn geconfigureerd, wordt de gebruiker aangeraden contact op te nemen met de beheerder om het wachtwoord opnieuw in te stellen.
  * Als het beleid twee methoden vereist, controleert u of de gebruiker de juiste gegevens heeft gedefinieerd voor ten minste twee van de verificatiemethoden die door het beheerdersbeleid zijn ingeschakeld.
    * Als de verificatiemethoden niet zijn geconfigureerd, wordt de gebruiker aangeraden contact op te nemen met de beheerder om het wachtwoord opnieuw in te stellen.
  * Als een Azure-beheerdersrol wordt toegewezen aan de gebruiker, wordt het sterke wachtwoordbeleid met twee gate afgedwongen. Zie [Verschillen in beleid voor het opnieuw instellen van beheerders](concept-sspr-policy.md#administrator-reset-policy-differences) voor meer informatie.
* Controleert of het wachtwoord van de gebruiker on-premises wordt beheerd, bijvoorbeeld of de Azure AD-tenant federatief, pass-through-verificatie of wachtwoordhashsynchronisatie gebruikt:
  * Als SSPR-terugschrijven is geconfigureerd en het wachtwoord van de gebruiker on-premises wordt beheerd, kan de gebruiker doorgaan met verifiëren en het wachtwoord opnieuw instellen.
  * Als SSPR-terugschrijven niet is geïmplementeerd en het wachtwoord van de gebruiker on-premises wordt beheerd, wordt de gebruiker gevraagd contact op te nemen met de beheerder om het wachtwoord opnieuw in te stellen.

Als alle eerdere controles zijn voltooid, wordt de gebruiker door het proces geleid om het wachtwoord opnieuw in te stellen of te wijzigen.

> [!NOTE]
> SSPR kan e-mailmeldingen verzenden naar gebruikers als onderdeel van het proces voor het opnieuw instellen van het wachtwoord. Deze e-mailberichten worden verzonden met behulp van de SMTP Relay-service, die in een actief/actief-modus in verschillende regio's werkt.
>
> SMTP-relayservices ontvangen en verwerken de e-mail, maar slaan deze niet op. De body van de SSPR-e-mail die mogelijk door de klant verstrekte gegevens kan bevatten, wordt niet opgeslagen in de smtp-relayservicelogboeken. De logboeken bevatten alleen protocolmetagegevens.

Voltooi de volgende zelfstudie om aan de slag te gaan met SSPR:

> [!div class="nextstepaction"]
> [Zelfstudie: Selfservice voor wachtwoord opnieuw instellen (SSPR) inschakelen](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Vereisen dat gebruikers zich registreren wanneer ze zich aanmelden

U kunt de optie inschakelen om te vereisen dat een gebruiker de SSPR-registratie voltooit als deze moderne verificatie of webbrowser gebruikt om zich met Azure AD aan te melden bij toepassingen. Deze werkstroom bevat de volgende toepassingen:

* Microsoft 365
* Azure Portal
* Toegangsvenster
* Federatief toepassingen
* Aangepaste toepassingen met Azure AD

Wanneer u geen registratie vereist, wordt gebruikers niet gevraagd om zich aan te melden, maar kunnen ze zich wel handmatig registreren. Gebruikers kunnen de koppeling Registreren voor wachtwoord opnieuw instellen op het tabblad Profiel in de [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) Toegangsvenster.  

![Registratieopties voor SSPR in de Azure Portal][Registration]

> [!NOTE]
> Gebruikers kunnen de SSPR-registratieportal sluiten door Annuleren **te** selecteren of door het venster te sluiten. Ze worden echter telkens wanneer ze zich aanmelden gevraagd om zich te registreren totdat ze hun registratie hebben voltooid.
>
> Deze interrupt om te registreren voor SSPR verbreekt de verbinding van de gebruiker niet als deze al is aangemeld.

## <a name="reconfirm-authentication-information"></a>Verificatiegegevens opnieuw bevestigen

Om ervoor te zorgen dat verificatiemethoden juist zijn wanneer ze hun wachtwoord opnieuw moeten instellen of wijzigen, kunt u vereisen dat gebruikers hun geregistreerde gegevens na een bepaalde periode bevestigen. Deze optie is alleen beschikbaar als u de optie **Vereisen dat gebruikers zich registreren bij aanmelding inschakelen.**

Geldige waarden om een gebruiker te vragen om de geregistreerde methoden te bevestigen, *liggen tussen 0* en *730* dagen. Als u deze waarde *instelt op 0,* wordt gebruikers nooit gevraagd om hun verificatiegegevens te bevestigen. Wanneer u de gecombineerde registratie-ervaring gebruikt, moeten gebruikers hun identiteit bevestigen voordat ze hun gegevens opnieuw bevestigen.

## <a name="authentication-methods"></a>Verificatiemethoden

Wanneer een gebruiker is ingeschakeld voor SSPR, moet deze ten minste één verificatiemethode registreren. We raden u ten zeerste aan twee of meer verificatiemethoden te kiezen, zodat uw gebruikers meer flexibiliteit hebben in het geval ze geen toegang hebben tot één methode wanneer ze deze nodig hebben. Zie Wat zijn [verificatiemethoden? voor meer informatie.](concept-authentication-methods.md)

De volgende verificatiemethoden zijn beschikbaar voor SSPR:

* Meldingen via mobiele app
* Code van mobiele app
* E-mail
* Mobiele telefoon
* Zakelijke telefoon
* Beveiligingsvragen

Gebruikers kunnen hun wachtwoord alleen opnieuw instellen als ze een verificatiemethode hebben geregistreerd die de beheerder heeft ingeschakeld.

> [!WARNING]
> Accounts aan toegewezen *Azure-beheerdersrollen* zijn vereist voor het gebruik van methoden zoals gedefinieerd in de sectie Verschillen in [beleid voor het opnieuw instellen van beheerders.](concept-sspr-policy.md#administrator-reset-policy-differences)

![Selectie van verificatiemethoden in de Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Het vereiste aantal verificatiemethoden

U kunt het aantal beschikbare verificatiemethoden configureren dat een gebruiker moet opgeven om het wachtwoord opnieuw in te stellen of te ontgrendelen. Deze waarde kan worden ingesteld op *een* of *twee*.

Gebruikers kunnen en moeten meerdere verificatiemethoden registreren. Nogmaals, het wordt ten zeerste aanbevolen dat gebruikers twee of meer verificatiemethoden registreren, zodat ze meer flexibiliteit hebben in het geval ze geen toegang hebben tot één methode wanneer ze deze nodig hebben.

Als een gebruiker niet het minimale aantal vereiste methoden heeft geregistreerd wanneer ze SSPR proberen te gebruiken, zien ze een foutpagina die hen vraagt om een beheerder te vragen hun wachtwoord opnieuw in te stellen. Zorg dat u het aantal vereiste methoden verhoogt van één naar twee als u bestaande gebruikers hebt geregistreerd voor SSPR en ze de functie vervolgens niet kunnen gebruiken. Zie de volgende sectie voor het wijzigen van [verificatiemethoden voor meer informatie.](#change-authentication-methods)

#### <a name="mobile-app-and-sspr"></a>Mobiele app en SSPR

Wanneer u een mobiele app gebruikt als methode voor wachtwoord opnieuw instellen, zoals de Microsoft Authenticator app, zijn de volgende overwegingen van toepassing:

* Wanneer beheerders één methode vereisen om een wachtwoord opnieuw in te stellen, is verificatiecode de enige beschikbare optie.
* Wanneer beheerders twee methoden nodig hebben om een wachtwoord opnieuw  in te stellen, kunnen gebruikers naast andere ingeschakelde methoden ook meldings- of verificatiecode gebruiken.

| Het aantal methoden dat is vereist om het opnieuw in te stellen | Eén | Twee |
| :---: | :---: | :---: |
| Functies voor mobiele apps beschikbaar | Code | Code of melding |

Gebruikers kunnen hun mobiele app niet registreren wanneer ze zich registreren voor selfservice voor wachtwoord opnieuw instellen vanuit [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Gebruikers kunnen hun mobiele app registreren op of in de gecombineerde registratie [https://aka.ms/mfasetup](https://aka.ms/mfasetup) van beveiligingsgegevens op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> De Authenticator-app kan niet worden geselecteerd als de enige verificatiemethode wanneer slechts één methode is vereist. Op dezelfde manier kunnen de Authenticator-app en slechts één extra methode niet worden geselecteerd wanneer er twee methoden nodig zijn.
>
> Bij het configureren van SSPR-beleid dat de Authenticator-app als methode bevat, moet ten minste één extra methode worden geselecteerd wanneer er één methode is vereist. Er moeten ten minste twee extra methoden worden geselecteerd wanneer twee methoden moeten worden geconfigureerd.
>
> Deze vereiste is omdat de huidige SSPR-registratie-ervaring niet de optie bevat om de authenticator-app te registreren. De optie voor het registreren van de Authenticator-app is opgenomen in de nieuwe [gecombineerde registratie-ervaring](./concept-registration-mfa-sspr-combined.md).
>
> Het toestaan van beleid dat alleen gebruik maakt van de Authenticator-app (wanneer één methode vereist is), of de Authenticator-app en slechts één extra methode (wanneer twee methoden vereist zijn), kan ertoe leiden dat gebruikers worden geblokkeerd voor registratie voor SSPR totdat ze zijn geconfigureerd voor het gebruik van de nieuwe gecombineerde registratie-ervaring.

### <a name="change-authentication-methods"></a>Verificatiemethoden wijzigen

Wat gebeurt er als u begint met een beleid met slechts één vereiste verificatiemethode voor het opnieuw instellen of ontgrendelen van geregistreerde gegevens en u dit wijzigt in twee methoden?

| Aantal geregistreerde methoden | Het vereiste aantal methoden | Resultaat |
| :---: | :---: | :---: |
| 1 of meer | 1 | **Kan** opnieuw instellen of ontgrendelen |
| 1 | 2 | **Kan** niet opnieuw instellen of ontgrendelen |
| 2 of meer | 2 | **Kan** opnieuw instellen of ontgrendelen |

Het wijzigen van de beschikbare verificatiemethoden kan ook problemen voor gebruikers veroorzaken. Als u de typen verificatiemethoden wijzigt die een gebruiker kan gebruiken, kunt u per ongeluk voorkomen dat gebruikers SSPR kunnen gebruiken als ze niet de minimale hoeveelheid beschikbare gegevens hebben.

Kijk eens naar het volgende voorbeeldscenario:

1. Het oorspronkelijke beleid is geconfigureerd met twee vereiste verificatiemethoden. Er wordt alleen gebruikgemaakt van het telefoonnummer van het kantoor en de beveiligingsvragen.
1. De beheerder wijzigt het beleid zodat de beveiligingsvragen niet meer worden gebruikt, maar staat het gebruik van een mobiele telefoon en een alternatief e-mailadres toe.
1. Gebruikers zonder de velden mobiele telefoon of alternatieve e-mail die nu zijn ingevuld, kunnen hun wachtwoord niet opnieuw instellen.

## <a name="notifications"></a>Meldingen

Als u de kennis van wachtwoordgebeurtenissen wilt verbeteren, kunt u met SSPR meldingen configureren voor zowel de gebruikers als identiteitsbeheerders.

### <a name="notify-users-on-password-resets"></a>Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord

Als deze optie is ingesteld op **Ja,** ontvangen gebruikers die hun wachtwoord opnieuw instellen een e-mail met de melding dat hun wachtwoord is gewijzigd. Het e-mailbericht wordt via de SSPR-portal verzonden naar hun primaire en alternatieve e-mailadressen die zijn opgeslagen in Azure AD. Niemand anders wordt op de hoogte gesteld van de gebeurtenis voor opnieuw instellen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoorden opnieuw instellen

Als deze optie is ingesteld op **Ja,** ontvangen alle andere Azure-beheerders een e-mail naar hun primaire e-mailadres dat is opgeslagen in Azure AD. De e-mail meldt dat een andere beheerder het wachtwoord heeft gewijzigd met behulp van SSPR.

Kijk eens naar het volgende voorbeeldscenario:

* Er zijn vier beheerders in een omgeving.
* Beheerder *A* stelt het wachtwoord opnieuw in met behulp van SSPR.
* Beheerders *B,* *C en* *D* ontvangen een e-mail waarin ze worden gewaarschuwd voor het opnieuw instellen van het wachtwoord.

## <a name="on-premises-integration"></a>On-premises integratie

Als u een hybride omgeving hebt, kunt u deze configureren Azure AD Connect gebeurtenissen voor wachtwoordwijziging terug te schrijven van Azure AD naar een on-premises directory.

![Wachtwoord terugschrijven valideren is ingeschakeld en werkt][Writeback]

Azure AD controleert uw huidige hybride connectiviteit en biedt een van de volgende berichten in de Azure Portal:

* Uw on-premises write-back-client is actief.
* Azure AD is online en is verbonden met uw on-premises terugschrijvenclient. Het lijkt er echter op dat de geïnstalleerde versie van Azure AD Connect verouderd is. Overweeg [upgraden Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) om ervoor te zorgen dat u beschikt over de nieuwste connectiviteitsfuncties en belangrijke oplossingen voor problemen.
* Helaas kunnen we de status van uw on-premises write-back-client niet controleren omdat de geïnstalleerde versie van Azure AD Connect verouderd is. [Werk Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) om uw verbindingsstatus te controleren.
* Helaas kunnen we op dit moment geen verbinding maken met uw on-premises terugschrijvenclient. [Problemen Azure AD Connect](./troubleshoot-sspr-writeback.md) om de verbinding te herstellen.
* Helaas kunnen we geen verbinding maken met uw on-premises terugschrijvenclient omdat het terugschrijven van wachtwoorden niet juist is geconfigureerd. [Wachtwoord terugschrijven configureren om](./tutorial-enable-sspr-writeback.md) de verbinding te herstellen.
* Helaas kunnen we op dit moment geen verbinding maken met uw on-premises terugschrijvenclient. Dit kan worden veroorzaakt door tijdelijke problemen aan onze kant. Als het probleem zich blijft [voordoen,](./troubleshoot-sspr-writeback.md) kunt Azure AD Connect verbinding herstellen.

Voltooi de volgende zelfstudie om aan de slag te gaan met het terugschrijven van SSPR:

> [!div class="nextstepaction"]
> [Zelfstudie: Selfservice voor wachtwoord opnieuw instellen (SSPR) terugschrijven inschakelen](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Wachtwoorden terugschrijven naar uw on-premises directory

U kunt wachtwoord terugschrijven inschakelen met behulp van Azure Portal. U kunt wachtwoord terugschrijven ook tijdelijk uitschakelen zonder dat u deze opnieuw moet Azure AD Connect.

* Als de optie is ingesteld op **Ja,** wordt write-back ingeschakeld. Met federatief, pass-through-verificatie of met wachtwoordhash gesynchroniseerde gebruikers kunnen hun wachtwoord opnieuw instellen.
* Als de optie is ingesteld op **Nee,** wordt write-back uitgeschakeld. Met federatief, pass-through-verificatie of met wachtwoordhash gesynchroniseerde gebruikers kunnen hun wachtwoord niet opnieuw instellen.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Gebruikers toestaan accounts te ontgrendelen zonder hun wachtwoord opnieuw in te stellen

Azure AD ontgrendelt standaard accounts wanneer het wachtwoord opnieuw wordt ingesteld. Om flexibiliteit te bieden, kunt u ervoor kiezen om gebruikers toe te staan hun on-premises accounts te ontgrendelen zonder hun wachtwoord opnieuw in te stellen. Gebruik deze instelling om deze twee bewerkingen van elkaar te scheiden.

* Als deze optie is ingesteld op **Ja,** krijgen gebruikers de mogelijkheid om hun wachtwoord opnieuw in te stellen en het account te ontgrendelen, of om hun account te ontgrendelen zonder dat ze het wachtwoord opnieuw moeten instellen.
* Als deze is **ingesteld op Nee,** kunnen gebruikers alleen een gecombineerde bewerking voor wachtwoord opnieuw instellen en account ontgrendelen uitvoeren.

### <a name="on-premises-active-directory-password-filters"></a>On-premises Active Directory-wachtwoordfilters

SSPR voert het equivalent uit van een door de beheerder geïnitieerd wachtwoord opnieuw instellen in Active Directory. Als u een wachtwoordfilter van derden gebruikt om aangepaste wachtwoordregels af te dwingen en u wilt dat dit wachtwoordfilter wordt gecontroleerd tijdens de selfservice voor wachtwoord opnieuw instellen van Azure AD, moet u ervoor zorgen dat de oplossing voor wachtwoordfilters van derden is geconfigureerd om toe te passen in het scenario voor het opnieuw instellen van het beheerderswachtwoord. [Azure AD-wachtwoordbeveiliging voor Active Directory Domain Services](concept-password-ban-bad-on-premises.md) wordt standaard ondersteund.

## <a name="password-reset-for-b2b-users"></a>Wachtwoord opnieuw instellen voor B2B-gebruikers

Wachtwoord opnieuw instellen en wijzigen worden volledig ondersteund in alle B2B-configuraties (Business-to-Business). B2B-gebruikerswachtwoord opnieuw instellen wordt ondersteund in de volgende drie gevallen:

* Gebruikers van een partnerorganisatie met een bestaande **Azure AD-tenant:** als de organisatie waarmee u werkt een bestaande Azure AD-tenant heeft, respecteren we het beleid voor wachtwoord opnieuw instellen dat is ingeschakeld voor die tenant. Het opnieuw instellen van het wachtwoord werkt alleen als de partnerorganisatie ervoor moet zorgen dat Azure AD SSPR is ingeschakeld. Er worden geen extra kosten in rekening Microsoft 365 klanten.
* **Gebruikers** die zich aanmelden via selfservice-registratie: als de organisatie waarmee u werkt, de selfservicefunctie voor aanmelden heeft gebruikt om toegang te krijgen tot een tenant, laten we hen het wachtwoord opnieuw instellen met het [e-mailadres](../enterprise-users/directory-self-service-signup.md) dat ze hebben geregistreerd.
* **B2B-gebruikers:** alle nieuwe B2B-gebruikers die zijn gemaakt met behulp van de nieuwe mogelijkheden van [Azure AD B2B](../external-identities/what-is-b2b.md) kunnen ook hun wachtwoord opnieuw instellen met het e-mailadres dat ze tijdens het uitnodigingsproces hebben geregistreerd.

Als u dit scenario wilt testen, gaat https://passwordreset.microsoftonline.com u naar met een van deze partnergebruikers. Als er een alternatief e-mailadres of verificatie-e-mailadres is gedefinieerd, werkt wachtwoord opnieuw instellen zoals verwacht.

> [!NOTE]
> Microsoft-accounts aan wie gasttoegang is verleend tot uw Azure AD-tenant, zoals accounts van Hotmail.com, Outlook.com of andere persoonlijke e-mailadressen, kunnen azure AD SSPR niet gebruiken. Ze moeten hun wachtwoord opnieuw instellen met behulp van de informatie in het artikel Wanneer u zich niet kunt aanmelden bij [Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) wachtwoord.

## <a name="next-steps"></a>Volgende stappen

Voltooi de volgende zelfstudie om aan de slag te gaan met SSPR:

> [!div class="nextstepaction"]
> [Zelfstudie: Selfservice voor wachtwoord opnieuw instellen (SSPR) inschakelen](tutorial-enable-sspr.md)

De volgende koppelingen bieden aanvullende informatie over wachtwoordherstel met behulp van Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Azure Active Directory-verificatiemethoden die beschikbaar zijn en hoeveel er vereist zijn"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "SSPR-registratieopties configureren in de Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "On-premises integratie voor SSPR in de Azure Portal"
