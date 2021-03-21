---
title: De Azure VMware-oplossing verkleinen door de CloudSimple-Privécloud
description: Meer informatie over het dynamisch verkleinen van een Privécloud in CloudSimple door een knoop punt uit een bestaand vSphere-cluster te verwijderen of een volledig cluster te verwijderen.
author: Ajayan1008
ms.author: v-hborys
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a99b9b56f17b78a98f37d47dcefab26dd9c859de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899130"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Een CloudSimple-Privécloud verkleinen

CloudSimple biedt de flexibiliteit voor het dynamisch verkleinen van een Privécloud.  Een Privécloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knoop punten bevatten. Bij het verkleinen van een Privécloud verwijdert u een knoop punt uit het bestaande cluster of verwijdert u een volledig cluster. 

## <a name="before-you-begin"></a>Voordat u begint

Voor het verkleinen van een Privécloud moet aan de volgende voor waarden worden voldaan.  Het beheer cluster (eerste cluster) dat is gemaakt tijdens het maken van een Privécloud, kan niet worden verwijderd.

* Een vSphere-cluster moet drie knoop punten hebben.  Een cluster met slechts drie knoop punten kan niet worden verkleind.
* De totale capaciteit die wordt verbruikt, mag niet groter zijn dan het aantal te verkleinen van het cluster.
* Controleer of een DRS-regel (Distributed resource Scheduler) voor komt dat vMotion van een virtuele machine wordt verhinderd.  Als er regels aanwezig zijn, schakelt u de regels uit of verwijdert u deze.  DRS-regels bevatten virtuele machine voor regels voor affiniteit met de host.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Een privécloud verkleinen

1. [Toegang tot de CloudSimple-Portal](access-cloudsimple-portal.md).

2. Open de pagina **resources** .

3. Klik op de Privécloud die u wilt verkleinen

4. Klik op de pagina samen vatting op **verkleinen**.

    ![Persoonlijke Cloud verkleinen](media/shrink-private-cloud.png)

5. Selecteer het cluster dat u wilt verkleinen of verwijderen. 

    ![Private Cloud verkleinen-cluster selecteren](media/shrink-private-cloud-select-cluster.png)

6. Selecteer **een knoop punt verwijderen** of **Verwijder het hele cluster**. 

7. De cluster capaciteit controleren

8. Klik op **verzenden** om de privécloud te verkleinen.

Het verkleinen van de Privécloud wordt gestart.  U kunt de voortgang in taken bewaken.  Het verkleinings proces kan enkele uren duren, afhankelijk van de gegevens, die opnieuw moeten worden gesynchroniseerd op vSAN.

> [!NOTE]
> 1. Als u een privécloud verkleint door de laatste of het enige cluster in het Data Center te verwijderen, wordt het Data Center niet verwijderd.
> 2. Als er sprake is van een schending van de DRS-regel, wordt het knoop punt niet verwijderd uit het cluster en wordt in de taak beschrijving aangegeven dat het verwijderen van een knoop punt in strijd is met DRS-regels op het cluster.    


## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [persoonlijke Clouds](cloudsimple-private-cloud.md)
