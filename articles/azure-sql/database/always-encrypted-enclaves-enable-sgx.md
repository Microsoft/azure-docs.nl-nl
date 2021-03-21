---
title: Intel SGX inschakelen voor uw Azure SQL Database
description: Meer informatie over het inschakelen van Intel SGX voor Always Encrypted met beveiligde enclaves in Azure SQL Database door een generatie van SGX ingeschakelde hardware te selecteren.
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
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733753"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Intel SGX inschakelen voor uw Azure SQL Database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted met beveiligde enclaves voor Azure SQL Database is momenteel beschikbaar als **open bare preview**.

[Always encrypted met beveiligde enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) in Azure SQL database gebruikt [Intel SGX-enclaves (software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) . Voor Intel SGX moet de data base gebruikmaken van het VCore- [model](service-tiers-vcore.md) en de generatie van de fabrikant van de [DC-serie](service-tiers-vcore.md#dc-series) .

Het configureren van de generatie van de fabrikant van de DC-serie voor het inschakelen van Intel SGX enclaves is de verantwoordelijkheid van de beheerder van de Azure SQL Database. Zie [rollen en verantwoordelijkheden bij het configureren van SGX-enclaves en-Attestation](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Intel SGX is niet beschikbaar in andere hardware dan DC-Series. Bijvoorbeeld: Intel SGX is niet beschikbaar voor GEN5-hardware en is niet beschikbaar voor data bases die gebruikmaken van het [DTU-model](service-tiers-dtu.md).

> [!IMPORTANT]
> Controleer de regionale Beschik baarheid van DC-Series voordat u de generatie van de hardware-serie voor uw data base configureert en zorg ervoor dat u de prestatie beperkingen begrijpt. Zie [DC-Series](service-tiers-vcore.md#dc-series)voor meer informatie.

Zie [een hardware-generatie selecteren](service-tiers-vcore.md#selecting-a-hardware-generation)voor gedetailleerde instructies voor het configureren van een nieuwe of bestaande Data Base voor het gebruik van een specifieke hardware-generatie.
   
## <a name="next-steps"></a>Volgende stappen

- [Azure-Attestation voor uw Azure SQL database-server configureren](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Zie ook

- [Zelf studie: aan de slag met Always Encrypted met beveiligde enclaves in Azure SQL Database](always-encrypted-enclaves-getting-started.md)