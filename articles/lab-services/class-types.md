---
title: Voor beeld klassen typen op Azure Lab Services | Microsoft Docs
description: Biedt een aantal typen klassen waarvoor u Labs kunt instellen met behulp van Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a90fb128f5954f3eb713714ff22ff40a3beab36
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627430"
---
# <a name="class-types-overview---azure-lab-services"></a>Overzicht van klassen typen-Azure Lab Services

Met Azure Lab Services kunt u klassikale test omgevingen snel instellen in de Cloud. De artikelen in deze sectie bieden richt lijnen voor het instellen van verschillende soorten Labs met behulp van Azure Lab Services.

## <a name="arcgis"></a>ArcGIS
[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) is een type geografisch informatie systeem (GIS).  U kunt een Lab instellen dat gebruikmaakt van verschillende toepassingen van ArcGIS Desktop, zoals [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) , om 2D-kaarten te maken, te bewerken en te analyseren.

Zie [een Lab instellen voor ArcMap\ArcGIS Desktop](class-type-arcgis.md)voor meer informatie over het instellen van dit type Lab.

## <a name="big-data-analytics"></a>Big data-analyse
U kunt een GPU-Lab instellen om een big data Analytics-klasse te leren. Met dit type klasse leren cursisten hoe ze grote hoeveel heden gegevens verwerken en de algoritmen voor machine-en statistische lessen Toep assen om gegevens inzichten af te leiden. Een belang rijke doel stelling voor studenten is het leren gebruiken van hulpprogram ma's voor gegevens analyse, zoals het open-source software pakket van Apache Hadoop waarmee u big data kunt opslaan, beheren en verwerken. 

Zie [een Lab instellen voor Big Data Analytics met docker-implementatie van HortonWorks-gegevens platform](class-type-big-data-analytics.md)voor meer informatie over het instellen van dit type Lab.

## <a name="database-management"></a>Databasebeheer
Data bases zijn concepten van een van de in de meeste computer wetenschappen geschoolde opleidingen. U kunt een Lab instellen voor een basis beheer klasse voor data bases in Azure Lab Services. U kunt bijvoorbeeld een virtuele-machine sjabloon in een Lab instellen met een [MySQL](https://www.mysql.com/) -database server of een [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) -server.

Voor gedetailleerde informatie over het instellen van dit type Lab, Zie [een Lab instellen voor het leren van database beheer voor relationele data bases](class-type-database-management.md).

## <a name="deep-learning-in-natural-language-processing"></a>Deep Learning in natuurlijke taalverwerking
U kunt een Lab richten op diep leren in natuurlijke taal verwerking (NLP) met behulp van Azure Lab Services. De verwerking van natuurlijke taal (NLP) is een vorm van kunst matige intelligentie (AI) waarmee computers kunnen beschikken over mogelijkheden voor vertaling, spraak herkenning en andere talen. Studenten die een NLP-klasse gebruiken, halen een virtuele Linux-machine (VM) op voor meer informatie over het Toep assen van Neural-netwerk algoritmen voor het ontwikkelen van diepe leer modellen die worden gebruikt voor het analyseren van geschreven menselijke talen.

Zie voor gedetailleerde informatie over het instellen van dit type Lab [een Lab instellen die gericht is op diep gaande lessen in natuurlijke taal verwerking met behulp van Azure Lab Services](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking"></a>Ethisch hacken
U kunt een Lab instellen voor een klasse die zich richt op de forensische-kant van ethische hacking. Indringings tests, een praktijk die wordt gebruikt door de ethische hacking-Community, treedt op wanneer iemand probeert toegang te krijgen tot het systeem of netwerk om beveiligings problemen te demonstreren die een kwaadwillende aanvaller kan misbruiken.

In een ethische hacking-klasse kunnen studenten moderne technieken leren voor het beschermen tegen beveiligings problemen. Elke student haalt een virtuele Windows Server-host op met twee geneste virtuele machines: één virtuele machine met [Metasploitable3](https://github.com/rapid7/metasploitable3) -installatie kopie en een andere machine met [Kali Linux](https://www.kali.org/) -installatie kopie. De virtuele machine Metasploitable wordt gebruikt voor het exploiteren van toepassingen.  De virtuele machine met Kali Linux biedt toegang tot de hulpprogram ma's die nodig zijn om forensische-taken uit te voeren.

Voor gedetailleerde informatie over het instellen van dit type Lab raadpleegt u [een Lab instellen om ethische hacking-klasse te leren](class-type-ethical-hacking.md).

## <a name="matlab"></a>MATLAB
[MATLAB](https://www.mathworks.com/products/matlab.html), die staat voor matrix laboratorium, bevindt zich in het programmeer platform van [MathWorks](https://www.mathworks.com/).  Het combineert reken kracht en visualisatie die het populaire hulp programma maakt in de velden van wiskunde, techniek, natuur kunde en schei kunde.

Voor gedetailleerde informatie over het instellen van dit type Lab raadpleegt [u een Lab instellen om MATLAB te leren](class-type-matlab.md).

## <a name="networking-with-gns3"></a>Netwerken met GNS3
U kunt een Lab instellen voor een klasse die gericht is op het toestaan van studenten, het configureren, testen en oplossen van problemen met virtuele en Real Networks met behulp van [GNS3](https://www.gns3.com/) -software. 

Voor gedetailleerde informatie over het instellen van dit type Lab raadpleegt [u een Lab instellen voor het leren van een netwerk klasse](class-type-networking-gns3.md).

## <a name="project-lead-the-way-pltw"></a>Project leider (PLTW)
[Project leider (PLTW)](https://www.pltw.org/) is een non-profit organisatie die PreK-12-leer plan biedt over de Verenigde Staten in computer wetenschappen, engineering en biomedische wetenschap.  In elke PLTW-klasse gebruiken studenten diverse software toepassingen als onderdeel van hun praktijk ervaring.

Zie voor meer informatie over het instellen van deze typen Labs [Labs instellen voor project leider, zoals klassen](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Python-en Jupyter-notebooks
U kunt een sjabloon machine instellen in Azure Lab Services met de hulpprogram ma's die nodig zijn om cursisten te leren hoe ze [Jupyter-notebooks](http://jupyter-notebook.readthedocs.io)kunnen gebruiken. Jupyter-notebooks is een open-source project waarmee u eenvoudig uitgebreide tekst en uitvoer bare [python](https://www.python.org/) -bron code kunt combi neren op één canvas dat een notitie blok wordt genoemd. Het uitvoeren van een notitie blok resulteert in een lineaire record met invoer en uitvoer.  Deze uitvoer kan tekst, tabel met gegevens, spreidings grafieken en meer zijn.

Zie [een Lab instellen om data Science te leren met python-en Jupyter-notebooks](class-type-jupyter-notebook.md)voor gedetailleerde informatie over het instellen van dit type Lab.

## <a name="shell-scripting-on-linux"></a>Shell-scripts in Linux
U kunt een Lab instellen om shell scripting op Linux te leren. Scripting is een nuttig onderdeel van systeem beheer waarmee beheerders terugkerende taken kunnen voor komen. In dit voorbeeld scenario bestrijkt de klasse traditionele bash-scripts en uitgebreide scripts. Uitgebreide scripts zijn scripts die bash-opdrachten en Ruby combi neren. Met deze benadering kan ruby gegevens rond en bash opdrachten door geven om met de shell te communiceren.

Studenten die deze script klassen nemen, krijgen een virtuele Linux-machine voor het leren van de basis principes van Linux en kunnen ook vertrouwd raken met de bash-shell scripts. Voor de virtuele Linux-machine is toegang tot extern bureau blad ingeschakeld en met [gedit](https://help.gnome.org/users/gedit/stable/) en [Visual Studio code](https://code.visualstudio.com/) text editors geïnstalleerd.

Zie [shell scripting op Linux](class-type-shell-scripting-linux.md)voor meer informatie over het instellen van dit type Lab.

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks computer-aided design (CAD)
U kunt een GPU-Lab instellen dat technisch studenten toegang biedt tot [SolidWorks](https://www.solidworks.com/).  SolidWorks biedt een 3D-CAD-omgeving voor het model leren van solide objecten.  Met SolidWorks kunnen technici hun ontwerpen eenvoudig maken, visualiseren, simuleren en documenteren.

Zie [een Lab instellen voor technische klassen met SolidWorks](class-type-solidworks.md)voor gedetailleerde informatie over het instellen van dit type Lab.

## <a name="sql-database-and-management"></a>SQL database en-beheer
Structured Query Language (SQL) is de standaard taal voor relationeel database beheer, waaronder het toevoegen, openen en beheren van inhoud in een Data Base.  U kunt een Lab instellen om database concepten te leren met behulp van de [MySQL](https://www.mysql.com/) -database server en [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) -server.

Voor gedetailleerde informatie over het instellen van dit type Lab, Zie [een Lab instellen voor het leren van database beheer voor relationele data bases](class-type-database-management.md).

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Stel een lab in dat is gericht op diep gaande lessen in de verwerking van natuurlijke taal met behulp van Azure Lab Services](class-type-deep-learning-natural-language-processing.md)
- [Shell-scripts in Linux](class-type-shell-scripting-linux.md)
- [Ethisch hacken](class-type-ethical-hacking.md)