---
title: Berekenings netwerk op Azure Stack Edge Pro beheren voor toegang tot modules | Microsoft Docs
description: Hierin wordt beschreven hoe u het Compute-netwerk uitbreidt op uw Azure Stack Edge Pro om modules te openen via een extern IP-adres.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 19c92deb58ac51aa882e7123b9a90aa3eae627d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90894109"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Berekenings netwerk op uw Azure Stack Edge Pro inschakelen

In dit artikel wordt beschreven hoe de modules die op uw Azure Stack Edge Pro worden uitgevoerd, toegang hebben tot het berekenings netwerk dat op het apparaat is ingeschakeld.

Als u het netwerk wilt configureren, voert u de volgende stappen uit:

- Een netwerk interface op uw Azure Stack Edge Pro-apparaat inschakelen voor reken kracht
- Een module toevoegen voor toegang tot het Compute-netwerk op uw Azure Stack Edge Pro
- Controleren of de module toegang heeft tot de ingeschakelde netwerk interface

In deze zelf studie gebruikt u een module webserver-app om het scenario te demonstreren.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u het volgende nodig:

- Een Azure Stack Edge Pro-apparaat met de installatie van het apparaat is voltooid.
- U bent klaar met het configureren van de **reken** stap volgens de [zelf studie: gegevens transformeren met Azure stack Edge Pro](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) op het apparaat. Op uw apparaat moet een IoT Hub resource, een IoT-apparaat en een IoT Edge apparaat zijn geïnstalleerd.

## <a name="enable-network-interface-for-compute"></a>Netwerk interface inschakelen voor compute

Als u toegang wilt krijgen tot de modules die op uw apparaat worden uitgevoerd via een extern netwerk, moet u een IP-adres toewijzen aan een netwerk interface op het apparaat. U kunt deze reken instellingen beheren vanuit uw lokale web-gebruikers interface.

Voer de volgende stappen uit op uw lokale webinterface om reken instellingen te configureren.

1. Ga in de lokale web-UI naar **configuratie-instellingen > Compute**.  

2. **Schakel** de netwerk interface in die u wilt gebruiken om verbinding te maken met een compute-module die u op het apparaat uitvoert.

    - Als u statische IP-adressen gebruikt, voert u een IP-adres voor de netwerk interface in.
    - Als u DHCP gebruikt, worden de IP-adressen automatisch toegewezen. In dit voor beeld wordt DHCP gebruikt.

    ![Reken instellingen inschakelen 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selecteer **Toep assen** om de instellingen toe te passen. Noteer het IP-adres dat is toegewezen aan de netwerk interface als u DHCP gebruikt.

    ![Reken instellingen inschakelen](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Webserver-app-module toevoegen

Voer de volgende stappen uit om een module webserver-app op uw Azure Stack Edge Pro-apparaat toe te voegen.

1. Ga naar de IoT Hub bron die is gekoppeld aan uw Azure Stack Edge Pro-apparaat en selecteer vervolgens **IOT edge apparaat**.
2. Selecteer het IoT Edge apparaat dat is gekoppeld aan uw Azure Stack Edge Pro-apparaat. Selecteer **modules instellen** op de details van het **apparaat**. Selecteer **+ toevoegen** in **modules toevoegen** en selecteer vervolgens **IOT Edge module**.
3. Op de Blade **aangepaste modules IOT Edge** :

    1. Geef een **naam** op voor de module webserver-app die u wilt implementeren.
    2. Geef een **afbeeldings-URI** voor de module installatie kopie op. Er wordt een module opgehaald die overeenkomt met de gegeven naam en tags. In dit geval `nginx:stable` wordt een stabiele nginx-installatie kopie (gelabeld als stabiel) opgehaald uit de open bare [docker-opslag plaats](https://hub.docker.com/_/nginx/).
    3. Plak in de opties voor het maken van de **container** de volgende voorbeeld code:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Met deze configuratie kunt u de module openen met behulp van het IP-adres van het Compute-netwerk via *http* op TCP-poort 8080 (met de standaard poort van de webserver 80).

        ![Poort gegevens opgeven in de Blade aangepaste module IoT Edge](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. Selecteer **Opslaan**.

## <a name="verify-module-access"></a>Module toegang controleren

1. Controleer of de module is geïmplementeerd en wordt uitgevoerd. Op de pagina **Details van apparaat** op het tabblad **modules** moet de runtime status van de module worden **uitgevoerd**.  
2. Verbinding maken met de module Web Server app. Open een browser venster en typ het volgende:

    `http://<compute-network-IP-address>:8080`

    U ziet dat de webserver-app wordt uitgevoerd.

    ![Verbinding met module via opgegeven poort controleren](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Gebruikers beheren via Azure Portal](azure-stack-edge-manage-users.md).
