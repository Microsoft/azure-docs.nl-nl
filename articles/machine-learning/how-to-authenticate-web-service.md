---
title: Verificatie configureren voor modellen die zijn geïmplementeerd als webservices
titleSuffix: Azure Machine Learning
description: Meer informatie over het configureren van verificatie machine learning modellen die zijn geïmplementeerd in webservices in Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: how-to
ms.custom: how-to
ms.openlocfilehash: b97bdd8f060aa22ea7bc6ca4901586be6d09505e
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886060"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Verificatie configureren voor modellen die zijn geïmplementeerd als webservices

Azure Machine Learning kunt u uw getrainde modellen machine learning als webservices implementeren. In dit artikel leert u hoe u verificatie voor deze implementaties configureert.

De modelimplementaties die door Azure Machine Learning kunnen worden geconfigureerd voor het gebruik van een van de volgende twee verificatiemethoden:

* **op basis van een** sleutel: er wordt een statische sleutel gebruikt om te verifiëren bij de webservice.
* op basis van een token: er moet een tijdelijk token worden verkregen van de **Azure Machine Learning-werkruimte**(met behulp van Azure Active Directory) en worden gebruikt voor verificatie bij de webservice. Dit token verloopt na een bepaalde periode en moet worden vernieuwd om met de webservice te kunnen blijven werken.

    > [!NOTE]
    > Verificatie op basis van een token is alleen beschikbaar bij het implementeren naar Azure Kubernetes Service.

## <a name="key-based-authentication"></a>Verificatie op basis van sleutels

Voor webservices die zijn geïmplementeerd op Azure Kubernetes Service (AKS) is standaard een op sleutels gebaseerde *auth* ingeschakeld.

Azure Container Instances (ACI) geïmplementeerde services hebben standaard een op sleutels gebaseerde *auth* uitgeschakeld, maar u kunt dit inschakelen door in te stellen bij het maken van de `auth_enabled=True` ACI-webservice. De volgende code is een voorbeeld van het maken van een ACI-implementatieconfiguratie met op sleutels gebaseerde auth ingeschakeld.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Vervolgens kunt u de aangepaste ACI-configuratie in de implementatie gebruiken met behulp van de `Model` klasse .

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

Gebruik om de auth-sleutels op te `aci_service.get_keys()` halen. Als u een sleutel opnieuw wilt maken, gebruikt u de `regen_key()` functie en passeert u **primaire** of **secundaire** sleutel.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Verificatie op basis van tokens

Wanneer u tokenverificatie voor een webservice inschakelen, moeten gebruikers een Azure Machine Learning JSON Web Token aan de webservice om toegang te krijgen. Het token verloopt na bepaalde tijd, en moet worden vernieuwd om aanroepen te kunnen blijven doen.

* Tokenverificatie **is standaard uitgeschakeld wanneer** u implementeert in Azure Kubernetes Service.
* Tokenverificatie **wordt niet ondersteund wanneer** u implementeert in Azure Container Instances.
* Tokenverificatie kan niet op hetzelfde moment worden **gebruikt als verificatie op basis van sleutels.**

Als u de verificatie van het token wilt controleren, gebruikt u `token_auth_enabled` de parameter bij het maken of bijwerken van een implementatie:

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

Als tokenverificatie is ingeschakeld, kunt u de methode gebruiken om een JSON Web Token (JWT) en de verlooptijd van dat `get_token` token op te halen:

> [!TIP]
> Als u een service-principal gebruikt om het token op te halen en u wilt  dat het de minimaal vereiste toegang heeft om een token op te halen, wijst u dit toe aan de rol lezer voor de werkruimte.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet een nieuw token aanvragen nadat de `refresh_by`-tijd van het token is verstreken. Als u tokens buiten de Python-SDK wilt vernieuwen, kunt u de REST API met service-principal-verificatie gebruiken om periodiek de aanroep uit te voeren, zoals eerder `service.get_token()` besproken.
>
> We raden u ten zeerste aan om Azure Machine Learning werkruimte te maken in dezelfde regio als uw Azure Kubernetes Service cluster.
>
> Om te verifiëren met een token, roept de webservice de regio aan waarin uw Azure Machine Learning werkruimte is gemaakt. Als uw werkruimteregio niet beschikbaar is, kunt u geen token ophalen voor uw webservice, zelfs niet als uw cluster zich in een andere regio van uw werkruimte. Het resultaat is dat Azure AD-verificatie niet beschikbaar is totdat uw werkruimteregio weer beschikbaar is.
>
> Hoe groter de afstand tussen de regio van uw cluster en uw werkruimteregio, hoe langer het duurt om een token op te halen.

## <a name="next-steps"></a>Volgende stappen

Zie Create a client for a model deployed as a web service (Een client maken voor een model dat is geïmplementeerd [als een webservice)](how-to-consume-web-service.md)voor meer informatie over het authenticeren van een geïmplementeerd model.