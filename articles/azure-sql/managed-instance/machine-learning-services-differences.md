---
title: Belangrijkste verschillen voor Machine Learning Services
description: In dit artikel worden de belangrijkste verschillen tussen Machine Learning Services in Azure SQL Managed instance en SQL Server Machine Learning Services beschreven.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599541"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>De belangrijkste verschillen tussen Machine Learning Services in Azure SQL Managed Instance en SQL Server

In dit artikel worden enkele belang rijke verschillen in functionaliteit tussen [Machine Learning Services in Azure SQL Managed instance](machine-learning-services-overview.md) en [SQL Server machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)beschreven.

## <a name="language-support"></a>Taalondersteuning

Machine Learning Services in zowel SQL Managed instance als SQL Server ondersteunen het python-en R- [uitbreidings raamwerk](/sql/machine-learning/concepts/extensibility-framework). De belangrijkste verschillen in het SQL Managed instance zijn:

- Alleen Python en R worden ondersteund. Externe talen, zoals Java, kunnen niet worden toegevoegd.

- De eerste versies van python en R verschillen:

  | Platform                   | Versie van python-runtime           | R runtime-versies                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Azure SQL Managed Instance | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 en 3.7.2 (CU22 en hoger) | 3.3.3 en 3.5.2 (CU22 en hoger)     |
  | SQL Server 2016            | Niet beschikbaar                    | 3.2.2 en 3.5.2 (SP2 CU14 en hoger) |

## <a name="python-and-r-packages"></a>Python-en R-pakketten

Er wordt geen ondersteuning geboden in het SQL Managed instance voor pakketten die afhankelijk zijn van externe Runtimes (zoals Java) of toegang moeten hebben tot OS Api's voor installatie of gebruik.

Zie voor meer informatie over het beheren van python-en R-pakketten:

- [Python-pakketgegevens ophalen](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [R-pakketgegevens ophalen](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Resourcebeheer

In SQL Managed instance is het niet mogelijk om R-resources via [Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true)te beperken en kunnen externe resource groepen niet worden ondersteund.

R-resources worden standaard ingesteld op Maxi maal 20% van de beschik bare SQL Managed instance-resources wanneer uitbreid baarheid is ingeschakeld. Als u dit standaard percentage wilt wijzigen, maakt u een ondersteunings ticket voor Azure op [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

Uitbreid baarheid is ingeschakeld met de volgende SQL-opdrachten (SQL Managed instance wordt opnieuw gestart en is enkele seconden niet beschikbaar):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Gebruik de volgende opdrachten om uitbrei ding uit te scha kelen en 100% van geheugen en CPU-resources te herstellen naar SQL Server:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

De totale hoeveelheid beschik bare resources voor het beheerde exemplaar van SQL is afhankelijk van welke servicelaag u kiest. Zie [Azure SQL database-aankoop modellen](/azure/sql-database/sql-database-service-tiers)voor meer informatie.

### <a name="insufficient-memory-error"></a>Onvoldoende geheugen fout

Het geheugengebruik is afhankelijk van de hoeveelheid die wordt gebruikt in uw R-scripts en het aantal parallelle query's dat wordt uitgevoerd. Als er onvoldoende geheugen beschikbaar is voor R, wordt een fout bericht weer gegeven. Veelvoorkomende fout berichten zijn:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Als een van deze fouten wordt weer gegeven, kunt u deze oplossen door uw data base te schalen naar een hogere servicelaag.

## <a name="sql-managed-instance-pools"></a>Door SQL beheerde exemplaar groepen

Machine Learning Services wordt momenteel niet ondersteund in [Azure SQL Managed instance Pools (preview)](instance-pools-overview.md).

## <a name="next-steps"></a>Volgende stappen

- Zie het overzicht [Machine Learning Services in Azure SQL Managed instance](machine-learning-services-overview.md).
- Zie [python-scripts uitvoeren](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)voor meer informatie over het gebruik van python in Machine Learning Services.
- Zie [r-scripts uitvoeren](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)voor meer informatie over het gebruik van r in Machine Learning Services.
