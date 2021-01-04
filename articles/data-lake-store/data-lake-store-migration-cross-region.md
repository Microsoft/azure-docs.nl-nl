---
title: Migratie van meerdere regio's Azure Data Lake Storage Gen1 | Microsoft Docs
description: Meer informatie over hoe u rekening moet houden bij het plannen en volt ooien van een migratie naar Azure Data Lake Storage Gen1 wanneer deze beschikbaar wordt in nieuwe regio's.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: twooley
ms.openlocfilehash: c6520036f3ddb8799025129391330268b0604886
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723803"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Azure Data Lake Storage Gen1 migreren tussen regio's

Als Azure Data Lake Storage Gen1 beschikbaar komt in nieuwe regio's, kunt u ervoor kiezen een eenmalige migratie uit te voeren om te profiteren van de nieuwe regio. Meer informatie over hoe u rekening moet houden bij het plannen en volt ooien van de migratie.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [vandaag nog uw gratis Azure-account maken](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie.
* **Een Data Lake Storage gen1-account in twee verschillende regio's**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md)voor meer informatie.
* **Azure Data Factory**. Zie voor meer informatie [Inleiding tot Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Overwegingen bij migratie

Bepaal eerst welke migratie strategie het meest geschikt is voor uw toepassing die gegevens in Data Lake Storage Gen1 schrijft, leest of verwerkt. Wanneer u een strategie kiest, moet u rekening houden met de beschikbaarheids vereisten van uw toepassing en de downtime die tijdens een migratie plaatsvindt. U kunt bijvoorbeeld het migratie model ' lift-and-Shift ' gebruiken. In deze benadering pauzeert u de toepassing in uw bestaande regio terwijl al uw gegevens naar de nieuwe regio worden gekopieerd. Wanneer het kopieer proces is voltooid, hervat u uw toepassing in de nieuwe regio en verwijdert u de oude Data Lake Storage Gen1-account. Uitval tijd tijdens de migratie is vereist.

Om uitval tijd te reduceren, kunt u onmiddellijk beginnen met het opnemen van nieuwe gegevens in de nieuwe regio. Wanneer u de mini maal vereiste gegevens hebt, voert u uw toepassing uit in de nieuwe regio. Ga op de achtergrond naar het kopiëren van oudere gegevens van het bestaande Data Lake Storage Gen1-account naar het nieuwe Data Lake Storage Gen1-account in de nieuwe regio. Met deze methode kunt u de overstap naar de nieuwe regio maken met weinig downtime. Wanneer alle oudere gegevens zijn gekopieerd, verwijdert u het oude Data Lake Storage Gen1-account.

Andere belang rijke informatie die u moet overwegen bij het plannen van de migratie zijn:

* **Gegevens volume**. Het gegevens volume (in gigabytes, het aantal bestanden en mappen, enzovoort) is van invloed op de tijd en resources die u nodig hebt voor de migratie.

* **Data Lake Storage gen1 account naam**. De nieuwe account naam in de nieuwe regio moet globaal uniek zijn. De naam van uw oude Data Lake Storage Gen1-account in VS-Oost 2 kan bijvoorbeeld contosoeastus2.azuredatalakestore.net zijn. U kunt uw nieuwe Data Lake Storage Gen1-account een naam in de Noord-contosonortheu.azuredatalakestore.net van de EU.

* **Hulpprogram ma's**. U kunt het beste de [Azure Data Factory Kopieer activiteit](../data-factory/connector-azure-data-lake-store.md) gebruiken om data Lake Storage gen1 bestanden te kopiëren. Data Factory ondersteunt het verplaatsen van gegevens met hoge prestaties en betrouw baarheid. Denk eraan dat Data Factory alleen de mappen hiërarchie en de inhoud van de bestanden kopieert. U moet hand matig toegangs beheer lijsten (Acl's) Toep assen die u in het oude account gebruikt voor het nieuwe account. Zie de [hand leiding Copy activity Performance and Tuning (Engelstalig](../data-factory/copy-activity-performance.md)) voor meer informatie, waaronder prestatie doelen voor beste scenario's. Als u wilt dat gegevens sneller worden gekopieerd, moet u mogelijk extra gegevens verplaatsings eenheden voor de Cloud gebruiken. Andere hulpprogram ma's, zoals AdlCopy, bieden geen ondersteuning voor het kopiëren van gegevens tussen regio's.  

* **Bandbreedte kosten**. De [bandbreedte kosten](https://azure.microsoft.com/pricing/details/bandwidth/) zijn van toepassing omdat de gegevens worden overgedragen uit een Azure-regio.

* **Acl's voor uw gegevens**. Beveilig uw gegevens in de nieuwe regio door Acl's toe te passen op bestanden en mappen. Zie gegevens beveiligen die zijn [opgeslagen in azure data Lake Storage gen1](data-lake-store-secure-data.md)voor meer informatie. U kunt de migratie het beste gebruiken om uw Acl's bij te werken en aan te passen. U kunt instellingen gebruiken die vergelijkbaar zijn met de huidige instellingen. U kunt de Acl's weer geven die op elk bestand worden toegepast met behulp van de Azure Portal, [Power shell-cmdlets](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)of sdk's.  

* **Locatie van Analytics Services**. Voor de beste prestaties moeten uw analyse Services, zoals Azure Data Lake Analytics of Azure HDInsight, zich in dezelfde regio bevinden als uw gegevens.  

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
