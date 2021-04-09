---
title: 'Quickstart: Een HoloLens-app maken met DirectX'
description: In deze Quick Start leert u hoe u een HoloLens-app kunt maken met behulp van object ankers.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b5db9f3766bdd7d754f49403665a371f9d10afd7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047606"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Snelstartgids: een HoloLens-app maken met Azure object-ankers in C++/WinRT en DirectX

In deze Quick Start wordt beschreven hoe u een HoloLens-app maakt met behulp van [Azure object ankerpunten](../overview.md) in C++/WinRT en DirectX. Azure object-ankers is een beheerde Cloud service waarmee 3D-assets naar AI-modellen worden geconverteerd waarmee objecten met gemengde realiteiten voor de HoloLens kunnen worden ingeschakeld. Wanneer u klaar bent, hebt u een app HoloLens die een object en de bijbehorende pose in een Holographic DirectX 11-toepassing (Universal Windows) kan detecteren.

U leert het volgende:

> [!div class="checklist"]
> * Een HoloLens-toepassing maken en side loads
> * Een object detecteren en het model visualiseren

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

* Een fysiek object in uw omgeving en het 3D-model (CAD of gescand).
* Een Windows-computer met het volgende geïnstalleerd:
  * <a href="https://git-scm.com" target="_blank">Git voor Windows</a>
  * <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **universeel Windows-platform Development** -werk belasting en het onderdeel **Windows 10 SDK (10.0.18362.0 of hoger)**
* Een apparaat van HoloLens 2 dat up-to-date is en waarvoor de [ontwikkelaars modus](/windows/mixed-reality/using-visual-studio#enabling-developer-mode) is ingeschakeld.
  * Als u wilt bijwerken naar de nieuwste release op HoloLens, opent u de app **Instellingen**, gaat u naar **Bijwerken en beveiliging** en selecteert u vervolgens **Controleren op updates**.

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Open `quickstarts/apps/directx/DirectXAoaSampleApp.sln` in Visual Studio.

Wijzig de **oplossings configuratie** in **release**, wijzig het **oplossings platform** in **ARM64**, selecteer **apparaat** uit de doel opties voor de implementatie. Bouw vervolgens het project **AoaSampleApp** door met de rechter muisknop op het project te klikken en **Build** te selecteren.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Visual Studio-project configureren voor implementatie":::

## <a name="deploy-the-app-to-hololens"></a>De app implementeren op HoloLens

Nadat u het voorbeeld project hebt gecompileerd, kunt u de app implementeren op HoloLens.

Start het HoloLens-apparaat, meld u aan en sluit het aan op de pc via een USB-kabel. Controleer of het **apparaat** het gekozen implementatie doel is (zie hierboven).

Klik met de rechter muisknop op **AoaSampleApp** project en klik vervolgens op **implementeren** in het pop-upmenu om de app te installeren. Als er geen fout wordt weer gegeven in de **uitvoervenster** van Visual Studio, wordt de app geïnstalleerd op HoloLens.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="App implementeren op HoloLens":::

Voordat u de app start, moet u een object model uploaden. Volg de instructies in **ingestie object model en Detecteer de sectie instance** hieronder.

Als u de app wilt starten en fouten wilt opsporen, selecteert u **fout opsporing > fout opsporing starten**. Als u de app wilt stoppen, selecteert u **stoppen met fout opsporing** of drukt u op **SHIFT + F5**.

## <a name="ingest-object-model-and-detect-its-instance"></a>Object model opnemen en het exemplaar detecteren

U moet een object model maken om de voor beeld-app uit te voeren. Stel dat u al een CAD-of gescand 3D-mesh model hebt van een object in uw Space. Raadpleeg [Quick Start: een 3D-model](./get-started-model-conversion.md) opnemen over het maken van een model.

Down load die model, **stoel. OE** in ons geval, naar uw computer. Selecteer vervolgens in de portal voor het HoloLens **systeem > bestanden verkenner > LocalAppData > AoaSampleApp > LocalState** en selecteer **Bladeren...**. Selecteer vervolgens uw model bestand, **Voorzitter. OE** , bijvoorbeeld en selecteer **uploaden**. Vervolgens ziet u het model bestand in de lokale cache.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Model voor uploaden van portal":::

Start vanuit de HoloLens de **AoaSampleApp** -app (als deze al is geopend, sluit deze en open deze opnieuw). Bewaak de sluiting (binnen een afstand van 2 meter) naar het doel object (stoel) en scan deze door deze vanuit meerdere perspectieven te bekijken. Als het goed is, ziet u een roze omsluitend kader rond het object, met een aantal gele punten die op het Opper vlak van het object worden weer gegeven. Dit geeft aan dat het is gedetecteerd.

:::image type="content" source="./media/chair-detection.png" alt-text="Stoel detectie":::

Afbeelding: een gedetecteerde Voorzitter die wordt weer gegeven met het begrenzingsvak (roze), de punt wolk (geel) en een zoek gebied (groot geel vak).

U kunt een zoek ruimte voor het object in de app definiëren door te klikken op de lucht met uw rechter-of linkerhand. De zoek ruimte wordt overgeschakeld tussen een bol van 2 meters, een 4 m ^ 3 selectie kader en een weer gave-frustum. Voor grotere objecten, zoals auto's, is het normaal om de selectie van de weer gave frustum te gebruiken terwijl er een hoek van het object zit op een afstand van 2 meter.
Telkens wanneer het zoek gebied verandert, worden de exemplaren die momenteel worden bijgehouden, door de app verwijderd en proberen ze opnieuw te vinden in het nieuwe zoek gebied.

Met deze app kunt u meerdere objecten tegelijk volgen. U doet dit door meerdere modellen te uploaden naar de map **LocalState** en een zoek gebied in te stellen dat betrekking heeft op alle doel objecten. Het kan langer duren om meerdere objecten te detecteren en op te sporen.

In de app wordt een 3D-model nauw keurig uitgelijnd op het fysieke equivalent. Een gebruiker kan lucht tikken met behulp van hun linkerhand om de tracerings modus met hoge precisie in te scha kelen, waarbij een nauw keurigere pose wordt berekend. Dit is nog steeds een experimentele functie, waardoor meer systeem bronnen worden verbruikt en waardoor een hogere jitter kan worden verkregen in de geschatte pose. Tik op de lucht opnieuw met de linkerkant om terug te scha kelen naar de normale tracerings modus.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Quick Start: een 3D-model opnemen](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Concepten: overzicht van de SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen](../faq.md)