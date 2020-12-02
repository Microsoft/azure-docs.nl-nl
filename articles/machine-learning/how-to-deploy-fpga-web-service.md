---
title: ML-modellen implementeren op Fpga's
titleSuffix: Azure Machine Learning
description: Meer informatie over Field-Programmeer bare poort matrices. U kunt een webservice implementeren op een FPGA met Azure Machine Learning voor een extreem lage latentie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, devx-track-python, deploy
ms.openlocfilehash: e2bb133997ec7b7d5ee3b8b82ec3179460596eeb
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511097"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>ML-modellen implementeren op veld-Programmeer bare poort matrices (Fpga's) met Azure Machine Learning 

In dit artikel leert u meer over Fpga's en hoe u uw ML-modellen implementeert in een Azure-FPGA met behulp van het [python-pakket met hardware-versnelde modellen](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py) van [Azure machine learning](overview-what-is-azure-ml.md).

## <a name="what-are-fpgas"></a>Wat zijn Fpga's?
FPGA's bevatten een matrix van programmeerbare logische blokken en een hiërarchie van herconfigureerbare interconnects. Met de onderlinge verbindingen kunnen deze blokken na de productie op verschillende manieren worden geconfigureerd. Vergeleken met andere chips, biedt Fpga's een combi natie van programmeer baarheid en prestaties. 

Fpga's maken het mogelijk om lage latentie te bieden voor aanvragen voor realtime-interferentie (of model scores). Asynchrone aanvragen (batch verwerking) zijn niet nodig. Batch verwerking kan latentie veroorzaken, omdat er meer gegevens moeten worden verwerkt. Implementaties van Neural-verwerkings eenheden vereisen geen batch verwerking; de latentie kan daarom veel keer lager zijn, vergeleken met CPU-en GPU-processors.

U kunt Fpga's opnieuw configureren voor verschillende soorten machine learning modellen. Deze flexibiliteit maakt het eenvoudiger om de toepassingen te versnellen op basis van de meest optimale numerieke precisie en het gebruikte geheugen model dat wordt gebruikt. Omdat Fpga's opnieuw worden geconfigureerd, kunt u op de hoogte blijven van de vereisten van het snel wijzigen van AI-algoritmen.

![Diagram van Azure Machine Learning FPGA-vergelijking](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processor| Afkorting |Beschrijving|
|---|:-------:|------|
|Toepassingsspecifieke geïntegreerde circuits|ASICs|Aangepaste circuits, zoals Google tensor processor units (TPU), bieden de hoogste efficiëntie. Ze kunnen niet opnieuw worden geconfigureerd als uw behoeften veranderen.|
|Veld-Programmeer bare poort matrices|FPGAs|Fpga's, zoals die beschikbaar zijn op Azure, bieden de prestaties bijna ASICs. Ze zijn ook flexibel en kunnen na verloop van tijd opnieuw worden geconfigureerd om nieuwe logica te implementeren.|
|Grafische verwerkings eenheden|GPU's|Een populaire keuze voor AI-berekeningen. Gpu's bieden parallelle verwerkings mogelijkheden, waardoor het sneller wordt weer gegeven in de beeld weergave dan Cpu's.|
|Centrale verwerkings eenheden|CPU's|Processors voor algemeen gebruik, de prestaties die niet ideaal zijn voor grafische en video verwerking.|

## <a name="fpga-support-in-azure"></a>Ondersteuning voor FPGA in azure

Microsoft Azure is de grootste Cloud investering wereld wijd in Fpga's. Micro soft maakt gebruik van Fpga's voor de evaluatie van diepe Neural Networks (DNN), zoek volgorde van Bing en SDN (software defined Networking) om de latentie te verminderen, terwijl Cpu's worden vrijgemaakt voor andere taken.

Fpga's op Azure zijn gebaseerd op de FPGA-apparaten van Intel, die gegevens wetenschappers en ontwikkel aars gebruiken om real-time AI-berekeningen te versnellen. Deze architectuur met FPGA biedt prestaties, flexibiliteit en schaal baarheid en is beschikbaar in Azure.

Azure Fpga's zijn geïntegreerd met Azure Machine Learning. Azure kan vooraf getrainde DNN over Fpga's parallelliseren om uw service uit te breiden. De DNNs kan vooraf worden getraind als een diep gaande featurizer voor het leren van de overdracht of nauw keurig zijn afgestemd op bijgewerkte gewichten.

|Scenario's & configuraties op Azure|Ondersteunde DNN modellen|Regionale ondersteuning|
|--------------------------|--------------------|----------------|
|+ Afbeeldings classificatie en herkennings scenario's<br/>+ Tensor flow-implementatie (vereist tensor flow 1. x)<br/>+ Intel FPGA-hardware|-ResNet 50<br/>-ResNet 152<br/>-DenseNet-121<br/>-VGG-16<br/>-SSD-VGG|-VS-Oost<br/>-Zuidoost-Azië<br/>-Europa-west<br/>-VS-West 2|

Om de latentie en door voer te optimaliseren, moet uw client gegevens verzenden naar het FPGA-model in een van de bovenstaande regio's (het model dat u hebt geïmplementeerd.)

De **PBS-serie van Azure-vm's** bevat Intel Arria 10 fpga's. Het wordt weer gegeven als ' standaard-PBS-serie Vcpu's ' wanneer u uw Azure-quotum toewijzing controleert. De PB6-VM heeft zes Vcpu's en één FPGA. PB6 VM wordt automatisch ingericht door Azure Machine Learning tijdens het implementeren van een model naar een FPGA. Het wordt alleen gebruikt in combi natie met Azure ML en er kan geen wille keurige bitstreams worden uitgevoerd. U kunt de FPGA bijvoorbeeld niet flashen met bitstreams om versleuteling, code ring, enzovoort uit te voeren.

## <a name="deploy-models-on-fpgas"></a>Modellen implementeren op Fpga's

U kunt een model als een webservice implementeren op Fpga's met [Azure Machine Learning modellen met hardwareversnelling](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py). Het gebruik van Fpga's biedt een dering van ultra lage latentie, zelfs met één batch grootte. 

In dit voor beeld maakt u een tensor flow-grafiek om de invoer installatie kopie voor te verwerken, een featurizer te maken met ResNet 50 op een FPGA en vervolgens de functies uit te voeren via een getrainde classificatie voor de ImageNet gegevensset. Vervolgens wordt het model geïmplementeerd naar een AKS-cluster.

### <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u er nog geen hebt, maakt u een [betalen per gebruik](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) -account (gratis Azure-accounts komen niet in aanmerking voor FPGA-quotum).

- Er is een Azure Machine Learning-werk ruimte en de Azure Machine Learning SDK voor python geïnstalleerd, zoals beschreven in [een werk ruimte maken](how-to-manage-workspace.md).
 
- Het pakket met hardware-versnelde modellen:  `pip install --upgrade azureml-accel-models[cpu]`    
    
- De [Azure cli](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)

- FPGA quota. Dien een [quotum aanvraag](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)in of voer deze cli-opdracht uit om het quotum te controleren: 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   Zorg ervoor dat er ten minste 6 Vcpu's zijn onder de __CurrentValue__ geretourneerd.  

### <a name="define-the-tensorflow-model"></a>Het tensor flow-model definiëren

Begin met het maken van een service definitie met behulp van de [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) . Een service definitie is een bestand met een beschrijving van een pijp lijn van grafieken (invoer, featurizer en classificatie) op basis van tensor flow. De implementatie opdracht comprimeert de definitie en grafieken in een ZIP-bestand en uploadt de ZIP naar Azure Blob-opslag. De DNN is al geïmplementeerd om te worden uitgevoerd op de FPGA.

1. Azure Machine Learning werk ruimte laden

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Afbeelding voorverwerken. De invoer voor de webservice is een JPEG-afbeelding.  De eerste stap is het decoderen van de JPEG-afbeelding en de voor verwerking ervan.  De JPEG-afbeeldingen worden behandeld als teken reeksen en het resultaat zijn tien tallen die de invoer zijn van het ResNet 50-model.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Featurizer laden. Initialiseer het model en down load een tensor flow-controle punt van de gevormde versie van ResNet50 die moet worden gebruikt als een featurizer.  Vervang "QuantizedResnet50" in het code fragment om andere diepe Neural-netwerken te importeren:

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. Voeg een classificatie toe. Deze classificatie is getraind op de ImageNet-gegevensset.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Sla het model op. Nu de preprocessor, ResNet 50 featurizer en de classificatie zijn geladen, slaat u de grafiek en de bijbehorende variabelen op als een model.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. Sla invoer-en uitvoer intensiteiten **op als u ze wilt gebruiken voor model conversie en aanvragen voor inschakeling**. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   De volgende modellen zijn beschikbaar met hun classificatie-uitvoer intensiteiten voor demijnen als u de standaard classificatie hebt gebruikt.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>Converteer het model naar de open Neural Network Exchange-indeling (ONNX)

Voordat u kunt implementeren in Fpga's, moet u het model converteren naar de [ONNX](https://onnx.ai/) -indeling.

1. [Registreer](concept-model-management-and-deployment.md) het model met behulp van de SDK met het zip-bestand in Azure Blob Storage. Door labels en andere meta gegevens over het model toe te voegen, kunt u uw getrainde modellen volgen.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Als u al een model hebt geregistreerd en wilt laden, kunt u het ophalen.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Converteer de tensor flow-grafiek naar de ONNX-indeling.  U moet de namen van de invoer-en uitvoer-tien tallen opgeven, zodat uw client deze kan gebruiken wanneer u de webservice verbruikt.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

### <a name="containerize-and-deploy-the-model"></a>Het model container plaatsen en implementeren

Maak vervolgens een docker-installatie kopie op basis van het geconverteerde model en alle afhankelijkheden.  Deze docker-installatie kopie kan vervolgens worden geïmplementeerd en geïnstantieerd.  Ondersteunde implementatie doelen zijn Azure Kubernetes service (AKS) in de Cloud of een edge-apparaat, zoals [Azure data Box Edge](../databox-online/azure-stack-edge-overview.md).  U kunt ook Tags en beschrijvingen toevoegen voor uw geregistreerde docker-installatie kopie.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   Vermeld de installatie kopieën op label en ontvang de gedetailleerde logboeken voor elke fout opsporing.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Implementeren in een Azure Kubernetes service-cluster

1. Gebruik AKS om uw model te implementeren als een grootschalige productie-webservice. U kunt een nieuw abonnement maken met behulp van de Azure Machine Learning SDK, CLI of [Azure machine learning Studio](https://ml.azure.com).

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    De implementatie van de AKS kan ongeveer 15 minuten duren.  Controleer of de implementatie is geslaagd.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Implementeer de container in het AKS-cluster.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

#### <a name="deploy-to-a-local-edge-server"></a>Implementeren op een lokale Edge-Server

Alle [Azure data Box edge-apparaten](../databox-online/azure-stack-edge-overview.md
) bevatten een FPGA voor het uitvoeren van het model.  Er kan slechts één model tegelijk op de FPGA worden uitgevoerd.  Als u een ander model wilt uitvoeren, implementeert u gewoon een nieuwe container. Instructies en voorbeeld code vindt u in [Dit Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models)-voor beeld.

### <a name="consume-the-deployed-model"></a>Het geïmplementeerde model gebruiken

Gebruik ten slotte de voor beeld-client om aan te roepen van de docker-installatie kopie om voor spellingen van het model te verkrijgen.  Voor beeld-client code is beschikbaar:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

De docker-installatie kopie ondersteunt gRPC en de tensor flow voor ' voors pellen '.

U kunt ook een voor beeld-client downloaden voor tensor flow.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Omdat deze classificatie is getraind op de [ImageNet](http://www.image-net.org/) -gegevensset, wijst u de klassen toe aan mensen met lees bare labels.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-resources"></a>Resources opschonen

Om onnodige kosten te voor komen, moet u uw resources opschonen **in deze volg orde**: webservice, vervolgens afbeelding, en vervolgens het model.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over het [beveiligen van uw webservices](how-to-secure-web-service.md) -document.

+ Meer informatie over de [prijzen en kosten](https://azure.microsoft.com/pricing/details/machine-learning/)voor FPGA en Azure machine learning.

+ [Grootschalige-hardware: ML op schaal boven op Azure + FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Configureer bare FPGA-Cloud (video) op basis van micro soft](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project brainwave voor real-time AI](https://www.microsoft.com/research/project/project-brainwave/)

+ [Systeem voor geautomatiseerde optische inspectie](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
