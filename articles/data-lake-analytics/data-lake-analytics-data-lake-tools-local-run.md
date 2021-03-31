---
title: Azure Data Lake U-SQL-scripts uitvoeren op uw lokale computer
description: Meer informatie over het gebruik van Azure Data Lake-Hulpprogram Ma's voor Visual Studio om U-SQL-taken uit te voeren op uw lokale machine.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018984"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>U-SQL-scripts uitvoeren op uw lokale computer

Wanneer u u-SQL-scripts ontwikkelt, kunt u tijd en kosten besparen door de scripts lokaal uit te voeren. Azure Data Lake-Hulpprogram Ma's voor Visual Studio bieden ondersteuning voor het uitvoeren van U-SQL-scripts op uw lokale computer. 

## <a name="basic-concepts-for-local-runs"></a>Basis concepten voor lokale uitvoeringen

In het volgende diagram ziet u de onderdelen voor lokale uitvoering en hoe deze onderdelen worden toegewezen aan de Cloud uitvoering.

|Onderdeel|Lokaal uitvoeren|Cloud uitvoering|
|---------|---------|---------|
|Storage|Hoofdmap van lokale gegevens|Standaard Azure Data Lake Store-account|
|Compute|U-SQL-engine voor lokaal uitvoeren|Azure Data Lake Analytics-Service|
|Uitvoerings omgeving|Werkmap op lokale computer|Azure Data Lake Analytics cluster|

In de volgende secties vindt u meer informatie over lokale uitvoer onderdelen.

### <a name="local-data-root-folders"></a>Lokale gegevens hoofd mappen

Een lokale hoofdmap van gegevens is een **lokaal archief** voor het lokale Compute-account. Een wille keurige map in het lokale bestands systeem op uw lokale computer kan een lokale hoofdmap van gegevens zijn. Dit is hetzelfde als de standaard Azure Data Lake Store account van een Data Lake Analytics-account. Overschakelen naar een andere hoofdmap van gegevens is net als het overschakelen naar een ander standaard archief account. 

De hoofdmap van de gegevens wordt als volgt gebruikt:
- Meta gegevens opslaan. Voor beelden zijn data bases, tabellen, functies voor tabel waarden en verzamelingen.
- Zoek naar de invoer-en uitvoer paden die als relatieve paden worden gedefinieerd in U-SQL-scripts. Door relatieve paden te gebruiken, is het eenvoudiger om uw U-SQL-scripts te implementeren in Azure.

### <a name="u-sql-local-run-engines"></a>Lokale run-engines voor U-SQL

Een U-SQL-engine voor lokaal uitvoeren is een **lokaal reken account** voor U-SQL-taken. Gebruikers kunnen U-SQL-taken lokaal uitvoeren via Azure Data Lake-Hulpprogram Ma's voor Visual Studio. Lokale uitvoeringen worden ook ondersteund via de Azure Data Lake U-SQL SDK opdracht regel-en programmeer interfaces. Meer informatie over de [Azure data Lake U-SQL-SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Werk mappen

Wanneer u een U-SQL-script uitvoert, is er een werkmap nodig om de compilatie resultaten in de cache op te slaan, logboeken uit te voeren en andere functies uit te voeren. In Azure Data Lake-Hulpprogram Ma's voor Visual Studio is de werkmap de werkmap van het U-SQL-project. Deze bevindt zich onder `<U-SQL project root path>/bin/debug>` . De werkmap wordt gereinigd telkens wanneer een nieuwe uitvoering wordt geactiveerd.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokale uitvoeringen in micro soft Visual Studio

Azure Data Lake-Hulpprogram Ma's voor Visual Studio hebben een ingebouwde lokale uitvoerings engine. De extra Opper vlakte van de engine als een lokaal reken account. Als u een U-SQL-script lokaal wilt uitvoeren, selecteert u de **lokale-machine** of het **lokale project** account in de vervolg keuzelijst editor-marge van het script. Selecteer vervolgens **verzenden**.

![Een U-SQL-script naar een lokaal account verzenden](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokale uitvoeringen met een lokaal computer account

Een **lokale computer** account is een gedeeld lokaal reken account met één lokale data root-map als het lokale archief account. De hoofdmap van de gegevens bevindt zich standaard op **C:\Users \<username> \AppData\Local\USQLDataRoot**. Het kan ook worden geconfigureerd via **hulpprogram ma's**  >  **Data Lake**  >  **Opties en instellingen**.

![Een lokale hoofdmap voor gegevens configureren](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Een U-SQL-project is vereist voor een lokale uitvoering. De werkmap van U-SQL-project wordt gebruikt voor de Active Directory voor de lokale uitvoering van U-SQL. Compilatie resultaten, uitvoer logboeken en andere bestanden voor taak uitvoering worden gegenereerd en opgeslagen in de map werkmap tijdens de lokale uitvoering. Telkens wanneer u het script opnieuw wilt uitvoeren, worden alle bestanden in de werkmap gereinigd en opnieuw gegenereerd.

## <a name="local-runs-with-a-local-project-account"></a>Lokale uitvoeringen met een account van het lokale project

Een **lokaal project** -account is een voor het project geïsoleerde lokale Compute-account voor elk project met een geïsoleerde lokale data root-map. Elk actief U-SQL-project dat in Solution Explorer in Visual Studio wordt geopend, heeft een bijbehorend `(Local-project: <project name>)` account. De accounts worden weer gegeven in de Server Explorer in Visual Studio en de U-SQL-script editor-marge.  

Het account **Local-project** biedt een schone en geïsoleerde ontwikkel omgeving. Een **lokale machine** account heeft een gedeelde lokale hoofdmap met gegevens die meta gegevens en invoer-en uitvoer gegevens voor alle lokale taken opslaat. Maar een account van het **lokale project** maakt een tijdelijke lokale hoofdmap voor gegevens onder een u-SQL-project werkmap telkens wanneer er een u-SQL-script wordt uitgevoerd. Deze map met tijdelijke gegevens wordt gereinigd wanneer een nieuwe build wordt gemaakt of opnieuw wordt uitgevoerd. 

Een U-SQL-project beheert de geïsoleerde lokale uitvoerings omgeving via een project verwijzing en-eigenschap. U kunt de invoer gegevens bronnen voor U-SQL-scripts configureren in zowel het project als de database omgevingen waarnaar wordt verwezen.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>De invoer gegevens bron beheren voor een account voor een lokaal project 

Een U-SQL-project maakt een lokale data root-map en stelt gegevens in voor een account voor een **lokaal project** . Een map met tijdelijke gegevens wordt gereinigd en opnieuw gemaakt in de werkmap van het U-SQL-project telkens wanneer een nieuwe build en een lokale uitvoering plaatsvinden. Alle gegevens bronnen die door het U-SQL-project zijn geconfigureerd, worden gekopieerd naar deze tijdelijke lokale data root-map voordat de lokale taak wordt uitgevoerd. 

U kunt de hoofdmap van uw gegevens bronnen configureren. Klik **met** de rechter muisknop op de  >    >  **gegevens bron test** van de eigenschap U-SQL project. Wanneer u een U-SQL-script uitvoert op een account van een **lokaal project** , worden alle bestanden en submappen in de map **test gegevens** gekopieerd naar de hoofdmap van de tijdelijke lokale data. Bestanden onder submappen zijn opgenomen. Nadat een lokale taak is uitgevoerd, kunnen uitvoer resultaten ook worden gevonden in de map met tijdelijke lokale data-hoofdmap in de werkmap van het project. Al deze uitvoer wordt verwijderd en gereinigd wanneer het project opnieuw wordt opgebouwd en gereinigd. 

![De test gegevens bron van een project configureren](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Een database omgeving waarnaar wordt verwezen, beheren voor een account voor een **lokaal project** 

Als een U-SQL-query gebruikmaakt van of query's met U-SQL database-objecten, moet u de database omgevingen lokaal voorbereiden voordat u het U-SQL-script lokaal uitvoert. Voor een account voor een **lokaal project** kunnen u-SQL database afhankelijkheden worden beheerd door u-SQL-project verwijzingen. U kunt u-SQL database project verwijzingen toevoegen aan uw U-SQL-project. Voordat U U-SQL-scripts uitvoert op een account van een **lokaal project** , worden alle data bases waarnaar wordt verwezen, geïmplementeerd naar de hoofdmap voor tijdelijke lokale gegevens. Voor elke uitvoering wordt de hoofdmap van de tijdelijke gegevens gereinigd als een nieuwe geïsoleerde omgeving.

Bekijk dit gerelateerde artikel:
* Meer informatie over het beheren van U-SQL database definities en verwijzingen in [U-SQL database projecten](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Het verschil tussen accounts van **lokale computers** en **lokale projecten**

Een **lokale machine** account simuleert een Azure data Lake Analytics-account op lokale computers van gebruikers. Het deelt dezelfde ervaring met een Azure Data Lake Analytics-account. Een account voor een **lokaal project** biedt een gebruiks vriendelijke lokale ontwikkel omgeving. Deze omgeving helpt gebruikers bij het implementeren van database verwijzingen en invoer gegevens voordat ze scripts lokaal uitvoeren. Een **lokale machine** account biedt een gedeelde permanente omgeving die toegankelijk is via alle projecten. Een account voor een **lokaal project** biedt een geïsoleerde ontwikkel omgeving voor elk project. Deze wordt vernieuwd voor elke uitvoering. Een account voor een **lokaal project** biedt een snellere ontwikkel ervaring door snel nieuwe wijzigingen toe te passen.

In de volgende tabel ziet u meer verschillen tussen accounts voor **lokale computers** en **lokale projecten** :

|Verschil hoek|Lokale computer|Lokaal-project|
|----------------|---------------|---------------|
|Lokale toegang|Kan worden gebruikt door alle projecten.|Alleen het bijbehorende project heeft toegang tot dit account.|
|Hoofdmap van lokale gegevens|Een permanente lokale map. Geconfigureerd via **hulpprogram ma's**  >  **Data Lake**  >  **Opties en instellingen**.|Een tijdelijke map die wordt gemaakt voor elke lokale uitvoering onder de U-SQL-project werkmap. De map wordt gereinigd wanneer er opnieuw wordt opgebouwd of opnieuw wordt uitgevoerd.|
|Invoer gegevens voor een U-SQL-script|Het relatieve pad onder de permanente lokale data root-map.|Stel de gegevens bron voor de eigenschap test van **U-SQL-project** in  >  . Alle bestanden en submappen worden gekopieerd naar de basismap van de tijdelijke gegevens voordat een lokale uitvoering wordt uitgevoerd.|
|Uitvoer gegevens voor een U-SQL-script|Relatief pad onder de permanente lokale gegevens basismap.|Uitvoer naar de basismap van de tijdelijke gegevens. De resultaten worden gereinigd wanneer een nieuwe build wordt gemaakt of opnieuw wordt uitgevoerd.|
|Data base-implementatie waarnaar wordt verwezen|Data bases waarnaar wordt verwezen, worden niet automatisch geïmplementeerd wanneer ze worden uitgevoerd op een **lokale machine** account. Dit is hetzelfde voor het indienen van een Azure Data Lake Analytics-account.|Data bases waarnaar wordt verwezen, worden automatisch geïmplementeerd op het **lokale-project** account vóór een lokale uitvoering. Alle database omgevingen worden gereinigd en opnieuw geïmplementeerd wanneer er opnieuw wordt opgebouwd of opnieuw wordt uitgevoerd.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Een lokale run met de U-SQL-SDK

U kunt u-SQL-scripts lokaal uitvoeren in Visual Studio en ook de Azure Data Lake U-SQL-SDK gebruiken om U-SQL-scripts lokaal uit te voeren met opdracht regel-en programmeer interfaces. Via deze interfaces kunt u lokale en tests voor het uitvoeren van U-SQL automatiseren.

Meer informatie over de [Azure data Lake U-SQL-SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Volgende stappen

- [Een CI/cd-pijp lijn instellen voor Azure data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Uw Azure data Lake Analytics code testen](data-lake-analytics-cicd-test.md).
