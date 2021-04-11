---
title: Zelfstudie voor het maken van verbinding met Azure Stack Edge Mini R in Azure Portal
description: Leer hoe u verbinding met uw Azure Stack Edge Mini R-apparaat kunt maken door de lokale webgebruikersinterface te gebruiken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 57d11e1c4109caded4287592a6d71a803e44b68e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061601"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Zelfstudie: Verbinding maken met Azure Stack Edge Mini R

In deze zelfstudie wordt beschreven hoe u verbinding kunt maken met uw Azure Stack Edge Mini R-apparaat via de lokale webgebruikersinterface.

Het verbindingsproces kan ongeveer vijf minuten duren.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met een fysiek apparaat



## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u uw Azure Stack Edge-apparaat configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge installeren](azure-stack-edge-mini-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Verbinding maken met de lokale gebruikersinterface instellen

1. Configureer de Ethernet-adapter op de computer die u wilt verbinden met het Azure Stack Edge Pro-apparaat met het vaste IP-adres 192.168.100.5 en het subnet 255.255.255.0.

2. Verbind de computer met poort 1 op het apparaat. Als u de computer rechtstreeks op het apparaat wilt aansluiten (zonder een switch), gebruikt u een crossover-kabel of een USB-ethernetadapter. Gebruik de volgende afbeelding om poort 1 op uw apparaat te vinden.

    ![Bekabeling voor wifi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Verbinding maken met een fysiek apparaat


Als u wilt weten hoe u netwerkinstellingen op uw apparaat configureert, raadpleegt u:

> [!div class="nextstepaction"]
> [Netwerk configureren](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
