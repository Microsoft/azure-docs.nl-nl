---
title: Selfservice voor wachtwoord herstel diep gaande-Azure Active Directory
description: Hoe werkt self-service voor het opnieuw instellen van wacht woorden
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
ms.openlocfilehash: 620b696be0b9a06cf0d68145b06e3cacf2bedff2
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762547"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>How it works: Azure AD self-service password reset (Hoe het werkt: selfservice voor wachtwoordherstel in Azure AD)

Self-service voor wachtwoordherstel (SSPR) voor Azure Active Directory (Azure AD) biedt gebruikers de mogelijkheid hun wachtwoord te wijzigen of opnieuw in te stellen zonder tussenkomst van een beheerder of helpdesk. Als het account van een gebruiker is vergrendeld of als deze zijn of haar wachtwoord is vergeten, kan de gebruiker de vergrendeling aan de hand van instructies zelf ongedaan maken en weer aan het werk gaan. Op deze manier wordt het aantal telefoontjes naar de helpdesk en productieverlies verminderd wanneer een gebruiker zich niet kan aanmelden bij een apparaat of toepassing.

> [!IMPORTANT]
> In dit conceptuele artikel wordt uitgelegd hoe een beheerder de functie voor het opnieuw instellen van wacht woorden kan gebruiken. Als u een eind gebruiker bent al geregistreerd voor selfservice voor het opnieuw instellen van het wacht woord en als u weer toegang wilt krijgen tot uw account, gaat u naar [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

## <a name="how-does-the-password-reset-process-work"></a>Hoe werkt het proces voor het opnieuw instellen van wacht woorden?

Een gebruiker kan het wacht woord opnieuw instellen of wijzigen met behulp van de [SSPR-Portal](https://aka.ms/sspr). Ze moeten eerst hun gewenste verificatie methoden hebben geregistreerd. Wanneer een gebruiker toegang heeft tot de SSPR-Portal, beschouwt het Azure-platform de volgende factoren:

* Hoe moet de pagina worden gelokaliseerd?
* Is het gebruikers account geldig?
* Tot welke organisatie behoort de gebruiker?
* Waar wordt het wacht woord van de gebruiker beheerd?
* Is de gebruiker in licentie gegeven voor het gebruik van de functie?

Wanneer een gebruiker de koppeling **geen toegang tot uw account kan krijgen** vanuit een toepassing of pagina of rechtstreeks naar gaat [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) , is de taal die wordt gebruikt in de SSPR-Portal gebaseerd op de volgende opties:

* De land instelling van de browser wordt standaard gebruikt om de SSPR in de juiste taal weer te geven. De ervaring voor het opnieuw instellen van wacht woorden is gelokaliseerd in dezelfde talen die [Microsoft 365 ondersteunt](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec).
* Als u een koppeling wilt maken naar de SSPR in een specifieke gelokaliseerde taal, voegt `?mkt=` u toe aan het einde van de URL voor het opnieuw instellen van het wacht woord en de vereiste land instelling.
    * Als u bijvoorbeeld de Spaanse *es-Amerikaanse* land instelling wilt opgeven, gebruikt u `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Nadat de SSPR-Portal in de vereiste taal wordt weer gegeven, wordt de gebruiker gevraagd om een gebruikers-ID in te voeren en een CAPTCHA te geven. Azure AD controleert nu of de gebruiker SSPR kan gebruiken door de volgende controles uit te voeren:

* Hiermee wordt gecontroleerd of SSPR is ingeschakeld voor de gebruiker en wordt er een Azure AD-licentie toegewezen.
  * Als de gebruiker niet is ingeschakeld voor SSPR of als er geen licentie is toegewezen, wordt de gebruiker gevraagd contact op te nemen met de beheerder om hun wacht woord opnieuw in te stellen.
* Controleert of de gebruiker beschikt over de juiste verificatie methoden die zijn gedefinieerd op hun account in overeenstemming met het beheerders beleid.
  * Als voor het beleid slechts één methode is vereist, controleert u of de gebruiker de juiste gegevens heeft gedefinieerd voor ten minste een van de verificatie methoden die door het beheerders beleid zijn ingeschakeld.
    * Als de verificatie methoden niet zijn geconfigureerd, wordt de gebruiker geadviseerd contact op te nemen met de beheerder om hun wacht woord opnieuw in te stellen.
  * Als voor het beleid twee methoden zijn vereist, controleert u of de gebruiker de juiste gegevens heeft gedefinieerd voor ten minste twee van de verificatie methoden die door het beheerders beleid zijn ingeschakeld.
    * Als de verificatie methoden niet zijn geconfigureerd, wordt de gebruiker geadviseerd contact op te nemen met de beheerder om hun wacht woord opnieuw in te stellen.
  * Als een rol van Azure-beheerder aan de gebruiker is toegewezen, wordt het sterke wachtwoord beleid voor twee poorten afgedwongen. Zie [Verschillen in beleid voor het opnieuw instellen van beheerders](concept-sspr-policy.md#administrator-reset-policy-differences) voor meer informatie.
* Hiermee wordt gecontroleerd of het wacht woord van de gebruiker on-premises wordt beheerd, bijvoorbeeld als de Azure AD-Tenant gebruikmaakt van federatieve, Pass-Through-verificatie of synchronisatie van wacht woord-hash:
  * Als SSPR write-back is geconfigureerd en het wacht woord van de gebruiker on-premises wordt beheerd, mag de gebruiker door gaan met het verifiëren en opnieuw instellen van hun wacht woord.
  * Als SSPR write-back niet wordt geïmplementeerd en het wacht woord van de gebruiker on-premises wordt beheerd, wordt de gebruiker gevraagd contact op te nemen met de beheerder om hun wacht woord opnieuw in te stellen.

Als alle vorige controles zijn voltooid, wordt de gebruiker begeleid door het proces om het wacht woord opnieuw in te stellen of te wijzigen.

> [!NOTE]
> SSPR kunnen e-mail meldingen verzenden naar gebruikers als onderdeel van het proces voor het opnieuw instellen van het wacht woord. Deze e-mail berichten worden verzonden via de SMTP-relay-service, die in de modus actief-actief in meerdere regio's actief is.
>
> De e-mail hoofdtekst worden door SMTP-relay Services ontvangen en verwerkt, maar niet opgeslagen. De hoofd tekst van de SSPR-e-mail die mogelijk door de klant verschafte informatie bevat, wordt niet opgeslagen in de logboeken van de SMTP relay-service. De logboeken bevatten alleen meta gegevens van het protocol.

Voltooi de volgende zelf studie om aan de slag te gaan met SSPR:

> [!div class="nextstepaction"]
> [Zelf studie: Self-service voor wacht woord opnieuw instellen inschakelen (SSPR)](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Vereisen dat gebruikers zich registreren wanneer ze zich aanmelden

U kunt de optie inschakelen om een gebruiker verplicht te maken de SSPR-registratie te volt ooien als deze zich aanmeldt bij alle toepassingen die gebruikmaken van Azure AD. Deze werk stroom omvat de volgende toepassingen:

* Microsoft 365
* Azure Portal
* Toegangsvenster
* Federatieve toepassingen
* Aangepaste toepassingen met Azure AD

Wanneer u geen registratie nodig hebt, wordt gebruikers niet gevraagd tijdens het aanmelden, maar kunnen ze hand matig worden geregistreerd. Gebruikers kunnen [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) de koppeling **registreren voor wacht woord opnieuw instellen** op het tabblad **profiel** in het toegangs venster bezoeken of selecteren.

![Registratie opties voor SSPR in de Azure Portal][Registration]

> [!NOTE]
> Gebruikers kunnen de SSPR-registratie Portal sluiten door **Annuleren** te selecteren of door het venster te sluiten. Ze worden echter gevraagd zich te registreren elke keer dat ze zich aanmelden tot ze hun registratie hebben voltooid.
>
> Deze interrupt die moet worden geregistreerd voor SSPR, verbreekt de verbinding van de gebruiker niet als ze al zijn aangemeld.

## <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Het aantal dagen instellen waarna gebruikers wordt gevraagd om de verificatie gegevens opnieuw te bevestigen

Om ervoor te zorgen dat de verificatie methoden juist zijn wanneer ze nodig zijn om het wacht woord opnieuw in te stellen of te wijzigen, kunt u vereisen dat gebruikers hun informatie over de geregistreerde gegevens na een bepaalde periode bevestigen. Deze optie is alleen beschikbaar als u de optie **gebruikers moeten zich registreren wanneer ze zich aanmelden** inschakelen.

Geldige waarden om een gebruiker te vragen om te bevestigen dat de geregistreerde methoden van *0* tot *730* dagen geldig zijn. Als u deze waarde instelt op *0* , worden gebruikers nooit gevraagd om hun verificatie gegevens te bevestigen.

## <a name="authentication-methods"></a>Verificatiemethoden

Wanneer een gebruiker is ingeschakeld voor SSPR, moet deze ten minste één verificatie methode registreren. We raden u ten zeerste aan twee of meer authenticatie methoden te kiezen, zodat uw gebruikers meer flexibiliteit hebben voor het geval ze geen toegang tot één methode hebben wanneer ze deze nodig hebben. Zie [Wat zijn verificatie methoden?](concept-authentication-methods.md)voor meer informatie.

De volgende verificatie methoden zijn beschikbaar voor SSPR:

* Meldingen via mobiele app
* Code van mobiele app
* Email
* Mobiele telefoon
* Zakelijke telefoon
* Beveiligingsvragen

Gebruikers kunnen hun wacht woord alleen opnieuw instellen als ze een verificatie methode hebben geregistreerd die de beheerder heeft ingeschakeld.

> [!WARNING]
> Accounts toegewezen Azure- *beheerders* rollen zijn vereist voor het gebruik van methoden zoals gedefinieerd in de sectie voor het [opnieuw instellen van beleids verschillen](concept-sspr-policy.md#administrator-reset-policy-differences)van de beheerder.

![De selectie van verificatie methoden in de Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Aantal vereiste verificatie methoden

U kunt het aantal beschik bare verificatie methoden configureren dat een gebruiker moet opgeven om het wacht woord opnieuw in te stellen of te ontgrendelen. Deze waarde kan worden ingesteld op *een* of *twee*.

Gebruikers kunnen en moeten meerdere verificatie methoden registreren. Het wordt ten zeerste aanbevolen om twee of meer verificatie methoden te registreren, zodat ze meer flexibiliteit hebben voor het geval ze geen toegang tot één methode hebben wanneer ze deze nodig hebben.

Als een gebruiker niet beschikt over het minimale aantal vereiste methoden dat is geregistreerd wanneer ze SSPR proberen te gebruiken, wordt er een fout pagina weer geven die hen omleidt om een beheerder te vragen of hun wacht woord opnieuw is ingesteld. Ga voorzichtig te werk als u het aantal methoden dat is vereist van een naar twee verhoogt als er bestaande gebruikers zijn geregistreerd voor SSPR en ze de functie vervolgens niet kunnen gebruiken. Zie de volgende sectie voor het [wijzigen van verificatie methoden](#change-authentication-methods)voor meer informatie.

#### <a name="mobile-app-and-sspr"></a>Mobiele app en SSPR

Wanneer u een mobiele app gebruikt als methode voor het opnieuw instellen van wacht woorden, zoals de app Microsoft Authenticator, gelden de volgende overwegingen:

* Wanneer beheerders een andere methode moeten gebruiken om een wacht woord opnieuw in te stellen, is verificatie code de enige optie die beschikbaar is.
* Wanneer beheerders twee methoden nodig hebben om een wacht woord opnieuw in te stellen, kunnen gebruikers naast andere ingeschakelde methoden ook een melding **of** verificatie code gebruiken.

| Aantal methoden dat is vereist om opnieuw in te stellen | Eén | Twee |
| :---: | :---: | :---: |
| Functies voor mobiele apps beschikbaar | Code | Code of melding |

Gebruikers hebben geen optie om hun mobiele app te registreren wanneer ze zich registreren voor selfservice voor wachtwoord herstel van [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Gebruikers kunnen hun mobiele app registreren op [https://aka.ms/mfasetup](https://aka.ms/mfasetup) of in de registratie van gecombineerde beveiligings gegevens op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> De verificator-app kan niet als enige verificatie methode worden geselecteerd als alleen de methode vereist is. Op dezelfde manier kan de verificator-app en slechts één extra methode worden geselecteerd wanneer er twee methoden vereist zijn.
>
> Bij het configureren van SSPR-beleid dat de verificator-app als methode bevat, moet er ten minste één extra methode worden geselecteerd wanneer één methode vereist is, en moeten er ten minste twee extra methoden worden geselecteerd wanneer twee methoden worden geconfigureerd.
>
> Deze vereiste is omdat de huidige registratie-ervaring voor SSPR niet de optie bevat voor het registreren van de verificator-app. De optie voor het registreren van de verificator-app is opgenomen in de nieuwe [gecombineerde registratie-ervaring](./concept-registration-mfa-sspr-combined.md).
>
> Het toestaan van beleids regels die alleen de verificator-app gebruiken (als één methode is vereist) of de verificator-app en slechts één extra methode (wanneer twee methoden zijn vereist), kunnen ertoe leiden dat gebruikers niet worden geregistreerd voor SSPR totdat ze zijn geconfigureerd voor het gebruik van de nieuwe gecombineerde registratie-ervaring.

### <a name="change-authentication-methods"></a>Verificatie methoden wijzigen

Wat gebeurt er als u begint met een beleid dat slechts één vereiste authenticatie methode heeft voor opnieuw instellen of ontgrendelen en u wijzigt dat op twee manieren?

| Aantal geregistreerde methoden | Aantal methoden dat is vereist | Resultaat |
| :---: | :---: | :---: |
| 1 of meer | 1 | **Kan** opnieuw instellen of ontgrendelen |
| 1 | 2 | **Kan niet** opnieuw instellen of ontgrendelen |
| 2 of meer | 2 | **Kan** opnieuw instellen of ontgrendelen |

Het wijzigen van de beschik bare verificatie methoden kan ook problemen voor gebruikers veroorzaken. Als u de typen verificatie methoden wijzigt die een gebruiker kan gebruiken, kunt u per ongeluk voor komen dat gebruikers SSPR kunnen gebruiken als ze niet beschikken over de minimale hoeveelheid gegevens die beschikbaar zijn.

Bekijk het volgende voorbeeld scenario:

1. Het oorspronkelijke beleid is geconfigureerd met twee vereiste verificatie methoden. Alleen het zakelijke telefoon nummer en de beveiligings vragen worden gebruikt.
1. De beheerder wijzigt het beleid zodat er geen beveiligings vragen meer worden gebruikt, maar maakt het gebruik van een mobiele telefoon en een alternatief e-mail adres mogelijk.
1. Gebruikers zonder de velden mobiel telefoon of alternatief e-mail adres kunnen nu hun wacht woord niet opnieuw instellen.

## <a name="notifications"></a>Meldingen

Met SSPR kunt u meldingen voor gebruikers en identiteits beheerders configureren om het bewustzijn van wachtwoord gebeurtenissen te verbeteren.

### <a name="notify-users-on-password-resets"></a>Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord

Als deze optie is ingesteld op **Ja**, krijgen gebruikers hun wacht woord opnieuw in te stellen een e-mail met de melding dat hun wacht woord is gewijzigd. Het e-mail bericht wordt via de SSPR-portal verzonden naar de primaire en alternatieve e-mail adressen die zijn opgeslagen in azure AD. Niemand anders wordt op de hoogte gesteld van de gebeurtenis opnieuw instellen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Alle beheerders waarschuwen wanneer andere beheerders hun wacht woord opnieuw instellen

Als deze optie is ingesteld op **Ja**, worden alle andere Azure-beheerders een e-mail ontvangen naar hun primaire e-mail adres dat is opgeslagen in azure AD. Het e-mail bericht geeft aan dat een andere beheerder zijn of haar wacht woord heeft gewijzigd met behulp van SSPR.

Bekijk het volgende voorbeeld scenario:

* Er zijn vier beheerders in een omgeving.
* Beheerder *een* wacht woord opnieuw instellen met behulp van SSPR.
* Beheerders *B*, *C* en *D* ontvangen een e-mail bericht met een waarschuwing voor het opnieuw instellen van het wacht woord.

## <a name="on-premises-integration"></a>On-premises integratie

Als u een hybride omgeving hebt, kunt u Azure AD Connect configureren om wachtwoord wijzigings gebeurtenissen terug te schrijven van Azure AD naar een on-premises Directory.

![Valideren van wacht woord terugschrijven is ingeschakeld en werkt][Writeback]

Azure AD controleert uw huidige hybride verbinding en biedt een van de volgende berichten in de Azure Portal:

* Uw on-premises terugschrijf client is actief.
* Azure AD is online en is verbonden met uw on-premises terugschrijf client. Het lijkt echter alsof de geïnstalleerde versie van Azure AD Connect verouderd is. Overweeg het [upgraden van Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) om ervoor te zorgen dat u beschikt over de nieuwste connectiviteits functies en belang rijke problemen met oplossingen.
* Helaas kan de status van uw on-premises terugschrijf client niet worden gecontroleerd, omdat de geïnstalleerde versie van Azure AD Connect verouderd is. [Upgrade Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) zodat u de verbindings status kunt controleren.
* Helaas lijkt het alsof er geen verbinding kan worden gemaakt met uw on-premises back-upclient. [Problemen met Azure AD Connect oplossen](./troubleshoot-sspr-writeback.md) om de verbinding te herstellen.
* Er kan geen verbinding worden gemaakt met uw on-premises terugschrijf client omdat het terugschrijven van wacht woorden niet correct is geconfigureerd. [Configureer write-back van wacht woord](./tutorial-enable-sspr-writeback.md) om de verbinding te herstellen.
* Helaas lijkt het alsof er geen verbinding kan worden gemaakt met uw on-premises back-upclient. Dit kan worden veroorzaakt door tijdelijke problemen aan onze kant. Als het probleem zich blijft voordoen, [lost u Azure AD Connect](./troubleshoot-sspr-writeback.md) op om de verbinding te herstellen.

Voltooi de volgende zelf studie om aan de slag te gaan met SSPR write-back:

> [!div class="nextstepaction"]
> [Zelf studie: write-back van self-service voor wacht woord opnieuw instellen (SSPR) inschakelen](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Wacht woorden terugschrijven naar uw on-premises Directory

U kunt wacht woord terugschrijven inschakelen met behulp van de Azure Portal. U kunt het terugschrijven van wacht woorden ook tijdelijk uitschakelen zonder dat Azure AD Connect opnieuw hoeft te worden geconfigureerd.

* Als de optie is ingesteld op **Ja**, wordt write-back ingeschakeld. Federatieve, Pass-Through-verificatie of gebruikers met een wacht woord-hash kunnen hun wacht woord opnieuw instellen.
* Als de optie is ingesteld op **Nee**, wordt write-back uitgeschakeld. Federatieve, Pass-Through-verificatie of wachtwoord-hash gesynchroniseerde gebruikers kunnen hun wacht woorden niet opnieuw instellen.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Gebruikers toestaan om accounts te ontgrendelen zonder hun wacht woord opnieuw in te stellen

Standaard worden accounts door Azure AD ontgrendeld wanneer het wacht woord opnieuw wordt ingesteld. Om flexibiliteit te bieden, kunt u ervoor kiezen om gebruikers toe te staan hun on-premises accounts te ontgrendelen zonder hun wacht woord opnieuw in te stellen. Gebruik deze instelling om deze twee bewerkingen van elkaar te scheiden.

* Als deze optie is ingesteld op **Ja**, krijgen gebruikers de mogelijkheid om hun wacht woord opnieuw in te stellen en het account te ontgrendelen, of om hun account te ontgrendelen zonder het wacht woord opnieuw in te stellen.
* Als deze instelling is ingesteld op **Nee**, kunnen gebruikers alleen een gecombineerde wachtwoord herstel-en account vergrendelings bewerking uitvoeren.

### <a name="on-premises-active-directory-password-filters"></a>On-premises Active Directory wachtwoord filters

SSPR voert het equivalent van een door de beheerder geïnitieerd wacht woord opnieuw instellen in Active Directory. Als u een wachtwoord filter van een derde partij gebruikt om aangepaste wachtwoord regels af te dwingen en u wilt dat dit wachtwoord filter wordt gecontroleerd tijdens de selfservice voor wachtwoord herstel van Azure AD, moet u ervoor zorgen dat de oplossing voor wachtwoord filter van derden is geconfigureerd voor het Toep assen van het scenario voor het opnieuw instellen van het beheerders wachtwoord. [Azure AD-wachtwoord beveiliging voor Active Directory Domain Services](concept-password-ban-bad-on-premises.md) wordt standaard ondersteund.

## <a name="password-reset-for-b2b-users"></a>Wacht woord opnieuw instellen voor B2B-gebruikers

Wacht woord opnieuw instellen en wijzigen worden volledig ondersteund voor alle Business-to-Business (B2B)-configuraties. Het opnieuw instellen van B2B-gebruikers wachtwoorden wordt in de volgende drie gevallen ondersteund:

* **Gebruikers van een partner organisatie met een bestaande Azure AD-Tenant**: als de organisatie waarmee u een partner hebt, een bestaande Azure AD-Tenant heeft, respecteren we dat het beleid voor het opnieuw instellen van het wacht woord is ingeschakeld voor die Tenant. Om het wacht woord opnieuw in te stellen, moet de partner organisatie alleen ervoor zorgen dat Azure AD SSPR is ingeschakeld. Er worden geen extra kosten in rekening gebracht voor Microsoft 365 klanten.
* **Gebruikers die zich aanmelden via** Self-Service-aanmelding: als de organisatie die u hebt gebruikt de [self-service-registratie](../enterprise-users/directory-self-service-signup.md) functie in een Tenant krijgt, kunnen we het wacht woord opnieuw instellen met het e-mail bericht dat ze hebben geregistreerd.
* **B2B-gebruikers**: alle nieuwe B2B-gebruikers die zijn gemaakt met de nieuwe [mogelijkheden van Azure AD B2B](../external-identities/what-is-b2b.md) , kunnen hun wacht woord ook opnieuw instellen met het e-mail adres dat is geregistreerd tijdens het INVITE-proces.

Als u dit scenario wilt testen, gaat u naar https://passwordreset.microsoftonline.com met een van deze partner-gebruikers. Als er een alternatieve e-mail of verificatie-e-mail is gedefinieerd, werkt wacht woord opnieuw instellen zoals verwacht.

> [!NOTE]
> Micro soft-accounts waaraan gast toegang is verleend voor uw Azure AD-Tenant, zoals die van Hotmail.com, Outlook.com of andere persoonlijke e-mail adressen, kunnen Azure AD SSPR niet gebruiken. Ze moeten hun wacht woord opnieuw instellen met behulp van de informatie in de [Als u zich niet kunt aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) -artikel.

## <a name="next-steps"></a>Volgende stappen

Voltooi de volgende zelf studie om aan de slag te gaan met SSPR:

> [!div class="nextstepaction"]
> [Zelf studie: Self-service voor wacht woord opnieuw instellen inschakelen (SSPR)](tutorial-enable-sspr.md)

De volgende koppelingen bieden aanvullende informatie over wachtwoordherstel met behulp van Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Azure Active Directory-verificatiemethoden die beschikbaar zijn en hoeveel er vereist zijn"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Opties voor SSPR-registratie configureren in de Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "On-premises integratie voor SSPR in de Azure Portal"