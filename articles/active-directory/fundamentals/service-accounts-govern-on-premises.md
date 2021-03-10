---
title: Over on-premises service accounts | Azure Active Directory
description: Een hand leiding voor het maken en uitvoeren van een account levenscyclus proces voor service accounts
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ad7cf7fe2ca1ddcb592e895014b1d956e55e1b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557366"
---
# <a name="governing-on-premises-service-accounts"></a>On-premises service accounts beheren

Er zijn vier typen on-premises service accounts in Windows Active Directory:

* Door [groep beheerde service accounts](service-accounts-group-managed.md) (gmsa's)

* [zelfstandige beheerde service accounts](service-accounts-standalone-managed.md) (sMSAs)

* [Computer accounts](service-accounts-computer.md)

* [Gebruikers accounts functioneren als service accounts](service-accounts-user-on-premises.md)


Het is essentieel om service accounts nauw keurig te beheren: 

* Beveilig service accounts op basis van hun gebruiks Case vereisten en-doel.

* De levens cyclus van service accounts en hun referenties beheren.

* Service accounts beoordelen op basis van het risico dat ze worden blootgesteld aan en de machtigingen die ze hebben, 

* Zorg ervoor dat Active Directory en Azure Active Directory geen verlopen service accounts hebben die mogelijk te veel machtigingen bereiken.

## <a name="principles-for-creating-a-new-service-account"></a>Principes voor het maken van een nieuw service account

Gebruik de volgende criteria bij het maken van een nieuw service account.

| Principes| Overwegingen | 
| - |- | 
| Toewijzing van service accounts| Het service account koppelen aan één service, toepassing of script. |
| Eigendom| Zorg ervoor dat er een eigenaar is die de verantwoordelijkheid voor het account aanvraagt en afneemt. |
| Bereik| Definieer het bereik duidelijk en verwachte gebruiks duur voor het service account. |
| Doel| Maak service accounts voor één specifiek doel. |
| Bevoegdheid| Pas het principe van minimale bevoegdheden toe door: <br>Wijs ze nooit toe aan ingebouwde groepen zoals beheerders.<br> Lokale machine bevoegdheden verwijderen, indien van toepassing.<br>Het aanpassen van de toegang tot en het gebruik van Active Directory overdracht voor Directory toegang.<br>Nauw keurige toegangs machtigingen gebruiken.<br>Account verloop en op locatie gebaseerde beperkingen instellen voor service accounts op basis van gebruikers |
| Gebruik controleren en controleren| Bewaak de aanmeldings gegevens en zorg ervoor dat deze overeenkomt met het beoogde gebruik. Stel waarschuwingen in voor afwijkend gebruik. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Minimale bevoegdheden afdwingen voor gebruikers accounts en overmatige account beperken

Gebruik de volgende instellingen met gebruikers accounts die worden gebruikt als service accounts:

* [**Verlopen account**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps): Stel het service account in om automatisch een ingestelde tijd te laten verlopen na de beoordelings periode, tenzij wordt vastgesteld dat deze moet door gaan

*  **LogonWorkstations**: machtigingen beperken voor waar het service account zich kan aanmelden. Als het lokaal op een computer wordt uitgevoerd en alleen bronnen op die computer worden geopend, moet u de logboek registratie op elke wille keurige locatie anders uitvoeren.

* [**Kan het wacht woord niet wijzigen**](/powershell/module/addsadministration/set-aduser): voor komen dat het service account een eigen wacht woord wijzigt door de para meter in te stellen op false.

 
## <a name="build-a-lifecycle-management-process"></a>Een levenscyclus beheer proces bouwen

Als u de beveiliging van uw service accounts wilt behouden, moet u ze beheren vanaf het moment dat u de nood zaak identificeert totdat ze buiten gebruik worden gesteld. 

Gebruik het volgende proces voor het levenscyclus beheer van service accounts:

1. Gebruiks gegevens verzamelen voor het account
1. Het service account en de app aan de configuratie beheer database (CMDB) voorbereiden
1. Risico analyse of formele beoordeling uitvoeren
1. Maak het service account en pas beperkingen toe.
1. Plan en voer terugkerende beoordelingen uit. Pas de machtigingen en bereiken zo nodig aan.
1. De inrichting van het account ongedaan maken wanneer dat nodig is.

### <a name="collect-usage-information-for-the-service-account"></a>Gebruiks gegevens verzamelen voor het service account

Verzamel de relevante bedrijfs gegevens voor elk Service account. In de onderstaande tabel ziet u de minimale gegevens die moeten worden verzameld, maar u moet alles verzamelen voor het maken van het bedrijfs probleem voor het bestaan van de accounts.

| Gegevens| Details |
| - | - |
| Eigenaar| Gebruiker of groep die verantwoordelijk is voor het service account |
| Doel| Doel van het service account |
| Machtigingen (bereiken)| Verwachte set machtigingen |
| Configuration Management data base (CMDB)-koppelingen| Service account kruislings koppelen met doel script/toepassing en eigenaar (s) |
| Risico| Beoordeling van Risico's en bedrijfs impact op basis van een beveiligings risico analyse |
| Dood| Verwachte maximale levens duur voor het plannen van de verval datum of hercertificering van het account |


 

In het ideale geval voert u de aanvraag voor een account self-service in en hebt u de relevante gegevens nodig. De eigenaar, die een toepassings-of bedrijfs eigenaar, een IT-lid of een eigenaar van een infra structuur kan zijn. Met een hulp programma zoals micro soft Forms voor deze aanvraag en de bijbehorende informatie kunt u het eenvoudig overbrengen naar uw CMDB-inventarisatie programma als het account wordt goedgekeurd.

### <a name="onboard-service-account-to-cmdb"></a>Onboard service account naar CMDB

Sla de verzamelde informatie op in een toepassing van het CMDB-type. Naast de Bedrijfs gegevens, moet u alle afhankelijkheden toevoegen aan andere infra structuur, apps en processen.  Met deze centrale opslag plaats kunt u het volgende vereenvoudigen:

* Risico's beoordelen.

* Configureer het service account met de vereiste beperkingen.

* Informatie over relevante functionele en beveiligings afhankelijkheden.

* Voer regel matige beoordelingen uit voor beveiliging en permanente behoeften.

* Neem contact op met de eigenaar (s) voor het controleren, buiten gebruik stellen en wijzigen van het service account.

Overweeg een service account dat wordt gebruikt voor het uitvoeren van een website en bevoegdheden heeft om verbinding te maken met een of meer SQL-data bases. Gegevens die zijn opgeslagen in uw CMDB voor dit service account, kunnen zijn:

|Gegevens | Details|
| - | - |
| Eigenaar, adjunct| John bloei, Anna Mayers |
| Doel| Voer de HR-webpagina uit en maak verbinding met HR-data bases. Kan eind gebruiker imiteren bij het openen van data bases. |
| Machtigingen, bereiken| HR-webserver: lokaal aanmelden, webpagina uitvoeren<br>HR-SQL1: lokaal aanmelden, lezen op alle HR *-data base<br>HR-SQL2: lokaal aanmelden, lezen op salaris * data base |
| Cost Center| 883944 |
| Risico beoordeeld| Drager Bedrijfs impact: gemiddeld; persoonlijke informatie; Drager |
| Account beperkingen| Meld u aan bij: alleen bovengenoemde servers; Kan het wacht woord niet wijzigen. MBI-Password beleid; |
| Dood| onbeperkte |
| Beoordelings cyclus| Bi-jaarlijks (op eigenaar, per beveiligings team, op privacy) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Risico analyse of formele beoordeling van het gebruik van het service account uitvoeren

Gezien de machtigingen en het doel, beoordelen we het risico dat het account kan worden gekoppeld aan de bijbehorende toepassing of service en aan uw infra structuur als deze is aangetast. Houd rekening met zowel direct als indirect risico. 

* Hoe krijgt een kwaadwillende persoon direct toegang tot?

* Welke andere informatie of systemen kan het service account gebruiken?

* Kan het account worden gebruikt voor het verlenen van extra machtigingen?

* Hoe weet u wanneer de machtigingen worden gewijzigd?

De risico beoordeling, nadat deze is uitgevoerd en gedocumenteerd, kan gevolgen hebben voor:

* Account beperkingen

* Levens duur van account

* Vereisten voor account beoordeling (uitgebracht en revisoren)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Een service account maken en account beperkingen Toep assen

U kunt alleen een service account maken als de relevante informatie wordt gedocumenteerd in uw CMDB en u een risico analyse uitvoert. Account beperkingen moeten worden afgestemd op de risico analyse. Houd rekening met de volgende beperkingen wanneer dit van toepassing is op uw beoordeling.:

* [Verlopen van account](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * Voor alle gebruikers accounts die worden gebruikt als service accounts, definieert u een realistische en eind datum voor gebruik. Stel dit in met de vlag ' account Expires '. Raadpleeg[ set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration)voor meer informatie. 

* Aanmelden bij ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* Vereisten voor [wachtwoord beleid](../../active-directory-domain-services/password-policy.md)

* Maken op een [OE-locatie](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) waarmee beheer alleen voor bevoegde gebruikers wordt gegarandeerd

* Het instellen en verzamelen van controle die wijzigingen in het service account en het [Service account-gebruik](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html) [detecteert](/windows/security/threat-protection/auditing/audit-directory-service-changes) .

Wanneer u klaar bent om in productie te worden gebracht, moet u het service account veilig verlenen. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Periodieke beoordelingen van service accounts plannen

Stel reguliere beoordelingen in van service accounts die zijn geclassificeerd als gemiddeld en hoog risico. De beoordelingen moeten het volgende omvatten: 

* Eigendoms verklaring van de permanente behoefte aan het account en de motivering van bevoegdheden en bereiken.

* Bekijk de privacy-en beveiligings teams, inclusief de evaluatie van upstream-en downstream-verbindingen.

* Gegevens van audits die ervoor zorgen dat deze alleen voor beoogde doel einden worden gebruikt

### <a name="deprovision-service-accounts"></a>Inrichting van service accounts ongedaan maken

In uw opvolgings proces verwijdert u eerst machtigingen en controle en verwijdert u vervolgens het account, indien van toepassing.

Inrichting van service accounts ongedaan maken wanneer:

* Het script of de toepassing waarvoor het service account is gemaakt, wordt buiten gebruik gesteld.

* De functie binnen het script of de toepassing, waarvoor het service account wordt gebruikt (bijvoorbeeld toegang tot een specifieke resource), wordt buiten gebruik gesteld.

* Het service account is vervangen door een ander service account.

Nadat u alle machtigingen hebt verwijderd, gebruikt u dit proces voor het verwijderen van het account.

1. Zodra de inrichting van de gekoppelde toepassing of het script is opheffen, controleert u de aanmeldingen en de toegang tot de resource voor de gekoppelde service accounts om te controleren of deze niet in een ander proces worden gebruikt. Als u zeker weet dat deze niet meer nodig is, gaat u naar de volgende stap.

2. Schakel de service account uit om u aan te melden en zorg ervoor dat deze niet meer nodig is. Het maken van een bedrijfs beleid voor de tijd accounts moet uitgeschakeld blijven.

3. Verwijder het service account nadat het beleid voor uitgeschakeld is afgehandeld. 

   * Voor Msa's kunt u [deze verwijderen](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) met behulp van Power shell of hand matig verwijderen uit de container van een beheerde service account.

   * Voor computer-of gebruikers accounts kunt u het account hand matig verwijderen uit in Active Directory.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen over het beveiligen van service accounts

* [Inleiding tot on-premises service accounts](service-accounts-on-premises.md)

* [Door groep beheerde service accounts beveiligen](service-accounts-group-managed.md)

* [Zelfstandige beheerde service accounts beveiligen](service-accounts-standalone-managed.md)

* [Computer accounts beveiligen](service-accounts-computer.md)

* [Gebruikers accounts beveiligen](service-accounts-user-on-premises.md)

* [On-premises service accounts beheren](service-accounts-govern-on-premises.md)