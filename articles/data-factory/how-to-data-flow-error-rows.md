---
title: Fout rijen verwerken met toewijzing van gegevens stromen in Azure Data Factory
description: Meer informatie over het afbreken van SQL-afbreking in Azure Data Factory met het toewijzen van gegevens stromen.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: a7a03ff1a58f50f16ebefce48b9e2772a16a011a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386336"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Afgebroken SQL-rijen in Data Factory gegevens stromen toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een veelvoorkomend scenario in Data Factory bij het gebruik van toewijzings gegevens stromen is het schrijven van uw getransformeerde gegevens naar een data base in Azure SQL Database. In dit scenario wordt een veelvoorkomende fout voorwaarde die u moet voor komen tegen mogelijke kolom afkap ping.

Er zijn twee primaire methoden voor het afhandelen van fouten bij het schrijven van gegevens naar uw data base-sink in ADF-gegevens stromen:

* Stel de verwerking van de Sink- [fout rijen](./connector-azure-sql-database.md#error-row-handling) in op ' door gaan bij fout ' bij het verwerken van database gegevens. Dit is een geautomatiseerde catch-all-methode waarvoor geen aangepaste logica in uw gegevens stroom nodig is.
* U kunt ook de volgende stappen uitvoeren om de logboek registratie van kolommen die niet in een doel reeks kolom passen, toe te voegen, zodat uw gegevens stroom kan door gaan.

> [!NOTE]
> Bij het inschakelen van de verwerking van automatische fout rijen, in tegens telling tot de onderstaande methode voor het schrijven van uw eigen fout bij het afhandelen van fouten, is er een kleine prestatie vermindering die wordt gemaakt door en extra stap die wordt uitgevoerd door ADF om een bewerking met twee fasen uit te voeren om fouten te ondervangen.

## <a name="scenario"></a>Scenario

1. Er is een doel database tabel die een kolom bevat met de ```nvarchar(5)``` naam ' name '.

2. In onze gegevens stroom willen we film titels van onze Sink toewijzen aan de kolom naam.

    ![Film gegevens stroom 1](media/data-flow/error4.png)
    
3. Het probleem is dat de titel van de film niet helemaal past binnen een Sink-kolom die Maxi maal vijf tekens kan bevatten. Wanneer u deze gegevens stroom uitvoert, treedt er een fout op, zoals deze: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

In deze video wordt een voor beeld gegeven van het instellen van een logische rij voor het verwerken van rijen in uw gegevens stroom:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Het ontwerp rond deze voor waarde

1. In dit scenario is de maximum lengte van de kolom ' name ' vijf tekens. Daarom voegen we een Conditional Split-trans formatie toe waarmee we rijen kunnen registreren met ' titels ' die langer zijn dan vijf tekens, terwijl ook de rest van de rijen die in die ruimte passen, kunnen worden opgeslagen in de data base.

    ![Conditional Split](media/data-flow/error1.png)

2. Met deze Conditional Split trans formatie wordt de maximum lengte van ' title ' gedefinieerd als vijf. Een rij die kleiner dan of gelijk aan vijf is, gaat in de ```GoodRows``` stroom. Rijen die groter zijn dan vijf, worden in de ```BadRows``` Stream verzonden.

3. De rijen die zijn mislukt, moeten nu worden geregistreerd. Voeg een Sink-trans formatie toe aan de ```BadRows``` stroom voor logboek registratie. Hier worden alle velden automatisch toegewezen, zodat de registratie van de volledige transactie record wordt geregistreerd. Dit is een tekst scheidings teken van CSV-bestand naar één bestand in Blob Storage. Het logboek bestand badrows.csv wordt aangeroepen.

    ![Ongeldige rijen](media/data-flow/error3.png)
    
4. De voltooide gegevens stroom wordt hieronder weer gegeven. We kunnen nu fout rijen opsplitsen om te voor komen dat er fouten in de SQL-afkap ping ontstaan en deze vermeldingen in een logboek bestand plaatsen. Ondertussen kunnen voltooide rijen naar de doel database blijven schrijven.

    ![gegevens stroom volt ooien](media/data-flow/error2.png)

5. Als u de optie voor het verwerken van de fout rijen in de Sink-trans formatie kiest en ' uitvoer fout rijen ' hebt ingesteld, genereert de ADF automatisch een CSV-bestands uitvoer van uw rijgegevens en de door het stuur programma gemelde fout berichten. U hoeft die logica niet hand matig toe te voegen aan uw gegevens stroom met deze alternatieve optie. Er wordt een kleine prestatie vermindering in rekening gebracht met deze optie, zodat ADF een twee fasen-methodologie kan implementeren om fouten te onderscheppen en deze te registreren.

    ![gegevens stroom volt ooien met fout rijen](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw gegevensstroom logica met behulp van trans [formaties](concepts-data-flow-overview.md)voor gegevens stromen.