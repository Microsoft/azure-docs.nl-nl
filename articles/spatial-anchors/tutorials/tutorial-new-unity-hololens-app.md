---
title: 'Zelfstudie: Een nieuwe HoloLens Unity-app maken'
description: In deze zelfstudie leert u hoe u een HoloLens Unity-app maakt met behulp van Azure Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0233e58a404721586af0ae2fbdf78dbab6d424ed
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550375"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Zelfstudie: Stapsgewijze instructies voor het maken van een nieuwe HoloLens Unity-app met behulp van Azure Spatial Anchors

In deze zelfstudie wordt beschreven hoe u een nieuwe HoloLens Unity-app maakt met behulp van Azure Spatial Anchors.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

1. Een Windows-computer waarop <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> is geïnstalleerd met de workload **Universeel Windows-platform-ontwikkeling** en het onderdeel **Windows 10 SDK (10.0.18362.0 of later)** en <a href="https://git-scm.com/download/win" target="_blank">Git voor Windows</a>.
2. De [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) voor Visual Studio moet worden geïnstalleerd vanuit de [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Een HoloLens-apparaat waarvoor de [ontwikkelaarsmodus](/windows/mixed-reality/using-visual-studio) is ingeschakeld. Voor dit artikel is een HoloLens-apparaat met de [Update voor Windows van 10 mei 2020](/windows/mixed-reality/whats-new/release-notes-may-2020) nodig. Als u wilt bijwerken naar de nieuwste release op HoloLens, opent u de app **Instellingen**, gaat u naar **Bijwerken en beveiliging** en selecteert u vervolgens de knop **Controleren op updates**.

## <a name="getting-started"></a>Aan de slag

We gaan eerst ons project en de Unity-scène instellen:
1. Start Unity.
2. Selecteer **Nieuw**.
4. Zorg ervoor dat **3D** is geselecteerd.
5. Geef uw project een naam en voer bij **Location** de opslaglocatie in.
6. Selecteer **Project maken**.
7. Sla de lege standaardscène op in een nieuw bestand met behulp van: **File** > **Save As**.
8. Geef de nieuwe scène de naam **Main** en druk op de knop **Save**.

**De projectinstellingen instellen**

We gaan nu enkele Unity-projectinstellingen instellen die ons helpen de Windows Holographic SDK te richten op ontwikkeling.

We gaan eerst kwaliteitsinstellingen voor onze toepassing instellen.
1. Selecteer **Edit** > **Project Settings** > **Quality**
2. Klik in de kolom onder het logo van **Windows Store** op de pijl op de **Default** rij en selecteer **Very Low**. U weet dat de instelling correct wordt toegepast wanneer het vak in de kolom **Windows Store** en de rij **Very Low** groen is.

We moeten onze Unity-app configureren zodat deze een detailweergave in plaats van een 2D-weergave gebruikt. We kunnen een detailweergave maken door ondersteuning voor Virtual Reality in te schakelen voor Unity die op de Windows 10 SDK is gericht.
1. Ga naar **Edit** > **Project Settings** > **Player**.
2. Selecteer in het deelvenster **Inspector** voor **Player Settings** het **Windows**-pictogram.
3. Vouw de groep **XR-instellingen** uit.
4. Schakel in de sectie **Rendering** het selectievakje **Virtual Reality Supported** in om een nieuwe lijst met **Virtual Reality SDK's** toe te voegen.
5. Controleer of **Windows Mixed Reality** wordt weergegeven in de lijst. Als dat niet het geval is, selecteert u de knop **+** onder aan de lijst en kiest u **Windows Mixed Reality**.

> [!NOTE]
> Als u het Windows-pictogram niet ziet, controleert u of u de back-end van de Windows .NET Scripting Backend hebt geselecteerd voordat u de installatie hebt uitgevoerd. Als dat niet het geval is, moet u Unity mogelijk opnieuw installeren met de juiste Windows-installatie.

**Configuratie van back-end voor scripts controleren**
1. Ga naar **Edit** > **Project Settings** > **Player** (mogelijk hebt u **Player** uit de vorige stap nog open).
2. Selecteer in het deelvenster **Inspector** voor **Player Settings** het pictogram **Windows Store**.
3. Zorg ervoor dat in de configuratiesectie **Other Settings** de optie **Scripting Backend** is ingesteld op **IL2CPP**.

**Mogelijkheden instellen**
1. Ga naar **Edit** > **Project Settings** > **Player** (mogelijk hebt u **Player** uit de vorige stap nog open).
2. Selecteer in het deelvenster **Inspector** voor **Player Settings** het pictogram **Windows Store**.
3. Schakel in de controlesectie **Publishing Settings** het selectievakje **InternetClientServer** en **SpatialPerception** in.

**De virtuele hoofdcamera instellen**
1. Selecteer in het **Hierarchy Panel** de optie **Main Camera**.
2. Stel in de **Inspector** de transformatiepositie in op **0,0,0**.
3. Zoek de eigenschap **Clear Flags** op en wijzig de vervolgkeuzelijst van **Skybox** in **Solid Color**.
4. Klik op het veld **Background** om een kleurenkiezer te openen.
5. Stel **R, G, B en A** in op **0**.
6. Selecteer **Add Component** en zoek **Spatial Mapping Collider** op en voeg deze toe.

**Het script maken**
1. Maak in het deelvenster **Project** een nieuwe map met de naam **Scripts** onder de map **Assets**.
2. Klik met de rechtermuisknop op de map en selecteer **Create >** , **C# Script**. Geef het de naam **AzureSpatialAnchorsScript**.
3. Ga naar **GameObject** -> **Create Empty**.
4. Selecteer het en wijzig in de **Inspector** de naam ervan van **GameObject** in **MixedRealityCloud**. Selecteer **Add Component**, zoek **AzureSpatialAnchorsScript** op en voeg dit toe.

**De bol-prefab maken**
1. Ga naar **GameObject** -> **3D Object** -> **Sphere**.
2. Stel in de **Inspector** de schaal in op **0,25, 0,25, 0,25**.
3. Zoek het **bol**-object op in het deelvenster **Hierarchy**. Klik erop en sleep het naar de map **Assets** in het deelvenster **Project**.
4. Klik met de rechtermuisknop en **verwijder** de oorspronkelijke bol die u hebt gemaakt in het deelvenster **Hierarchy**.

U hebt nu een bol-prefab in uw deelvenster **Project**.

## <a name="trying-it-out"></a>Het uitproberen
Als u wilt testen of alles werkt, bouwt u uw app in **Unity** en implementeert u deze vanuit **Visual Studio**. Volg hoofdstuk 6 van de [**MR-basis beginselen 100: Aan de slag met Unity**-cursus](/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) om dit te doen. U moet nu het startscherm van Unity zien en vervolgens een duidelijke weergave.

## <a name="place-an-object-in-the-real-world"></a>Een object in het echt plaatsen
We gaan een object maken en plaatsen met behulp van uw app. Open de Visual Studio-oplossing die we hebben gemaakt toen we [onze app hebben geïmplementeerd](#trying-it-out).

Voeg eerst de volgende importen toe aan uw `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `AzureSpatialAnchorsScript`-klasse:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

Voordat we verdergaan, moeten we de bol-prefab instellen die we hebben gemaakt op basis van de spherePrefab-lidvariabele. Ga terug naar **Unity**.
1. Selecteer in **Unity** het object **MixedRealityCloud** in het deelvenster **Hierarchy**.
2. Klik op de **bol**-prefab die u hebt opgeslagen in het deelvenster **Project**. Sleep de **bol** waarop u hebt geklikt naar het gebied **Sphere Prefab** onder **Azure Spatial Anchors Script (Script)** in het deelvenster **Inspector**.

U moet de **bol** nu als de prefab in uw script hebben ingesteld. Bouw vanuit **Unity** en open de resulterende **Visual Studio**-oplossing opnieuw, zoals u dat ook hebt gedaan in [Het uitproberen](#trying-it-out).

Open `AzureSpatialAnchorsScript.cs` opnieuw in **Visual Studio**. Voeg de volgende code toe aan de methode `Start()`. Met deze code wordt `GestureRecognizer` gekoppeld, waardoor `HandleTap` wordt aangeroepen wanneer er in de lucht wordt getikt.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

We moeten nu de volgende `HandleTap()`-methode onder `Update()` toevoegen. Er wordt een ray cast uitgevoerd en er wordt een raakpunt opgehaald waarop een bol moet worden geplaatst.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

We moeten nu de bol maken. De bol is in eerste instantie wit, maar deze waarde wordt later aangepast. Voeg de volgende `CreateAndSaveSphere()`-methode toe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

Voer uw app vanuit **Visual Studio** uit om deze opnieuw te valideren. Tik op het scherm om uw witte bol op het gewenste oppervlak te maken en te plaatsen.

## <a name="set-up-the-dispatcher-pattern"></a>Het dispatcherpatroon instellen

Wanneer u met Unity werkt, moeten alle Unity-API's (bijvoorbeeld API's die u gebruikt voor het uitvoeren van UI-updates) plaatsvinden op de hoofdthread. In de code die wij schrijven, krijgen we echter callbacks op andere threads. We willen de gebruikersinterface bijwerken in deze callbacks, dus hebben we een manier nodig om van een zijthread naar de hoofdthread te gaan. Om code vanuit een zijthread uit te voeren op de hoofdthread gebruiken we het dispatcherpatroon.

We gaan een lidvariabele toevoegen, namelijk `dispatchQueue`. Dit is een wachtrij met acties. We gaan acties naar de wachtrij pushen en de acties in de hoofdthread vervolgens uit de wachtrij verwijderen en uitvoeren.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

Vervolgens voegen we een manier toe om een actie aan de wachtrij toe te voegen. Voeg `QueueOnUpdate()` direct na `Update()` in:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

We kunnen nu de Update()-lus gebruiken om te controleren of er een actie in de wachtrij is geplaatst. Als dit het geval is, wordt de actie uit de wachtrij verwijderd en uitgevoerd.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>De Azure Spatial Anchors SDK ophalen

### <a name="download-packages"></a>Pakketten downloaden
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Pakketten importeren
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="prepare-code"></a>Code voorbereiden
Voeg in uw **Visual Studio**-oplossing de volgende import toe aan uw `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `AzureSpatialAnchorsScript`-klasse:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Een lokale Azure Spatial Anchor koppelen aan het lokale anker

We gaan de CloudSpatialAnchorSession van de Azure Spatial Anchor instellen. We beginnen met het toevoegen van de volgende `InitializeSession()`-methode binnen uw `AzureSpatialAnchorsScript`-klasse. Zodra deze is aangeroepen, zorgt deze ervoor dat er een Azure Spatial Anchors-sessie wordt gemaakt en op de juiste wijze wordt geïnitialiseerd tijdens het opstarten van uw app.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

We moeten nu code schrijven voor het afhandelen van aanroepen van gemachtigden. We voegen hier meer aan toe naarmate we verdergaan.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

We gaan nu uw `initializeSession()`-methode koppelen aan uw `Start()`-methode.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

Voeg tot slot de volgende code toe aan uw `CreateAndSaveSphere()`-methode. Hiermee wordt een lokale Azure Spatial Anchor gekoppeld aan de bol die we in het echt gaan plaatsen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

Voordat u verder gaat, moet u een Azure Spatial Anchors-account maken om de account-id, -sleutel en het accountdomein te verkrijgen. Als u deze waarden nog niet hebt, volgt u de volgende sectie om ze te verkrijgen.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Uw lokale anker uploaden naar de cloud

Wanneer u een account-id, sleutel en domein hebt voor uw Azure Spatial Anchors-account, plakt u de `Account Id` in `SpatialAnchorsAccountId`, de `Account Key` in `SpatialAnchorsAccountKey` en de `Account Domain` in `SpatialAnchorsAccountDomain`.

Ten slotte gaan we alles aan elkaar koppelen. Voeg de volgende code toe aan de methode `CreateAndSaveSphere()`. Hiermee wordt de `CreateAnchorAsync()`-methode aangeroepen zodra uw bol is gemaakt. Zodra de methode is geretourneerd, wordt met de onderstaande code nog eenmaal uw bol bijgewerkt, waardoor de kleur wordt gewijzigd in blauw.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

Voer uw app nog een keer uit vanuit **Visual Studio**. Beweeg uw hoofd en tik vervolgens in de lucht om uw bol te plaatsen. Zodra er voldoende frames zijn, verandert de bol in geel en wordt het uploaden naar de cloud gestart. Zodra het uploaden is voltooid, wordt de bol blauw. U kunt desgewenst ook het [Uitvoervenster](/visualstudio/ide/reference/output-window) gebruiken tijdens de foutopsporing in **Visual Studio** om de logboekberichten te controleren die uw app verzendt. Zorg ervoor dat u de `Debug`-configuratie van uw app vanuit Visual Studio implementeert, zodat u de logboekberichten kunt bekijken. U kunt `RecommendedForCreateProgress` bekijken en zodra het uploaden is voltooid, kunt u de anker-id zien die door de cloud is geretourneerd.

> [!NOTE]
> Als u de melding 'DllNotFoundException: Kan DLL 'AzureSpatialAnchors' niet laden: De opgegeven module is niet gevonden." ontvangt, moet u uw oplossing opnieuw **Opschonen** en **Bouwen**.

## <a name="locate-your-cloud-spatial-anchor"></a>Het ruimtelijke anker in de cloud opzoeken

Als het anker naar de cloud is geüpload, gaan we proberen het weer te vinden. We voegen de volgende code toe aan de methode `HandleTap()`. Met deze code gebeurt het volgende:

* `ResetSession()` aanroepen, waardoor de `CloudSpatialAnchorSession` wordt gestopt en onze bestaande blauwe bol van het scherm wordt verwijderd.
* `CloudSpatialAnchorSession` opnieuw initialiseren. We doen dit zodat we er zeker van zijn dat het anker waarnaar we op zoek gaan afkomstig is uit de cloud en niet het lokale anker is dat we hebben gemaakt.
* Een **Watcher** maken die zoekt naar het anker dat is geüpload naar Azure Spatial Anchors.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

We gaan nu de methoden `ResetSession()` en `CleanupObjects()` toevoegen. U kunt deze onder `QueueOnUpdate()` plaatsen

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

We moeten nu de code koppelen die wordt aangeroepen wanneer het anker waarnaar we zoeken, wordt gevonden. Voeg binnen `InitializeSession()` de volgende callbacks toe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


We voegen nu code toe waarmee een groene bol wordt gemaakt en geplaatst wanneer de CloudSpatialAnchor is gevonden. Ook wordt tikken op het scherm weer geactiveerd, zodat u het hele scenario nog een keer kunt herhalen: nog een lokaal anker maken, het uploaden en het weer zoeken.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

Dat is alles. Voer uw app een laatste keer uit vanuit **Visual Studio** om het hele scenario van begin tot eind uit te proberen. Verplaats uw apparaat en plaats uw witte bol. Beweeg vervolgens uw hoofd om omgevingsgegevens vast te leggen totdat de bol geel wordt. Uw lokale anker wordt geüpload en uw bol wordt blauw. Tik ten slotte nog één keer op uw scherm, om uw lokale anker te verwijderen en start een query om het equivalent ervan in de cloud op te halen. Blijf doorgaan met het verplaatsen van uw apparaat totdat het ruimtelijke anker in de cloud is gevonden. Er wordt een groene bol op de juiste locatie weergegeven en u kunt het hele scenario herhalen.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]