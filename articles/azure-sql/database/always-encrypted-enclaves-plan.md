---
title: Plan voor Intel SGX enclaves en Attestation in Azure SQL Database
description: Plan de implementatie van Always Encrypted met beveiligde enclaves in Azure SQL Database.
keywords: versleutelen van gegevens, SQL-versleuteling, database versleuteling, gevoelige gegevens, Always Encrypted, beveiligde enclaves, SGX, Attestation
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732741"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Plan voor Intel SGX enclaves en Attestation in Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted met beveiligde enclaves voor Azure SQL Database is momenteel beschikbaar als **open bare preview**.

[Always encrypted met beveiligde enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) in Azure SQL database maakt gebruik van [Intel SGX-enclaves (software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) en is [Microsoft Azure Attestation](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation)vereist.

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Plannen voor Intel SGX in Azure SQL Database

Intel SGX is een op hardware gebaseerde vertrouwde Execution Environment-technologie. Intel SGX is beschikbaar voor data bases die gebruikmaken van het [vCore-model](service-tiers-vcore.md) en de generatie van de hardware van de [DC-serie](service-tiers-vcore.md?#dc-series) . Om ervoor te zorgen dat u Always Encrypted met beveiligde enclaves in uw data base kunt gebruiken, moet u de generatie van de fabrikant van de DC-serie selecteren tijdens het maken van de data base, maar u kunt ook uw bestaande data base bijwerken voor het gebruik van de generatie van de hardware van de DC-serie.

> [!NOTE]
> Intel SGX is niet beschikbaar in andere hardware dan DC-Series. Bijvoorbeeld: Intel SGX is niet beschikbaar voor GEN5-hardware en is niet beschikbaar voor data bases die gebruikmaken van het [DTU-model](service-tiers-dtu.md).

> [!IMPORTANT]
> Controleer de regionale Beschik baarheid van DC-Series voordat u de generatie van de hardware-serie voor uw data base configureert en zorg ervoor dat u de prestatie beperkingen begrijpt. Zie [DC-Series](service-tiers-vcore.md#dc-series)voor meer informatie.

## <a name="plan-for-attestation-in-azure-sql-database"></a>Attestation plannen in Azure SQL Database

[Microsoft Azure Attestation](../../attestation/overview.md) (preview) is een oplossing voor het controleren van vertrouwde uitvoerings omgevingen (TEEs), waaronder Intel SGX enclaves in Azure SQL-data bases met behulp van de generatie van de fabrikant van de DC-serie.

Als u Azure Attestation wilt gebruiken voor de verificatie van Intel SGX enclaves in Azure SQL Database, moet u het volgende doen:

1. Een [Attestation-provider](../../attestation/basic-concepts.md#attestation-provider) maken en deze configureren met een Attestation-beleid. 

2. Verleen uw logische Azure SQL-Server toegang tot de gemaakte Attestation-provider.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Rollen en verantwoordelijkheden bij het configureren van SGX-enclaves en-Attestation

Het configureren van uw omgeving voor de ondersteuning van Intel SGX enclaves en Attestation voor Always Encrypted in Azure SQL Database omvat het instellen van onderdelen van verschillende typen: Microsoft Azure Attestation, Azure SQL Database en toepassingen die enclave-Attestation activeren. Het configureren van onderdelen van elk type wordt uitgevoerd door gebruikers, ervan uitgaande dat een van de onderstaande afzonderlijke rollen:

- Attestation-beheerder: maakt een Attestation-provider in Microsoft Azure attest, auteurs het Attestation-beleid, verleent de logische Azure SQL-Server toegang tot de Attestation-provider en deelt de Attestation-URL die naar het beleid verwijst naar toepassings beheerders.
- Azure SQL Database beheerder: Hiermee schakelt u SGX-enclaves in data bases in door het genereren van de fabrikant van de DC-serie te selecteren en de Attestation-beheerder te voorzien van de identiteit van de logische Azure SQL-Server die toegang moet hebben tot de Attestation-provider.
- Toepassings beheerder: Hiermee configureert u toepassingen met de Attestation-URL die is verkregen door de Attestation-beheerder.

In productie omgevingen (die echte gevoelige gegevens verwerken), is het belang rijk dat uw organisatie voldoet aan functie scheiding bij het configureren van Attestation, waarbij elke afzonderlijke rol wordt gebruikt door verschillende personen. Met name als het doel van de implementatie van Always Encrypted in uw organisatie is het verminderen van de aanval surface area door Azure SQL Database beheerders geen toegang te geven tot gevoelige gegevens, moeten Azure SQL Database beheerders geen Attestation-beleids regels beheren.

## <a name="next-steps"></a>Volgende stappen

- [Intel SGX inschakelen voor uw Azure-SQL database](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Zie ook

- [Zelf studie: aan de slag met Always Encrypted met beveiligde enclaves in Azure SQL Database](always-encrypted-enclaves-getting-started.md)