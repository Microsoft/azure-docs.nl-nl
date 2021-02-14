---
title: Overzicht van sjablonen
description: Meer informatie over het gebruik van een vooraf gedefinieerde sjabloon om snel aan de slag te gaan met Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 8c0e4db2bc686fff2bd718f45c63a0fc26f6cd55
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375388"
---
# <a name="templates"></a>Sjablonen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Sjablonen zijn vooraf gedefinieerde Azure Data Factory pijp lijnen waarmee u snel aan de slag kunt met Data Factory. Sjablonen zijn handig wanneer u geen ervaring hebt met Data Factory en snel aan de slag wilt. Deze sjablonen verminderen de ontwikkelings tijd voor het samen stellen van gegevens integratie projecten, waardoor de productiviteit van ontwikkel aars wordt verbeterd.

## <a name="create-data-factory-pipelines-from-templates"></a>Data Factory pijp lijnen maken op basis van sjablonen

U kunt op de volgende twee manieren aan de slag gaan met het maken van een Data Factory pijplijn op basis van een sjabloon:

1.  Selecteer **pijp lijn maken op basis van de sjabloon** op de pagina overzicht om de sjabloon galerie te openen.

    ![Open de sjabloon galerie op de pagina overzicht](media/solution-templates-introduction/templates-intro-image1.png)

1.  Selecteer op het tabblad auteur in resource Explorer de optie **+** en vervolgens **pijp lijn van sjabloon** om de sjabloon galerie te openen.

    ![Open de sjabloon galerie op het tabblad Auteur](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Sjabloon galerie

![De sjabloon galerie](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Data Factory sjablonen uit het vak

Data Factory gebruikt Azure Resource Manager sjablonen voor het opslaan van data factory pijplijn sjablonen. U kunt alle Resource Manager-sjablonen bekijken, samen met het manifest bestand dat wordt gebruikt voor out-of-Box Data Factory sjablonen, in de [officiële Azure Data Factory github opslag plaats](https://github.com/Azure/Azure-DataFactory/tree/master/templates). De vooraf gedefinieerde sjablonen die door micro soft worden geleverd, zijn niet beperkt tot de volgende items:

-   Sjablonen kopiëren:

    -   [Bulksgewijs kopiëren uit data base](solution-template-bulk-copy-with-control-table.md)
    
    -   [Nieuwe bestanden kopiëren op basis van LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Meerdere bestands containers kopiëren tussen archieven op basis van bestanden](solution-template-copy-files-multiple-containers.md)

    -   [Bestanden verplaatsen](solution-template-move-files.md)

    -   [Delta kopie van data base](solution-template-delta-copy-with-control-table.md)

    -   Kopiëren van \<source\> naar \<destination\>

        -   [Van Amazon S3 naar Azure Data Lake Store gen 2](solution-template-migration-s3-azure.md)

        -   Van Google Big query tot Azure Data Lake Store gen 2

        -   Van HDF tot Azure Data Lake Store gen 2

        -   Van Netezza tot Azure Data Lake Store gen 1

        -   Van SQL Server on-premises naar Azure SQL Database

        -   Van SQL Server on-premises naar Azure Synapse Analytics

        -   Van Oracle on premises naar Azure Synapse Analytics

-   SSIS-sjablonen

    -   Azure-SSIS Integration Runtime plannen voor het uitvoeren van SSIS-pakketten

-   Sjablonen transformeren

    -   [ETL met Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mijn sjablonen

U kunt een pijp lijn ook als sjabloon opslaan door **Opslaan als sjabloon** te selecteren op het tabblad pijp lijn.

![Een pijp lijn opslaan als een sjabloon](media/solution-templates-introduction/templates-intro-image4.png)

U kunt pijp lijnen weer geven die zijn opgeslagen als sjablonen in het gedeelte **Mijn sjablonen** van de sjabloon galerie. U kunt ze ook bekijken in de sectie **sjablonen** in de resource Explorer.

![Mijn sjablonen](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Als u de functie Mijn sjablonen wilt gebruiken, moet u GIT-integratie inschakelen. Zowel Azure DevOps GIT als GitHub worden ondersteund.
