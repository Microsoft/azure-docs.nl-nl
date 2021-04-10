---
title: 'Quickstart: Een beveiligingsmoduledubbel maken'
description: In deze quickstart leert u hoe een Defender for IoT-moduledubbel maakt voor gebruik met Azure Defender for IoT.
ms.topic: quickstart
ms.date: 1/21/2021
ms.openlocfilehash: 2ee88bd23b7d125ef9244f8ff630ee5eb8cdd015
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782670"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Quickstart: Een azureiotsecurity-moduledubbel maken

In deze quickstart wordt uitgelegd hoe u afzonderlijke _azureiotsecurity_-moduledubbels maakt voor nieuwe apparaten of massaal moduledubbels maakt voor alle apparaten in een IoT Hub.

## <a name="prerequisites"></a>Vereisten

Geen

## <a name="understanding-azureiotsecurity-module-twins"></a>Informatie over azureiotsecurity-moduledubbels

Apparaatdubbels spelen een belangrijke rol voor zowel apparaatbeheer als procesautomatisering voor IoT-oplossingen die in Azure zijn ingebouwd.

Defender voor IoT biedt volledige integratie met uw bestaande IoT Device Management-platform, zodat u de beveiligings status van uw apparaat kunt beheren en het gebruik van bestaande mogelijkheden voor apparaatbesturing wilt gebruiken.
Defender for IoT-integratie wordt bereikt door gebruik te maken van het IoT Hub-dubbelmechanisme.

Zie [IoT Hub-moduledubbels](../iot-hub/iot-hub-devguide-module-twins.md) voor meer informatie over het algemene concept van moduledubbels in Azure IoT Hub.

Defender for IoT maakt gebruik van het moduledubbelmechanisme en onderhoudt een beveiligingsmodule met de naam _azureiotsecurity_ voor al uw apparaten.

De Defender-IoT-micro agent bevat alle informatie die relevant is voor de beveiliging van apparaten voor elk apparaat.

Als u volledig gebruik wilt maken van Defender voor IoT-functies, moet u deze Defender-IoT-micro agent apparaatdubbels voor elk apparaat in de service maken, configureren en gebruiken.

## <a name="create-azureiotsecurity-module-twin"></a>Azureiotsecurity-moduledubbel maken

_azureiotsecurity_-moduledubbels kunnen op twee manieren worden gemaakt:

1. [Module-batchscript](https://aka.ms/iot-security-github-create-module) : maakt automatisch een moduledubbel voor nieuwe apparaten of apparaten zonder een moduledubbel die de standaardconfiguratie gebruiken.
1. Handmatige bewerking van elk moduledubbel met specifieke configuraties voor elk apparaat.

>[!NOTE]
> Als u de batch-methode gebruikt, worden de bestaande azureiotsecurity-moduledubbels niet overschreven. Met de batch-methode maakt u ALLEEN nieuwe moduledubbels voor apparaten die nog geen beveiligingsmoduledubbel hebben.

Zie [Agentconfiguratie](how-to-agent-configuration.md) voor meer informatie over het wijzigen of aanpassen van de configuratie van een bestaande moduledubbel.

Hand matig een nieuwe _azureiotsecurity_ -module maken, twee maal voor een apparaat:

1. Zoek en selecteer in uw IoT Hub het apparaat waarvoor u een beveiligingsmoduledubbel wilt maken.

1. Selecteer op uw apparaat en klik vervolgens op **module identiteit toevoegen**.

1. Voer in het veld **Naam module-id** **azureiotsecurity** in.

1. Selecteer **Opslaan**.

## <a name="verify-creation-of-a-module-twin"></a>Het maken van een moduledubbel controleren

Ga als volgt te werk om te controleren of er een moduledubbel bestaat voor een specifiek apparaat:

1. Selecteer in uw Azure IoT Hub **IoT-apparaten** in het menu **Explorers**.

1. Voer de apparaat-ID in of selecteer een optie in het **veld query apparaat** en selecteer **query apparaten**.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Query uitvoeren op apparaten":::

1. Selecteer het apparaat of dubbel Selecteer dit om de pagina met details van het apparaat te openen.

1. Selecteer het menu **Module-identiteiten** en bevestig dat de **azureiotsecurity**-module aanwezig is in de lijst met module-identiteiten die zijn gekoppeld aan het apparaat.

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Modules die zijn gekoppeld aan een apparaat":::

Raadpleeg [Agentconfiguratie](how-to-agent-configuration.md) voor meer informatie over het aanpassen van eigenschappen van Defender for IoT-moduledubbels.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het onderzoeken van beveiligingsaanbevelingen...

> [!div class="nextstepaction"]
> [Beveiligingsaanbevelingen onderzoeken](quickstart-investigate-security-recommendations.md)