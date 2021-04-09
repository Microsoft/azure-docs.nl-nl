---
title: Een aangepast gesimuleerd apparaat maken - Azure | Microsoft Docs
description: In deze zelfstudie gebruikt u Apparaatsimulatie om een aangepast gesimuleerd apparaat te maken voor gebruik in simulaties.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 22920e6535a19b1ab0ce970c1195cee676d9363f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057725"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Zelfstudie: Een aangepast gesimuleerd apparaat maken

In deze zelfstudie gebruikt u Apparaatsimulatie om een aangepast gesimuleerd apparaat te maken voor gebruik in simulaties. Om aan de slag te gaan met Apparaatsimulatie kunt u een van de opgenomen voorbeelden van gesimuleerde apparaten gebruiken. U kunt ook aangepaste gesimuleerde apparaten maken zoals in dit artikel wordt beschreven. Zie [Create an advanced device model](iot-accelerators-device-simulation-advanced-device.md) (Een geavanceerd apparaatmodel maken) voor meer aanpassingsopties.

In deze zelfstudie hebt u:

>[!div class="checklist"]
> * Een lijst weergeven van uw gesimuleerde apparaatmodellen
> * Aangepast gesimuleerd apparaat maken
> * Een apparaatmodel klonen
> * Een apparaatmodel verwijderen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt volgen, hebt u een geïmplementeerd exemplaar van Apparaatsimulatie in uw Azure-abonnement nodig.

Als u nog geen apparaatsimulatie hebt geïmplementeerd, raadpleegt u [Apparaatsimulatie implementeren](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) in GitHub.

## <a name="view-your-device-models"></a>Uw apparaatmodellen weergeven

Selecteer **Apparaatmodellen** in de menubalk. Op de pagina **Apparaatmodellen** ziet u een lijst van alle beschikbare apparaatmodellen in dit exemplaar van Apparaatsimulatie:

![Apparaatmodellen](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Een apparaatmodel maken

Klik op **+ Apparaatmodellen toevoegen** in de rechterbovenhoek van de pagina:

![Apparaatmodel toevoegen](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

In deze zelfstudie maakt u een gesimuleerde koelkast die zowel temperatuur- en vochtigheidsgegevens verzendt.

Vul het formulier in met de volgende gegevens:

| Instelling             | Waarde                                                |
| ------------------- | ---------------------------------------------------- |
| Naam van apparaatmodel   | Koelkast                                         |
| Modelbeschrijving   | Een koelkast met temperatuur- en vochtigheidssensoren |
| Versie             | 1.0                                                  |

> [!NOTE]
> De naam van het apparaatmodel moet uniek zijn.

Klik op **+ Gegevenspunt toevoegen** om gegevenspunten voor de temperatuur en vochtigheid toe te voegen met de volgende waarden:

| Gegevenspunt          | Gedrag        | Minimumwaarde | Maximumwaarde | Eenheid |
| ------------------- | --------------- | --------- | --------- | ---- |
| Temperatuur         | Willekeurig          | -50       | 100       | F    |
| Vochtigheid            | Willekeurig          | 0         | 100       | %    |

Klik op **Opslaan** om het apparaatmodel op te slaan.

![Apparaatmodel maken](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

Uw koelkast is nu opgenomen in de lijst met apparaatmodellen. Misschien moet u op **Volgende** klikken om naar een andere pagina te gaan en uw koelkast te zien.

## <a name="clone-a-device-model"></a>Een apparaatmodel klonen

Door een apparaatmodel te klonen, maakt u een kopie van een bestaand apparaatmodel. Vervolgens kunt u de kopie bewerken zodat deze voldoet aan uw specifieke behoeften. Klonen bespaart tijd wanneer u vergelijkbare apparaatmodellen moet maken.

Als u een apparaatmodel wilt klonen, schakelt u het selectievakje naast het model in en klikt u in de actiebalk op **Klonen**:

![Schermopname waarin het geselecteerde model en de knop Klonen zijn gemarkeerd.](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Een apparaatmodel verwijderen

U kunt elk aangepast apparaatmodel verwijderen. Als u een apparaatmodel wilt verwijderen, schakelt u het selectievakje naast het model in en klikt u in de actiebalk op **Verwijderen**:

![Apparaatmodel verwijderen](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u aangepaste apparaatmodellen kunt maken, klonen en verwijderen. Zie het volgende artikel met instructies voor meer informatie over apparaatmodellen:

> [!div class="nextstepaction"]
> [Een geavanceerd apparaatmodel maken](iot-accelerators-device-simulation-advanced-device.md)