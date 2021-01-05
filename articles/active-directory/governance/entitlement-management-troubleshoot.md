---
title: Problemen met het beheer van rechten oplossen-Azure AD
description: Meer informatie over sommige items die u moet controleren om u te helpen bij het oplossen van Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bca78195d4fb7bb265c4a7fd8d78ea49a35335b6
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746708"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Het beheer van rechten voor Azure AD oplossen

In dit artikel worden enkele items beschreven die u moet controleren om te helpen bij het oplossen van het beheer van rechten van Azure Active Directory (Azure AD).

## <a name="administration"></a>Beheer

* Als u een bericht krijgt dat toegang is geweigerd bij het configureren van rechten beheer en u een globale beheerder bent, moet u ervoor zorgen dat uw Directory een [Azure AD Premium P2 (of EMS E5)-licentie](entitlement-management-overview.md#license-requirements)heeft.

* Als u een bericht krijgt dat toegang wordt geweigerd bij het maken of weer geven van toegangs pakketten en u lid bent van een maker van een catalogus, moet u [een catalogus maken](entitlement-management-catalog-create.md) voordat u uw eerste toegangs pakket maakt.

## <a name="resources"></a>Resources

* Rollen voor toepassingen worden gedefinieerd door de toepassing zelf en worden beheerd in Azure AD. Als een toepassing geen resource rollen heeft, wijst het rechten beheer gebruikers toe aan een **standaard-Access** -rol.

    Houd er rekening mee dat de Azure Portal ook service-principals kunnen weer geven voor services die niet als toepassingen kunnen worden geselecteerd.  Met name **Exchange Online** en **share point online** zijn services, geen toepassingen die resource rollen hebben in de Directory, zodat ze niet kunnen worden opgenomen in een toegangs pakket.  Gebruik in plaats daarvan op groepen gebaseerde licenties om een geschikte licentie te maken voor een gebruiker die toegang nodig heeft tot deze services.

* Een groep kan alleen een resource in een toegangspakket zijn als deze kan worden gewijzigd in Azure AD.  Groepen die afkomstig zijn van een on-premises Active Directory, kunnen niet worden toegewezen als resources omdat hun eigenaar- of lidkenmerken niet kunnen worden gewijzigd in Azure AD.   Groepen die afkomstig zijn van Exchange Online als distributiegroep kunnen evenmin in Azure AD worden gewijzigd. 

* SharePoint Online-documentbibliotheken en afzonderlijke documenten kunnen niet worden toegevoegd als resources.  Maak in plaats daarvan een [Azure AD-beveiligings groep](../fundamentals/active-directory-groups-create-azure-portal.md), neem deze groep en een siterol op in het toegangs pakket, en gebruik in share point online die groep om de toegang tot de document bibliotheek of het document te beheren.

* Als er gebruikers zijn die al zijn toegewezen aan een resource die u met een toegangspakket wilt beheren, moet u ervoor zorgen dat de gebruikers met een toepasselijk beleid worden toegewezen aan het toegangspakket. Het is bijvoorbeeld mogelijk dat u een groep wilt opnemen in een toegangspakket dat al gebruikers in de groep bevat. Als die gebruikers in de groep voortdurende toegang nodig hebben, moeten ze beschikken over het juiste beleid voor de toegangspakketten, zodat ze hun toegang tot de groep niet verliezen. U kunt het toegangspakket toewijzen door de gebruikers te vragen het toegangspakket met die resource aan te vragen, of door ze rechtstreeks toe te wijzen aan het toegangspakket. Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket](entitlement-management-access-package-request-policy.md)voor meer informatie.

* Wanneer u een lid van een team verwijdert, worden deze ook uit de groep Microsoft 365 verwijderd. Het verwijderen uit de chatfunctionaliteit van het team kan worden uitgesteld. Zie [groepslid maatschap](/microsoftteams/office-365-groups#group-membership)voor meer informatie.

* Zorg ervoor dat uw directory niet is geconfigureerd voor Multi-Geo. Rechtenbeheer biedt momenteel geen ondersteuning voor Multi-Geo-locaties voor SharePoint Online. SharePoint Online-sites moeten zich in de standaard geo-locatie bevinden om te worden beheerd met rechtenbeheer. Zie [multi-geo-mogelijkheden in OneDrive en share point online](/Microsoft 365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)voor meer informatie.

## <a name="access-packages"></a>Toegangspakketten

* Als u probeert een toegangs pakket of beleid te verwijderen en een fout bericht te zien waarin staat dat er actieve toewijzingen zijn, kunt u controleren of recent verwijderde gebruikers nog steeds toewijzingen hebben als u geen gebruikers met toewijzingen ziet. Tijdens de periode van 30 dagen nadat een gebruiker is verwijderd, kan het gebruikers account worden hersteld.   

## <a name="external-users"></a>Externe gebruikers

* Wanneer een externe gebruiker de toegang tot een toegangs pakket wil aanvragen, moet u ervoor zorgen dat ze de **Portal-koppeling van mijn toegang** gebruiken voor het toegangs pakket. Zie [koppeling delen om een toegangs pakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie. Als een externe gebruiker alleen **myaccess.Microsoft.com** bezoekt en niet de volledige mijn Access-Portal koppeling gebruikt, zien ze de toegangs pakketten die voor hen beschikbaar zijn in hun eigen organisatie en niet in uw organisatie.

* Als een externe gebruiker geen toegang kan aanvragen tot een toegangs pakket of geen toegang kan krijgen tot bronnen, controleert u de [instellingen voor externe gebruikers](entitlement-management-external-users.md#settings-for-external-users).

* Als een nieuwe externe gebruiker die zich nog niet eerder bij uw directory heeft aangemeld een toegangspakket ontvangt met een SharePoint Online site, wordt het toegangspakket weergegeven als niet volledig geleverd totdat het account van de gebruiker in SharePoint Online is ingericht. Zie [uw externe share point Online-instellingen voor delen controleren](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)voor meer informatie over het delen van instellingen.

## <a name="requests"></a>Aanvragen

* Wanneer een gebruiker de toegang tot een toegangs pakket wil aanvragen, moet u ervoor zorgen dat ze de **Portal-koppeling van mijn toegang** gebruiken voor het toegangs pakket. Zie [koppeling delen om een toegangs pakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie.

* Als u de portal Mijn toegang opent met de browser in privé- of incognitomodus, kan dit conflicteren met het aanmeldingsgedrag. U wordt aangeraden geen in-privé-of incognito-modus voor uw browser te gebruiken wanneer u de portal Mijn toegang bezoekt.

* Wanneer een gebruiker die zich nog niet in uw directory bevindt zich aanmeldt bij de portal Mijn toegang om een toegangspakket aan te vragen, moet deze zich authenticeren met zijn/haar organisatieaccount. Het organisatieaccount kan een account in de resourcedirectory zijn, of in een directory die is opgenomen in een van de beleidsregels van het toegangspakket. Als het account van de gebruiker geen organisatieaccount is, of de directory waar deze zich authenticeert niet is opgenomen in het beleid, kan de gebruiker het toegangspakket niet zien. Zie [toegang tot een toegangs pakket aanvragen](entitlement-management-request-access.md)voor meer informatie.

* Als een gebruiker zich niet kan aanmelden bij de resourcedirectory, kan deze geen toegang aanvragen in de portal Mijn toegang. Voordat de gebruiker toegang kan aanvragen, moet u de aanmeldingsblokkering verwijderen uit het profiel van de gebruiker. Als u het aanmeldings blok wilt verwijderen, klikt u in het Azure Portal op **Azure Active Directory**, klikt u op **gebruikers**, klikt u op de gebruiker en klikt u vervolgens op **profiel**. Bewerk de sectie **instellingen** en wijzig de **blok aanmelding in** op **Nee**. Zie [de profiel gegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)voor meer informatie.  U kunt ook controleren of de gebruiker is geblokkeerd vanwege een [beleid voor identiteits beveiliging](../identity-protection/howto-identity-protection-remediate-unblock.md).

* Als een gebruiker zowel een aanvrager als een fiatteur is, wordt in de portal mijn toegang de aanvraag voor een toegangs pakket niet weer geven op de pagina **goed keuringen** . Dit gedrag is opzettelijk: een gebruiker kan zijn/haar eigen aanvraag niet goedkeuren. Zorg ervoor dat er meerdere fiatteurs zijn geconfigureerd in het beleid voor het toegangspakket dat de gebruiker aanvraagt. Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket](entitlement-management-access-package-request-policy.md)voor meer informatie.

### <a name="view-a-requests-delivery-errors"></a>De leverings fouten van een aanvraag weer geven

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar, toegangs pakket beheer of toegangs pakket toewijzings beheer

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Selecteer de aanvraag die u wilt weer geven.

    Als de aanvraag een leverings fout bevat, wordt de status van de aanvraag niet **bezorgd** of **gedeeltelijk bezorgd**.

    Als er bezorgings fouten zijn, wordt er een telling van de bezorgings fouten weer gegeven in het detail venster van de aanvraag.

1. Klik op het aantal om alle bezorgings fouten van de aanvraag weer te geven.

### <a name="reprocess-a-request"></a>Een aanvraag opnieuw verwerken

Als er een fout is opgetreden na het activeren van een aanvraag voor het opnieuw verwerken van een toegangs pakket, moet u wachten tot de aanvraag opnieuw wordt verwerkt door het systeem. Het systeem probeert gedurende een aantal uur meerdere keren opnieuw te verwerken, zodat u de herverwerking tijdens deze periode niet kunt afdwingen. 

U kunt alleen een aanvraag met de status **bezorgd** of **gedeeltelijk** opnieuw verwerken, en een voltooide datum van minder dan een week. De knop opnieuw **verwerken** zou anders grijs worden weer gegeven.

![Knop opnieuw verwerken grijs weer gegeven](./media/entitlement-management-troubleshoot/cancel-reprocess-grayedout.png)

- Als de fout is opgelost tijdens het venster trials, wordt de status van de aanvraag gewijzigd in **bezorgd**. De aanvraag wordt opnieuw verwerkt zonder aanvullende acties van de gebruiker.

- Als de fout **niet is** opgelost tijdens het venster experimenten, kan de status van de aanvraag mogelijk niet worden **bezorgd of gedeeltelijk geleverd**. Vervolgens kunt u de knop opnieuw **verwerken** gebruiken. U hebt zeven dagen nodig om de aanvraag opnieuw te verwerken.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar, toegangs pakket beheer of toegangs pakket toewijzings beheer

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op de aanvraag die u opnieuw wilt verwerken.

1. Klik in het deel venster aanvraag Details op **aanvraag opnieuw verwerken**.

    ![Een mislukte aanvraag opnieuw verwerken](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

U kunt een aanvraag die in behandeling is, alleen annuleren als deze nog niet is bezorgd of waarvoor de bezorging is mislukt. De knop **Annuleren** wordt anders grijs weer gegeven.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar, toegangs pakket beheer of toegangs pakket toewijzings beheer

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op de aanvraag die u wilt annuleren.

1. Klik in het deel venster aanvraag Details op **Aanvraag annuleren**.

## <a name="multiple-policies"></a>Meerdere beleids regels

* Voor het beheer van rechten worden de best practices voor de minimale bevoegdheid gevolgd. Wanneer een gebruiker toegang vraagt tot een toegangs pakket dat meerdere beleids regels heeft, is het beheer van rechten de logica om ervoor te zorgen dat strengere of meer specifieke beleids regels worden toegepast via algemeen beleid. Als een beleid algemeen is, kan het beheer van rechten het beleid niet weer geven voor de aanvrager of kan er automatisch een strikter beleid worden geselecteerd.

* Denk bijvoorbeeld aan een toegangs pakket met twee beleids regels voor interne werk nemers waarin beide beleids regels van toepassing zijn op de aanvrager. Het eerste beleid is voor specifieke gebruikers die de aanvrager bevatten. Het tweede beleid geldt voor alle gebruikers in een map waarvan de aanvrager lid is. In dit scenario wordt het eerste beleid automatisch geselecteerd voor de aanvrager omdat het strikter is. De aanvrager krijgt niet de optie om het tweede beleid te selecteren.

* Als er meerdere beleids regels van toepassing zijn, is het beleid dat automatisch wordt geselecteerd of het beleid dat wordt weer gegeven voor de aanvrager gebaseerd op de volgende prioriteits logica:

    | Beleids prioriteit | Bereik |
    | --- | --- |
    | P1 | Specifieke gebruikers en groepen in uw Directory of specifieke verbonden organisaties |
    | P2 | Alle leden in uw directory (exclusief gasten) |
    | P3 | Alle gebruikers in uw directory (inclusief gasten) of specifieke verbonden organisaties |
    | P4 | Alle geconfigureerde verbonden organisaties of alle gebruikers (alle verbonden organisaties en alle nieuwe externe gebruikers) |
    
    Als een beleid zich in een categorie met hogere prioriteit bevindt, worden de categorieën met lagere prioriteit genegeerd. Zie [een beleid selecteren](entitlement-management-request-access.md#select-a-policy)voor een voor beeld van hoe er meerdere beleids regels met dezelfde prioriteit worden weer gegeven voor de aanvrager.

## <a name="next-steps"></a>Volgende stappen

- [Toegang voor externe gebruikers beheren](entitlement-management-external-users.md)
- [Rapporten weer geven over hoe gebruikers toegang krijgen in het recht beheer](entitlement-management-reports.md)