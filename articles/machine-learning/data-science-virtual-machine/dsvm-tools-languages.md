---
title: Ondersteunde talen
titleSuffix: Azure Data Science Virtual Machine
description: De ondersteunde programma talen en gerelateerde hulpprogram ma's die vooraf zijn geïnstalleerd op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516427"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Talen die worden ondersteund op de Data Science Virtual Machine 

De Data Science Virtual Machine (DSVM) wordt geleverd met verschillende vooraf ontwikkelde talen en hulpprogram ma's voor het ontwikkelen van AI-toepassingen (kunst matige intelligentie). Hier volgen enkele van de belangrijkste items.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde taal versies | Python 2,7 en 3,7 |
| Ondersteunde DSVM-edities      | Windows Server 2016     |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Er worden twee wereld wijde `conda` omgevingen gemaakt: <br /> * De `root` omgeving die zich bevindt in `/anaconda/` is python 3,7. <br/> * De `python2` omgeving die zich bevindt in `/anaconda/envs/python2` is Python 2,7.       |
| Koppelingen naar voor beelden      | Er zijn voor beelden van Jupyter-notebooks voor python opgenomen.     |
| Gerelateerde hulpprogram ma's op de DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Builds van Windows Server 2016 die zijn gemaakt vóór 2018 maart bevatten python 3,5 en Python 2,7. Python 2,7 is de Conda- **hoofd** omgeving en **Py37** is de python 3,7-omgeving.

### <a name="how-to-use-and-run-it"></a>Hoe gebruiken en uitvoeren    

* Uitvoeren vanaf een opdracht prompt:

  Open een opdracht prompt en gebruik een van de volgende methoden, afhankelijk van de versie van python die u wilt uitvoeren:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Gebruiken in een IDE:

  Gebruik Python Tools for Visual Studio (PTVS), dat is geïnstalleerd in de Visual Studio Community-editie. Standaard is de enige omgeving die automatisch in PTVS wordt ingesteld, python 3,6. 

    > [!NOTE]
    > Om PTVS te laten verwijzen naar python 2,7, moet u een aangepaste omgeving maken in PTVS. Als u dit pad naar de omgeving in Visual Studio Community Edition wilt instellen, gaat u naar **hulpprogram ma's**  ->  **python tools**  ->  **python-omgevingen** en selecteert u **+ Custom**. Stel vervolgens de locatie in op **c:\anaconda\envs\python2** en selecteer **Automatische detectie**.

* Gebruiken in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitie blok te maken. U kunt het kernel-type instellen als _python [Conda root]_ voor python 3,7 en _python [Conda env: Python2]_ voor python 2,7.

* Python-pakketten installeren:

  De standaard python-omgevingen op de DSVM zijn wereld wijde omgevingen die door alle gebruikers kunnen worden gelezen. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de globale omgeving, activeert u de hoofd-of python2-omgeving met behulp van de `activate` opdracht als beheerder. Vervolgens kunt u een pakket beheer programma gebruiken, zoals `conda` of `pip` om pakketten te installeren of bij te werken.

## <a name="python-linux-edition"></a>Python (Linux Edition)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde taal versies | Python 2,7 en 3,5 |
| Ondersteunde DSVM-edities      | Linux   |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Er worden twee wereld wijde `conda` omgevingen gemaakt: <br /> * `root` de omgeving die zich bevindt in `/anaconda/` is Python 2,7. <br/> * `py35` de omgeving die zich bevindt in `/anaconda/envs/py35` is Python 3,5.       |
| Koppelingen naar voor beelden      | Er zijn voor beelden van Jupyter-notebooks voor python opgenomen.     |
| Gerelateerde hulpprogram ma's op de DSVM      | PySpark, R, Julia      |

### <a name="how-to-use-and-run-it"></a>Hoe gebruiken en uitvoeren    

* Uitvoeren in een Terminal:

  Open de Terminal en voer een van de volgende handelingen uit, afhankelijk van de versie van python die u wilt uitvoeren:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Gebruiken in een IDE:

  Gebruik PyCharm, geïnstalleerd in de Visual Studio Community-editie. 

* Gebruiken in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitie blok te maken. U kunt het kernel-type instellen als **python [Conda root]** voor python 2,7 en **python [Conda env: py35]** voor de python 3,5-omgeving. 

* Python-pakketten installeren:

  De standaard python-omgevingen op de DSVM zijn wereld wijde omgevingen die door alle gebruikers kunnen worden gelezen. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de globale omgeving, gaat u naar de hoofd-of py35-omgeving met behulp van de `source activate` opdracht als Administrator of als gebruiker met sudo-machtigingen. Vervolgens kunt u een pakket beheer programma gebruiken, zoals `conda` of `pip` om pakketten te installeren of bij te werken.


## <a name="r"></a>R

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde taal versies | Micro soft R open 3. x (100% compatibel met KRANen-R)<br /> Microsoft R Server 9. x Developer Edition (een schaalbaar platform dat geschikt is voor bedrijven)|
| Ondersteunde DSVM-edities      | Linux, Windows     |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Koppelingen naar voor beelden      | Er zijn voor beelden van Jupyter-notebooks voor R opgenomen.     |
| Gerelateerde hulpprogram ma's op de DSVM      | Spark, Python, Julia      |

### <a name="how-to-use-and-run-it"></a>Hoe gebruiken en uitvoeren    

**Windows**:

* Uitvoeren vanaf een opdracht prompt:

  Open een opdrachtprompt en typ `R`.

* Gebruiken in een IDE:

  Gebruik RTools voor Visual Studio (RTVS) die is geïnstalleerd in de Visual Studio Community Edition of RStudio. Deze zijn beschikbaar in het menu Start of als pictogram op het bureau blad. 

* Gebruiken in Jupyter

  Open Jupyter en selecteer **Nieuw** om een nieuw notitie blok te maken. U kunt het kernel-type instellen op **r** om de Jupyter R kernel (IRKernel) te gebruiken.

* R-pakketten installeren:

  R is geïnstalleerd op de DSVM in een wereld wijde omgeving die door alle gebruikers kan worden gelezen. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de globale omgeving, voert u R uit met een van de voor gaande methoden. Vervolgens kunt u de R-pakket beheer uitvoeren `install.packages()` om pakketten te installeren of bij te werken.

**Linux**:

* Uitvoeren in Terminal:

  Open een Terminal en voer uit `R` .  

* Gebruiken in een IDE:

  Gebruik RStudio, geïnstalleerd op de Linux-DSVM.  

* Gebruiken in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitie blok te maken. U kunt het kernel-type instellen op **r** om de Jupyter R kernel (IRKernel) te gebruiken. 

* R-pakketten installeren:

  R is geïnstalleerd op de DSVM in een wereld wijde omgeving die door alle gebruikers kan worden gelezen. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de globale omgeving, voert u R uit met een van de voor gaande methoden. Vervolgens kunt u de R-pakket beheer uitvoeren `install.packages()` om pakketten te installeren of bij te werken.


## <a name="julia"></a>Julia

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde taal versies | 0,6 |
| Ondersteunde DSVM-edities      | Linux, Windows     |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Windows: geïnstalleerd op `C:\JuliaPro-VERSION`<br /> Linux: geïnstalleerd op `/opt/JuliaPro-VERSION`    |
| Koppelingen naar voor beelden      | Voor beelden van Jupyter-notebooks voor Julia zijn opgenomen.     |
| Gerelateerde hulpprogram ma's op de DSVM      | Python, R      |

### <a name="how-to-use-and-run-it"></a>Hoe gebruiken en uitvoeren    

**Windows**:

* Uitvoeren vanaf een opdracht prompt

  Open een opdracht prompt en voer uit `julia` .
* Gebruiken in een IDE:

  Gebruiken `Juno` met de Julia IDE die is geïnstalleerd op de DSVM en beschikbaar is als snelkoppeling op het bureau blad.

* Gebruiken in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitie blok te maken. U kunt het kernel-type instellen als **Julia-versie**.

* Julia-pakketten installeren:

  De standaard locatie voor Julia is een wereld wijde omgeving die door alle gebruikers kan worden gelezen. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Voer Julia uit met behulp van een van de voor gaande methoden om pakketten te installeren in de wereld wijde omgeving. Vervolgens kunt u Julia Package Manager-opdrachten uitvoeren, zoals `Pkg.add()` het installeren of bijwerken van pakketten.


**Linux**:
* Uitvoeren in een Terminal:

  Open een Terminal en voer uit `julia` .
* Gebruiken in een IDE:

  Gebruik `Juno` , waarbij de Julia IDE is geïnstalleerd op de DSVM en beschikbaar is als snelkoppeling voor een **toepassings** menu.

* Gebruiken in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitie blok te maken. U kunt het kernel-type instellen als **Julia-versie**.

* Julia-pakketten installeren:

  De standaard locatie voor Julia is een wereld wijde omgeving die door alle gebruikers kan worden gelezen. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Voer Julia uit met behulp van een van de voor gaande methoden om pakketten te installeren in de wereld wijde omgeving. Vervolgens kunt u Julia Package Manager-opdrachten uitvoeren, zoals `Pkg.add()` het installeren of bijwerken van pakketten.

## <a name="other-languages"></a>Andere talen

**C#**: beschikbaar in Windows en toegankelijk via de Visual Studio Community-editie of de `Developer Command Prompt for Visual Studio` , waar u de opdracht kunt uitvoeren `csc` .

**Java**: openjdk is beschikbaar op zowel de Linux-als Windows-edities van de DSVM en is ingesteld op het pad. Als u Java wilt gebruiken, typt u de `javac` of- `java` opdracht bij een opdracht prompt in Windows of op de bash-shell in Linux.

**Node.js**: Node.js is beschikbaar op zowel de Linux-als Windows-edities van de DSVM en is ingesteld op het pad. Als u toegang wilt krijgen tot Node.js, typt u de `node` of- `npm` opdracht bij een opdracht prompt in Windows of op de bash-shell in Linux. In Windows is de Visual Studio-uitbrei ding voor de Node.js-hulpprogram ma's geïnstalleerd om een grafische IDE te bieden voor het ontwikkelen van uw Node.js-toepassing.

**F #**: beschikbaar in Windows en toegankelijk via de Visual Studio Community Edition of op een `Developer Command Prompt for Visual Studio` , waar u de opdracht kunt uitvoeren `fsc` .
