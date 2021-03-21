---
title: Azure Data Factory prijzen door middel van voor beelden
description: In dit artikel wordt uitgelegd wat het Azure Data Factory-prijs model met gedetailleerde voor beelden bevat
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: b9f163a7632ca59d4f97aef21d8d62157610ba73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372804"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Data Factory-prijzen leren begrijpen met voorbeelden

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt uitgelegd wat het Azure Data Factory-prijs model met gedetailleerde voor beelden bevat.

> [!NOTE]
> De prijzen die in deze voor beelden worden gebruikt, zijn hypothetisch en zijn niet bedoeld als werkelijke prijzen.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Gegevens vanaf elk uur kopiëren van AWS S3 naar Azure Blob Storage

In dit scenario wilt u gegevens van AWS S3 kopiëren naar Azure Blob-opslag op basis van een schema.

Als u het scenario wilt volt ooien, moet u een pijp lijn maken met de volgende items:

1. Een Kopieer activiteit met een invoer-gegevensset voor de gegevens die moeten worden gekopieerd uit AWS S3.

2. Een uitvoer gegevensset voor de gegevens op Azure Storage.

3. Een schema trigger om elk uur de pijp lijn uit te voeren.

   ![Diagram toont een pijp lijn met een plannings trigger. Kopieer in de pijp lijn activiteit stromen naar een invoer-gegevensset, die stromen naar een gekoppelde service van W S S3 en de Kopieer activiteit ook stromen naar een uitvoer gegevensset, die naar een Azure Storage gekoppelde service doorloopt.](media/pricing-concepts/scenario1.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 2 entiteit voor lezen/schrijven  |
| Gegevens sets maken | 4 entiteiten voor lezen/schrijven (2 voor het maken van de gegevensset, 2 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 3 entiteiten voor lezen/schrijven (1 voor het maken van pijp lijnen, 2 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 1 entiteit lezen/schrijven |
| Pijp lijn uitvoeren | 2 uitvoeringen van activiteit (1 voor uitvoering van trigger, 1 voor uitvoeringen van activiteit) |
| Gegevens kopiëren veronderstelling: uitvoerings tijd = 10 minuten | 10 \* 4 Azure Integration runtime (standaard instelling voor DIU = 4) Zie [dit artikel](copy-activity-performance.md) voor meer informatie over de gegevens integratie-eenheden en het optimaliseren van Kopieer prestaties. |
| Bewaak pijp lijn controleren: er is slechts 1 uitvoering uitgevoerd | 2 bewakings records voor uitvoering opgehaald (1 voor de pijplijn uitvoering, 1 voor de uitvoering van de activiteit) |

**Totaal prijs scenario: $0,16811**

- Data Factory bewerkingen = **$0,0001**
  - Lezen/schrijven = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 2 \* 000005 = $0,00001 [1 bewaking = $0,25/50.000 = 0,000005]
- Uitvoering van pipeline-Orchestration &amp; = **$0,168**
  - Uitvoeringen van activiteit = 001 \* 2 = 0,002 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevens verplaatsing = $0,166 (in verhouding tot 10 minuten voor de uitvoerings tijd). $0,25/uur op Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Gegevens en trans formatie met Azure Databricks per uur kopiëren

In dit scenario wilt u gegevens van AWS S3 kopiëren naar Azure Blob-opslag en de gegevens transformeren met Azure Databricks volgens een planning per uur.

Als u het scenario wilt volt ooien, moet u een pijp lijn maken met de volgende items:

1. Een Kopieer activiteit met een invoer-gegevensset voor de gegevens die moeten worden gekopieerd uit AWS S3 en een uitvoer gegevensset voor de gegevens in azure Storage.
2. Een Azure Databricks activiteit voor de gegevens transformatie.
3. Eén schema-trigger voor het uitvoeren van de pijp lijn elk uur.

![Diagram toont een pijp lijn met een plannings trigger. Kopieer in de pijp lijn activiteit stromen naar een invoer-gegevensset, een uitvoer-gegevensset en een DataBricks-activiteit, die wordt uitgevoerd op Azure Databricks. De invoer gegevensset loopt over naar een gekoppelde service van W S-S3. De uitvoer gegevensset loopt door naar een Azure Storage gekoppelde service.](media/pricing-concepts/scenario2.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 3 entiteit voor lezen/schrijven  |
| Gegevens sets maken | 4 entiteiten voor lezen/schrijven (2 voor het maken van de gegevensset, 2 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 3 entiteiten voor lezen/schrijven (1 voor het maken van pijp lijnen, 2 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 1 entiteit lezen/schrijven |
| Pijp lijn uitvoeren | 3 uitvoeringen van activiteit (1 voor uitvoering van trigger, 2 voor uitvoeringen van activiteit) |
| Gegevens kopiëren veronderstelling: uitvoerings tijd = 10 minuten | 10 \* 4 Azure Integration runtime (standaard instelling voor DIU = 4) Zie [dit artikel](copy-activity-performance.md) voor meer informatie over de gegevens integratie-eenheden en het optimaliseren van Kopieer prestaties. |
| Bewaak pijp lijn controleren: er is slechts 1 uitvoering uitgevoerd | 3 controle records voor uitvoering opgehaald (1 voor pijplijn uitvoering, 2 voor uitvoering van activiteit) |
| Uitvoer van Databricks-activiteit uitvoeren: uitvoerings tijd = 10 minuten | 10 min. uitvoering van externe pijplijn activiteit |

**Totaal prijs scenario: $0,16916**

- Data Factory bewerkingen = **$0,00012**
  - Lezen/schrijven = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 3 \* 000005 = $0,00001 [1 bewaking = $0,25/50.000 = 0,000005]
- Uitvoering van pipeline-Orchestration &amp; = **$0,16904**
  - Uitvoeringen van activiteit = 001 \* 3 = 0,003 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevens verplaatsing = $0,166 (in verhouding tot 10 minuten voor de uitvoerings tijd). $0,25/uur op Azure Integration Runtime)
  - Externe pijplijn activiteit = $0,000041 (in verhouding tot 10 minuten van uitvoerings tijd. $0.00025/uur op Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Gegevens en trans formatie met dynamische para meters per uur kopiëren

In dit scenario wilt u gegevens van AWS S3 kopiëren naar Azure Blob-opslag en transformeren met Azure Databricks (met dynamische para meters in het script) volgens een planning per uur.

Als u het scenario wilt volt ooien, moet u een pijp lijn maken met de volgende items:

1. Een Kopieer activiteit met een invoer-gegevensset voor de gegevens die moeten worden gekopieerd uit AWS S3, een uitvoer gegevensset voor de gegevens in azure Storage.
2. Een opzoek activiteit voor het dynamisch door geven van para meters aan het transformatie script.
3. Een Azure Databricks activiteit voor de gegevens transformatie.
4. Eén schema-trigger voor het uitvoeren van de pijp lijn elk uur.

![Diagram toont een pijp lijn met een plannings trigger. Kopieer in de pijp lijn activiteit stromen naar een invoer-gegevensset, een uitvoer gegevensset en lookup-activiteit die stromen naar een DataBricks-activiteit, die wordt uitgevoerd op Azure Databricks. De invoer gegevensset loopt over naar een gekoppelde service van W S-S3. De uitvoer gegevensset loopt door naar een Azure Storage gekoppelde service.](media/pricing-concepts/scenario3.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 3 entiteit voor lezen/schrijven  |
| Gegevens sets maken | 4 entiteiten voor lezen/schrijven (2 voor het maken van de gegevensset, 2 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 3 entiteiten voor lezen/schrijven (1 voor het maken van pijp lijnen, 2 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 1 entiteit lezen/schrijven |
| Pijp lijn uitvoeren | 4 uitvoeringen van activiteit (1 voor uitvoering van trigger, 3 voor uitvoeringen van activiteit) |
| Gegevens kopiëren veronderstelling: uitvoerings tijd = 10 minuten | 10 \* 4 Azure Integration runtime (standaard instelling voor DIU = 4) Zie [dit artikel](copy-activity-performance.md) voor meer informatie over de gegevens integratie-eenheden en het optimaliseren van Kopieer prestaties. |
| Bewaak pijp lijn controleren: er is slechts 1 uitvoering uitgevoerd | 4 records voor het uitvoeren van controles die zijn opgehaald (1 voor de pijplijn uitvoering, 3 voor de uitvoering van de activiteit) |
| Opzoek activiteit uitvoeren hypo these: uitvoerings tijd = 1 min | 1 min uitvoering pijplijn activiteit |
| Uitvoer van Databricks-activiteit uitvoeren: uitvoerings tijd = 10 minuten | 10 min. uitvoering van externe pijplijn activiteit |

**Totaal prijs scenario: $0,17020**

- Data Factory bewerkingen = **$0,00013**
  - Lezen/schrijven = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 4 \* 000005 = $0,00002 [1 bewaking = $0,25/50.000 = 0,000005]
- Uitvoering van pipeline-Orchestration &amp; = **$0,17007**
  - Uitvoeringen van activiteit = 001 \* 4 = 0,004 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevens verplaatsing = $0,166 (in verhouding tot 10 minuten voor de uitvoerings tijd). $0,25/uur op Azure Integration Runtime)
  - Pijplijn activiteit = $0,00003 (voor 1 minuut van uitvoer tijd). $0.002/uur op Azure Integration Runtime)
  - Externe pijplijn activiteit = $0,000041 (in verhouding tot 10 minuten van uitvoerings tijd. $0.00025/uur op Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Fout opsporing voor het toewijzen van gegevens stromen gebruiken voor een normale werkdag

Als data Engineer is Sam verantwoordelijk voor het ontwerpen, bouwen en testen van gegevens stromen elke dag. Sam meldt zich in de ochtend aan op de ADF-gebruikers interface en schakelt de foutopsporingsmodus in voor gegevens stromen. De standaard-TTL voor debug-sessies is 60 minuten. Sam werkt gedurende 8 uur in de hele dag, waardoor de foutopsporingssessie nooit verloopt. Daarom zijn de kosten van Sam voor de dag:

**8 (uur) x 8 (door Compute geoptimaliseerde kernen) x $0,193 = $12,35**

Op hetzelfde moment kan Chris, een andere data Engineer, ook zich aanmelden bij de gebruikers interface van de ADF-browser voor gegevens profilering en ETL-ontwerp werk. Chris werkt niet altijd op de ADF-dag zoals Sam. Chris hoeft het fout opsporingsprogramma voor gegevens stromen gedurende één uur gedurende dezelfde periode en dezelfde dag als Sam hierboven te gebruiken. Dit zijn de kosten van Chris voor het gebruik van fout opsporing:

**1 (uur) x 8 (algemene kernen) x $0,274 = $2,19**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Gegevens in BLOB Store transformeren met toewijzings gegevens stromen

In dit scenario wilt u gegevens in BLOB Store visueel transformeren in ADF-toewijzings gegevens stromen op schema.

Als u het scenario wilt volt ooien, moet u een pijp lijn maken met de volgende items:

1. Een gegevens stroom activiteit met de transformatie logica.

2. Een invoer gegevensset voor de gegevens op Azure Storage.

3. Een uitvoer gegevensset voor de gegevens op Azure Storage.

4. Een schema trigger om elk uur de pijp lijn uit te voeren.

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 2 entiteit voor lezen/schrijven  |
| Gegevens sets maken | 4 entiteiten voor lezen/schrijven (2 voor het maken van de gegevensset, 2 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 3 entiteiten voor lezen/schrijven (1 voor het maken van pijp lijnen, 2 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 1 entiteit lezen/schrijven |
| Pijp lijn uitvoeren | 2 uitvoeringen van activiteit (1 voor uitvoering van trigger, 1 voor uitvoeringen van activiteit) |
| Veronderstellingen voor gegevens stroom: uitvoerings tijd = 10 min + 10 min TTL | 10 \* 16 kernen van algemene Compute met TTL van 10 |
| Bewaak pijp lijn controleren: er is slechts 1 uitvoering uitgevoerd | 2 bewakings records voor uitvoering opgehaald (1 voor de pijplijn uitvoering, 1 voor de uitvoering van de activiteit) |

**Totaal prijs scenario: $1,4631**

- Data Factory bewerkingen = **$0,0001**
  - Lezen/schrijven = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 2 \* 000005 = $0,00001 [1 bewaking = $0,25/50.000 = 0,000005]
- Uitvoering van pipeline-Orchestration &amp; = **$1,463**
  - Uitvoeringen van activiteit = 001 \* 2 = 0,002 [1 run = $1/1000 = 0,001]
  - Data flow-activiteiten = $1,461 naar verhouding van 20 minuten (10 minuten uitvoerings tijd + 10 minuten TTL). $0.274/uur op Azure Integration Runtime met een algemene reken kracht van 16 kernen

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Gegevens integratie in Azure Data Factory beheerde VNET
In dit scenario wilt u de oorspronkelijke bestanden op Azure Blob Storage verwijderen en gegevens van Azure SQL Database naar Azure Blob Storage kopiëren. U kunt dit uitvoeren twee keer uitvoeren op verschillende pijp lijnen. De uitvoerings tijd van deze twee pijp lijnen is overlappend.
![Scenario4 ](media/pricing-concepts/scenario-4.png) u het scenario wilt volt ooien, moet u twee pijp lijnen maken met de volgende items:
  - Een pijplijn activiteit – activiteit verwijderen.
  - Een Kopieer activiteit met een invoer-gegevensset voor de gegevens die moeten worden gekopieerd uit Azure Blob-opslag.
  - Een uitvoer gegevensset voor de gegevens op Azure SQL Database.
  - Een schema triggers voor het uitvoeren van de pijp lijn.


| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 4 entiteit voor lezen/schrijven |
| Gegevens sets maken | 8 entiteiten voor lezen/schrijven (4 voor het maken van de gegevensset, 4 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 6 entiteiten voor lezen/schrijven (2 voor het maken van pijp lijnen, 4 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 2 entiteit voor lezen/schrijven |
| Pijp lijn uitvoeren | 6 uitvoeringen van activiteit (2 voor uitvoering van trigger, 4 voor uitvoeringen van activiteit) |
| Delete-activiteit uitvoeren: elke uitvoerings tijd = 5 minuten. De uitvoering van de activiteit verwijderen in de eerste pijp lijn is van 10:00 uur UTC tot 10:05 uur UTC. De uitvoering van de activiteit verwijderen in een tweede pijp lijn is van 10:02 uur UTC tot 10:07 uur UTC.|Totale aantal uitgevoerde pijplijn activiteit in beheerde VNET. Pijplijn activiteit biedt ondersteuning voor Maxi maal 50 gelijktijdigheid in beheerde VNET. |
| Gegevens kopiëren hypo these: elke uitvoerings tijd = 10 minuten. De Kopieer uitvoering in de eerste pijp lijn is van 10:06 uur UTC tot 10:15 uur UTC. De uitvoering van de activiteit verwijderen in een tweede pijp lijn is van 10:08 uur UTC tot 10:17 uur UTC. | 10 * 4 Azure Integration Runtime (standaard instelling DIU = 4) Zie [dit artikel](copy-activity-performance.md) voor meer informatie over de gegevens integratie-eenheden en het optimaliseren van Kopieer prestaties. |
| Bewaak pijp lijn controleren: er zijn slechts twee uitvoeringen | 6 bewakings records voor uitvoering opgehaald (2 voor de pijplijn uitvoering, 4 voor het uitvoeren van de activiteit) |


**Totaal prijs scenario: $0,45523**

- Data Factory bewerkingen = $0,00023
  - Lezen/schrijven = 20 * 00001 = $0,0002 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 6 * 000005 = $0,00003 [1 bewaking = $0,25/50.000 = 0,000005]
- Pipeline-indeling & Execution = $0,455
  - Uitvoeringen van activiteit = 0,001 * 6 = 0,006 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevens verplaatsing = $0,333 (in verhouding tot 10 minuten voor de uitvoerings tijd). $0,25/uur op Azure Integration Runtime)
  - Pijplijn activiteit = $0,116 (in verhouding tot 7 minuten van uitvoerings tijd. $1/uur op Azure Integration Runtime)

> [!NOTE]
> Deze prijzen zijn alleen bedoeld als voor beeld.

**Veelgestelde vragen**

V: als ik meer dan 50 pijplijn activiteiten wil uitvoeren, kunnen deze activiteiten tegelijk worden uitgevoerd?

A: Maxi maal 50 gelijktijdige pijplijn activiteiten worden toegestaan.  De activiteit van de 51th-pijp lijn wordt in de wachtrij geplaatst totdat een vrije sleuf wordt geopend. Hetzelfde voor externe activiteiten. Maxi maal 800 gelijktijdige externe activiteiten worden toegestaan.

## <a name="next-steps"></a>Volgende stappen

Nu u de prijzen voor Azure Data Factory begrijpt, kunt u aan de slag.

- [Een data factory maken met behulp van de Azure Data Factory gebruikers interface](quickstart-create-data-factory-portal.md)

- [Inleiding tot Azure Data Factory](introduction.md)

- [Visueel ontwerpen in Azure Data Factory](author-visually.md)
