---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 7b5034f2163e8478d7ddb7b9271402b094a809d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564167"
---
## <a name="generate-the-certificate-signing-request-file"></a>Het bestand met de aanvraag voor certificaatondertekening genereren

De Apple Push Notification service (APNs) maakt gebruik van certificaten om uw pushmeldingen te verifiëren. Volg deze instructies om het pushcertificaat te maken dat vereist is om meldingen te verzenden en te ontvangen. Zie de officiële documentatie van de [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) voor meer informatie over deze concepten.

Genereer het bestand met de aanvraag voor certificaatondertekening dat door Apple wordt gebruikt om een ondertekend pushcertificaat te genereren.

1. Voer op uw Mac het hulpprogramma Sleutelhangertoegang uit. Dit kan worden gestart vanuit de map **Hulpprogramma's** of de map **Overige** in Launchpad.

1. Selecteer **Toegang tot sleutelhanger**, vouw **Certificaatassistent** uit en selecteer vervolgens **Een certificaat bij een certificaatautoriteit aanvragen**.

    ![Toegang tot sleutelhanger gebruiken om een nieuw certificaat aan te vragen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Sleutelhangertoegang selecteert standaard het eerste item in de lijst. Dit kan een probleem zijn als u zich in de categorie **Certificaten** bevindt en **Apple Worldwide Developer Relations Certification Authority** niet het eerste item in de lijst is. Zorg ervoor dat u een item hebt dat geen sleutel is of dat de sleutel **Apple Worldwide Developer Relations Certification Authority** is geselecteerd, voordat u de CSR (Certificate Signing Request) genereert.

1. Selecteer uw **E-mailadres van de gebruiker**, voer uw waarde voor **Algemene naam** in, zorg ervoor dat u **Opgeslagen op schijf** hebt opgegeven en selecteer **Doorgaan**. Laat **E-mailadres van CA** leeg omdat dit niet vereist is.

    ![Vereiste certificaatgegevens](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Voer een naam in voor het CSR-bestand in **Opslaan als**, selecteer de locatie in **Waar** en selecteer vervolgens **Opslaan**.

    ![Kies een bestandsnaam voor het certificaat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Met deze actie wordt het CSR-bestand opgeslagen op de geselecteerde locatie. De standaardlocatie is **Desktop**. Onthoud de locatie die u voor het bestand hebt gekozen.

Vervolgens gaat u uw app registreren bij Apple, pushmeldingen inschakelen en het geëxporteerde bestand met de aanvraag voor certificaatondertekening uploaden om een pushcertificaat te maken.

## <a name="register-your-app-for-push-notifications"></a>Uw app voor pushmeldingen registreren

Registreer uw app bij Apple en registreer u voor pushmeldingen om pushmeldingen te verzenden naar een iOS-app.  

1. Als u uw app nog niet hebt geregistreerd, bladert u naar de [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) in het Apple Developer Center. Meld u aan bij de portal met uw Apple ID en selecteer **ID's**. Selecteer vervolgens **+** om een nieuwe app te registreren.

    ![App-id-pagina van iOS-inrichtingsportal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Selecteer op het scherm **Een nieuwe id registreren** het keuzerondje **App-id's**. Selecteer vervolgens **Doorgaan**.

    ![Nieuwe id-pagina registreren voor iOS Provisioning Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Werk de volgende drie waarden voor uw nieuwe app bij en selecteer vervolgens **Doorgaan**:

   * **Beschrijving**: Typ een beschrijvende naam voor uw app.

   * **Bundel-id**: Voer een bundel-id in van het formulier **Organization Identifier.Product Name** zoals genoemd in de [App Distribution Guide](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). De waarden voor *Organization Identifier* en *Product Name* moeten overeenkomen met de organisatie-id en productnaam die u gebruikt wanneer u een Xcode-project gaat maken. In de volgende schermopname wordt de waarde voor **NotificationHubs** als de organisatie-id gebruikt en de waarde voor **GetStarted** als de productnaam. Zorg ervoor dat de waarde voor **Bundel-id** overeenkomt met de waarde in uw Xcode-project, zodat Xcode het juiste publicatieprofiel gebruikt.

      ![App-id-pagina registreren voor iOS Provisioning Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Pushmeldingen**: Controleer de optie **Pushmeldingen** in het gedeelte **Mogelijkheden**.

      ![Formulier voor het registreren van een nieuwe app-id](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Met deze actie wordt uw app-id gegenereerd en wordt u gevraagd om de gegevens te bevestigen. Selecteer **Doorgaan** en selecteer vervolgens **Registreren** om de nieuwe app-id te bevestigen.

      ![Nieuwe app-id bevestigen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Nadat u **Registreren** hebt geselecteerd, ziet u de nieuwe app-id als een regelitem in de pagina **Certificaten, id's en profielen**.

4. Ga op de pagina **Certificaten, id's en profielen** onder **Id's** naar het regelitem App-id dat u zojuist hebt gemaakt, en selecteer de bijbehorende rij om het scherm **De configuratie van uw app-id bewerken** weer te geven.

## <a name="creating-a-certificate-for-notification-hubs"></a>Een certificaat maken voor Notification Hubs
Er is een certificaat vereist om de Notification Hub te kunnen laten werken met **APNs**. Dit kan op een van de volgende twee manieren worden gedaan:

1. Maak een **.p12** dat rechtstreeks kan worden geüpload naar de Notification Hub.  
2. Maak een **.p8** dat kan worden gebruikt voor [op tokens gebaseerde verificatie](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md) (*de nieuwe methode*).

De nieuwe methode heeft een aantal voordelen (ten opzichte van het gebruik van certificaten), zoals beschreven in [Op tokens gebaseerde (HTTP/2) verificatie voor APNs](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md). Er worden echter stappen gegeven voor beide methoden. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPTIE 1: Een .p12-pushcertificaat maken dat rechtstreeks kan worden geüpload naar de Notification Hub

1. Scrol omlaag naar de ingeschakelde optie **Pushmeldingen** en selecteer vervolgens **Configureren** om het certificaat te maken.

    ![Pagina app-id bewerken](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Het venster **Apple Push Notification service SSL-certificaten** wordt weergegeven. Selecteer de knop **Certificaat maken** in de sectie **SSL-ontwikkelingscertificaat**.

    ![Certificaat voor app-id-knop maken](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Het scherm **Een nieuw certificaat maken** wordt weergegeven.

    > [!NOTE]
    > In deze zelfstudie wordt een ontwikkelingscertificaat gebruikt, dat uw app gebruikt om een uniek apparaattoken te genereren. Hetzelfde proces wordt gebruikt bij het registreren van een productiecertificaat. Zorg er wel voor dat u hetzelfde certificaattype gebruikt als u meldingen verzendt.

3. Selecteer **Bestand kiezen**, blader naar de locatie waar u het CSR-bestand hebt opgeslagen bij de eerste taak en dubbelklik vervolgens op de naam van het certificaat om het te laden. Selecteer vervolgens **Doorgaan**.

4. Nadat de portal het certificaat heeft gemaakt, selecteert u de knop **Downloaden**. Sla het certificaat op en onthoud de locatie waar het wordt opgeslagen.

    ![Download-pagina voor gemaakte certificaat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Het certificaat is nu gedownload en op uw computer in de map **Downloads** opgeslagen.

    ![Certificaatbestand zoeken in de map Downloads](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Standaard krijgt het gedownloade ontwikkelingscertificaat de naam **aps_development.cer**.

5. Dubbelklik op het gedownloade pushcertificaat **aps_development.cer**. Nu wordt het nieuwe certificaat in de sleutelhanger geïnstalleerd, zoals op de volgende afbeelding wordt weergegeven:

    ![Certificatenlijst in Sleutelhangertoegang geeft nieuw certificaat weer](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Hoewel de naam in uw certificaat kan afwijken, wordt de naam voorafgegaan door **Apple Development iOS Push Services**.

6. In Sleutelhangertoegang klikt u met de rechtermuisknop op het nieuwe pushcertificaat dat u hebt gemaakt in de categorie **Certificaten**. Selecteer **Exporteren**, geef het bestand een naam, selecteer de indeling **.p12** en klik vervolgens op **Opslaan**.

    ![Certificaat exporteren in p12-indeling](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    U kunt ervoor kiezen om het certificaat met een wachtwoord te beveiligen, maar dit is optioneel. Klik op **OK** als u het maken van wachtwoorden wilt overslaan. Noteer de bestandsnaam en locatie van het geëxporteerde .p12-certificaat. Ze worden gebruikt om verificatie met APNs mogelijk te maken.

    > [!NOTE]
    > Uw .p12-bestandsnaam en-locatie kunnen afwijken van wat er in deze zelfstudie wordt afgebeeld.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPTIE 2: Een .p8-certificaat maken dat kan worden gebruikt voor op tokens gebaseerde verificatie

1. Noteer de volgende details:

    - **App-id-voorvoegsel** (dit is een **Team-id**)
    - **Bundel-id**
    
2. Als u teruggaat naar **Certificaten, id's en profielen**, klikt u op **Sleutels**.

   > [!NOTE]
   > Als u al een sleutel voor **APNS** hebt geconfigureerd, kunt u het .p8-certificaat dat u hebt gedownload opnieuw gebruiken nadat het is gemaakt. Als dat het geval is, kunt u de stappen **3** tot **5** negeren.

3. Klik op de knop **+** (of de knop **Een sleutel maken**) om een nieuwe sleutel te maken.
4. Geef een geschikte waarde voor **Sleutelnaam** op, selecteer de optie **Apple Push Notifications service (APNs)** en klik vervolgens op **Doorgaan**, gevolgd door **Registreren** op het volgende scherm.
5. Klik op **Downloaden**, verplaats het bestand **.p8** (voorafgegaan door *AuthKey_* ) naar een veilige lokale map en klik vervolgens op **Klaar**.

   > [!NOTE] 
   > Zorg ervoor dat uw .p8-bestand op een veilige plaats is opgeslagen (en sla een back-up op). Nadat de sleutel is gedownload, kan deze niet opnieuw worden gedownload omdat de serverkopie wordt verwijderd.
  
6. Klik in **Sleutels** op de sleutel die u zojuist hebt gemaakt (of een bestaande sleutel als u ervoor hebt gekozen om deze te gebruiken).
7. Noteer de waarde van **Sleutel-id**.
8. Open uw .p8-certificaat in een geschikte toepassing naar keuze, zoals [**Visual Studio Code**](https://code.visualstudio.com) en noteer de sleutelwaarde. Dit is de waarde tussen **-----BEGIN PRIVATE KEY-----** en **-----END PRIVATE KEY-----** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Dit is de **tokenwaarde** die later wordt gebruikt om **Notification Hub** te configureren. 

Aan het einde van deze stappen moet u de volgende informatie gebruiken voor later in [Uw Notification Hub configureren met APNs-gegevens](#configure-your-notification-hub-with-apns-information):

- **Team-id** (zie stap 1)
- **Bundel-id** (zie stap 1)
- **Sleutel-id** (zie stap 7)
- **Tokenwaarde**, d.w.z. de .p8-sleutelwaarde (zie stap 8)

## <a name="create-a-provisioning-profile-for-the-app"></a>Een inrichtingsprofiel voor de app maken

1. Ga terug naar de [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecteer **Certificaten, id's en profielen**, selecteer **Profielen** in het linkermenu en selecteer vervolgens **+** om een nieuw profiel te maken. Het scherm **Een nieuw inrichtingsprofiel registreren** wordt weergegeven.

1. Selecteer **Ontwikkeling iOS-app** onder **Ontwikkeling** als het inrichtingsprofieltype en selecteer vervolgens **Doorgaan**.

    ![Lijst met inrichtingsprofielen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selecteer vervolgens in de vervolgkeuzelijst **App-id** de app-id die u hebt gemaakt en selecteer **Doorgaan**.

    ![Selecteer de app-id](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Selecteer in het venster **Certificaten selecteren** het ontwikkelingscertificaat dat u gebruikt voor het ondertekenen bij programmacode en selecteer **Doorgaan**. Dit certificaat is niet het pushcertificaat dat u hebt gemaakt. Als er nog geen bestaat, moet u er een maken. Als er wel een certificaat bestaat, gaat u verder met de volgende stap. Een ontwikkelingscertificaat maken als er geen bestaat:

    1. Als u **Geen certificaten beschikbaar** ziet, selecteert u **Certificaat maken**.
    2. In de sectie **Software** selecteert u **Apple Development**. Selecteer vervolgens **Doorgaan**.
    3. In het scherm **Een nieuw certificaat maken** selecteert u **Bestand kiezen**.
    4. Blader naar het certificaat **Aanvraag certificaatondertekening** dat u eerder hebt gemaakt, selecteer het en selecteer **Openen**.
    5. Selecteer **Doorgaan**.
    6. Download het ontwikkelingscertificaat en onthoud de locatie waar het is opgeslagen.

1. Ga terug naar de pagina **Certificaten, id's en profielen**, selecteer **Profielen** in het linkermenu en selecteer vervolgens **+** om een nieuw profiel te maken. Het scherm **Een nieuw inrichtingsprofiel registreren** wordt weergegeven.

1. Selecteer in het venster **Certificaten selecteren** het ontwikkelingscertificaat dat u zojuist hebt gemaakt. Selecteer vervolgens **Doorgaan**.

1. Selecteer vervolgens de apparaten die u voor de tests wilt gebruiken en selecteer **Doorgaan**.

1. Kies ten slotte een naam voor het profiel in **Inrichtingsprofielnaam** en selecteer **Genereren**.

    ![Een naam kiezen voor het inrichtingsprofiel](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Wanneer het nieuwe inrichtingsprofiel is gemaakt, selecteert u **Downloaden**. Onthoud de locatie waar het wordt opgeslagen.

1. Blader naar de locatie van het inrichtingsprofiel en dubbelklik erop om het te installeren op uw Xcode-ontwikkelingscomputer.

## <a name="create-a-notification-hub"></a>Een Notification Hub maken

In deze sectie maakt u een Notification Hub en configureert u verificatie met APNs met het .p12-pushcertificaat of op tokens gebaseerde verificatie. Als u een Notification Hub wilt gebruiken die u al hebt gemaakt, kunt u doorgaan naar stap 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Uw Notification Hub configureren met APNs-gegevens

Selecteer onder **Notification Services** de optie **Apple (APNs)** en voer vervolgens de juiste stappen uit op basis van de methode die u eerder hebt gekozen in de sectie [Een certificaat maken voor Notification Hubs](#creating-a-certificate-for-notification-hubs).  

> [!NOTE]
> Als u uw app ontwikkelt met een App Store- of ad-hocdistributieprofiel, gebruikt u **Productie** als **Toepassingsmodus**. Zo kan uw apparaat pushmeldingen verzenden naar gebruikers die uw app in de Store hebben gekocht.

### <a name="option-1-using-a-p12-push-certificate"></a>OPTIE 1: Een .p12-pushcertificaat gebruiken

1. Selecteer **Certificaat**.

1. Selecteer het bestandspictogram.

1. Selecteer het .p12-bestand dat u eerder hebt geëxporteerd en selecteer vervolgens **Openen**.

1. Geef indien nodig het juiste wachtwoord op.

1. Selecteer de modus **Sandbox**.

    ![APNs-certificering in Azure Portal configureren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Selecteer **Opslaan**.

### <a name="option-2-using-token-based-authentication"></a>OPTIE 2: Op tokens gebaseerde verificatie gebruiken

1. Selecteer **Token**.
1. Voer de volgende waarden in die u eerder hebt verkregen:

    - **Sleutel-id**
    - **Bundel-id**
    - **Team-id**
    - **Token** 

1. Kies **Sandbox**.
1. Selecteer **Opslaan**. 

U hebt nu uw Notification Hub geconfigureerd met APNs. U beschikt ook over de verbindingsreeksen om uw app te registreren en pushmeldingen te verzenden.