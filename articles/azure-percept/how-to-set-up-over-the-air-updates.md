---
title: Azure IoT Hub instellen om te implementeren via de Air-updates
description: Meer informatie over het configureren van Azure IoT Hub voor het implementeren van updates via de lucht naar Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662322"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Azure IoT Hub instellen voor implementatie via de Air-updates voor uw Azure percept DK
Uw Azure percept DK veilig en up-to-date houden met behulp van over-the-Air-updates. In een paar eenvoudige stappen kunt u uw Azure-omgeving instellen met update van het apparaat voor IoT Hub en de meest recente updates implementeren in azure percept DK.

## <a name="create-a-device-update-account"></a>Een update account voor een apparaat maken

1. Ga naar de [Azure Portal](https://portal.azure.com) en meld u aan met het Azure-account dat u gebruikt met Azure percept 

1. Begin met het typen van ' apparaat bijwerken voor IoT Hub ' in het venster Zoeken boven aan de pagina.

1. Selecteer **apparaat bijwerken voor IOT-hubs** zoals deze wordt weer gegeven in het venster zoeken.

1. Klik in het linkerbovenhoek van de pagina op de knop **toevoegen** .

1. Selecteer het Azure-abonnement en de resource groep die is gekoppeld aan uw Azure percept-apparaat (dit is waar de IoT Hub van de onboarding is opgeslagen).

1. Geef een naam en locatie op voor het update account van uw apparaat

1. Bekijk de details en selecteer vervolgens **controleren + maken**.
 
1. Zodra de implementatie is voltooid, klikt **u op Ga naar resource**.
 
## <a name="create-a-device-update-instance"></a>Een update-exemplaar voor een apparaat maken
Maak nu een instantie in de update van uw apparaat voor IoT Hub-account.

1. Klik in de update van het apparaat voor IoT Hub bron op **instanties** onder **instance Management**.
 
1. Klik op **+ maken**, geef een exemplaar naam op en selecteer het IOT hub dat is gekoppeld aan uw Azure percept-apparaat (dat wil zeggen, gemaakt tijdens de voorbereidings ervaring). Dit kan een paar minuten duren.
 
1. Klik op **Maken**.

## <a name="configure-iot-hub"></a>IoT Hub configureren

1. Wacht op de pagina Instance Management- **instanties** om de update van het apparaat te verplaatsen naar de status **geslaagd** . Klik op het pictogram **vernieuwen** naast **verwijderen** om de status bij te werken.
 
1. Selecteer het exemplaar dat voor u is gemaakt en klik vervolgens op **IOT hub configureren**. Selecteer in het linkerdeel venster de optie **Ik ga akkoord om deze wijzigingen aan te brengen** en op **bijwerken** te klikken.
 
1. Wacht tot het proces is voltooid.
 
## <a name="configure-access-control-roles"></a>Toegangs beheer rollen configureren
In de laatste stap kunt u machtigingen verlenen aan gebruikers voor het publiceren en implementeren van updates.

1. Klik in de update van het apparaat voor IoT Hub resource op **toegangs beheer (IAM)**
 
2. Klik op **+ toevoegen** en selecteer vervolgens **functie toewijzing toevoegen**
 
3. Selecteer bij **rol** **Update beheerder apparaat**. Voor **het toewijzen van toegang aan** **gebruikers-, groeps-of Service principe** selecteren. Selecteer **uw** account of het account van de persoon die updates gaat implementeren. Klik vervolgens op **Opslaan**. 

    > [!TIP]
    > Als u meer mensen toegang wilt geven tot uw organisatie, kunt u deze stap herhalen en elk van deze gebruikers een **Update beheerder** van het apparaat maken.

## <a name="next-steps"></a>Volgende stappen

U bent nu ingesteld en kunt [uw Azure percept dev kit over-the-Air bijwerken](./how-to-update-over-the-air.md) met behulp van de update voor IOT hub van het apparaat. Ga naar de Azure-IoT Hub die u voor uw Azure percept-apparaat gebruikt.