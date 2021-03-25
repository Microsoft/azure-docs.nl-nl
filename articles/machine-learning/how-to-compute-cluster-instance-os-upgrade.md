---
title: Besturings systeem van de host upgraden voor het berekenings cluster en het exemplaar
titleSuffix: Azure Machine Learning
description: Voer een upgrade uit voor het besturings systeem van de host voor reken cluster en reken instantie van Ubuntu 16,04 LTS naar 18,04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 710a860b1ed87f176b6f42b4963dad17acb323b1
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954051"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Reken instantie en reken cluster host-besturings systeem bijwerken

Azure Machine Learning __Compute-Cluster__ en __reken instantie__ worden beheerde Compute-infra structuur. Als beheerde service beheert micro soft het besturings systeem van de host en de geïnstalleerde pakketten en software versies.

Het besturings systeem voor de host voor het berekenings cluster en het reken exemplaar is Ubuntu 16,04 LTS. Op **30 April 2021** wordt de ondersteuning voor 16,04 voor Ubuntu beëindigd. Vanaf __15 maart 2021__ wordt het hostbesturingssysteem automatisch bijgewerkt naar Ubuntu 18,04 LTS. Bijwerken naar 18,04 zorgt voor voortdurende beveiligings updates en ondersteuning van de Ubuntu-community. Deze update wordt doorgevoerd in azure-regio's en is beschikbaar in alle regio's van __9 April 2021__. Zie de [Ubuntu release-blog](https://wiki.ubuntu.com/Releases)voor meer informatie over de Ubuntu-ondersteuning voor 16,04.

> [!TIP]
> * Het besturings systeem van de host is niet de versie van het besturings systeem dat u kunt opgeven voor een [omgeving](how-to-use-environments.md) bij het trainen of implementeren van een model. Omgevingen worden uitgevoerd in docker. Docker wordt uitgevoerd op het besturings systeem van de host.
> * Als u momenteel gebruikmaakt van Ubuntu 16,04-omgevingen voor training of implementatie, raadt micro soft u aan om over te scha kelen naar het gebruik van Ubuntu 18,04-gebaseerde installatie kopieën. Zie omgevingen en de [opslag plaats van Azure machine learning containers](https://github.com/Azure/AzureML-Containers/tree/master/base) [gebruiken](how-to-use-environments.md) voor meer informatie.
> * Wanneer u een Azure Machine Learning Compute-instantie gebruikt op basis van Ubuntu 18,04, is de standaard Python-versie _python 3,8_.
## <a name="creating-new-resources"></a>Nieuwe resources maken

Compute-Cluster of COMPUTE-instanties die zijn gemaakt na __9 April 2021__ gebruiken Ubuntu 18,04 LTS standaard als hostbesturingssysteem. U kunt geen ander host-besturings systeem selecteren.

## <a name="upgrade-existing-resources"></a>Bestaande resources bijwerken

Als u een bestaand reken cluster of reken instanties hebt gemaakt vóór __15 maart 2021__, moet u actie ondernemen om het hostbesturingssysteem bij te werken naar Ubuntu 18,04. Afhankelijk van de regio waar u toegang tot hebt Azure Machine Learning, raden we u aan deze acties na __9 April 2021__ te volgen om ervoor te zorgen dat de wijzigingen zijn doorgevoerd naar alle regio's:

* __Azure machine learning Compute-Cluster__:

    * Als het cluster is geconfigureerd met __minimum aantal knoop punten = 0__, wordt het automatisch geüpgraded wanneer alle taken zijn voltooid en wordt het aantal knoop punten verminderd.
    * Als de minimum __knooppunten > 0__, wijzigt u de minimale knoop punten tijdelijk in nul en wordt het cluster beperkt tot nul knoop punten.

    Voor meer informatie over het wijzigen van het minimum aantal knoop punten raadpleegt u de opdracht [AZ ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) Azure cli (Engelstalig) of de SDK-Naslag informatie over [amlcompute. update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) .

* __Azure machine learning Compute-instantie__: Maak een nieuw Compute-exemplaar (dat gebruikmaakt van Ubuntu 18,04) en verwijder het oude exemplaar.

    * Elk notitie blok dat is opgeslagen in de werkruimte bestands share, gegevens archieven, van data sets, is toegankelijk vanuit het nieuwe reken exemplaar.
    * Als u aangepaste Conda-omgevingen hebt gemaakt, kunt u deze omgevingen vanuit het bestaande exemplaar exporteren en importeren op het nieuwe exemplaar. Zie [Conda-documentatie](https://docs.conda.io/) op docs.Conda.io voor meer informatie over het exporteren en importeren van Conda.

    Zie voor meer informatie het artikel [Wat is reken](concept-compute-instance.md) proces en [een Azure machine learning Compute-exemplaar maken en beheren](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Versie van host-besturings systeem controleren

Voor informatie over het controleren van de versie van het besturings systeem van de host raadpleegt u de Ubuntu community wiki-pagina voor het [controleren van uw Ubuntu-versie](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> Als u de `lsb_release -a` opdracht van de wiki wilt gebruiken, kunt u [een terminal sessie op een reken instantie gebruiken](how-to-access-terminal.md).
## <a name="next-steps"></a>Volgende stappen

Neem contact met ons op als u meer vragen of problemen hebt [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
