---
title: Belangrijkste verschillen voor Machine Learning Services (preview-versie)
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
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324530"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>De belangrijkste verschillen tussen Machine Learning Services in Azure SQL Managed Instance en SQL Server

De functionaliteit van [Machine Learning Services in Azure SQL Managed instance (preview)](machine-learning-services-overview.md) is bijna identiek aan [SQL Server machine learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning). Hier volgen enkele belang rijke verschillen.

> [!IMPORTANT]
> Machine Learning Services in Azure SQL Managed instance is momenteel beschikbaar als open bare preview. Zie [registreren voor de preview](machine-learning-services-overview.md#signup)om u aan te melden.

## <a name="preview-limitations"></a>Preview-beperkingen

Tijdens de preview gelden de volgende beperkingen voor de service:

- Loop back-verbindingen werken niet (Zie [loop back-verbinding met SQL Server vanuit een python-of R-script](/sql/machine-learning/connect/loopback-connection)).
- Externe resourcegroepen worden niet ondersteund.
- Alleen Python en R worden ondersteund. Externe talen, zoals Java, kunnen niet worden toegevoegd.
- Scenario's die gebruikmaken van de [Message Passing Interface](/message-passing-interface/microsoft-mpi) (MPI) worden niet ondersteund.

In het geval van een serviceniveau doelstelling (SLO) update, werkt u de SLO bij en verhoogt u een ondersteunings ticket om de toegewezen resource limieten voor R/python opnieuw in te scha kelen.

## <a name="language-support"></a>Taalondersteuning

Machine Learning Services in SQL Managed instance en SQL Server ondersteunen zowel python als R [Extensibility Framework](/sql/advanced-analytics/concepts/extensibility-framework). De belangrijkste verschillen zijn:

- De eerste versies van python en R verschillen van Machine Learning Services in SQL Managed instance en SQL Server:

  | Systeem               | Python | R     |
  |----------------------|--------|-------|
  | SQL Managed Instance | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- U hoeft niet in te stellen `external scripts enabled` via `sp_configure` . Zodra u zich hebt [geregistreerd](machine-learning-services-overview.md#signup) voor de preview-versie, wordt machine learning ingeschakeld voor Azure SQL Managed instance.

## <a name="packages"></a>Pakketten

Python en R-pakket beheer werken anders tussen SQL Managed instance en SQL Server. Deze verschillen zijn:

- Er wordt geen ondersteuning geboden voor pakketten die afhankelijk zijn van externe Runtimes (zoals Java) of waarvoor u toegang nodig hebt tot OS Api's voor installatie of gebruik.
- Pakketten kunnen uitgaande netwerk aanroepen uitvoeren (eerdere wijzigingen in de preview-versie). U kunt de juiste uitgaande beveiligings regels instellen op het niveau van de [netwerk beveiligings groep](../../virtual-network/network-security-groups-overview.md) om uitgaande netwerk aanroepen in te scha kelen.

Zie voor meer informatie over het beheren van python-en R-pakketten:

- [Python-pakketgegevens ophalen](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [R-pakketgegevens ophalen](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Resourcebeheer

Het is niet mogelijk om R-resources te beperken via [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) en externe resource groepen.

Tijdens de openbare preview worden R-resources ingesteld op maximaal 20% van de SQL Managed Instance-resources en zijn ze afhankelijk van welke servicelaag u kiest. Zie [Azure SQL database-aankoop modellen](../database/purchasing-models.md)voor meer informatie.

### <a name="insufficient-memory-error"></a>Onvoldoende geheugen fout

Als er onvoldoende geheugen beschikbaar is voor R, wordt er een foutbericht weergegeven. Veelvoorkomende fout berichten zijn:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Het geheugengebruik is afhankelijk van de hoeveelheid die wordt gebruikt in uw R-scripts en het aantal parallelle query's dat wordt uitgevoerd. Als u de bovenstaande foutberichten ontvangt, kunt u uw database schalen naar een hogere servicelaag om dit probleem op te lossen.

## <a name="next-steps"></a>Volgende stappen

- Zie het overzicht [Machine Learning Services in Azure SQL Managed instance](machine-learning-services-overview.md).
- Zie [python-scripts uitvoeren](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)voor meer informatie over het gebruik van python in Machine Learning Services.
- Zie [r-scripts uitvoeren](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)voor meer informatie over het gebruik van r in Machine Learning Services.