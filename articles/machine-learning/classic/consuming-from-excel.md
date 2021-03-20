---
title: 'ML Studio (klassiek): webservice gebruiken in Excel-Azure'
description: Met Azure Machine Learning Studio (klassiek) kunt u eenvoudig webservices rechtstreeks vanuit Excel aanroepen zonder dat u code hoeft te schrijven.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e8e7404a2b643cf90866253e5a7453381b66f73e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517855"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Een Azure Machine Learning Studio-webservice (klassiek) gebruiken vanuit Excel

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Met Azure Machine Learning Studio (klassiek) kunt u eenvoudig webservices rechtstreeks vanuit Excel aanroepen zonder dat u code hoeft te schrijven.

Als u Excel 2013 (of hoger) of Excel online gebruikt, wordt u aangeraden de Excel [Excel-invoeg toepassing](excel-add-in-for-web-services.md)te gebruiken.



## <a name="steps"></a>Stappen
Publiceer een webservice. [Zelf studie 3: het model](tutorial-part3-credit-risk-deploy.md) voor het maken van een credit risico geeft uitleg hoe dit moet doen. Momenteel wordt de functie Excel-werkmap alleen ondersteund voor aanvraag/antwoord-Services met één uitvoer (dat wil zeggen, een enkel Score label). 

Zodra u een webservice hebt, klikt u op de **sectie webservices** aan de linkerkant van Studio en selecteert u vervolgens de webservice die vanuit Excel moet worden gebruikt.

**Klassieke webservice**

1. Op het tabblad **dash board** voor de webservice is een rij voor de **aanvraag/antwoord** service. Als deze service één uitvoer had, ziet u de koppeling **Excel-werkmap downloaden** in die rij.

    ![Excel-werkmap downloaden met behulp van de Web Service-Portal van Studio (klassiek)](./media/consuming-from-excel/excellink.png)
2. Klik op **Excel-werkmap downloaden**.

**Nieuwe webservice**

1. Selecteer in de portal Azure Machine Learning-webservice de optie **verbruik**.
2. Klik op de pagina verbruik in de sectie opties voor het gebruik van **webservices** op het pictogram Excel.

**De werkmap gebruiken**

1. Open de werkmap.
2. Er wordt een beveiligings waarschuwing weer gegeven. Klik op de knop **bewerken inschakelen** .

    ![Bewerken inschakelen om de beveiligde weer gave beveiligings waarschuwing te verwijderen](./media/consuming-from-excel/enableeditting.png)
3. Er wordt een beveiligings waarschuwing weer gegeven. Klik op de knop **inhoud inschakelen** om macro's uit te voeren in uw werk blad.

    ![Inhoud inschakelen voor het uitschakelen van de beveiligings waarschuwing macro's verwijderen](./media/consuming-from-excel/enablecontent.png)
4. Zodra macro's zijn ingeschakeld, wordt er een tabel gegenereerd. Kolommen in blauw zijn vereist als invoer in de bron records-webservice, of **para meters**. Let op de uitvoer van de RR'S-service, **VOORspelde waarden** groen. Wanneer alle kolommen voor een bepaalde rij zijn gevuld, wordt de Score-API automatisch door de werkmap aangeroepen en worden de gescoorde resultaten weer gegeven.

    ![Tabel voor parameter invoer en de resulterende voorspelde waarden](./media/consuming-from-excel/sampletable.png)
5. Als u meer dan één rij wilt scoren, vult u de tweede rij met gegevens in en worden de voorspelde waarden geproduceerd. U kunt zelfs meerdere rijen tegelijk plakken.

U kunt een van de Excel-functies (grafieken, Power map, voorwaardelijke opmaak enz.) gebruiken met de voorspelde waarden om te helpen bij het visualiseren van de gegevens.

## <a name="sharing-your-workbook"></a>Uw werkmap delen
Voor een goede werking van de macro's moet uw API-sleutel deel uitmaken van het werk blad. Dit betekent dat u de werkmap alleen moet delen met entiteiten/individuen die u vertrouwt.

## <a name="automatic-updates"></a>Automatische updates
In deze twee situaties wordt een aanroep van een bron records gemaakt:

1. De eerste keer dat een rij inhoud bevat in alle **para meters**
2. Telkens wanneer een van de **para meters** verandert in een rij waarvoor alle **para meters** zijn opgegeven.