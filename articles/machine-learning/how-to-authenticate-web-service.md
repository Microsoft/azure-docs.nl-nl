---
title: Verificatie configureren voor modellen die als webservices zijn geïmplementeerd
titleSuffix: Azure Machine Learning
description: Meer informatie over het configureren van verificatie voor machine learning modellen die zijn geïmplementeerd op webservices in Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94447649"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Verificatie configureren voor modellen die als webservices zijn geïmplementeerd

Met Azure Machine Learning kunt u uw getrainde machine learning-modellen als webservices implementeren. In dit artikel vindt u informatie over het configureren van verificatie voor deze implementaties.

De model implementaties die door Azure Machine Learning zijn gemaakt, kunnen worden geconfigureerd voor het gebruik van een van de volgende twee verificatie methoden:

* **op basis van sleutels**: een statische sleutel wordt gebruikt om te verifiëren bij de webservice.
* **op basis** van een token: er moet een tijdelijk token worden verkregen uit de Azure machine learning-werk ruimte (met behulp van Azure Active Directory) en worden gebruikt voor verificatie bij de webservice. Dit token verloopt na een bepaalde tijd en moet worden vernieuwd om te blijven werken met de webservice.

    > [!NOTE]
    > Verificatie op basis van tokens is alleen beschikbaar bij het implementeren naar de Azure Kubernetes-service.

## <a name="key-based-authentication"></a>Verificatie op basis van sleutels

Op de web-services die zijn geïmplementeerd op Azure Kubernetes service (AKS) is standaard op basis van sleutel verificatie *ingeschakeld* .

Op basis van Azure Container Instances (ACI) geïmplementeerde Services hebben op sleutel gebaseerde verificatie standaard *uitgeschakeld* , maar u kunt deze inschakelen door in te stellen `auth_enabled=True` bij het maken van de ACI-webservice. De volgende code is een voor beeld van het maken van een ACI-implementatie configuratie met ingeschakelde authenticatie op basis van een sleutel.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Vervolgens kunt u de aangepaste ACI-configuratie in de implementatie gebruiken met behulp van de- `Model` klasse.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Gebruik om de verificatie sleutels op te halen `aci_service.get_keys()` . Als u een sleutel opnieuw wilt genereren, gebruikt u de `regen_key()` functie en geeft u **primair** of **secundair** door.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Verificatie op basis van tokens

Wanneer u token verificatie inschakelt voor een webservice, moeten gebruikers een Azure Machine Learning JSON Web Token aan de webservice aanbieden om deze te openen. Het token verloopt na bepaalde tijd, en moet worden vernieuwd om aanroepen te kunnen blijven doen.

* Token verificatie is **standaard uitgeschakeld** wanneer u implementeert in azure Kubernetes service.
* Verificatie van tokens **wordt niet ondersteund** wanneer u implementeert in azure container instances.
* Token verificatie **kan niet tegelijkertijd worden gebruikt als verificatie op basis van een sleutel**.

Als u de verificatie van tokens wilt beheren, gebruikt `token_auth_enabled` u de para meter bij het maken of bijwerken van een implementatie:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Als token verificatie is ingeschakeld, kunt u de `get_token` methode gebruiken om een JSON Web token (JWT) op te halen en de verval tijd van dat token:

> [!TIP]
> Als u een Service-Principal gebruikt om het token op te halen en de minimale vereiste toegang wilt hebben om een token op te halen, wijst u deze toe aan de rol van **lezer** voor de werk ruimte.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet een nieuw token aanvragen nadat de `refresh_by`-tijd van het token is verstreken. Als u tokens wilt vernieuwen buiten de python-SDK, kunt u het beste de REST API met Service-Principal-verificatie gebruiken om de oproep periodiek `service.get_token()` uit te voeren, zoals eerder is besproken.
>
> We raden u ten zeerste aan om uw Azure Machine Learning-werk ruimte te maken in dezelfde regio als uw Azure Kubernetes service-cluster.
>
> Als u wilt verifiëren met een token, wordt er door de webservice een aanroep naar de regio waarin uw Azure Machine Learning-werk ruimte is gemaakt. Als uw werkruimte regio niet beschikbaar is, kunt u geen Token ophalen voor uw webservice, zelfs als uw cluster zich in een andere regio bevindt dan uw werk ruimte. Het resultaat is dat Azure AD-verificatie pas beschikbaar is als de werkruimte regio weer beschikbaar is.
>
> Hoe groter de afstand tussen de regio van uw cluster en de regio van uw werk ruimte, hoe langer het duurt om een token op te halen.

## <a name="next-steps"></a>Volgende stappen

Zie [een client maken voor een model dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)voor meer informatie over het verifiëren van een geïmplementeerd model.