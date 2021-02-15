---
title: 'ML Studio (klassiek): para meters van de webservice-Azure'
description: Azure Machine Learning para meters van de webservice gebruiken om het gedrag van uw model te wijzigen wanneer de webservice wordt geopend.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio-classic
ms.workload: data-services
ms.topic: how-to
ms.date: 01/12/2017
ms.openlocfilehash: 49814d01209b58666c011a6bbd7fe6c328d460f8
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518331"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Para meters voor de webservice Azure Machine Learning Studio (klassiek) gebruiken

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Een Azure Machine Learning-webservice wordt gemaakt door een experiment te publiceren dat modules bevat met Configureer bare para meters. In sommige gevallen wilt u mogelijk het gedrag van de module wijzigen terwijl de webservice wordt uitgevoerd. Met de *para meters* van de webservice kunt u deze taak uitvoeren. 

Een voor beeld hiervan is het instellen van de module [import data][reader] , zodat de gebruiker van de gepubliceerde webservice een andere gegevens bron kan opgeven wanneer de webservice wordt geopend. Of configureer de module [gegevens exporteren][writer] zodat er een andere bestemming kan worden opgegeven. Enkele andere voor beelden zijn het wijzigen van het aantal bits voor de [functie-hash][feature-hashing] -module of het aantal gewenste functies voor de [functie selectie module op basis van filter][filter-based-feature-selection] . 

U kunt para meters voor de webservice instellen en deze koppelen aan een of meer module parameters in uw experiment, en u kunt opgeven of ze vereist of optioneel zijn. De gebruiker van de webservice kan vervolgens waarden opgeven voor deze para meters wanneer ze de webservice aanroepen. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Webservice-para meters instellen en gebruiken
U definieert een webservice-para meter door te klikken op het pictogram naast de para meter voor een module en de optie instellen als webservice-para meter. Hiermee maakt u een nieuwe para meter voor de webservice en verbindt u deze met de para meter van die module. Wanneer de webservice vervolgens wordt geopend, kan de gebruiker een waarde voor de para meter van de webservice opgeven en wordt deze toegepast op de para meter van de module.

Wanneer u een webservice-para meter definieert, is deze beschikbaar voor elke andere module parameter in het experiment. Als u een para meter voor een webservice definieert die is gekoppeld aan een para meter voor een module, kunt u dezelfde webservice-para meter voor een andere module gebruiken, mits de para meter hetzelfde type waarde verwacht. Als de para meter van de webservice bijvoorbeeld een numerieke waarde is, kan deze alleen worden gebruikt voor module parameters die een numerieke waarde verwachten. Wanneer de gebruiker een waarde voor de para meter van de webservice instelt, wordt deze toegepast op alle bijbehorende module parameters.

U kunt beslissen of u een standaard waarde wilt opgeven voor de para meter van de webservice. Als u dit wel doet, is de para meter optioneel voor de gebruiker van de webservice. Als u geen standaard waarde opgeeft, moet de gebruiker een waarde invoeren wanneer de webservice wordt geopend.

De API-documentatie voor de webservice bevat informatie voor de web service-gebruiker over het programmatisch opgeven van de para meter voor de webservice bij het openen van de webservice.

> [!NOTE]
> De API-documentatie voor een klassieke webservice wordt geleverd via de koppeling **API Help pagina** in het **dash board** van de webservice in machine learning Studio (klassiek). De API-documentatie voor een nieuwe webservice wordt geleverd via de [Azure machine learning](https://services.azureml.net/Quickstart) webservices Portal op de API-pagina's voor **gebruik en** **Swagger** voor uw webservice.
> 
> 

## <a name="example"></a>Voorbeeld
Als voor beeld gaan we ervan uit dat we een experiment hebben met een [export gegevens][writer] module die informatie naar Azure Blob-opslag verzendt. Er wordt een webservice-para meter met de naam ' BLOB path ' gedefinieerd waarmee de webservice-gebruiker het pad naar de Blob-opslag kan wijzigen wanneer de service wordt geopend.

1. Klik in Machine Learning Studio (klassiek) op de module [gegevens exporteren][writer] om deze te selecteren. De eigenschappen worden weer gegeven in het deel venster Eigenschappen rechts van het canvas voor het experiment.
2. Geef het opslag type op:
   
   * Selecteer onder **gegevens doel opgeven** de optie Azure Blob Storage.
   * Selecteer onder **verificatie type opgeven** de optie account.
   * Voer de account gegevens in voor de Azure Blob-opslag. 

3. Klik op het pictogram rechts van het **pad naar de blob die begint met de container parameter**. Deze ziet er als volgt uit:
   
   ![Pictogram van de webservice-para meter](./media/web-service-parameters/icon.png)
   
   Selecteer instellen als webservice-para meter.
   
   Er wordt een vermelding toegevoegd onder de **para meters** van de webservice onder aan het deel venster Eigenschappen met de naam ' pad naar blob, beginnend met container '. Dit is de para meter van de webservice die nu is gekoppeld aan deze module parameter voor [export gegevens][writer] .
4. Als u de naam van de para meter voor de webservice wilt wijzigen, klikt u op de naam, typt u "blob-pad" en drukt u op **Enter** . 
5. Als u een standaard waarde wilt opgeven voor de para meter van de webservice, klikt u op het pictogram rechts van de naam, selecteert u standaard waarde opgeven, geeft u een waarde op (bijvoorbeeld ' container1/output1.csv ') en drukt u op **Enter** .
   
   ![Webservice-para meter](./media/web-service-parameters/parameter.png)
6. Klik op **Run**. 
7. Klik op **Deploy web service** en selecteer **Deploy web service [Classic]** of **Deploy web service [New]** om de webservice te implementeren.

> [!NOTE] 
> Als u een nieuwe webservice wilt implementeren, moet u voldoende machtigingen hebben in het abonnement waarvoor u de webservice implementeert. Zie [Manage a web service using the Azure machine learning Web Services portal](manage-new-webservice.md)(Engelstalig) voor meer informatie. 

De gebruiker van de webservice kan nu een nieuwe bestemming opgeven voor de module [gegevens exporteren][writer] bij het openen van de webservice.

## <a name="more-information"></a>Meer informatie
Zie de vermelding [webservice-para meters](/archive/blogs/machinelearning/azureml-web-service-parameters) in het [machine learning blog](/archive/blogs/machinelearning/azureml-web-service-parameters)voor een gedetailleerd voor beeld.

Zie [How to verbruik a Azure machine learning web service](consume-web-services.md)(Engelstalig) voor meer informatie over het openen van een machine learning-webservice.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[reader]: /azure/machine-learning/studio-module-reference/import-data
[writer]: /azure/machine-learning/studio-module-reference/export-data