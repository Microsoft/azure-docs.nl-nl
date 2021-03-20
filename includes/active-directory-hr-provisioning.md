---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93134951"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Cloud-HR-toepassing voor Azure Active Directory-gebruikersinrichting

In het verleden gebruikten IT-medewerkers handmatige methoden voor het maken, bijwerken en verwijderen van medewerkers. Ze gebruikten methoden zoals het uploaden van CSV-bestanden of aangepaste scripts om gegevens van werknemers te synchroniseren. Deze inrichtingsprocessen zijn foutgevoelig, onveilig en moeilijk te beheren.

Voor het beheren van de identiteitslevenscycli van werknemers, leveranciers of tijdelijke werknemers, biedt de [Azure Active Directory (Azure AD)-gebruikersinrichtingsservice](../articles/active-directory/app-provisioning/user-provisioning.md) integratie met human resources (HR)-toepassingen in de cloud. Voorbeelden van toepassingen zijn Workday of SuccessFactors.

Azure AD gebruikt deze integratie om de volgende workflows voor de HR-toepassing (app) in de cloud in te schakelen:

- **Gebruikers inrichten op Active Directory:** U kunt bepaalde sets gebruikers van een HR-app in de cloud in een of meer Active Directory-domeinen inrichten.
- **Alleen-cloud gebruikers inrichten op Azure AD:** In scenario's waarin Active Directory niet wordt gebruikt, dient u gebruikers rechtstreeks vanuit de cloud-HR-app in te richten op Azure AD.
- **Terugschrijven naar de cloud-HR-app:** Schrijf de e-mailadressen en de kenmerken van de gebruikersnamen van Azure AD terug naar de cloud-HR-app.


## <a name="enabled-hr-scenarios"></a>Ingeschakelde HR-scenario's

Met de Azure AD-gebruikersinrichtingsservice kunnen de volgende HR-scenario's voor beheer van identiteitslevenscycli worden geautomatiseerd:

- **Nieuwe werknemer aannemen:** Wanneer een nieuwe werknemer wordt toegevoegd aan de cloud-HR-app, wordt er automatisch een gebruikersaccount gemaakt in Active Directory en Azure AD met de optie om het e-mailadres en de kenmerken van de gebruikersnaam terug te schrijven naar de cloud-HR-app.
- **Updates van kenmerken en profielen van werknemers:** Wanneer een werknemersrecord, zoals naam, titel of manager, wordt bijgewerkt in de cloud-HR-app, wordt het gebruikersaccount automatisch bijgewerkt in Active Directory en Azure AD.
- **Beëindiging van dienstverband:** Wanneer een dienstverband wordt beëindigd in de cloud-HR-app, wordt het gebruikersaccount automatisch uitgeschakeld in Active Directory en Azure AD.
- **Werknemers opnieuw in dienst genomen:** Wanneer een werknemer in de cloud-HR-app opnieuw wordt aangenomen, kan het oude account automatisch opnieuw worden geactiveerd of opnieuw worden ingericht voor Active Directory en Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Voor wie is deze integratie het meest geschikt?

De integratie van de cloud-HR-app met Azure AD-gebruikersinrichting is ideaal voor organisaties die:

- Een vooraf ontwikkelde cloud-oplossing willen voor cloud-HR-gebruikersinrichting.
- Rechtstreekse gebruikersinrichting nodig hebben van de cloud-HR-app naar Active Directory of Azure AD.
- Vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen uit de cloud-HR-app.
- Vereisen dat gebruikers die erbij komen, verhuizen of vertrekken worden gesynchroniseerd met een of meer Active Directory-forests, -domeinen en OE’s op basis van wijzigingsinformatie die is gedetecteerd in de cloud-HR-app.
- Office 365 gebruiken voor e-mail.


### <a name="key-benefits"></a>Belangrijkste voordelen

Deze mogelijkheid van HR-aangestuurde IT-inrichting biedt de volgende belangrijke zakelijke voordelen:

- **Productiviteit verhogen:** U kunt nu de toewijzing van gebruikersaccounts en Office 365-licenties automatiseren en toegang verlenen tot belangrijke groepen. Als u toewijzingen automatiseert, krijgen nieuwe medewerkers direct toegang tot de hulpprogramma's voor hun werk en wordt de productiviteit verhoogd.
- **Risico’s beheren:** U kunt de beveiliging verbeteren door wijzigingen te automatiseren op basis van de status van werknemers of groepslidmaatschappen met gegevens die binnenstromen vanuit de cloud-HR-app. Als u wijzigingen automatiseert, zorgt u ervoor dat gebruikersidentiteiten en toegang tot belangrijke apps automatisch worden bijgewerkt wanneer gebruikers overstappen of de organisatie verlaten.
- **Naleving en governance aanpakken:** Azure AD biedt ondersteuning voor systeemeigen auditlogboeken voor aanvragen voor gebruikersinrichting die worden uitgevoerd door apps van zowel de bron- als doelsystemen. Tijdens het controleren kunt u vanaf één scherm bijhouden wie toegang heeft tot de apps.
- **Kosten beheren:** Automatische inrichting vermindert de kosten door inefficiëntie en menselijke fouten te voorkomen die gerelateerd zijn aan handmatige inrichting. Het vermindert de behoefte aan aangepaste, op maat ontwikkelde oplossingen voor gebruikersinrichting die in de loop van de tijd zijn gebouwd met behulp van verouderde platforms.
