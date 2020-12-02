---
title: 'ML Studio (klassiek) zelfstudie 3: Kredietrisicomodellen implementeren - Azure'
description: Een gedetailleerde zelfstudie voor het maken van een predictive analytics-oplossing voor kredietrisicobeoordeling in Azure Machine Learning Studio (klassiek). Deze zelfstudie is deel drie van een driedelige serie. U ontdek hierin hoe u een model implementeert als webservice.
keywords: credit risk, predictive analytics solution,risk assessment, deploy, web service
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 3afcf87c360651ac314450910fbf5ab72afd289a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503902"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Zelfstudie 3: Een kredietrisicomodel implementeren - Azure Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek)   ![Dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

In deze zelfstudie wordt uitgebreid ingegaan op het ontwikkelingsproces van een predictive analytics-oplossing. U ontwikkelt een eenvoudig model in Machine Learning Studio (klassiek).  Vervolgens implementeert u het model als een Azure Machine Learning-webservice.  Dit geïmplementeerde model kan voorspellingen doen op basis van nieuwe gegevens. Deze zelfstudie is **deel drie van een driedelige serie**.

Stel dat u iemands kredietrisico moet voorspellen op basis van de gegevens die deze persoon in een kredietaanvraag heeft ingevuld.  

Kredietrisicobeoordeling is een complex probleem, maar in deze zelfstudie wordt het enigszins vereenvoudigd. Het wordt gebruikt als voorbeeld van hoe u een predictive analytics-oplossing kunt maken met behulp van Microsoft Azure Machine Learning Studio (klassiek). U gebruikt Azure Machine Learning Studio (klassiek) en een Machine Learning-webservice om deze oplossing te maken. 

In deze driedelige zelfstudie begint u met openbaar beschikbare kredietrisicogegevens.  Vervolgens ontwikkelt en traint u een voorspellend model.  En ten slotte implementeert u het model als een webservice.

In [deel één van de zelfstudie](tutorial-part1-credit-risk.md) hebt u een Machine Learning Studio (klassiek)-werkruimte gemaakt, gegevens geüpload en een experiment gemaakt.

In [deel twee van de zelfstudie](tutorial-part2-credit-risk-train.md) hebt u modellen getraind en geëvalueerd.

In dit deel van de zelfstudie gaat u het volgende doen:

> [!div class="checklist"]
> * Implementatie voorbereiden
> * De webservice implementeren
> * De webservice testen
> * De webservice beheren
> * De webservice openen

## <a name="prerequisites"></a>Vereisten

Voltooi [deel twee van de zelfstudie](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Implementatie voorbereiden
Als u anderen de mogelijkheid wilt bieden om het voorspellende model te gebruiken dat u in deze zelfstudie hebt ontwikkeld, kunt u het als webservice implementeren in Azure.

Tot nu hebt u geëxperimenteerd met het trainen van het model. Met de geïmplementeerde service wordt echter niet meer getraind - hij wordt gebruikt om nieuwe voorspellingen te genereren door de invoer van de gebruiker te beoordelen op basis van het model. We gaan nu voorbereidingen treffen om dit experiment van een ***trainingsexperiment** _ te converteren naar een _*_voorspellend_*_ experiment. 

Het voorbereiden op de implementatie bestaat uit drie stappen:  

1. Een van de modellen verwijderen
1. Het _trainingsexperiment* dat u hebt gemaakt, converteren naar een *voorspellend experiment*
1. Het voorspellende experiment implementeren als webservice

### <a name="remove-one-of-the-models"></a>Een van de modellen verwijderen

Om te beginnen met u dit experiment beperken. Momenteel bestaat het experiment over twee verschillende modellen, maar u moet slechts één model gebruiken wanneer u het experiment implementeert als webservice.  

Stel dat u hebt besloten dat het Boosted Tree-model beter presteerde dan het dan de SVM-model. Als eerste verwijdert u dan de module [Ondersteuningsvectormachine met twee klassen][two-class-support-vector-machine] en de modules die zijn gebruikt voor het trainen van deze module. Het is een goed idee om eerst een kopie van het experiment te maken door op **Opslaan als** te klikken aan de onderzijde van het experimentcanvas.

U moet de volgende modules verwijderen:  

* [Ondersteuningsvectormachine met twee klassen][two-class-support-vector-machine]
* De modules [Trainingsmodel][train-model] en [Beoordelingsmodel][score-model] die eraan waren gekoppeld
* [Gegevens normaliseren][normalize-data] (van beide)
* [Model evalueren][evaluate-model] (omdat u klaar bent met het evalueren van de modellen)

Selecteer de modules en druk op Delete, of klik met de rechtermuisknop op de modules en selecteer **Verwijderen**. 

![Hiermee wordt gemarkeerd welke modules moeten worden verwijderd om het Support Vector Machine-model te verwijderen](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Het model zou er nu ongeveer zo uit moeten zien:

![Het resulterende experiment wanneer het Support Vector Machine-model wordt verwijderd](./media/tutorial-part3-credit-risk-deploy/publish3.png)

U bent er nu klaar voor om het model te implementeren met de [Versterkte beslissingsstructuur met twee klassen][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Het trainingsexperiment converteren naar een voorspellend experiment

Om het model voor te bereiden voor implementatie moet u het trainingsexperiment omzetten in een voorspellend experiment. Dit omvat drie stappen:

1. Sla het model dat u hebt getraind op en vervang de trainingsmodules
1. Werk het experiment bij door modules te verwijderen die alleen voor de training nodig waren
1. Bepaal waar de webservice invoer accepteert en waar de uitvoer wordt gegenereerd

U kunt dit handmatig doen, maar gelukkig kunnen alle drie de stappen worden uitgevoerd door onder in het experimentcanvas te klikken op **Set Up Web Service** (Webservice configureren). Selecteer dan de optie **Predictive Web Service** (Voorspellende webservice).

> [!TIP]
> Zie [Uw model voorbereiden voor implementatie in Azure Machine Learning Studio (klassiek)](deploy-a-machine-learning-web-service.md) voor meer informatie over wat er gebeurt wanneer u een trainingsexperiment omzet in een voorspellend experiment.

Wanneer u op **Set Up Web Service** klikt, gebeuren er verschillende zaken:

* Het getrainde model wordt omgezet in één module met een **getraind model**. De module wordt opgeslagen in het modulepalet links van het experimentcanvas. (Terug te vinden bij de **getrainde modellen**)
* Modules die zijn gebruikt voor de training, worden verwijderd. Specifieker gezegd:
  * [Versterkte beslissingsstructuur met twee klassen][two-class-boosted-decision-tree]
  * [Trainingsmodel][train-model]
  * [Gegevens splitsen][split]
  * De tweede module voor de module [R-script uitvoeren][execute-r-script] die is gebruikt voor het testen van gegevens
* Het opgeslagen getrainde model wordt weer toegevoegd aan het experiment
* De modules **Webservice-invoer** en **Webservice-uitvoer** worden toegevoegd (deze bepalen waar de gegevens van de gebruiker worden ingevoerd in het model, welke gegevens worden geretourneerd en wanneer de webservice wordt geopend)

> [!NOTE]
> U kunt zien dat het experiment in twee delen wordt opgeslagen op tabbladen die boven aan het experimentcanvas zijn toegevoegd. Het oorspronkelijke trainingsexperiment staat op het tabblad **Trainingsexperiment** en het zojuist gemaakte voorspellende experiment staat bij **Voorspellend experiment**. U gaat het voorspellende experiment implementeren als webservice.

U moet nog een extra stap uitvoeren bij dit specifieke experiment.
U hebt twee keer de module [R-script uitvoeren][execute-r-script] toegevoegd om de gegevens te kunnen wegen. Dat trucje had u nodig voor trainingen en tests, dus u kunt ze uit het uiteindelijke model verwijderen.
Machine Learning Studio (klassiek) heeft één [R-script uitvoeren][execute-r-script]-module verwijderd bij het verwijderen van de module [Splitsen][split]. U kunt de andere nu verwijderen en [Metagegevenseditor][metadata-editor] rechtstreeks koppelen aan [Beoordelingsmodel][score-model].    

Het experiment ziet er nu als volgt uit:  

![Het getrainde model beoordelen](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> U vraagt zich misschien af waarom u de gegevensset UCI German Credit Card niet hebt meegenomen uit het voorspellende experiment. De service gaat de gegevens van de gebruiker beoordelen (niet de oorspronkelijke gegevensset), dus waarom zou u de oorspronkelijke gegevensset in het model laten staan?
> 
> De service heeft de oorspronkelijke creditcardgegevens niet nodig. Het schema van de gegevens is echter wel nodig. Dit bevat informatie over het aantal kolommen dat er is en welke kolommen numeriek zijn. Deze schemagegevens zijn nodig om de gegevens van de gebruiker te kunnen interpreteren. U laat deze onderdelen verbonden zodat de beoordelingsmodule beschikt over het gegevenssetschema wanneer de service actief is. De gegevens worden niet gebruikt, maar het schema wel.  
> 
>Het is belangrijk om te weten dat als uw oorspronkelijke gegevensset het label bevatte, het verwachte schema van de webinvoer ook een kolom met dat label verwacht! U kunt dit oplossen door het label te verwijderen in combinatie met andere gegevens die in de gegevensset van de training zaten, maar die niet in de webinvoer staan. Doe dit voordat u de webinvoer en trainingsgegevensset koppelt aan een algemene module. 
> 

Voer het experiment nog een laatste keer uit (klik op **Uitvoeren**). Als u wilt controleren of het model nog steeds werkt, klikt u op de uitvoer van de module [Beoordelingsmodel][score-model] en selecteert u **Resultaten weergeven**. U kunt zien dat de oorspronkelijke gegevens worden weergegeven, samen met de kredietrisicowaarde ('beoordeelde labels') en de waarschijnlijkheidswaarde van de beoordeling ('beoordeelde waarschijnlijkheden'). 

## <a name="deploy-the-web-service"></a>De webservice implementeren
U kunt het experiment als klassieke webservice implementeren of als nieuwe webservice (op basis van Azure Resource Manager).

### <a name="deploy-as-a-classic-web-service"></a>Als een klassieke webservice implementeren
Als u op basis van het experiment een klassieke webservice wilt implementeren, klikt u op **Webservice implementeren** onder het canvas en selecteert u **Webservice implementeren (klassiek)** . Machine Learning Studio (klassiek) implementeert het experiment als webservice en leidt u naar het dashboard van die webservice. Op deze pagina kunt u terugkeren naar het experiment (**Momentopname weergeven** of **Nieuwste weergeven**) en een eenvoudige test uitvoeren voor de webservice (zie **De webservice testen** hieronder). Er staat hier ook informatie over het maken van toepassingen die toegang kunnen verkrijgen tot de webservice. In de volgende stap van deze zelfstudie komt u daar meer over te weten.

![Dashboard van de webservice](./media/tutorial-part3-credit-risk-deploy/publish6.png)


U kunt de service configureren door op het tabblad **CONFIGURATIE** te klikken. U kunt hier de naam van de service wijzigen (deze krijgt standaard de naam van het experiment) en een beschrijving opgeven. U kunt ook meer beschrijvende labels opgeven voor de invoer- en uitvoergegevens.  

![De webservice configureren](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Als een nieuwe webservice implementeren

> [!NOTE] 
> Voor het implementeren van een nieuwe webservice moet u over voldoende machtigingen beschikken in het abonnement waarvoor u de webservice implementeert. Zie [Manage a web service using the Azure Machine Learning Web Services portal](manage-new-webservice.md) (Een webservice beheren met de Azure Machine Learning-portal voor webservices) voor meer informatie. 

Een nieuwe webservice implementeren op basis van het experiment:

1. Klik op **Webservice implementeren** onder het canvas en selecteer **Webservice implementeren [nieuw]** . Machine Learning Studio (klassiek) brengt u naar de Azure Machine Learning-webservicespagina **Experiment implementeren**.

1. Voer een naam in voor de webservice. 

1. Bij **Prijsplan** kunt u een bestaand prijsplan selecteren of u selecteert Nieuwe maken. Geef het nieuwe plan dan een naam en selecteer de maandelijkse optie. Standaard worden de prijscategorieën voor uw standaardregio gebruikt en de webservice wordt ook in die regio geïmplementeerd.

1. Klik op **Implementeren**.

Na een paar minuten wordt de pagina **Quickstart** van uw webservice geopend.

U kunt de service configureren door op het tabblad **Configureren** te klikken. U kunt hier de naam van de service wijzigen en een beschrijving toevoegen. 

Als u de webservice wilt testen, klikt u op het tabblad **Testen** (zie **De webservice testen** hieronder). Voor informatie over het maken van toepassingen die toegang hebben tot de webservice, klikt u op het tabblad **Verbruiken** (in de volgende stap van deze zelfstudie krijgt u hier meer informatie over).

> [!TIP]
> U kunt de webservice na het implementeren bijwerken. Als u bijvoorbeeld uw model wilt wijzigen, bewerkt u het trainingsexperiment, verbetert u de modelparameters en klikt u op **Webservice implementeren**. Selecteer dan **Webservice implementeren (klassiek)** of **Webservice implementeren (nieuw)** . Wanneer u het experiment opnieuw implementeert, wordt de webservice vervangen. Er wordt dan gebruikgemaakt van het bijgewerkte model.  
> 
> 

## <a name="test-the-web-service"></a>De webservice testen

Als de webservice wordt geopend, worden de gegevens van de gebruiker ingevoerd via de module **Webservice-invoer**. Van hieruit worden ze doorgegeven aan de module [Beoordelingsmodel][score-model] en beoordeeld. Met deze manier waarop u het voorspellende experiment hebt ingesteld, verwacht het model gegevens in dezelfde indeling als die van de oorspronkelijke kredietrisicogegevensset.
De resultaten worden aan de gebruiker geretourneerd vanuit de webservice, via de module **Webservice-uitvoer**.

> [!TIP]
> Met de manier waarop het voorspellende experiment is geconfigureerd, worden de volledige resultaten van de module [Beoordelingsmodel][score-model] geretourneerd. Deze omvatten alle invoergegevens, plus de kredietrisicowaarde en de beoordelingswaarschijnlijkheid. U kunt echter ook iets anders retourneren als u wilt; u zou bijvoorbeeld alleen de kredietrisicowaarde kunnen retourneren. Hiervoor voegt u de module [Kolommen selecteren][select-columns] toe tussen [Beoordelingsmodel][score-model] en **Webservice-uitvoer**. De kolommen die u niet door de webservice wilt laten retourneren, worden dan verwijderd. 
> 
> 

U kunt klassieke webservices testen in **Machine Learning Studio (klassiek)** of in de portal voor **Azure Machine Learning-webservices**.
U kunt nieuwe webservices alleen testen in de portal voor **Machine Learning-webservices**.

> [!TIP]
> Wanneer u een test uitvoert in de portal voor Azure Machine Learning-webservices, kunt u de portal voorbeeldgegevens laten maken. Deze kunt u vervolgens gebruiken voor het testen van de aanvraag/antwoord-service. Op de pagina **Configureren** selecteert u Ja bij **Voorbeeldgegevens inschakelen?** . Wanneer u het tabblad Aanvraag/antwoord opent op de pagina **Testen**, vult de portal de voorbeeldgegevens in van de oorspronkelijke kredietrisicogegevensset.

### <a name="test-a-classic-web-service"></a>Een klassieke webservice testen

U kunt klassieke webservices testen in Machine Learning Studio (klassiek) of in de portal voor Machine Learning-webservices. 

#### <a name="test-in-machine-learning-studio-classic"></a>Testen in Machine Learning Studio (klassiek)

1. Op de pagina **DASHBOARD** van de webservice klikt u op de knop **Testen** onder het **standaardeindpunt**. Er wordt een dialoogvenster weergegeven en u wordt om de invoergegevens voor de service gevraagd. Dit zijn dezelfde kolommen die zijn weergegeven in de oorspronkelijke kredietrisicogegevensset.  

1. Voer een set gegevens in en klik vervolgens op **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testen in de Machine Learning-webserviceportal

1. Op de pagina **DASHBOARD** van de webservice klikt u op de koppeling **Voorbeeld testen** onder het **standaardeindpunt**. De testpagina in de portal voor Azure Machine Learning-webservices wordt geopend voor het webservice-eindpunt. U wordt gevraagd om de invoergegevens voor de service. Dit zijn dezelfde kolommen die zijn weergegeven in de oorspronkelijke kredietrisicogegevensset.

2. Klik op **Aanvraag/antwoord testen**. 

### <a name="test-a-new-web-service"></a>Een nieuwe webservice testen

U kunt nieuwe webservices alleen testen in de portal voor Machine Learning-webservices.

1. In de portal voor [Azure Machine Learning-webservices](https://services.azureml.net/quickstart) klikt u boven aan de pagina op **Testen**. De pagina **Testen** wordt geopend. Hier kunt u gegevens voor de service invoeren. De invoervelden die worden weergegeven, komen overeen met de kolommen die werden weergegeven in de oorspronkelijke kredietrisicogegevensset. 

1. Voer een set gegevens in en klik vervolgens op **Test Request-Response** (Aanvraag/antwoord testen).

De resultaten van de test worden aan de rechterkant van de pagina weergegeven, in de uitvoerkolom. 


## <a name="manage-the-web-service"></a>De webservice beheren

Wanneer u uw webservice hebt geïmplementeerd (klassiek of nieuw), kunt u deze beheren via de [Microsoft Azure Machine Learning-webservice](https://services.azureml.net/quickstart)portal.

De prestaties van uw webservice bewaken:

1. Meld u aan bij de [Microsoft Azure Machine Learning-webservices](https://services.azureml.net/quickstart)portal
1. Klik op **Webservices**
1. Klik op uw webservice
1. Klik op het **dashboard**

## <a name="access-the-web-service"></a>De webservice openen

In de vorige stap van deze zelfstudie hebt u een webservice geïmplementeerd die gebruikmaakt voorspellingsmodel voor kredietrisico. Gebruikers kunnen er nu gegevens naar verzenden en resultaten ontvangen. 

De webservice is een Azure-webservice waarmee gegevens kunnen worden ontvangen en geretourneerd. Hiervoor wordt op één van de volgende manieren gebruikgemaakt van REST API's:  

* **Aanvraag/antwoord**: de gebruiker stuurt een of meer rijen kredietgegevens naar de service aan de hand van een HTTP-protocol. De service reageert met een of meer resultatensets.
* **Batchuitvoering**: de gebruiker slaat een of meer rijen kredietgegevens op in een Azure-blob en stuurt vervolgens de bloblocatie naar de service. De service beoordeelt de rijen met gegevens in de invoerblob, slaat de resultaten op in een andere blob en retourneert de URL van de container. 

> [!NOTE]
> Functiekolomnamen in Studio (klassiek) zijn **hoofdlettergevoelig**. Zorg ervoor dat uw invoergegevens voor het aanroepen van de webservice dezelfde kolomnamen hebben als in de trainingsgegevensset.

Zie [Een Azure Machine Learning-webservice gebruiken met een web-app-sjabloon](./consume-web-services.md) voor meer informatie over toegang tot en het gebruik van de web-app-sjablonen.



## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende stappen voltooid:

> [!div class="checklist"]
> * Implementatie voorbereiden
> * De webservice implementeren
> * De webservice testen
> * De webservice beheren
> * De webservice openen

U kunt ook een aangepaste toepassing ontwikkelen voor toegang tot de webservice. Gebruik hiervoor de beginnerscode die wordt aangeboden in de programmeertalen R, C# en Python.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning-webservice gebruiken](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[metadata-editor]: /azure/machine-learning/studio-module-reference/edit-metadata
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset