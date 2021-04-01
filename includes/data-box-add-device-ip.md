---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553220"
---
1. Meld u aan op het Data Box-apparaat. Zorg ervoor dat het apparaat is ontgrendeld.

    ![Schermopname van uw dashboard, met het apparaat weergegeven als Ontgrendeld.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Ga naar **Netwerkinterfaces instellen**. Noteer het IP-adres van het apparaat voor de netwerkinterface die wordt gebruikt voor verbinding met de client.

    ![Schermopname van de Netwerkinstellingen, waar u het IP-adres kunt zien.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Ga naar **Verbinding maken en kopiëren** en klik op **Rest**.

    ![Schermopname van het deelvenster Verbinding maken en kopiëren, waar u REST kunt selecteren als toegangsinstelling.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Kopieer in het dialoogvenster **Opslagaccount openen en gegevens uploaden** het **eindpunt van de blob-service**.

    ![Schermopname van het dialoogvenster Opslagaccount openen en gegevens uploaden, waar u het Blob-service-eindpunt kunt kopiëren.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Start **Kladblok** als beheerder en open vervolgens het bestand met **hosts** dat zich bevindt in `C:\Windows\System32\Drivers\etc`.
6. Voeg de volgende vermelding toe aan uw bestand met **hosts**: `<device IP address> <Blob service endpoint>`
7. Gebruik de volgende afbeelding ter referentie. Sla het bestand met **hosts** op.

    ![Schermopname van een Kladblok-document, met het IP-adres en Blob-service-eindpunt toegevoegd.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
