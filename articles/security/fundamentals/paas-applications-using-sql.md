---
title: PaaS-data bases beveiligen in azure | Microsoft Docs
description: 'Meer informatie over de aanbevolen procedures voor de beveiliging van Azure SQL Database en Azure Synapse Analytics voor het beveiligen van uw PaaS-web-en mobiele toepassingen. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 278812754c636d434bf579c0408832f1e99d3445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408070"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Aanbevolen procedures voor het beveiligen van PaaS-data bases in azure

In dit artikel bespreken we een verzameling [Azure SQL database](../../azure-sql/database/sql-database-paas-overview.md) en de aanbevolen procedures voor [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) voor het beveiligen van uw PaaS-web en mobiele toepassingen (platform-as-a-Service). Deze aanbevolen procedures zijn afgeleid van onze ervaring met Azure en de ervaringen van klanten, zoals uzelf.

Azure SQL Database en Azure Synapse Analytics bieden een relationele database service voor uw op internet gebaseerde toepassingen. Laten we kijken naar de services die uw toepassingen en gegevens helpen beschermen bij het gebruik van Azure SQL Database en Azure Synapse Analytics in een PaaS-implementatie:

- Verificatie Azure Active Directory (in plaats van SQL Server-verificatie)
- Azure SQL-firewall
- Transparent Data Encryption (TDE)

## <a name="use-a-centralized-identity-repository"></a>Een gecentraliseerde identiteits opslagplaats gebruiken

Azure SQL Database kunnen worden geconfigureerd voor het gebruik van een van de twee typen verificatie:

- **SQL-verificatie** maakt gebruik van een gebruikers naam en wacht woord. Wanneer u de server voor uw data base hebt gemaakt, hebt u de aanmelding ' Server beheerder ' opgegeven met een gebruikers naam en wacht woord. Met deze referenties kunt u zich bij elke Data Base op die server als de eigenaar van de data base verifiëren.

- **Azure Active Directory-verificatie** maakt gebruik van identiteiten die worden beheerd door Azure Active Directory en wordt ondersteund voor beheerde en geïntegreerde domeinen. Als u Azure Active Directory-verificatie wilt gebruiken, moet u een andere server beheerder maken met de naam ' Azure AD-beheerder ', waarmee Azure AD-gebruikers en-groepen kunnen worden beheerd. Deze beheerder kan ook alle bewerkingen uitvoeren die reguliere serverbeheerders kunnen uitvoeren.

[Azure Active Directory-verificatie](../../active-directory/develop/authentication-vs-authorization.md) is een mechanisme om verbinding te maken met Azure SQL database en Azure Synapse Analytics met behulp van identiteiten in azure Active Directory (AD). Azure AD biedt een alternatief voor SQL Server verificatie, zodat u de verspreiding van gebruikers identiteiten op database servers kunt stoppen. Met Azure AD-verificatie kunt u de identiteiten van database gebruikers en andere micro soft-services centraal beheren op één centrale locatie. Centraal identiteitsbeheer biedt één plek voor het beheren van databasegebruikers en vereenvoudigt het machtigingenbeheer.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Voor delen van het gebruik van Azure AD in plaats van SQL-verificatie

- Maakt rotatie van wachtwoorden op één plek mogelijk.
- Beheert database machtigingen met externe Azure AD-groepen.
- Elimineert het opslaan van wacht woorden door geïntegreerde Windows-verificatie en andere verificatie methoden die worden ondersteund door Azure AD in te scha kelen.
- Gebruikt Inge sloten database gebruikers voor het verifiëren van identiteiten op database niveau.
- Biedt ondersteuning voor verificatie op basis van tokens voor toepassingen die verbinding maken met SQL Database.
- Ondersteunt domein Federatie met Active Directory Federation Services (ADFS) of systeem eigen gebruikers-en wachtwoord verificatie voor een lokale Azure AD zonder domein synchronisatie.
- Ondersteunt verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory universele verificatie, waaronder [multi-factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md). MFA omvat robuuste verificatie met een scala aan gebruikersvriendelijke verificatieopties, waaronder telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app. Zie voor meer informatie [universele verificatie met SQL database en Azure Synapse Analytics](../../azure-sql/database/authentication-mfa-ssms-overview.md).

Zie voor meer informatie over Azure AD-verificatie:

- [Azure Active Directory verificatie gebruiken voor verificatie met SQL Database, een beheerd exemplaar of Azure Synapse Analytics](../../azure-sql/database/authentication-aad-overview.md)
- [Verificatie voor Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Ondersteuning voor verificatie op basis van tokens voor Azure SQL Database met Azure AD-verificatie](../../azure-sql/database/authentication-aad-overview.md)

> [!NOTE]
> Zie [Azure AD-functies en-beperkingen](../../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)om ervoor te zorgen dat Azure Active Directory geschikt is voor uw omgeving.

## <a name="restrict-access-based-on-ip-address"></a>Toegang beperken op basis van IP-adres

U kunt Firewall regels maken die bereiken van acceptabele IP-adressen opgeven. Deze regels kunnen worden gericht op het niveau van de server en de data base. U kunt het beste firewall regels op database niveau gebruiken om de beveiliging te verbeteren en uw data base draagbaarer te maken. Firewall regels op server niveau worden het beste gebruikt voor beheerders en wanneer u veel data bases hebt die dezelfde toegangs vereisten hebben, maar u niet de tijd wilt best Eden aan het configureren van elke Data Base afzonderlijk.

SQL Database standaard IP-adres beperkingen van de bron bieden toegang vanaf elk Azure-adres, inclusief andere abonnementen en tenants. U kunt dit beperken om uw IP-adressen alleen toegang te geven tot het exemplaar. Zelfs met uw SQL-firewall-en IP-adres beperkingen is sterke verificatie nog steeds nodig. Bekijk de aanbevelingen die eerder in dit artikel zijn gemaakt.

Zie voor meer informatie over Azure SQL-firewall en IP-beperkingen:

- [Access Control van Azure SQL Database en Azure Synapse Analytics](../../azure-sql/database/logins-create-manage.md)
- [Firewall regels voor Azure SQL Database en Azure Synapse Analytics](../../azure-sql/database/firewall-configure.md)

## <a name="encrypt-data-at-rest"></a>Data-at-rest versleutelen

[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) is standaard ingeschakeld. TDE versleutelt transparante SQL Server, Azure SQL Database en Azure Synapse Analytics-gegevens en-logboek bestanden. TDE beschermt tegen inbreuk op directe toegang tot de bestanden of de back-up. Zo kunt u gegevens in rust versleutelen zonder dat u bestaande toepassingen hoeft te wijzigen. TDE moet altijd blijven ingeschakeld. Hierdoor wordt een aanvaller echter niet gestopt met het normale toegangs traject. TDE biedt de mogelijkheid om te voldoen aan de vele wetten, voor schriften en richt lijnen die in verschillende branches zijn gevestigd.

Azure SQL beheert belang rijke problemen met betrekking tot TDE. Net als bij TDE moet u on-premises speciale aandacht best rijken om te zorgen voor herstel baarheid en bij het verplaatsen van data bases. In meer geavanceerde scenario's kunnen de sleutels expliciet worden beheerd in Azure Key Vault via Extensible Key Management. Zie [TDe inschakelen op SQL Server met EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Dit maakt ook Bring Your Own Key (BYOK) mogelijk via de BYOK-functie van Azure-sleutel kluizen.

Azure SQL biedt versleuteling voor kolommen via [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Hiermee kunnen alleen geautoriseerde toepassingen toegang krijgen tot gevoelige kolommen. Het gebruik van dit type versleuteling beperkt SQL-query's voor versleutelde kolommen tot op gelijkheid gebaseerde waarden.

Versleuteling op toepassings niveau moet ook worden gebruikt voor selectieve gegevens. Gegevens soevereiniteit kan soms worden verholpen door het versleutelen van gegevens met een sleutel die in het juiste land/regio wordt bewaard. Hiermee wordt voor komen dat een probleem wordt veroorzaakt door een onopzettelijke gegevens overdracht, omdat het niet mogelijk is om de gegevens zonder de sleutel te ontsleutelen, uitgaande van een sterk algoritme (zoals AES 256).

U kunt extra voorzorgsmaatregelen gebruiken om de data base te beveiligen, zoals het ontwerpen van een beveiligd systeem, het versleutelen van vertrouwelijke assets en het bouwen van een firewall rond de database servers.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een verzameling van SQL Database en de best practices voor beveiliging van Azure Synapse Analytics geïntroduceerd voor het beveiligen van uw PaaS-web-en mobiele toepassingen. Zie voor meer informatie over het beveiligen van uw PaaS-implementaties:

- [PaaS-implementaties beveiligen](paas-deployments.md)
- [PaaS-webtoepassingen en mobiele toepassingen beveiligen met Azure-app Services](paas-applications-using-app-services.md)