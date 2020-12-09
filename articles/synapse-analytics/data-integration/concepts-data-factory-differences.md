---
title: Verschillen met Azure Data Factory
description: Meer informatie over hoe de mogelijkheden voor gegevens integratie van Azure Synapse Analytics verschillen van die van Azure Data Factory
services: synapse-analytics
author: shirleywangmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: shwang
ms.reviewer: jrasnick
ms.openlocfilehash: db5d05e1a211ce14926ee4031054669fff5110d9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930208"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Gegevens integratie in azure Synapse Analytics versus Azure Data Factory

In azure Synapse Analytics zijn de mogelijkheden voor gegevens integratie, zoals Synapse-pijp lijnen en gegevens stromen, gebaseerd op die van Azure Data Factory. Zie [Wat is Azure Data Factory](../../data-factory/introduction.md)voor meer informatie. Bijna alle mogelijkheden zijn identiek of vergelijkbaar en de documentatie wordt gedeeld tussen de twee services. In dit artikel worden de huidige verschillen tussen Azure Data Factory en Azure Synapse gemarkeerd en geïdentificeerd.

Als u wilt zien of een Azure Data Factory functie of artikel van toepassing is op Azure Synapse, controleert u de moniker boven aan het artikel.

![Is van toepassing op moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Is van toepassing op moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Functies in Azure Data Factory niet gepland voor Azure Synapse

De volgende functies zijn beschikbaar in Azure Data Factory, maar zijn niet gepland voor Azure Synapse.

* **Life-en Shift-SSIS-pakketten:** In Azure Data Factory hebt u de mogelijkheid om SSIS-pakketten uit te scha kelen met behulp van de SSIS Integration runtime. De SSIS Integration runtime en de activiteit uitvoeren SSIS-pakket zijn niet beschikbaar in Synapse-werk ruimten. 
* **Time to Live:** Time to Live is een instelling in de Azure Integration runtime waarmee het Spark-cluster in toewijzing van gegevens stromen gedurende een bepaalde tijd na voltooiing van een gegevens stroom *warme* blijft. Deze functie is niet beschikbaar in Synapse-werk ruimten.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Synapse-functies die niet worden ondersteund in Azure Data Factory

De volgende functies zijn beschikbaar in azure Synapse, maar zijn niet gepland voor Azure Data Factory.

* **Spark-taak bewaking voor het toewijzen van gegevens stromen:** In Synapse is de Spark-engine opgenomen in het abonnement van de gebruiker, zodat gebruikers gedetailleerde Spark-logboeken kunnen bekijken. In Azure Data Factory vindt taak uitvoering plaats op een door Azure Data Factory beheerd Spark-cluster en is deze informatie niet beschikbaar. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory functies die zich anders gedragen in Synapse

De volgende functies gedragen zich op een andere manier of bestaan momenteel niet in azure Synapse. 

* **Wrangling-gegevens stromen:** De activiteit wrangling data flow is op dit moment alleen beschikbaar in Azure Data Factory.
* **De oplossings sjabloon galerie:** In Azure Data Factory kunnen gebruikers pijplijn sjablonen vinden in de galerie met oplossingen sjablonen. In Synapse-werk ruimten bevat het kennis centrum een andere set sjablonen, samen met aanvullende gegevens sets en SQL-scripts. 
* **Integratie met Azure monitor:** Synapse-werk ruimten worden niet geïntegreerd met Azure Monitor zoals Azure Data Factory.
* **Runtime configuratie voor hybride integratie:** Binnen een Synapse-werk ruimte kan een gebruiker geen beheerde VNet-IR en een Azure IR hebben. Deze functie wordt ondersteund in Azure Data Factory.
* **Delen van integratie-runtime:** Zelf-hostende Integration Runtimes kunnen niet worden gedeeld tussen Synapse-werk ruimten. Deze functie wordt ondersteund in Azure Data Factory.
* **Integratie-Runtimes voor meerdere regio's voor gegevens stromen:** Gegevens stromen kunnen niet worden uitgevoerd op Integration Runtimes in verschillende regio's dan een Synapse-werk ruimte. Deze functie wordt ondersteund in Azure Data Factory.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met gegevens integratie in uw Synapse-werk ruimte door te leren hoe u gegevens opneemt [in een Azure data Lake Storage Gen2-account](data-integration-data-lake.md).
