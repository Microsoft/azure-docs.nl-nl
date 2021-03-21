---
title: Een beveiligings plan voor externe toegang tot Azure Active Directory maken
description: Plan de beveiliging voor externe toegang tot de resources van uw organisatie...
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266142240ba9e892c905ac8aa6521da5a14c4c3d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554017"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. een beveiligings plan voor externe toegang maken 

Nu u hebt vastgesteld dat u [de gewenste beveiligings-postuur voor de beveiliging hebt ingesteld voor externe toegang](1-secure-access-posture.md) en [de huidige samenwerkings status hebt gedetecteerd](2-secure-access-current-state.md), kunt u een extern gebruikers beveiligings-en beheer plan maken. 

In dit plan wordt het volgende gedocumenteerd:

* De toepassingen en andere resources die moeten worden gegroepeerd voor toegang.

* De juiste aanmeldings voorwaarden voor externe gebruikers. Dit kunnen de apparaatstatus, aanmeldings locatie, client toepassings vereisten en gebruikers risico zijn.

* Bedrijfs beleid op wanneer u de toegang wilt controleren en verwijderen. 

* Gebruikers populaties worden gegroepeerd en behandeld op dezelfde manier.

Zodra deze gebieden zijn gedocumenteerd, kunt u het beleid voor identiteits-en toegangs beheer van micro soft of een andere ID-provider (IdP) gebruiken voor het implementeren van dit abonnement.

## <a name="document-resources-to-be-grouped-for-access"></a>Document bronnen die moeten worden gegroepeerd voor toegang

Er zijn meerdere manieren om resources te groeperen voor toegang. 

* Micro soft teams groeperen bestanden, conversatie-threads en andere bronnen op één plek. U moet een strategie voor externe toegang voor micro soft-teams formuleren. Zie [beveiligde toegang tot teams, OneDrive en share point](9-secure-access-teams-sharepoint.md).

* Met rechten beheer toegangs pakketten kunt u één pakket met toepassingen en andere resources maken waaraan u toegang wilt verlenen. 

* Beleid voor voorwaardelijke toegang kan worden toegepast op Maxi maal 250 toepassingen met dezelfde toegangs vereisten.

U beheert de toegang echter, u moet documenteren welke toepassingen moeten worden gegroepeerd. Overwegingen moeten het volgende omvatten:

* **Risico profiel**. Wat is het risico voor uw bedrijf als een ongeldige actor toegang heeft verkregen tot een toepassing? Overweeg elke toepassing te coderen als hoog, gemiddeld of laag risico. Wees voorzichtig met het groeperen van toepassingen met een hoog risico met een laag risico. 

   * Documenteer toepassingen die nooit ook moeten worden gedeeld met externe gebruikers.

* **Nalevings raamwerken**. Wat gebeurt er als aan een nalevings raamwerk een toepassing moet worden voldaan? Wat zijn de vereisten voor toegang en beoordeling?

* **Toepassingen voor specifieke taak rollen of afdelingen**. Zijn er toepassingen die moeten worden gegroepeerd, omdat alle gebruikers in een specifieke functie of afdeling toegang moeten hebben?

* **Toepassingen die gericht zijn op samenwerkings** verband. Welke toepassingen die geschikt zijn voor samen werking, moeten toegang hebben tot externe gebruikers? Micro soft teams en share point moeten mogelijk toegankelijk zijn. Voor productiviteits toepassingen in Office 365, zoals Word en Excel, zullen externe gebruikers hun eigen licenties gebruiken of moeten ze een licentie geven en toegang verlenen?

Voor elke groep toepassingen en resources die u toegankelijk wilt maken voor externe gebruikers, documenteert u het volgende:

* Een beschrijvende naam voor de groep, bijvoorbeeld *High_Risk_External_Access_Finance*. 

* Volledige lijst met alle toepassingen en resources in de groep.

* Toepassings-en resource-eigen aren en contact gegevens.

* Of de toegang wordt bepaald door de gebruiker of overgedragen aan de eigenaar van het bedrijf.

* Alle vereisten, bijvoorbeeld het volt ooien van een achtergrond controle of een training, voor toegang.

* Alle nalevings vereisten voor toegang tot de resources.

* Eventuele extra uitdagingen, bijvoorbeeld het vereisen van multi-factor Authentication voor specifieke resources.

* Hoe vaak de toegang wordt gecontroleerd, door wie en waar deze wordt gedocumenteerd.

Dit type governance-plan kan en ook worden uitgevoerd voor interne toegang.

## <a name="document-sign-in-conditions-for-external-users"></a>Aanmeldings voorwaarden voor documenten voor externe gebruikers.

Als onderdeel van uw plan moet u de aanmeldings vereisten voor uw externe gebruikers bepalen wanneer ze toegang krijgen tot resources. Aanmeldings vereisten zijn vaak gebaseerd op het risico profiel van de resources en de risico beoordeling van de aanmelding van de gebruiker.

Aanmeldings voorwaarden worden geconfigureerd in [voorwaardelijke toegang voor Azure AD](../conditional-access/overview.md) en bestaan uit een voor waarde en een resultaat. Wanneer bijvoorbeeld multi-factor Authentication vereist is

**Voor waarden voor aanmelden op basis van een resource risico.**

| Toepassings risico profiel| Dit beleid overwegen voor het activeren van multi-factor Authentication |
| - |-|
| Laag risico| MFA vereisen voor specifieke toepassings sets |
| Med-risico| MFA vereisen wanneer andere Risico's aanwezig zijn |
| Hoog risico| MFA altijd vereisen voor externe gebruikers |


U kunt nu [multi-factor Authentication afdwingen voor B2B-gebruikers in uw Tenant](../external-identities/b2b-tutorial-require-mfa.md). 

**Aanmeldings voorwaarden op basis van gebruikers en apparaten**.

| Gebruiker of aanmeldings risico| Dit beleid overwegen |
| - | - |
| Apparaat| Compatibele apparaten vereisen |
| Mobiele apps| Goedgekeurde apps vereisen |
| Identiteits beveiliging toont een hoog risico| Gebruiker moet wacht woord wijzigen |
| Netwerk locatie| Aanmelden van een specifiek IP-adres bereik vereisen voor zeer vertrouwelijke projecten |

Vandaag moet het apparaat worden geregistreerd of gekoppeld aan uw Tenant om de apparaatstatus te gebruiken als invoer voor een beleid. 

[Beleid voor risico op basis van identiteits beveiliging](../conditional-access/howto-conditional-access-policy-risk.md) kan worden gebruikt. Problemen moeten echter worden verholpen in de thuis Tenant van de gebruiker.

Voor [netwerk locaties](../conditional-access/howto-conditional-access-policy-location.md)kunt u de toegang beperken tot elk IP-adres bereik waarvan u eigenaar bent. U kunt dit bijvoorbeeld gebruiken als u alleen externe partners toegang tot een toepassing wilt geven wanneer deze zich op de site van uw organisatie bevinden.

[Meer informatie over beleids regels voor voorwaardelijke toegang](../conditional-access/overview.md).

## <a name="document-access-review-policies"></a>Toegangs beoordelings beleid documenteren

Documenteer uw bedrijfs beleid voor wanneer u de toegang tot resources moet controleren en wanneer u account toegang voor externe gebruikers wilt verwijderen. De invoer van deze beslissingen kan het volgende omvatten:

* Vereisten die worden beschreven in alle nalevings kaders.

* Intern bedrijfs beleid en-processen

* Gebruikers gedrag

Houd rekening met het volgende wanneer uw beleids regels zeer worden aangepast aan uw behoeften:

* **Toegangs Beoordelingen voor rechten beheer**. Gebruik de functionaliteit in het recht beheer om

   * De [toegangs pakketten automatisch laten verlopen](../governance/entitlement-management-access-package-lifecycle-policy.md)en dus externe gebruikers hebben toegang tot de Inge sloten resources.

   * Stel een [vereiste beoordelings frequentie](../governance/entitlement-management-access-reviews-create.md) in voor toegangs Beoordelingen.

   * Als u [verbonden organisaties](../governance/entitlement-management-organization.md) gebruikt voor het groeperen van alle gebruikers van één partner, moet u regel matig beoordelingen plannen met de eigenaar van het bedrijf en de vertegenwoordiger van de partner.

* **Microsoft 365 groepen**. Stel een [verloop beleid voor groepen](/microsoft-365/solutions/microsoft-365-groups-expiration-policy) in voor Microsoft 365 groepen waaraan externe gebruikers worden uitgenodigd. 

* **Andere opties**. Als externe gebruikers toegang hebben tot buiten rechten beheer toegangs pakketten of Microsoft 365 groepen, stelt u bedrijfs processen in om te controleren wanneer accounts inactief moeten worden gemaakt of verwijderd. Bijvoorbeeld:

   * Verwijder aanmeldings mogelijkheden voor een account dat gedurende 90 dagen niet is aangemeld.

   * Bepaal de toegangs behoeften en onderneem aan het einde van elk project met externe gebruikers actie.

 

## <a name="determine-your-access-control-methods"></a>Uw methode voor toegangs beheer bepalen

Nu u weet wat u wilt beheren van de toegang tot, hoe deze activa moeten worden gegroepeerd voor algemene toegang en de vereiste aanmeldings-en toegangs beoordelings beleid, kunt u bepalen hoe u uw plan moet uitvoeren. 

Sommige functies, bijvoorbeeld het [beheer van rechten](../governance/entitlement-management-overview.md), zijn alleen beschikbaar met een P2-licentie (Azure AD Premium 2). Microsoft 365 E5 en Office 365 E5-licenties zijn Azure AD P2-licenties. 

Andere Combi Naties van Microsoft 365, Office 365 en Azure AD bieden ook een aantal functies voor het beheren van externe gebruikers. Zie [Information Protection](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) voor meer informatie.

> [!NOTE]
> Licenties zijn per gebruiker. Daarom kunt u specifieke gebruikers, waaronder beheerders en zakelijke eigen aren, gedelegeerde toegangs beheer, op het niveau van Azure AD P2 of Microsoft 365 E5, zonder dat u deze licenties voor alle gebruikers hoeft in te scha kelen. Uw eerste 50.000 externe gebruikers zijn gratis. Als u P2-licenties niet inschakelt voor uw andere interne gebruikers, kunnen ze geen rechten voor beheer gebruiken zoals toegangs pakketten. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Beheersen toegang met Azure AD P2 en micro soft/Office 365 E5
Azure AD P2 en Microsoft 365 E5 hebben de volledige suite met hulpprogram ma's voor beveiliging en beheer.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Inrichting, aanmelden, controleren van toegang en ongedaan maken van de inrichting. Vetgedrukte vermeldingen hebben de voorkeurs methoden

| Functie| Externe gebruikers inrichten| Aanmeldings reqs afdwingen.| Toegang beoordelen| Toegang ongedaan maken |
| - | - | - | - | - |
| Azure AD B2B-samen werking| Uitnodigen via e-mail, OTP, self-service| | **Periodieke controle per partner**| Account verwijderen<br>Aanmelden beperken |
| Beheer rechten| **Gebruiker toevoegen via toewijzing of selfservice toegang**| | Toegangsbeoordelingen|**De verloopt of het verwijderen van het toegangs pakket is verlopen**|
| Office 365 Groups| | | Groepslid maatschappen controleren| Verval datum of verwijdering van groep<br> Formulier groep verwijderen |
| Azure AD-beveiligingsgroepen| | **Beleid voor voorwaardelijke toegang** (indien nodig externe gebruikers toevoegen aan beveiligings groepen)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Toegang tot resources. Vetgedrukte vermeldingen hebben de voorkeurs methoden

|Functie | Toegang tot APP & resource| Share point-& toegang tot OneDrive| Teams toegang| Beveiliging e-mail & document |
| - |-|-|-|-|
| Beheer rechten| **Gebruiker toevoegen via toewijzing of selfservice toegang**| **Toegangspakketten**| **Toegangspakketten**|  |
| Office 365-groep| | Toegang tot site (s) (en bijbehorende inhoud) die zijn opgenomen in de groep| Toegang tot teams (en bijbehorende inhoud) die zijn opgenomen in de groep|  |
| Vertrouwelijkheidslabels| | **De toegang hand matig en automatisch classificeren en beperken**| **De toegang hand matig en automatisch classificeren en beperken**| **De toegang hand matig en automatisch classificeren en beperken** |
| Azure AD-beveiligingsgroepen| **Beleid voor voorwaardelijke toegang voor toegang die niet is opgenomen in toegangs pakketten**| | |  |


### <a name="entitlement-management"></a>Beheer rechten 

[Toegangs pakketten voor rechten beheer](../governance/entitlement-management-access-package-create.md) bieden het inrichten en ongedaan maken van de inrichting van toegang tot groepen en teams, toepassingen en share point-sites. U kunt definiëren welke verbonden organisaties toegang mogen hebben, of selfservice aanvragen zijn toegestaan en welke goedkeurings werk stromen zijn vereist (indien van toepassing) om toegang te verlenen. Om ervoor te zorgen dat de toegang niet langer dan nodig is, kunt u een verloop beleid en toegangs Beoordelingen voor elk toegangs pakket definiëren. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Toegang beheren met Azure AD P1 en micro soft/Office 365 E3
U kunt robuuste governance krijgen met Azure AD P1 en Microsoft 365 E3

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Inrichting, aanmelden, controleren van toegang en ongedaan maken van inrichting


|Functie | Externe gebruikers inrichten| Aanmeldings vereisten afdwingen| Toegang beoordelen| Toegang ongedaan maken |
| - |-|-|-|-|
| Azure AD B2B-samen werking| **Uitnodigen via e-mail, OTP, self-service**| Direct B2B-Federatie| **Periodieke controle per partner**| Account verwijderen<br>Aanmelden beperken |
| Micro soft-of Office 365-groepen| | | | Verval datum of verwijdering van de groep.<br>Verwijderen uit de groep. |
| Beveiligingsgroepen| | **Externe gebruikers toevoegen aan beveiligings groepen (org, team, project, enz.)**| |  |
| Beleid voor voorwaardelijke toegang| | **Beleid voor voorwaardelijke toegang aanmelden voor externe gebruikers**| |  |


 ### <a name="access-to-resources"></a>Toegang tot resources.

|Functie | Toegang tot APP & resource| Share point-& toegang tot OneDrive| Teams toegang| Beveiliging e-mail & document |
| - |-|-|-|-|
| Micro soft-of Office 365-groepen| | **Toegang tot de site (s) die zijn opgenomen in de groep (en de gekoppelde inhoud)**|**Toegang tot teams die deel uitmaken van Microsoft 365 groep (en gekoppelde inhoud)**|  |
| Vertrouwelijkheidslabels| | De toegang hand matig classificeren en beperken| De toegang hand matig classificeren en beperken.| Hand matig classificeren om te beperken en te versleutelen |
| Beleid voor voorwaardelijke toegang| Beleid voor voorwaardelijke toegang voor toegangs beheer| | |  |
| Aanvullende methoden| | Beperk de toegang tot share point-sites met beveiligings groepen.<br>Direct delen niet toestaan.| **Externe uitnodigingen in teams beperken**|  |


### <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Uw beveiligings postuur voor externe toegang bepalen](1-secure-access-posture.md)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governance-abonnement maken](3-secure-access-plan.md) (u bent hier.)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Veilige toegang met het rechten beheer](6-secure-access-entitlement-managment.md)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Veilige toegang met gevoeligheids labels](8-secure-access-sensitivity-labels.md)

9. [Beveiligde toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md)