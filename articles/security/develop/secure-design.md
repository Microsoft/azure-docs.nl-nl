---
title: Veilige toepassingen ontwerpen op Microsoft Azure
description: In dit artikel worden aanbevolen procedures besproken die u moet overwegen tijdens de vereisten-en ontwerp fasen van uw Web Application project.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.openlocfilehash: 9e5246edd2d6490e823bacbdfff0f60ef553878b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729130"
---
# <a name="design-secure-applications-on-azure"></a>Veilige toepassingen ontwerpen in azure
In dit artikel bieden we beveiligings activiteiten en-controles waarmee u rekening moet houden bij het ontwerpen van toepassingen voor de Cloud. Trainings bronnen samen met beveiligings vragen en concepten die u kunt overwegen tijdens de vereisten en ontwerp fasen van micro soft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) , worden gedekt. Het doel is om u te helpen bij het definiëren van activiteiten en Azure-Services die u kunt gebruiken om een veiligere toepassing te ontwerpen.

In dit artikel komen de volgende SDL-fasen aan bod:

- Training
- Vereisten
- Ontwerp

## <a name="training"></a>Training
Voordat u begint met het ontwikkelen van uw Cloud toepassing, is het tijd om inzicht te krijgen in de beveiliging en privacy van Azure. Als u deze stap uitvoert, kunt u het aantal en de ernst van de zwakke plekken in uw toepassing beperken. U bent er meer voor bereid om op de juiste manier te reageren op de steeds veranderende bedreigings breedte.

Gebruik de volgende resources tijdens de trainings fase om vertrouwd te raken met de Azure-Services die beschikbaar zijn voor ontwikkel aars en met best practices voor beveiliging op Azure:

  - In [de hand leiding voor ontwikkel aars van Azure](https://azure.microsoft.com/campaigns/developer-guide/) ziet u hoe u aan de slag kunt gaan met Azure. De hand leiding laat zien welke services u kunt gebruiken om uw toepassingen uit te voeren, uw gegevens op te slaan, intelligentie op te nemen, IoT-apps te bouwen en uw oplossingen op een efficiënte en veilige manier te implementeren.

  - De [aan de slag-hand leiding voor Azure-ontwikkel aars](../../guides/developer/azure-developer-guide.md) biedt essentiële informatie voor ontwikkel aars die aan de slag willen gaan met het Azure-platform voor hun ontwikkelings behoeften.

  - [Sdk's en hulpprogram ma's](../../index.yml?pivot=sdkstools) beschrijft de hulpprogram ma's die beschikbaar zijn in Azure.

  - [Azure DevOps Services](/azure/devops/) biedt ontwikkel samenwerkings hulpmiddelen. De hulpprogram ma's zijn hoogwaardige pijp lijnen, gratis Git-opslag plaatsen, Configureer bare Kanban boards en uitgebreide geautomatiseerde belasting tests op basis van de Cloud.
    In het [DevOps Resource Center](/azure/devops/learn/) worden onze resources gecombineerd voor het leren van DevOps-procedures, Git-versie beheer, agile-methoden, hoe we werken met DevOps bij micro soft en hoe u uw eigen DevOps-voortgang kunt beoordelen.

  - De [belangrijkste 5 beveiligings items die u moet overwegen voordat u naar productie pusht](/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) , laten zien hoe u uw webtoepassingen op Azure kunt beveiligen en uw apps tegen de meest voorkomende en gevaarlijke webtoepassingen moet beveiligen.

  - [Secure DevOps Kit voor Azure](https://azsk.azurewebsites.net/index.html) is een verzameling scripts, hulpprogram ma's, uitbrei dingen en automaters die zijn voorzien van het uitgebreide Azure-abonnement en de vereisten voor resource beveiliging van DevOps-teams die gebruikmaken van uitgebreide automatisering. Met de Secure DevOps Kit voor Azure kunt u zien hoe u de beveiliging probleemloos integreert in uw systeem eigen DevOps-werk stromen. De kit bevat hulpprogram ma's zoals beveiligings verificatie tests (SVTs), waarmee ontwikkel aars beveiligde code kunnen schrijven en de veilige configuratie van hun Cloud toepassingen in de fase ring en vroege ontwikkeling moeten testen.

  - De [Aanbevolen procedures voor beveiliging voor Azure-oplossingen](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) bieden een aantal aanbevolen beveiligings procedures voor het ontwerpen, implementeren en beheren van uw cloud oplossingen met behulp van Azure.

## <a name="requirements"></a>Vereisten
De definitie fase van vereisten is een belang rijke stap bij het definiëren van wat uw toepassing is en wat er gebeurt wanneer deze wordt uitgebracht. De fase vereisten is ook een tijd om u te bedenken over de beveiligings controles die u in uw toepassing gaat maken. Tijdens deze fase gaat u ook aan de hand van de stappen die u in de hele SDL gaat uitvoeren om ervoor te zorgen dat u een veilige toepassing vrijgeeft en implementeert.

### <a name="consider-security-and-privacy-issues"></a>Beveiligings-en privacy-problemen overwegen
Deze fase is de beste manier om basis problemen met de beveiliging en privacy te voor komen. Het definiëren van een acceptabel beveiligings niveau en privacy aan het begin van een project helpt een team:

- Meer informatie over Risico's die zijn gekoppeld aan beveiligings problemen.
- Identificeer en los beveiligings fouten op tijdens de ontwikkeling.
- Pas in het hele project bestaande niveaus van beveiliging en privacy toe.

Wanneer u de vereisten voor uw toepassing schrijft, moet u rekening houden met beveiligings controles waarmee u uw toepassing en gegevens veilig kunt houden.

### <a name="ask-security-questions"></a>Beveiligings vragen stellen
Vraag beveiligings vragen als:

  - Bevat mijn toepassing gevoelige gegevens?

  - Verzamelt of opslaat mijn toepassing gegevens die mij nodig heeft om te voldoen aan de industrie normen en nalevings Programma's zoals de [Federal Financial Instituut onderzoeksbureau (FFIEC)](/previous-versions/azure/security/blueprints/ffiec-analytics-overview) of de [PCI DSS (Data Security Standards) van de betalings kaart](/previous-versions/azure/security/blueprints/pcidss-analytics-overview).

  - Verzamelt mijn toepassing gevoelige persoonlijke of klant gegevens die kunnen worden gebruikt, hetzij zelf, hetzij met andere informatie, om een persoon te identificeren, contact op te nemen of te vinden.

  - Verzamelt mijn toepassing gegevens die kunnen worden gebruikt om toegang te krijgen tot de medische, onderwijs, financiële of dienst informatie van een persoon? Het identificeren van de gevoeligheid van uw gegevens tijdens de vereisten fase helpt u bij het classificeren van uw gegevens en het identificeren van de gegevens beveiligings methode die u voor uw toepassing gaat gebruiken.

  - Waar en hoe worden mijn gegevens opgeslagen? Denk na over hoe u de opslag services bewaken die door uw toepassing worden gebruikt voor onverwachte wijzigingen (zoals tragere reactie tijden). Kunt u de logboek registratie zodanig beïnvloeden dat er gedetailleerdere gegevens worden verzameld en een probleem dieper kan worden geanalyseerd?

  - Is mijn toepassing beschikbaar voor het publiek (op Internet) of alleen intern? Als uw toepassing voor het publiek beschikbaar is, hoe beveiligt u de gegevens die kunnen worden verzameld op de verkeerde manier? Als uw toepassing alleen intern beschikbaar is, kunt u overwegen wie in uw organisatie toegang moet hebben tot de toepassing en hoelang ze toegang moeten hebben.

  - Begrijpt u uw identiteits model voordat u begint met het ontwerpen van uw toepassing? Hoe kunt u bepalen wie de gebruikers zijn die ze zeggen en wat een gebruiker mag doen?

  - Voert mijn toepassing gevoelige of belang rijke taken uit (zoals het overdragen van geld, het ontgrendelen van deuren of het leveren van medicijnen)?
    Denk na over hoe u controleert of de gebruiker die een gevoelige taak uitvoert, gemachtigd is om de taak uit te voeren en hoe u zich verifieert dat de persoon die het heeft gedicteerd. Authorization (AuthZ) is het verlenen van een geverifieerde beveiligingsprincipal-machtiging om iets te doen. Verificatie (authn) is de handeling van een feestje voor legitieme referenties.

  - Voert mijn toepassing eventuele Risk ante software activiteiten uit, zoals toestaan dat gebruikers bestanden of andere gegevens uploaden of downloaden? Als uw toepassing Risk ante activiteiten uitvoert, kunt u overwegen hoe uw toepassing gebruikers zal beschermen tegen het afhandelen van schadelijke bestanden of gegevens.

### <a name="review-owasp-top-10"></a>OWASP Top 10 controleren
Bekijk de [<span class="underline">OWASP Top 10-beveiligings Risico's voor toepassingen</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
De OWASP Top 10 heeft betrekking op kritieke beveiligings Risico's voor webtoepassingen.
Het bewustzijn van deze beveiligings Risico's kan u helpen om vereisten en ontwerp beslissingen te nemen die deze Risico's in uw toepassing minimaliseren.

Het is belang rijk dat u op de hoogte bent van beveiligings controles om inbreuken te voor komen.
U wilt echter ook [aannemen dat er](/azure/devops/learn/devops-at-microsoft/security-in-devops) sprake is van een schending. Uitgaande van een inbreuk kunnen enkele belang rijke vragen over beveiliging vooraf worden beantwoord, zodat deze niet in nood gevallen hoeven te worden beantwoord:

  - Hoe kan ik een aanval detecteren?

  - Wat moet ik doen als er een aanval of schending is?

  - Hoe kan ik de aanval herstellen, zoals gegevens lekken of knoeien?

## <a name="design"></a>Ontwerp

De ontwerp fase is essentieel voor het opstellen van aanbevolen procedures voor ontwerp-en functionele specificaties. Het is ook essentieel voor het uitvoeren van een risico analyse waardoor beveiligings-en privacy-problemen in een project worden verholpen.

Wanneer u beveiligings vereisten hebt en gebruik maakt van beveiligde ontwerp concepten, kunt u de kansen voor een beveiligings probleem voor komen of minimaliseren. Een beveiligings fout is een toezicht op het ontwerp van de toepassing waardoor een gebruiker schadelijke of onverwachte acties kan uitvoeren nadat de toepassing is uitgebracht.

Tijdens de ontwerp fase moet u ook nadenken over hoe u beveiliging in lagen kunt Toep assen. Eén verdedigings niveau is niet noodzakelijkerwijs genoeg. Wat gebeurt er als een aanvaller voorbij uw Web Application Firewall (WAF) komt? U wilt een ander beveiligings beheer hebben om te beschermen tegen die aanval.

Daarom bespreken we de volgende beveiligde ontwerp concepten en de beveiligings maatregelen die u moet aanpakken wanneer u veilige toepassingen ontwerpt:

- Gebruik een beveiligde coderings bibliotheek en een software raamwerk.
- Scannen op kwets bare onderdelen.
- Gebruik bedreigingen modellering tijdens het ontwerpen van toepassingen.
- Verminder de kwets baarheid voor aanvallen.
- Een beleid met identiteit als de primaire beveiligings verbinding wordt aangenomen.
- Nieuwe verificatie vereisen voor belang rijke trans acties.
- Gebruik een oplossing voor sleutel beheer om sleutels, referenties en andere geheimen te beveiligen.
- Beveilig gevoelige gegevens.
- Implementeer fout veilige maat regelen.
- Profiteer van de afhandeling van fouten en uitzonde ringen.
- Gebruik logboek registratie en waarschuwingen.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Een beveiligde coderings bibliotheek en een software raamwerk gebruiken

Gebruik voor ontwikkeling een beveiligde coderings bibliotheek en een software raamwerk met geïntegreerde beveiliging. Ontwikkel aars kunnen bestaande, bewezen functies (versleuteling, invoer-, uitvoer-of-teken codering, sleutels of verbindings reeksen en alle andere die worden beschouwd als een beveiligings controle) gebruiken in plaats van een volledig nieuwe beveiligings besturings elementen te ontwikkelen. Zo kunt u zich beschermen tegen beveiligings problemen met betrekking tot het ontwerp en de implementatie.

Zorg ervoor dat u de nieuwste versie van uw Framework gebruikt en alle beveiligings functies die beschikbaar zijn in het Framework. Micro soft biedt een uitgebreide [set ontwikkel tools](https://azure.microsoft.com/product-categories/developer-tools/) voor alle ontwikkel aars die op elk platform of elke taal werken om Cloud toepassingen te leveren. U kunt code met de taal van uw keuze door kiezen uit verschillende [sdk's](https://azure.microsoft.com/downloads/).
U kunt profiteren van Ide's (Full-complete Integrated Development Environments) en editors met geavanceerde mogelijkheden voor fout opsporing en ingebouwde ondersteuning voor Azure.

Micro soft biedt diverse [talen, frameworks en hulpprogram ma's](../../index.yml?panel=sdkstools-all&pivot=sdkstools) die u kunt gebruiken om toepassingen te ontwikkelen in Azure. Een voor beeld is [Azure voor .net-en .net core-ontwikkel aars](/dotnet/azure/). Voor elke taal en elk Framework dat we bieden, vindt u Quick starts, zelf studies en API-verwijzingen waarmee u snel aan de slag kunt.

Azure biedt tal van services die u kunt gebruiken voor het hosten van websites en webtoepassingen. Met deze services kunt u ontwikkelen in uw favoriete taal, of dat nu .NET, .NET core, Java, Ruby, Node.js, PHP of python is.
[Azure App Service web apps](../../app-service/overview.md) (Web apps) is een van deze services.

Web Apps voegt de kracht van Microsoft Azure toe aan uw toepassing. Het bevat beveiliging, taak verdeling, automatisch schalen en geautomatiseerd beheer. U kunt ook profiteren van de DevOps-mogelijkheden in Web Apps, zoals pakket beheer, faserings omgevingen, aangepaste domeinen, SSL/TLS-certificaten en doorlopende implementatie vanuit Azure DevOps, GitHub, docker hub en andere bronnen.

Azure biedt andere services die u kunt gebruiken voor het hosten van websites en webtoepassingen. Voor de meeste scenario's is Web Apps de beste keuze. Voor een micro service-architectuur kunt u [Azure service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)overwegen.
Als u meer controle wilt over de virtuele machines waarop uw code wordt uitgevoerd, kunt u [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) overwegen.
Zie een [vergelijking van Azure app service, virtual machines, service Fabric en Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree)voor meer informatie over het kiezen van deze Azure-Services.

### <a name="apply-updates-to-components"></a>Updates Toep assen op onderdelen

Om beveiligings problemen te voor komen, moet u zowel uw onderdelen aan client zijde als server zijde (bijvoorbeeld frameworks en bibliotheken) en hun afhankelijkheden voor updates continu inventariseren. Nieuwe beveiligings problemen en bijgewerkte software versies worden continu vrijgegeven. Zorg ervoor dat u beschikt over een actief plan voor het bewaken, sorteren en Toep assen van updates of configuratie wijzigingen in de bibliotheken en onderdelen die u gebruikt.

Zie de pagina [OWASP (Web Application Security project) openen](https://www.owasp.org/index.php/Main_Page) voor het [gebruik van componenten met bekende beveiligings problemen](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) voor suggesties voor het hulp programma. U kunt zich ook abonneren op e-mail waarschuwingen voor beveiligings problemen die betrekking hebben op onderdelen die u gebruikt.

### <a name="use-threat-modeling-during-application-design"></a>Threatisatie modellen gebruiken tijdens het ontwerpen van toepassingen

Bedreigings modellering is het proces van het identificeren van mogelijke beveiligings Risico's voor uw bedrijf en toepassing en zorgt ervoor dat de juiste oplossingen worden uitgevoerd. SDL specificeert dat teams tijdens de ontwerp fase een bedreigings model moeten nemen, wanneer het oplossen van potentiële problemen relatief eenvoudig en rendabel is. Met het gebruik van bedreigings modellen in de ontwerp fase kunt u de totale ontwikkelings kosten aanzienlijk verlagen.

Ter ondersteuning van het proces van bedreigings modellering hebben we de [sdl-Threat Modeling tool](threat-modeling-tool.md) ontworpen met niet-beveiligings experts. Met dit hulp programma kunnen alle ontwikkel aars de bedreigings modellen vereenvoudigen door duidelijke richt lijnen te bieden voor het maken en analyseren van bedreigingen.

Het ontwerpen van het ontwerp van de toepassing en het opsommen van [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) Threats: vervalsing, knoeien, afwijzing, vrijgeven van informatie, denial of service en uitbrei ding van bevoegdheden: in alle vertrouwens grenzen is een doel matige manier bewezen om ontwerp fouten vroegtijdig op te vangen. De volgende tabel geeft een lijst van de STRIDE-bedreigingen en biedt enkele voor beelden van oplossingen die gebruikmaken van de functies van Azure. Deze oplossingen werken niet in elke situatie.

| Bedreiging | Beveiligings eigenschap | Potentieel beperking van het Azure-platform |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Adresvervalsing (spoofing)               | Verificatie        | [HTTPS-verbindingen vereisen](/aspnet/core/security/enforcing-ssl?tabs=visual-studio). |
| Manipulatie              | Integriteit             | SSL/TLS-certificaten valideren. Toepassingen die gebruikmaken van SSL/TLS moeten de X. 509-certificaten van de entiteiten waarmee ze verbinding maken, volledig controleren. Gebruik Azure Key Vault certificaten om [uw x509-certificaten te beheren](../../key-vault/general/about-keys-secrets-certificates.md). |
| Weerlegbaarheid            | Niet-weerlegbaarheid       | [Bewaking en diagnose](/azure/architecture/best-practices/monitoring)van Azure inschakelen.|
| Openbaarmaking van informatie | Vertrouwelijkheid       | Versleutel gevoelige gegevens in [rust](../fundamentals/encryption-atrest.md) en [onderweg](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Denial of Service      | Beschikbaarheid          | Bewaak de prestatie gegevens voor mogelijke denial of service-voor waarden. Verbindings filters implementeren. [Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md#next-steps), gecombineerd met aanbevolen procedures voor het ontwerpen van toepassingen, biedt bescherming tegen DDoS-aanvallen.|
| Verhoging van bevoegdheden | Autorisatie         | Gebruik Azure Active Directory <span class="underline"></span> [privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Verminder de kwets baarheid voor aanvallen

Een kwets bare kwets baarheid is de totale som van de mogelijke problemen die zich kunnen voordoen. In dit artikel richten we ons op de kwets baarheid van een toepassing.
Het is de focus om een toepassing te beschermen tegen aanvallen. Een eenvoudige en snelle manier om uw kwets baarheid te minimaliseren, is het verwijderen van ongebruikte resources en code uit uw toepassing. Hoe kleiner uw toepassing, hoe kleiner uw kwets baarheid. Verwijder bijvoorbeeld:

- Code voor functies die nog niet zijn vrijgegeven.
- Fout opsporings code.
- Netwerk interfaces en protocollen die niet worden gebruikt of die zijn afgeschaft.
- Virtuele machines en andere resources die u niet gebruikt.

U kunt regel matig uw resources opschonen en ervoor zorgen dat ongebruikte code op een goede manier wordt gebruikt om te garanderen dat er minder mogelijkheden zijn voor kwaad aardige aanvallen.

Een meer gedetailleerde en diep gaande manier om de kwets baarheid voor aanvallen te verminderen is door een kwets baarheid voor aanvallen te volt ooien. Een kwets baarheid voor aanvallen helpt u bij het toewijzen van de onderdelen van een systeem die moeten worden beoordeeld en getest op beveiligings problemen.

Het doel van een analyse van een kwets baarheid is om inzicht te krijgen in de risico gebieden in een toepassing, zodat ontwikkel aars en beveiligings specialisten weten welke onderdelen van de toepassing zijn geopend voor aanvallen. Vervolgens vindt u de manieren om dit mogelijk te minimaliseren, te volgen wanneer en hoe de kwets baarheid wordt gewijzigd en wat dit van invloed is op een risico perspectief.

Met een kwets baarheid voor aanvallen kunt u het volgende identificeren:

- Functies en onderdelen van het systeem die u moet controleren en testen op beveiligings problemen.
- Gebieden met een hoog risico voor code die ingrijpende beveiliging vereisen (onderdelen van het systeem dat u moet verdedigen).
- Wanneer u de kwets baarheid wijzigt en een bedreigings beoordeling moet vernieuwen.

Als u de mogelijkheden van aanvallers wilt beperken om misbruik te maken van een mogelijke zwakke plek of beveiligingslek, moet u de algehele kwets baarheid van uw toepassing grondig analyseren. Het omvat ook het uitschakelen of beperken van de toegang tot systeem services, het Toep assen van het principe van minimale bevoegdheden en waar mogelijk gelaagde verdediging.

We bespreken het [uitvoeren van een kwets baarheid voor een aanval](secure-develop.md#conduct-attack-surface-review) tijdens de verificatie fase van de SDL.

> [!NOTE]
> **Wat is het verschil tussen bedreigings modellering en kwets baarheid voor aanvallen?**
Bedreigings modellering is het proces van het identificeren van mogelijke beveiligings Risico's voor uw toepassing en ervoor zorgen dat de juiste oplossingen tegen de bedreigingen aanwezig zijn. Analyse van kwets baarheid duidt op gebieden met een hoog risico van code die open zijn om aan te vallen. Het omvat het vinden van manieren voor het beschermen van gebieden met een hoog risico voor uw toepassing en het controleren en testen van deze code gebieden voordat u de toepassing implementeert.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Een beleid met identiteit als de primaire beveiligings verbinding aannemen

Wanneer u Cloud toepassingen ontwerpt, is het belang rijk dat u de focus van de beveiligings omgeving uitbreidt van een netwerk gerichte benadering tot een identiteits gerichte aanpak. In het verleden was de primaire on-premises beveiligings verbinding het netwerk van een organisatie. De meeste on-premises beveiligings ontwerpen gebruiken het netwerk als de primaire beveiligings Pivot. Voor Cloud toepassingen is het beter om de identiteit als de primaire beveiligings verbinding te beschouwen.

Dingen die u kunt doen om een identiteits gerichte aanpak te ontwikkelen voor het ontwikkelen van webtoepassingen:

- Multi-factor Authentication afdwingen voor gebruikers.
- Gebruik krachtige platformen voor verificatie en autorisatie.
- Pas het principe van minimale bevoegdheden toe.
- Implementeer just-in-time-toegang.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Multi-factor Authentication afdwingen voor gebruikers

Gebruik twee ledige verificatie. Verificatie met twee factoren is de huidige standaard voor verificatie en autorisatie, omdat hiermee wordt voor komen dat de zwakke plekken in de beveiliging van gebruikers naam en wacht woord worden geauthenticeerd. Toegang tot de Azure-beheer interfaces (Azure Portal/Remote Power shell) en aan klant gerichte services moeten zijn ontworpen en geconfigureerd voor het gebruik van [Azure multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Sterke verificatie-en autorisatie platforms gebruiken

Gebruik platform verificatie-en autorisatie mechanismen in plaats van aangepaste code. Dit komt doordat het ontwikkelen van aangepaste verificatie code gevoelig voor fouten kan zijn. Commerciële code (bijvoorbeeld van micro soft) wordt vaak uitgebreid gecontroleerd op beveiliging. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) is de Azure-oplossing voor identiteits-en toegangs beheer. Deze Azure AD-hulpprogram ma's en-services helpen bij veilige ontwikkeling:

- [Micro soft Identity platform](../../active-directory/develop/index.yml) is een set onderdelen die ontwikkel aars gebruiken om apps te bouwen die veilig gebruikers kunnen aanmelden. Het platform helpt ontwikkel aars die een LOB-app (line-of-Business) en ontwikkel aars van één Tenant bouwen die apps voor meerdere tenants willen ontwikkelen. Apps die zijn gebouwd met behulp van het micro soft Identity-platform kunnen naast de eenvoudige aanmelding ook micro soft-Api's en aangepaste Api's aanroepen. Het micro soft-identiteits platform ondersteunt industrie-standaard protocollen zoals OAuth 2,0 en OpenID Connect Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) is een service voor identiteits beheer die u kunt gebruiken voor het aanpassen en bepalen van de manier waarop gebruikers zich aanmelden, aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken. Dit geldt ook voor toepassingen die zijn ontwikkeld voor iOS, Android en .NET, onder andere. Azure AD B2C maakt deze acties mogelijk tijdens het beveiligen van de identiteiten van klanten.

#### <a name="apply-the-principle-of-least-privilege"></a>Het principe van minimale bevoegdheden Toep assen

Het concept van de [minimale bevoegdheid](https://en.wikipedia.org/wiki/Principle_of_least_privilege) betekent dat gebruikers het nauw keurige toegangs niveau en het beheer kunnen geven dat ze nodig hebben om hun taken uit te voeren en niets meer te doen.

Moet een software ontwikkelaar domein beheerders rechten hebben? Moet een administratief mede werker toegang hebben tot de besturings elementen voor beheer op hun personal computer? Het evalueren van de toegang tot de software is niet anders. Als u op [rollen gebaseerd toegangs beheer (Azure RBAC) van Azure](../../role-based-access-control/overview.md) gebruikt om gebruikers verschillende vaardig heden en instanties in uw toepassing te geven, geeft u iedereen geen toegang tot alles. Door de toegang te beperken tot wat vereist is voor elke rol, beperkt u het risico van een beveiligings probleem dat optreedt.

Zorg ervoor dat uw toepassing [minimale bevoegdheden](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) afdwingt in de bijbehorende toegangs patronen.

> [!NOTE]
> De regels van de minimale bevoegdheid moeten worden toegepast op de software en aan de personen die de software maken. Software ontwikkelaars kunnen een zeer risico op IT-beveiliging zijn als ze te veel toegang krijgen. De gevolgen kunnen ernstig zijn als een ontwikkelaar kwaad aardige intentie heeft of te veel toegang heeft gekregen. We raden aan dat de regels van de minimale bevoegdheid worden toegepast op ontwikkel aars tijdens de ontwikkelings levenscyclus.

#### <a name="implement-just-in-time-access"></a>Just-in-time-toegang implementeren

Implementeer just *-in-time* -toegang (JIT) om de belichtings tijd van bevoegdheden verder te verlagen. Gebruik [Azure AD privileged Identity Management](../../active-directory/roles/security-planning.md#stage-3-take-control-of-admin-activity) voor het volgende:

- Geef gebruikers de machtigingen die ze alleen JIT nodig hebben.
- Wijs rollen toe voor een verkorte duur met vertrouwen dat de bevoegdheden automatisch worden ingetrokken.

### <a name="require-re-authentication-for-important-transactions"></a>Nieuwe verificatie vereisen voor belang rijke trans acties

[Cross-site-aanvraag vervalsing](/aspnet/core/security/anti-request-forgery) (ook wel bekend als *XSRF* of *CSRF*) is een aanval voor webhostende apps waarin een schadelijke Web-App de interactie beïnvloedt tussen een client browser en een web-app die die browser vertrouwt. Aanvallen op aanvraag vervalsing op meerdere sites zijn mogelijk omdat webbrowsers sommige typen verificatie tokens automatisch laten verzenden met elke aanvraag naar een website.
Deze vorm van exploitatie is ook bekend als een aanval of *sessie* met *één klik* , omdat de aanval de eerder geauthenticeerde sessie van de gebruiker benut.

De beste manier om dit soort aanvallen te tegen komen, is door de gebruiker te vragen om iets dat alleen de gebruiker kan opgeven vóór elke belang rijke trans actie, zoals een aankoop, het deactiveren van accounts of het wijzigen van een wacht woord. U kunt de gebruiker vragen om het wacht woord opnieuw in te voeren, een CAPTCHA te volt ooien of een geheim token in te dienen dat alleen de gebruiker zou hebben. De meest voorkomende benadering is het geheime token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Een oplossing voor sleutel beheer gebruiken om sleutels, referenties en andere geheimen te beveiligen

Het verlies van sleutels en referenties is een veelvoorkomend probleem. Het enige wat u nog ergert dan het verlies van uw sleutels en referenties is dat een niet-geautoriseerde partij er toegang toe krijgt. Aanvallers kunnen gebruikmaken van geautomatiseerde en hand matige technieken om sleutels en geheimen te vinden die zijn opgeslagen in code opslagplaatsen zoals GitHub. Plaats geen sleutels en geheimen in deze open bare code opslagplaatsen of op een andere server.

Plaats altijd uw sleutels, certificaten, geheimen en verbindings reeksen in een oplossing voor sleutel beheer. U kunt een gecentraliseerde oplossing gebruiken waarin sleutels en geheimen worden opgeslagen in Hardware Security modules (Hsm's). Azure biedt u een HSM in de Cloud met [Azure Key Vault](../../key-vault/general/overview.md).

Key Vault is een *geheim archief*: het is een gecentraliseerde Cloud service voor het opslaan van toepassings geheimen. Key Vault uw vertrouwelijke gegevens veilig houdt door toepassings geheimen op één centrale locatie te bewaren en beveiligde toegang, machtigings beheer en logboek registratie van toegang te bieden.

Geheimen worden opgeslagen in afzonderlijke *kluizen*. Elke kluis heeft zijn eigen configuratie-en beveiligings beleid om de toegang te beheren. U krijgt toegang tot uw gegevens via een REST API of via een client-SDK die beschikbaar is voor de meeste programmeer talen.

> [!IMPORTANT]
> Azure Key Vault is ontworpen om configuratie geheimen voor server toepassingen op te slaan. Het is niet bedoeld voor het opslaan van gegevens die bij app-gebruikers horen. Dit wordt weerspiegeld in de prestatiekenmerken, de API en het kostenmodel.
>
> Gebruikers gegevens moeten elders worden opgeslagen, zoals in een Azure SQL Database-exemplaar met Transparent Data Encryption (TDE) of in een opslag account dat gebruikmaakt van Azure Storage-service versleuteling. Geheimen die door uw toepassing worden gebruikt voor toegang tot deze gegevens archieven, kunnen worden bewaard in Azure Key Vault.

### <a name="protect-sensitive-data"></a>Gevoelige gegevens beveiligen

Het beveiligen van gegevens is een essentieel onderdeel van uw beveiligings strategie.
Het classificeren van uw gegevens en het identificeren van uw behoeften voor gegevens beveiliging helpt u bij het ontwerpen van uw app met gegevens beveiliging. Het classificeren (categoriseren) van opgeslagen gegevens door middel van gevoeligheid en bedrijfs impact helpt ontwikkel aars bij het bepalen van de Risico's die aan gegevens zijn gekoppeld.

Voorzie alle toepasselijke gegevens als vertrouwelijk wanneer u uw gegevens indelingen ontwerpt. Zorg ervoor dat de toepassing de toepasselijke gegevens als vertrouwelijk behandelt. Deze procedures kunnen u helpen bij het beveiligen van uw gevoelige gegevens:

- Versleuteling gebruiken.
- Vermijd het gebruik van geheime code ringen, zoals sleutels en wacht woorden.
- Zorg ervoor dat toegangs beheer en controle zijn geïmplementeerd.

#### <a name="use-encryption"></a>Versleuteling gebruiken

Het beveiligen van gegevens moet een essentieel onderdeel zijn van uw beveiligings strategie.
Als uw gegevens worden opgeslagen in een Data Base of als deze tussen locaties worden teruggezet, gebruikt u versleuteling van [gegevens in rust](../fundamentals/encryption-atrest.md) (in de data base) en versleuteling van [gegevens die onderweg](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) zijn (op de manier van en naar de gebruiker, de data base, een API of service-eind punt). U wordt aangeraden altijd SSL/TLS-protocollen te gebruiken om gegevens uit te wisselen. Zorg ervoor dat u de meest recente versie van TLS gebruikt voor versleuteling (momenteel is dit versie 1,2).

#### <a name="avoid-hard-coding"></a>Geen vaste code

Sommige dingen mogen nooit in uw software worden vastgelegd. Enkele voor beelden zijn hostnamen of IP-adressen, Url's, e-mail adressen, gebruikers namen, wacht woorden, sleutels voor het opslag account en andere cryptografische sleutels. Overweeg de implementatie van vereisten om te bepalen wat er in uw code kan of niet kan worden vastgelegd, zoals in de sectie opmerkingen van uw code.

Als u opmerkingen in uw code plaatst, moet u ervoor zorgen dat u geen gevoelige informatie opslaat. Dit geldt ook voor uw e-mail adres, wacht woorden, verbindings reeksen, informatie over uw toepassing die alleen bekend zijn bij iemand in uw organisatie, en alles wat een aanvaller kan geven om uw toepassing of organisatie te gebruiken.

In principe wordt ervan uitgegaan dat alles in uw ontwikkelings project openbaar kennis is wanneer het is geïmplementeerd. Vermijd het opnemen van gevoelige gegevens van elk soort in het project.

Eerder zijn we besproken [Azure Key Vault](../../key-vault/general/overview.md). U kunt Key Vault gebruiken om geheimen zoals sleutels en wacht woorden op te slaan in plaats van ze vast te schrijven. Wanneer u Key Vault gebruikt in combi natie met beheerde identiteiten voor Azure-resources, kan uw Azure-web-app eenvoudig en veilig toegang krijgen tot geheime configuratie waarden zonder geheimen in uw bron beheer of configuratie op te slaan. Zie [geheimen beheren in uw server-apps met Azure Key Vault](/learn/modules/manage-secrets-with-azure-key-vault/)voor meer informatie.

### <a name="implement-fail-safe-measures"></a>Fout veilige maat regelen implementeren

Uw toepassing moet in staat zijn om [fouten](/dotnet/standard/exceptions/) die tijdens de uitvoering optreden, op een consistente manier te verwerken. De toepassing moet alle fouten opvangen en een veilige of gesloten fout hebben.

U moet er ook voor zorgen dat fouten worden geregistreerd met voldoende gebruikers context om verdachte of schadelijke activiteiten te identificeren. Logboeken moeten voldoende tijd worden bewaard om vertraagde forensische-analyse mogelijk te maken. Logboeken moeten een indeling hebben die eenvoudig kan worden gebruikt met een oplossing voor logboek beheer. Zorg ervoor dat er waarschuwingen worden gegenereerd voor fouten die betrekking hebben op beveiliging. Door ontoereikende logboek registratie en controle kunnen aanvallers systemen verder aanvallen en persistentie onderhouden.

### <a name="take-advantage-of-error-and-exception-handling"></a>Profiteer van de afhandeling van fouten en uitzonde ringen

Het implementeren van de juiste fout-en [uitzonderings afhandeling](/dotnet/standard/exceptions/best-practices-for-exceptions) is een belang rijk onderdeel van de code voor verdediging. Het verwerken van fouten en uitzonde ringen is van cruciaal belang om een systeem betrouwbaar en beveiligd te maken. Fouten bij het afhandelen van fouten kunnen leiden tot verschillende soorten beveiligings problemen, zoals het lekken van informatie aan kwaadwillende personen en het helpen van kwaadwillende personen meer inzicht te krijgen in uw platform en ontwerp.

Zorg ervoor dat:

- U kunt uitzonde ringen op een gecentraliseerde manier afhandelen om dubbele [try/catch-blokken](/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) in de code te voor komen.

- Alle onverwachte gedragingen worden in de toepassing afgehandeld.

- Berichten die voor gebruikers worden weer gegeven, lekken geen essentiële gegevens maar bieden wel voldoende informatie om het probleem te verklaren.

- Uitzonde ringen worden vastgelegd en er wordt voldoende informatie geboden voor forensische of reacties op incidenten die kunnen worden onderzocht.

[Azure Logic apps](../../logic-apps/logic-apps-overview.md) biedt een eersteklas ervaring voor het [afhandelen van fouten en uitzonde ringen](../../logic-apps/logic-apps-exception-handling.md) die worden veroorzaakt door afhankelijke systemen. U kunt Logic Apps gebruiken om werk stromen te maken voor het automatiseren van taken en processen die apps, gegevens, systemen en services integreren in ondernemingen en organisaties.

### <a name="use-logging-and-alerting"></a>Logboek registratie en waarschuwingen gebruiken

[Registreer](/aspnet/core/fundamentals/logging/) uw beveiligings problemen voor beveiligings onderzoeken en activeer waarschuwingen over problemen om ervoor te zorgen dat mensen tijdig op de hoogte zijn van problemen. Schakel controle en logboek registratie in voor alle onderdelen. In controle logboeken moet de gebruikers context worden vastgelegd en alle belang rijke gebeurtenissen worden geïdentificeerd.

Controleer of u geen gevoelige gegevens in het logboek registreert die een gebruiker naar uw site verzendt. Voor beelden van gevoelige gegevens zijn:

- Gebruikers referenties
- Sofi-nummers of andere identificerende informatie
- Creditcard nummers of andere financiële gegevens
- Status informatie
- Persoonlijke sleutels of andere gegevens die kunnen worden gebruikt voor het ontsleutelen van versleutelde informatie
- Systeem-of toepassings informatie die kan worden gebruikt om de toepassing effectiever te aanvallen

Zorg ervoor dat de toepassing gebruikers beheer gebeurtenissen bewaakt, zoals geslaagde en mislukte gebruikers aanmeldingen, wacht woorden opnieuw instellen, wachtwoord wijzigingen, account vergrendeling en gebruikers registratie. Logboek registratie voor deze gebeurtenissen helpt u mogelijk verdacht gedrag te detecteren en hierop te reageren. U kunt hiermee ook operationele gegevens verzamelen, zoals wie toegang heeft tot de toepassing.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen raden wij aan beveiligings maatregelen en activiteiten te gebruiken die u kunnen helpen bij het ontwikkelen en implementeren van veilige toepassingen.

- [Veilige toepassingen ontwikkelen](secure-develop.md)
- [Veilige toepassingen implementeren](secure-deploy.md)