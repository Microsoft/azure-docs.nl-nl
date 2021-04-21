---
title: Beveiligingsoverzicht
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Meer informatie over beveiliging in Azure SQL Database en Azure SQL Managed Instance, waaronder hoe deze verschilt van SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 084f9aae16cfbf495f05c90c8244b2b9b71cf624
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812931"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Een overzicht van Azure SQL Database en SQL Managed Instance beveiligingsmogelijkheden
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In dit artikel worden de basisbeginselen beschreven van het beveiligen van de gegevenslaag van een toepassing met [Azure SQL Database,](sql-database-paas-overview.md) [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). De beschreven beveiligingsstrategie volgt de gelaagde benadering van diepgaande verdediging, zoals wordt weergegeven in de onderstaande afbeelding, en verplaatst zich van buitenaf in:

![Diagram van gelaagde diepgaande verdediging. Klantgegevens zijn ingepakt in lagen van netwerkbeveiliging, toegangsbeheer en bedreigings- en informatiebeveiliging.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Netwerkbeveiliging

Microsoft Azure SQL Database, SQL Managed Instance en Azure Synapse Analytics bieden een relationele databaseservice voor cloud- en bedrijfstoepassingen. Ter bescherming van klantgegevens verhinderen firewalls netwerktoegang tot de server totdat expliciet toegang wordt verleend op basis van IP-adres of de oorsprong van het virtuele Azure-netwerkverkeer.

### <a name="ip-firewall-rules"></a>IP-firewallregels

IP-firewallregels verlenen toegang tot databases op basis van het oorspronkelijke IP-adres van elke aanvraag. Zie Overview of Azure SQL Database and Azure Synapse Analytics firewall rules (Overzicht van Azure SQL Database [en Azure Synapse Analytics firewallregels) voor meer informatie.](firewall-configure.md)

### <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtueel netwerk

[Service-eindpunten voor virtuele netwerken](../../virtual-network/virtual-network-service-endpoints-overview.md) breiden de connectiviteit van uw virtuele netwerk uit via de Azure-backbone en stellen Azure SQL Database in staat om het subnet van het virtuele netwerk te identificeren waar het verkeer van afkomstig is. Als u wilt dat verkeer toegang Azure SQL Database, gebruikt u de [SQL-servicetags](../../virtual-network/network-security-groups-overview.md) om uitgaand verkeer via netwerkbeveiligingsgroepen toe te staan.

[Met regels voor virtuele](vnet-service-endpoint-rule-overview.md) netwerken Azure SQL Database alleen communicatie accepteren die vanuit geselecteerde subnetten binnen een virtueel netwerk wordt verzonden.

> [!NOTE]
> Toegang beheren met firewallregels is *niet van toepassing* op **SQL Managed Instance.** Zie Verbinding maken met een beheerd exemplaar voor meer informatie over de benodigde [netwerkconfiguratie](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Toegangsbeheer

> [!IMPORTANT]
> Het beheren van databases en servers in Azure wordt beheerd door de roltoewijzingen van uw portalgebruikersaccount. Zie Op rollen gebaseerd toegangsbeheer in Azure in het artikel [Azure Portal.](../../role-based-access-control/overview.md)

### <a name="authentication"></a>Verificatie

Verificatie is het proces waarbij wordt bewezen dat de gebruiker is wie hij of zij claimt te zijn. Azure SQL Database en SQL Managed Instance twee typen verificatie ondersteunen:

- **SQL-verificatie:**

    SQL-verificatie verwijst naar de verificatie van een gebruiker bij het maken van verbinding met Azure SQL Database of Azure SQL Managed Instance gebruikersnaam en wachtwoord. Een **aanmeldgegevens** van de serverbeheerder met een gebruikersnaam en wachtwoord moeten worden opgegeven wanneer de server wordt gemaakt. Met deze referenties kan een **serverbeheerder** zich als de database-eigenaar verifiëren bij elke database op die server of instantie. Daarna kunnen extra SQL-aanmeldingen en -gebruikers worden gemaakt door de serverbeheerder, zodat gebruikers verbinding kunnen maken met behulp van gebruikersnaam en wachtwoord.

- **Azure Active Directory verificatie:**

    Azure Active Directory verificatie is een mechanisme om verbinding te maken met [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) [en Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in Azure Active Directory (Azure AD). Met Azure AD-verificatie kunnen beheerders de identiteiten en machtigingen van databasegebruikers samen met andere Azure-services op één centrale locatie centraal beheren. Dit omvat het minimaliseren van wachtwoordopslag en maakt gecentraliseerd roulatiebeleid voor wachtwoorden mogelijk.

     Er moet een serverbeheerder met de **naam Active Directory-beheerder** worden gemaakt om Azure AD-verificatie te kunnen gebruiken met SQL Database. Zie Connecting to SQL Database By Using Azure Active Directory Authentication (Verbinding maken met SQL Database [met behulp van Azure Active Directory-verificatie) voor meer informatie.](authentication-aad-overview.md) Azure AD-verificatie ondersteunt zowel beheerde als federatief accounts. De federatief accounts ondersteunen Windows-gebruikers en -groepen voor een klantdomein dat is ge federeerd met Azure AD.

    Er zijn extra Azure AD-verificatieopties beschikbaar voor universele [verificatie van Active Directory SQL Server Management Studio](authentication-mfa-ssms-overview.md) verbindingen, waaronder [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) en [voorwaardelijke toegang.](conditional-access-configure.md)

> [!IMPORTANT]
> Het beheren van databases en servers in Azure wordt beheerd door de roltoewijzingen van uw portalgebruikersaccount. Zie Op rollen gebaseerd toegangsbeheer van Azure in Azure Portal voor [meer informatie over Azure Portal.](../../role-based-access-control/overview.md) Toegang beheren met firewallregels is *niet van toepassing* op **SQL Managed Instance.** Raadpleeg het volgende artikel over het maken [van verbinding met een beheerd exemplaar](../managed-instance/connect-application-instance.md) voor meer informatie over de benodigde netwerkconfiguratie.

## <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar de machtigingen die zijn toegewezen aan een gebruiker in een database in Azure SQL Database of Azure SQL Managed Instance en bepaalt wat de gebruiker mag doen. Machtigingen worden beheerd door gebruikersaccounts toe te voegen aan [databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles) en machtigingen op databaseniveau toe te wijzen aan deze rollen of door de gebruiker bepaalde [machtigingen op objectniveau te verlenen.](/sql/relational-databases/security/permissions-database-engine) Zie Aanmeldingen en gebruikers [voor meer informatie](logins-create-manage.md)

Als een best practice kunt u aangepaste rollen maken wanneer dat nodig is. Voeg gebruikers toe aan de rol met de minste bevoegdheden die nodig zijn om hun taakfunctie uit te kunnen doen. Wijs niet rechtstreeks machtigingen toe aan gebruikers. Het serverbeheerdersaccount is lid van de ingebouwde db_owner-rol, die uitgebreide machtigingen heeft en alleen mag worden verleend aan enkele gebruikers met administratieve taken. Gebruik voor toepassingen EXECUTE [AS](/sql/t-sql/statements/execute-as-clause-transact-sql) om de uitvoeringscontext van de aangeroepen module op te geven of gebruik [Toepassingsrollen](/sql/relational-databases/security/authentication-access/application-roles) met beperkte machtigingen. Deze praktijk zorgt ervoor dat de toepassing die verbinding maakt met de database, de minste bevoegdheden heeft die nodig zijn voor de toepassing. Het volgen van deze best practices bevordert ook de scheiding van taken.

### <a name="row-level-security"></a>Beveiliging op rijniveau

Row-Level Security kunnen klanten de toegang tot rijen in een databasetabel bepalen op basis van de kenmerken van de gebruiker die een query uitvoert (bijvoorbeeld groepslidmaatschap of uitvoeringscontext). Row-Level Security kan ook worden gebruikt voor het implementeren van aangepaste beveiligingsconcepten op basis van labels. Zie Beveiliging op rijniveau [voor meer informatie.](/sql/relational-databases/security/row-level-security)

![Diagram dat laat zien Row-Level beveiliging afzonderlijke rijen van een SQL database toegang door gebruikers afschermt via een client-app.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Bescherming tegen bedreigingen

SQL Database en SQL Managed Instance klantgegevens te beveiligen door mogelijkheden voor controle en detectie van bedreigingen te bieden.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-controle in Azure Monitor logboeken en Event Hubs

SQL Database en SQL Managed Instance houdt databaseactiviteiten bij en helpt de naleving van beveiligingsstandaarden te handhaven door databasegebeurtenissen vast te leggen in een auditlogboek in een Azure-opslagaccount dat eigendom is van de klant. Met controle kunnen gebruikers lopende databaseactiviteiten bewaken, en historische activiteiten analyseren en onderzoeken om mogelijke bedreigingen of vermoedelijk misbruik en schendingen van de beveiliging te identificeren. Zie Aan de slag met SQL Database [Auditing voor meer informatie.](../../azure-sql/database/auditing-overview.md)  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Advanced Threat Protection analyseert uw logboeken om ongebruikelijk gedrag en mogelijk schadelijke pogingen om toegang te krijgen tot of misbruik te maken van databases te detecteren. Er worden waarschuwingen gemaakt voor verdachte activiteiten, zoals SQL-injectie, mogelijke gegevensinfiltratie en beveiligingsaanvallen of voor afwijkingen in toegangspatronen om escalaties van bevoegdheden en het gebruik van geschonden referenties te ondervangen. Waarschuwingen worden bekeken vanuit de  [Azure Security Center](https://azure.microsoft.com/services/security-center/), waar de details van de verdachte activiteiten worden gegeven en aanbevelingen voor verder onderzoek, samen met acties om de bedreiging te beperken. Advanced Threat Protection kan per server worden ingeschakeld tegen extra kosten. Zie Aan de slag met [Advanced Threat Protection SQL Database meer informatie.](threat-detection-configure.md)

![Diagram met de bewakingstoegang van SQL-bedreigingsdetectie tot de SQL database voor een web-app van een externe aanvaller en kwaadwillende insider.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Gegevensbeveiliging en -versleuteling

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (versleuteling in transit)

SQL Database, SQL Managed Instance en Azure Synapse Analytics klantgegevens beveiligen door gegevens in beweging te versleutelen [met Transport Layer Security (TLS).](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

SQL Database, SQL Managed Instance en Azure Synapse Analytics versleuteling (SSL/TLS) te allen tijde afdwingen voor alle verbindingen. Dit zorgt ervoor dat alle gegevens 'in transit' tussen de client en de server worden versleuteld, ongeacht de instelling van **Encrypt** of **TrustServerCertificate** in de connection string.

Als een best practice u aan dat u in de connection string die door de __ toepassing wordt gebruikt, een versleutelde verbinding opgeeft en het servercertificaat niet vertrouwt. Dit dwingt uw toepassing om het servercertificaat te verifiëren en voorkomt zo dat uw toepassing kwetsbaar is voor man-in-the-middle-aanvallen.

Wanneer u bijvoorbeeld het ADO.NET wordt dit bereikt via **Encrypt=True** en **TrustServerCertificate=False.** Als u uw gegevens connection string de Azure Portal, heeft deze de juiste instellingen.

> [!IMPORTANT]
> Houd er rekening mee dat sommige niet-Microsoft-stuurprogramma's niet standaard TLS gebruiken of afhankelijk zijn van een oudere versie van TLS (<1.2) om te kunnen functioneren. In dit geval kunt u op de server nog steeds verbinding maken met uw database. We raden u echter aan om de beveiligingsrisico's te evalueren die het mogelijk maken dat dergelijke stuurprogramma's en toepassingen verbinding kunnen maken met SQL Database, met name als u gevoelige gegevens opgeslagen.
>
> Zie TLS-overwegingen voor meer informatie over [TLS en connectiviteit](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (versleuteling-at-rest)

[Transparent Data Encryption (TDE)](transparent-data-encryption-tde-overview.md) voor SQL Database, SQL Managed Instance en Azure Synapse Analytics voegt een beveiligingslaag toe om data-at-rest te beschermen tegen onbevoegde of offline toegang tot onbewerkte bestanden of back-ups. Veelvoorkomende scenario's zijn diefstal van datacenters of onbeveiligde verwijdering van hardware of media, zoals schijfstations en back-uptapes.TDE versleutelt de hele database met behulp van een AES-versleutelingsalgoritme, waarvoor toepassingsontwikkelaars geen wijzigingen in bestaande toepassingen moeten aanbrengen.

In Azure worden alle nieuwe databases standaard versleuteld en wordt de databaseversleutelingssleutel beveiligd met een ingebouwd servercertificaat.  Certificaatonderhoud en -rotatie worden beheerd door de service en vereisen geen invoer van de gebruiker. Klanten die liever de controle over de versleutelingssleutels hebben, kunnen de sleutels in [Azure Key Vault.](../../key-vault/general/security-features.md)

### <a name="key-management-with-azure-key-vault"></a>Sleutelbeheer met Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) voor [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) kunnen klanten eigenaar worden van sleutelbeheer en roulatie met behulp van [Azure Key Vault,](../../key-vault/general/security-features.md)het externe sleutelbeheersysteem in de cloud van Azure. Als de toegang van de database tot de sleutelkluis is ingetrokken, kan een database niet worden ontsleuteld en in het geheugen worden gelezen. Azure Key Vault biedt een centraal platform voor sleutelbeheer, maakt gebruik van nauw bewaakte hardwarebeveiligingsmodules (HMS's) en maakt scheiding van taken mogelijk tussen het beheer van sleutels en gegevens om te voldoen aan de beveiligingsvereisten.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (versleuteling in gebruik)

![Diagram met de basisbeginselen van Always Encrypted functie. Een SQL database met een vergrendeling is alleen toegankelijk voor een app die een sleutel bevat.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) is een functie die is ontworpen om gevoelige gegevens die zijn opgeslagen in specifieke databasekolommen te beschermen tegen toegang (bijvoorbeeld creditcardnummers, nationale identificatienummers of gegevens op basis van de noodzaak om dit _te_ weten). Dit omvat databasebeheerders of andere bevoegde gebruikers die gemachtigd zijn om toegang te krijgen tot de database om beheertaken uit te voeren, maar geen toegang hoeven te hebben tot de specifieke gegevens in de versleutelde kolommen. De gegevens worden altijd versleuteld, wat betekent dat de versleutelde gegevens alleen worden ontsleuteld voor verwerking door clienttoepassingen met toegang tot de versleutelingssleutel. De versleutelingssleutel wordt nooit blootgesteld aan SQL Database of SQL Managed Instance en kan worden opgeslagen in het [Windows-certificaatopslag](always-encrypted-certificate-store-configure.md) of in [Azure Key Vault](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Dynamische gegevensmaskering

![Diagram met dynamische gegevensmaskering. Een zakelijke app verzendt gegevens naar een SQL database waarmee de gegevens worden gemaskeerd voordat deze weer naar de bedrijfs-app worden verzonden.](./media/security-overview/azure-database-ddm.png)

Met dynamische gegevensmaskering wordt de blootstelling van gevoelige gegevens beperkt door deze gegevens te maskeren voor niet-gemachtigde gebruikers. Dynamische gegevensmaskering detecteert automatisch mogelijk gevoelige gegevens in Azure SQL Database en SQL Managed Instance en biedt aanbevelingen die kunnen worden gedaan om deze velden te maskeren, met minimale gevolgen voor de toepassingslaag. Dit werkt als volgt: de gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden, worden bedekt, terwijl de gegevens in de database niet worden gewijzigd. Zie Aan de slag met dynamische SQL Database [en SQL Managed Instance voor meer informatie.](dynamic-data-masking-overview.md)

## <a name="security-management"></a>Beveiligingsbeheer

### <a name="vulnerability-assessment"></a>Evaluatie van beveiligingsproblemen

[Evaluatie van beveiligingsproblemen](sql-vulnerability-assessment.md) is een eenvoudig te configureren service die potentiële beveiligingsproblemen in de database kan ontdekken, volgen en verhelpen, met als doel de algehele databasebeveiliging proactief te verbeteren. Evaluatie van beveiligingsleed maakt deel uit van de Azure Defender voor SQL, een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Evaluatie van beveiligingsleed kan worden geopend en beheerd via de centrale Azure Defender voor SQL Portal.

### <a name="data-discovery-and-classification"></a>Gegevensdetectie en -classificatie

Gegevensdetectie en -classificatie (momenteel in preview) biedt geavanceerde mogelijkheden die zijn ingebouwd in Azure SQL Database en SQL Managed Instance voor het detecteren, classificeren, labelen en beveiligen van de gevoelige gegevens in uw databases. Het detecteren en classificeren van uw zeer gevoelige gegevens (zakelijke/financiële gegevens, gezondheidszorg, persoonlijke gegevens, enzovoort) kan een belangrijke rol spelen in de status van gegevensbeveiliging in uw organisatie. Dit kan dienen als infrastructuur om:

- Verschillende beveiligingsscenario's, zoals bewaking (controle) en waarschuwingen over afwijkende toegang tot gevoelige gegevens.
- Toegang tot databases met uiterst gevoelige gegevens beheren en de beveiliging van de databases verbeteren.
- Te helpen voldoen aan standaarden op het gebied van gegevensbescherming en aan vereisten voor naleving van regelgeving.

Zie Aan de slag met gegevensdetectie en [-classificatie voor meer informatie.](data-discovery-and-classification-overview.md)

### <a name="compliance"></a>Naleving

Naast de bovenstaande functies en functionaliteit die uw toepassing kunnen helpen aan verschillende beveiligingsvereisten te voldoen, neemt Azure SQL Database ook deel aan reguliere controles en is het gecertificeerd volgens een aantal nalevingsstandaarden. Zie het vertrouwenscentrum van [Microsoft Azure](https://www.microsoft.com/trust-center/compliance/compliance-overview) voor meer informatie. Hier vindt u de meest recente lijst met SQL Database nalevingscertificeringen.

## <a name="next-steps"></a>Volgende stappen

- Zie Aanmeldingen en gebruikersaccounts beheren voor een bespreking van het gebruik van aanmeldingen, gebruikersaccounts, databaserollen en machtigingen in SQL Database en [SQL Managed Instance.](logins-create-manage.md)
- Zie Controleren voor een bespreking van [databasecontrole.](../../azure-sql/database/auditing-overview.md)
- Zie Detectie van bedreigingen voor een bespreking [van detectie van bedreigingen.](threat-detection-configure.md)