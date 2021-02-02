---
title: Cloud-HR-toepassing plannen voor Azure Active Directory gebruikers inrichting
description: In dit artikel wordt het implementatie proces beschreven voor de integratie van Cloud-HR-systemen, zoals workday en SuccessFactors, met Azure Active Directory. Het integreren van Azure AD met uw Cloud-HR-systeem resulteert in een volledig identiteits levenscyclus beheer systeem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9c896d4cccf898b8818b4c363c5bc891a8734ca5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256708"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Cloud-HR-toepassing plannen voor Azure Active Directory gebruikers inrichting

In het verleden gebruikten IT-medewerkers handmatige methoden voor het maken, bijwerken en verwijderen van medewerkers. Ze gebruikten methoden zoals het uploaden van CSV-bestanden of aangepaste scripts om gegevens van werknemers te synchroniseren. Deze inrichtingsprocessen zijn foutgevoelig, onveilig en moeilijk te beheren.

Voor het beheren van de identiteitslevenscycli van werknemers, leveranciers of tijdelijke werknemers, biedt de [Azure Active Directory (Azure AD)-gebruikersinrichtingsservice](../app-provisioning/user-provisioning.md) integratie met human resources (HR)-toepassingen in de cloud. Voorbeelden van toepassingen zijn Workday of SuccessFactors.

Azure AD gebruikt deze integratie om de volgende workflows voor de HR-toepassing (app) in de cloud in te schakelen:

- **Gebruikers inrichten op Active Directory:** U kunt bepaalde sets gebruikers van een HR-app in de cloud in een of meer Active Directory-domeinen inrichten.
- **Alleen-cloud gebruikers inrichten op Azure AD:** In scenario's waarin Active Directory niet wordt gebruikt, dient u gebruikers rechtstreeks vanuit de cloud-HR-app in te richten op Azure AD.
- **Terugschrijven naar de cloud-HR-app:** Schrijf de e-mailadressen en de kenmerken van de gebruikersnamen van Azure AD terug naar de cloud-HR-app.

> [!NOTE]
> Dit implementatie plan laat zien hoe u uw workflows voor de werk stroom van de cloud kunt implementeren met Azure AD-gebruikers inrichting. Zie [een automatische gebruikers inrichting plannen](./plan-auto-user-provisioning.md)voor meer informatie over het implementeren van automatische gebruikers inrichting voor SaaS-apps (Software as a Service).

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
- Gebruik Microsoft 365 voor e-mail.

## <a name="learn"></a>Learn

Gebruikers inrichten maakt een basis voor de voortdurende identiteits bestuur. Het verbetert de kwaliteit van bedrijfs processen die afhankelijk zijn van gezaghebbende identiteits gegevens.

### <a name="terms"></a>Termen

In dit artikel worden de volgende termen gebruikt:

- **Bron systeem**: de opslag plaats van gebruikers die Azure AD-voorzieningen van hebben. Een voor beeld is een Cloud-HR-app, zoals workday of SuccessFactors.
- **Doel systeem**: de opslag plaats van gebruikers die door de Azure AD worden ingericht. Voor beelden zijn Active Directory, Azure AD, Microsoft 365 of andere SaaS-apps.
- Invoeg toepassingen **-verwerkers**: een term die wordt gebruikt voor nieuwe aanstellingen, overdrachten en beëindiging door gebruik te maken van een Cloud-HR-app als systeem van records. Het proces wordt voltooid wanneer de vereiste kenmerken door de service zijn ingericht naar het doel systeem.

### <a name="key-benefits"></a>Belangrijkste voordelen

Deze mogelijkheid van HR-aangestuurde IT-inrichting biedt de volgende belangrijke zakelijke voordelen:

- **Verhoog de productiviteit:** U kunt nu de toewijzing van gebruikers accounts en Microsoft 365 licenties automatiseren en toegang verlenen tot sleutel groepen. Als u toewijzingen automatiseert, krijgen nieuwe medewerkers direct toegang tot de hulpprogramma's voor hun werk en wordt de productiviteit verhoogd.
- **Risico’s beheren:** U kunt de beveiliging verbeteren door wijzigingen te automatiseren op basis van de status van werknemers of groepslidmaatschappen met gegevens die binnenstromen vanuit de cloud-HR-app. Als u wijzigingen automatiseert, zorgt u ervoor dat gebruikersidentiteiten en toegang tot belangrijke apps automatisch worden bijgewerkt wanneer gebruikers overstappen of de organisatie verlaten.
- **Naleving en governance aanpakken:** Azure AD biedt ondersteuning voor systeemeigen auditlogboeken voor aanvragen voor gebruikersinrichting die worden uitgevoerd door apps van zowel de bron- als doelsystemen. Tijdens het controleren kunt u vanaf één scherm bijhouden wie toegang heeft tot de apps.
- **Kosten beheren:** Automatische inrichting vermindert de kosten door inefficiëntie en menselijke fouten te voorkomen die gerelateerd zijn aan handmatige inrichting. Het vermindert de behoefte aan aangepaste, op maat ontwikkelde oplossingen voor gebruikersinrichting die in de loop van de tijd zijn gebouwd met behulp van verouderde platforms.

### <a name="licensing"></a>Licentieverlening

Als u de Cloud-HR-app wilt configureren voor integratie met Azure AD-gebruikers, hebt u een geldige [Azure AD Premium licentie](https://azure.microsoft.com/pricing/details/active-directory/) en een licentie voor de werk-app in de Cloud nodig, zoals workday of SuccessFactors.

U hebt ook een geldige licentie voor Azure AD Premium P1 of hoger nodig voor elke gebruiker die vanuit de Cloud-app wordt gebrond en is ingericht voor Active Directory of Azure AD. Een onjuist aantal licenties dat eigendom is van de Cloud HR-app kan leiden tot fouten tijdens het inrichten van de gebruiker.

### <a name="prerequisites"></a>Vereisten

- Azure AD [Hybrid Identity Administrator](../roles/permissions-reference.md#hybrid-identity-administrator)  to configure the Azure AD Connect inrichtings agent.
- De rol van de Azure AD- [toepassings beheerder](../roles/permissions-reference.md#application-administrator) voor het configureren van de inrichtings-app in de Azure Portal
- Een test-en productie-exemplaar van de Cloud-HR-app.
- Beheerders machtigingen in de Cloud HR-app om een systeem integratie gebruiker te maken en wijzigingen aan te brengen voor het testen van werknemers gegevens voor test doeleinden.
- Voor het inrichten van gebruikers aan Active Directory is een server met Windows Server 2012 of hoger met .NET 4.7.1 + runtime vereist voor het hosten van de Azure AD Connect-inrichtings agent
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) voor het synchroniseren van gebruikers tussen Active Directory en Azure AD.

### <a name="training-resources"></a>Trainings bronnen

| **Bronnen** | **Koppeling en beschrijving** |
|:-|:-|
| Video's | [Wat is gebruikers inrichten in Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Het inrichten van gebruikers in Active Azure Directory implementeren](https://youtu.be/pKzyts6kfrw) |
| Zelfstudies | [Lijst met zelfstudies over het integreren van SaaS-apps met Azure AD](../saas-apps/tutorial-list.md) |
| | [Zelfstudie: Workday configureren voor automatisch inrichten van gebruikers](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Veelgestelde vragen | [Automatische gebruikers inrichting](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Inrichten van workday naar Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architectuur voor de oplossing

In het volgende voor beeld wordt de end-to-end-oplossings architectuur voor gebruikers ingericht voor veelvoorkomende hybride omgevingen en bevat:

- **Gezaghebbende HR-gegevens stroom vanuit de Cloud HR-app naar Active Directory.** In deze stroom wordt de HR-gebeurtenis (samen voegers-het proces voor de Leavers) gestart in de app-Tenant voor HR-apps. De Azure AD Provisioning-Service en Azure AD Connect inrichtings agent richten de gebruikers gegevens in van de Tenant van de HR-app van de cloud in Active Directory. Afhankelijk van de gebeurtenis kan dit leiden tot het maken, bijwerken, inschakelen en uitschakelen van bewerkingen in Active Directory.
- **Synchroniseer met Azure AD en schrijf e-mail en gebruikers naam van on-premises Active Directory naar een Cloud-app.** Nadat de accounts zijn bijgewerkt in Active Directory, wordt deze gesynchroniseerd met Azure AD via Azure AD Connect. De kenmerken van het e-mail adres en de gebruikers naam kunnen worden teruggeschreven naar de Tenant voor de Cloud-app HR.

![Werk stroom diagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Beschrijving van werk stroom

De volgende belang rijke stappen worden in het diagram aangegeven:  

1. **HR-team** voert de trans acties uit in de Cloud HR app-Tenant.
2. De **Azure AD-inrichtings service** voert de geplande cycli uit van de Tenant van de HR-app in de Cloud en identificeert wijzigingen die moeten worden verwerkt om te synchroniseren met Active Directory.
3. De **Azure AD-inrichtings service** roept de Azure AD Connect-inrichtings agent aan met een aanvraag lading die Active Directory account maken, bijwerken, inschakelen en uitschakelen heeft.
4. **Azure AD Connect-inrichtings agent** gebruikt een service account voor het beheren van Active Directory account gegevens.
5. **Azure AD Connect** voert Delta [synchronisatie](../hybrid/how-to-connect-sync-whatis.md) uit voor het ophalen van updates in Active Directory.
6. **Active Directory** updates worden gesynchroniseerd met Azure AD.
7. **Azure AD Provisioning Service** schrijft back-ups van het e-mail kenmerk en de gebruikers naam van Azure AD naar de app-TENANT voor HR-apps.

## <a name="plan-the-deployment-project"></a>Het implementatie project plannen

Houd rekening met de behoeften van uw organisatie terwijl u de strategie voor deze implementatie in uw omgeving bepaalt.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans als gevolg van niet-overeenkomende verwachtingen wat betreft impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](../fundamentals/active-directory-deployment-plans.md). Zorg er ook voor dat de belanghebbende rollen in het project goed zijn begrepen. Documenteer de belanghebbenden en hun project invoer en accountabilities.

Neem een vertegenwoordiger van de HR-organisatie op die invoer kan bieden aan bestaande HR-bedrijfs processen en worker-identiteit plus taak verwerkings vereisten.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Communiceer proactief met uw gebruikers over wanneer en hoe hun ervaring wordt gewijzigd. Laat ze weten hoe ze ondersteuning kunnen krijgen als ze problemen ondervinden.

### <a name="plan-a-pilot"></a>Een pilot plannen

Voor het integreren van bedrijfs processen en identiteits werk stromen van de Cloud HR-app naar doel systemen is een aanzienlijke hoeveelheid gegevens validatie, gegevens transformatie, het opschonen van gegevens en end-to-end-tests vereist voordat u de oplossing in productie kunt implementeren.

Voer de eerste configuratie uit in een [test omgeving](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) voordat u deze schaalt naar alle gebruikers in productie.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Apps voor de inrichtings connector voor Clouds selecteren

Als u Azure AD-inrichtings werk stromen tussen de Cloud HR-app en Active Directory wilt vereenvoudigen, kunt u meerdere apps voor het inrichten van een connector toevoegen via de Azure AD-App-galerie:

- **Cloud-HR-app voor het Active Directory van gebruikers inrichten**: deze provisioning connector-app vereenvoudigt het inrichten van gebruikers accounts vanuit de HR-app in één Active Directory domein. Als u meerdere domeinen hebt, kunt u één exemplaar van deze app toevoegen vanuit de Azure AD-App-galerie voor elk Active Directory domein dat u wilt inrichten.
- **Cloud HR-app naar Azure AD-gebruikers inrichten**: Hoewel Azure AD Connect het hulp programma is dat moet worden gebruikt om Active Directory gebruikers te synchroniseren met Azure AD, kan deze app voor de inrichtings connector worden gebruikt om het inrichten van Cloud gebruikers vanuit de HR-app naar één Azure AD-Tenant te vergemakkelijken.
- **Cloud-HR-app write-back**: deze provisioning connector-app vereenvoudigt de write-back van de e-mail adressen van de gebruiker van Azure AD naar de Cloud-app.

In de volgende afbeelding ziet u bijvoorbeeld de workday-connector-apps die beschikbaar zijn in de Azure AD-App-galerie.

![Galerie met apps voor Azure Active Directory Portal](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Diagram van de beslissings stroom

Gebruik de volgende beslissings stroom om te bepalen welke Clouds voor de inrichtings-app van uw afdeling relevant zijn voor uw scenario.

![Diagram van de beslissings stroom](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>De implementatie topologie voor de Azure AD Connect-inrichtings agent ontwerpen

De inrichtings integratie tussen de Cloud HR-app en Active Directory vereist vier onderdelen:

- Cloud HR app-Tenant
- Connector-app inrichten
- Inrichtings agent Azure AD Connect
- Active Directory-domein

De implementatie topologie voor de Azure AD Connect-inrichtings agent is afhankelijk van het aantal tenants in de Cloud-app voor apps en Active Directory onderliggende domeinen die u wilt integreren. Als u meerdere Active Directory domeinen hebt, is het afhankelijk van het feit of de Active Directory domeinen aaneengesloten of [gescheiden](/windows-server/identity/ad-ds/plan/disjoint-namespace)zijn.

Kies op basis van uw beslissing een van de implementatie scenario's:

- Tenant-> voor één Cloud-app met één of meerdere Active Directory onderliggende domeinen in een vertrouwd forest
- Tenant-> voor één Cloud-app met meerdere onderliggende domeinen in een niet-aaneengesloten Active Directory forest

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Tenant-> voor één Cloud-app met één of meerdere Active Directory onderliggende domeinen in een vertrouwd forest

We raden u aan de volgende productie configuratie te configureren:

|Vereiste|Aanbeveling|
|:-|:-|
|Aantal Azure AD Connect inrichten agents om te implementeren|Twee (voor hoge Beschik baarheid en failover)
|Aantal te configureren apps voor de inrichtings connector|Eén app per onderliggend domein|
|Server host voor Azure AD Connect-inrichtings agent|Windows 2012 R2 + met een regel van het gezichts vermogen naar geolocatie Active Directory domein controllers</br>Kan samen met Azure AD Connect service worden gecombineerd|

![Stroom naar on-premises agents](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Tenant-> voor één Cloud-app met meerdere onderliggende domeinen in een niet-aaneengesloten Active Directory forest

In dit scenario moet u gebruikers in de Cloud-HR-app inrichten voor domeinen in niet-aaneengesloten Active Directory forests.

We raden u aan de volgende productie configuratie te configureren:

|Vereiste|Aanbeveling|
|:-|:-|
|Aantal Azure AD Connect inrichten agents om on-premises te implementeren|Twee per niet-aaneengesloten Active Directory forest|
|Aantal te configureren apps voor de inrichtings connector|Eén app per onderliggend domein|
|Server host voor Azure AD Connect-inrichtings agent|Windows 2012 R2 + met een regel van het gezichts vermogen naar geolocatie Active Directory domein controllers</br>Kan samen met Azure AD Connect service worden gecombineerd|

![Niet-aaneengesloten app-Tenant voor een enkele Cloud Active Directory-forest](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Agent vereisten voor Azure AD Connect inrichten

Voor de Cloud HR-app voor het Active Directory van de oplossing voor het inrichten van gebruikers is vereist dat u een of meer Azure AD Connect-inrichtings agenten implementeert op servers waarop Windows 2012 R2 of hoger wordt uitgevoerd. De servers moeten mini maal 4 GB RAM-geheugen en .NET 4.7.1 + runtime hebben. Zorg ervoor dat de hostserver netwerk toegang heeft tot het doel Active Directory domein.

Om de on-premises omgeving voor te bereiden, wordt met de wizard agent configureren van Azure AD Connect de agent geregistreerd bij uw Azure AD-Tenant, worden [poorten geopend](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [krijgt u toegang tot url's](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)en worden de [uitgaande HTTPS-proxy configuratie](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)ondersteund.

De inrichtings agent gebruikt een service account om te communiceren met de Active Directory domeinen. Voordat u de Agent installeert, maakt u een service account in Active Directory gebruikers en computers die aan de volgende vereisten voldoen:

- Een wacht woord dat niet verloopt
- Gemachtigde beheer machtigingen voor het lezen, maken, verwijderen en beheren van gebruikers accounts

U kunt domein controllers selecteren die inrichtings aanvragen moeten verwerken. Als u meerdere geografisch gedistribueerde domein controllers hebt, installeert u de inrichtings agent op dezelfde site als uw voorkeurs domein controllers. Deze positie verbetert de betrouw baarheid en prestaties van de end-to-end-oplossing.

Voor maximale Beschik baarheid kunt u meer dan één Azure AD Connect inrichtings agent implementeren. Registreer de agent om dezelfde set on-premises Active Directory domeinen te verwerken.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Bereik filters en kenmerk toewijzing plannen

Wanneer u het inrichten van de Cloud-app naar Active Directory of Azure AD inschakelt, beheert de Azure Portal de kenmerk waarden via kenmerk toewijzing.

### <a name="define-scoping-filters"></a>Bereik filters definiëren

Gebruik [bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) om de op kenmerken gebaseerde regels te definiëren waarmee wordt bepaald welke gebruikers moeten worden ingericht vanuit de app HR naar Active Directory of Azure AD.

Wanneer u het proces voor samen voegers start, moet u de volgende vereisten verzamelen:

- Is de Cloud-app die wordt gebruikt om werk nemers en voorwaardelijke werk nemers aan boord te brengen?
- Wilt u de Cloud HR-app gebruiken voor het inrichten van Azure AD-gebruikers om zowel werk nemers als voorwaardelijke werk nemers te beheren?
- Wilt u de implementatie van de Cloud-app naar Azure AD-gebruikers alleen inrichten voor een subset van de gebruikers van de Cloud-HR-app? Een voor beeld is mogelijk alleen werk nemers.

Afhankelijk van uw vereisten kunt u, wanneer u kenmerk toewijzingen configureert, het bereik van het **bron object** instellen om te selecteren welke gebruikers sets in de Cloud-app in het bereik moeten worden ingericht voor het inrichten van Active Directory. Zie voor meer informatie de zelf studie Cloud HR-app voor veelgebruikte bereik filters.

### <a name="determine-matching-attributes"></a>Overeenkomende kenmerken bepalen

Bij het inrichten beschikt u over de mogelijkheid om bestaande accounts te koppelen aan het bron-en doel systeem. Wanneer u de Cloud HR-app integreert met de Azure AD-inrichtings service, kunt u [kenmerk toewijzing configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) om te bepalen welke gebruikers gegevens moeten stromen van de HR-app naar Active Directory of Azure AD.

Wanneer u het proces voor samen voegers start, moet u de volgende vereisten verzamelen:

- Wat is de unieke ID in deze Cloud-HR-app die wordt gebruikt voor het identificeren van elke gebruiker?
- Hoe verwerkt u met een levens cyclus voor identiteiten? Blijven de oude werk nemer-Id's worden gehuurd?
- Verwerkt u toekomstig gehuurde mede werkers en maakt u Active Directory accounts vooraf.
- Hoe kunt u vanuit een perspectief voor de levens cyclus van de identiteit werk nemers omzetten naar conversies van een voorwaardelijke werk nemer of anderszins?
- Blijven de oude Active Directory accounts in geconverteerde gebruikers?

Afhankelijk van uw vereisten ondersteunt Azure AD directe toewijzing van kenmerk naar kenmerk door constante waarden te bieden of expressies te [schrijven voor kenmerk toewijzingen](../app-provisioning/functions-for-customizing-application-data.md). Deze flexibiliteit biedt u de ultieme controle over wat er wordt ingevuld in het kenmerk van de doel toepassing. U kunt de [Microsoft Graph-API](../app-provisioning/export-import-provisioning-configuration.md) en Graph Explorer gebruiken om de kenmerk toewijzingen en het schema voor het inrichten van gebruikers te exporteren naar een JSON-bestand en dit weer te importeren in azure AD.

Het kenmerk in de Cloud-HR-app dat de unieke werk nemer-ID vertegenwoordigt, wordt standaard gebruikt als het overeenkomende kenmerk dat is *toegewezen aan het unieke kenmerk in Active Directory.* In het scenario van de werkdag-app wordt bijvoorbeeld het **WorkerID** kenmerk **workday** toegewezen aan het kenmerk Active Directory **employeeID** .

U kunt meerdere overeenkomende kenmerken instellen en overeenkomende prioriteit toewijzen. Ze worden geëvalueerd op volg orde van overeenkomst. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

U kunt ook [de standaard kenmerk toewijzingen aanpassen](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types), zoals het wijzigen of verwijderen van bestaande kenmerk toewijzingen. U kunt ook nieuwe kenmerk toewijzingen maken op basis van de behoeften van uw bedrijf. Zie voor meer informatie de zelf studie over Cloud-HR-apps (zoals [workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)) voor een lijst met aangepaste kenmerken die u wilt toewijzen.

### <a name="determine-user-account-status"></a>De status van het gebruikers account bepalen

Standaard wordt de status van het gebruikers profiel voor de inrichtings connector toegewezen aan de status van het gebruikers account in Active Directory of Azure AD om te bepalen of het gebruikers account moet worden in-of uitgeschakeld.

Wanneer u het Joiners-Leavers proces initieert, moet u de volgende vereisten verzamelen.

| Proces | Vereisten |
| - | - |
| **Samen voegers** | Hoe verwerkt u met een levens cyclus voor identiteiten? Blijven de oude werk nemer-Id's worden gehuurd? |
| | Verwerkt u toekomstig gehuurde mede werkers en maakt u Active Directory accounts vooraf. Zijn deze accounts gemaakt met de status ingeschakeld of uitgeschakeld? |
| | Hoe kunt u vanuit een perspectief voor de levens cyclus van de identiteit werk nemers omzetten naar conversies van een voorwaardelijke werk nemer of anderszins? |
| | Blijven de oude Active Directory accounts in geconverteerde gebruikers staan of worden ze nieuw? |
| **Leavers** | Zijn er afsluitingen voor werk nemers en voorwaardelijke werk rollen in Active Directory? |
| | Welke ingangs datums worden er in rekening gehouden voor het verwerken van gebruikers beëindiging? |
| | Hoe beïnvloeden de conversies van werk nemers en voorwaardelijke werk nemers de bestaande Active Directory accounts? |
| | Hoe verwerkt u de Rescind-bewerking in Active Directory? Rescind-bewerkingen moeten worden verwerkt als toekomstige gehuurde activiteiten in Active Directory worden gemaakt als onderdeel van het proces voor samen voegen. |

Afhankelijk van uw vereisten kunt u de toewijzings logica aanpassen met behulp van [Azure AD-expressies](../app-provisioning/functions-for-customizing-application-data.md) , zodat het Active Directory-account wordt ingeschakeld of uitgeschakeld op basis van een combi natie van gegevens punten.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Cloud HR-app toewijzen aan Active Directory gebruikers kenmerken

Elke Cloud HR-app wordt geleverd met een standaard-HR-app voor het Active Directory van toewijzingen.

Wanneer u het proces voor het verwijderen van samenvoegers-verplaatsers start, moet u de volgende vereisten verzamelen.

| Proces | Vereisten |
| - | - |
| **Samen voegers** | Is het proces voor het maken van het Active Directory account hand matig, automatisch of gedeeltelijk geautomatiseerd? |
| | Wilt u aangepaste kenmerken van de werk-app in de Cloud door geven aan Active Directory? |
| **Verplaatsers** | Welke kenmerken wilt u verwerken wanneer een verplaatsings bewerking plaatsvindt in de Cloud HR-app? |
| | Voert u de validatie van specifieke kenmerken uit op het moment van de gebruikers updates? Zo ja, geeft u de details op. |
| **Leavers** | Zijn er afsluitingen voor werk nemers en voorwaardelijke werk rollen in Active Directory? |
| | Welke ingangs datums worden er in rekening gehouden voor het verwerken van gebruikers beëindiging? |
| | Hoe beïnvloeden de conversies van werk nemers en voorwaardelijke werk nemers de bestaande Active Directory accounts? |

Afhankelijk van uw vereisten kunt u de toewijzingen aanpassen om te voldoen aan uw integratie doelen. Voor meer informatie raadpleegt u de specifieke zelf studie over de Cloud-HR-app (zoals [workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)) voor een lijst met aangepaste kenmerken die u wilt toewijzen.

### <a name="generate-a-unique-attribute-value"></a>Een unieke kenmerk waarde genereren

Wanneer u het proces voor samen voegers initieert, moet u mogelijk unieke kenmerk waarden genereren wanneer u kenmerken zoals CN, samAccountName en de UPN instelt, die unieke beperkingen hebben.

De Azure AD-functie [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) evalueert elke regel en controleert vervolgens de waarde die is gegenereerd voor uniekheid in het doel systeem. Zie voor een voor beeld [unieke waarde genereren voor het kenmerk userPrincipalName (UPN)](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Deze functie wordt momenteel alleen ondersteund voor werk dagen Active Directory het inrichten van de gebruiker. Het kan niet worden gebruikt met andere inrichtings toepassingen.

### <a name="configure-active-directory-ou-container-assignment"></a>Toewijzing van Active Directory OE-container configureren

Het is een gemeen schappelijke vereiste om Active Directory gebruikers accounts in containers te plaatsen op basis van bedrijfs eenheden, locaties en afdelingen. Wanneer u een verplaatsings proces initieert en als er een leidinggevende organisatie wordt gewijzigd, moet u de gebruiker mogelijk van de ene organisatie-eenheid verplaatsen naar een andere in Active Directory.

Gebruik de functie [Switch ()](../app-provisioning/functions-for-customizing-application-data.md#switch) om de bedrijfs logica voor de OE-toewijzing te configureren en wijs deze toe aan het Active Directory kenmerk **parentDistinguishedName**.

Als u bijvoorbeeld gebruikers in OE wilt maken op basis van het HR-kenmerk **gemeente**, kunt u de volgende expressie gebruiken:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Als de waarde voor de gemeente van Rotterdam, Austin, Amsterdam of Londen is ingesteld op de volgende expressie, wordt het gebruikers account gemaakt in de bijbehorende organisatie-eenheid. Als er geen overeenkomst is, wordt het account gemaakt in de standaard organisatie-eenheid.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plannen voor het leveren van wacht woorden van nieuwe gebruikers accounts

Wanneer u het proces voor samen voegers initieert, moet u een tijdelijk wacht woord voor nieuwe gebruikers accounts instellen en leveren. Met Cloud HR naar Azure AD-gebruikers inrichten kunt u de Azure AD selfservice-functie voor het [opnieuw instellen van wacht woorden](../authentication/tutorial-enable-sspr.md) (SSPR) voor de gebruiker op de eerste dag implementeren.

SSPR is een eenvoudige manier om IT-beheerders in staat te stellen hun wacht woorden opnieuw in te stellen of hun accounts te ontgrendelen. U kunt het kenmerk **mobiele nummer** in de Cloud-app inrichten om Active Directory te maken en te synchroniseren met Azure AD. Nadat het **mobiele nummer** kenmerk in azure AD is gedefinieerd, kunt u SSPR inschakelen voor het account van de gebruiker. Vervolgens kan de nieuwe gebruiker op de eerste dag het geregistreerde en geverifieerde mobiele nummer voor verificatie gebruiken.

## <a name="plan-for-initial-cycle"></a>Plannen voor de eerste cyclus

Wanneer de Azure AD-inrichtings service voor de eerste keer wordt uitgevoerd, wordt er een [eerste cyclus](../app-provisioning/how-provisioning-works.md#initial-cycle) voor de Cloud HR-app uitgevoerd om een moment opname te maken van alle gebruikers objecten in de Cloud-app HR. De tijd die nodig is voor de eerste cycli is direct afhankelijk van het aantal gebruikers dat in het bron systeem aanwezig is. De eerste cyclus voor sommige tenants in de Cloud-app van meer dan 100.000 gebruikers kan veel tijd in beslag nemen.

Voer de eerste cyclus in progressieve stadia uit **voor grote tenants van de Cloud voor HR-apps (>30.000-gebruikers)** . Start de incrementele updates pas nadat u hebt gecontroleerd of de juiste kenmerken zijn ingesteld in Active Directory voor verschillende scenario's voor het inrichten van gebruikers. Volg de onderstaande volg orde.

1. Voer de eerste cyclus alleen uit voor een beperkt aantal gebruikers door het [bereik filter](#plan-scoping-filters-and-attribute-mapping)in te stellen.
2. Controleer Active Directory account inrichten en de kenmerk waarden die zijn ingesteld voor de gebruikers die zijn geselecteerd voor de eerste uitvoering. Als het resultaat aan uw verwachtingen voldoet, vouwt u het filter bereik uit om meer gebruikers toe te voegen en de resultaten voor de tweede uitvoering te controleren.

Nadat u tevreden bent met de resultaten van de eerste cyclus voor test gebruikers, start u de [incrementele updates](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Testen en beveiliging plannen

Zorg ervoor dat u tijdens elke fase van de implementatie van de eerste pilot door het inschakelen van gebruikers inrichting test dat de resultaten worden uitgevoerd en de inrichtings cycli worden gecontroleerd.

### <a name="plan-testing"></a>Tests plannen

Nadat u de Cloud-HR-app hebt geconfigureerd voor Azure AD-gebruikers inrichten, voert u test cases uit om te controleren of deze oplossing voldoet aan de vereisten van uw organisatie.

|Scenario's|Verwachte resultaten|
|:-|:-|
|Nieuwe werk nemers worden in de Cloud-app HR ingehuurd.| -Het gebruikers account is ingericht in Active Directory.</br>-De gebruiker kan zich aanmelden bij Active Directory-domein-apps en de gewenste acties uitvoeren.</br>-Als Azure AD Connect synchronisatie is geconfigureerd, wordt het gebruikers account ook gemaakt in azure AD.
|De gebruiker wordt beëindigd in de Cloud-app HR.|-Het gebruikers account is uitgeschakeld in Active Directory.</br>-De gebruiker kan zich niet aanmelden bij bedrijfs-apps die zijn beveiligd door Active Directory.
|Organisatie van gebruikers toezicht wordt bijgewerkt in de Cloud-app HR.|Op basis van de kenmerk toewijzing wordt het gebruikers account verplaatst van de ene OE naar een andere in Active Directory.|
|HR werkt de Manager van de gebruiker bij in de Cloud HR-app.|Het veld Manager in Active Directory wordt bijgewerkt op basis van de naam van de nieuwe manager.|
|HR herhuurt een werk nemer in een nieuwe rol.|Gedrag is afhankelijk van hoe de Cloud-HR-app is geconfigureerd voor het genereren van werk nemer-Id's:</br>-Als de oude werk nemer-ID opnieuw wordt gebruikt voor het opnieuw inhuren, schakelt de connector het bestaande Active Directory-account voor de gebruiker in.</br>-Als het opnieuw inhuren een nieuwe werk nemer-ID krijgt, maakt de connector een nieuw Active Directory-account voor de gebruiker.|
|HR converteert de werk nemer naar een contract medewerker of andersom.|Er wordt een nieuw Active Directory-account gemaakt voor de nieuwe persoona en het oude account wordt uitgeschakeld op de ingangs datum van de conversie.|

Gebruik de vorige resultaten om te bepalen hoe u de implementatie van de automatische gebruikers inrichting naar productie kunt overzetten op basis van de ingestelde tijd lijnen.

> [!TIP]
> Gebruik technieken als gegevens reductie en data scrubbing wanneer u de test omgeving met productie gegevens vernieuwt om gevoelige persoonlijke gegevens te verwijderen of te maskeren om te voldoen aan de privacy-en beveiligings standaarden. 

### <a name="plan-security"></a>Beveiliging plannen

Het is gebruikelijk dat een beveiligings beoordeling vereist is als onderdeel van de implementatie van een nieuwe service. Als een beveiligings beoordeling vereist is of niet is uitgevoerd, raadpleegt u de vele Azure AD [White papers](https://www.microsoft.com/download/details.aspx?id=36391) die een overzicht bieden van de identiteit als een service.

### <a name="plan-rollback"></a>Plan terugdraai actie

De implementatie van de gebruikers inrichting in de Cloud voor HR kan niet naar wens werken in de productie omgeving. Als dit het geval is, kunt u de volgende stappen om u te helpen bij het herstellen naar een eerdere bekende juiste status.

1. Bekijk het [inrichtings samenvattings rapport](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) en de [inrichtings logboeken](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) om te bepalen welke onjuiste bewerkingen zijn uitgevoerd voor de betrokken gebruikers of groepen. Zie voor meer informatie over het inrichtings samenvattings rapport en-Logboeken de gebruikers inrichting voor het inrichten van [Cloud-apps beheren](#manage-your-configuration).
2. De laatste bekende juiste status van de betrokken gebruikers of groepen kan worden bepaald met behulp van de audit logboeken van de inrichting of door de doel systemen (Azure AD of Active Directory) te controleren.
3. Werk samen met de eigenaar van de app om de betrokken gebruikers of groepen rechtstreeks in de app bij te werken met behulp van de laatste bekende juiste status waarden.

## <a name="deploy-the-cloud-hr-app"></a>De Cloud-HR-app implementeren

Kies de Cloud-HR-app die wordt uitgelijnd met uw oplossings vereisten.

**Workday**: voor het importeren van worker-profielen van workday in Active Directory en Azure AD, Zie [zelf studie: werk dagen configureren voor automatische gebruikers inrichting](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Desgewenst kunt u het e-mail adres, de gebruikers naam en het telefoon nummer naar werkdag terugschrijven.

**SAP-SuccessFactors**: Zie [zelf studie: Configure SAP SuccessFactors for automatic user Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)(Engelstalig) voor het importeren van worker-profielen van SuccessFactors in Active Directory en Azure AD. U kunt desgewenst het e-mail adres en de gebruikers naam naar SuccessFactors schrijven.

## <a name="manage-your-configuration"></a>Uw configuratie beheren

Azure AD kan inzicht geven in het gebruik van gebruikers en de operationele status van uw organisatie via controle logboeken en-rapporten.

### <a name="gain-insights-from-reports-and-logs"></a>Inzicht verkrijgen in rapporten en Logboeken

Na een geslaagde [eerste cyclus](../app-provisioning/how-provisioning-works.md#initial-cycle)blijft de Azure AD-inrichtings service voor onbepaalde tijd back-to-back-incrementele updates uitvoeren, op intervallen die zijn gedefinieerd in de zelf studies die specifiek zijn voor elke app, totdat een van de volgende gebeurtenissen zich voordoet:

- De service is hand matig gestopt. Er wordt een nieuwe eerste cyclus geactiveerd met behulp van de [Azure Portal](https://portal.azure.com/) of de juiste [Microsoft Graph API](/graph/api/resources/synchronization-overview) -opdracht.
- Er wordt een nieuwe eerste cyclus geactiveerd als gevolg van een wijziging in kenmerk toewijzingen of filter bereik.
- Het inrichtings proces gaat in quarantaine vanwege een hoge fout frequentie. Het blijft langer dan vier weken in quarantaine, op het moment dat het automatisch wordt uitgeschakeld.

Als u deze gebeurtenissen en alle andere activiteiten wilt bekijken die door de inrichtings service worden uitgevoerd, [leert u hoe u Logboeken kunt controleren en rapporten kunt ophalen over inrichtings activiteiten](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Alle activiteiten die worden uitgevoerd door de inrichtings service worden vastgelegd in de Azure AD-audit Logboeken. U kunt Azure AD-controle logboeken door sturen naar Azure Monitor logboeken voor verdere analyse. Met Azure Monitor-Logboeken (ook wel Log Analytics werk ruimte genoemd) kunt u gegevens opvragen om gebeurtenissen te vinden, trends te analyseren en de correlatie tussen verschillende gegevens bronnen uit te voeren. Bekijk deze [video](https://youtu.be/MP5IaCTwkQg) voor meer informatie over de voor delen van het gebruik van Azure monitor logboeken voor Azure AD-Logboeken in praktische gebruikers scenario's.

Installeer de [log Analytics-weer gaven voor Azure AD-activiteiten logboeken](../reports-monitoring/howto-install-use-log-analytics-views.md) om toegang te krijgen tot [vooraf ontwikkelde rapporten](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) rondom het inrichten van gebeurtenissen in uw omgeving.

Zie [de Azure AD-activiteiten logboeken analyseren met uw Azure monitor-logboeken](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)voor meer informatie.

### <a name="manage-personal-data"></a>Persoonlijke gegevens beheren

De Azure AD Connect-inrichtings agent die op de Windows-Server is geïnstalleerd, maakt Logboeken in het Windows-gebeurtenis logboek die persoonlijke gegevens kunnen bevatten, afhankelijk van de HR-app van uw Cloud naar Active Directory kenmerk toewijzingen. Om te voldoen aan de verplichtingen van de privacy van gebruikers, stelt u een geplande Windows-taak in om het gebeurtenis logboek te wissen en ervoor te zorgen dat er geen gegevens meer dan 48 uur worden bewaard.

De Azure AD-inrichtings service genereert geen rapporten, voert analyses uit of biedt meer dan 30 dagen, omdat de service geen gegevens kan opslaan, verwerken of bewaren na 30 dagen.

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de volgende artikelen voor informatie over het oplossen van problemen die tijdens het inrichten kunnen worden gemaakt:

- [Probleem bij het configureren van de gebruikers inrichting voor een Azure AD Gallery-toepassing](application-provisioning-config-problem.md)
- [Een kenmerk van uw on-premises Active Directory naar Azure AD synchroniseren voor het inrichten van een toepassing](user-provisioning-sync-attributes-for-mapping.md)
- [Probleem bij het opslaan van de beheerders referenties tijdens het configureren van de gebruikers inrichting voor een Azure Active Directory galerie-toepassing](./user-provisioning.md)
- [Er worden geen gebruikers ingericht voor een Azure AD-galerie toepassing](application-provisioning-config-problem-no-users-provisioned.md)
- [Er wordt een verkeerde set gebruikers ingericht voor een Azure AD-galerie toepassing](../manage-apps/add-application-portal-assign-users.md)
- [Windows Logboeken instellen voor het oplossen van problemen met agenten](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Auditlogboeken in Azure Portal instellen voor het oplossen van problemen met services](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Informatie over logboeken over het maken van AD-gebruikersaccounts](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Informatie over logboeken over het bijwerken van managers](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Veelvoorkomende fouten oplossen](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Volgende stappen

- [Expressies schrijven voor kenmerktoewijzingen](functions-for-customizing-application-data.md)
- [Overzicht van Azure AD-synchronisatie-API](/graph/api/resources/synchronization-overview)
- [Verwijdering van gebruikers accounts die buiten het bereik vallen, overs Laan](skip-out-of-scope-deletions.md)
- [Azure AD Connect-inrichtings agent: release geschiedenis van versie](provisioning-agent-release-version-history.md)