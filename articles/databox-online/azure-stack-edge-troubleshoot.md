---
title: Gebruik de Azure Portal voor het oplossen van problemen met Azure Stack Edge Pro | Microsoft Docs
description: Meer informatie over het oplossen van problemen met uw Azure Stack Edge Pro. U kunt Diagnostische gegevens uitvoeren, informatie voor ondersteuning verzamelen en Logboeken gebruiken om problemen op te lossen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: ff6d1f46299875354a72a6a93a828df7960169ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831536"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-issues"></a>Problemen met uw Azure Stack Edge Pro oplossen

In dit artikel wordt beschreven hoe u problemen kunt oplossen met uw Azure Stack Edge Pro. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Diagnose uitvoeren
> * Ondersteuningspakket verzamelen
> * Logboeken gebruiken om problemen op te lossen
> * Problemen met IoT Edge oplossen

## <a name="run-diagnostics"></a>Diagnose uitvoeren

U kunt de diagnostische tests uitvoeren om apparaatfouten vast te stellen en op te lossen. Voer de volgende stappen uit in de lokale webinterface van uw apparaat om diagnostische tests uit te voeren.

1. Ga in de lokale webinterface naar **Probleemoplossing > Diagnostische tests**. Selecteer de test die u wilt uitvoeren en selecteer **test uitvoeren**. Hiermee voert u de tests uit om mogelijke problemen met uw netwerk-, apparaat-, webproxy-, tijd- of cloudinstellingen vast te stellen. U wordt gewaarschuwd als er tests worden uitgevoerd op het apparaat.

    ![Testen selecteren](media/azure-stack-edge-troubleshoot/run-diag-1.png)

2. Wanneer de tests zijn voltooid, worden de resultaten weergegeven.

    ![Testresultaten evalueren](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    Als een test mislukt, wordt er een URL voor de aanbevolen actie weergegeven. Selecteer de URL om de aanbevolen actie weer te geven.

    ![Waarschuwingen voor mislukte testen controleren](media/azure-stack-edge-troubleshoot/run-diag-3.png)

## <a name="collect-support-package"></a>Ondersteuningspakket verzamelen

Er wordt een logboekpakket samengesteld van alle relevante logboeken die Microsoft Ondersteuning kunnen helpen bij het oplossen van eventuele problemen met het apparaat. U kunt een logboekpakket genereren via de lokale webinterface.

Voer de volgende stappen uit om een ondersteuningspakket te verzamelen.

1. Ga in de lokale webinterface naar **Probleemoplossing > Ondersteuning**. Selecteer **ondersteunings pakket maken**. Het systeem begint met het verzamelen van het ondersteuningspakket. Het verzamelen van het pakket kan enkele minuten duren.

    ![Klikken op Gebruiker toevoegen](media/azure-stack-edge-troubleshoot/collect-logs-1.png)

2. Nadat het ondersteunings pakket is gemaakt, selecteert u **ondersteunings pakket downloaden**. Er wordt een gecomprimeerd pakket gedownload naar het pad dat u hebt gekozen. U kunt het pakket uitpakken en de systeemlogboekbestanden bekijken.

    ![Klik op gebruiker toevoegen 2](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Logboeken gebruiken om problemen op te lossen

Eventuele fouten die zich hebben voorgedaan tijdens de upload- en vernieuwingsprocessen zijn opgenomen in de respectieve foutbestanden.

1. Als u de fout bestanden wilt weer geven, gaat u naar uw share en selecteert u deze om de inhoud weer te geven. 

      ![Verbinding maken met en de share-inhoud weer geven](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. Selecteer de _map Microsoft Azure stack Edge Pro_. Deze map heeft twee submappen:

    * uploadmap met logboekbestanden van fouten tijdens het uploaden.
    * vernieuwingsmap met logboekbestanden van fouten tijdens het vernieuwen.

    Hier volgt een voorbeeld van een logboekbestand voor vernieuwen.

    ```xml
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ```

3. Als er een fout in dit bestand wordt weer geven (gemarkeerd in het voor beeld), noteert u de fout code, in dit geval 16001. Zoek de beschrijving van deze foutcode op in de volgende naslaginformatie voor fouten.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="troubleshoot-iot-edge-errors"></a>Problemen met IoT Edge oplossen

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [bekende problemen in deze versie](../databox-gateway/data-box-gateway-release-notes.md).