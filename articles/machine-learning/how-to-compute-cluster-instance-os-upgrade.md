---
title: Host-besturingssysteem upgraden voor rekencluster en exemplaar
titleSuffix: Azure Machine Learning
description: Werk het host-besturingssysteem voor het rekencluster en reken exemplaar bij van Ubuntu 16.04 LTS naar 18.04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7445de8349d025679b1560e065ed15d9eec3b08f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872007"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Besturingssysteem van reken-exemplaar en rekenclusterhost upgraden

Azure Machine Learning __rekencluster en__ __rekenin exemplaar zijn__ beheerde rekeninfrastructuur. Als beheerde service beheert Microsoft het hostbesturingssysteem en de pakketten en softwareversies die zijn geïnstalleerd.

Het host-besturingssysteem voor het berekeningscluster en reken-exemplaar is Ubuntu 16.04 LTS. Op **30 april 2021** stopt Ubuntu de ondersteuning voor 16.04. Vanaf __15 maart 2021__ werkt Microsoft het host-besturingssysteem automatisch bij naar Ubuntu 18.04 LTS. Bijwerken naar 18.04 zorgt voor voortdurende beveiligingsupdates en ondersteuning van de Ubuntu-community. Deze update wordt uitgerold in Azure-regio's en is vanaf 9 april __2021__ beschikbaar in alle regio's. Zie de [Ubuntu-releaseblog](https://wiki.ubuntu.com/Releases)voor meer informatie over het beëindigen van de ondersteuning voor Ubuntu voor 16.04.

> [!TIP]
> * Het host-besturingssysteem is niet de versie van het besturingssysteem die u voor een omgeving [kunt opgeven bij](how-to-use-environments.md) het trainen of implementeren van een model. Omgevingen worden uitgevoerd in Docker. Docker wordt uitgevoerd op het host-besturingssysteem.
> * Als u momenteel op Ubuntu 16.04 gebaseerde omgevingen gebruikt voor training of implementatie, raadt Microsoft u aan over te schakelen naar het gebruik van op Ubuntu 18.04 gebaseerde installatie afbeeldingen. Zie Omgevingen gebruiken en de opslagplaats [Azure Machine Learning](how-to-use-environments.md) [containers voor meer informatie.](https://github.com/Azure/AzureML-Containers/tree/master/base)
> * Wanneer u een Azure Machine Learning compute-exemplaar op basis van Ubuntu 18.04 gebruikt, is _python 3.8 de standaardversie van Python._
## <a name="creating-new-resources"></a>Nieuwe resources maken

Rekencluster- of reken-exemplaren die zijn gemaakt na 9 april __2021 gebruiken__ standaard Ubuntu 18.04 LTS als hostbesturingssysteem. U kunt geen ander host-besturingssysteem selecteren.

## <a name="upgrade-existing-resources"></a>Bestaande resources upgraden

Als u bestaande rekenclusters of reken-exemplaren hebt gemaakt vóór __15 maart 2021,__ moet u actie ondernemen om het host-besturingssysteem te upgraden naar Ubuntu 18.04. Afhankelijk van de regio waar u toegang Azure Machine Learning, raden we u aan deze acties na 9 april __2021__ uit te voeren om ervoor te zorgen dat onze wijzigingen in alle regio's zijn doorgevoerd:

* __Azure Machine Learning rekencluster:__

    * Als het cluster is geconfigureerd met __min. knooppunten = 0,__ wordt het automatisch bijgewerkt wanneer alle taken zijn voltooid en wordt het gereduceerd tot nul knooppunten.
    * Als __het minimum aantal knooppunten > 0,__ wijzigt u de minimale knooppunten tijdelijk in nul en staat u toe dat het cluster wordt beperkt tot nul knooppunten.

    Zie de Azure CLI-opdracht [az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ml/computetarget/update#az_ml_computetarget_update_amlcompute) of de SDK-verwijzing [AmlCompute.update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) voor meer informatie over het wijzigen van de minimumknooppunten.

* __Azure Machine Learning compute-exemplaar:__ maak een nieuw reken-exemplaar (dat gebruik gaat maken van Ubuntu 18.04) en verwijder het oude exemplaar.

    * Elk notebook dat is opgeslagen in de bestands share van de werkruimte, gegevensopslag, van gegevenssets, is toegankelijk vanaf de nieuwe reken-instantie.
    * Als u aangepaste Conda-omgevingen hebt gemaakt, kunt u deze omgevingen exporteren vanuit het bestaande exemplaar en importeren in het nieuwe exemplaar. Zie [Conda-documentatie](https://docs.conda.io/) op de docs.conda.io voor meer informatie over conda docs.conda.io.

    Zie de artikelen Wat is een [reken-exemplaar en](concept-compute-instance.md) Een reken-Azure Machine Learning [maken en beheren?](how-to-create-manage-compute-instance.md) voor meer informatie

## <a name="check-host-os-version"></a>Controleer de versie van het host-besturingssysteem

Zie de wikipagina van de Ubuntu-community over het controleren van uw Ubuntu-versie voor meer informatie over het controleren van de versie van [het host-besturingssysteem.](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)

> [!TIP]
> Als u de `lsb_release -a` opdracht van de wiki wilt gebruiken, kunt u een [terminalsessie op een reken-exemplaar gebruiken.](how-to-access-terminal.md)
## <a name="next-steps"></a>Volgende stappen

Als u meer vragen of problemen hebt, kunt u contact met ons opnemen via [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
