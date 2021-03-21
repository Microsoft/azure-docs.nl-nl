---
title: Machine Learning Services in Azure SQL Managed instance
description: Dit artikel bevat een overzicht of Machine Learning Services in Azure SQL Managed instance.
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
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599558"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Machine Learning Services in Azure SQL Managed instance

Machine Learning Services is een functie van Azure SQL Managed instance die in-data base-machine learning voorziet en zowel python-als R-scripts ondersteunt. De functie bevat micro soft python en R-pakketten voor predictive analytics met hoge prestaties en machine learning. De relationele gegevens kunnen worden gebruikt in scripts via opgeslagen procedures, een T-SQL-script met python-of R-instructies of python-of R-code met T-SQL.

## <a name="what-is-machine-learning-services"></a>Wat is Machine Learning Services?

Met Machine Learning Services in Azure SQL Managed Instance kunt u python-en R-scripts uitvoeren in de-data base. U kunt dit gebruiken om gegevens voor te bereiden en op te schonen, functie techniek uit te voeren en machine learning modellen in een Data Base te trainen, te evalueren en te implementeren. De functie voert uw scripts uit waarin de gegevens zich bevinden en elimineert de overdracht van de gegevens over het netwerk naar een andere server.

Gebruik Machine Learning Services met R/python-ondersteuning in Azure SQL Managed instance voor het volgende:

- **Voer r-en python-scripts uit om gegevens voorbereiding en gegevens verwerking voor algemeen gebruik** uit te voeren. u kunt nu uw R/python-scripts overbrengen naar Azure SQL Managed instance waar uw gegevens zich bevinden, in plaats van gegevens naar een andere server te verplaatsen om R-en python-scripts uit te voeren. U kunt voor komen dat gegevens verplaatsing en gerelateerde problemen met betrekking tot latentie, beveiliging en naleving overbodig zijn.

- **Machine learning modellen trainen in data base** : u kunt modellen trainen met behulp van open-source-algoritmen. U kunt uw training eenvoudig schalen naar de hele gegevensset, in plaats van te vertrouwen op voorbeeld gegevens sets die uit de Data Base worden gehaald.

- **Implementeer uw modellen en scripts in productie in opgeslagen procedures** : de scripts en getrainde modellen kunnen eenvoudigweg worden operationeel door ze in de opgeslagen T-SQL-procedures in te sluiten. Apps die verbinding maken met een beheerd exemplaar van Azure SQL kunnen profiteren van voor spellingen en intelligentie in deze modellen door alleen een opgeslagen procedure aan te roepen. U kunt ook de native T-SQL-functie voors PELLEn gebruiken om modellen te operationeel maken, zodat u snel kunt scoren in zeer gelijktijdige Score scenario's voor realtime.

Basis distributies van python en R zijn opgenomen in Machine Learning Services. U kunt open source-pakketten en-frameworks installeren en gebruiken, zoals PyTorch, tensor flow en scikit-learn, naast de micro soft-pakketten [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) en [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) voor python, en [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [Microsoftml](/sql/machine-learning/r/ref-r-microsoftml), [olapr](/sql/machine-learning/r/ref-r-olapr)en [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) voor R.

## <a name="how-to-enable-machine-learning-services"></a>Machine Learning Services inschakelen

U kunt Machine Learning Services in Azure SQL Managed instance inschakelen door uitbreid baarheid in te scha kelen met de volgende SQL-opdrachten (SQL Managed instance wordt opnieuw gestart en gedurende een paar seconden niet beschikbaar):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Zie [resource governance](machine-learning-services-differences.md#resource-governance)voor meer informatie over de werking van deze opdracht voor SQL Managed instance-resources.

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Machine Learning Services in een failovergroep inschakelen

In een [failovergroep](failover-group-add-instance-tutorial.md)worden systeem databases niet gerepliceerd naar het secundaire exemplaar (Zie de [beperkingen van failover-groepen](../database/auto-failover-group-overview.md#limitations-of-failover-groups) voor meer informatie).

Als het beheerde exemplaar dat u gebruikt deel uitmaakt van een failovergroep, gaat u als volgt te werk:

- Voer de `sp_configure` opdrachten en uit `RECONFIGURE` voor elk exemplaar van de failovergroep om Machine Learning Services in te scha kelen.

- Installeer de R/python-bibliotheken op een gebruikers database in plaats van de hoofd database.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de belangrijkste verschillen ten opzichte [van SQL Server machine learning Services](machine-learning-services-differences.md).
- Zie [python-scripts uitvoeren](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)voor meer informatie over het gebruik van python in Machine Learning Services.
- Zie [r-scripts uitvoeren](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)voor meer informatie over het gebruik van r in Machine Learning Services.
- Zie de [documentatie voor sql machine learning](/sql/machine-learning/index)voor meer informatie over machine learning op andere SQL-platforms.
