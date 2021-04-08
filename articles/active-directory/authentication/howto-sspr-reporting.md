---
title: Rapporten van self-service voor het opnieuw instellen van wacht woorden-Azure Active Directory
description: Rapportage over Azure AD self-service-wachtwoord herstel gebeurtenissen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad1b8318e261c7dfef7fc125716736087a84bdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579175"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Reporting options for Azure AD password management (Rapportageopties voor Azure AD-wachtwoordbeheer)

Na de implementatie willen veel organisaties weten hoe of als self-service voor wachtwoord herstel (SSPR) werkelijk wordt gebruikt. Met de rapportage functie die Azure Active Directory (Azure AD) kunt u vragen beantwoorden met behulp van vooraf ontwikkelde rapporten. Als u een juiste licentie hebt, kunt u ook aangepaste query's maken.

![Rapportage op SSPR met behulp van de audit Logboeken in azure AD][Reporting]

De volgende vragen kunnen worden beantwoord door de rapporten die voor komen in de [Azure Portal](https://portal.azure.com/):

> [!NOTE]
> U moet [een globale beheerder](../roles/permissions-reference.md)zijn en u moet zich aanmelden om deze gegevens te verzamelen namens uw organisatie. Als u zich wilt aanmelden, moet u het tabblad **rapportage** of de audit logboeken ten minste één keer bezoeken. Tot die toe worden er geen gegevens verzameld voor uw organisatie.
>

* Hoeveel mensen zijn geregistreerd voor wachtwoord herstel?
* Wie heeft de registratie voor wacht woord opnieuw instellen?
* Welke gegevens worden geregistreerd door anderen?
* Hoeveel mensen hebben hun wacht woorden in de afgelopen zeven dagen opnieuw ingesteld?
* Wat zijn de meest voorkomende methoden die gebruikers of beheerders gebruiken om hun wacht woord opnieuw in te stellen?
* Wat zijn veelvoorkomende problemen met gebruikers of beheerders?
* Welke beheerders moeten hun eigen wacht woorden regel matig opnieuw instellen?
* Gaat er verdachte activiteit mee met het opnieuw instellen van wacht woorden?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>How to view Password Management Reports (Rapporten voor wachtwoordbeheer bekijken in Azure Portal)

In de Azure Portal-ervaring hebben we de manier verbeterd waarop u wacht woord opnieuw instellen en registratie activiteit voor het opnieuw instellen van wacht woorden kunt bekijken. Gebruik de volgende stappen om de wachtwoord herstel-en registratie gebeurtenissen voor het opnieuw instellen van het wacht woord te vinden:

1. Blader naar [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** in het linkerdeelvenster.
3. Zoek naar **Azure Active Directory** in de lijst met Services en selecteer deze.
4. Selecteer **gebruikers** in de sectie beheren.
5. Selecteer **controle logboeken** op de Blade **gebruikers** . Hiermee worden alle controle gebeurtenissen weer gegeven die zijn opgetreden voor alle gebruikers in uw Directory. U kunt deze weer gave filteren om alle met een wacht woord gerelateerde gebeurtenissen weer te geven.
6. Selecteer in het menu **filter** boven aan het deel venster de vervolg keuzelijst **service** en wijzig deze in het Service type **selfservice voor wachtwoord beheer** .
7. U kunt de lijst eventueel verder filteren door de specifieke **activiteit** te kiezen die u wilt.

### <a name="combined-registration"></a>Gecombineerde registratie

Als u [gecombineerde registratie](./concept-registration-mfa-sspr-combined.md)hebt ingeschakeld, wordt informatie over gebruikers activiteit in de audit logboeken gevonden onder methoden voor **beveiligings**  >  **verificatie**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Beschrijving van de rapport kolommen in de Azure Portal

De volgende lijst bevat een uitleg van elk van de rapport kolommen in het Azure Portal detail:

* **Gebruiker**: de gebruiker die de registratie bewerking voor wacht woord opnieuw instellen heeft geprobeerd.
* **Rol**: de rol van de gebruiker in de Directory.
* **Datum en tijd**: de datum en tijd van de poging.
* **Geregistreerde gegevens**: de verificatie gegevens die de gebruiker heeft opgegeven tijdens de registratie van het wacht woord opnieuw instellen.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Beschrijving van de rapport waarden in de Azure Portal

In de volgende tabel worden de verschillende waarden beschreven die u kunt instellen voor elke kolom in de Azure Portal:

| Kolom | Toegestane waarden en hun betekenis |
| --- | --- |
| Geregistreerde gegevens |**Alternatief e-mail adres**: de gebruiker heeft een alternatieve e-mail of verificatie-e-mail gebruikt om te verifiëren.<p><p>**Office Phone**: de gebruiker heeft een zakelijke telefoon gebruikt om te verifiëren.<p>**Mobiele telefoon**: de gebruiker heeft een mobiele telefoon of authenticatie-telefoon gebruikt om te verifiëren.<p>**Beveiligings vragen**: de gebruiker gebruikte beveiligings vragen om te verifiëren.<p>**Een combi natie van de vorige methoden, bijvoorbeeld alternatieve e-mail en mobiele telefoon**: treedt op wanneer een twee-poort beleid is opgegeven en laat zien welke twee methoden de gebruiker heeft gebruikt voor het verifiëren van de aanvraag voor het opnieuw instellen van een wacht woord. |

## <a name="self-service-password-management-activity-types"></a>Self-Service activiteiten typen voor wachtwoord beheer

De volgende typen activiteiten worden weer gegeven in de categorie controle gebeurtenis **selfservice wachtwoord beheer** :

* [Geblokkeerd voor de selfservice voor wachtwoord herstel](#activity-type-blocked-from-self-service-password-reset): geeft aan dat een gebruiker heeft geprobeerd een wacht woord opnieuw in te stellen, een specifieke poort te gebruiken of een telefoon nummer van meer dan vijf keer in 24 uur te valideren.
* [Wacht woord wijzigen (Self-Service)](#activity-type-change-password-self-service): geeft aan dat een gebruiker een wacht woord voor vrijwillige of geforceerd (vanwege een verval datum) heeft gewijzigd.
* [Wacht woord opnieuw instellen (door beheerder)](#activity-type-reset-password-by-admin): geeft aan dat een beheerder een wacht woord opnieuw heeft ingesteld namens een gebruiker van de Azure Portal.
* [Wacht woord opnieuw instellen (Self-Service)](#activity-type-reset-password-self-service): geeft aan dat een gebruiker het wacht woord van de [Azure AD-Portal](https://passwordreset.microsoftonline.com)voor het opnieuw instellen van het wacht woord opnieuw heeft ingesteld.
* Voortgang van de stroom voor het [opnieuw instellen van het wacht woord voor wachtwoord herstel](#activity-type-self-serve-password-reset-flow-activity-progress): Hiermee wordt aangegeven welke stap een gebruiker doorloopt, zoals het door geven van een specifieke verificatie poort voor wacht woord opnieuw instellen als onderdeel van het proces voor het opnieuw instellen van wacht woorden.
* [Gebruikers account ontgrendelen (Self-Service)](#activity-type-unlock-a-user-account-self-service)): geeft aan dat een gebruiker het Active Directory account heeft ontgrendeld zonder het wacht woord opnieuw in te stellen vanuit de [Azure AD-portal voor wachtwoord herstel](https://passwordreset.microsoftonline.com) met behulp van de Active Directory-functie van account ontgrendelen zonder opnieuw in te stellen.
* [Gebruiker geregistreerd voor selfservice voor wachtwoord herstel](#activity-type-user-registered-for-self-service-password-reset): geeft aan dat een gebruiker alle vereiste gegevens heeft geregistreerd om hun wacht woord opnieuw in te stellen in overeenstemming met het momenteel opgegeven Tenant wachtwoord opnieuw instellen beleid.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Activiteitstype: geblokkeerd voor selfservice voor wachtwoord herstel

In de volgende lijst vindt u meer informatie over deze activiteit:

* **Beschrijving van activiteit**: geeft aan dat een gebruiker heeft geprobeerd een wacht woord opnieuw in te stellen, een specifieke poort te gebruiken of een telefoon nummer meer dan vijf keer in 24 uur te valideren.
* **Activiteit actor**: de gebruiker die is beperkt tot het uitvoeren van extra herstel bewerkingen. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Doel van activiteit**: de gebruiker die is beperkt tot het uitvoeren van extra herstel bewerkingen. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Activiteitsstatus**:
  * _Geslaagd_: geeft aan dat een gebruiker is beperkt tot het uitvoeren van eventuele extra herstelde pogingen, het proberen van aanvullende authenticatie methoden of het valideren van eventuele extra telefoon nummers voor de komende 24 uur.
* **Reden van fout status van activiteit**: niet van toepassing.

### <a name="activity-type-change-password-self-service"></a>Type activiteit: wacht woord wijzigen (Self-Service)

In de volgende lijst vindt u meer informatie over deze activiteit:

* **Beschrijving van activiteit**: geeft aan dat een gebruiker een wacht woord voor vrijwillige of geforceerd (als gevolg van een verval datum) heeft gewijzigd.
* **Activiteit actor**: de gebruiker die het wacht woord heeft gewijzigd. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Doel van activiteit**: de gebruiker die het wacht woord heeft gewijzigd. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Status van activiteit**:
  * _Geslaagd_: geeft aan dat een gebruiker het wacht woord heeft gewijzigd.
  * _Fout_: geeft aan dat een gebruiker het wacht woord niet heeft gewijzigd. U kunt de rij selecteren om de reden categorie van de **activiteit** te bekijken voor meer informatie over waarom de fout is opgetreden.
* **Reden van fout bij status van activiteit**:
  * _FuzzyPolicyViolationInvalidPassword_: de gebruiker heeft een wacht woord geselecteerd dat automatisch is geblokkeerd omdat de door micro soft geweigerde wachtwoord detectie mogelijkheden te vaak of zeer zwak zijn gevonden.

### <a name="activity-type-reset-password-by-admin"></a>Type activiteit: wacht woord opnieuw instellen (door beheerder)

In de volgende lijst vindt u meer informatie over deze activiteit:

* **Beschrijving van activiteit**: Hiermee geeft u aan dat een beheerder een wacht woord opnieuw instellen heeft uitgevoerd namens een gebruiker van de Azure Portal.
* **Activiteit actor**: de beheerder die het wacht woord opnieuw instelt namens een andere eind gebruiker of beheerder. Moet een wachtwoord beheerder, gebruikers beheerder of helpdesk beheerder zijn.
* **Doel van activiteit**: de gebruiker waarvan het wacht woord opnieuw is ingesteld. De gebruiker kan een eind gebruiker of een andere beheerder zijn.
* **Status van activiteit**:
  * _Geslaagd_: geeft aan dat een beheerder het wacht woord van een gebruiker is opnieuw ingesteld.
  * _Fout_: geeft aan dat een beheerder het wacht woord van een gebruiker niet kan wijzigen. U kunt de rij selecteren om de reden categorie van de **activiteit** te bekijken voor meer informatie over waarom de fout is opgetreden.

### <a name="activity-type-reset-password-self-service"></a>Type activiteit: wacht woord opnieuw instellen (Self-Service)

In de volgende lijst vindt u meer informatie over deze activiteit:

* **Beschrijving van activiteit**: geeft aan dat een gebruiker het wacht woord opnieuw heeft ingesteld vanuit de [Portal voor wachtwoord herstel van Azure AD](https://passwordreset.microsoftonline.com).
* **Activiteit actor**: de gebruiker die het wacht woord opnieuw instelt. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Doel van activiteit**: de gebruiker die het wacht woord opnieuw instelt. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Status van activiteit**:
  * _Geslaagd_: geeft aan dat een gebruiker hun eigen wacht woord opnieuw heeft ingesteld.
  * _Fout_: geeft aan dat een gebruiker hun eigen wacht woord niet opnieuw heeft ingesteld. U kunt de rij selecteren om de reden categorie van de **activiteit** te bekijken voor meer informatie over waarom de fout is opgetreden.
* **Reden van fout bij status van activiteit**:
  * _FuzzyPolicyViolationInvalidPassword_: de beheerder heeft een wacht woord geselecteerd dat automatisch is geblokkeerd omdat de door micro soft geweigerde wachtwoord detectie mogelijkheden te vaak of bijzonder zwak zijn gevonden.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Type activiteit: zelf de stroom voor het opnieuw instellen van wacht woorden

In de volgende lijst vindt u meer informatie over deze activiteit:

* **Beschrijving van activiteit**: geeft elke specifieke stap aan die een gebruiker doorloopt (zoals het door geven van een specifieke verificatie poort voor wacht woord opnieuw instellen) als onderdeel van het proces voor het opnieuw instellen van het wacht woord.
* **Activiteit actor**: de gebruiker die een deel van de stroom voor het opnieuw instellen van het wacht woord heeft uitgevoerd. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Doel van activiteit**: de gebruiker die een deel van de stroom voor het opnieuw instellen van het wacht woord heeft uitgevoerd. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Status van activiteit**:
  * _Geslaagd_: geeft aan dat een gebruiker een specifieke stap van de stroom voor het opnieuw instellen van het wacht woord heeft voltooid.
  * _Fout_: geeft aan dat een specifieke stap van de stroom voor het opnieuw instellen van het wacht woord is mislukt. U kunt de rij selecteren om de reden categorie van de **activiteit** te bekijken voor meer informatie over waarom de fout is opgetreden.
* **Reden van activiteit status**: Raadpleeg de volgende tabel voor [alle toegestane redenen voor het opnieuw instellen van de activiteit](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Type activiteit: een gebruikers account ontgrendelen (Self-Service)

In de volgende lijst vindt u meer informatie over deze activiteit:

* **Beschrijving van activiteit**: geeft aan dat een gebruiker het Active Directory-account heeft ontgrendeld zonder het wacht woord opnieuw in te stellen vanuit de [Azure AD-portal voor wachtwoord herstel](https://passwordreset.microsoftonline.com) met behulp van de Active Directory-functie van account ontgrendelen zonder opnieuw in te stellen.
* **Activiteit actor**: de gebruiker die het account heeft ontgrendeld zonder het wacht woord opnieuw in te stellen. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Doel van activiteit**: de gebruiker die het account heeft ontgrendeld zonder het wacht woord opnieuw in te stellen. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Status van toegestane activiteiten**:
  * _Geslaagd_: geeft aan dat een gebruiker zijn of haar eigen account heeft ontgrendeld.
  * _Fout_: geeft aan dat een gebruiker het account niet heeft ontgrendeld. U kunt de rij selecteren om de reden categorie van de **activiteit** te bekijken voor meer informatie over waarom de fout is opgetreden.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Type activiteit: gebruiker geregistreerd voor selfservice voor wachtwoord herstel

In de volgende lijst vindt u meer informatie over deze activiteit:

* **Beschrijving van activiteit**: geeft aan dat een gebruiker alle vereiste gegevens heeft geregistreerd om hun wacht woord opnieuw in te stellen in overeenstemming met het momenteel opgegeven Tenant wachtwoord opnieuw instellen beleid. 
* **Activiteit actor**: de gebruiker die is geregistreerd voor wachtwoord herstel. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Doel van activiteit**: de gebruiker die zich heeft geregistreerd voor het opnieuw instellen van het wacht woord. De gebruiker kan een eind gebruiker of een beheerder zijn.
* **Status van toegestane activiteiten**:
  * _Geslaagd_: geeft aan dat een gebruiker is geregistreerd voor het opnieuw instellen van het wacht woord volgens het huidige beleid. 
  * _Fout_: geeft aan dat een gebruiker niet kan worden geregistreerd voor het opnieuw instellen van het wacht woord. U kunt de rij selecteren om de reden categorie van de **activiteit** te bekijken voor meer informatie over waarom de fout is opgetreden.

     >[!NOTE]
     >Fout betekent niet dat een gebruiker een eigen wacht woord niet opnieuw kan instellen. Dit betekent dat het registratie proces niet is voltooid. Als er niet-geverifieerde gegevens op hun account aanwezig zijn, zoals een telefoon nummer dat niet is gevalideerd, zelfs als ze dit telefoon nummer niet hebben geverifieerd, kunnen ze nog steeds hun wacht woord opnieuw instellen.

## <a name="next-steps"></a>Volgende stappen

* [SSPR-en MFA-gebruik en inzichten rapportage](./howto-authentication-methods-activity.md)
* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registreer u voor selfservice voor wacht woord opnieuw instellen](../user-help/active-directory-passwords-reset-register.md).
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](./tutorial-enable-sspr-writeback.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets is verbroken. Hoe kan ik problemen met SSPR oplossen?](./troubleshoot-sspr.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Voor beeld van audit logboeken voor SSPR-activiteiten in azure AD"