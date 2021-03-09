---
title: 'Python-script uitvoeren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de script module python uitvoeren in Azure Machine Learning Designer voor het uitvoeren van python-code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 01/02/2021
ms.openlocfilehash: 6003ca9156d8553604d7ebbf94c5c3373d077f0f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500696"
---
# <a name="execute-python-script-module"></a>Python-script module uitvoeren

In dit artikel wordt de script module voor het uitvoeren van python in Azure Machine Learning Designer beschreven.

Gebruik deze module om python-code uit te voeren. Zie voor meer informatie over de architectuur en ontwerp principes van python de [procedures voor het uitvoeren van python-code in azure machine learning Designer](../how-to-designer-python.md).

Met python kunt u taken uitvoeren die niet worden ondersteund door bestaande modules, zoals:

+ Het visualiseren van gegevens met behulp van `matplotlib` .
+ Python-bibliotheken gebruiken voor het opsommen van gegevens sets en modellen in uw werk ruimte.
+ Gegevens lezen, laden en bewerken vanuit bronnen die niet worden ondersteund door de module [gegevens importeren](./import-data.md) .
+ Voer uw eigen diepe leer code uit. 

## <a name="supported-python-packages"></a>Ondersteunde Python-pakketten

Azure Machine Learning maakt gebruik van de Anaconda-distributie van python, met veel algemene hulpprogram ma's voor gegevens verwerking. De Anaconda-versie wordt automatisch bijgewerkt. De huidige versie is:
 -  Anaconda 4.5 + Distribution voor python 3,6 

Zie de sectie [vooraf geïnstalleerde Python-pakketten](#preinstalled-python-packages)voor een volledige lijst.

Als u pakketten wilt installeren die zich niet in de vooraf geïnstalleerde lijst bevinden (bijvoorbeeld *scikit-misc*), voegt u de volgende code toe aan het script: 

```python
import os
os.system(f"pip install scikit-misc")
```

Gebruik de volgende code om pakketten te installeren voor betere prestaties, met name voor demijnen:
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> Als uw pijp lijn meerdere python-script modules voor uitvoeren bevat waarvoor pakketten nodig zijn die zich niet in de vooraf geïnstalleerde lijst bevinden, installeert u de pakketten in elke module.

> [!WARNING]
> De excute python-script module biedt geen ondersteuning voor het installeren van pakketten die afhankelijk zijn van extra systeem eigen bibliotheken met opdracht als ' apt-get ', zoals Java, PyODBC en etc. Dit is omdat deze module wordt uitgevoerd in een eenvoudige omgeving met python vooraf geïnstalleerd en met niet-beheerders machtigingen.  

## <a name="access-to-current-workspace-and-registered-datasets"></a>Toegang tot de huidige werk ruimte en geregistreerde gegevens sets

U kunt de volgende voorbeeld code gebruiken om toegang te krijgen tot de [geregistreerde gegevens sets](../how-to-create-register-datasets.md) in uw werk ruimte:

```Python
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    #access to current workspace
    ws = run.experiment.workspace

    #access to registered dataset of current workspace
    from azureml.core import Dataset
    dataset = Dataset.get_by_name(ws, name='test-register-tabular-in-designer')
    dataframe1 = dataset.to_pandas_dataframe()
     
    # If a zip file is connected to the third input port,
    # it is unzipped under "./Script Bundle". This directory is added
    # to sys.path. Therefore, if your zip file contains a Python file
    # mymodule.py you can import it using:
    # import mymodule

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
```

## <a name="upload-files"></a>Bestanden uploaden
De script module voor het uitvoeren van python ondersteunt het uploaden van bestanden met behulp van de [Azure machine learning PYTHON SDK](/python/api/azureml-core/azureml.core.run%28class%29#upload-file-name--path-or-stream-).

In het volgende voor beeld ziet u hoe u een afbeeldings bestand uploadt in de script module voor het uitvoeren van python:

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Nadat de pijplijn uitvoering is voltooid, kunt u een voor beeld van de afbeelding bekijken in het rechterdeel venster van de module.

> [!div class="mx-imgBorder"]
> ![Voor beeld van geüploade afbeelding](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Het configureren van een script voor het uitvoeren van python

De script module python uitvoeren bevat een voor beeld van python-code die u als uitgangs punt kunt gebruiken. Als u de script module voor het uitvoeren van python wilt configureren, geeft u een set invoer-en Python-code op die in het tekstvak **python-script** wordt uitgevoerd.

1. Voeg de **script module python uitvoeren** toe aan de pijp lijn.

2. Voeg gegevens sets toe aan **Dataset1** uit de ontwerp functie die u voor invoer wilt gebruiken en maak er verbinding mee. Verwijs naar deze gegevensset in uw python-script als **DataFrame1**.

    Het gebruik van een gegevensset is optioneel. Gebruik deze functie als u gegevens wilt genereren met behulp van python of python-code wilt gebruiken om de gegevens rechtstreeks in de module te importeren.

    Deze module biedt ondersteuning voor het toevoegen van een tweede gegevensset op **Dataset2**. Raadpleeg de tweede gegevensset in uw python-script als **DataFrame2**.

    Gegevens sets die zijn opgeslagen in Azure Machine Learning worden automatisch geconverteerd naar Panda data-frames wanneer deze met deze module worden geladen.

    ![Python-invoer toewijzing uitvoeren](media/module/python-module.png)

4. Als u nieuwe Python-pakketten of-code wilt toevoegen, verbindt u het zip-bestand dat deze aangepaste bronnen bevat, om de poort te **bundelen** . Als uw script groter is dan 16 KB, gebruikt u de **script bundel** poort om fouten te voor komen, zoals *commandline de limiet van 16597 tekens overschrijdt*. 

    
    1. Het script en andere aangepaste resources bundelen naar een zip-bestand.
    1. Upload het zip-bestand als een **bestand gegevensset** naar de Studio. 
    1. Sleep de module gegevensset van de lijst *gegevens sets* in het deel venster met de linker module op de ontwerp pagina voor ontwerpen. 
    1. Verbind de module dataset met de script **bundel** poort van de script module voor het **uitvoeren van python** .
    
    Elk bestand in het geüploade gezipte archief kan worden gebruikt tijdens de uitvoering van de pijp lijn. Als het archief een mapstructuur bevat, blijft de structuur behouden.
 
    > [!WARNING]
    > Gebruik **app** **niet** als de naam van de map of uw script, omdat de **app** een gereserveerd woord is voor ingebouwde services. Maar u kunt ook andere naam ruimten gebruiken, zoals `app123` .
   
    Hieronder ziet u een voor beeld van een script bundel, dat een python-script bestand en een txt-bestand bevat:
      
    > [!div class="mx-imgBorder"]
    > ![Voor beeld van script bundel](media/module/python-script-bundle.png)  

    Hieronder vindt u de inhoud van `my_script.py` :

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    Hieronder ziet u voorbeeld code waarin wordt getoond hoe de bestanden in de script bundel worden gebruikt:    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. Typ of plak geldige python-script in het tekstvak **python-script** .

    > [!NOTE]
    >  Wees voorzichtig met het schrijven van uw script. Zorg ervoor dat er geen syntaxis fouten zijn, zoals het gebruik van niet-gedeclareerde variabelen of niet-geïmporteerde modules of functies. U kunt extra aandacht best Eden aan de vooraf geïnstalleerde module lijst. Als u modules wilt importeren die niet worden weer gegeven, installeert u de bijbehorende pakketten in uw script, zoals:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Het **python-script** tekstvak is vooraf ingevuld met enkele instructies in opmerkingen, en voorbeeld code voor gegevens toegang en-uitvoer. U moet deze code bewerken of vervangen. Python-conventies volgen voor inspringing en behuizing:

    + Het script moet een functie bevatten met `azureml_main` de naam van het toegangs punt voor deze module.
    + De functie voor het toegangs punt moet twee invoer argumenten hebben `Param<dataframe1>` en `Param<dataframe2>` , zelfs wanneer deze argumenten niet in uw script worden gebruikt.
    + Gezipte bestanden die zijn verbonden met de derde invoer poort, worden uitgepakt en opgeslagen in de map `.\Script Bundle` , die ook aan de python worden toegevoegd `sys.path` . 

    Als uw zip-bestand bevat `mymodule.py` , kunt u dit importeren met behulp van `import mymodule` .

    Er kunnen twee gegevens sets worden geretourneerd naar de ontwerper. dit moet een reeks van het type zijn `pandas.DataFrame` . U kunt andere uitvoer in uw Python-code maken en deze rechtstreeks naar Azure Storage schrijven.

    > [!WARNING]
    > Het wordt **niet** aanbevolen om verbinding te maken met een Data Base of andere externe opslag in de **script module** voor het uitvoeren van python. U kunt de [module gegevens importeren](./import-data.md) en de [module gegevens exporteren](./export-data.md) gebruiken     

6. Verzend de pijp lijn.

    Als de module is voltooid, controleert u de uitvoer indien dit wordt verwacht.

    Als de module is mislukt, moet u een aantal problemen oplossen. Selecteer de module en open **uitvoer en logboeken** in het rechterdeel venster. Open **70_driver_log.txt** en zoek **in azureml_main op** welke regel de fout is veroorzaakt. Bijvoorbeeld: ' file '/tmp/tmp01_ID/user_script. py ', line 17, in azureml_main ' geeft aan dat de fout is opgetreden in de 17-regel van uw python-script.

## <a name="results"></a>Resultaten

De resultaten van alle berekeningen door de Inge sloten python-code moeten worden geleverd als `pandas.DataFrame` , die automatisch wordt geconverteerd naar de indeling van de Azure machine learning-gegevensset. U kunt vervolgens de resultaten gebruiken met andere modules in de pijp lijn.

De module retourneert twee gegevens sets:  
  
+ De **resultaten gegevensset 1**, zoals gedefinieerd door het eerste geretourneerde Panda-gegevens frame in een python-script.

+ **Resulterende gegevensset 2**, gedefinieerd door het tweede geretourneerde gegevens frame van Panda, in een python-script.

## <a name="preinstalled-python-packages"></a>Vooraf geïnstalleerde Python-pakketten
De vooraf geïnstalleerde pakketten zijn:
-    adal = = 1.2.2
-    applicationinsights = = 0.11.9
-    attr = = 19.3.0
-    Azure-common = = 1.1.25
-    Azure-Core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure-Identity = = 1.3.0
-    Azure-beheer-autorisatie = = 0.60.0
-    Azure-beheer-containerregistry = = 2.8.0
-    Azure-beheer-sleutel kluis = = 2.2.0
-    Azure-beheer-resource = = 8.0.1
-    Azure-beheer-opslag = = 8.0.0
-    Azure-Storage-BLOB = = 1.5.0
-    Azure-opslag-common = = 1.4.2
-    azureml-Core = = 1.1.5.5
-    azureml-dataprep-native = = 14.1.0
-    azureml-dataprep = = 1.3.5
-    azureml-defaults = = 1.1.5.1
-    azureml-Designer-Classic-modules = = 0.0.118
-    azureml-Designer-core = = 0.0.31
-    azureml-Designer-Internal = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml-pijp lijn-Core = = 1.1.5
-    azureml-telemetrie = = 1.1.5.3
-    backports. tempfile = = 1.0
-    backports. weakref = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cache-werkset = = 4.0.0
-    Certificeer = = 2019.11.28
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    Klik = = 7.1.1
-    cloudpickle = = 1.3.0
-    configparser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    crypto grafie = = 2.8
-    Cycle = = 0.10.0
-    Dill = = 0.3.1.1
-    distributie = = 1.4.0
-    docker = = 4.2.0
-    docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    kolf = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-Core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-hervattingable-media = = 0.5.0
-    googleapis-algemeen-proto = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2,9
-    niet in evenwicht: meer informatie = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-logboek registratie-py = = 0,2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    Liac-ARFF = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    meer-ITER-extra = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    numpy = = 1.18.2
-    oauthlib = = 3.1.0
-    Pandas = = 0.25.3
-    pathspec = = 0.7.0
-    PIP = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modules = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    python-dateutil = = 2.8.1
-    pytz = = 2019.3
-    aanvragen-oauthlib = = 1.3.0
-    aanvragen = = 2.23.0
-    RSA = = 4.0
-    ruamel. yaml = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit-Learn = = 0.22.2
-    scipy = = 1.4.1
-    secretstorage = = 3.1.2
-    installatie-instellingen = = 46.1.1. post20200323
-    zes = = 1.14.0
-    Smart-open = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-client = = 0.57.0
-    werkzeug = = 0.16.1
-    wiel = = 0.34.2

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.