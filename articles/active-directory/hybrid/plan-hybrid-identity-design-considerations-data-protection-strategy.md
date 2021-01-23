---
title: Hybride identiteits ontwerp-strategie voor gegevens beveiliging Azure | Microsoft Docs
description: U definieert de strategie voor gegevens beveiliging voor uw hybride identiteits oplossing om te voldoen aan de bedrijfs vereisten die u hebt gedefinieerd.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bac3f53def6db1038a6dd7e45d7933daa22df9f0
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703849"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Strategie voor gegevens beveiliging definiëren voor uw hybride identiteits oplossing
In deze taak definieert u de strategie voor gegevens beveiliging voor uw hybride identiteits oplossing om te voldoen aan de bedrijfs vereisten die u hebt gedefinieerd in:

* [Vereisten voor gegevens beveiliging bepalen](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Vereisten voor inhoudsbeheer vaststellen](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Vereisten voor toegangsbeheer vaststellen](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Vereisten voor reageren op incidenten vaststellen](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Opties voor gegevens beveiliging definiëren
Zoals wordt uitgelegd in de [vereisten voor adreslijst synchronisatie bepalen](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD kunnen synchroniseren met uw on-premises Active Directory Domain Services (AD DS). Met deze integratie kunnen organisaties Azure AD gebruiken om referenties van gebruikers te verifiëren wanneer ze proberen toegang te krijgen tot bedrijfs bronnen. U kunt dit voor beide scenario's doen: gegevens in rest on-premises en in de Cloud. Toegang tot gegevens in azure AD vereist verificatie van de gebruiker via een beveiligings token service (STS).

Na verificatie wordt de user principal name (UPN) gelezen uit het verificatie token. Vervolgens bepaalt het autorisatie systeem de gerepliceerde partitie en container die overeenkomt met het domein van de gebruiker. Informatie over de aanwezigheid van de gebruiker, de status ingeschakeld en de rol helpt het autorisatie systeem vervolgens te bepalen of de toegang tot de doel-Tenant is geautoriseerd voor de gebruiker in die sessie. Bepaalde goedgekeurde acties (met name voor het maken van gebruikers-en wachtwoord herstel) een audittrail maken die door een Tenant beheerder wordt gebruikt om de nalevings inspanningen of onderzoek te beheren.

Het verplaatsen van gegevens van uw on-premises Data Center naar Azure Storage via een Internet verbinding is mogelijk niet altijd haalbaar vanwege gegevens volume, band breedte Beschik baarheid of andere overwegingen. De [Azure Storage import/export-service](../../import-export/storage-import-export-service.md) biedt een optie op basis van hardware voor het plaatsen/ophalen van grote hoeveel heden gegevens in Blob Storage. Hiermee kunt u [BitLocker-versleutelde](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn306081(v=ws.11)#BKMK_BL2012R2) harde schijven rechtstreeks naar een Azure-Data Center verzenden, waar Cloud operators de inhoud naar uw opslag account uploaden, of ze kunnen uw Azure-gegevens naar uw stations downloaden om naar u terug te gaan. Alleen versleutelde schijven worden voor dit proces geaccepteerd (met behulp van een BitLocker-sleutel die door de service zelf wordt gegenereerd tijdens de installatie van de taak). De BitLocker-sleutel wordt afzonderlijk aan Azure verstrekt, waardoor het delen van de band-sleutel wordt verzorgd.

Omdat gegevens in transit in verschillende scenario's kunnen plaatsvinden, is ook relevant om te weten dat Microsoft Azure gebruik maakt van [virtuele netwerken](https://azure.microsoft.com/documentation/services/virtual-network/) om het verkeer van tenants van elkaar te isoleren, waarbij metingen zoals host-en gast niveau firewalls, het filteren van IP-pakketten, poort blokkering en HTTPS-eind punten worden gebruikt. De meeste interne communicatie van Azure, met inbegrip van de infra structuur-naar-infra structuur en de infra structuur naar de klant (on-premises), wordt echter ook versleuteld. Een ander belang rijk scenario is de communicatie binnen Azure-data centers. Micro soft beheert netwerken om ervoor te zorgen dat er geen virtuele machine kan worden geïmiteerd of Eavesdrop op het IP-adres van een andere. TLS/SSL wordt gebruikt bij het openen van Azure Storage-of SQL-data bases, of bij het maken van verbinding met Cloud Services. In dit geval is de beheerder van de klant verantwoordelijk voor het verkrijgen van een TLS/SSL-certificaat en het implementeren ervan in de Tenant-infra structuur. Gegevens verkeer dat tussen Virtual Machines in dezelfde implementatie of tussen tenants in één implementatie via Microsoft Azure Virtual Network wordt verplaatst, kan worden beveiligd via versleutelde communicatie protocollen zoals HTTPS, SSL/TLS of anderen.

Afhankelijk van hoe u de vragen in de [vereisten voor gegevens beveiliging](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)hebt beantwoord, moet u kunnen bepalen hoe u uw gegevens wilt beveiligen en hoe de hybride identiteits oplossing u kan helpen bij dat proces. In de volgende tabel ziet u de opties die worden ondersteund door Azure en die beschikbaar zijn voor elk scenario voor gegevens bescherming.

| Opties voor gegevens beveiliging | In rust in de Cloud | On-premises rest | In-transit |
| --- | --- | --- | --- |
| BitLocker-stationsversleuteling |X |X | |
| SQL Server voor het versleutelen van data bases |X |X | |
| Versleuteling van VM naar VM | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Lees de [functie naleving door](https://azure.microsoft.com/support/trust-center/services/) het [vertrouwens centrum van Microsoft Azure](https://azure.microsoft.com/support/trust-center/) om meer te weten te komen over de certificeringen waarmee elke Azure-service compatibel is.
> Omdat de opties voor gegevens beveiliging gebruikmaken van een meerlaagse benadering, is vergelijking tussen deze opties niet van toepassing op deze taak. Zorg ervoor dat u alle beschik bare opties voor elke status van de gegevens kunt gebruiken.
>
>

## <a name="define-content-management-options"></a>Opties voor inhouds beheer definiëren

Een voor deel van het gebruik van Azure AD voor het beheren van een hybride identiteits infrastructuur is dat het proces volledig transparant is ten opzichte van het perspectief van de eind gebruiker. De gebruiker probeert toegang te krijgen tot een gedeelde resource, de bron vereist verificatie, de gebruiker moet een verificatie aanvraag verzenden naar Azure AD om het token te verkrijgen en toegang te krijgen tot de resource. Dit hele proces gebeurt op de achtergrond, zonder tussen komst van de gebruiker. 

Voor organisaties die betrekking hebben op privacy van gegevens is doorgaans gegevens classificatie vereist voor de oplossing. Als de huidige on-premises infra structuur al gebruikmaakt van gegevens classificatie, is het mogelijk om Azure AD te gebruiken als de hoofd opslagplaats voor de identiteit van de gebruiker. Een algemeen hulp programma dat het on-premises gebruikt voor gegevens classificatie heet [Data Classificatie Toolkit](/previous-versions/tn-archive/hh204743(v=technet.10)) voor Windows Server 2012 R2. Dit hulp programma kan helpen bij het identificeren, classificeren en beveiligen van gegevens op bestands servers in uw privécloud. Het is ook mogelijk om de [automatische bestands classificatie](/windows-server/identity/solution-guides/deploy-automatic-file-classification--demonstration-steps-) in Windows Server 2012 te gebruiken om deze taak uit te voeren.

Als uw organisatie geen gegevens classificatie heeft, maar wel gevoelige bestanden moet beveiligen zonder nieuwe on-premises servers toe te voegen, kunnen ze gebruikmaken van micro soft [Azure Rights Management service](/azure/information-protection/what-is-azure-rms).  Azure RMS maakt gebruik van beleids regels voor versleuteling, identiteit en verificatie om uw bestanden en e-mail te beveiligen en werkt op meerdere apparaten, zoals telefoons, Tablets en Pc's. Omdat Azure RMS een Cloud service is, hoeft u geen expliciete vertrouwens relaties met andere organisaties te configureren voordat u beveiligde inhoud met hen kunt delen. Als ze al een Microsoft 365 of een Azure AD-adres lijst hebben, wordt samen werking tussen organisaties automatisch ondersteund. U kunt ook alleen de adreslijst kenmerken synchroniseren die Azure RMS nodig hebt om een algemene identiteit voor uw on-premises Active Directory accounts te ondersteunen met behulp van Azure Active Directory Synchronization Services (Azure AD Sync) of Azure AD Connect.

Een belang rijk onderdeel van inhouds beheer is om te begrijpen wie toegang heeft tot welke resource, daarom is een uitgebreide logboek registratie van belang voor de oplossing voor identiteits beheer. Azure AD biedt logboeken meer dan 30 dagen, waaronder:

* Wijzigingen in het lidmaatschap van de rol (bijvoorbeeld: gebruiker toegevoegd aan rol van globale beheerder)
* Referentie-updates (bijvoorbeeld: wachtwoord wijzigingen)
* Domein beheer (bijvoorbeeld: het verifiëren van een aangepast domein, het verwijderen van een domein)
* Toepassingen toevoegen of verwijderen
* Gebruikers beheer (bijvoorbeeld: toevoegen, verwijderen, bijwerken van een gebruiker)
* Licenties toevoegen of verwijderen

> [!NOTE]
> Lees [Microsoft Azure beveiliging en audit logboek beheer](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) voor meer informatie over logboek registratie mogelijkheden in Azure.
> Afhankelijk van hoe u de vragen in de [vereisten voor inhouds beheer](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)hebt beantwoord, moet u kunnen bepalen hoe u de inhoud in uw hybride identiteits oplossing wilt beheren. Hoewel alle opties die worden weer gegeven in tabel 6 kunnen worden geïntegreerd met Azure AD, is het belang rijk om te definiëren wat geschikter is voor uw bedrijfs behoeften.
>
>

| Opties voor inhouds beheer | Voordelen | Nadelen |
| --- | --- | --- |
| Gecentraliseerd on-premises (Active Directory Rights Management-Server) |Volledige controle over de server infrastructuur die verantwoordelijk is voor het classificeren van de gegevens <br> Ingebouwde mogelijkheid in Windows Server, geen extra licentie of abonnement nodig <br> Kan worden geïntegreerd met Azure AD in een hybride scenario <br> Biedt ondersteuning voor Information Rights Management (IRM)-mogelijkheden in micro soft Online Services, zoals Exchange Online en share point online, en Microsoft 365 <br> Biedt ondersteuning voor on-premises micro soft Server-producten, zoals Exchange Server, share Point server en bestands servers waarop Windows Server en de infra structuur voor bestands classificatie (FCI) worden uitgevoerd. |Hoger onderhoud (Houd bij updates, configuratie en mogelijke upgrades), omdat het eigenaar is van de server <br> On-premises server infrastructuur vereisen<br> Maakt geen gebruik van Azure-mogelijkheden systeem eigen |
| Gecentraliseerd in de Cloud (Azure RMS) |Gemakkelijker te beheren in vergelijking met de on-premises oplossing <br> Kan worden geïntegreerd met AD DS in een hybride scenario <br>  Volledig geïntegreerd met Azure AD <br> Vereist geen on-premises server om de service te implementeren <br> Biedt ondersteuning voor on-premises micro soft Server-producten, zoals Exchange Server, share point, server en bestands servers waarop Windows Server en bestands classificatie, infra structuur (FCI) wordt uitgevoerd. <br> Dit kan volledige controle hebben over de sleutel van de Tenant met BYOK-functionaliteit. |Uw organisatie moet een Cloud abonnement hebben dat RMS ondersteunt <br> Uw organisatie moet een Azure AD-Directory hebben voor de ondersteuning van gebruikers verificatie voor RMS |
| Hybride (Azure RMS geïntegreerd met, on-premises Active Directory Rights Management Server) |In dit scenario worden de voor delen van zowel gecentraliseerd on-premises als in de Cloud verzameld. |Uw organisatie moet een Cloud abonnement hebben dat RMS ondersteunt <br> Uw organisatie moet een Azure AD-Directory hebben voor de ondersteuning van gebruikers verificatie voor RMS, <br> Vereist een verbinding tussen de Azure-Cloud service en de on-premises infra structuur |

## <a name="define-access-control-options"></a>Opties voor toegangs beheer definiëren
Door gebruik te maken van de mogelijkheden voor verificatie, autorisatie en toegangs beheer die beschikbaar zijn in azure AD, kunt u uw bedrijf de mogelijkheid bieden een centrale identiteits opslagplaats te gebruiken en gebruikers en partners in staat stellen om eenmalige aanmelding (SSO) te gebruiken, zoals wordt weer gegeven in de volgende afbeelding:

![gecentraliseerd beheer](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Gecentraliseerd beheer en volledige integratie met andere directory's

Azure Active Directory biedt eenmalige aanmelding voor duizenden SaaS-toepassingen en on-premises webtoepassingen. Zie de [Azure Active Directory federatieve compatibiliteits lijst: id-providers van derden die kunnen worden gebruikt om eenmalige aanmelding te implementeren](how-to-connect-fed-compatibility.md) voor meer informatie over de SSO-partij die door micro soft is getest. Met deze mogelijkheid kunnen organisaties diverse B2B-scenario's implementeren en het beheer van identiteiten en toegang behouden. Tijdens het ontwerp proces van B2B is echter belang rijk om te begrijpen welke verificatie methode wordt gebruikt door de partner en te controleren of deze methode wordt ondersteund door Azure. Momenteel worden de volgende methoden ondersteund door Azure AD:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokens
* Certificaten

> [!NOTE]
> Lees [Azure Active Directory verificatie protocollen](/previous-versions/azure/dn151124(v=azure.100)) voor meer informatie over elk protocol en de mogelijkheden ervan in Azure.
>
>

Met de Azure AD-ondersteuning kunnen mobiele zakelijke toepassingen gebruikmaken van dezelfde eenvoudige Mobile Services verificatie-ervaring, zodat werk nemers zich kunnen aanmelden bij hun mobiele toepassingen met hun bedrijfs Active Directory referenties. Met deze functie wordt Azure AD ondersteund als een id-provider in Mobile Services naast de andere id-providers die al worden ondersteund (met inbegrip van micro soft-accounts, Facebook-ID, Google ID en Twitter-ID). Als de on-premises apps gebruikmaken van de referenties van de gebruiker die zich op de AD DS van het bedrijf bevinden, moet de toegang van partners en gebruikers die afkomstig zijn van de Cloud transparant zijn. U kunt het voorwaardelijke toegangs beheer van gebruikers beheren op basis van (Cloud) webtoepassingen, Web-API, micro soft-Cloud Services, SaaS-toepassingen van derden en systeem eigen (mobiele) client toepassingen en hebben de voor delen van beveiliging, controle en rapportage op één plek. Het wordt echter aangeraden de implementatie in een niet-productie omgeving of met een beperkt aantal gebruikers te valideren.

> [!TIP]
> het is belang rijk om te vermelden dat Azure AD geen groepsbeleid heeft, zoals AD DS heeft. Als u beleid voor apparaten wilt afdwingen, hebt u een Mobile Device Management oplossing nodig, zoals [Microsoft intune](/mem/intune/).
>
>

Zodra de gebruiker is geverifieerd met Azure AD, is het belang rijk om het toegangs niveau te evalueren dat de gebruiker heeft. Het toegangs niveau dat de gebruiker heeft voor een resource kan variëren. Hoewel Azure AD een extra beveiligingslaag kan toevoegen door de toegang tot bepaalde resources te beheren, moet u er ook voor zorgen dat de resource zelf een eigen toegangs beheer lijst afzonderlijk kan hebben, zoals het toegangs beheer voor bestanden die zich op een bestands server bevinden. De volgende afbeelding bevat een overzicht van de niveaus van toegangs beheer die u in een hybride scenario kunt hebben:

![Toegangs beheer](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Elke interactie in het diagram wordt weer gegeven in afbeelding X, een scenario voor toegangs beheer dat kan worden gedekt door Azure AD. Hieronder ziet u een beschrijving van elk scenario:

1. Voorwaardelijke toegang tot toepassingen die on-premises worden gehost: u kunt geregistreerde apparaten gebruiken met toegangs beleid voor toepassingen die zijn geconfigureerd voor het gebruik van AD FS met Windows Server 2012 R2.

2. Access Control met de Azure Portal: Azure kunt u ook de toegang tot de portal beheren door gebruik te maken van Azure op rollen gebaseerd toegangs beheer (Azure RBAC)). Met deze methode kan het bedrijf het aantal bewerkingen beperken dat een persoon in de Azure Portal kan doen. Door gebruik te maken van Azure RBAC om de toegang tot de portal te beheren, kunnen IT-beheerders de toegang delegeren met behulp van de volgende toegangs beheer methoden:

   - Functie toewijzing op basis van een groep: u kunt toegang tot Azure AD-groepen toewijzen die kunnen worden gesynchroniseerd met uw lokale Active Directory. Zo kunt u gebruikmaken van de bestaande investeringen die uw organisatie heeft gemaakt bij het beheer en de processen voor het beheren van groepen. U kunt ook de functie gedelegeerde groeps beheer van Azure AD Premium gebruiken.
   - Ingebouwde rollen gebruiken in Azure: u kunt drie rollen: eigenaar, Inzender en lezer gebruiken om ervoor te zorgen dat gebruikers en groepen gemachtigd zijn om alleen de taken uit te voeren die ze nodig hebben om hun taken uit te voeren.
   -  Gedetailleerde toegang tot resources: u kunt rollen toewijzen aan gebruikers en groepen voor een bepaald abonnement, een resource groep of een afzonderlijke Azure-resource, zoals een website of Data Base. Op deze manier kunt u ervoor zorgen dat gebruikers toegang hebben tot alle resources die ze nodig hebben en geen toegang hebben tot resources die ze niet hoeven te beheren.

   > [!NOTE]
   > Als u toepassingen bouwt en het toegangs beheer voor hen wilt aanpassen, is het ook mogelijk Azure AD-toepassings rollen voor autorisatie te gebruiken. Bekijk deze [webapp-RoleClaims-DotNet-voorbeeld](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) informatie over het bouwen van uw app om deze mogelijkheid te gebruiken.


3. Voorwaardelijke toegang voor Microsoft 365 toepassingen met Microsoft Intune: IT-beheerders kunnen beleid voor voorwaardelijke toegang inrichten om bedrijfs bronnen te beveiligen, terwijl ze tegelijkertijd informatie werkers op compatibele apparaten kunnen gebruiken om toegang te krijgen tot de services. 
  
4. Voorwaardelijke toegang voor SaaS-apps: met [deze functie](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) kunt u toegangs regels voor multi-factor Authentication per toepassing configureren en kunt u de toegang blok keren voor gebruikers die zich niet in een vertrouwd netwerk besturen. U kunt de multi-factor Authentication-regels Toep assen op alle gebruikers die zijn toegewezen aan de toepassing of alleen voor gebruikers binnen opgegeven beveiligings groepen. Gebruikers kunnen worden uitgesloten van de multi-factor Authentication-vereiste als ze toegang hebben tot de toepassing vanaf een IP-adres dat zich in het netwerk van de organisatie bevindt.

Omdat de opties voor toegangs beheer gebruikmaken van een methode met meerdere lagen, is vergelijking tussen deze opties niet van toepassing op deze taak. Zorg ervoor dat u alle beschik bare opties voor elk scenario hebt waarmee u de toegang tot uw resources moet beheren.

## <a name="define-incident-response-options"></a>Opties voor incident reacties definiëren
Azure AD kan IT helpen bij het bewaken van potentiële beveiligings Risico's in de omgeving door de activiteit van de gebruiker te controleren. HET kan gebruikmaken van Azure AD-toegang en gebruiks rapporten om inzicht te krijgen in de integriteit en beveiliging van de directory van uw organisatie. Met deze informatie kan een IT-beheerder beter bepalen waar mogelijke beveiligings Risico's zich kunnen voordoen, zodat ze adequaat kunnen plannen om deze Risico's te beperken.  [Azure AD Premium abonnement](../fundamentals/active-directory-get-started-premium.md) beschikt over een set beveiligings rapporten waarmee deze gegevens kunnen worden opgehaald. [Azure AD-rapporten](../reports-monitoring/overview-reports.md) worden als volgt gecategoriseerd:

* **Afwijkingen rapporten**: bevatten aanmeldings gebeurtenissen die afwijkend zijn aangetroffen. Het doel is om u op de hoogte te stellen van deze activiteit en u in staat te stellen te bepalen of een gebeurtenis verdacht is.
* **Geïntegreerd toepassings rapport**: biedt inzicht in de manier waarop Cloud toepassingen in uw organisatie worden gebruikt. Azure Active Directory biedt integratie met duizenden Cloud toepassingen.
* **Fout rapporten**: duiden op fouten die kunnen optreden bij het inrichten van accounts voor externe toepassingen.
* **Gebruikersspecifieke rapporten**: Hiermee geeft u de gegevens van het apparaat of de activiteit voor een specifieke gebruiker weer.
* **Activiteiten logboeken**: bevatten een record van alle gecontroleerde gebeurtenissen in de afgelopen 24 uur, in de afgelopen 7 dagen, of de afgelopen 30 dagen, evenals wijzigingen in de groeps activiteit, en het opnieuw instellen van het wacht woord en de registratie activiteit.

> [!TIP]
> Een ander rapport dat ook kan helpen het incident response team dat aan een aanvraag werkt, is de [gebruiker met het rapport gelekte referenties](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) . In dit rapport worden alle overeenkomsten tussen de lijst met gelekte referenties en uw Tenant geoppereerd.
>


Andere belang rijke ingebouwde rapporten in azure AD die kunnen worden gebruikt tijdens een onderzoek naar de incident respons en zijn:

* **Activiteit voor het opnieuw instellen van het wacht woord**: Geef de beheerder een inzicht in hoe actief wacht woord opnieuw instellen in de organisatie wordt gebruikt.
* **Registratie activiteit voor wacht woord opnieuw instellen**: geeft inzicht in welke gebruikers hun methoden hebben geregistreerd voor het opnieuw instellen van wacht woorden en welke methoden ze hebben geselecteerd.
* **Groeps activiteit**: bevat een overzicht van de wijzigingen aan de groep (bijvoorbeeld: gebruikers die zijn toegevoegd of verwijderd) die zijn gestart in het toegangs venster.

Naast de belangrijkste rapportage mogelijkheden van Azure AD Premium die u tijdens een onderzoek naar de incident respons kunt gebruiken, kan het ook profiteren van het controle rapport om informatie te verkrijgen, zoals:

* Wijzigingen in het lidmaatschap van de rol (bijvoorbeeld gebruiker toegevoegd aan rol van globale beheerder)
* Referentie-updates (bijvoorbeeld wachtwoord wijzigingen)
* Domein beheer (bijvoorbeeld het verifiëren van een aangepast domein, het verwijderen van een domein)
* Toepassingen toevoegen of verwijderen
* Gebruikers beheer (bijvoorbeeld het toevoegen, verwijderen, bijwerken van een gebruiker)
* Licenties toevoegen of verwijderen

Omdat de opties voor incident reacties gebruikmaken van een meerlaagse benadering, is vergelijking tussen deze opties niet van toepassing op deze taak. Zorg ervoor dat u alle beschik bare opties gebruikt voor elk scenario waarvoor u Azure AD-rapportage mogelijkheden moet gebruiken als onderdeel van het antwoord proces van uw bedrijf.

## <a name="next-steps"></a>Volgende stappen
[Taken voor het beheren van hybride identiteiten bepalen](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)