---
title: Host-besturingssysteem upgraden voor rekencluster en exemplaar
titleSuffix: Azure Machine Learning
description: Upgrade het host-besturingssysteem voor het berekeningscluster en reken-exemplaar van Ubuntu 16.04 LTS naar 18.04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: how-to
ms.openlocfilehash: 8f2264cbe316f9be32b74e6d26169da4038d9e28
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885880"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Besturingssysteem van reken-exemplaar en berekeningsclusterhost upgraden

Azure Machine Learning __rekencluster en__ __rekenin exemplaar zijn__ beheerde rekeninfrastructuur. Als beheerde service beheert Microsoft het hostbesturingssysteem en de pakketten en softwareversies die zijn geïnstalleerd.

Het host-besturingssysteem voor het berekeningscluster en reken-exemplaar is Ubuntu 16.04 LTS. Op **30 april 2021** stopt Ubuntu de ondersteuning voor 16.04. Vanaf __15 maart 2021__ werkt Microsoft het host-besturingssysteem automatisch bij naar Ubuntu 18.04 LTS. Bijwerken naar 18.04 zorgt voor voortdurende beveiligingsupdates en ondersteuning van de Ubuntu-community. Deze update wordt uitgerold in Azure-regio's en is beschikbaar in alle regio's vanaf 9 april __2021.__ Zie de [Ubuntu-releaseblog](https://wiki.ubuntu.com/Releases)voor meer informatie over het beëindigen van de ondersteuning voor Ubuntu voor 16.04.

> [!TIP]
> * Het host-besturingssysteem is niet de versie van het besturingssysteem die u voor een omgeving kunt [opgeven bij](how-to-use-environments.md) het trainen of implementeren van een model. Omgevingen worden uitgevoerd in Docker. Docker wordt uitgevoerd op het host-besturingssysteem.
> * Als u momenteel op Ubuntu 16.04 gebaseerde omgevingen gebruikt voor training of implementatie, wordt u aangeraden over te schakelen naar het gebruik van op Ubuntu 18.04 gebaseerde installatie afbeeldingen. Zie Omgevingen gebruiken en de opslagplaats [Azure Machine Learning](how-to-use-environments.md) [containers voor meer informatie.](https://github.com/Azure/AzureML-Containers/tree/master/base)
> * Wanneer u een Azure Machine Learning compute-exemplaar op basis van Ubuntu 18.04 gebruikt, is _python 3.8_ de standaard python-versie.
## <a name="creating-new-resources"></a>Nieuwe resources maken

Rekencluster of reken-exemplaren die zijn gemaakt na 9 april __2021 gebruiken__ standaard Ubuntu 18.04 LTS als hostbesturingssysteem. U kunt geen ander host-besturingssysteem selecteren.

## <a name="upgrade-existing-resources"></a>Bestaande resources upgraden

Als u bestaande rekenclusters of reken-exemplaren hebt gemaakt vóór __15 maart 2021,__ moet u actie ondernemen om het host-besturingssysteem bij te werken naar Ubuntu 18.04. Afhankelijk van de regio waar u toegang Azure Machine Learning, raden we u aan deze acties na 9 april __2021__ uit te voeren om ervoor te zorgen dat onze wijzigingen in alle regio's zijn doorgevoerd:

* __Azure Machine Learning rekencluster:__

    * Als het cluster is geconfigureerd met min. knooppunten __= 0,__ wordt het automatisch bijgewerkt wanneer alle taken zijn voltooid en wordt het beperkt tot nul knooppunten.
    * Als __minimale knooppunten > 0,__ wijzigt u de minimale knooppunten tijdelijk in nul en staat u toe dat het cluster wordt beperkt tot nul knooppunten.

    Zie de Azure CLI-opdracht [az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ml/computetarget/update#az_ml_computetarget_update_amlcompute) of de SDK-verwijzing [AmlCompute.update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) voor meer informatie over het wijzigen van de minimale knooppunten.

* __Azure Machine Learning compute-exemplaar:__ maak een nieuw reken-exemplaar (dat gebruik gaat maken van Ubuntu 18.04) en verwijder het oude exemplaar.

    * Elk notebook dat is opgeslagen in de bestands share van de werkruimte, gegevensopslag, van gegevenssets, is toegankelijk vanaf de nieuwe reken-instantie.
    * Als u aangepaste Conda-omgevingen hebt gemaakt, kunt u deze omgevingen exporteren vanuit het bestaande exemplaar en importeren in het nieuwe exemplaar. Zie [Conda-documentatie](https://docs.conda.io/) op de docs.conda.io voor meer informatie over het exporteren en importeren docs.conda.io.

    Zie de artikelen Wat is een [reken-exemplaar en](concept-compute-instance.md) Een reken-Azure Machine Learning maken en beheren [voor meer](how-to-create-manage-compute-instance.md) informatie

## <a name="check-host-os-version"></a>Controleer de versie van het host-besturingssysteem

Zie de wikipagina van de Ubuntu-community over het controleren van uw Ubuntu-versie voor meer informatie over het controleren van de versie van het [host-besturingssysteem.](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)

> [!TIP]
> Als u de opdracht `lsb_release -a` uit de wiki wilt gebruiken, kunt u een [terminalsessie op een reken-exemplaar gebruiken.](how-to-access-terminal.md)
## <a name="next-steps"></a>Volgende stappen

Als u meer vragen of vragen hebt, kunt u contact met ons opnemen via [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
