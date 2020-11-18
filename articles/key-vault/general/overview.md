---
title: Overzicht van Azure Key Vault - Azure Key Vault
description: Azure Key Vault is een archief met beveiligde geheimen dat zorgt voor het beheer van geheimen, sleutels en certificaten, volledig ondersteund door hardwarebeveiligingsmodules.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5025c4ad81433b6eb18f5beded860625791ffce5
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445095"
---
# <a name="about-azure-key-vault"></a>Over Azure Key Vault

Met Azure Key Vault kunt u de volgende problemen oplossen:

- **Geheimenbeheer** - Met Azure Key Vault kunt u veilig de toegang tot tokens, wachtwoorden, certificaten, API-sleutels en andere geheimen opslaan en strikt beheren
- **Sleutelbeheer** - U kunt Azure Key Vault ook gebruiken als een oplossing voor sleutelbeheer. Met Azure Key Vault kunt u eenvoudig de versleutelingssleutels maken en beheren waarmee uw gegevens worden versleuteld. 
- **Certificaatbeheer** - Azure Key Vault is ook een service waarmee u eenvoudig openbare en persoonlijke TLS/SSL-certificaten (Transport Layer Security/Secure Sockets Layer) kunt inrichten, beheren en implementeren voor gebruik met Azure en uw interne verbonden bronnen.

Azure Key Vault heeft twee servicelagen: De Standard-laag, die wordt versleuteld met een softwaresleutel, en de Premium-laag, met sleutels die met HSM beveiligd zijn. Als u een vergelijking tussen de lagen Standard en Premium wilt zien, raadpleegt u de [pagina met prijzen voor Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="why-use-azure-key-vault"></a>Waarom zou ik Azure Key Vault gebruiken?

### <a name="centralize-application-secrets"></a>Toepassingsgeheimen centraliseren

Door de opslag van toepassingsgeheimen te centraliseren in Azure Key Vault kunt u de verdeling ervan bepalen. Key Vault vermindert de kans dat geheimen per ongeluk worden gelekt aanzienlijk. Bij gebruik van Key Vault hoeven ontwikkelaars van toepassingen geen beveiligingsinformatie meer in de toepassing zelf op te slaan. Doordat u geen beveiligingsinformatie in toepassingen hoeft op te slaan elimineert u de noodzaak om deze informatie onderdeel van de code te maken. Een toepassing wil bijvoorbeeld verbinding maken met een database. In plaats van de verbindingsreeks op te slaan in de code van de app, kunt u deze veilig bewaren in Key Vault.

Met behulp van URI's hebben uw toepassingen veilig toegang tot de benodigde gegevens. Met deze URI's kunnen de toepassingen specifieke versies van een geheim ophalen. U hoeft geen aangepaste code te schrijven om de geheime informatie die in Key Vault is opgeslagen te beveiligen.

### <a name="securely-store-secrets-and-keys"></a>Geheimen en sleutels veilig opslaan

Voor toegang tot een sleutelkluis is de juiste verificatie en autorisatie vereist voordat een aanroeper (gebruiker of toepassing) toegang kan krijgen. Met verificatie wordt de identiteit van de aanroeper vastgesteld, terwijl autorisatie bepaalt welke bewerkingen de aanroeper mag uitvoeren.

Verificatie wordt uitgevoerd via Azure Active Directory. Autorisatie kan worden uitgevoerd via op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) of Key Vault-toegangsbeleid. Azure RBAC wordt gebruikt bij het beheren van de kluizen. Toegangsbeleid tot sleutelkluizen wordt gebruikt bij pogingen om toegang te krijgen tot gegevens in een kluis.

Azure-sleutelkluizen kunnen worden beveiligd met software of, zoals bij de Premium-laag voor Azure Key Vault, met hardware door middel van HSM's (Hardware Security Modules). Met software beveiligde sleutels, geheimen en certificaten worden beveiligd door Azure. Hiervoor wordt gebruikgemaakt van algoritmen en sleutellengten die voldoen aan de industriestandaard.  Voor situaties waar extra zekerheid is vereist, kunt u sleutels in HSM's importeren of genereren die nooit verdergaan dan de HSM-grens. Azure Key Vault maakt gebruik van nCipher-HSM's. Deze zijn Federal Information Processing Standards (FIPS) 140-2 Level 2 gevalideerd. U kunt nCipher-hulpprogramma's gebruiken om een sleutel te verplaatsen van uw HSM naar Azure Key Vault.

Tot slot is Azure Key Vault zodanig ontworpen dat Microsoft uw gegevens niet kan zien of extraheren.

### <a name="monitor-access-and-use"></a>Toegang tot en gebruik van controles

Nadat u enkele sleutelkluizen hebt gemaakt, kunt u controleren hoe en wanneer er toegang tot uw sleutels en geheimen is gezocht. U kunt de activiteit controleren door logboekregistratie voor uw kluizen in te schakelen. U kunt Azure Key Vault voor het volgende configureren:

- Archiveren naar een opslagaccount.
- Streamen naar een Event Hub.
- De logboeken verzenden naar logboeken van Azure Monitor.

U hebt de controle over uw logboeken en kunt ze beveiligen door de toegang te beperken. Bovendien kunt u logboeken verwijderen die u niet meer nodig hebt.

### <a name="simplified-administration-of-application-secrets"></a>Vereenvoudigd beheer van toepassingsgeheimen

Bij het opslaan van waardevolle gegevens moet u verschillende stappen uitvoeren. Beveiligingsinformatie moet worden beschermd, moet een bepaalde levenscyclus volgen en moet maximaal beschikbaar zijn. Met Azure Key Vault vereenvoudigt u het proces om aan deze vereisten te voldoen door:

- Het wegnemen van de noodzaak tot interne kennis van Hardware Security Modules.
- Het op korte termijn omhoog schalen om de gebruikspieken van uw organisatie aan te kunnen.
- Het repliceren van de inhoud van uw Key Vault binnen een regio en naar een secundaire regio. Gegevensreplicatie zorgt voor een maximale beschikbaarheid en de beheerder hoeft geen actie te ondernemen om de failover te activeren.
- Het bieden van standaard Azure-beheeropties via de portal, Azure CLI en PowerShell.
- Het automatiseren van bepaalde taken voor certificaten die u aanschaft bij openbare CA's, zoals registreren en verlengen.

Bovendien kunt u met sleutelkluizen van Azure toepassingsgeheimen van elkaar scheiden. Toepassingen krijgen alleen toegang tot de kluis die ze mogen benaderen en ze mogen alleen specifieke bewerkingen uitvoeren. U kunt een Azure-sleutelkluis per toepassing maken en de geheimen die in een bepaalde sluitelkleus zijn opgeslagen beperken tot een specifieke toepassing en team van ontwikkelaars.

### <a name="integrate-with-other-azure-services"></a>Integreren met andere Azure-services

Key Vault wordt in Azure gebruikt als beveiligd archief om scenario's te vereenvoudigen, zoals:
-  [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  De functionaliteit [altijd versleuteld]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) en [Transparent Data Encryption]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) in SQL-server en Azure SQL Database
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

Key Vault zelf kan worden geïntegreerd met opslagaccounts, Event Hubs en logboekanalyses.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-certificates.md)
- [Snelstart: een Azure Key Vault maken met behulp van de CLI](../secrets/quick-create-cli.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
