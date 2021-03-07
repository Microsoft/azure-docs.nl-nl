---
title: Uw Azure Stack Edge Pro-apparaat bewaken via Kubernetes-dash board | Microsoft Docs
description: Hierin wordt beschreven hoe u het Kubernetes-dash board opent en gebruikt om uw Azure Stack Edge Pro-apparaat te bewaken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 19b3595228c29814e42af88696972fd81b156190
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443043"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes-dash board gebruiken om uw Azure Stack Edge Pro GPU-apparaat te bewaken

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u het Kubernetes-dash board opent en gebruikt om uw Azure Stack Edge Pro GPU-apparaat te bewaken. Als u uw apparaat wilt bewaken, kunt u grafieken gebruiken in Azure Portal, het Kubernetes-dash board weer geven of `kubectl` opdrachten uitvoeren via de Power shell-interface van het apparaat. 

Dit artikel richt zich alleen op de bewakings taken die kunnen worden uitgevoerd op het Kubernetes-dash board.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Toegang tot het Kubernetes-dash board op uw apparaat
> * Modules weer geven die op uw apparaat zijn geïmplementeerd
> * IP-adres ophalen voor toepassingen die op uw apparaat zijn geïmplementeerd
> * Container logboeken weer geven voor modules die op uw apparaat zijn geïmplementeerd


## <a name="about-kubernetes-dashboard"></a>Over Kubernetes-dash board

Kubernetes Dashboard is een gebruikersinterface op het web die u kunt gebruiken voor het oplossen van problemen met in containers geplaatste toepassingen. Het Kubernetes-dash board is een alternatief voor de Kubernetes- `kubectl` opdracht regel. Zie [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)voor meer informatie. 

Op uw Azure Stack Edge Pro-apparaat kunt u het Kubernetes-dash board in de modus *alleen-lezen* gebruiken om een overzicht te krijgen van de toepassingen die worden uitgevoerd op uw Azure stack Edge Pro-apparaat, de status van Kubernetes-cluster bronnen weer geven en eventuele fouten te bekijken die op het apparaat zijn opgetreden.

## <a name="access-dashboard"></a>Access-dash board

Het Kubernetes-dash board is *alleen-lezen* en wordt uitgevoerd op het Kubernetes-hoofd knooppunt op poort 31000. Volg deze stappen om toegang te krijgen tot het dash board: 

1. Ga in de lokale gebruikers interface van uw apparaat naar **apparaat** en ga vervolgens naar **eind punten** van het apparaat. 
1. Selecteer **Download configuratie** om een te downloaden `kubeconfig` waarmee u toegang krijgt tot het dash board. Sla het `config.json` bestand op uw lokale systeem op.
1. Selecteer de URL van het Kubernetes-dash board om het dash board in een browser te openen.

    ![URL van Kubernetes-dash board op de pagina apparaat in lokale gebruikers interface](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Op de aanmeldings pagina van het **Kubernetes-dash board** :
    
    1. Selecteer **kubeconfig**. 
        ![Selecteer de optie kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. Het weglatings teken selecteren.. **.** Blader naar en ga naar de `kubeconfig` versie die u eerder hebt gedownload op uw lokale systeem. Selecteer **Aanmelden**.
        ![Bladeren naar het kubeconfig-bestand](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. U kunt nu het Kubernetes-dash board weer geven voor uw Azure Stack Edge Pro-apparaat in de modus alleen-lezen.

    ![Hoofd pagina van Kubernetes-dash board](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Module status weer geven

Compute-modules zijn containers waarvoor een bedrijfs logica is geïmplementeerd. U kunt het dash board gebruiken om te controleren of een compute-module is geïmplementeerd op uw Azure Stack Edge Pro-apparaat.

Volg deze stappen in het dash board om de module status weer te geven:

1. Ga in het linkerdeel venster van het dash board naar de **naam ruimte**. Filter op de naam ruimte waar IoT Edge modules worden weer gegeven, in dit geval **iotedge**.
1. Ga in het linkerdeel venster naar **workloads > implementaties**.
1. In het rechterdeel venster ziet u alle modules die op uw apparaat zijn geïmplementeerd. In dit geval is er een GettingStartedWithGPU-module geïmplementeerd op de Azure Stack Edge Pro. U kunt zien dat de module is geïmplementeerd.

    ![Module-implementatie weer geven](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>IP-adres voor services of modules ophalen

U kunt het dash board gebruiken om de IP-adressen op te halen van de services of modules die u buiten het Kubernetes-cluster wilt weer geven. 

U wijst het IP-bereik voor deze externe services toe via de lokale web-UI van het apparaat op de pagina instellingen van het **berekenings netwerk** . Nadat u IoT Edge-modules hebt geïmplementeerd, kunt u het IP-adres ophalen dat is toegewezen aan een specifieke module of service. 

Voer de volgende stappen uit op het dash board om het IP-adres op te halen:

1. Ga in het linkerdeel venster van het dash board naar de **naam ruimte**. Filter op de naam ruimte waarin een externe service is geïmplementeerd, in dit geval **iotedge**.
1. Ga in het linkerdeel venster naar detectie- **en taak verdeling > Services**.
1. In het rechterdeel venster ziet u alle services die worden uitgevoerd in de `iotedge` naam ruimte op uw Azure stack Edge Pro-apparaat.

    ![IP ophalen voor externe services](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Containerlogboeken ophalen

Er zijn exemplaren waarvoor u de container logboeken wilt weer geven. U kunt het dash board gebruiken om logboeken op te halen voor een specifieke container die u hebt geïmplementeerd op uw Kubernetes-cluster.

Als u de container logboeken wilt weer geven, voert u de volgende stappen uit op het dash board:

1. Ga in het linkerdeel venster van het dash board naar de **naam ruimte**. Filter op de naam ruimte waarin de IoT Edge-modules zijn geïmplementeerd, in dit geval **iotedge**.
1. Ga in het linkerdeel venster naar **werk belastingen > peul**.
1. In het rechterdeel venster ziet u alle op het apparaat uitgevoerde peulen. Zoek de pod die de module uitvoert waarvoor u de logboeken wilt weer geven. Selecteer de verticale beletsel tekens voor de pod die u hebt geïdentificeerd en selecteer **Logboeken** in het context menu.

    ![Container logboeken weer geven 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. De logboeken worden weer gegeven in een logboeken viewer die is ingebouwd in het dash board. U kunt de logboeken ook downloaden.

    ![Container logboeken weer geven 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>CPU, geheugen gebruik weer geven

Het Kubernetes-dash board voor Azure Stack Edge Pro-apparaat heeft ook een [invoeg toepassing voor metrische gegevens](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) die het CPU-en geheugen gebruik in Kubernetes-bronnen samenvoegt.
 
U kunt bijvoorbeeld de CPU en het geheugen weer geven die zijn verbruikt over implementaties in alle naam ruimten. 

![Het CPU-en geheugen gebruik in alle implementaties weer geven](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

U kunt ook filteren op een specifieke naam ruimte. In het volgende voor beeld kunt u het CPU-en geheugen gebruik alleen bekijken voor Azure Arc-implementaties.  

![CPU-en geheugen gebruik voor Azure Arc-implementaties weer geven](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

De Kubernetes Metrics-server biedt automatisch schalen van pijp lijnen, zoals [horizontale pod-automatisch schalen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [oplossen van problemen met apparaten](azure-stack-edge-gpu-troubleshoot.md).
