---
title: Machine learning-en data Science-hulpprogram ma's
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de hulpprogram ma's voor machine learning en frameworks die vooraf zijn geïnstalleerd op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: ac71d8d7830f1d740d0156886a364b1d53ab247f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519555"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Machine learning-en data Science-hulpprogram ma's op Azure data Science Virtual Machines
Azure data Science Virtual Machines (Dsvm) beschikt over een uitgebreide set hulpprogram ma's en bibliotheken voor machine learning die beschikbaar zijn in populaire talen, zoals python, R en Julia.

Hier volgen enkele van de machine learning-hulpprogram ma's en-bibliotheken op Dsvm.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning-SDK voor Python

Zie de volledige referentie voor de [Azure machine learning SDK voor python](../overview-what-is-azure-ml.md).

| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   |   Azure Machine Learning is een Cloud service die u kunt gebruiken om machine learning-modellen te ontwikkelen en te implementeren. U kunt uw modellen volgen wanneer u deze bouwt, traint, schaalt en beheert met behulp van de python-SDK. Implementeer modellen als containers en voer deze uit in de Cloud, on-premises of op Azure IoT Edge.   |
| Ondersteunde versies     | Windows (Conda-omgeving: AzureML), Linux (Conda-omgeving: py36)    |
| Typische toepassingen      | Algemeen machine learning-platform      |
| Hoe wordt het geconfigureerd of geïnstalleerd?      |  Geïnstalleerd met GPU-ondersteuning   |
| Het gebruik of uitvoeren van het programma      | Als een python-SDK en in de Azure CLI. Activeer de Conda-omgeving `AzureML` op de Windows-editie *of* op de `py36` Linux-editie.      |
| Koppeling naar voor beelden      | Voor beelden van Jupyter-notebooks zijn opgenomen in de `AzureML` Directory onder notebooks.  |
| Verwante hulpprogramma's      | Visual Studio code, Jupyter   |

## <a name="h2o"></a>H2O

| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   | Een open-source AI-platform dat ondersteuning biedt voor in-Memory, gedistribueerde, snelle en schaal bare machine learning.  |
| Ondersteunde versies      | Linux   |
| Typische toepassingen      | Gedistribueerde, schaal bare machine learning voor algemeen gebruik   |
| Hoe wordt het geconfigureerd of geïnstalleerd?      | H2O is geïnstalleerd in `/dsvm/tools/h2o` .      |
| Het gebruik of uitvoeren van het programma      | Maak verbinding met de virtuele machine met behulp van X2Go. Start een nieuwe terminal en voer uit `java -jar /dsvm/tools/h2o/current/h2o.jar` . Start vervolgens een webbrowser en maak verbinding met `http://localhost:54321` .      |
| Koppeling naar voor beelden      | Voor beelden zijn beschikbaar op de virtuele machine in Jupyter in de `h2o` Directory.      |
| Verwante hulpprogramma's      | Apache Spark, MXNet, XGBoost, mousserend water, diep water    |

Er zijn verschillende andere machine learning-bibliotheken op Dsvm, zoals het populaire `scikit-learn` pakket dat deel uitmaakt van de Anaconda python-distributie voor dsvm. Als u de lijst met beschik bare pakketten in Python, R en Julia wilt bekijken, voert u de respectieve pakket beheerders uit.

## <a name="lightgbm"></a>LightGBM

| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   | Een snel, gedistribueerd verloop van de kleur overgang-Boosting (GBDT, GBRT, GBM of MART Framework) op basis van de beslissings structuur-algoritmen. Dit wordt gebruikt voor classificatie, classificatie en veel andere taken voor machine learning.    |
| Ondersteunde versies      | Windows, Linux    |
| Typische toepassingen      | Algemeen gebruik van kleur overgang-Boosting Framework      |
| Hoe wordt het geconfigureerd of geïnstalleerd?      | In Windows wordt LightGBM geïnstalleerd als een python-pakket. Op Linux is het opdracht regel programma in `/opt/LightGBM/lightgbm` , het R-pakket geïnstalleerd en Python-pakketten zijn geïnstalleerd.     |
| Koppeling naar voor beelden      | [LightGBM-hand leiding](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Verwante hulpprogramma's      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   |   Een Graphical User Interface voor gegevens analyse met behulp van R.   |
| Ondersteunde versies     | Windows, Linux     |
| Typische toepassingen      | Algemeen UI-hulp programma voor gegevens analyse voor R    |
| Het gebruik of uitvoeren van het programma      | Als een hulp programma voor de gebruikers interface. Start in Windows een opdracht prompt, voer R uit en voer vervolgens in R uit `rattle()` . Op Linux maakt u verbinding met X2Go, start u een Terminal, voert u R uit en voert u vervolgens in R uit `rattle()` . |
| Koppeling naar voor beelden      | [Rattle](https://togaware.com/onepager/) |
| Verwante hulpprogramma's      |LightGBM, Fridge, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   |   Een snelle, open-source, out-of-Core-learning systeem bibliotheek    |
| Ondersteunde versies     | Windows, Linux     |
| Typische toepassingen      | Algemene machine-learning-bibliotheek      |
| Hoe wordt het geconfigureerd of geïnstalleerd?      |  Windows: MSI-installatie programma<br/>Linux: apt-get |
| Het gebruik of uitvoeren van het programma      | Als een opdracht regel programma op het pad (in `C:\Program Files\VowpalWabbit\vw.exe` Windows `/usr/bin/vw` op Linux)    |
| Koppeling naar voor beelden      | [VowPal Wabbit-voor beelden](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Verwante hulpprogramma's      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   |  Een verzameling machine learning-algoritmen voor gegevens analyse taken. De algoritmen kunnen rechtstreeks worden toegepast op een gegevensset of worden aangeroepen vanuit uw eigen Java-code. Fridge bevat hulpprogram ma's voor het vooraf verwerken van gegevens, classificatie, regressie, Clustering, koppelings regels en visualisatie. |
| Ondersteunde versies     | Windows, Linux     |
| Typische toepassingen      | Algemeen hulp programma voor machine learning     |
| Het gebruik of uitvoeren van het programma      | Zoek in Windows naar Fridge in het menu **Start** . Meld u aan bij Linux met X2Go en ga vervolgens naar **toepassingen**  >  **Development**  >  **Fridge**. |
| Koppeling naar voor beelden      | [Fridge-voor beelden](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Verwante hulpprogramma's      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   |   Een snelle, draag bare en gedistribueerde GBDT-, GBRT-of GBM-bibliotheek voor python, R, Java, scala, C++ en meer. Deze wordt uitgevoerd op één computer en op Apache Hadoop en Spark.    |
| Ondersteunde versies     | Windows, Linux     |
| Typische toepassingen      | Algemene machine-learning-bibliotheek      |
| Hoe wordt het geconfigureerd of geïnstalleerd?      |  Geïnstalleerd met GPU-ondersteuning   |
| Het gebruik of uitvoeren van het programma      | Als een python-bibliotheek (2,7 en 3,5), R-pakket en het opdracht regel programma op het pad ( `C:\dsvm\tools\xgboost\bin\xgboost.exe` voor Windows en `/dsvm/tools/xgboost/xgboost` voor Linux)    |
| Koppelingen naar voor beelden      | Voor beelden zijn beschikbaar op de virtuele machine, in `/dsvm/tools/xgboost/demo` op Linux en in `C:\dsvm\tools\xgboost\demo` Windows.   |
| Verwante hulpprogramma's      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   | Open-Source SQL-query-engine op big data    |
| Ondersteunde DSVM-versies      | Windows 2019, Linux  |
| Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM?      |  Alleen geïnstalleerd in de `/dsvm/tools/drill*` Inge sloten modus   |
| Typische toepassingen      |  Voor in-place gegevens onderzoek zonder extra heren, transformeren, laden (ETL). Query's uitvoeren op verschillende gegevens bronnen en indelingen, waaronder CSV, JSON, relationele tabellen en Hadoop.     |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad  <br/> [Aan de slag met boren in 10 minuten](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Gerelateerde hulpprogram ma's op de DSVM      |   Rattle, Fridge, SQL Server Management Studio      |