---
title: 'Quick Start: een team toevoegen aan een iOS-app met behulp van Azure Communication Services'
description: In deze Quick Start leert u hoe u de Azure Communication Services-teams bibliotheek voor iOS kunt gebruiken.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803973"
---
In deze Quick Start leert u hoe u aan een team vergadering kunt deel nemen met de Azure Communication Services-invoeg bibliotheek voor iOS.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Snelstartgids hebt u de volgende vereisten nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een Mac waarop [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) wordt uitgevoerd, evenals een geldig ontwikkelaarscertificaat dat is geïnstalleerd in uw Sleutelhanger.
- Een gebruikte Communication Services-resource. [Een Communication Services maken](../../create-communication-resource.md).
- Een [Token voor gebruikerstoegang](../../access-tokens.md) voor uw Azure Communication Service.
- [CocoaPods](https://cocoapods.org/) is geïnstalleerd voor uw build-omgeving.

## <a name="setting-up"></a>Instellen

### <a name="creating-the-xcode-project"></a>Het Xcode-project maken

Maak in Xcode een nieuw iOS-project en selecteer de **app** -sjabloon. We gaan gebruikmaken van UIKit Story boards. U gaat geen tests maken tijdens deze Quick Start. U kunt **Tests opnemen** uitschakelen.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Scherm afbeelding van de selectie van de nieuwe project sjabloon in Xcode.":::

Geef het project een naam `TeamsEmbedGettingStarted` .

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Scherm opname met de nieuwe project details in Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installeer het pakket en de afhankelijkheden met CocoaPods

1. Een Podfile maken voor uw toepassing:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Voer `pod install` uit.
3. Open de gegenereerde `.xcworkspace` with Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Vraag toegang aan de microfoon, camera, enzovoort.

Werk de gegevens van de app-eigenschappen lijst bij om toegang te krijgen tot de hardware van het apparaat. Stel de bijbehorende waarde in op een `string` die wordt opgenomen in het dialoog venster dat door het systeem wordt gebruikt om toegang aan te vragen bij de gebruiker.

Klik met de rechtermuisknop op de `Info.plist`-vermelding van de projectstructuur en selecteer **Open As** > **Source Code**. Voeg de volgende regels toe in de bovenste sectie `<dict>` en sla het bestand op.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Het Framework voor het insluiten van teams toevoegen

1. Down load het Framework.
2. Maak een `Frameworks` map in de hoofdmap van het project. Bijvoorbeeld `\TeamsEmbedGettingStarted\Frameworks\`
3. Kopieer de gedownloade `TeamsAppSDK.framework` en `MeetingUIClient.framework` Frameworks naar deze map.
4. Voeg de `TeamsAppSDK.framework` en `MeetingUIClient.framework` toe aan het doel van het project op het tabblad Algemeen. Gebruik de `Add Other`  ->  `Add Files...` om naar de Framework-bestanden te navigeren en deze toe te voegen.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Scherm opname met de toegevoegde frameworks in Xcode.":::

5. Als dit nog niet het geval is, voegt `$(PROJECT_DIR)/Frameworks` u toe aan `Framework Search Paths` onder het tabblad instellingen van het doel van het project. Als u de instelling wilt vinden, wijzigt u het filter van `basic` in `all` , kunt u ook de zoek balk aan de rechter kant gebruiken.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Scherm opname van het zoekpad voor het framework in Xcode.":::

### <a name="turn-off-bitcode"></a>Bitcode uitschakelen

Stel `Enable Bitcode` de optie `No` in op de instellingen van de project build. Als u de instelling wilt vinden, wijzigt u het filter van `basic` in `all` , kunt u ook de zoek balk aan de rechter kant gebruiken.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Scherm opname met de optie BitCode in Xcode.":::

### <a name="add-framework-signing-script"></a>Framework-handtekening script toevoegen

Selecteer het doel van uw app en klik op het `Build Phases` tabblad.  Klik vervolgens op de `+` , gevolgd door `New Run Script Phase` . Zorg ervoor dat deze nieuwe fase plaatsvindt na de `Embed Frameworks` fasen.



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Scherm opname van het toevoegen van het build-script in Xcode.":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>Schakel Voice-over-IP in de achtergrond modus in.

Selecteer het doel van uw app en klik op het tabblad mogelijkheden.

Schakel `Background Modes` het `+ Capabilities` selectie vakje aan de bovenkant in en selecteer `Background Modes` .

Selecteer selectie vakje voor `Voice over IP` .

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Scherm opname met ingeschakelde VOIP in Xcode.":::

### <a name="add-a-window-reference-to-appdelegate"></a>Een venster verwijzing naar AppDelegate toevoegen

Open het bestand **AppDelegate. Swift** van het project en voeg een verwijzing toe voor ' Window '.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Een knop toevoegen aan de View Controller

Maak een knop in de `viewDidLoad` call back in **View Controller. Swift**.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Maak een uitgang voor de knop in **View Controller. Swift**.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Open het bestand **View Controller. Swift** van het project en voeg `import` boven aan het bestand een declaratie toe om de `AzureCommunication library` en te importeren `MeetingUIClient` . 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

Vervang de implementatie van de `ViewController` klasse door een eenvoudige knop waarmee de gebruiker kan deel nemen aan een vergadering. We zullen bedrijfs logica koppelen aan de knop in deze Quick Start.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-teams bibliotheek voor insluiting:

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | De MeetingUIClient is het belangrijkste ingangs punt voor de insluitings bibliotheek van teams. |
| MeetingUIClientDelegate | De MeetingUIClientDelegate wordt gebruikt voor het ontvangen van gebeurtenissen, zoals wijzigingen in de aanroep status. |
| MeetingJoinOptions | MeetingJoinOptions worden gebruikt voor Configureer bare opties zoals weergave naam. | 
| CallState | De CallState wordt gebruikt voor het rapporteren van status wijzigingen van de oproep. U kunt kiezen uit de volgende opties: verbinding maken, waitingInLobby, verbonden en beëindigd. |

## <a name="create-and-authenticate-the-client"></a>De client maken en verifiëren

Initialiseer een `MeetingUIClient` exemplaar met een token voor gebruikers toegang waarmee we vergaderingen kunnen toevoegen. Voeg de volgende code toe aan de `viewDidLoad` call back in **View Controller. Swift**:

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Vervang door `<USER_ACCESS_TOKEN>` een geldig token voor gebruikers toegang voor uw bron. Raadpleeg de documentatie inzake [Token voor gebruikerstoegang](../../access-tokens.md) als u nog geen token hebt.

### <a name="setup-token-refreshing"></a>Het installatie token vernieuwen

Maak een `fetchTokenAsync`-methode. Voeg vervolgens uw `fetchToken` logica toe om het token van de gebruiker op te halen.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Vervang door `<USER_ACCESS_TOKEN>` een geldig token voor gebruikers toegang voor uw bron.

## <a name="join-a-meeting"></a>Deel nemen aan een vergadering

De `joinMeeting` methode wordt ingesteld als de actie die wordt uitgevoerd wanneer wordt getikt op de knop voor het toevoegen van een *vergadering* . Werk de implementatie bij om deel te nemen aan een vergadering met `MeetingUIClient` :

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Vervangen `<MEETING URL>` door een koppeling naar een team vergadering.

### <a name="get-a-teams-meeting-link"></a>Een koppeling naar een team vergadering ophalen

Een koppeling naar een team vergadering kan worden opgehaald met Graph Api's. Dit wordt beschreven in [Graph-documentatie](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
The aanroepende SDK voor Communication Services accepteert een volledige koppeling naar een Teams-vergadering. Deze koppeling wordt geretourneerd als onderdeel van de `onlineMeeting`-resource, die toegankelijk is bij de [eigenschap `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). U kunt de vereiste vergaderingsgegevens ook ophalen via de **URL** in de uitnodiging voor de Teams-vergadering zelf.

## <a name="run-the-code"></a>De code uitvoeren

U kunt uw app maken en uitvoeren op een iOS-simulator door **Product** > **Uitvoeren** te selecteren of door de sneltoets (&#8984;-R) te gebruiken.

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Laatste look en feel van de snelstart-app":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Laatste uiterlijk wanneer de vergadering is toegevoegd":::

> [!NOTE]
> De eerste keer dat u deelneemt aan een vergadering, wordt u door het systeem gevraagd om toegang te krijgen tot de microfoon. In een productietoepassing moet u de `AVAudioSession`API[ gebruiken om de machtigingsstatus ](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) te controleren en het gedrag van uw toepassing bij te werken wanneer geen toestemming wordt verleend.

## <a name="add-localization-support-based-on-your-app"></a>Lokalisatie ondersteuning toevoegen op basis van uw app

De SDK van micro soft teams ondersteunt meer dan 100 teken reeksen en bronnen. De Framework bundel bevat basis-en Engelse talen. De rest hiervan zijn opgenomen in het bestand dat is `Localizations.zip` opgenomen in het pakket.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Lokalisatie toevoegen aan de SDK op basis van wat uw app ondersteunt

1. Bepaal wat voor soort lokalisatie uw toepassing ondersteunt vanuit de lijst Xcode project > info > lokalisatie
2. De Localizations.zip uitpakken die deel uitmaakt van het pakket
3. Kopieer de lokalisatie mappen van de uitgepakte map op basis van wat uw app ondersteunt in de hoofdmap van TeamsAppSDK. Framework

## <a name="preparation-for-app-store-upload"></a>Voor bereiding voor uploaden van App Store

Verwijder i386-en x86_64 architecturen uit de frameworks in het geval van archiveren.

Voeg het `i386` script en de `x86_64` architecturen toe om de toepassing te verwijderen in de build-fases vóór de fase-multidesigntijd als u de app wilt archiveren.

Selecteer uw project in de project Navigator. Ga in het deel venster Editor naar Build-fasen → Klik op + Sign → een nieuwe run script-fase maken.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Voorbeeldcode

U kunt de voorbeeld-app downloaden uit [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started).
