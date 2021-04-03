---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028542"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Een Consumer groep toevoegen aan uw IoT-hub

[Consumenten groepen](../articles/event-hubs/event-hubs-features.md#event-consumers) bieden onafhankelijke weer gaven in de gebeurtenis stroom die apps en Azure-Services in staat stellen gegevens onafhankelijk van hetzelfde Event hub-eind punt te gebruiken. In deze sectie voegt u een Consumer groep toe aan het ingebouwde eind punt van uw IoT-hub die later in deze zelf studie wordt gebruikt om gegevens uit het eind punt op te halen.

Voer de volgende stappen uit om een Consumer groep toe te voegen aan uw IoT-hub:

1. Open uw IoT-hub in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het linkerdeel venster **ingebouwde eind punten**, selecteer **gebeurtenissen** in het rechterdeel venster en voer een naam in bij **consumenten groepen**. Selecteer **Opslaan**.

   ![Een Consumer groep maken in uw IoT-hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)