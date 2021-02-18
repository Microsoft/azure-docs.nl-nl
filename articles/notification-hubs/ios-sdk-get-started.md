---
title: Pushmeldingen verzenden naar iOS met Azure Notification Hubs en de iOS-SDK
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs en de Apple-service voor pushmeldingen verzendt naar iOS-apparaten.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c18814230eda6624e8ab1040797c66108f630e30
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581747"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Zelfstudie: Pushmeldingen verzenden naar iOS-apps met Azure Notification Hubs

In deze zelfstudie leert u hoe u Azure Notification Hubs instelt en aanmeldingsgegevens configureert voor pushmeldingen naar een iOS-apparaat via de [Apple Push Notification-service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

Het uitvoeren van deze zelfstudie is een vereiste voor de opvolgende Doelstelling C en Swift iOS-zelfstudies en bevat over de volgende stappen:

- Het bestand met de aanvraag voor certificaatondertekening genereren.
- Uw app registreren voor pushmeldingen.
- Een inrichtingsprofiel voor de app maken.
- Een Notification Hub maken.
- De Notification Hub configureren met APNS-gegevens.

## <a name="prerequisites"></a>Vereisten

U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/free/) voor meer informatie.

U hebt ook het volgende nodig:

- Een actief [Apple Developer](https://developer.apple.com/)-account.
- Een Mac waarop [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) wordt uitgevoerd, evenals een geldig ontwikkelaarscertificaat dat is geïnstalleerd in uw Sleutelhanger.
- Een iPhone of iPad waarop iOS versie 10 of hoger wordt uitgevoerd.
- Uw fysieke apparaat dat is geregistreerd in de [Apple Portal](https://developer.apple.com/) en is gekoppeld aan uw certificaat.

Lees het [overzicht van Azure Notification Hubs](notification-hubs-push-notification-overview.md) als u niet bekend bent met de service.

> [!NOTE]
> De meldingshub wordt alleen geconfigureerd voor gebruik van de Sandbox-verificatiemodus. Gebruik deze verificatiemodus niet voor productiewerkbelastingen.

## <a name="generate-the-certificate-signing-request-file"></a>Het bestand met de aanvraag voor certificaatondertekening genereren

De Apple Push Notification Service (APNS) gebruikt certificaten om uw pushmeldingen te verifiëren. Volg deze instructies om het pushcertificaat te maken dat vereist is om meldingen te verzenden en te ontvangen. Zie de officiële documentatie van de [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) voor meer informatie over deze concepten.

Genereer het bestand met de aanvraag voor certificaatondertekening dat door Apple wordt gebruikt om een ondertekend pushcertificaat te genereren:

1. Voer op uw Mac het hulpprogramma Sleutelhangertoegang uit. Dit kan worden gestart vanuit de map **Hulpprogramma's** of de map **Overige** in Launchpad.

2. Selecteer **Toegang tot sleutelhanger**, vouw **Certificaatassistent** uit en selecteer vervolgens **Een certificaat bij een certificaatautoriteit aanvragen**.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Schermopname waarin de optie Een certificaat aanvragen in het menu Certificeringsinstantie is gemarkeerd.":::

   > [!NOTE]
   > Sleutelhangertoegang selecteert standaard het eerste item in de lijst. Dit kan een probleem zijn als u zich in de categorie **Certificaten** bevindt en **Apple Worldwide Developer Relations Certification Authority** niet het eerste item in de lijst is. Zorg ervoor dat u een item hebt dat geen sleutel is of dat de sleutel **Apple Worldwide Developer Relations Certification Authority** is geselecteerd, voordat u de CSR (Certificate Signing Request) genereert.

3. Selecteer uw **E-mailadres van de gebruiker**, voer uw waarde voor **Algemene naam** in, zorg ervoor dat u **Opgeslagen op schijf** hebt opgegeven en selecteer **Doorgaan**. Laat **E-mailadres van CA** leeg omdat dit niet vereist is.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Vereiste certificaatgegevens":::

4. Voer een naam in voor het CSR-bestand in **Opslaan als**, selecteer de locatie in **Waar** en selecteer vervolgens **Opslaan**.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Bestandsnaam kiezen":::

   Met deze actie wordt het CSR-bestand opgeslagen op de geselecteerde locatie. De standaardlocatie is **Desktop**. Onthoud de locatie die u voor het bestand hebt gekozen.

Vervolgens gaat u uw app registreren bij Apple, pushmeldingen inschakelen en het geëxporteerde bestand met de aanvraag voor certificaatondertekening uploaden om een pushcertificaat te maken.

## <a name="register-your-app-for-push-notifications"></a>Uw app voor pushmeldingen registreren

Registreer uw app bij Apple en registreer u voor pushmeldingen om pushmeldingen te verzenden naar een iOS-app.

1. Als u uw app nog niet hebt geregistreerd, bladert u naar de [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) in het Apple Developer Center. Meld u aan bij de portal met uw Apple ID en selecteer **ID's**. Selecteer vervolgens **+** om een nieuwe app te registreren.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Pagina app-id's":::

2. Selecteer op het scherm **Een nieuwe id registreren** het keuzerondje **App-id's**. Selecteer vervolgens **Doorgaan**.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Pagina Nieuwe id registreren":::

3. Werk de volgende drie waarden voor uw nieuwe app bij en selecteer vervolgens **Doorgaan**:

   - **Beschrijving**: Typ een beschrijvende naam voor uw app.
   - **Bundel-id**: Voer een bundel-id in van het formulier **Organization Identifier.Product Name** zoals genoemd in de [App Distribution Guide](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). De waarden voor **Organization Identifier** en **Product Name** moeten overeenkomen met de organisatie-id en productnaam die u gebruikt wanneer u een Xcode-project gaat maken. In de volgende schermopname wordt de waarde voor **NotificationHubs** als de organisatie-id gebruikt en de waarde voor **GetStarted** als de productnaam. Zorg ervoor dat de waarde voor **Bundel-id** overeenkomt met de waarde in uw Xcode-project, zodat Xcode het juiste publicatieprofiel gebruikt.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="App-id registreren":::

   - **Pushmeldingen**: Controleer de optie **Pushmeldingen** in het gedeelte **Mogelijkheden**.

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Nieuwe app-id registreren":::

      Met deze actie wordt uw app-id gegenereerd en wordt u gevraagd om de gegevens te bevestigen. Selecteer **Doorgaan** en selecteer vervolgens **Registreren** om de nieuwe app-id te bevestigen.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Nieuwe app-id bevestigen":::

      Nadat u **Registreren** hebt geselecteerd, ziet u de nieuwe app-id als een regelitem op de pagina **Certificaten, id's en profielen**.

4. Ga op de pagina **Certificaten, id's en profielen** onder **Id's** naar het regelitem App-id dat u zojuist hebt gemaakt, en selecteer de bijbehorende rij om het scherm **De configuratie van uw app-id bewerken** weer te geven.

## <a name="create-a-certificate-for-notification-hubs"></a>Een certificaat maken voor Notification Hubs

> [!NOTE]
> Sinds de release van iOS 13 kunt u nu alleen stille meldingen ontvangen met op tokens gebaseerde verificatie. Als u op certificaat gebaseerde verificatie gebruikt voor uw APNS-referenties, moet u overschakelen naar op tokens gebaseerde verificatie.

Er is een certificaat vereist om de Notification Hub te kunnen laten werken met **APNs**. Dit kan op een van de volgende twee manieren worden gedaan:

- Maak een **.p12**-bestand dat rechtstreeks kan worden geüpload naar Notification Hubs.

- Maak een **.p8** dat kan worden gebruikt voor [op tokens gebaseerde verificatie](notification-hubs-push-notification-http2-token-authentication.md) (de nieuwere methode).

De tweede optie heeft een aantal voordelen ten opzichte van het gebruik van certificaten, zoals wordt beschreven in [Op tokens gebaseerde (HTTP/2) verificatie voor APNS](notification-hubs-push-notification-http2-token-authentication.md). Er worden echter stappen gegeven voor beide methoden.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Optie 1: Het maken van een .p12-pushcertificaat dat rechtstreeks kan worden geüpload naar Notification Hubs

1. Scrol omlaag naar de ingeschakelde optie **Pushmeldingen** en selecteer vervolgens **Configureren** om het certificaat te maken.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="App-id":::

2. Het venster **Apple Push Notification service SSL-certificaten** wordt weergegeven. Selecteer de knop **Certificaat maken** in de sectie **SSL-ontwikkelingscertificaat**.

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Certificaat maken":::

   Het scherm **Een nieuw certificaat maken** wordt weergegeven.

   > [!NOTE]
   > In deze zelfstudie wordt een ontwikkelingscertificaat gebruikt. Hetzelfde proces wordt gebruikt bij het registreren van een productiecertificaat. Zorg ervoor dat u hetzelfde certificaattype gebruikt als u meldingen verzendt.

3. Selecteer **Bestand kiezen**, blader naar de locatie waar u het CSR-bestand hebt opgeslagen bij de eerste taak en dubbelklik vervolgens op de naam van het certificaat om het te laden. Selecteer vervolgens **Doorgaan**.

4. Nadat de portal het certificaat heeft gemaakt, selecteert u de knop **Downloaden**. Sla het certificaat op en onthoud de locatie waar het wordt opgeslagen.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Certificaat downloaden":::

   Het certificaat wordt gedownload en opgeslagen in uw map **Downloads**.

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Certificaatbestand zoeken":::

   Standaard krijgt het gedownloade ontwikkelingscertificaat de naam **aps_development.cer**.

5. Dubbelklik op het gedownloade pushcertificaat **aps\_development.cer**. Nu wordt het nieuwe certificaat in de sleutelhanger geïnstalleerd, zoals op de volgende afbeelding wordt weergegeven:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Toegang tot sleutelhanger":::

   Hoewel de naam in uw certificaat kan afwijken, wordt de naam voorafgegaan door **Apple Development iOS Push Services**.

6. In Sleutelhangertoegang klikt u met de rechtermuisknop op het nieuwe pushcertificaat dat u hebt gemaakt in de categorie **Certificaten**. Selecteer **Exporteren**, geef het bestand een naam, selecteer de indeling **.p12** en klik vervolgens op **Opslaan**.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Certificaat exporteren":::

   U kunt ervoor kiezen om het certificaat met een wachtwoord te beveiligen, maar dit is optioneel. Klik op **OK** als u het maken van wachtwoorden wilt overslaan. Noteer de bestandsnaam en locatie van het geëxporteerde .p12-certificaat. Ze worden gebruikt om verificatie met APNS mogelijk te maken.

   > [!NOTE]
   > Uw .p12-bestandsnaam en-locatie kunnen afwijken van wat er in deze zelfstudie wordt afgebeeld.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Optie 2: Het maken van een .p8-certificaat dat kan worden gebruikt voor op tokens gebaseerde verificatie

1. Noteer de volgende details:

   - **App-id-voorvoegsel** (dit is een **Team-id**)
   - **Bundel-id**

2. Als u teruggaat naar **Certificaten, id's en profielen**, klikt u op **Sleutels**. Als u al een sleutel voor **APNS** hebt geconfigureerd, kunt u het .p8-certificaat dat u hebt gedownload opnieuw gebruiken nadat het is gemaakt. Als dat het geval is, kunt u de stappen 3 tot 5 negeren.

3. Klik op de knop **+** (of de knop **Een sleutel maken**) om een nieuwe sleutel te maken.

4. Geef een geschikte waarde voor **Sleutelnaam** op, selecteer de optie **Apple Push Notifications service (APNS)** en klik vervolgens op **Doorgaan**, gevolgd door **Registreren** op het volgende scherm.

5. Klik op **Downloaden**, verplaats het bestand **.p8** (voorafgegaan door `AuthKey_` ) naar een veilige lokale map en klik vervolgens op **Klaar**.

   > [!IMPORTANT]
   > Zorg ervoor dat uw .p8-bestand op een veilige plaats is opgeslagen (en sla een back-up op). Nadat de sleutel is gedownload, kan deze niet opnieuw worden gedownload omdat de serverkopie wordt verwijderd.

6. Klik in **Sleutels** op de sleutel die u zojuist hebt gemaakt (of een bestaande sleutel als u ervoor hebt gekozen om deze te gebruiken).

7. Noteer de waarde van **Sleutel-id**.

8. Open uw .p8-certificaat in een geschikte toepassing naar keuze, zoals [Visual Studio Code](https://code.visualstudio.com/), en noteer de sleutelwaarde. Dit is de waarde tussen **-----BEGIN PRIVATE KEY-----** en **-----END PRIVATE KEY-----** .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Dit is de tokenwaarde die later wordt gebruikt om Notification Hubs te configureren.

Aan het einde van deze stappen zou u de volgende informatie moeten hebben voor later gebruik in [Uw Notification Hub configureren met APNS-gegevens](#configure-the-notification-hub-with-apns-information):

- **Team-id** (zie stap 1)
- **Bundel-id** (zie stap 1)
- **Sleutel-id** (zie stap 7)
- **Tokenwaarde** (de .p8-sleutelwaarde, zie stap 8)

## <a name="create-a-provisioning-profile"></a>Een inrichtingsprofiel maken

1. Ga terug naar de [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecteer **Certificaten, id's en profielen**, selecteer **Profielen** in het linkermenu en selecteer vervolgens **+** om een nieuw profiel te maken. Het scherm **Een nieuw inrichtingsprofiel registreren** wordt weergegeven.

2. Selecteer **Ontwikkeling iOS-app** onder **Ontwikkeling** als het inrichtingsprofieltype en selecteer vervolgens **Doorgaan**.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Lijst met inrichtingsprofielen":::

3. Selecteer daarna in de vervolgkeuzelijst **App-id** de app-id die u hebt gemaakt en selecteer **Doorgaan**.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="App-id selecteren":::

4. Selecteer in het venster **Certificaten selecteren** het ontwikkelingscertificaat dat u gebruikt voor het ondertekenen bij programmacode en selecteer **Doorgaan**. Dit certificaat is niet het pushcertificaat dat u hebt gemaakt. Als er nog geen bestaat, moet u er een maken. Als er wel een certificaat bestaat, gaat u verder met de volgende stap. Een ontwikkelingscertificaat maken als er geen bestaat:

   1. Als u **Geen certificaten beschikbaar** ziet, selecteert u **Certificaat maken**.
   2. In de sectie **Software** selecteert u **Apple Development**. Selecteer vervolgens **Doorgaan**.
   3. In het scherm **Een nieuw certificaat maken** selecteert u **Bestand kiezen**.
   4. Blader naar het certificaat **Aanvraag certificaatondertekening** dat u eerder hebt gemaakt, selecteer het en selecteer **Openen**.
   5. Selecteer **Doorgaan**.
   6. Download het ontwikkelingscertificaat en onthoud de locatie waar het is opgeslagen.

5. Ga terug naar de pagina **Certificaten, id's en profielen**, selecteer **Profielen** in het linkermenu en selecteer vervolgens **+** om een nieuw profiel te maken. Het scherm **Een nieuw inrichtingsprofiel registreren** wordt weergegeven.

6. Selecteer in het venster **Certificaten selecteren** het ontwikkelingscertificaat dat u zojuist hebt gemaakt. Selecteer vervolgens **Doorgaan**.

7. Selecteer vervolgens de apparaten die u voor de tests wilt gebruiken en selecteer **Doorgaan**.

8. Kies ten slotte een naam voor het profiel in **Inrichtingsprofielnaam** en selecteer **Genereren**.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Naam kiezen voor het inrichtingsprofiel":::

9. Wanneer het nieuwe inrichtingsprofiel is gemaakt, selecteert u **Downloaden**. Onthoud de locatie waar het wordt opgeslagen.

10. Blader naar de locatie van het inrichtingsprofiel en dubbelklik erop om het te installeren op uw Xcode-ontwikkelingscomputer.

## <a name="create-a-notification-hub"></a>Een Notification Hub maken

In dit gedeelte maakt u een Notification Hub en configureert u verificatie met APNS met het .p12-pushcertificaat of op tokens gebaseerde verificatie. Als u een Notification Hub wilt gebruiken die u al hebt gemaakt, kunt u doorgaan naar stap 5.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Alle services** in het menu aan de linkerkant en selecteer **Notification Hubs** in de sectie **Mobiel**. Selecteer het sterpictogram naast de servicenaam om de service toe te voegen aan de sectie **FAVORIETEN** in het menu aan de linkerkant. Nadat u **Notification Hubs** hebt toegevoegd aan **FAVORIETEN**, selecteert u dit.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure-portal":::

3. Op de **Notification Hubs**-pagina selecteert u **Toevoegen** op de werkbalk.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Werkbalkknop Toevoegen":::

4. Voer op de pagina **Notification Hubs** de volgende stappen uit:

   1. Voer een naam in **Notification Hub** in.
   2. Voer een naam in **Een nieuwe naamruimte maken** in. Een naamruimte bevat een of meer Notification Hubs.
   3. Selecteer een waarde in de vervolgkeuzelijst **Locatie**. Deze waarde specificeert de locatie waar u de Notification Hub wilt maken.
   4. Selecteer een bestaande resourcegroep in **Resourcegroep** of maak een nieuwe resourcegroep.
   5. Selecteer **Maken**.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Eigenschappen instellen":::

5. Selecteer **Meldingen** (het belpictogram) en selecteer vervolgens **Ga naar resource**. U kunt ook de lijst op de pagina **Notification Hubs** vernieuwen en uw hub selecteren.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Meldingen in portal":::

6. Selecteer **Toegangsbeleid** in de lijst. U ziet dat de twee verbindingsreeksen voor u beschikbaar zijn. Later moet u er pushmeldingen mee afhandelen.

   > [!IMPORTANT]
   > Gebruik niet het beleid **DefaultFullSharedAccessSignature** in uw toepassing. Deze mag alleen in uw back-end worden gebruikt.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Verbindingsreeksen":::

## <a name="configure-the-notification-hub-with-apns-information"></a>De Notification Hub configureren met APNS-gegevens

Selecteer onder **Notification Services** de optie **Apple (APNS)** en voer vervolgens de juiste stappen uit op basis van de methode die u eerder hebt gekozen in de sectie [Een certificaat maken voor Notification Hubs](#create-a-certificate-for-notification-hubs).

> [!NOTE]
> Gebruik **Productie** voor **Toepassingsmodus** alleen als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht.

### <a name="option-1-use-a-p12-push-certificate"></a>Optie 1: Een .p12-pushcertificaat gebruiken

1. Selecteer **Certificaat**.

2. Selecteer het bestandspictogram.

3. Selecteer het .p12-bestand dat u eerder hebt geëxporteerd en selecteer vervolgens **Openen**.

4. Geef indien nodig het juiste wachtwoord op.

5. Selecteer de modus **Sandbox**.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Configureren":::

6. Selecteer **Opslaan**.

### <a name="option-2-use-token-based-authentication"></a>Optie 2: Op tokens gebaseerde verificatie gebruiken

1. Selecteer **Token**.

2. Voer de volgende waarden in die u eerder hebt verkregen:

   - **Sleutel-id**
   - **Bundel-id**
   - **Team-id**
   - **Token**

3. Kies **Sandbox**.

4. Selecteer **Opslaan**.

U hebt nu uw Notification Hub geconfigureerd met APNS. U beschikt ook over de benodigde verbindingsreeksen om uw app te registreren en pushmeldingen te verzenden.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Notification Hub gemaakt en geconfigureerd in Azure en deze zo geconfigureerd dat meldingen naar uw toepassing kunnen worden verzonden via Apple Push Notification Service (APNS). Vervolgens maken we een voorbeeld van een iOS-toepassing en integreren we de Azure Notification Hubs-SDK zodat deze pushmeldingen kan ontvangen die via de Azure-portal worden verzonden. Ga naar de volgende zelfstudie op basis van de taal van uw keuze:

- [Zelfstudie: Pushmeldingen verzenden naar iOS-apps met Azure Notification Hubs](ios-sdk-current.md)
