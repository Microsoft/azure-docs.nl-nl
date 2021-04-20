---
title: Connector- en indelingsproblemen in toewijzingsgegevensstromen oplossen
description: Meer informatie over het oplossen van problemen met gegevensstromen met betrekking tot connectors en indelingen in Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/20/2021
ms.openlocfilehash: 1991436e2cc890b5c6a339f79e42536ced2fbd36
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739466"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Connector- en indelingsproblemen oplossen in toewijzingsgegevensstromen in Azure Data Factory


In dit artikel worden methoden voor probleemoplossing met betrekking tot connectors en indelingen voor het toewijzen van gegevensstromen in Azure Data Factory (ADF) beschreven.


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Ondersteuning voor aangepaste schema's in de bron

#### <a name="symptoms"></a>Symptomen
Wanneer u de ADF-gegevensstroom wilt gebruiken om gegevens van Cosmos DB/JSON naar andere gegevensopslag te verplaatsen of over te dragen, worden sommige kolommen van de brongegevens mogelijk overgeslagen. 

#### <a name="cause"></a>Oorzaak 
Voor de connectors voor gratis schema's (het kolomnummer, de kolomnaam en het kolomgegevenstype van elke rij kunnen verschillen wanneer ze worden vergeleken met andere), gebruikt ADF standaard voorbeeldrijen (bijvoorbeeld top 100- of 1000-rijengegevens) om het schema af te leiden, en het afgeleide resultaat wordt gebruikt als een schema om gegevens te lezen. Dus als uw gegevensopslag extra kolommen bevatten die niet worden weergegeven in voorbeeldrijen, worden de gegevens van deze extra kolommen niet gelezen, verplaatst of overgebracht naar sinkgegevensopslag.

#### <a name="recommendation"></a>Aanbeveling
Als u het standaardgedrag wilt overschrijven en aanvullende velden wilt inleveren, biedt ADF opties voor het aanpassen van het bronschema. U kunt aanvullende/ontbrekende kolommen opgeven die mogelijk ontbreken in schema-defer-result in de projectie van de gegevensstroombron om de gegevens te lezen. U kunt een van de volgende opties toepassen om het aangepaste schema in te stellen. Meestal heeft **optie-1** de voorkeur.

- **Optie-1:** vergeleken met de oorspronkelijke brongegevens die één groot bestand, tabel of container kunnen zijn die miljoenen rijen met complexe schema's bevat, kunt u een tijdelijke tabel/container maken met een paar rijen die alle kolommen bevatten die u wilt lezen en vervolgens verder gaan met de volgende bewerking: 

    1. Gebruik de instellingen voor foutopsporing **van de gegevensstroombron** om **importprojectie** met voorbeeldbestanden/tabellen te gebruiken om het volledige schema op te halen. U kunt de stappen in de volgende afbeelding volgen:<br/>

        ![Schermopname van het eerste deel van de eerste optie voor het aanpassen van het bronschema.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Selecteer **Instellingen voor foutopsporing** in het gegevensstroomvas.
         1. Selecteer in het pop-upvenster Voorbeeldtabel **onder** het tabblad **cosmosSource** en voer de naam van uw tabel in het **blok Tabel** in.
         1. Selecteer **Opslaan om** uw instellingen op te slaan.
         1. Selecteer **Projectie importeren.**<br/>  
    
    1. Wijzig de **Foutopsporingsinstellingen** weer om de brongegevensset te gebruiken voor de resterende verplaatsing/transformatie van gegevens. U kunt verder gaan met de stappen in de volgende afbeelding:<br/>

        ![Schermopname van het tweede deel van de eerste optie voor het aanpassen van het bronschema.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Selecteer **Instellingen voor foutopsporing** in het gegevensstroomvas.
         1. Selecteer in het pop-upvenster **Brongegevensset** op het **tabblad cosmosSource.**
         1. Selecteer **Opslaan om** uw instellingen op te slaan.<br/>
    
    Daarna wordt de ADF-gegevensstroomruntime in ere gebracht en wordt het aangepaste schema gebruikt om gegevens uit het oorspronkelijke gegevensopslag te lezen. <br/>

- **Optie-2:** als u bekend bent met het schema en de DSL-taal van de brongegevens, kunt u het bronscript van de gegevensstroom handmatig bijwerken om extra/gemiste kolommen toe te voegen om de gegevens te lezen. In de volgende afbeelding ziet u een voorbeeld: 

    ![Schermopname met de tweede optie voor het aanpassen van het bronschema.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende resources voor meer hulp bij het oplossen van problemen:

*  [Problemen met toewijzingsgegevensstromen in Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functieaanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)