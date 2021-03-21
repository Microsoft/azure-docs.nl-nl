---
title: Gerelateerde gegevensassets weer geven in Azure Data Catalog
description: In dit artikel wordt uitgelegd hoe u gerelateerde gegevensassets van een geselecteerde gegevensasset in Azure Data Catalog kunt weer geven.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: c1c5ddcacfc529f8fc4ab9a70cea540c44da9fa6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674781"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Gerelateerde gegevensassets in Azure Data Catalog weer geven?

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Met Azure Data Catalog kunt u gegevensassets weer geven die zijn gerelateerd aan een geselecteerd gegevens activum en relaties tussen deze activa weer geven. 

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen 
Wanneer u gegevensassets registreert bij de volgende gegevens bronnen, registreert Azure Data Catalog automatisch meta gegevens over het koppelen van relaties tussen de geselecteerde gegevensassets. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Als Data Catalog relatie tussen twee gegevensassets wilt importeren, moet u beide activa tegelijk registreren. Als u een van deze afzonderlijk hebt toegevoegd, voegt u deze opnieuw toe en de andere gegevensasset om de relatie tussen de gegevens te importeren.

## <a name="view-related-data-assets"></a>Gerelateerde gegevensassets weergeven
Als u gegevensassets wilt weer geven die zijn gerelateerd aan een geselecteerde gegevensset, gebruikt u het tabblad **relaties** zoals wordt weer gegeven in de volgende afbeelding: 

![Gerelateerde gegevensassets Azure Data Catalog weer geven](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

In dit voor beeld zijn er twee relaties voor de geselecteerde **tabel productsubcategory** -gegevens Asset: 

- De kolom ProductSubcategoryID van de product tabel heeft een refererende-sleutel relatie met de kolom ProductSubcategoryID van de geselecteerde tabel productsubcategory-tabel. 
- De kolom ProductCategoryID van de tabel tabel productsubcategory heeft een refererende-sleutel relatie met de kolom ProductCategoryID van de geselecteerde ProductCategory-tabel.

> [!NOTE]
> Let op de richting van de pijl in de structuur weergave relaties.  

Als u meer informatie wilt weer geven, bijvoorbeeld de volledig gekwalificeerde naam van de kolom, plaatst u de muis aanwijzer op en ziet u een pop-upvenster dat lijkt op de volgende afbeelding: 

![Pop-up Azure Data Catalog-relatie](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Als u relaties wilt opnemen tussen activa die al zijn geregistreerd, registreert u deze opnieuw.

## <a name="next-steps"></a>Volgende stappen
- [Gegevensassets beheren](data-catalog-how-to-manage.md)
