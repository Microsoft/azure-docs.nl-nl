---
title: Micro Focus Enter prise Server 5,0 implementeren naar AKS | Microsoft Docs
description: Host uw IBM z/OS mainframe-workloads met behulp van de micro focus ontwikkelings-en test omgeving op virtuele machines van Azure (Vm's).
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 9e5b3857c2252a939080206fb1f92cc422f326fc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564353"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Micro Focus Enter prise Server 5,0 implementeren naar AKS

In een ander [artikel](./run-enterprise-server-container.md)zijn de stappen beschreven voor het uitvoeren van micro focus Enter prise Server 5,0 in een docker-container. Zo kunt u ook zien hoe u het één stap verder kunt doen en de docker-installatie kopie kunt implementeren die u hebt gemaakt in azure Kubernetes service (AKS).

De Azure Kubernetes-service is een beheerde Orchestration-service op basis van Kubernetes. Hiermee kunt u docker-containers (en andere toepassingen op basis van containers) implementeren, schalen en beheren in een cluster container-hosts.

Dit is een proces dat uit drie stappen bestaat. U moet het volgende doen:

1.  Maak een Azure Container Registry om uw docker-installatie kopie op te slaan.

2.  Maak een Azure Kubernetes-cluster om de docker-installatie kopie uit te voeren.

3.  Voer de toepassing uit.

Zo kunt u uw mainframe-moderniserings werkbelastingen uitschalen (en omlaag schalen) in azure, zodat u het Cloud platform kunt profiteren van waar voor deel.

Voortzetten? Laten we aan de slag gaan!

## <a name="create-the-azure-container-registry"></a>De Azure Container Registry maken

Selecteer in de Azure Portal **een resource maken** in de linkerbovenhoek. Selecteer in het Marketplace-dash board **containers en** vervolgens **container Registry**. Hiermee gaat u naar het deel venster **container register maken** waarin u de **register naam**, het Azure- **abonnement**, de **resource groep** en de **locatie** moet invullen. De **register naam** moet worden omgezet en moet uniek zijn. Selecteer de **resource groep** die u hebt gebruikt in het vorige blog bericht en dezelfde corresponderende **locatie**. Selecteer **inschakelen** voor **gebruiker met beheerders** rechten en **basis** voor de **SKU**. Zodra u alles hebt ingevuld, selecteert u **maken**.

![Container register interface maken](media/deploy-image-1.png)

Nadat het REGI ster is geïmplementeerd, selecteert **u Ga naar resource**. Hiermee gaat u naar de hoofd Blade voor de Azure Container Registry. Hier vindt u de menu optie **snel starten** . Selecteer dit en u ziet instructies voor wat er moet gebeuren om installatie kopieën naar en van het REGI ster te pushen en te halen. We gaan nu het volgende doen:

1.  **Docker installeren** : u hoeft zich geen zorgen te maken over dit probleem, omdat deze al is uitgevoerd.

2.  **Voer de basis installatie kopie ' Hello – World '** opnieuw uit. het is niet nodig om het uit te proberen.

3.  **Meld u aan bij uw container register** . dit moet u doen vanaf de virtuele machine (VM). Kopieer de opdracht naar het klem bord of Klad blok.

    Voor het register dat is gemaakt, is de opdracht: **docker login acrmf50.azurecr.io**

4.  **Naar uw REGI ster pushen** : u moet dit doen voor de micro focus installatie kopie en u bent aangemeld bij de virtuele machine.

5.  **Pull uit uw REGI ster** : het is niet relevant voor deze procedure, maar u kunt het beste weten als u een andere docker-installatie kopie moet uitvoeren.

Voordat u de portal verlaat, moet u de referenties voor het REGI ster ophalen zodat u zich kunt aanmelden. Sluit de Blade **snel starten** af en selecteer **toegangs sleutels** in het menu REGI ster. Kopieer de **gebruikers naam** en een van de **wacht woorden** (er zijn er twee) naar het klem bord of Klad blok. U hebt deze later nodig om u aan te melden.

Nu u weet wat u moet doen, meldt u zich aan bij de virtuele machine.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>RDP naar de virtuele machine die u hebt gebruikt voor het maken van de docker-installatie kopie

Omdat u de docker-installatie kopie al hebt gemaakt op een Windows 2016-server, moet u zich aanmelden bij die VM. Vanuit deze VM kunt u de installatie kopie pushen naar de Azure Container Registry die u zojuist hebt gemaakt. Ga in het Azure Portal naar de virtuele machine en selecteer **overzicht** en vervolgens **verbinding maken**. Hiermee maakt u verbinding met de virtuele machine via Remote Desktop Protocol (RDP). U moet de referenties gebruiken bij het maken van de virtuele machine.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Aanmelden en de installatie kopie naar het REGI ster pushen

Nadat u bent aangemeld, opent u een opdracht prompt en initieert u de volgende docker-opdrachten:

-   **docker-installatie kopieën** : Hiermee wordt een lijst weer gegeven met alle geïnstalleerde installatie kopieën op de VM. Let op de **MicroFocus/es-acctdemo** omdat dit het account is dat u wilt gebruiken.

-   **docker-aanmeldings acrmf50.azurecr.io** : de juiste indeling is *docker login \<registry name\>*. Vervang elke naam die u hebt gebruikt bij het maken van het REGI ster.

    -   U hebt de **gebruikers naam** en het **wacht woord** nodig die u van de Azure Portal hebt gekopieerd. Er verschijnt een tekst die er ongeveer uitziet als in de volgende afbeelding.

    ![Scherm opname van een Administrator opdracht prompt](media/deploy-image-2.png)

-   **MicroFocus van docker-tag/es-acctdemo acrmf50.azurecr.io/es-acctdemo** : Hiermee wordt de juiste installatie kopie met de naam van de opslag plaats gelabeld. **Opmerking**: als de naam \<microfocus/es-acctdemo\> niet werkt, kunt u het beste de volledige installatie kopie-id gebruiken. Nadat u de opdracht hebt uitgevoerd, typt u **docker-installatie kopieën – geen-TRUNC**. De volgende afbeelding ziet er ongeveer als volgt uit. U ziet dat de afbeelding juist is gelabeld.

    ![Opdracht prompt venster voor Administrator selecteren](media/deploy-image-3.png)

-   **docker push acrmf50.azurecr.io/es-acctdemo** : dit is de werkelijke push naar uw opslag plaats. Omdat de grootte 15 GB is, kan het enkele minuten duren voordat deze wordt uitgevoerd. Als alles goed gaat, ziet u iets zoals in de volgende afbeelding.

    ![Opdracht prompt venster voor beheerders](media/deploy-image-4.png)

Ga nu terug naar de Azure Portal, specifiek voor de **opslag plaats**. Selecteer in het menu voor de **opslag plaats** **opslag** plaatsen en u moet **es-acctdemo** weer gegeven. Maak nu het AKS-cluster.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Het Azure Kubernetes (AKS)-cluster maken

Selecteer in de Azure Portal de optie **een resource maken** en vervolgens de **service containers/Kubernetes** in het menu **Marketplace** . Vervolgens moet u de Blade Kubernetes- **cluster maken** invullen. Zorg ervoor dat u het cluster in dezelfde regio en resource groep hebt die u hebt gebruikt. U kunt de rest van de standaard waarden accepteren, behalve het **aantal knoop punten** . dit hoeft alleen 1 te zijn. Als u klaar bent, selecteert u **Beoordelen en maken**.

![Het Kubernetes-cluster scherm maken](media/deploy-image-5.png)

Wanneer dit is voltooid, plaatst de implementatie de **Kubernetes-service** artefacten in de **resource groep** die u hebt geselecteerd op de Blade. Het daad werkelijke cluster heeft echter een eigen resource groep die tijdens de implementatie is gemaakt. Als u **resource groepen** in het menu aan de linkerkant selecteert, kunt u deze vinden op basis van de naam Conventie. Dit is een afbeelding van mijn: het is de laatste van de lijst.

![Scherm opname van resource groepen](media/deploy-image-6.png)

**De installatiekopie uitvoeren**

Nu is het tijd om de installatie kopie te halen en uit te voeren in AKS. De eenvoudigste manier om dit te doen vanuit het Azure Portal is door gebruik te maken van de Cloud Shell. U vindt het pictogram in de rechter bovenhoek van de Azure Portal. Houd er rekening mee dat in dit scenario de bash-shell wordt gebruikt.

![Scherm afbeelding van het Cloud Shell pictogram](media/deploy-image-7.png)

Zodra de shell is geladen, typt u de volgende opdracht:

**kubectl uitvoeren es-acctdemo--image acrmf50.azurecr.io/es-acctdemo--Port = 9040**

Hiermee haalt u de installatie kopie op uit de **acrmf50.azurecr.io** -opslag plaats en laadt u deze in AKS. Vervolgens wordt de installatie kopie met poort 9040 geopend. U kunt dit intrekken voor de poort die u voor uw docker-installatie kopie had geopend. U hebt toegang tot de Enter prise-server nodig.

Kubernetes moet reageren met een bericht dat de implementatie is gemaakt.

![Scherm opname van een implementatie bericht](media/deploy-image-8.jpg)

Als u wilt zien of de container daad werkelijk wordt uitgevoerd, typt u: **kubectl alles ophalen**.

Als een actief pod moet u es-acctdemo zien, zoals in de volgende afbeelding.

![Scherm afbeelding es-acctdemo als een actief pod](media/deploy-image-9.png)

Gefeliciteerd U voert nu de Enter prise-server uit in de Azure Kubernetes-service. In het volgende artikel wordt uitgelegd hoe u toegang krijgt tot de beheer console van de Enter prise-server en hoe u Kubernetes kunt gebruiken om uw implementatie uit te breiden.
