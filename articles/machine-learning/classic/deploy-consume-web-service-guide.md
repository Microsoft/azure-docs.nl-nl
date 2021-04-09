---
title: 'ML Studio (klassiek): implementatie en verbruik-Azure'
description: U kunt Azure Machine Learning Studio (klassiek) gebruiken om machine learning werk stromen en modellen als webservices te implementeren. Deze webservices kunnen vervolgens worden gebruikt voor het aanroepen van de machine learning modellen van toepassingen via internet om voor spellingen in realtime of in batch modus uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 57c5462cd6eccab1d9c42e674e10ea34db3612b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520354"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Webservices Azure Machine Learning Studio (klassiek): implementatie en verbruik

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

U kunt Azure Machine Learning Studio (klassiek) gebruiken om machine learning werk stromen en modellen als webservices te implementeren. Deze webservices kunnen vervolgens worden gebruikt voor het aanroepen van de machine learning modellen van toepassingen via internet om voor spellingen in realtime of in batch modus uit te voeren. Omdat de webservices actief zijn, kunt u deze aanroepen vanuit verschillende programmeer talen en platformen, zoals .NET en Java, en vanuit toepassingen, zoals Excel.

De volgende secties bevatten koppelingen naar instructies, code en documentatie om aan de slag te gaan.

## <a name="deploy-a-web-service"></a>Een webservice implementeren

### <a name="with-azure-machine-learning-studio-classic"></a>Met Azure Machine Learning Studio (klassiek)

De Studio-Portal en de Microsoft Azure Machine Learning web services-Portal helpen u bij het implementeren en beheren van een webservice zonder code te schrijven.

De volgende koppelingen bevatten algemene informatie over het implementeren van een nieuwe webservice:

* Zie [een nieuwe webservice implementeren](deploy-a-machine-learning-web-service.md)voor een overzicht van het implementeren van een nieuwe webservice op basis van Azure Resource Manager.
* Zie [een Azure machine learning-webservice implementeren](deploy-a-machine-learning-web-service.md)voor een overzicht van het implementeren van een webservice.
* Voor een volledig overzicht van het maken en implementeren van een webservice, begint u met [zelf studie 1: krediet risico voors pellen](tutorial-part1-credit-risk.md).
* Zie voor specifieke voor beelden voor het implementeren van een webservice:

  * [Zelf studie 3: een model voor krediet Risico's implementeren](tutorial-part3-credit-risk-deploy.md)
  * [Een webservice implementeren in meerdere regio's](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Met Web Services resource provider Api's (Azure Resource Manager-Api's)

Met de resource provider Azure Machine Learning Studio (klassiek) voor webservices kunt u webservices implementeren en beheren met behulp van REST API-aanroepen. Zie voor meer informatie de referentie voor [machine learning-webservice (rest)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>Met Power shell-cmdlets

Met de resource provider Azure Machine Learning Studio (klassiek) voor webservices kunt u webservices implementeren en beheren met behulp van Power shell-cmdlets.

Als u de cmdlets wilt gebruiken, moet u zich eerst vanuit de Power shell-omgeving aanmelden bij uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Zie [Azure PowerShell gebruiken met Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md)als u niet bekend bent met het aanroepen van Power shell-opdrachten die zijn gebaseerd op Resource Manager.

Als u uw voorspellende experiment wilt exporteren, gebruikt u [deze voorbeeld code](https://github.com/ritwik20/AzureML-WebServices). Nadat u het exe-bestand van de code hebt gemaakt, kunt u het volgende typen:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

Als u de toepassing uitvoert, wordt een JSON-sjabloon voor de webservice gemaakt. Als u de sjabloon wilt gebruiken om een webservice te implementeren, moet u de volgende informatie toevoegen:

* Naam en sleutel van het opslag account

    U kunt de naam en sleutel van het opslag account ophalen uit de [Azure Portal](https://portal.azure.com/).
* Toezeggings plan-ID

    U kunt de plan-ID ophalen uit de [Azure machine learning Web Services](https://services.azureml.net) -portal door u aan te melden en te klikken op een naam van een abonnement.

Voeg ze toe aan de JSON-sjabloon als onderliggende elementen van het knoop punt *Eigenschappen* op hetzelfde niveau als het *MachineLearningWorkspace* -knoop punt.

Hier volgt een voorbeeld:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Raadpleeg de volgende artikelen en voorbeeld code voor aanvullende informatie:

* Naslag informatie voor [Azure machine learning Studio (klassieke)-cmdlets](/powershell/module/az.machinelearning) op MSDN

## <a name="consume-the-web-services"></a>De webservices gebruiken

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Vanuit de gebruikers interface van Azure Machine Learning-webservices (testen)

U kunt uw webservice testen vanuit de Azure Machine Learning-webservices Portal. Dit omvat het testen van de Request-Response service (RR'S) en BES-interfaces (batch Execution Service).

* [Een nieuwe webservice implementeren](deploy-a-machine-learning-web-service.md)
* [Een Azure Machine Learning-webservice implementeren](deploy-a-machine-learning-web-service.md)
* [Zelf studie 3: een model voor krediet Risico's implementeren](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Vanuit Excel

U kunt een Excel-sjabloon downloaden die de webservice gebruikt:

* [Een Azure Machine Learning-webservice gebruiken vanuit Excel](consuming-from-excel.md)
* [Excel-invoeg toepassing voor Azure Machine Learning-webservices](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Van een REST-gebaseerde client

Azure Machine Learning-webservices zijn REST Api's. U kunt deze Api's van verschillende platformen gebruiken, zoals .NET, Python, R, Java, enzovoort. De pagina **verbruik** voor uw webservice op de [Microsoft Azure machine learning Web Services-portal](https://services.azureml.net) bevat voorbeeld code waarmee u aan de slag kunt gaan. Zie [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Azure Machine Learning-webservice gebruiken) voor meer informatie.