---
title: Reken beheer van Azure Stack Edge Pro GPU | Microsoft Docs
description: Hierin wordt beschreven hoe u de instellingen voor Edge-berekening beheert, zoals trigger, modules, compute-configuratie weer geven, configuratie verwijderen via de Azure Portal op uw Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 057f80049b13a265925f2d6f24a008d1e1e04c96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638070"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Reken kracht beheren op uw Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u Compute beheert via IoT Edge-service op uw Azure Stack Edge Pro GPU-apparaat. U kunt de compute beheren via de Azure Portal of via de lokale web-UI. Gebruik de Azure Portal om modules, triggers en IoT Edge configuratie te beheren, en de lokale web-UI voor het beheren van de instellingen van het reken netwerk.



## <a name="manage-triggers"></a>Triggers beheren

Gebeurtenissen zijn de dingen die gebeuren in uw cloudomgeving of op uw apparaat op basis waarvan u mogelijk actie wilt ondernemen. Wanneer bijvoorbeeld een bestand wordt gemaakt in een share, is dit een gebeurtenis. Triggers veroorzaken de gebeurtenissen. Voor uw Azure Stack Edge Pro kunnen triggers worden in reactie op bestands gebeurtenissen of een schema.

- **Bestand**: deze triggers zijn in antwoord op bestands gebeurtenissen, zoals het maken van een bestand, het wijzigen van een bestand.
- **Gepland**: deze triggers bevinden zich in antwoord op een schema dat u kunt definiëren met een begin datum, begin tijd en het herhalings interval.


### <a name="add-a-trigger"></a>Een trigger toevoegen

Voer de volgende stappen uit in de Azure Portal om een trigger te maken.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar **IOT Edge**. Ga naar **Triggers** en selecteer **+ trigger toevoegen** op de opdracht balk.

    ![Trigger toevoegen selecteren](media/azure-stack-edge-gpu-manage-compute/add-trigger-1-m.png)

2. Geef in de Blade **trigger toevoegen** een unieke naam op voor de trigger.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selecteer een **type** voor de trigger. Kies **bestand** wanneer de trigger zich in reactie op een bestands gebeurtenis bevindt. Selecteer **gepland** wanneer u wilt dat de trigger op een bepaald tijdstip start en voer uit met een opgegeven herhalings interval. Afhankelijk van uw selectie wordt een andere set opties weer gegeven.

    - **Bestands trigger** : Kies een gekoppelde share in de vervolg keuzelijst. Wanneer een bestands gebeurtenis in deze share wordt geactiveerd, wordt er een Azure-functie aangeroepen door de trigger.

        ![SMB-share toevoegen](media/azure-stack-edge-gpu-manage-compute/add-file-trigger.png)

    - **Geplande trigger** : Geef de begin datum/-tijd en het herhalings interval in uren, minuten of seconden op. Voer ook de naam in voor een onderwerp. Een onderwerp biedt u de flexibiliteit om de trigger te routeren naar een module die op het apparaat is geïmplementeerd.

        Een voor beeld van een route teken reeks is: `"route3&quot;: &quot;FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint(&quot;modules/modulename/inputs/input1")"` .

        ![NFS-share toevoegen](media/azure-stack-edge-gpu-manage-compute/add-scheduled-trigger.png)

4. Selecteer **toevoegen** om de trigger te maken. Er wordt een melding weer gegeven dat het maken van de trigger wordt uitgevoerd. Nadat de trigger is gemaakt, wordt de Blade bijgewerkt om de nieuwe trigger weer te geven.
 
    ![Bijgewerkte trigger lijst](media/azure-stack-edge-gpu-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Een trigger verwijderen

Voer de volgende stappen uit in de Azure Portal om een trigger te verwijderen.

1. Selecteer de trigger die u wilt verwijderen in de lijst met triggers.

    ![Trigger selecteren](media/azure-stack-edge-gpu-manage-compute/delete-trigger-1.png)

2. Klik met de rechter muisknop en selecteer **verwijderen**.

    ![Verwijderen selecteren](media/azure-stack-edge-gpu-manage-compute/delete-trigger-2.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd.

    ![De verwijdering bevestigen](media/azure-stack-edge-gpu-manage-compute/delete-trigger-3.png)

De lijst met triggers-updates om de verwijdering weer te geven.

## <a name="manage-iot-edge-configuration"></a>IoT Edge configuratie beheren

Gebruik de Azure Portal om de compute-configuratie weer te geven, een bestaande Compute-configuratie te verwijderen of de compute-configuratie te vernieuwen om toegangs sleutels voor het IoT-apparaat en IoT Edge apparaat voor uw Azure Stack Edge Pro te synchroniseren.

### <a name="view-iot-edge-configuration"></a>IoT Edge configuratie weer geven

Voer de volgende stappen uit in de Azure Portal om de IoT Edge configuratie voor uw apparaat weer te geven.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar **IOT Edge**. Nadat de IoT Edge-service op uw apparaat is ingeschakeld, wordt in de pagina overzicht aangegeven dat de IoT Edge-service goed wordt uitgevoerd.

    ![Reken proces weer geven selecteren](media/azure-stack-edge-gpu-manage-compute/view-compute-1.png)

2. Ga naar **Eigenschappen** om de IOT Edge configuratie op het apparaat weer te geven. Wanneer u Compute hebt geconfigureerd, hebt u een IoT Hub resource gemaakt. Onder die IoT Hub resource worden een IoT-apparaat en een IoT Edge-apparaat geconfigureerd. Alleen de linux-modules worden ondersteund voor uitvoering op het IoT Edge-apparaat.

    ![Configuratie weergeven](media/azure-stack-edge-gpu-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>IoT Edge-service verwijderen

Voer de volgende stappen uit in de Azure Portal om de bestaande IoT Edge configuratie voor uw apparaat te verwijderen.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar **IOT Edge**. Ga naar **overzicht** en selecteer **verwijderen** op de opdracht balk.

    ![Selecteer reken proces verwijderen](media/azure-stack-edge-gpu-manage-compute/remove-compute-1.png)

2. Als u de IoT Edge-service verwijdert, is de actie onomkeerbaar en kan niet ongedaan worden gemaakt. De modules en triggers die u hebt gemaakt, worden ook verwijderd. U moet het apparaat opnieuw configureren voor het geval u IoT Edge opnieuw moet gebruiken. Als u om bevestiging wordt gevraagd, selecteert u **OK**.

    ![Selecteer Compute 2 verwijderen](media/azure-stack-edge-gpu-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT-apparaat en toegangs sleutels voor IoT Edge apparaten synchroniseren

Wanneer u Compute op uw Azure Stack Edge Pro configureert, worden er een IoT-apparaat en een IoT Edge apparaat gemaakt. Aan deze apparaten worden automatisch symmetrische toegangs sleutels toegewezen. Als beveiligings best practice worden deze sleutels regel matig gedraaid via de IoT Hub-service.

Als u deze sleutels wilt draaien, gaat u naar de IoT Hub-service die u hebt gemaakt en selecteert u het IoT-apparaat of het IoT Edge-apparaat. Elk apparaat heeft een primaire toegangs sleutel en secundaire toegangs sleutels. Wijs de primaire toegangs sleutel toe aan de secundaire toegangs sleutel en Genereer de primaire toegangs sleutel opnieuw.

Als uw IoT-apparaat en IoT Edge-apparaatklassen zijn geroteerd, moet u de configuratie op uw Azure Stack Edge Pro vernieuwen om de nieuwste toegangs sleutels te verkrijgen. De synchronisatie helpt het apparaat de meest recente sleutels te verkrijgen voor uw IoT-apparaat en IoT Edge apparaat. Azure Stack Edge Pro maakt alleen gebruik van de primaire toegangs sleutels.

Voer de volgende stappen uit in de Azure Portal om de toegangs sleutels voor uw apparaat te synchroniseren.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar **IOT Edge Compute**. Ga naar **overzicht** en selecteer **configuratie vernieuwen** op de opdracht balk.

    ![Selecteer configuratie vernieuwen](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-1.png)

2. Selecteer **Ja** als u om bevestiging wordt gevraagd.

    ![Selecteer Ja wanneer u hierom wordt gevraagd](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-2.png)

3. Sluit het dialoogvenster wanneer de synchronisatie is voltooid.

## <a name="change-external-service-ips-for-containers"></a>Externe service Ip's voor containers wijzigen

Kubernetes External service Ip's worden gebruikt om te bereiken naar services die buiten het Kubernetes-cluster worden weer gegeven. Nadat het apparaat is geactiveerd, kunt u de IP-adressen van de externe service voor het apparaat in containers instellen of wijzigen door de lokale gebruikers interface te openen.


1. Ga in de lokale gebruikers interface van het apparaat naar **Compute**.
1. Selecteer de poort waarvan het netwerk is geconfigureerd voor compute. Op de Blade die wordt geopend, geeft u (nieuw) of wijzigen (indien bestaande) de Kubernetes externe service Ip's. Deze IP-adressen worden gebruikt voor alle services die buiten het Kubernetes-cluster moeten worden weer gegeven. 
    - U hebt mini maal 1 service-IP nodig voor de `edgehub` service die op uw apparaat wordt uitgevoerd en wordt gebruikt door IOT Edge modules. 
    - U hebt een IP-adres nodig voor elke aanvullende IoT Edge module of container die u wilt implementeren. 
    - Dit zijn statische, aaneengesloten IP-adressen.

    ![Ip's van Kubernetes-service wijzigen](media/azure-stack-edge-gpu-manage-compute/change-service-ips-1.png)

1. Selecteer **Toepassen**. Nadat de IP-adressen zijn toegepast, hoeft het apparaat niet opnieuw te worden opgestart of opnieuw op te starten. De nieuwe IP-adressen worden onmiddellijk van kracht.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het oplossen van problemen met uw Azure stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md).
