---
title: Ontwikkelings strategie voor de levens cyclus van hybride identiteiten Azure | Microsoft Docs
description: Helpt bij het definiëren van de hybride identiteits beheer taken op basis van de beschik bare opties voor elke levenscyclus fase.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68406d4b0806e1d533e0bb8669a01939387989
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997746"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Strategie voor acceptatie van levens duur van hybride identiteit bepalen
In deze taak definieert u de strategie voor identiteits beheer voor uw hybride identiteits oplossing om te voldoen aan de bedrijfs vereisten die u hebt gedefinieerd in [hybride identiteits beheer taken bepalen](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Als u de hybride identiteits beheer taken wilt definiëren volgens de end-to-end levens cyclus van de identiteit die eerder in deze stap is gepresenteerd, moet u rekening houden met de beschik bare opties voor elke levens cyclus fase.

## <a name="access-management-and-provisioning"></a>Toegangs beheer en inrichting
Met een goede account toegangs beheer oplossing kan uw organisatie nauw keurig bijhouden wie er toegang tot de gegevens in de hele organisatie heeft.

Access Control is een belang rijke functie van een gecentraliseerd systeem voor het inrichten van één punt. Naast het beveiligen van gevoelige informatie, worden met toegangs beheer bestaande accounts met niet-goedgekeurde autorisaties toegankelijk of niet langer nodig. Voor het beheren van verouderde accounts, wordt het inrichtings systeem gekoppeld aan account gegevens met gezaghebbende informatie over de gebruikers die eigenaar zijn van de accounts. Gegevens over gezaghebbende gebruikers identiteit worden doorgaans bewaard in de data bases en in de directory's van human resources.

Accounts in geavanceerde IT-ondernemingen bevatten honderden para meters waarmee de autoriteiten worden gedefinieerd. deze gegevens kunnen worden beheerd door uw inrichtings systeem. Nieuwe gebruikers kunnen worden geïdentificeerd aan de hand van de gegevens die u opgeeft van de gezaghebbende bron. De functie voor het goed keuren van de toegangs aanvraag initieert de processen die het inrichten van resources goed keuren (of afwijzen).

| Levenscyclus beheer fase | On-premises | Cloud | Hybride |
| --- | --- | --- | --- |
| Account beheer en inrichting |Met behulp van de serverfunctie AD DS (Active Directory® Domain Services) kunt u een schaalbare, veilige en beheerbare infrastructuur maken voor gebruikers- en resourcebeheer, en ondersteuning bieden voor Active Directory-toepassingen zoals Microsoft® Exchange Server. <br><br> [U kunt groepen in AD DS inrichten via een identiteits Manager](/previous-versions/mim/ff686261(v=ws.10)) <br>[U kunt gebruikers inrichten in AD DS](/previous-versions/mim/ff686263(v=ws.10)) <br><br> Beheerders kunnen toegangs beheer gebruiken voor het beheren van de gebruikers toegang tot gedeelde bronnen om veiligheids redenen. In Active Directory wordt toegangs beheer op object niveau beheerd door verschillende toegangs niveaus of machtigingen in te stellen voor objecten, zoals volledig beheer, schrijven, lezen of geen toegang. Access Control in Active Directory definieert hoe verschillende gebruikers Active Directory objecten kunnen gebruiken. Machtigingen voor objecten in Active Directory worden standaard ingesteld op de veiligste instelling. |U moet een account maken voor elke gebruiker die toegang heeft tot een micro soft-Cloud service. U kunt ook gebruikers accounts wijzigen of verwijderen wanneer ze niet meer nodig zijn. Standaard hebben gebruikers geen beheerders machtigingen, maar u kunt ze desgewenst toewijzen. <br><br> Binnen Azure Active Directory is een van de belangrijkste functies de mogelijkheid om de toegang tot resources te beheren. Deze resources kunnen deel uitmaken van de adreslijst, zoals in het geval van machtigingen voor het beheren van objecten door middel van rollen in de adreslijst, of resources die zich buiten de adreslijst bevinden, zoals SaaS-toepassingen, Azure-services, en SharePoint-sites of on-premises resources. <br><br> In het midden van de oplossing voor toegangs beheer van Azure Active Directory is de beveiligings groep. De resource-eigenaar (of de beheerder van de adreslijst of directory) kan een groep toewijzen om een bepaald toegangsrecht in te stellen voor de resources waarvan ze eigenaar zijn. De leden van de groep krijgen de toegang en de resource-eigenaar kan het recht delegeren om de leden lijst van een groep te beheren voor iemand anders, zoals een Afdelings Manager of een helpdesk beheerder<br> <br> De sectie groepen beheren in azure AD biedt meer informatie over het beheren van toegang via groepen. |Active Directory-identiteiten in de Cloud uitbreiden via synchronisatie en Federatie |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer
Azure RBAC (op rollen gebaseerd toegangs beheer) gebruikt rollen en inrichtings beleid om uw bedrijfs processen en regels voor het verlenen van toegang aan gebruikers te evalueren, te testen en af te dwingen. Belang rijke beheerders maken inrichtings beleid en wijzen gebruikers toe aan rollen en die sets van rechten definiëren voor de resources voor deze rollen. Azure RBAC breidt de oplossing voor identiteits beheer uit voor het gebruik van software processen en reduceert de gebruikers matige interactie in het inrichtings proces.
Met Azure RBAC kan het bedrijf het aantal bewerkingen beperken dat een persoon kan doen als ze toegang hebben tot de Azure Portal. Door gebruik te maken van Azure RBAC om de toegang tot de portal te beheren, beheert de CA-gemachtigde toegang met behulp van de volgende toegangs beheer methoden:

* **Functie toewijzing op basis** van een groep: u kunt toegang tot Azure ad-groepen toewijzen die kunnen worden gesynchroniseerd met uw lokale Active Directory. Zo kunt u gebruikmaken van de bestaande investeringen die uw organisatie heeft gemaakt bij het beheer en de processen voor het beheren van groepen. U kunt ook de functie gedelegeerde groeps beheer van Azure AD Premium gebruiken.
* **Ingebouwde rollen in azure benutten**: u kunt drie rollen gebruiken: eigenaar, Inzender en lezer, om ervoor te zorgen dat gebruikers en groepen gemachtigd zijn om alleen de taken uit te voeren die ze nodig hebben om hun taken uit te voeren.
* **Gedetailleerde toegang tot resources**: u kunt rollen toewijzen aan gebruikers en groepen voor een bepaald abonnement, een resource groep of een afzonderlijke Azure-resource, zoals een website of Data Base. Op deze manier kunt u ervoor zorgen dat gebruikers toegang hebben tot alle resources die ze nodig hebben en geen toegang hebben tot resources die ze niet hoeven te beheren.

## <a name="provisioning-and-other-customization-options"></a>Inrichtings-en andere aanpassings opties
Uw team kan gebruikmaken van zakelijke abonnementen en vereisten om te bepalen hoeveel u de identiteits oplossing wilt aanpassen. Een grote onderneming kan bijvoorbeeld een uitgaand implementatie plan voor werk stromen en aangepaste adapters vereisen dat is gebaseerd op een tijd lijn voor het incrementeel inrichten van toepassingen die veel worden gebruikt in verschillende geografische gebieden. Een ander aanpassings plan kan voor twee of meer toepassingen worden ingericht voor een hele organisatie, na geslaagde tests. Interactie van de gebruiker-toepassing kan worden aangepast en de procedures voor het inrichten van resources kunnen worden gewijzigd voor automatische inrichting.

U kunt het inrichten voor het verwijderen van een service of onderdeel ongedaan maken. Het ongedaan maken van de inrichting van een account betekent bijvoorbeeld dat het account wordt verwijderd uit een resource.

Het hybride model van de inrichtings resources combineert aanvragen en op rollen gebaseerde benaderingen, die beide door Azure AD worden ondersteund. Voor een subset van werk nemers of beheerde systemen is het mogelijk dat een bedrijf toegang kan automatiseren met toewijzing op basis van een rol. Een bedrijf kan ook alle andere toegangs aanvragen of uitzonde ringen afhandelen via een op een aanvraag gebaseerd model. Sommige bedrijven kunnen beginnen met hand matige toewijzing en zich ontwikkelen naar een hybride model, met als doel een volledig op rollen gebaseerde implementatie in een later tijdstip.

Andere bedrijven kunnen het niet praktisch om zakelijke redenen vinden om op rollen gebaseerde inrichting te verzorgen en een hybride benadering als een gewenst doel te bereiken. Toch kunnen andere bedrijven aan de slag gaan met het inrichten op basis van een aanvraag en willen ze geen extra inspanningen investeren om een op rollen gebaseerd, geautomatiseerd inrichtings beleid te definiëren en te beheren.

## <a name="license-management"></a>Licentiebeheer
Met licentie beheer op basis van groepen in azure AD kunnen beheerders gebruikers toewijzen aan een beveiligings groep en wordt in azure AD automatisch licenties toegewezen aan alle leden van de groep. Als een gebruiker vervolgens wordt toegevoegd aan of verwijderd uit de groep, wordt een licentie automatisch toegewezen of verwijderd als dat nodig is.

U kunt groepen gebruiken die u synchroniseert vanuit on-premises AD of beheren in azure AD. Koppeling maken met Azure AD Premium Self-Service groeps beheer u kunt de licentie toewijzing eenvoudig delegeren aan de juiste besluit vormers. U kunt er zeker van zijn dat problemen zoals licentie conflicten en ontbrekende locatie gegevens automatisch worden gesorteerd.

## <a name="self-regulating-user-administration"></a>Zelfregulerend gebruikers beheer
Wanneer uw organisatie begint met het inrichten van resources voor alle interne organisaties, implementeert u de zelfregulerende functie voor het beheer van de gebruiker. U kunt profiteren van de voor delen en voor delen van het inrichten van gebruikers binnen de grenzen van de organisatie. In deze omgeving wordt een wijziging in de status van een gebruiker automatisch doorgevoerd in de toegangs rechten voor de grenzen van de organisatie en de geografische gebieden. U kunt de inrichtings kosten verlagen en de toegangs-en goedkeurings processen stroom lijnen. De implementatie realiseert de volledige mogelijkheden voor het implementeren van op rollen gebaseerd toegangs beheer voor end-to-end toegangs beheer in uw organisatie. U kunt de beheer kosten verlagen door geautomatiseerde procedures voor het inrichten van gebruikers. U kunt de beveiliging verbeteren door het afdwingen van het beveiligings beleid te automatiseren en het beheer van de levens cyclus van gebruikers en resources te stroom lijnen en te centraliseren voor grote gebruikers populaties.

> [!NOTE]
> Zie voor meer informatie instellen van Azure AD for self service Application Access Management
> 
> 

Azure AD-Services op basis van licenties (op basis van rechten) werkt door een abonnement te activeren in uw Azure AD Directory/service-Tenant. Zodra het abonnement actief is, kunnen de service mogelijkheden worden beheerd door de Directory/service-beheerders en worden gebruikt door gebruikers met een licentie. 

## <a name="integration-with-other-3rd-party-providers"></a>Integratie met andere externe providers

Azure Active Directory biedt een eenmalige aanmelding en verbeterde beveiliging van toepassings toegang tot duizenden SaaS-toepassingen en on-premises webtoepassingen. Zie [toepassingen integreren met Azure Active Directory](../develop/quickstart-register-app.md) voor meer informatie

## <a name="define-synchronization-management"></a>Synchronisatie beheer definiëren
Wanneer u uw on-premises directory's integreert met Azure AD, worden uw gebruikers productiever omdat zij één identiteit hebben voor toegang tot zowel resources in de cloud als on-premises. Met deze integratie kunnen gebruikers en organisaties profiteren van het volgende:

* Organisaties kunnen gebruikers voorzien van een gemeen schappelijke hybride identiteit voor on-premises of Cloud Services die gebruikmaken van Windows Server Active Directory en vervolgens verbinding maken met Azure Active Directory.
* Beheerders kunnen voorwaardelijke toegang bieden op basis van toepassings resource, apparaat-en gebruikers identiteit, netwerk locatie en multi-factor Authentication.
* Gebruikers kunnen met hun algemene identiteit gebruikmaken van accounts in azure AD om Microsoft 365, intune, SaaS-apps en toepassingen van derden.
* Ontwikkel aars kunnen toepassingen bouwen die gebruikmaken van het gemeen schappelijke identiteits model, toepassingen integreren in Active Directory on-premises of Azure voor Cloud toepassingen

De volgende afbeelding bevat een voor beeld van een globaal overzicht van het proces van identiteits synchronisatie.

![Synchroniseren](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Identiteitssynchronisatie proces

Bekijk de volgende tabel om de synchronisatie opties te vergelijken:

| Optie synchronisatie beheer | Voordelen | Nadelen |
| --- | --- | --- |
| Op basis van synchronisatie (via DirSync of AADConnect) |Gebruikers en groepen die zijn gesynchroniseerd vanuit on-premises en in de Cloud <br>  **Beleids beheer**: account beleid kan worden ingesteld via Active Directory, waarmee de beheerder wachtwoord beleid, werk station, beperkingen, vergrendelings besturings elementen en meer kan beheren, zonder dat er extra taken in de Cloud hoeven te worden uitgevoerd.  <br>  **Toegangs beheer**: kan de toegang tot de Cloud service beperken, zodat de services toegankelijk zijn via de bedrijfs omgeving, via online servers of beide. <br>  Beperkte ondersteunings aanvragen: als gebruikers minder wacht woorden hebben om te onthouden, zijn ze minder waarschijnlijk om ze te verg eten. <br>  Beveiliging: gebruikers identiteiten en gegevens zijn beveiligd omdat alle servers en services die worden gebruikt in eenmalige aanmelding, worden gemasterd en beheerd. <br>  Ondersteuning voor sterke verificatie: u kunt sterke verificatie (ook wel twee ledige verificatie genoemd) met de Cloud service gebruiken. Als u echter sterke authenticatie gebruikt, moet u eenmalige aanmelding gebruiken. | |
| Op Federatie gebaseerd (via AD FS) |Ingeschakeld door de Security Token Service (STS). Wanneer u een STS configureert om toegang met eenmalige aanmelding te bieden met een micro soft-Cloud service, maakt u een federatieve vertrouwens relatie tussen uw on-premises STS en het federatieve domein dat u hebt opgegeven in uw Azure AD-Tenant. <br> Hiermee kunnen eind gebruikers dezelfde set referenties gebruiken om toegang te krijgen tot meerdere resources <br>eind gebruikers hoeven niet meerdere sets met referenties te onderhouden. De gebruikers moeten de referenties echter aan elk van de deelnemende bronnen verstrekken., B2B-en B2C-scenario's worden ondersteund. |Vereist gespecialiseerde mede werkers voor de implementatie en het onderhoud van toegewezen on-premises AD FS servers. Er gelden beperkingen voor het gebruik van sterke verificatie als u AD FS voor uw STS wilt gebruiken. Zie [Geavanceerde opties configureren voor AD FS 2,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh237448(v=ws.10))voor meer informatie. |

> [!NOTE]
> Zie [uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md)voor meer informatie.
> 
> 

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)