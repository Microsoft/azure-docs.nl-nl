---
title: 'ML Studio (klassiek): Excel-invoeg toepassing voor Web Services-Azure'
description: Azure Machine Learning webservices rechtstreeks in Excel gebruiken zonder dat u code hoeft te schrijven.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 45d9e494b9f885cfa0680bec595aefcd4074d41e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519997"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel-invoeg toepassing voor Azure Machine Learning Studio (klassieke) webservices

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Excel maakt het eenvoudig om webservices rechtstreeks aan te roepen zonder dat u code hoeft te schrijven.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Stappen voor het gebruik van een bestaande webservice in de werkmap

1. Open het [Excel-voorbeeld bestand](https://aka.ms/amlexcel-sample-2), dat de Excel-invoeg toepassing bevat en gegevens over reizigers op het Titanic. 
 
    > [!NOTE]
    > - U ziet de lijst met de webservices die zijn gerelateerd aan het bestand en onder een selectie vakje voor ' automatisch voors pellen '. Als u automatische voor speling inschakelt, worden de voor spellingen van **alle** Services telkens bijgewerkt wanneer er een wijziging wordt doorgevoerd in de invoer. Als u dit selectie vakje uitschakelt, moet u op alles voors pellen klikken voor vernieuwen. Ga naar stap 6 voor het inschakelen van automatische voor speling op een service niveau.
    > - De Azure Machine Learning Excel-invoeg toepassing roept het archief van Office-invoeg toepassingen aan om te laden. Als uw organisatie toegang heeft tot de Office-invoeg toepassingen Store, wordt de fout weer geven bij het laden van de invoeg toepassing. In dit geval implementeert u de Azure Machine Learning Excel-invoeg toepassing vanuit Microsoft 365 beheer centrum. Roep vervolgens de invoeg toepassing aan en voeg de webservice hand matig toe door de URL en API-sleutel te plakken.

 

2. Kies de webservice door erop te klikken: ' Titanic Survivor Prediction (voor beeld van Excel-invoeg toepassing) [score] ' in dit voor beeld.
   
    ![Webservice selecteren](./media/excel-add-in-for-web-services/image1.png)
3. Hiermee gaat u naar de sectie voor **speld** .  Deze werkmap bevat al voorbeeld gegevens, maar voor een lege werkmap kunt u een cel in Excel selecteren en op **voorbeeld gegevens gebruiken** klikken.
4. Selecteer de gegevens met kopteksten en klik op het pictogram invoer gegevens bereik.  Zorg ervoor dat het selectie vakje mijn gegevens bevat kopteksten is ingeschakeld.
5. Onder **uitvoer** voert u het nummer in van de cel waar de uitvoer moet worden opgegeven, bijvoorbeeld "H1".
6. Klik op voor **spel**. Als u het selectie vakje ' automatisch voors pellen ' inschakelt, wordt een wijziging in de geselecteerde gebieden (de waarden die zijn opgegeven als invoer) geactiveerd en wordt er een update van de uitvoer cellen uitgevoerd zonder dat u op de knop voor het voors pellen hoeft te drukken.
   
    ![Sectie voors pellen](./media/excel-add-in-for-web-services/image1.png)

Een webservice implementeren of een bestaande webservice gebruiken. Zie voor meer informatie over het implementeren van een webservice [zelf studie 3: het model](tutorial-part3-credit-risk-deploy.md)voor het implementeren van een credit risico.

Down load de API-sleutel voor uw webservice. Wanneer u deze actie uitvoert, is afhankelijk van of u een klassieke Machine Learning-webservice van een nieuwe Machine Learning-webservice hebt gepubliceerd.

**Een klassieke webservice gebruiken** 

1. Klik in Machine Learning Studio (klassiek) op de sectie **WEBservices** in het linkerdeel venster en selecteer vervolgens de webservice.
   
    ![Studio een webservice selecteren](./media/excel-add-in-for-web-services/image4.png)
2. Kopieer de API-sleutel voor de webservice.
   
    ![Studio API-sleutel](./media/excel-add-in-for-web-services/image5.png)
3. Klik op het tabblad **dash board** voor de webservice op de koppeling **aanvraag/antwoord** .
4. Zoek naar de sectie **URI van aanvraag** .  Kopieer de URL en sla deze op.

> [!NOTE]
> Het is nu mogelijk om u aan te melden bij de [Azure machine learning Web Services](https://services.azureml.net) -Portal om de API-sleutel voor een klassieke machine learning-webservice te verkrijgen.
> 
> 

**Een nieuwe webservice gebruiken**

1. Klik in het [Azure machine learning Web Services](https://services.azureml.net) -Portal op **webservices** en selecteer vervolgens uw webservice. 
2. Klik op **verbruik**.
3. Zoek naar de sectie **basis informatie over verbruik** . Kopieer de **primaire sleutel** en sla de **aanvraag-antwoord-** URL op.

## <a name="steps-to-add-a-new-web-service"></a>Stappen voor het toevoegen van een nieuwe webservice

1. Een webservice implementeren of een bestaande webservice gebruiken. Zie voor meer informatie over het implementeren van een webservice [zelf studie 3: het model](tutorial-part3-credit-risk-deploy.md)voor het implementeren van een credit risico.
2. Klik op **verbruik**.
3. Zoek naar de sectie **basis informatie over verbruik** . Kopieer de **primaire sleutel** en sla de **aanvraag-antwoord-** URL op.
4. Ga in Excel naar de sectie **webservices** (als u in de sectie voor **spelt** klikt u op de pijl terug om naar de lijst met webservices te gaan).
   
    ![Ga naar de selectie van de webservice](./media/excel-add-in-for-web-services/image3.png)
5. Klik op **Add Web Service**.
6. Plak de URL in het tekstvak Excel-invoeg toepassing met de naam **URL**.
7. Plak de API/Primary-sleutel in de label- **API-sleutel** van het tekstvak.
8. Klik op **Add**.
   
    ![URL en API-sleutel voor een klassieke webservice.](./media/excel-add-in-for-web-services/image6.png)
9. Als u de webservice wilt gebruiken, volgt u de voor gaande instructies, ' stappen voor het gebruik van een bestaande webservice '.

## <a name="sharing-your-workbook"></a>Uw werkmap delen
Als u de werkmap opslaat, wordt de API/Primary-sleutel voor de webservices die u hebt toegevoegd, ook opgeslagen. Dit betekent dat u de werkmap alleen moet delen met personen die u vertrouwt.

Stel alle vragen in de volgende sectie met opmerkingen of op ons [forum](/answers/topics/azure-machine-learning.html).