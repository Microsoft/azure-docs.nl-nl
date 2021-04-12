---
title: Azure IoT Hub instellen voor de implementatie van over-the-Air-updates
description: Meer informatie over het configureren van Azure IoT Hub voor het implementeren van updates over-the-Air naar Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064117"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Azure IoT Hub instellen voor implementatie via de Air-updates voor uw Azure percept DK

Uw Azure percept DK veilig en up-to-date houden met behulp van over-the-Air-updates. In een paar eenvoudige stappen kunt u uw Azure-omgeving instellen met update van het apparaat voor IoT Hub en de meest recente updates implementeren in azure percept DK.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw dev kit verbonden met een Wi-Fi netwerk, een IOT hub gemaakt en de dev kit verbonden met de IOT hub

## <a name="create-a-device-update-account"></a>Een update account voor een apparaat maken

1. Ga naar de [Azure Portal](https://portal.azure.com) en meld u aan met het Azure-account dat u gebruikt met Azure percept.

1. Voer in de zoek balk boven aan de pagina **apparaat bijwerken in voor IOT hub**.

1. Selecteer **apparaat bijwerken voor IOT hub** wanneer het wordt weer gegeven in de zoek balk.

1. Klik op de knop **toevoegen** in het linkerbovenhoek-gedeelte van de pagina.

1. Selecteer het **Azure-abonnement** en de **resource groep** die zijn gekoppeld aan uw Azure percept-apparaat en de bijbehorende IOT hub.

1. Geef een **naam** en **locatie** op voor het update account van uw apparaat.

1. Bekijk de details en selecteer **controleren + maken**.

1. Zodra de implementatie is voltooid, klikt **u op Ga naar resource**.

## <a name="create-a-device-update-instance"></a>Een update-exemplaar voor een apparaat maken

1. Klik in de update van het apparaat voor IoT Hub bron op **instanties** onder **instance Management**.

1. Klik op **+ maken**, geef een exemplaar naam op en selecteer het IOT hub dat aan uw Azure percept-apparaat is gekoppeld. Dit kan een paar minuten duren.

1. Klik op **Create**.

## <a name="configure-iot-hub"></a>IoT Hub configureren

1. Wacht op de pagina Instance Management- **instanties** om de update van het apparaat te verplaatsen naar de status **geslaagd** . Klik op het pictogram **vernieuwen** om de status bij te werken.

1. Selecteer het exemplaar dat voor u is gemaakt en klik op **IOT hub configureren**. Selecteer in het linkerdeel venster de optie **Ik ga akkoord om deze wijzigingen aan te brengen** en op **bijwerken** te klikken.

1. Wacht tot het proces is voltooid.

## <a name="configure-access-control-roles"></a>Toegangs beheer rollen configureren

In de laatste stap kunt u machtigingen verlenen aan gebruikers voor het publiceren en implementeren van updates.

1. Klik in de update van het apparaat voor IoT Hub resource op **toegangs beheer (IAM)**.

1. Klik op **+ toevoegen** en selecteer vervolgens **functie toewijzing toevoegen**.

1. Selecteer bij **rol** **Update beheerder apparaat**. Voor **het toewijzen van toegang aan** **gebruikers-, groeps-of Service principe** selecteren. Selecteer bij **selecteren** uw account of het account van de persoon die updates gaat implementeren. Klik op **Opslaan**.

> [!TIP]
> Als u meer mensen toegang wilt geven tot uw organisatie, kunt u deze stap herhalen en elk van deze gebruikers een **Update beheerder** van het apparaat maken.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om [uw Azure percept dev kit over-the-Air](./how-to-update-over-the-air.md) bij te werken met het bijwerken van het apparaat voor IOT hub.