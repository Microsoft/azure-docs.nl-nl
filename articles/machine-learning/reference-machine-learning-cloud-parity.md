---
title: Pariteit tussen open bare en soevereine regio's
titleSuffix: Azure Machine Learning
description: In dit artikel vindt u een overzicht van de functie pariteit tussen open bare Cloud en de regio's Azure Government, Azure Duitsland en Azure China 21Vianet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 12/21/2020
ms.custom: references_regions
ms.openlocfilehash: 88240f9b46997d11f1e7c2d93fa880b004615a11
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97725017"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning soevereine Cloud pariteit

Meer informatie over Azure Machine Learning-functies beschikbaar in soevereine Cloud regio's. 

In de lijst met wereld wijde Azure-regio's zijn er verschillende soevereine regio's die specifieke markten bezorgen. Bijvoorbeeld het Azure Government en de Azure China 21Vianet-regio's. Momenteel Azure Machine Learning wordt geïmplementeerd in de volgende soevereine-Cloud regio's:

* Azure Government regio's **VS-Arizona** en **US-Virginia**.
* Azure China 21Vianet **-regio China-Oost-2**.

> [!TIP]
> Om onderscheid te maken tussen soevereine en niet-soevereine regio's, wordt in dit artikel de term __open bare Cloud__ gebruikt om te verwijzen naar niet-soevereine regio's.

We streven ernaar maximale pariteit te bieden tussen onze open bare Cloud en soevereine regio's. Alle Azure Machine Learning-functies zijn beschikbaar in deze regio's binnen **30 dagen na de dag ga** (algemene Beschik baarheid) in onze open bare Cloud. We scha kelen ook een geselecteerd aantal preview-functies in deze regio's in. Hieronder ziet u de huidige pariteits verschillen tussen onze soevereine en open bare Clouds.

## <a name="azure-government"></a>Azure Government 

| Functie | Status open bare Cloud  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Geautomatiseerde Machine Learning** | | | |
| Experimenten maken en uitvoeren in notitie blokken                                    | Algemene beschikbaarheid                   | JA                | JA         |
| Experimenten maken en uitvoeren in Studio Web Experience                        | Openbare preview       | JA                | JA         |
| Toonaangevende prognose mogelijkheden                                  | Algemene beschikbaarheid                   | JA                | JA         |
| Ondersteuning voor diep gaande lessen en andere geavanceerde kennissen                      | Algemene beschikbaarheid                   | JA                | JA         |
| Ondersteuning voor grote gegevens (Maxi maal 100 GB)                                          | Openbare preview       | JA                | JA         |
| Integratie van Azure Databricks                                              | Algemene beschikbaarheid                   | NO                 | NO          |
| SQL-, CosmosDB-en HDInsight-integraties                                   | Algemene beschikbaarheid                   | JA                | JA         |
| **Machine Learning pijp lijnen** |   |  | | 
| Pijp lijnen maken, uitvoeren en publiceren met behulp van de Azure ML SDK                   | Algemene beschikbaarheid                   | JA                | JA         |
| Pijplijn eindpunten maken met behulp van de Azure ML SDK                           | Algemene beschikbaarheid                   | JA                | JA         |
| Geplande uitvoeringen van pijp lijnen maken, bewerken en verwijderen met behulp van de Azure ML SDK | Algemene beschikbaarheid                   | KLIKT               | KLIKT        |
| Details van pijplijn uitvoering weer geven in Studio                                        | Algemene beschikbaarheid                   | JA                | JA         |
| Pijp lijnen maken, uitvoeren, visualiseren en publiceren in azure ML Designer          | Algemene beschikbaarheid      | JA                | JA         |
| Integratie met ML-pijp lijn Azure Databricks                             | Algemene beschikbaarheid                   | NO                 | NO          |
| Pijplijn eindpunten maken in azure ML Designer                             | Algemene beschikbaarheid      | JA                | JA         |
| **Geïntegreerde notebooks** |   |  | | 
| Werkruimte notitieblokken en delen van bestanden                                        | Algemene beschikbaarheid                   | JA                | JA         |
| Ondersteuning voor R en python                                                       | Algemene beschikbaarheid                   | JA                | JA         |
| Ondersteuning voor virtuele netwerken                                                    | Openbare preview       | NO                 | NO          |
| **Reken instantie** |   |  | | 
| Beheerde Compute-instanties voor geïntegreerde notebooks                         | Algemene beschikbaarheid                   | JA                | JA         |
| Jupyter, Jjupyterlab-integratie                                            | Algemene beschikbaarheid                   | JA                | JA         |
| Ondersteuning voor Virtual Network (VNet)                                             | Openbare preview       | JA                | JA         |
| **SDK-ondersteuning** |  |  | | 
| R SDK-ondersteuning                                                              | Openbare preview       | JA                | JA         |
| Ondersteuning voor python-SDK                                                         | Algemene beschikbaarheid                   | JA                | JA         |
| **Beveiliging** |   | | | 
| Virtual Network-ondersteuning (VNet) voor training                                | Algemene beschikbaarheid                   | JA                | JA         |
| Virtual Network-ondersteuning (VNet) voor demijnen                               | Algemene beschikbaarheid                   | JA                | JA         |
| Score endpoint-verificatie                                            | Openbare preview       | JA                | JA         |
| Persoonlijke werkplek koppeling                                                     | Openbare preview       | NO                 | NO          |
| ACI achter VNet                                                            | Openbare preview       | NO                 | NO          |
| ACR achter VNet                                                            | Openbare preview       | NO                 | NO          |
| Persoonlijk IP-adres van AKS-cluster                                                  | Openbare preview       | NO                 | NO          |
| **Compute** |   | | |
| quota beheer in werk ruimten                                         | Algemene beschikbaarheid                   | JA                | JA         |
| **Gegevens voor machine learning** |   | | |
| Gegevens sets en gegevens opslag maken, weer geven of bewerken vanuit de SDK                  | Algemene beschikbaarheid                   | JA                | JA         |
| Gegevens sets en gegevens opslag maken, weer geven of bewerken vanuit de gebruikers interface                   | Algemene beschikbaarheid                   | JA                | JA         |
| Videodrijf monitors voor gegevensset weer geven, bewerken of verwijderen uit de SDK                   | Openbare preview       | JA                | JA         |
| Het maken, bewerken of verwijderen van de gegevensset voor het maken van gegevens sets vanuit de gebruikers interface                    | Openbare preview       | JA                | JA         |
| **Machine learning-levens cyclus** |   | | |
| Model profilering                                                            | Algemene beschikbaarheid                   | JA                | GEDEELTELIJKE     |
| De Azure DevOps-extensie voor Machine Learning & de Azure ML CLI         | Algemene beschikbaarheid                   | JA                | JA         |
| Modellen met hardwareversnelling op basis van FPGA                                     | Algemene beschikbaarheid                   | NO                 | NO          |
| Integratie van Visual Studio code                                             | Openbare preview       | NO                 | NO          |
| Event Grid-integratie                                                     | Openbare preview       | NO                 | NO          |
| Azure Stream Analytics integreren met Azure Machine Learning               | Openbare preview       | NO                 | NO          |
| **Labels** |   | | |
| Labelen project Beheerportal                                        | Algemene beschikbaarheid                   | JA                | JA         |
| Labeler-Portal                                                            | Algemene beschikbaarheid                   | JA                | JA         |
| Labelen met privé personeel                                          | Algemene beschikbaarheid                   | JA                | JA         |
| ML gesteunde labels (afbeeldings classificatie en object detectie)           | Openbare preview       | JA                | JA         |
| **Verantwoordelijke ML** |   | | |
| Uitleg bij de gebruikers interface                                                       | Openbare preview       | NO                 | NO          |
| Differentiële privacybeleid SmartNoise Toolkit                                    | OSS                  | NO                 | NO          |
| aangepaste labels in Azure Machine Learning voor het implementeren van gegevens bladen              | Algemene beschikbaarheid                   | NO                 | NO          |
| Verdeling AzureML-integratie                                               | Openbare preview       | NO                 | NO          |
| Coninterpreter-SDK                                                      | Algemene beschikbaarheid                   | JA                | JA         |
| **Training** |   | | |
| Streaming logboek streamen                                              | Algemene beschikbaarheid                   | JA                | JA         |
| Leer versterking                                                     | Openbare preview       | NO                 | NO          |
| Gebruikers interface voor experimenten                                                         | Algemene beschikbaarheid                   | JA                | JA         |
| .NET-integratie ML.NET 1,0                                                | Algemene beschikbaarheid                   | JA                | JA         |
| **Deductie** |   | | |
| Batch-deinterferentie                                                          | Algemene beschikbaarheid                   | JA                | JA         |
| Data Box Edge met FPGA                                                    | Openbare preview       | NO                 | NO          |
| **Overige** |   | | |
| Open Datasets                                                              | Openbare preview       | JA                | JA         |
| Aangepaste Cognitive Search                                                    | Openbare preview       | JA                | JA         |
| Veel modellen                                                                | Openbare preview       | NO                 | NO          |


### <a name="azure-government-scenarios"></a>Azure Government scenario's

| Scenario                                                    | US-Virginia | US-Arizona| Beperkingen  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Algemene beveiligings instellingen** |   | | |
| Particuliere netwerk communicatie tussen services                                     | NO | NO | Er is momenteel geen persoonlijke koppeling | 
| Internet toegang (inkomend en uitgaand) en specifiek VNet uitschakelen/beheren | GEDEELTELIJKE| GEDEELTELIJKE   | ACR achter VNet is niet beschikbaar in Azure Government-dubbel controleren op ACI | 
| Plaatsing voor alle gekoppelde resources/Services  | JA | JA |  |
| Versleuteling op rest en in-transit.                                                 | JA | JA |  |
| Basis-en SSH-toegang tot reken resources.                                          | JA | JA |  |
| De beveiliging van geïmplementeerde systemen (instanties, eind punten, enz.) onderhouden, inclusief Endpoint Protection, patches en logboek registratie |  GEDEELTELIJKE|  GEDEELTELIJKE |ACI achter VNet en privé-eind punt is momenteel niet beschikbaar |                                  
| Beheer (het gebruik van ACI/AKS-integratie uitschakelen/beperken)                    | GEDEELTELIJKE| GEDEELTELIJKE |ACI achter VNet en privé-eind punt is momenteel niet beschikbaar|
| Op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC)-aangepaste functie creatie                           | JA | JA |  |
| Toegang tot ACR-installatie kopieën beheren die worden gebruikt door de ML-service (door Azure aangestuurd/onderhouden versus aangepast)  |GEDEELTELIJKE|  GEDEELTELIJKE | ACR achter persoonlijk eind punt en VNet niet ondersteund in Azure Government |
| **Algemeen Machine Learning service gebruik** |  | | |
| De mogelijkheid om een model te maken voor een ontwikkel omgeving, het model te trainen, te hosten als een eind punt en dit te gebruiken via een webapp     | JA | JA |  |
| De mogelijkheid om gegevens op te halen uit ADLS (Data Lake Storage)                                 |JA | JA |  |
| De mogelijkheid om gegevens op te halen uit Azure Blob Storage                                       |JA | JA |  |



### <a name="additional-azure-government-limitations"></a>Aanvullende Azure Government beperkingen

* Voor Azure Machine Learning Compute-instanties is de mogelijkheid om een token te vernieuwen dat langer is dan 24 uur niet beschikbaar in Azure Government.
* Model profilering biedt geen ondersteuning voor vier Cpu's in de US-Arizona regio.   
* Voorbeeld notitieblokken werken mogelijk niet in Azure Government als deze toegang nodig heeft tot open bare gegevens.
* IP-adressen: de CLI-opdracht die wordt gebruikt in de instructies [VNet en geforceerde tunneling](how-to-secure-training-vnet.md#forced-tunneling) retourneert geen IP-bereiken. Gebruik in plaats daarvan de [Azure IP-bereiken en-service tags voor Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) .
* Voor geplande pijp lijnen bieden we ook een activerings mechanisme op basis van een blob. Dit mechanisme wordt niet ondersteund voor CMK-werk ruimten. Voor het inschakelen van een op BLOB gebaseerde trigger voor CMK-werk ruimten, moet u aanvullende installatie uitvoeren. Zie [een uitvoering van een machine learning-pijp lijn activeren vanuit een logische app](how-to-trigger-published-pipeline.md)voor meer informatie.
* Firewalls: als u een Azure Government regio gebruikt, voegt u de volgende extra hosts toe aan de firewall instelling:

    * Voor Arizona gebruik: `usgovarizona.api.ml.azure.us`
    * Gebruik voor Virginia: `usgovvirginia.api.ml.azure.us`
    * Voor beide: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure China 21Vianet 

| Functie                                       | Status open bare Cloud | CH-Oost-2 | CH-Noord-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Geautomatiseerde Machine Learning** |    | | |
| Experimenten maken en uitvoeren in notitie blokken                                    | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Experimenten maken en uitvoeren in Studio Web Experience                        | Openbare preview   | JA       | N.v.t.        |
| Toonaangevende prognose mogelijkheden                                  | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Ondersteuning voor diep gaande lessen en andere geavanceerde kennissen                      | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Ondersteuning voor grote gegevens (Maxi maal 100 GB)                                          | Openbare preview   | JA       | N.v.t.        |
| Integratie van Azure Databricks                                              | Algemene beschikbaarheid               | NO        | N.v.t.        |
| SQL-, CosmosDB-en HDInsight-integraties                                   | Algemene beschikbaarheid               | JA       | N.v.t.        |
| **Machine Learning pijp lijnen** |    | | |
| Pijp lijnen maken, uitvoeren en publiceren met behulp van de Azure ML SDK                   | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Pijplijn eindpunten maken met behulp van de Azure ML SDK                           | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Geplande uitvoeringen van pijp lijnen maken, bewerken en verwijderen met behulp van de Azure ML SDK | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Details van pijplijn uitvoering weer geven in Studio                                        | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Pijp lijnen maken, uitvoeren, visualiseren en publiceren in azure ML Designer          | Algemene beschikbaarheid  | JA       | N.v.t.        |
| Integratie met ML-pijp lijn Azure Databricks                             | Algemene beschikbaarheid               | NO        | N.v.t.        |
| Pijplijn eindpunten maken in azure ML Designer                             | Algemene beschikbaarheid   | JA       | N.v.t.        |
| **Geïntegreerde notebooks** |   | | |
| Werkruimte notitieblokken en delen van bestanden                                        | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Ondersteuning voor R en python                                                       | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Ondersteuning voor virtuele netwerken                                                    | Openbare preview   | NO        | N.v.t.        |
| **Reken instantie** |    | | |
| Beheerde Compute-instanties voor geïntegreerde notebooks                         | Algemene beschikbaarheid               | NO        | N.v.t.        |
| Jupyter, Jjupyterlab-integratie                                            | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Ondersteuning voor Virtual Network (VNet)                                             | Openbare preview   | JA       | N.v.t.        |
| **SDK-ondersteuning** |    | | |
| R SDK-ondersteuning                                                              | Openbare preview   | JA       | N.v.t.        |
| Ondersteuning voor python-SDK                                                         | Algemene beschikbaarheid               | JA       | N.v.t.        |
| **Beveiliging** |   | | |
| Virtual Network-ondersteuning (VNet) voor training                                | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Virtual Network-ondersteuning (VNet) voor demijnen                               | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Score endpoint-verificatie                                            | Openbare preview   | JA       | N.v.t.        |
| Persoonlijke werkplek koppeling                                                     | Openbare preview   | NO        | N.v.t.        |
| ACI achter VNet                                                            | Openbare preview   | NO        | N.v.t.        |
| ACR achter VNet                                                            | Openbare preview   | NO        | N.v.t.        |
| Persoonlijk IP-adres van AKS-cluster                                                  | Openbare preview   | NO        | N.v.t.        |
| **Compute** |   | | |
| quota beheer in werk ruimten                                         | Algemene beschikbaarheid               | JA       | N.v.t.        |
| **Gegevens voor machine learning** | | | |
| Gegevens sets en gegevens opslag maken, weer geven of bewerken vanuit de SDK                  | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Gegevens sets en gegevens opslag maken, weer geven of bewerken vanuit de gebruikers interface                   | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Videodrijf monitors voor gegevensset weer geven, bewerken of verwijderen uit de SDK                   | Openbare preview   | JA       | N.v.t.        |
| Het maken, bewerken of verwijderen van de gegevensset voor het maken van gegevens sets vanuit de gebruikers interface                    | Openbare preview   | JA       | N.v.t.        |
| **Machine learning-levens cyclus** |    | | |
| Model profilering                                                            | Algemene beschikbaarheid               | GEDEELTELIJKE   | N.v.t.        |
| De Azure DevOps-extensie voor Machine Learning & de Azure ML CLI         | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Modellen met hardwareversnelling op basis van FPGA                                     | Algemene beschikbaarheid               | NO        | N.v.t.        |
| Integratie van Visual Studio code                                             | Openbare preview   | NO        | N.v.t.        |
| Event Grid-integratie                                                     | Openbare preview   | JA       | N.v.t.        |
| Azure Stream Analytics integreren met Azure Machine Learning               | Openbare preview   | NO        | N.v.t.        |
| **Labels** |    | | |
| Labelen project Beheerportal                                        | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Labeler-Portal                                                            | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Labelen met privé personeel                                          | Algemene beschikbaarheid               | JA       | N.v.t.        |
| ML gesteunde labels (afbeeldings classificatie en object detectie)           | Openbare preview   | JA       | N.v.t.        |
| **Verantwoordelijke ML** |    | | |
| Uitleg bij de gebruikers interface                                                       | Openbare preview   | NO        | N.v.t.        |
| Differentiële privacybeleid SmartNoise Toolkit                                    | OSS              | NO        | N.v.t.        |
| aangepaste labels in Azure Machine Learning voor het implementeren van gegevens bladen              | Algemene beschikbaarheid               | NO        | N.v.t.        |
| Verdeling AzureML-integratie                                               | Openbare preview   | NO        | N.v.t.        |
| Coninterpreter-SDK                                                      | Algemene beschikbaarheid               | JA       | N.v.t.        |
| **Training** |    | | |
| Streaming logboek streamen                                              | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Leer versterking                                                     | Openbare preview   | NO        | N.v.t.        |
| Gebruikers interface voor experimenten                                                         | Algemene beschikbaarheid               | JA       | N.v.t.        |
| .NET-integratie ML.NET 1,0                                                | Algemene beschikbaarheid               | JA       | N.v.t.        |
| **Deductie** |   | | |
| Batch-deinterferentie                                                          | Algemene beschikbaarheid               | JA       | N.v.t.        |
| Data Box Edge met FPGA                                                    | Openbare preview   | NO        | N.v.t.        |
| **Overige** |    | | |
| Open Datasets                                                              | Openbare preview   | JA       | N.v.t.        |
| Aangepaste Cognitive Search                                                    | Openbare preview   | JA       | N.v.t.        |
| Veel modellen                                                                | Openbare preview   | NO        | N.v.t.        |



### <a name="additional-azure-china-limitations"></a>Extra beperkingen voor Azure China

* Azure China heeft een beperkte VM-SKU, met name voor GPU-SKU. Het heeft alleen NCv3 Family (V100).
* REST API-eind punten verschillen van de wereld wijde Azure. Gebruik de volgende tabel om het REST API-eind punt te vinden voor regio's van Azure China:

    | REST-eind punt                 | Wereld wijd Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Beheerlaag | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Gegevenslaag       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Een voor beeld van een notebook werkt mogelijk niet, als het toegang tot open bare gegevens vereist.
* IP-adresbereiken: de CLI-opdracht die wordt gebruikt in de instructies voor [geforceerde tunneling](how-to-secure-training-vnet.md#forced-tunneling) , retourneert geen IP-bereiken. Gebruik in plaats daarvan de [Azure IP-bereiken en service tags voor Azure China](https://www.microsoft.com//download/details.aspx?id=57062) .
* Azure Machine Learning Compute instances wordt niet ondersteund in een werk ruimte waar een persoonlijke koppeling momenteel is ingeschakeld, maar CI wordt wel ondersteund in de volgende implementatie voor de service-uitbrei ding voor alle AML-regio's.

## <a name="next-steps"></a>Volgende stappen

Zie [producten per regio](https://azure.microsoft.com/global-infrastructure/services/)voor meer informatie over de regio's waarin Azure machine learning beschikbaar is.
