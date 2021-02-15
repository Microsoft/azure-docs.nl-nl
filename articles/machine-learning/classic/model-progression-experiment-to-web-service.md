---
title: 'ML Studio (klassiek): hoe een model wordt een webservice-Azure'
description: Een overzicht van de mechanismen van hoe het model van Azure Machine Learning Studio (klassiek) van een ontwikkelings experiment naar een webservice wordt uitgevoerd.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 4e0f5786047977a319825aae9f3c7b89c0aa118b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518620"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Hoe een model van Machine Learning Studio (klassiek) van een experiment naar een webservice wordt uitgevoerd

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek)   ![Dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Azure Machine Learning Studio (klassiek) biedt een interactief canvas waarmee u een ***experiment*** kunt ontwikkelen, uitvoeren, testen en herhalen voor een voorspellend analyse model. Er zijn tal van beschik bare modules die kunnen:

* Gegevens invoeren in uw experiment
* De gegevens bewerken
* Een model trainen met machine learning algoritmen
* Het model beoordelen
* De resultaten evalueren
* Uitvoer van de laatste waarden

Zodra u tevreden bent met uw experiment, kunt u dit implementeren als een ***klassiek Azure machine learning-webservice** _ of een _ *_nieuwe Azure machine learning-webservice_** zodat gebruikers nieuwe gegevens kunnen verzenden en de resultaten terugkrijgen.

In dit artikel geven we een overzicht van de mechanismen van hoe uw Machine Learning model van een ontwikkelings experiment naar een operationele webservice verloopt.

> [!NOTE]
> Er zijn andere manieren om machine learning modellen te ontwikkelen en te implementeren, maar dit artikel is gericht op de manier waarop u Machine Learning Studio (klassiek) gebruikt. Als u bijvoorbeeld een beschrijving wilt lezen van het maken van een klassieke voorspellende webservice met R, raadpleegt u het blog bericht [Build & het implementeren van voorspellende web apps met behulp van RStudio en Azure machine learning Studio](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml).
>
>

Hoewel Azure Machine Learning Studio (klassiek) is ontworpen om u te helpen bij het ontwikkelen en implementeren van een *voorspellend analyse model*, is het mogelijk Studio (klassiek) te gebruiken voor het ontwikkelen van een experiment dat geen voorspellend analyse model bevat. Een experiment kan bijvoorbeeld alleen gegevens invoeren, bewerken en vervolgens de resultaten uitvoeren. Net als bij een voor speld analyse experiment kunt u dit niet-voorspellende experiment implementeren als een webservice, maar dit is een eenvoudiger proces omdat het experiment geen training of een beoordeling van een machine learning model is. Hoewel het niet gebruikelijk is om Studio (klassiek) op deze manier te gebruiken, nemen we deze op in de discussie zodat we een volledige uitleg kunnen geven over hoe Studio (klassiek) werkt.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Een voorspellende webservice ontwikkelen en implementeren
Hier volgen de stappen die een typische oplossing volgt wanneer u deze ontwikkelt en implementeert met behulp van Machine Learning Studio (klassiek):

![Implementatie stroom](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Afbeelding 1: fasen van een typisch voorspellend analyse model*

### <a name="the-training-experiment"></a>Het trainings experiment
Het ***trainings experiment*** is de eerste fase van het ontwikkelen van uw web-service in machine learning Studio (klassiek). Het trainings experiment is bedoeld om u een plek te geven voor het ontwikkelen, testen, herhalen en uiteindelijk trainen van een machine learning model. U kunt zelfs meerdere modellen tegelijk trainen tijdens het zoeken naar de beste oplossing, maar nadat u klaar bent met het experimenteren, selecteert u één getraind model en verwijdert u de rest van het experiment. Voor een voor beeld van het ontwikkelen van een experiment voor voorspellende analyses raadpleegt u [een Predictive Analytics oplossing ontwikkelen voor een beoordeling van de krediet risico in azure machine learning Studio (klassiek)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Het voorspellende experiment
Zodra u een getraind model hebt in uw trainings experiment, klikt u op **webservice instellen** en selecteert u **Predictive Web Service** in machine learning Studio (klassiek) om het proces van het converteren van uw trainings experiment naar een **_voorspellend experiment_** te initiëren. Het doel van het voorspellende experiment is het gebruik van uw getrainde model om nieuwe gegevens te scoren, met als doel een Azure-webservice te gebruiken.

Deze conversie vindt plaats door de volgende stappen uit te voeren:

* De set modules die worden gebruikt voor trainingen omzetten in een enkele module en deze opslaan als een getraind model
* Overbodige modules elimineren die geen verband houden met Score
* Invoer-en uitvoer poorten toevoegen die door de uiteindelijke webservice worden gebruikt

Er zijn mogelijk meer wijzigingen die u wilt aanbrengen om uw voorspellende experiment gereed te maken voor de implementatie als een webservice. Als u bijvoorbeeld wilt dat de webservice alleen een subset met resultaten kan uitvoeren, kunt u een filter module toevoegen vóór de uitvoer poort.

In dit conversie proces wordt het trainings experiment niet verwijderd. Wanneer het proces is voltooid, hebt u twee tabbladen in Studio (klassiek): één voor het trainings experiment en één voor het voorspellende experiment. Op deze manier kunt u wijzigingen aanbrengen in het trainings experiment voordat u de webservice implementeert en het voorspellende experiment opnieuw bouwt. U kunt ook een kopie van het trainings experiment opslaan om een andere regel van experimenten te starten.

> [!NOTE]
> Wanneer u op **Predictive web service** klikt, start u een automatisch proces om uw trainings experiment om te zetten in een voorspellend experiment. dit werkt in de meeste gevallen goed. Als uw trainings experiment complex is (bijvoorbeeld omdat u meerdere paden hebt voor de training die u samenvoegt), kunt u deze conversie het beste hand matig uitvoeren. Zie [uw model voorbereiden voor implementatie in azure machine learning Studio (klassiek)](deploy-a-machine-learning-web-service.md)voor meer informatie.
>
>

### <a name="the-web-service"></a>De webservice
Zodra u tevreden bent over uw voorspellende experiment, kunt u uw service als een klassieke webservice of een nieuwe webservice implementeren op basis van Azure Resource Manager. Als u uw model wilt operationeel maken door het te implementeren als een *klassieke machine learning-webservice*, klikt u op **webservice implementeren** en selecteert u **Web service implementeren [klassiek]**. Als u de *nieuwe machine learning-webservice* wilt implementeren, klikt u op **webservice implementeren** en selecteert u **Web service implementeren [New]**. Gebruikers kunnen nu gegevens naar uw model verzenden met de webservice REST API en de resultaten terugsturen. Zie [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Azure Machine Learning-webservice gebruiken) voor meer informatie.

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Het niet-typische geval: het maken van een niet-voorspellende webservice
Als uw experiment geen voorspellend analyse model traint, hoeft u geen Oefen experiment en een score voor punten te maken. er is slechts één experiment, en u kunt het implementeren als een webservice. Machine Learning Studio (klassiek) detecteert of uw experiment een voorspellend model bevat door de modules te analyseren die u hebt gebruikt.

Nadat u het experiment hebt herhaald en ermee tevreden bent:

1. Klik op **webservice instellen** en selecteer opnieuw **training web service** -invoer-en uitvoer knooppunten worden automatisch toegevoegd
2. Klik op **Uitvoeren**
3. Klik op **Deploy web service** en selecteer **Deploy web service [Classic]** of **Deploy web service [New]** , afhankelijk van de omgeving die u wilt implementeren.

Uw webservice is nu geïmplementeerd en u kunt deze net als een voorspellende webservice openen en beheren.

## <a name="updating-your-web-service"></a>Uw webservice bijwerken
Nu u uw experiment hebt geïmplementeerd als een webservice, wat moet u doen als u deze wilt bijwerken?

Dat is afhankelijk van wat u moet bijwerken:

**U wilt de invoer of uitvoer wijzigen of u wilt de manier wijzigen waarop de webservice gegevens manipuleert**

Als u het model niet wijzigt, maar alleen de manier wijzigt waarop de webservice gegevens verwerkt, kunt u het voorspellende experiment bewerken en vervolgens op **webservice implementeren** klikken en vervolgens web service implementeren **[klassiek]** of **Web service [New] opnieuw implementeren** . De webservice is gestopt, het bijgewerkte voorspellende experiment wordt geïmplementeerd en de webservice wordt opnieuw gestart.

Hier volgt een voor beeld: Stel dat uw voorspellende experiment de volledige rij met invoer gegevens retourneert met het voorspelde resultaat. U kunt ervoor kiezen om alleen het resultaat van de webservice te retour neren. U kunt dus een module **project kolommen** toevoegen in het voorspellende experiment, rechts voor de uitvoer poort, om andere kolommen dan het resultaat uit te sluiten. Wanneer u op **webservice implementeren** klikt en vervolgens de optie **webservice [klassiek]** of **Web-service implementeren [Nieuw]** opnieuw, wordt de webservice bijgewerkt.

**U wilt het model opnieuw trainen met nieuwe gegevens**

Als u uw machine learning model wilt blijven gebruiken, maar u het opnieuw wilt trainen met nieuwe gegevens, hebt u twee opties:

1. **Het model opnieuw trainen tijdens het uitvoeren van de webservice** : als u uw model opnieuw wilt trainen terwijl de voorspellende webservice wordt uitgevoerd, kunt u dit doen door een aantal wijzigingen aan te brengen in het trainings experiment om het opnieuw te trainen **_experiment_*_, en vervolgens kunt u dit implementeren als een _*_retraining-webservice_**. Zie [machine learning-modellen programmatisch opnieuw trainen](./retrain-machine-learning-model.md)voor instructies over hoe u dit doet.
2. **Ga terug naar het oorspronkelijke trainings experiment en gebruik verschillende trainings gegevens voor het ontwikkelen van uw model** -uw voorspellende experiment is gekoppeld aan de webservice, maar het trainings experiment is op deze manier niet rechtstreeks gekoppeld. Als u het oorspronkelijke opleidings experiment wijzigt en op **webservice instellen** klikt, wordt er een *Nieuw*     voorspellend experiment gemaakt, dat tijdens de implementatie een *nieuwe* webservice maakt. De oorspronkelijke webservice wordt niet alleen bijgewerkt.

   Als u het trainings experiment wilt wijzigen, opent u het en klikt u op **Opslaan als** om een kopie te maken. Hierdoor blijft het oorspronkelijke trainings experiment, het voorspellende experiment en de webservice intact. U kunt nu een nieuwe webservice maken met uw wijzigingen. Wanneer u de nieuwe webservice hebt geïmplementeerd, kunt u vervolgens beslissen of u de vorige webservice wilt stoppen of dat u naast het nieuwe web service moet worden uitgevoerd.

**U wilt een ander model trainen**

Als u wijzigingen wilt aanbrengen in uw oorspronkelijke voorspellende experiment, zoals het selecteren van een andere machine learning-algoritme, het uitvoeren van een andere Trainings methode, enzovoort, moet u de tweede hierboven beschreven procedure volgen voor het opnieuw trainen van uw model: Open het trainings experiment, klik op **Opslaan als** om een kopie te maken en start vervolgens het nieuwe pad van het ontwikkelen van uw model, het maken van het voorspellende experiment en het implementeren van de webservice. Hiermee maakt u een nieuwe webservice die niet gerelateerd is aan de oorspronkelijke, en kunt u bepalen welke service of beide er moet worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over het proces van ontwikkelen en experimenteren:

* het experiment converteren: de [voor bereiding van uw model voor implementatie in azure machine learning Studio (klassiek)](deploy-a-machine-learning-web-service.md)
* de webservice implementeren- [een Azure machine learning-webservice implementeren](deploy-a-machine-learning-web-service.md)
* het model opnieuw trainen [machine learning modellen via een programma](./retrain-machine-learning-model.md)

Zie voor voor beelden van het hele proces:

* [Machine learning-zelf studie: uw eerste experiment maken in Azure Machine Learning Studio (klassiek)](create-experiment.md)
* [Walkthrough: een predictive analytics-oplossing ontwikkelen voor de beoordeling van een krediet risico in Azure Machine Learning](tutorial-part1-credit-risk.md)