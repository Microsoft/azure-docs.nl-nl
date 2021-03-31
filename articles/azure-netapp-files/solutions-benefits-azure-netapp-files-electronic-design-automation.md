---
title: Voor delen van het gebruik van Azure NetApp Files voor elektronische ontwerp automatisering | Microsoft Docs
description: In dit artikel wordt uitgelegd wat de oplossing Azure NetApp Files biedt om tegemoet te komen aan de behoeften van de halfgeleider-en chip ontwerp industrie. Geeft test scenario's voor het uitvoeren van een standaard industrie benchmark voor Electronic Design Automation (EDA) met behulp van Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82160150"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>Voordelen van het gebruik van Azure NetApp Files voor electronic design automation

Time-to-Market (TTM) is een belang rijke overweging voor de design-industrie van halfgeleider en chip. De branche heeft een hoge band breedte en een lage latentie vereist voor opslag. In dit artikel wordt uitgelegd wat de oplossing Azure NetApp Files biedt om tegemoet te komen aan de behoeften van de branche. Er wordt gebruikgemaakt van test scenario's voor het uitvoeren van een standaard industrie benchmark voor Electronic Design Automation (EDA) met behulp van Azure NetApp Files. 

## <a name="test-scenario-configurations"></a>Configuraties van test scenario's

De tests hebben drie scenario's met de volgende configuraties. 

|    Scenario    |    Volumes    |    Clients<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    Eén         |    1          |    1                           |
|    Twee         |    6          |    24                          |
|    Drie       |    12         |    24                          |

In het eerste scenario wordt beschreven hoe ver één volume kan worden aangestuurd.  

De tweede en derde scenario's evalueren de limieten van één Azure NetApp Files eind punt. Ze onderzoeken de mogelijke voor delen van de limieten en latentie van I/O-boven.

## <a name="test-scenario-results"></a>Resultaten van test scenario

De volgende tabel bevat een overzicht van de resultaten van de test scenario's.

|    Scenario       |    I/O-frequentie<br>  bij 2 MS     |    I/O-frequentie<br>  aan de rand     |    Doorvoer<br>  bij 2 MS     |    Doorvoer<br>  aan de rand     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 volume       |    39.601                 |    49.502                      |    692-MiB/s                 |    866-MiB/s                      |
|    6 volumes      |    255.613                |    317.000                     |    4.577-MiB/s               |    5.568-MiB/s                    |
|    12 volumes     |    256.612                |    319.196                     |    4.577-MiB/s               |    5.709-MiB/s                    |

Het scenario met één volume vertegenwoordigt de basis configuratie van de toepassing. Het is het basis scenario voor follow-on test scenario's.  

In het scenario met zes volumes wordt een lineaire toename gedemonstreerd (600%) ten opzichte van de werk belasting met één volume.  Alle volumes binnen één virtueel netwerk hebben toegang tot een enkel IP-adres.  

In het scenario met 12 volumes ziet u een algemene afname van de latentie ten opzichte van het scenario met zes volumes. Maar het heeft geen overeenkomstige toename van de haal bare door voer.   

In de volgende grafiek ziet u het aantal latentie en bewerkings snelheden voor de EDA-werk belasting op Azure NetApp Files.  

![Latentie en bewerkings snelheden voor de EDA-workload op Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

In de volgende grafiek ziet u de latentie en door Voer voor de EDA-werk belasting op Azure NetApp Files.  

![Latentie en door Voer voor de EDA-werk belasting op Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>Indeling van test scenario's 

De volgende tabel bevat een overzicht van de indeling van de test scenario's.

|    Test scenario     |    Totaal aantal directory's     |    Totaal aantal bestanden     |
|----------------------|------------------------------------|------------------------------|
|    1 volume          |    88.000                          |    880.000                   |
|    6 volumes         |    568.000                         |    5.680.000                 |
|    12 volumes        |    568.000                         |    5.680.000                 |

De volledige werk belasting is een combi natie van de gelijktijdig uitgevoerde functionele en fysieke fasen. Het vertegenwoordigt een typische stroom van een set EDA-hulpprogram ma's naar een andere.   

De functionele fase bestaat uit de eerste specificaties en een logisch ontwerp. De fysieke fase vindt plaats wanneer het logische ontwerp wordt geconverteerd naar een fysieke chip. Tijdens de afmeldings-en tape-runtime worden eind controles voltooid en wordt het ontwerp bezorgd bij een gieterij voor productie.  

De functionele fase bevat een combi natie van sequentiële en wille keurige I/O-lees-en schrijf bewerkingen. De functionele fase is intensieve meta gegevens, zoals bestands statistieken en toegangs aanroepen. Hoewel meta gegevens bewerkingen effectief zijn zonder grootte, wordt het Lees-en schrijf bewerkingen bereik kleiner dan 1 K en 16 K. De meeste Lees bewerkingen tussen 4 en 16 K.  De meeste schrijf bewerkingen zijn 4 K of minder. De fysieke fase bestaat volledig uit sequentiële Lees-en schrijf bewerkingen, met een combi natie van 32 K en 64 K OP grootten.  

In de bovenstaande grafieken is de meeste door Voer van de sequentiële fysieke fase van de werk belasting. De I/O is afkomstig uit de kleine functionele fase wille keurig en op basis van meta gegevens. Beide fasen gebeuren parallel. 

In de conclusie kunt u Azure Compute koppelen met Azure NetApp Files voor EDA-ontwerp om schaal bare band breedte te verkrijgen. 

## <a name="next-steps"></a>Volgende stappen

- [Oplossingsarchitecturen op basis van Azure NetApp Files](azure-netapp-files-solution-architectures.md)
