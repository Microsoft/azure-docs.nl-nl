---
title: Modules implementeren vanuit Azure Portal-Azure IoT Edge
description: Gebruik uw IoT Hub in de Azure Portal om een IoT Edge module te pushen van uw IoT Hub naar uw IoT Edge-apparaat, zoals geconfigureerd door een implementatie manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9248c9578d94b000c04c82b33eeeb089e55a26ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200301"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure IoT Edge modules implementeren vanuit de Azure Portal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Wanneer u IoT Edge modules met uw bedrijfs logica hebt gemaakt, wilt u deze implementeren op uw apparaten zodat ze aan de rand kunnen worden uitgevoerd. Als u meerdere modules hebt die samen werken om gegevens te verzamelen en te verwerken, kunt u ze allemaal tegelijk implementeren en de routerings regels declareren waarmee ze verbinding maken.

In dit artikel wordt beschreven hoe de Azure Portal u begeleidt bij het maken van een implementatie manifest en het pushen van de implementatie naar een IoT Edge-apparaat. Zie [IOT Edge modules op schaal implementeren en bewaken](how-to-deploy-at-scale.md)voor meer informatie over het maken van een implementatie die gericht is op meerdere apparaten op basis van hun gedeelde labels.

## <a name="prerequisites"></a>Vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een IoT Edge-apparaat.

  Als u geen IoT Edge apparaat hebt ingesteld, kunt u er een maken in een virtuele Azure-machine. Volg de stappen in een van de Quick Start-artikelen voor het [maken van een virtueel Linux-apparaat](quickstart-linux.md) of [het maken van een virtueel Windows-apparaat](quickstart.md).

## <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. Zie [begrijpen hoe IOT Edge modules kunnen worden gebruikt, geconfigureerd en opnieuw worden gebruikt](module-composition.md)voor meer informatie over hoe implementatie manifesten werken en hoe u ze kunt maken.

De Azure Portal bevat een wizard die u helpt bij het maken van het implementatie manifest, in plaats van het JSON-document hand matig te bouwen. Er zijn drie stappen: **modules toevoegen**, **routes opgeven** en de **implementatie controleren**.

>[!NOTE]
>De stappen in dit artikel zijn gebaseerd op de meest recente schema versie van de IoT Edge agent en hub. Schema versie 1,1 is uitgebracht samen met IoT Edge versie 1.0.10 en maakt de module opstart volgorde en de functies voor het door sturen van prioriteit.
>
>Als u implementeert op een apparaat waarop versie 1.0.9 of eerder wordt uitgevoerd, bewerkt u de **runtime-instellingen** in de stap **modules** van de wizard om schema versie 1,0 te gebruiken.

### <a name="select-device-and-add-modules"></a>Apparaat selecteren en modules toevoegen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.
1. Selecteer in het linkerdeel venster **IOT Edge** in het menu.
1. Klik op de ID van het doel apparaat in de lijst met apparaten.
1. Selecteer op de bovenste balk **Modules instellen**.
1. Geef in de sectie **container Registry-instellingen** van de pagina de referenties op voor toegang tot persoonlijke container registers die uw module installatie kopieën bevatten.
1. Selecteer in de sectie **IOT Edge-modules** van de pagina de optie **toevoegen**.
1. Kies een van de drie typen modules in de vervolg keuzelijst:

   * **Module IOT Edge** : u geeft de module naam en URI van de container installatie kopie op. De afbeeldings-URI voor de voor beeld-SimulatedTemperatureSensor-module is bijvoorbeeld `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Als de module installatie kopie is opgeslagen in een persoonlijk container register, voegt u de referenties op deze pagina toe om de installatie kopie te openen.
   * **Marketplace-module** : modules die worden gehost op de Azure Marketplace. Voor sommige Marketplace-modules is aanvullende configuratie vereist. Controleer dus de module gegevens in de lijst met [IOT Edge modules van Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Azure stream Analytics module** : modules die zijn gegenereerd op basis van een Azure stream Analytics workload.

1. Nadat u een module hebt toegevoegd, selecteert u de module naam in de lijst om de module-instellingen te openen. Vul zo nodig de optionele velden in.

   Zie [module configuratie en-beheer](module-composition.md#module-configuration-and-management)voor meer informatie over de beschik bare module-instellingen.

   Zie voor meer informatie over de module twee [gewenste eigenschappen definiëren of bijwerken](module-composition.md#define-or-update-desired-properties).

1. Herhaal stap 6 tot en met 8 om extra modules toe te voegen aan uw implementatie.
1. Selecteer **volgende: routes** om door te gaan naar de sectie routes.

### <a name="specify-routes"></a>Routes opgeven

Op het tabblad **Routes** definieert u hoe berichten tussen modules en de IoT Hub worden uitgewisseld. Berichten worden gemaakt met behulp van naam/waarde-paren. De eerste implementatie voor een nieuw apparaat bevat standaard een route met de naam **route** en gedefinieerd als **van/messages/ \* in $upstream**. Dit betekent dat alle berichten die door modules worden uitgevoerd, worden verzonden naar uw IOT-hub.  

De para meters **prioriteit** en **time to Live** zijn optionele para meters die u in een route definitie kunt gebruiken. Met de para meter Priority kunt u kiezen voor welke routes de berichten eerst moeten worden verwerkt of welke routes als laatste moeten worden verwerkt. Prioriteit wordt bepaald door het instellen van een getal van 0-9, waarbij 0 de hoogste prioriteit heeft. Met de para meter time to Live kunt u declareren hoe lang berichten in die route moeten worden bewaard totdat ze worden verwerkt of verwijderd uit de wachtrij.

Zie voor meer informatie over het maken van routes [routes declareren](module-composition.md#declare-routes).

Zodra de routes zijn ingesteld, selecteert u **volgende: controleren + maken** om door te gaan naar de volgende stap van de wizard.

### <a name="review-deployment"></a>Implementatie controleren

In het gedeelte beoordeling ziet u het JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Houd er rekening mee dat er twee modules zijn gedeclareerd die u niet hebt toegevoegd: **$edgeAgent** en **$edgeHub**. Deze twee modules vormen de [IOT Edge runtime](iot-edge-runtime.md) en zijn de standaard instellingen voor elke implementatie.

Controleer uw implementatie gegevens en selecteer vervolgens **maken**.

## <a name="view-modules-on-your-device"></a>Modules op uw apparaat weer geven

Zodra u modules hebt geïmplementeerd op uw apparaat, kunt u ze weer geven op de pagina Details van apparaat van uw IoT Hub. Op deze pagina ziet u de naam van elke geïmplementeerde module, evenals nuttige informatie, zoals de implementatie status en afsluit code.

## <a name="deploy-modules-from-azure-marketplace"></a>Modules implementeren vanuit Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) is een online Marketplace voor toepassingen en services waar u kunt bladeren door een breed scala aan bedrijfs toepassingen en oplossingen die zijn gecertificeerd en geoptimaliseerd om te worden uitgevoerd op Azure, met inbegrip van [IOT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

U kunt een IoT Edge module implementeren vanuit Azure Marketplace en vanaf uw IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Implementeren vanuit Azure Marketplace

Bekijk de IoT Edge-modules in de Marketplace en wanneer u het gewenste item kunt implementeren door **maken** of **nu downloaden** te selecteren. Ga door met de stappen in de wizard implementatie die kunnen variëren, afhankelijk van de IoT Edge module die u hebt geselecteerd:

1. Bevestig de gebruiks voorwaarden en het privacybeleid van de provider door **door gaan** te selecteren. Mogelijk moet u eerst contact gegevens opgeven.
1. Kies uw abonnement en het IoT Hub waaraan het doel apparaat is gekoppeld.
1. Kies **implementeren op een apparaat**.
1. Voer de naam van het apparaat in of selecteer **apparaat zoeken** om te bladeren tussen de apparaten die zijn geregistreerd bij de hub.
1. Selecteer **maken** om door te gaan met het standaard proces voor het configureren van een implementatie manifest, inclusief het toevoegen van andere modules, indien gewenst. Details voor de nieuwe module, zoals afbeeldings-URI, opties voor maken en gewenste eigenschappen, zijn vooraf gedefinieerd, maar kunnen worden gewijzigd.

Controleer of de module is geïmplementeerd in uw IoT Hub in de Azure Portal. Selecteer uw apparaat, selecteer **modules instellen** en de module moet worden weer gegeven in de sectie **IOT Edge modules** .

### <a name="deploy-from-azure-iot-hub"></a>Implementeren vanuit Azure IoT Hub

U kunt snel een module implementeren vanuit Azure Marketplace op uw apparaat in uw IoT Hub in de Azure Portal.

1. Ga in Azure Portal naar uw IoT Hub.
1. Selecteer in het linkerdeel venster onder **automatische Apparaatbeheer** de optie **IOT Edge**.
1. Selecteer het IoT Edge apparaat dat de implementatie moet ontvangen.
1. Selecteer op de bovenste balk **Modules instellen**.
1. Klik in de sectie **IOT Edge modules** op **toevoegen** en selecteer **Marketplace module** in de vervolg keuzelijst.

![Module toevoegen in IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Kies een module op de **Marketplace** -pagina van de IOT Edge-module. De module die u selecteert, wordt automatisch geconfigureerd voor uw abonnement, de resource groep en het apparaat. Deze wordt vervolgens weer gegeven in de lijst met IoT Edge-modules. Voor sommige modules is mogelijk aanvullende configuratie vereist.

> [!TIP]
> De informatie over IoT Edge modules van de Azure-IoT Hub is beperkt. U kunt eerst meer te weten komen over de [IOT Edge modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) in de Azure Marketplace.

Selecteer **volgende: routes** en ga door met de implementatie zoals beschreven in [routes opgeven](#specify-routes) en de [implementatie controleren](#review-deployment) eerder in dit artikel.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IOT Edge modules op schaal](how-to-deploy-at-scale.md)
