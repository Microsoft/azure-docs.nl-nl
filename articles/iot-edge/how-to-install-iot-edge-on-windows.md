---
title: Een Azure IoT Edge linux installeren in Windows | Microsoft Docs
description: Azure IoT Edge installeren op Windows-apparaten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2799e25dbd84ff07b375c6fa1b103789aae82b49
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538425"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Azure IoT Edge voor Linux installeren en inrichten op een Windows-apparaat (preview)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

De Azure IoT Edge runtime verandert een apparaat in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten van pc-klasse naar industriële servers. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, kunt u beginnen met het implementeren van bedrijfslogica vanuit de cloud. Zie Inzicht in de Azure IoT Edge [runtime en de architectuur ervan voor meer informatie.](iot-edge-runtime.md)

Azure IoT Edge linux in Windows kunt u virtuele Linux-machines gebruiken Azure IoT Edge Windows-apparaten. De Linux-versie van Azure IoT Edge en alle Linux-modules die mee zijn geïmplementeerd, worden uitgevoerd op de virtuele machine. Van hieruit kunnen Windows-toepassingen en -code en de IoT Edge runtime en modules vrij met elkaar communiceren.

In dit artikel worden de stappen beschreven voor het instellen IoT Edge op een Windows-apparaat. Met deze stappen wordt een virtuele Linux-machine geïmplementeerd die de IoT Edge-runtime bevat die op uw Windows-apparaat moet worden uitgevoerd. Vervolgens wordt het apparaat ingericht met de IoT Hub-apparaat-id.

>[!NOTE]
>IoT Edge voor Linux in Windows is in [openbare preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>
>Hoewel IoT Edge voor Linux in Windows de aanbevolen ervaring is voor het gebruik van Azure IoT Edge in een Windows-omgeving, zijn Windows-containers nog steeds beschikbaar. Als u liever Windows-containers gebruikt, bekijkt u de handleiding voor het installeren en beheren [van Azure IoT Edge voor Windows.](how-to-install-iot-edge-windows-on-windows.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een geldig abonnement. Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.

* Een Windows-apparaat met de volgende minimale systeemvereisten:

  * Windows 10 versie 1809 of hoger; build 17763 of hoger
  * Professional-, Enterprise- of Server-edities
  * Minimaal vrij geheugen: 1 GB
  * Minimale vrije schijfruimte: 10 GB
  * Als u een nieuwe implementatie maakt met behulp van Windows 10, moet u Hyper-V inschakelen. Zie Voor meer informatie [hyper-V installeren](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)op Windows 10.
  * Als u een nieuwe implementatie maakt met Windows Server, moet u de Hyper-V-functie installeren. Zie de [Hyper-V-functie](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)installeren op Windows Server voor meer informatie.
  * Als u een nieuwe implementatie maakt met behulp van een virtuele machine, moet u ervoor zorgen dat u geneste virtualisatie correct configureert. Zie de handleiding voor [geneste virtualisatie voor meer](nested-virtualization.md) informatie.

* Toegang tot Windows Admin Center met de Azure IoT Edge-extensie voor Windows Admin Center geïnstalleerd:

   1. Download het [Windows Admin Center installatieprogramma](https://aka.ms/wacdownload).

   1. Voer het gedownloade installatieprogramma uit en volg de aanwijzingen in de installatiewizard om het Windows Admin Center. 

   1. Nadat de app is geïnstalleerd, gebruikt u een ondersteunde browser om de Windows Admin Center. Ondersteunde browsers zijn Microsoft Edge (Windows 10, versie 1709 of hoger), Google Chrome en Microsoft Edge Insider.

   1. Bij het eerste gebruik van Windows Admin Center wordt u gevraagd een certificaat te selecteren dat u wilt gebruiken. Selecteer **Windows Admin Center Client** als uw certificaat.

   1. Het is tijd om de extensie Azure IoT Edge installeren. Selecteer het tandwielpictogram rechtsboven in het Windows Admin Center dashboard.

      ![Selecteer het tandwielpictogram rechtsboven in het dashboard om toegang te krijgen tot de instellingen.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Selecteer in **het** menu Instellingen onder **Gateway** de optie **Extensies.**

   1. Zoek op **het tabblad** Beschikbare extensies **Azure IoT Edge** in de lijst met extensies. Kies deze optie en selecteer de **installatieprompt** boven de lijst met extensies.

   1. Nadat de installatie is voltooid, ziet u Azure IoT Edge in de lijst met geïnstalleerde extensies op **het tabblad Geïnstalleerde extensies.**

## <a name="choose-your-provisioning-method"></a>Uw inrichtingsmethode kiezen

Azure IoT Edge linux in Windows ondersteunt de volgende inrichtingsmethoden:

* Handmatig inrichten met behulp van IoT Edge apparaatapparaat connection string. Als u deze methode wilt gebruiken, registreert u uw apparaat en haalt u een connection string op met behulp van de stappen in [Register an IoT Edge device in IoT Hub](how-to-register-device.md).
  * Kies de verificatieoptie voor symmetrische sleutels, omdat zelf-ondertekende X.509-certificaten momenteel niet worden ondersteund door IoT Edge voor Linux in Windows.
* Automatisch inrichten met Device Provisioning Service (DPS) en symmetrische sleutels. Meer informatie over [het maken en inrichten van IoT Edge apparaat met DPS en symmetrische sleutels.](how-to-auto-provision-symmetric-keys.md)
* Automatische inrichting met BEHULP van DPS- en X.509-certificaten. Meer informatie over [het maken en inrichten van IoT Edge apparaat met DPS- en X.509-certificaten.](how-to-auto-provision-x509-certs.md)

Handmatige inrichting is eenvoudiger om met een paar apparaten aan de slag te gaan. Device Provisioning Service is handig voor het inrichten van veel apparaten.

Als u van plan bent een van de DPS-methoden te gebruiken voor het inrichten van uw apparaat of apparaten, volgt u de stappen in het betreffende artikel hierboven om een exemplaar van DPS te maken, uw DPS-exemplaar te koppelen aan uw IoT Hub en een DPS-inschrijving te maken. U kunt een *afzonderlijke inschrijving maken* voor één apparaat of een *groepsinschrijving* voor een groep apparaten. Ga naar de concepten Azure IoT Hub Device Provisioning Service voor meer informatie over de [inschrijvingstypen.](../iot-dps/concepts-service.md#enrollment)

## <a name="create-a-new-deployment"></a>Een nieuwe implementatie maken

Maak uw implementatie van Azure IoT Edge voor Linux in Windows op uw doelapparaat.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Op de Windows Admin Center startpagina, onder de lijst met verbindingen, ziet u een lokale hostverbinding die de pc vertegenwoordigt waarop u de Windows Admin Center. Eventuele extra servers, pc's of clusters die u beheert, worden hier ook weer aangeboden.

U kunt deze Windows Admin Center installeren en beheren Azure IoT Edge Linux in Windows op uw lokale apparaat of extern beheerde apparaten. In deze handleiding dient de lokale hostverbinding als het doelapparaat voor de implementatie van Azure IoT Edge voor Linux in Windows.

Als u wilt implementeren op een extern doelapparaat in plaats van uw lokale apparaat en u het gewenste doelapparaat niet in de lijst ziet staan, volgt u de instructies om uw apparaat toe [te voegen.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Eerste Windows Admin Center dashboard met doelapparaat vermeld](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Selecteer **Toevoegen**.

1. Zoek in **het deelvenster Resources toevoegen of** maken de **tegel Azure IoT Edge** maken. Selecteer **Nieuwe maken** om een nieuw exemplaar van een Azure IoT Edge linux op Windows op een apparaat te installeren.

   Als u al een IoT Edge voor Linux in Windows hebt  die wordt uitgevoerd op een apparaat, kunt u Toevoegen selecteren om verbinding te maken met dat bestaande IoT Edge-apparaat en dit beheren met Windows Admin Center.

   ![Selecteer Nieuwe maken op Azure IoT Edge tegel in Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Het **deelvenster Een Azure IoT Edge voor Linux in Windows-implementatie** wordt geopend. Op de **1. Aan de slag** het tabblad Controleren of uw doelapparaat voldoet aan de minimale vereisten en selecteer **Volgende.**

1. Lees de licentievoorwaarden, schakel **Ik ga akkoord in** en selecteer **Volgende.**

1. U kunt Optionele **diagnostische gegevens in-** of uitschakelen, afhankelijk van uw voorkeur.

1. Selecteer **Volgende: Implementeren.**

   ![Selecteer de knop Volgende: Implementeren nadat u optionele diagnostische gegevens naar uw voorkeur hebt geklikt](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Op de **2. Klik** op het tabblad Implementeren **onder Een doelapparaat selecteren** op het vermelde apparaat om te controleren of het voldoet aan de minimale vereisten. Zodra de status is bevestigd als ondersteund, selecteert u **Volgende.**

   ![Selecteer uw apparaat om te controleren of het wordt ondersteund](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Controleer op **het tabblad 2.2 Settings** de configuratie-instellingen van uw implementatie. Wanneer u tevreden bent met de instellingen, selecteert u **Volgende.**

   ![De configuratie-instellingen van uw implementatie controleren](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Als u een virtuele Windows-machine gebruikt, is het raadzaam om een standaardsschakelaar te gebruiken in plaats van een externe switch om ervoor te zorgen dat de virtuele Linux-machine die in de implementatie is gemaakt, een IP-adres kan verkrijgen.
   >
   >Met een standaardsschakelaar wordt aan de virtuele Linux-machine een intern IP-adres toegewezen. Dit interne IP-adres kan niet van buiten de virtuele Windows-machine worden bereikt, maar kan wel lokaal worden verbonden terwijl u bent aangemeld bij de virtuele Windows-machine.
   >
   >Als u Windows Server gebruikt, moet u er rekening mee Azure IoT Edge voor Linux in Windows niet automatisch ondersteuning biedt voor de standaardschakelaar. Zorg ervoor dat de virtuele Linux-machine een IP-adres kan verkrijgen via de externe switch voor een lokale virtuele Windows Server-machine. Voor een virtuele Windows Server-machine in Azure stelt u een interne switch in voordat u een IoT Edge linux in Windows implementeert.

1. Op het **tabblad 2.3 Deployment** kunt u de voortgang van de implementatie bekijken. Het volledige proces omvat het downloaden van het Azure IoT Edge voor Linux op Windows-pakket, het installeren van het pakket, het configureren van het hostapparaat en het instellen van de virtuele Linux-machine. Dit proces kan enkele minuten duren. Hieronder ziet u een geslaagde implementatie.

   ![Bij een geslaagde implementatie wordt elke stap met een groen vinkje en een label Voltooid](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Zodra de implementatie is voltooid, kunt u uw apparaat inrichten. Selecteer **Volgende: Verbinding maken** om door te gaan naar de **3. Het** tabblad Verbinding maken, waarmee het Azure IoT Edge apparaat wordt verwerkt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installeer IoT Edge linux in Windows op uw doelapparaat als u dat nog niet hebt gedaan.

> [!NOTE]
> In het volgende PowerShell-proces wordt beschreven hoe u een lokale hostimplementatie van Azure IoT Edge voor Linux in Windows maakt. Als u een implementatie op een extern doelapparaat wilt maken met behulp van [PowerShell,](/powershell/module/microsoft.powershell.core/about/about_remote) kunt u Remote PowerShell gebruiken om een verbinding met een extern apparaat tot stand te brengen en deze opdrachten op afstand op dat apparaat uit te voeren.

1. Voer in een PowerShell-sessie met verhoogde bevoegdheid elk van de volgende opdrachten uit om de IoT Edge Linux op Windows te downloaden.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installeer IoT Edge voor Linux in Windows op uw apparaat.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > U kunt aangepaste IoT Edge voor Linux op Windows-installatie en VHDX-directories opgeven door de parameters INSTALLDIR="<FULLY_QUALIFIED_PATH>" en VHDXDIR="<FULLY_QUALIFIED_PATH>" toe te voegen aan de bovenstaande installatieopdracht.

1. Als u de implementatie wilt uitvoeren, moet u het uitvoeringsbeleid op het doelapparaat instellen op als dit `AllSigned` nog niet is gedaan. U kunt het huidige uitvoeringsbeleid controleren in een PowerShell-prompt met verhoogde bevoegdheid met behulp van:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Als het uitvoeringsbeleid van `local machine` niet `AllSigned` is, kunt u het uitvoeringsbeleid instellen met behulp van:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Maak de IoT Edge voor Linux in Windows-implementatie.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > U kunt deze opdracht uitvoeren zonder parameters of optioneel de implementatie aanpassen met parameters. Raadpleeg de naslag [voor linux IoT Edge Linux op](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) Windows PowerShell script om de betekenis van parameters en standaardwaarden te bekijken.

1. Voer 'Y' in om de licentievoorwaarden te accepteren.

1. Voer O of R in om  Optionele diagnostische gegevens in of uit te schakelen, afhankelijk van uw voorkeur. Hieronder ziet u een geslaagde implementatie.

   ![Bij een geslaagde implementatie staat 'Implementatie geslaagd' aan het einde van de berichten](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Zodra de implementatie is voltooid, kunt u uw apparaat inrichten.

---

Als u uw apparaat wilt inrichten, kunt u de onderstaande koppelingen volgen om naar de sectie voor de geselecteerde inrichtingsmethode te gaan:

* [Optie 1: Handmatig inrichten met behulp van de IoT Edge van uw connection string](#option-1-provisioning-manually-using-the-connection-string)
* [Optie 2: Automatisch inrichten met Device Provisioning Service (DPS) en symmetrische sleutels](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Optie 3: Automatisch inrichten met BEHULP van DPS- en X.509-certificaten](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Uw apparaat inrichten

Kies een methode voor het inrichten van uw apparaat en volg de instructies in de betreffende sectie. U kunt de Windows Admin Center of een PowerShell-sessie met verhoogde bevoegdheid gebruiken om uw apparaten in terichten.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Optie 1: handmatig inrichten met behulp van de connection string

Deze sectie bevat informatie over het handmatig inrichten van uw apparaat met behulp Azure IoT Edge van uw connection string.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Selecteer in Azure IoT Edge het deelvenster Apparaat **inrichten** de optie Verbindingsreeks **(handmatig)** in de vervolgkeuze lijst met inrichtingsmethodes.

1. Ga in [Azure Portal](https://ms.portal.azure.com/)naar het **tabblad IoT Edge** van uw IoT Hub.

1. Klik op de apparaat-id van uw apparaat. Kopieer het **veld Primaire verbindingsreeks.**

1. Plak deze in het veld connection string apparaat in het Windows Admin Center. Kies vervolgens **Inrichten met de geselecteerde methode**.

   ![Inrichting kiezen met de geselecteerde methode nadat u de gegevens van uw apparaat connection string](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Zodra het inrichten is voltooid, selecteert u **Voltooien.** U gaat terug naar het hoofddashboard. Nu wordt er een nieuw apparaat weergegeven, waarvan het type `IoT Edge Devices` is. U kunt het IoT Edge apparaat selecteren om er verbinding mee te maken. Op de pagina **Overzicht** kunt u de lijst met **module IoT Edge** bekijken en **IoT Edge status van** uw apparaat weergeven.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Ga in [Azure Portal](https://ms.portal.azure.com/)naar het **tabblad IoT Edge** van uw IoT Hub.

1. Klik op de apparaat-id van uw apparaat. Kopieer het **veld Primaire verbindingsreeks.**

1. Plak de tekst van de tijdelijke aanduiding in de volgende opdracht en voer deze uit in een PowerShell-sessie met verhoogde bevoegdheid op uw doelapparaat.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Optie 2: Inrichten via DPS met behulp van symmetrische sleutels

Deze sectie gaat over het automatisch inrichten van uw apparaat met behulp van DPS en symmetrische sleutels.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Selecteer in **Azure IoT Edge deelvenster** voor apparaatinrichting de optie Symmetrische sleutel **(DPS)** in de vervolgkeuze lijst met inrichtingsmethodes.

1. [Navigeer in Azure Portal](https://ms.portal.azure.com/)naar uw DPS-exemplaar.

1. Kopieer op **het tabblad** Overzicht de waarde **id-bereik.** Plak deze in het veld Bereik-id in het Windows Admin Center.

1. Selecteer op **het tabblad Inschrijvingen beheren** in Azure Portal de inschrijving die u hebt gemaakt. Kopieer de **waarde van de** primaire sleutel in de inschrijvingsdetails. Plak deze in het veld met de symmetrische sleutel in Windows Admin Center.

1. Geef de registratie-id van uw apparaat op in het veld Registratie-id in Windows Admin Center.

1. Kies **Inrichten met de geselecteerde methode**.

   ![Inrichting kiezen met de geselecteerde methode na het invullen van de vereiste velden voor het inrichten van symmetrische sleutels](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Zodra het inrichten is voltooid, selecteert u **Voltooien.** U gaat terug naar het hoofddashboard. Als het goed is, ziet u nu een nieuw apparaat met het type `IoT Edge Devices` . U kunt het IoT Edge apparaat selecteren om er verbinding mee te maken. Op de pagina **Overzicht** kunt u de lijst IoT Edge **module bekijken en** IoT Edge status **van** uw apparaat weergeven.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopieer de volgende opdracht naar een teksteditor. Vervang de tekst van de tijdelijke aanduiding door de gedetailleerde informatie.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. [Navigeer in Azure Portal](https://ms.portal.azure.com/)naar uw DPS-exemplaar.

1. Kopieer op **het tabblad** Overzicht de waarde **id-bereik.** Plak deze over de juiste tekst van de tijdelijke aanduiding in de opdracht .

1. Selecteer op **het tabblad Inschrijvingen beheren** in Azure Portal de inschrijving die u hebt gemaakt. Kopieer de **waarde van de** primaire sleutel in de inschrijvingsdetails. Plak deze over de juiste tekst van de tijdelijke aanduiding in de opdracht .

1. Geef de registratie-id van het apparaat op om de juiste tekst van de tijdelijke aanduiding in de opdracht te vervangen.

1. Voer de opdracht uit in een PowerShell-sessie met verhoogde bevoegdheid op het doelapparaat.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Optie 3: Inrichten via DPS met behulp van X.509-certificaten

Deze sectie gaat over het automatisch inrichten van uw apparaat met behulp van DPS- en X.509-certificaten.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Selecteer in **Azure IoT Edge inrichtingsvenster** **X.509 Certificate (DPS)** in de vervolgkeuzekeuze lijst met inrichtingsmethodes.

1. [Navigeer in Azure Portal](https://ms.portal.azure.com/)naar uw DPS-exemplaar.

1. Kopieer op **het tabblad** Overzicht de waarde **id-bereik.** Plak deze in het veld Bereik-id in Windows Admin Center.

1. Geef de registratie-id van uw apparaat op in het veld Registratie-id in Windows Admin Center.

1. Upload uw certificaat en persoonlijke sleutelbestanden.

1. Kies **Inrichten met de geselecteerde methode**.

   ![Inrichting kiezen met de geselecteerde methode na het invullen van de vereiste velden voor het inrichten van X.509-certificaten](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Zodra het inrichten is voltooid, selecteert u **Voltooien.** U gaat terug naar het hoofddashboard. Nu wordt er een nieuw apparaat weergegeven, waarvan het type `IoT Edge Devices` is. U kunt het IoT Edge apparaat selecteren om er verbinding mee te maken. Op de pagina **Overzicht** kunt u de lijst met **module IoT Edge** bekijken en **IoT Edge status van** uw apparaat weergeven.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopieer de volgende opdracht naar een teksteditor. Vervang de tekst van de tijdelijke aanduiding door de gedetailleerde informatie.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. [Navigeer in Azure Portal](https://ms.portal.azure.com/)naar uw DPS-exemplaar.

1. Kopieer op **het tabblad** Overzicht de waarde **id-bereik.** Plak deze over de juiste tekst van de tijdelijke aanduiding in de opdracht .

1. Geef de registratie-id van het apparaat op om de juiste tekst van de tijdelijke aanduiding in de opdracht te vervangen.

1. Vervang de juiste tekst van de tijdelijke aanduiding door het absolute bronpad naar uw certificaatbestand.

1. Vervang de juiste tekst van de tijdelijke aanduiding door het absolute bronpad naar uw persoonlijke sleutelbestand.

1. Voer de opdracht uit in een PowerShell-sessie met verhoogde bevoegdheid op het doelapparaat.

---

## <a name="verify-successful-configuration"></a>Controleer of de configuratie is geslaagd

Controleer of IoT Edge linux in Windows is geïnstalleerd en geconfigureerd op uw IoT Edge apparaat.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Selecteer uw IoT Edge apparaat in de lijst met verbonden apparaten in Windows Admin Center om er verbinding mee te maken.

1. Op de overzichtspagina van het apparaat wordt informatie over het apparaat weergegeven:

    1. In **IoT Edge modulelijst ziet** u de modules die op het apparaat worden uitgevoerd. Wanneer de IoT Edge voor het eerst wordt gestart, ziet u dat alleen de **edgeAgent-module wordt** uitgevoerd. De edgeAgent-module wordt standaard uitgevoerd en helpt bij het installeren en starten van aanvullende modules die u op uw apparaat implementeert.
    1. In **IoT Edge status ziet** u de servicestatus en moet actief zijn **(actief) worden gemeld.**

1. Als u problemen met de IoT Edge-service  wilt oplossen, gebruikt u het opdrachtshell-hulpprogramma op de apparaatpagina om ssh (Secure Shell) in te voeren op de virtuele machine en de Linux-opdrachten uit te voeren.

    1. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

       ```bash
       journalctl -u iotedge
       ```

    2. Gebruik het `check` hulpprogramma om de configuratie en verbindingsstatus van het apparaat te controleren.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Meld u aan bij uw IoT Edge Linux op een virtuele Windows-machine met behulp van de volgende opdracht in uw PowerShell-sessie:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >Het enige account dat is toegestaan voor SSH voor de virtuele machine is de gebruiker die deze heeft gemaakt.

1. Zodra u bent aangemeld, kunt u de lijst met de IoT Edge modules controleren met behulp van de volgende Linux-opdracht:

   ```bash
   iotedge list
   ```

1. Als u problemen met de IoT Edge service wilt oplossen, gebruikt u de volgende Linux-opdrachten.

    1. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

       ```bash
       journalctl -u iotedge
       ```

    2. Gebruik het `check` hulpprogramma om de configuratie en verbindingsstatus van het apparaat te controleren.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Volgende stappen

* Ga door [met het IoT Edge modules voor](how-to-deploy-modules-portal.md) meer informatie over het implementeren van modules op uw apparaat.
* Meer informatie over het beheren van certificaten op uw IoT Edge voor Linux op een virtuele [Windows-machine](how-to-manage-device-certificates.md) en het overdragen van bestanden van het host-besturingssysteem naar uw virtuele Linux-machine.
* Meer informatie over het [configureren van IoT Edge apparaten om te communiceren via een proxyserver.](how-to-configure-proxy-support.md)
