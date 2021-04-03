---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553237"
---
Volg deze stappen om verbinding te maken met het opslagaccount en de verbinding te controleren.

1. Open in Storage Explorer het dialoogvenster **Verbinding maken met Azure Storage**. Selecteer in het dialoogvenster **Verbinding maken met Azure Storage** de optie **Een opslagaccountnaam en -sleutel gebruiken**.

    ![Schermopname van het dialoogvenster Verbinding maken met Azure Storage, met de optie Een opslagaccountnaam en -sleutel gebruiken geselecteerd.](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Plak uw **accountnaam** en **accountsleutel** (de waarde van sleutel 1 op de pagina **Verbinding maken en kopiëren** in de lokale webgebruikersinterface). Selecteer het domein van opslageindpunten als **Andere (hieronder opgeven)** en geef het eindpunt van de blob-service op, zoals hieronder weergegeven. Schakel de optie **HTTP gebruiken** alleen in wanneer u gegevens overbrengt via *http*. Als u *https* gebruikt, laat u de optie uitgeschakeld. Selecteer **Next**.

    ![Schermopname van het dialoogvenster Verbinding maken met naam en sleutel, met waarden ingevoerd.](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Controleer in het dialoogvenster **Samenvatting verbinding** de gegevens die u hebt opgegeven. Selecteer **Verbinding maken**.

    ![Schermopname van het dialoogvenster Overzicht van verbinding, met Verbinding maken geselecteerd.](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Het account dat is toegevoegd, wordt weergegeven in het linkerdeelvenster van Storage Explorer met (Externe, Andere) toegevoegd aan de naam. Klik op **Blobcontainers** om de container weer te geven.

    ![Schermopname van het menu Verkenner, met Blob-containers geselecteerd.](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
