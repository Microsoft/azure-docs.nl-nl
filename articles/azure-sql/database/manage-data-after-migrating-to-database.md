---
title: Beheren na migratie
titleSuffix: Azure SQL Database
description: Meer informatie over het beheren van uw afzonderlijke en gepoolde data bases na de migratie naar Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: b34ac24cb26bf5db4a49a5ad5b531deb252f4695
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446119"
---
# <a name="new-dba-in-the-cloud--managing-azure-sql-database-after-migration"></a>Nieuwe DBA in de Cloud: Azure SQL Database na migratie beheren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Als u van de traditionele zelf beheerde, zelf beheerd omgeving naar een PaaS-omgeving gaat, kan het om de eerste stap gaan. Als een app-ontwikkelaar of een DBA wilt u weten wat de kern mogelijkheden van het platform zijn die u helpen uw toepassing beschikbaar te houden, te presen teren, veilig en robuust-altijd. In dit artikel wordt ernaar gestreefd dat. In het artikel worden de resources beknopt ingedeeld en krijgt u enkele richt lijnen voor het beste gebruik van de belangrijkste mogelijkheden van Azure SQL Database met één en gegroepeerde Data bases om te beheren en uw toepassing efficiënt te laten werken en optimale resultaten te krijgen in de Cloud. Een typische doel groep voor dit artikel is de gebruikers die:

- Evalueren van de migratie van hun toepassing (en) naar Azure SQL Database-moderniseren van uw toepassing (en).
- Zijn in het proces voor het migreren van hun toepassings-en migratie scenario.
- Onlangs de migratie naar Azure SQL Database-nieuwe DBA in de Cloud hebt voltooid.

In dit artikel worden enkele van de belangrijkste kenmerken van Azure SQL Database als platform beschreven die u gemakkelijk kunt gebruiken bij het werken met afzonderlijke data bases en gepoolde data bases in elastische Pools. Dit zijn de volgende:

- Databases bewaken via de Azure-portal
- Bedrijfscontinuïteit en herstel na noodgeval (BCDR)
- Beveiliging en naleving
- Intelligente database bewaking en onderhoud
- Gegevensverplaatsing

## <a name="monitor-databases-using-the-azure-portal"></a>Databases bewaken via de Azure-portal

In de [Azure Portal](https://portal.azure.com/)kunt u het gebruik van een afzonderlijke data base bewaken door uw data base te selecteren en op de grafiek **bewaking** te klikken. Nu wordt een venster **Metrische gegevens** geopend, dat u kunt wijzigen door op de knop **Grafiek bewerken** te klikken. Voeg de volgende metrische gegevens toe:

- CPU-percentage
- DTU-percentage
- Gegevens-I/O-percentage
- Databaseomvangpercentage

Zodra u deze metrische gegevens hebt toegevoegd, kunt u ze in het **bewakings** diagram bekijken met meer informatie over het venster **metrische** gegevens. De vier metrische gegevens tonen het gemiddelde gebruikspercentage ten opzichte van de **DTU** van uw database. Zie het [op DTU gebaseerde aankoop model](service-tiers-dtu.md) en [vCore](service-tiers-vcore.md) voor meer informatie over service lagen.  

![Servicelaagbewaking van databaseprestaties.](./media/manage-data-after-migrating-to-database/sqldb_service_tier_monitoring.png)

U kunt ook meldingen configureren voor prestatiewaarden. Klik op de knop **Melding toevoegen** in het venster **Metrische gegevens**. Volg de wizard om de melding te configureren. U hebt de keuze om een melding weer te geven als de metrische gegevens een bepaalde drempelwaarde overschrijden of als het meetpunt onder een bepaalde drempelwaarde komt.

Als u bijvoorbeeld verwacht dat de workload van de database zal toenemen, kunt u configureren dat er een e-mailmelding wordt verstuurd wanneer de database 80% van een van de prestatiewaarden heeft bereikt. U kunt dit als een vroege waarschuwing gebruiken om te bepalen wanneer u mogelijk moet overschakelen naar de volgende hoogste reken grootte.

Met de metrische gegevens over prestaties kunt u ook bepalen of u een downgrade kunt uitvoeren naar een lagere reken grootte. Stel dat u een Standard S2-database gebruikt en alle prestatiewaarden aangeven dat de database gemiddeld nooit meer dan 10% gebruikt. Het is dan waarschijnlijk dat de database in Standard S1 goed werkt. Houd echter rekening met workloads die pieken of schommelen voordat u besluit om over te stappen op een lagere reken grootte.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Bedrijfscontinuïteit en herstel na noodgeval (BCDR)

Dankzij de mogelijkheden voor bedrijfs continuïteit en herstel na nood gevallen kunt u uw bedrijf, zoals gebruikelijk, in het geval van een ramp voortzetten. De nood geval kan een gebeurtenis op database niveau zijn (bijvoorbeeld iemand die per ongeluk een cruciale tabel verbreekt) of een gebeurtenis op het niveau van het gegevens centrum (regionaal rampen, bijvoorbeeld een tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hoe kan ik maken en beheren van back-ups op SQL Database

U maakt geen back-ups op Azure SQL Database en dat is omdat u dat niet hoeft te doen. SQL Database maakt automatisch back-ups van data bases, zodat u zich geen zorgen meer hoeft te maken over het plannen, maken en beheren van back-ups. Het platform neemt elke week een volledige back-up, differentiële back-up elke paar uur en een back-up van het logboek om de vijf minuten om te controleren of het herstel na nood geval efficiënt is en het gegevens verlies mini maal. De eerste volledige back-up gebeurt zodra u een Data Base maakt. Deze back-ups zijn voor u beschikbaar voor een bepaalde periode met de naam ' Bewaar periode ' en zijn afhankelijk van de servicelaag die u kiest. SQL Database biedt u de mogelijkheid om op elk gewenst moment binnen deze Bewaar periode te herstellen met behulp van [PITR (Point in time Recovery)](recovery-using-backups.md#point-in-time-restore).

|Servicelaag|Bewaar periode in dagen|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Daarnaast kunt u met de functie voor [lange termijn retentie (LTR)](long-term-retention-overview.md) uw back-upbestanden gedurende een periode van meer dan tien jaar bewaren en gegevens van deze back-ups op elk gewenst moment binnen die periode herstellen. Daarnaast worden de back-ups van de data base opgeslagen in geo-gerepliceerde opslag om de flexibiliteit van regionale rampen te garanderen. U kunt deze back-ups ook in een Azure-regio op elk gewenst moment herstellen binnen de Bewaar periode. Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hoe kan ik zorgen voor bedrijfs continuïteit in het geval van een nood situatie op Data Center of een regionale rampen

Omdat uw database back-ups worden opgeslagen in opslag met geo-replicatie om ervoor te zorgen dat in het geval van een regionale nood situatie, kunt u de back-up herstellen naar een andere Azure-regio. Dit wordt geo-Restore genoemd. De RPO (beoogd herstel punt) is over het algemeen < 1 uur en de ERT (geschatte herstel tijd – enkele minuten tot uur).

Voor essentiële data bases, Azure SQL Database aanbiedingen, actieve geo-replicatie. Dit is in feite het maken van een secundaire kopie met geo-replicatie van de oorspronkelijke data base in een andere regio. Als uw Data Base voor het eerst wordt gehost in de regio Azure West VS en u een regionale nood herstel tolerantie wilt. U maakt een actieve geo-replica van de data base in VS West om VS-Oost te zeggen. Wanneer de Calamity aan het VS-West wordt doorgeschakeld, kunt u een failover uitvoeren naar de regio VS-Oost. Het configureren van deze in een groep met automatische failovers is nog beter omdat hiermee ervoor wordt gezorgd dat de data base automatisch wordt overgeschakeld naar de secundaire in VS Oost in het geval van een ramp. De RPO voor dit is < vijf seconden en de ERT < 30 seconden.

Als een groep met automatische failover niet is geconfigureerd, moet uw toepassing actief controleren op een nood geval en een failover naar de secundaire initiëren. U kunt Maxi maal vier dergelijke actieve geo-replica's maken in verschillende Azure-regio's. Het wordt nog beter. U kunt ook toegang krijgen tot deze secundaire actieve geo-replica's voor alleen-lezen toegang. Dit is een zeer handige manier om de latentie van een geografisch gedistribueerde toepassings scenario te verminderen.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hoe verandert mijn plan voor herstel na nood gevallen van on-premises naar SQL Database

SQL Server Setup vereist dat u uw Beschik baarheid actief beheert door gebruik te maken van functies zoals Failoverclustering, database spiegeling, transactie replicatie of het vastleggen en beheren van back-ups voor bedrijfs continuïteit. Met SQL Database beheert het platform deze voor u, zodat u zich kunt concentreren op het ontwikkelen en optimaliseren van uw database toepassing en niet zo veel moeite hoeft te doen met nood beheer. U kunt plannen voor back-ups en herstel na nood gevallen configureren en met slechts enkele klikken op de Azure Portal (of enkele opdrachten die gebruikmaken van de Power shell-Api's).

Zie voor meer informatie over herstel na nood gevallen: [Azure SQL database herstel na nood gevallen 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Beveiliging en naleving

SQL Database is zeer serieus beveiligd en privacy. Beveiliging binnen SQL Database is beschikbaar op het niveau van de data base en op platform niveau en is het meest geschikt wanneer het in meerdere lagen wordt ingedeeld. Op elke laag krijgt u de controle en optimale beveiliging voor uw toepassing. De lagen zijn:

- Identiteits & authenticatie ([SQL-verificatie en Azure Active Directory [Azure AD]-verificatie](logins-create-manage.md)).
- Bewakings activiteit ([controle](../../azure-sql/database/auditing-overview.md) en [detectie van bedreigingen](threat-detection-configure.md)).
- Het beveiligen van werkelijke gegevens ([transparent Data Encryption [TDe]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) en [Always encrypted [ae]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Toegang tot gevoelige en beschermde gegevens beheren ([beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) en [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) biedt gecentraliseerd beveiligings beheer voor workloads die worden uitgevoerd in azure, on-premises en in andere Clouds. U kunt weer geven of essentiële SQL Database beveiliging zoals [controle](../../azure-sql/database/auditing-overview.md) en [transparent Data Encryption [TDe]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) zijn geconfigureerd voor alle resources en beleids regels maken op basis van uw eigen vereisten.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Welke verificatie methoden voor gebruikers worden aangeboden in SQL Database

Er zijn twee verificatie methoden beschikbaar in SQL Database:

- [Azure Active Directory-verificatie](authentication-aad-overview.md)
- [SQL-verificatie](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

De traditionele Windows-verificatie wordt niet ondersteund. Azure Active Directory (Azure AD) is een gecentraliseerde service voor identiteits-en toegangs beheer. Zo kunt u eenvoudig een eenmalige aanmelding (SSO) voor alle mede werkers in uw organisatie bieden. Dit betekent dat de referenties worden gedeeld in alle Azure-Services voor een eenvoudigere verificatie. 

Azure AD biedt ondersteuning voor [Azure AD-multi-factor Authentication](authentication-mfa-ssms-overview.md) en met een [paar klikken](../../active-directory/hybrid/how-to-connect-install-express.md) Azure AD kan worden geïntegreerd met Windows Server Active Directory. SQL-verificatie werkt precies zoals u deze in het verleden hebt gebruikt. U geeft een gebruikers naam en wacht woord op en u kunt gebruikers verifiëren voor elke Data Base op een bepaalde server. Hierdoor kunnen SQL Database en Azure Synapse Analytics Multi-Factor Authentication-en gast gebruikers accounts binnen een Azure AD-domein aanbieden. Als u al een on-premises Active Directory hebt, kunt u de adres lijst met Azure Active Directory om uw directory uit te breiden naar Azure.

|**Als u...**|**SQL Database/Azure Synapse Analytics**|
|---|---|
|Geen gebruik van Azure Active Directory (Azure AD) in azure|[SQL-verificatie](security-overview.md) gebruiken|
|On-premises AD gebruiken op SQL Server|Vervoer [AD met Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)en gebruik Azure AD-verificatie. Met deze kunt u eenmalige aanmelding gebruiken.|
|Multi-Factor Authentication moet worden afgedwongen|Multi-Factor Authentication als beleid vereisen via [voorwaardelijke toegang van micro soft](conditional-access-configure.md)en [Azure AD Universal-verificatie gebruiken met multi-factor Authentication ondersteuning](authentication-mfa-ssms-overview.md).|
|Gast accounts van micro soft-accounts (live.com, outlook.com) of andere domeinen (gmail.com) hebben|Gebruik [Azure AD Universal-verificatie](authentication-mfa-ssms-overview.md) in SQL database/Data Warehouse, dat gebruikmaakt van [Azure AD B2B-samen werking](../../active-directory/external-identities/what-is-b2b.md).|
|Worden aangemeld bij Windows met uw Azure AD-referenties van een federatief domein|[Geïntegreerde Azure AD-verificatie](authentication-aad-configure.md)gebruiken.|
|Worden aangemeld bij Windows met behulp van referenties van een domein dat niet federatief is met Azure|[Geïntegreerde Azure AD-verificatie](authentication-aad-configure.md)gebruiken.|
|Services van de middelste laag die verbinding moeten maken met SQL Database of Azure Synapse Analytics|[Geïntegreerde Azure AD-verificatie](authentication-aad-configure.md)gebruiken.|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hoe kan ik de connectiviteits toegang tot mijn data base beperken of beheren

Er zijn meerdere technieken ter beschikking die u kunt gebruiken om de optimale connectiviteits organisatie voor uw toepassing te bereiken.

- Firewallregels
- VNeT-service-eindpunten
- Gereserveerde IP-adressen

#### <a name="firewall"></a>Firewall

Een firewall voor komt dat toegang tot uw server vanaf een externe entiteit wordt toegestaan door alleen specifieke entiteiten toegang te geven tot uw server. Standaard zijn alle verbindingen met data bases in de server niet toegestaan, met uitzonde ring van optionally7-verbindingen die afkomstig zijn van andere Azure-Services. Met een firewall regel kunt u toegang tot uw server alleen tot entiteiten (bijvoorbeeld een ontwikkelaars computer) die u goedkeurt, openen door het IP-adres van die computer via de firewall toe te staan. U kunt hiermee ook een bereik van IP-adressen opgeven die u toegang wilt verlenen tot de server. U kunt bijvoorbeeld de IP-adressen van ontwikkel aars in uw organisatie tegelijk toevoegen door een bereik op te geven op de pagina Firewall instellingen.

U kunt Firewall regels maken op server niveau of op database niveau. IP-firewall regels op server niveau kunnen worden gemaakt met behulp van de Azure Portal of met SSMS. Zie voor meer informatie over het instellen van een firewall regel op server-en database niveau: IP- [firewall regels maken in SQL database](secure-database-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Service-eindpunten

Uw data base is standaard geconfigureerd voor toegang tot de server met Azure-Services. Dit betekent dat een virtuele machine in azure verbinding kan maken met uw data base. Deze pogingen moeten nog steeds worden geverifieerd. Als u echter niet wilt dat uw data base toegankelijk is voor Azure Ip's, kunt u ' Azure-Services toegang verlenen tot server ' uitschakelen. Daarnaast kunt u VNet- [service-eind punten](vnet-service-endpoint-rule-overview.md)configureren.

Met Service-eind punten (SE) kunt u uw kritieke Azure-resources alleen beschikbaar maken voor uw eigen particuliere virtuele netwerk in Azure. Door dit te doen, kunt u de open bare toegang tot uw resources in wezen elimineren. Het verkeer tussen uw virtuele netwerk en Azure blijft in het Azure-backbone-netwerk. Zonder SE krijgt u pakket routering met geforceerde tunneling. Met uw virtuele netwerk wordt het Internet verkeer naar uw organisatie en het verkeer van de Azure-service afgedwongen om over dezelfde route te gaan. Met Service-eind punten kunt u dit optimaliseren omdat de pakketten direct van het virtuele netwerk naar de service in het backbone-netwerk van Azure worden getransporteerd.

![VNeT-service-eindpunten](./media/manage-data-after-migrating-to-database/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Gereserveerde IP-adressen

Een andere mogelijkheid is om [gereserveerde ip's](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) voor uw vm's in te richten en deze specifieke VM-IP-adressen toe te voegen in de firewall instellingen van de server. Door gereserveerde Ip's toe te wijzen, bespaart u de moeite van het bijwerken van de firewall regels met het wijzigen van IP-adressen.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Met welke poort kan ik verbinding maken met SQL Database op

Poort 1433. SQL Database communiceert via deze poort. Als u verbinding wilt maken vanuit een bedrijfs netwerk, moet u een uitgaande regel toevoegen in de firewall instellingen van uw organisatie. Als richt lijn vermijdt u poort 1433 buiten de Azure-grens.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hoe kan ik activiteiten controleren en reguleren op mijn server en data base in SQL Database

#### <a name="sql-database-auditing"></a>SQL Database Auditing

Met SQL Database kunt u controle inschakelen om database gebeurtenissen bij te houden. [SQL database controle](../../azure-sql/database/auditing-overview.md) records database gebeurtenissen en schrijft deze naar een audit logboek bestand in uw Azure Storage-account. Controle is vooral nuttig als u van plan bent om inzicht te krijgen in mogelijke beveiligings-en beleids schendingen, behoud van de naleving van de regelgeving, enzovoort. Hiermee kunt u bepaalde gebeurtenis Categorieën definiëren en configureren die u wilt controleren en op basis van dat u vooraf geconfigureerde rapporten en een dash board kunt ophalen om een overzicht te krijgen van de gebeurtenissen die in uw data base optreden. U kunt dit controle beleid Toep assen op het niveau van de data base of op het server niveau. Zie [SQL database controle inschakelen](secure-database-tutorial.md#enable-security-features)voor meer informatie over het inschakelen van controle voor uw server of Data Base.

#### <a name="threat-detection"></a>Detectie van bedreigingen

Met [detectie van bedreigingen](threat-detection-configure.md)krijgt u de mogelijkheid om te reageren op beveiligings-of beleids schendingen die worden gedetecteerd door zeer eenvoudig te controleren. U hoeft geen beveiligings expert te zijn om mogelijke dreigingen of schendingen in uw systeem op te lossen. Detectie van bedreigingen heeft ook een aantal ingebouwde mogelijkheden zoals SQL-injectie detectie. SQL-injectie is een poging om de gegevens te wijzigen of in te brengen, en een zeer veelvoorkomende manier om een database toepassing in het algemeen aan te vallen. Met detectie van bedreigingen worden meerdere sets algoritmen uitgevoerd waarmee mogelijke beveiligings problemen en SQL-injectie aanvallen worden gedetecteerd, evenals afwijkende database toegangs patronen (zoals toegang vanaf een ongebruikelijke locatie of door een onbekende principal). Beveiligings ambtenaren of andere aangewezen beheerders ontvangen een e-mail melding als er een bedreiging wordt gedetecteerd op de data base. Elke melding bevat details over de verdachte activiteit en aanbevelingen voor het verder onderzoeken en oplossen van de dreiging. Zie voor meer informatie over het inschakelen van detectie van bedreigingen: [detectie van bedreigingen inschakelen](secure-database-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hoe kan ik mijn gegevens in het algemeen op SQL Database beveiligen

Versleuteling biedt een sterk mechanisme om uw gevoelige gegevens van indringers te beschermen en te beveiligen. De versleutelde gegevens kunnen niet worden gebruikt voor de indringer zonder de sleutel voor ontsleuteling. Daarom wordt er een extra beveiligingslaag toegevoegd boven op de bestaande lagen van beveiliging die zijn ingebouwd in SQL Database. Er zijn twee aspecten waarmee u uw gegevens in SQL Database kunt beveiligen:

- Uw gegevens in de gegevens-en logboek bestanden die zich in de rest bevinden
- Uw gegevens in de vlucht

In SQL Database zijn uw gegevens in de gegevens en logboek bestanden op het opslag subsysteem standaard volledig en altijd versleuteld via [transparent Data Encryption [TDe]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Uw back-ups worden ook versleuteld. Bij TDE zijn er geen wijzigingen vereist voor uw toepassings zijde die toegang tot deze gegevens heeft. De versleuteling en ontsleuteling gebeurt transparant; Daarom is dit de naam.
Voor het beveiligen van uw gevoelige gegevens in-Flight en op rest, SQL Database een functie met de naam [Always encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine)biedt. AE is een vorm van versleuteling aan de client zijde waarmee gevoelige kolommen in uw data base worden versleuteld (zodat ze in een gecodeerde tekst worden omgezet naar database beheerders en niet-gemachtigde gebruikers). De server ontvangt de versleutelde gegevens om te beginnen met. De sleutel voor Always Encrypted wordt ook opgeslagen aan de client zijde, zodat alleen geautoriseerde clients de gevoelige kolommen kunnen ontsleutelen. De server-en gegevens beheerders kunnen de gevoelige gegevens niet zien omdat de versleutelings sleutels zijn opgeslagen op de client. Met AE worden gevoelige kolommen in de tabel end-to-end versleuteld, van niet-geautoriseerde clients naar de fysieke schijf. AE ondersteunt vandaag nog gelijkheids vergelijkingen, zodat Dba's de versleutelde kolommen kan doorzoeken als onderdeel van hun SQL-opdrachten. Always Encrypted kan worden gebruikt met diverse opties voor sleutel opslag, zoals [Azure Key Vault](always-encrypted-azure-key-vault-configure.md), Windows-certificaat archief en lokale hardware security modules.

|**Kenmerken**|**Altijd versleuteld**|**Transparent Data Encryption**|
|---|---|---|
|**Versleutelings bereik**|End-to-end|At-rest-gegevens|
|**De server kan toegang krijgen tot gevoelige gegevens**|Nee|Ja, omdat versleuteling voor de Data-at-rest is|
|**Toegestane T-SQL-bewerkingen**|Gelijkheids vergelijking|Alle T-SQL-surface area is beschikbaar|
|**App-wijzigingen die zijn vereist voor het gebruik van de functie**|Minimaal|Zeer mini maal|
|**Granulatie van versleuteling**|Kolom niveau|databaseniveau|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hoe kan ik de toegang tot gevoelige gegevens in mijn data base beperken

Elke toepassing heeft een bepaalde bit van gevoelige gegevens in de data base die moet worden beveiligd om voor iedereen zichtbaar te zijn. Bepaalde mede werkers in de organisatie moeten deze gegevens weer geven, maar anderen mogen deze gegevens niet weer geven. Een voor beeld is werknemers lonen. Een manager heeft toegang nodig tot de salaris informatie voor hun directe rapporten, maar de afzonderlijke team leden mogen geen toegang hebben tot de salaris gegevens van hun collega's. Een ander scenario is het maken van gegevens ontwikkelaars die interactief met gevoelige gegevens kunnen werken tijdens ontwikkelings stadia of tests kunnen doen, bijvoorbeeld burger servicenummers klanten. Deze informatie hoeft niet meer te worden blootgesteld aan de ontwikkelaar. In dergelijke gevallen moeten uw gevoelige gegevens worden gemaskeerd of helemaal niet worden weer gegeven. SQL Database biedt twee dergelijke benaderingen om te voor komen dat onbevoegde gebruikers gevoelige gegevens kunnen bekijken:

[Dynamische gegevens maskering](dynamic-data-masking-overview.md) is een functie voor gegevens maskering waarmee u de bloot stelling van gevoelige gegevens kunt beperken door deze te maskeren voor niet-gemachtigde gebruikers op de toepassingslaag. U definieert een maskerings regel die een maskerings patroon kan maken (bijvoorbeeld om alleen de laatste vier cijfers van een National ID SSN: XXX-XX-0000 te laten zien en het grootste deel van het te markeren) en te bepalen welke gebruikers moeten worden uitgesloten van de maskerings regel. De maskering gebeurt aan de vliegen en er zijn verschillende maskerings functies beschikbaar voor verschillende gegevens categorieën. Met dynamische gegevens maskering kunt u automatisch gevoelige gegevens in uw data base detecteren en er maskering op Toep assen.

Met [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) kunt u de toegang op rijniveau beheren. Dat betekent dat bepaalde rijen in een database tabel op basis van de gebruiker die de query uitvoert (groepslid maatschap of uitvoerings context), worden verborgen. De toegangs beperking wordt uitgevoerd op de data base-laag in plaats van in een toepassingslaag om uw app-logica te vereenvoudigen. U begint met het maken van een filter predicaat, het filteren van rijen die niet worden weer gegeven en het beveiligings beleid volgende definieert wie toegang heeft tot deze rijen. Ten slotte voert de eind gebruiker hun query uit en, afhankelijk van de bevoegdheid van de gebruiker, worden deze beperkte rijen weer gegeven of kunnen ze helemaal geen toegang krijgen.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Versleutelings sleutels in de Cloud Hoe kan ik beheren

Er zijn opties voor sleutel beheer voor zowel Always Encrypted (versleuteling aan client zijde) als Transparent Data Encryption (versleuteling op rest). Het is raadzaam om regel matig versleutelings sleutels te draaien. De rotatie frequentie moet worden uitgelijnd met de voor schriften van uw interne organisatie en naleving.

#### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

Er bevindt zich een hiërarchie met twee sleutels in TDE: de gegevens in elke gebruikers database zijn versleuteld met een symmetrische AES-256-data base-unieke database versleutelings sleutel (DEK), die op zijn beurt is versleuteld met een unieke server-uniek asymmetrisch 2048 RSA-sleutel De hoofd sleutel kan worden beheerd op een van de volgende manieren:

- Automatisch door de platform-SQL Database.
- Of u gebruikt [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) als de sleutel opslag.

De hoofd sleutel voor Transparent Data Encryption wordt standaard beheerd door de SQL Database-Service voor het gemak. Als uw organisatie de hoofd sleutel wil beheren, is er een optie voor het gebruik van Azure Key Vault] (altijd versleuteld-Azure-Key-kluis-configure.md) als de sleutel opslag. Door gebruik te maken van Azure Key Vault, neemt uw organisatie de controle over de besturings elementen voor sleutel inrichting, rotatie en machtigingen. [Draaien of overschakelen van het type van een TDe-hoofd sleutel](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) is snel, omdat deze alleen de dek opnieuw versleutelt. Voor organisaties met een schei ding van functies tussen beveiliging en gegevens beheer kan een beveiligings beheerder het sleutel materiaal inrichten voor de hoofd sleutel TDE in Azure Key Vault en een Azure Key Vault sleutel-id opgeven aan de database beheerder voor versleuteling in rust op een server. De Key Vault is zodanig ontworpen dat micro soft geen versleutelings sleutels kan zien of extra heren. U krijgt ook een gecentraliseerd beheer van sleutels voor uw organisatie.

#### <a name="always-encrypted"></a>Altijd versleuteld

Er is ook een [hiërarchie met twee sleutels](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) in always Encrypted: een kolom met gevoelige gegevens wordt versleuteld met een AES 256-kolom versleutelings sleutel (CEK), die op zijn beurt is versleuteld met een kolom hoofd sleutel (CMK). De client Stuur Programma's die voor Always Encrypted worden meegeleverd, hebben geen beperkingen voor de lengte van CMKs. De versleutelde waarde van de CEK wordt opgeslagen in de data base en de CMK wordt opgeslagen in een vertrouwde sleutel opslag, zoals Windows-certificaat archief, Azure Key Vault of een Hardware Security module.

- Zowel de [CEK als de CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) kunnen worden gedraaid.
- CEK draaien is een grootte van de gegevens bewerking en kan veel tijd in beslag worden, afhankelijk van de grootte van de tabellen die de versleutelde kolommen bevatten. Daarom is het verstandig CEK rotaties dienovereenkomstig te plannen.
- CMK draaien heeft echter geen invloed op de prestaties van de data base en kan worden uitgevoerd met gescheiden rollen.

In het volgende diagram ziet u de opties voor de sleutel opslag voor de kolom hoofd sleutels in Always Encrypted

![Altijd versleutelde CMK-opslag providers](./media/manage-data-after-migrating-to-database/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hoe kan ik het verkeer tussen mijn organisatie en SQL Database optimaliseren en beveiligen

Het netwerk verkeer tussen uw organisatie en SQL Database wordt doorgaans gerouteerd via het open bare netwerk. Als u ervoor kiest om dit pad te optimaliseren en het veiliger te maken, kunt u ook naar Azure ExpressRoute kijken. Met ExpressRoute kunt u uw bedrijfs netwerk uitbreiden naar het Azure-platform via een particuliere verbinding. Door dit te doen, gaat u niet via het open bare Internet. U krijgt ook betere beveiliging, betrouw baarheid en optimalisatie van route ring die worden omgezet naar lagere netwerk latenties en veel snellere snelheden dan u normaal gesp roken zou doen via het open bare Internet. Als u van plan bent een aanzienlijk deel van gegevens over te brengen tussen uw organisatie en Azure, kunt u met ExpressRoute kosten voordelen opleveren. U kunt kiezen uit drie verschillende connectiviteits modellen voor de verbinding tussen uw organisatie en Azure:

- [Co-locatie van Cloud Exchange](../../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-to-any](../../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Punt-naar-punt](../../expressroute/expressroute-connectivity-models.md#Ethernet)

Met ExpressRoute kunt u ook tot twee maal de limiet voor de band breedte die u aanschaft, opwaarderen. Het is ook mogelijk om connectiviteit tussen regio's te configureren met behulp van ExpressRoute. Zie voor een lijst met ExpressRoute-connectiviteits providers: [ExpressRoute-partners en peering-locaties](../../expressroute/expressroute-locations.md). In de volgende artikelen wordt een gedetailleerde beschrijving gegeven van Express route:

- [Inleiding op Express route](../../expressroute/expressroute-introduction.md)
- [Vereisten](../../expressroute/expressroute-prerequisites.md)
- [Werkstromen](../../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Voldoet aan de eisen van de regelgeving en hoe deze helpt bij de naleving van mijn eigen organisatie SQL Database

SQL Database voldoet aan een aantal regelgevende compliancies. Als u de meest recente set compliancies wilt weer geven waaraan SQL Database is voldaan, gaat u naar het [vertrouwens centrum van micro soft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) en zoomt u in op de compliancies die belang rijk zijn voor uw organisatie om te zien of SQL database is opgenomen in de compatibele Azure-Services. Het is belang rijk om te weten dat SQL Database kan worden gecertificeerd als een compatibele service, de IT-afdeling helpt bij de naleving van de service van uw organisatie, maar deze niet automatisch gegarandeerd.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligente database controle en-onderhoud na migratie

Wanneer u uw Data Base naar SQL Database hebt gemigreerd, kunt u uw data base bewaken (Bekijk bijvoorbeeld hoe het resource gebruik is of DBCC-controles) en voert u regel matig onderhoud uit (bijvoorbeeld het opnieuw samen stellen of opnieuw indelen van indexen, statistieken, enzovoort). Gelukkig is SQL Database intelligent in de zin dat het gebruik maakt van de historische trends en vastgelegde metrische gegevens en statistieken om u proactief te helpen bij het bewaken en onderhouden van uw data base, zodat uw toepassing optimaal wordt uitgevoerd. In sommige gevallen kan Azure SQL Database automatisch onderhouds taken uitvoeren, afhankelijk van de configuratie-instellingen. Er zijn drie facetten voor het bewaken van uw data base in SQL Database:

- Prestaties bewaken en optimaliseren.
- Beveiligings optimalisatie.
- Kosten optimalisatie.

### <a name="performance-monitoring-and-optimization"></a>Prestaties bewaken en optimaliseren

Met query performance Insights kunt u op maat gemaakte aanbevelingen voor uw data base-workload krijgen zodat uw toepassingen op een optimaal niveau kunnen blijven werken. U kunt deze ook zo instellen dat deze aanbevelingen automatisch worden toegepast en u hoeft geen onderhouds taken uit te voeren. Met SQL Database Advisor kunt u automatisch index aanbevelingen implementeren op basis van uw workload. dit wordt automatisch afstemmen genoemd. De aanbevelingen worden ontwikkeld om de werk belasting van uw toepassing te wijzigen zodat u de meest relevante suggesties krijgt. U krijgt ook de mogelijkheid om deze aanbevelingen hand matig te controleren en deze op uw keuze toe te passen.  

### <a name="security-optimization"></a>Beveiligings optimalisatie

SQL Database biedt belastbare beveiligings aanbevelingen om u te helpen bij het beveiligen van uw gegevens en detectie van bedreigingen voor het identificeren en onderzoeken van verdachte database activiteiten die een mogelijke thread kunnen vormen voor de data base. De [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md) is een service voor het scannen en rapporteren van een Data Base waarmee u de beveiligings status van uw data bases op schaal kunt controleren en beveiligings Risico's en-drift op basis van een door u gedefinieerde beveiligings basislijn moet identificeren. Na elke scan wordt een aangepaste lijst met bewerkingen en herstel scripts weer gegeven, evenals een evaluatie rapport dat kan worden gebruikt om te voldoen aan de nalevings vereisten.

Met Azure Security Center identificeert u de beveiligings aanbevelingen op het bord en past u deze met één klik toe.

### <a name="cost-optimization"></a>Kostenoptimalisatie

Het Azure SQL-platform analyseert de gebruiks geschiedenis over de data bases op een server om de opties voor de kosten optimalisatie voor u te evalueren en aan te bevelen. Deze analyse vergt meestal een Fortnight om aanbevelingen te analyseren en te ontwikkelen. Elastische pool is een van deze opties. De aanbeveling wordt op de portal weer gegeven als banner:

![aanbevelingen voor elastische Pools](./media/manage-data-after-migrating-to-database/elastic-pool-recommendations.png)

U kunt deze analyse ook bekijken in de sectie ' Advisor ':

![aanbevelingen voor elastische Pools-Advisor](./media/manage-data-after-migrating-to-database/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hoe kan ik de prestaties en het resource gebruik in SQL Database bewaken

In SQL Database kunt u gebruikmaken van de intelligente inzichten van het platform om de prestaties te bewaken en op de juiste wijze af te stemmen. U kunt de prestaties en het resource gebruik in SQL Database bewaken met behulp van de volgende methoden:

#### <a name="azure-portal"></a>Azure Portal

Het Azure Portal toont het gebruik van een Data Base door de data base te selecteren en te klikken op de grafiek in het deel venster Overzicht. U kunt de grafiek wijzigen om meerdere metrische gegevens weer te geven, waaronder het CPU-percentage, het DTU-percentage, het IO-percentage van de gegevens, het percentage van sessies en het percentage van de database grootte.

![Bewakings grafiek](./media/manage-data-after-migrating-to-database/monitoring-chart.png)

![Chart2 bewaken](./media/manage-data-after-migrating-to-database/chart.png)

Vanuit deze grafiek kunt u ook waarschuwingen per resource configureren. Met deze waarschuwingen kunt u reageren op resource voorwaarden met een e-mail bericht, schrijven naar een HTTPS/HTTP-eind punt of een actie uitvoeren. Zie [Create Alerts](alerts-insights-configure-portal.md)(Engelstalig) voor meer informatie.

#### <a name="dynamic-management-views"></a>Dynamische beheerweergaven

U kunt een query uitvoeren op de [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamische beheer weergave om de geschiedenis van het resource verbruiks statistieken te retour neren van het afgelopen uur en de [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) systeem catalogus weergave om de laatste 14 dagen geschiedenis te retour neren.

#### <a name="query-performance-insight"></a>Inzicht in queryprestaties

Met [query Performance Insight](query-performance-insight-use.md) kunt u een geschiedenis bekijken van de belangrijkste query's en langlopende query's voor een specifieke data base. U kunt snel de meest voorkomende query's identificeren door middel van resource gebruik, duur en uitvoerings frequentie. U kunt query's volgen en regressies detecteren. Deze functie vereist dat het [query archief](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) is ingeschakeld en actief is voor de data base.

![Inzicht in queryprestaties](./media/manage-data-after-migrating-to-database/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL-analyse (preview) in Azure Monitor-logboeken

Met [Azure monitor-logboeken](../../azure-monitor/insights/azure-sql.md) kunt u belang rijke Azure SQL database prestatie gegevens verzamelen en visualiseren, die ondersteuning bieden voor maxi maal 150.000 data bases en ELASTISCHe SQL-pools van 5.000 per werk ruimte. U kunt deze gebruiken om meldingen te bewaken en te ontvangen. U kunt de metrische gegevens van SQL Database en elastische groepen bewaken in meerdere Azure-abonnementen en elastische Pools en kunnen worden gebruikt om problemen te identificeren in elke laag van een toepassings stack.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Ik merkt prestatie problemen: Hoe verschilt mijn SQL Database-probleemoplossings methodologie van SQL Server

Een belang rijk deel van de probleemoplossings technieken die u zou gebruiken voor het vaststellen van problemen met de prestaties van query's en data bases, blijft hetzelfde. Nadat alle dezelfde data base-engine de Cloud heeft gebevoegdheden. De platform-Azure SQL Database heeft echter ingebouwde intelligentie. Het helpt u bij het oplossen van problemen met de prestaties en het eenvoudiger te diagnosticeren. Het kan ook enkele van deze corrigerende maat regelen uitvoeren namens u en in sommige gevallen, zodat deze automatisch worden opgelost.

Uw aanpak voor het oplossen van problemen met prestaties kan aanzienlijk profiteren van intelligente functies als [query Performance Insight (QPI)](query-performance-insight-use.md) en [database Advisor](database-advisor-implement-performance-recommendations.md) in combi natie, waardoor het verschil in de methodologie in dat opzicht afwijkt: u hoeft niet langer te doen om de essentiële informatie te verfijnen die u kan helpen bij het oplossen van het probleem. Het platform doet de hard werk voor u. Een voor beeld van dat is QPI. Met QPI kunt u alles inzoomen op het query niveau en de historische trends bekijken en nagaan waar precies de query teruggedraaide. De Database Advisor biedt u aanbevelingen die u kunnen helpen bij het verbeteren van de algehele prestaties in algemene, zoals-ontbrekende indexen, het weghalen van indexen, het parameterizing van uw query's, enzovoort.

Bij het oplossen van problemen is het belang rijk om te bepalen of het nu alleen de toepassing of de data base is die er een back-up van maakt, wat invloed heeft op de prestaties van uw toepassing. Vaak ligt het prestatie probleem in de toepassingslaag. Dit kan de architectuur of het gegevens toegangs patroon zijn. Stel dat u een intensieve-toepassing hebt die gevoelig is voor netwerk latentie. In dit geval gaat uw toepassing in, omdat er veel korte aanvragen worden teruggestuurd tussen de toepassing en de server en op een gelaste netwerk. deze retours worden snel opgeteld. U kunt in dit geval [batch query's](performance-guidance.md#batch-queries)gebruiken om de prestaties te verbeteren. Door batches te gebruiken, kunt u op een enorme manier uw aanvragen verwerken in een batch. Zo kunt u de retour latentie verminderen en de prestaties van uw toepassingen verbeteren.

Daarnaast kunt u, als u een degradatie van de algehele prestaties van uw data base ziet, de [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) en [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamische beheer weergaven bewaken om inzicht te krijgen in het CPU-, i/o-en geheugen gebruik. Uw prestaties zijn mogelijk van invloed op uw data base geen resources. Het kan zijn dat u de berekenings grootte en/of de servicelaag moet wijzigen op basis van de groeiende en verkleinde belasting vereisten.

Zie voor een uitgebreide set aanbevelingen voor het afstemmen van prestatie problemen: [uw data base afstemmen](performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Hoe kan ik ervoor te zorgen dat ik de juiste servicelaag en reken grootte gebruik

SQL Database biedt diverse service lagen Basic, Standard en Premium. Elke servicelaag krijgt een gegarandeerde voorspel bare prestatie die is gekoppeld aan die servicelaag. Afhankelijk van uw werk belasting hebt u mogelijk bursts van activiteit waarbij het gebruik van resources mogelijk het plafond van de huidige berekenings grootte kan aanraken. In dergelijke gevallen is het handig om eerst te evalueren of een afstemming kan helpen (bijvoorbeeld het toevoegen of wijzigen van een index, enzovoort). Als u nog steeds problemen met de limiet ondervindt, kunt u overwegen om over te stappen op een hogere service laag of reken grootte.

|**Servicelaag**|**Algemene use-case Scenario's**|
|---|---|
|**Basic**|Toepassingen met een gekente gebruiker en een Data Base die geen hoge gelijktijdigheids-, schaal-en prestatie vereisten hebben. |
|**Standard**|Toepassingen met een aanzienlijke gelijktijdigheids-, schaal-en prestatie vereisten in combi natie met lage tot gemiddelde i/o-aanvragen. |
|**Ultieme**|Toepassingen met veel gelijktijdige gebruikers, hoge CPU/geheugen en hoge IO-vereisten. Met hoge gelijktijdigheid, hoge door Voer en latentie gevoelige apps kunt u gebruikmaken van het Premium-niveau. |
|||

Om ervoor te zorgen dat u zich op de juiste reken grootte bevindt, kunt u uw query-en database Resource verbruik bewaken via een van de hierboven genoemde manieren in Hoe kan ik de prestaties en het resource gebruik in SQL Database bewaken. Als u merkt dat uw query's/data bases op consistente wijze worden uitgevoerd op CPU/geheugen, enzovoort, kunt u overwegen om omhoog te schalen naar een hogere reken grootte. Op dezelfde manier kunt u, als u weet dat zelfs tijdens uw piek uren, de resources niet zo veel gebruiken. u kunt overwegen om van de huidige reken grootte omlaag te schalen.

Als u een SaaS-app-patroon of een scenario voor database consolidatie hebt, kunt u overwegen een elastische pool te gebruiken voor de kosten optimalisatie. Elastische pool is een uitstekende manier om de samen voeging van data bases en kosten te optimaliseren. Zie voor meer informatie over het beheren van meerdere data bases met elastische pool: [groepen en data bases beheren](elastic-pool-manage.md#azure-portal).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Hoe vaak moet ik controles van de data base-integriteit uitvoeren voor mijn data base

SQL Database maakt gebruik van een aantal slimme technieken waarmee de IT-server bepaalde klassen gegevens automatisch kan verwerken en zonder dat er gegevens verloren gaan. Deze technieken zijn ingebouwd in de service en worden door de service gebruikt wanneer dat nodig is. Op regel matige basis worden de back-ups van uw data bases in de service getest door ze te herstellen en DBCC CHECKDB op het bestand uit te voeren. Als er problemen zijn, worden deze door SQL Database proactief opgelost. [Automatische pagina reparatie](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) wordt gebruikt voor het oplossen van pagina's die beschadigd zijn of problemen met de gegevens integriteit hebben. De database pagina's worden altijd gecontroleerd met de standaard instelling voor de CONTROLESOM die de integriteit van de pagina verifieert. SQL Database proactief bewaakt en controleert de gegevens integriteit van uw data base. als er problemen optreden, worden deze geadresseerd met de hoogste prioriteit. Daarnaast kunt u ervoor kiezen om eventueel uw eigen integriteits controles uit te voeren.  Zie voor meer informatie [integriteit van gegevens in SQL database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Verplaatsing van gegevens na migratie

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database-using-the-azure-portal"></a>Hoe kan ik gegevens exporteren en importeren als BACPAC-bestanden vanuit SQL Database met behulp van de Azure Portal

- **Exporteren**: u kunt uw data base in Azure SQL database exporteren als een BACPAC-bestand van de Azure Portal

   ![data base exporteren](./media/manage-data-after-migrating-to-database/database-export1.png)

- **Importeren**: u kunt gegevens ook importeren als een BACPAC-bestand in uw data base in Azure SQL database met behulp van de Azure Portal.

   ![data base importeren](./media/manage-data-after-migrating-to-database/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hoe kan ik gegevens tussen SQL Database en SQL Server synchroniseren

U kunt dit op verschillende manieren doen:

- **[Gegevens synchronisatie](sql-data-sync-data-sql-server-sql-database.md)** : met deze functie kunt u gegevens in twee richtingen synchroniseren tussen meerdere SQL server data bases en SQL database. Als u wilt synchroniseren met SQL Server-data bases, moet u de synchronisatie agent installeren en configureren op een lokale computer of een virtuele machine en de uitgaande TCP-poort 1433 openen.
- **[Transactie replicatie](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** : met transactie replicatie kunt u uw gegevens vanuit een SQL Server-Data Base synchroniseren naar Azure SQL database met het SQL Server exemplaar dat de uitgever is en het Azure SQL database de abonnee wordt. Momenteel wordt alleen deze configuratie ondersteund. Zie voor meer informatie over het migreren van uw gegevens van een SQL Server Data Base naar Azure SQL met minimale downtime: [transactie replicatie gebruiken](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [SQL database](sql-database-paas-overview.md).