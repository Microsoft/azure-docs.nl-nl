---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185530"
---
1. Ga naar de lokale webgebruikersinterface van uw apparaat en meld u aan bij uw apparaat. Zorg ervoor dat het apparaat is ontgrendeld.

2. Ga naar de pagina **Netwerkinstellingen**. Noteer het IP-adres van het apparaat voor de netwerkinterface die wordt gebruikt voor verbinding met de client.

3. Als u met een externe Windows-client werkt, start u **Kladblok** als beheerder en opent u vervolgens het bestand met hosts dat zich bevindt in `C:\Windows\System32\Drivers\etc`.

4. Voeg de volgende vermelding toe aan uw bestand met hosts: `<Device IP address> <Blob service endpoint>`

    Het blobservice-eindpunt hebt u opgehaald uit het Edge-opslagaccount dat u in de Azure-portal hebt gemaakt. U zult alleen het achtervoegsel van het blobservice-eindpunt gebruiken.

    Gebruik de volgende afbeelding ter referentie. Sla het bestand `hosts` op.

    ![Het bestand met hosts wijzigen op een Windows-client](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)