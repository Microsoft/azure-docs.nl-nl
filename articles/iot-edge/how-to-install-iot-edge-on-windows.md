---
title: Azure IoT Edge voor Linux installeren op Windows | Microsoft Docs
description: Installatie-instructies Azure IoT Edge op Windows-apparaten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8b549d868aed443e19d639ba6f6df7db20e014b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612111"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Azure IoT Edge voor Linux installeren en inrichten op een Windows-apparaat (preview)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten van PC-klasse naar industriële servers. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, kunt u beginnen met het implementeren van bedrijfslogica vanuit de cloud. Zie [inzicht krijgen in de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md)voor meer informatie.

Met Azure IoT Edge voor Linux in Windows kunt u Azure IoT Edge op Windows-apparaten gebruiken met behulp van virtuele Linux-machines. De Linux-versie van Azure IoT Edge en eventuele linux-modules die worden geïmplementeerd, worden uitgevoerd op de virtuele machine. Van daaruit kunnen Windows-toepassingen en-code en de runtime en modules van de IoT Edge vrij met elkaar communiceren.

In dit artikel worden de stappen beschreven voor het instellen van IoT Edge op een Windows-apparaat. Met deze stappen implementeert u een virtuele Linux-machine die de IoT Edge runtime bevat die moet worden uitgevoerd op uw Windows-apparaat. vervolgens wordt het apparaat ingericht met de IoT Hub apparaat-id.

>[!NOTE]
>IoT Edge voor Linux in Windows is in [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Hoewel IoT Edge voor Linux in Windows de aanbevolen ervaring is voor het gebruik van Azure IoT Edge in een Windows-omgeving, zijn er nog steeds Windows-containers beschikbaar. Als u liever Windows-containers gebruikt, raadpleegt u de hand leiding voor het [installeren en beheren van Azure IOT Edge voor Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een geldig abonnement. Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.

* Een Windows-apparaat met de volgende minimale systeem vereisten:

  * Windows 10 versie 1809 of hoger; Build 17763 of hoger
  * Professional-, Enter prise-of Server-edities
  * Mini maal beschikbaar geheugen: 2 GB
  * Minimale vrije schijf ruimte: 10 GB
  * Als u een nieuwe implementatie maakt met behulp van Windows 10, moet u Hyper-V inschakelen. Zie [Hyper-V installeren op Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v.md)voor meer informatie.
  * Als u een nieuwe implementatie maakt met behulp van Windows Server, moet u de Hyper-V-functie installeren. Zie How to [install the Hyper-V Role op Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server.md)(Engelstalig) voor meer informatie.
  * Als u een nieuwe implementatie maakt met behulp van een VM, moet u ervoor zorgen dat geneste virtualisatie correct is geconfigureerd. Zie de [geneste virtualisatie](nested-virtualization.md) -hand leiding voor meer informatie.

* Toegang tot het Windows-beheer centrum met de uitbrei ding Azure IoT Edge voor Windows-beheer centrum is geïnstalleerd:

   1. Down load het [installatie programma voor het Windows-beheer centrum](https://aka.ms/wacdownload).

   1. Voer het gedownloade installatie programma uit en volg de installatie wizard prompts om Windows-beheer centrum te installeren. 

   1. Nadat de installatie is uitgevoerd, gebruikt u een ondersteunde browser om Windows-beheer centrum te openen. Ondersteunde browsers zijn onder andere micro soft Edge (Windows 10, versie 1709 of hoger), Google Chrome en micro soft Edge Insider.

   1. Bij het eerste gebruik van Windows-beheer centrum wordt u gevraagd om een certificaat te selecteren dat u wilt gebruiken. Selecteer de **Windows-beheer centrum-client** als uw certificaat.

   1. Het is tijd om de Azure IoT Edge extensie te installeren. Selecteer het tandwiel pictogram in de rechter bovenhoek van het dash board van Windows-beheer centrum.

      ![Selecteer het tandwiel pictogram in de rechter bovenhoek van het dash board om toegang te krijgen tot de instellingen.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Klik in het menu **instellingen** onder **Gateway** op **extensies**.

   1. Zoek **Azure IOT Edge** in de lijst met extensies op het tabblad **beschik bare uitbrei dingen** . Kies deze optie en selecteer de **installatie** prompt boven de lijst met extensies.

   1. Nadat de installatie is voltooid, ziet u Azure IoT Edge in de lijst met geïnstalleerde uitbrei dingen op het tabblad **geïnstalleerde uitbrei dingen** .

## <a name="choose-your-provisioning-method"></a>Uw inrichtings methode kiezen

Azure IoT Edge voor Linux in Windows ondersteunt de volgende inrichtings methoden:

* Hand matige inrichting met de connection string van uw IoT Edge-apparaat. Als u deze methode wilt gebruiken, registreert u uw apparaat en haalt u een connection string op met behulp van de stappen in [een IOT edge-apparaat registreren in IOT hub](how-to-register-device.md).
  * Kies de optie voor de verificatie van de symmetrische sleutel, als X. zelfondertekende certificaten van 509 worden momenteel niet ondersteund door IoT Edge voor Linux in Windows.
* Automatische inrichting met behulp van DPS (Device Provisioning Service) en symmetrische sleutels. Meer informatie over [het maken en inrichten van een IOT edge apparaat met DPS en symmetrische sleutels](how-to-auto-provision-symmetric-keys.md).
* Automatische inrichting met DPS en X. 509-certificaten. Meer informatie over [het maken en inrichten van een IOT edge apparaat met DPS en X. 509-certificaten](how-to-auto-provision-x509-certs.md).

Hand matige inrichting is eenvoudiger om aan de slag te gaan met enkele apparaten. De Device Provisioning Service is handig voor het inrichten van een groot aantal apparaten.

Als u van plan bent een van de DPS-methoden te gebruiken om uw apparaat of apparaten in te richten, volgt u de stappen in het desbetreffende artikel hierboven om een instantie van DPS te maken, uw DPS-exemplaar te koppelen aan uw IoT Hub en een DPS-inschrijving te maken. U kunt een *afzonderlijke inschrijving* voor één apparaat of een *groeps registratie* voor een groep apparaten maken. Ga voor meer informatie over de inschrijvings typen naar de [concepten van Azure IOT hub Device Provisioning Service](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Een nieuwe implementatie maken

Maak uw implementatie van Azure IoT Edge voor Linux in Windows op uw doel apparaat.

# <a name="windows-admin-center"></a>[Windows-beheer centrum](#tab/windowsadmincenter)

Op de start pagina van het Windows-beheer centrum wordt onder de lijst met verbindingen een lokale host weer geven die de PC vertegenwoordigt waarop u Windows-beheer centrum uitvoert. Eventuele extra servers, Pc's of clusters die u beheert, worden hier ook weer gegeven.

U kunt Windows-beheer centrum gebruiken voor het installeren en beheren van Azure IoT Edge voor Linux in Windows op uw lokale apparaat of op externe beheerde apparaten. In deze hand leiding wordt de verbinding van de lokale host als doel apparaat gebruikt voor de implementatie van Azure IoT Edge voor Linux in Windows.

Als u wilt implementeren op een extern doel apparaat in plaats van uw lokale apparaat en u het gewenste doel apparaat niet in de lijst ziet, volgt u de [instructies om uw apparaat toe te voegen.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

   ![Eerste Windows-beheer centrum-dash board met weer gegeven doel apparaat](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Selecteer **Toevoegen**.

1. Zoek de **Azure IOT Edge** tegel in het deel venster **resources toevoegen of maken** . Selecteer **nieuwe maken** om een nieuw exemplaar van Azure IOT Edge voor Linux op een apparaat te installeren.

   Als u IoT Edge voor Linux al hebt geïnstalleerd op een apparaat, kunt u **toevoegen** selecteren om verbinding te maken met die bestaande IOT edge apparaat en dit te beheren met Windows-beheer centrum.

   ![Selecteer Nieuw op Azure IoT Edge tegel maken in het Windows-beheer centrum](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Het deel venster **een Azure IOT Edge maken voor Linux in de Windows-implementatie** wordt geopend. Op de **1. Tabblad aan de slag** , Controleer of het doel apparaat voldoet aan de minimale vereisten en selecteer **volgende**.

1. Lees de licentie voorwaarden, Controleer **Ik ga akkoord** en selecteer **volgende**.

1. U kunt **optionele diagnostische gegevens** in-of uitschakelen, afhankelijk van uw voor keur.

1. Selecteer **volgende: implementeren**.

   ![Selecteer de volgende: de knop implementeren nadat u optionele diagnostische gegevens naar uw voor keur hebt geschakeld](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Op de **2.** Ga naar het tabblad implementeren en klik onder **Selecteer een doel apparaat** op het apparaat dat wordt weer gegeven om te valideren dat voldoet aan de minimale vereisten. Zodra de status is bevestigd als ondersteund, selecteert u **volgende**.

   ![Selecteer uw apparaat om te controleren of dit wordt ondersteund](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Controleer op het tabblad **2,2-instellingen** de configuratie-instellingen van uw implementatie. Wanneer u tevreden bent met de instellingen, selecteert u **volgende**.

   ![Controleer de configuratie-instellingen van uw implementatie](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Als u een virtuele Windows-machine gebruikt, is het raadzaam een standaard switch te gebruiken in plaats van een externe switch om ervoor te zorgen dat de virtuele Linux-machine die in de implementatie wordt gemaakt, een IP-adres kan verkrijgen.
   >
   >Als u een standaard switch gebruikt, wordt de virtuele Linux-machine een intern IP-adres toegewezen. Dit interne IP-adres kan niet worden bereikt buiten de virtuele Windows-machine, maar kan worden verbonden met lokaal wanneer u bent aangemeld bij de virtuele Windows-machine.
   >
   >Als u Windows Server gebruikt, moet u er rekening mee houden dat Azure IoT Edge voor Linux in Windows niet automatisch de standaard switch ondersteunt. Zorg ervoor dat de virtuele Linux-machine een IP-adres via de externe switch kan verkrijgen voor een lokale virtuele machine met Windows Server. Voor een virtuele Windows Server-machine in azure stelt u een interne switch in voordat u IoT Edge voor Linux implementeert in Windows.

1. Op het tabblad **2,3-implementatie** kunt u de voortgang van de implementatie bekijken. Het volledige proces omvat het downloaden van de Azure IoT Edge voor Linux op Windows-pakket, het installeren van het pakket, het configureren van het hostapparaat en het instellen van de virtuele Linux-machine. Het kan enkele minuten duren voordat dit proces is voltooid. Hieronder vindt u een geslaagde implementatie.

   ![Bij een geslaagde implementatie worden elke stap weer gegeven met een groen vinkje en een ' volledig ' label](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Zodra de implementatie is voltooid, bent u klaar om uw apparaat in te richten. Selecteer **volgende: verbinding maken** om door te gaan naar de **3. Tabblad verbinden** , waarmee Azure IOT edge apparaten worden ingericht.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installeer IoT Edge voor Linux in Windows op uw doel apparaat als u dat nog niet hebt gedaan.

> [!NOTE]
> In het volgende Power Shell-proces wordt beschreven hoe u een implementatie van een lokale host maakt van Azure IoT Edge voor Linux in Windows. Als u een implementatie wilt maken op een extern doel apparaat met behulp van Power shell, kunt u [externe Power shell](/powershell/module/microsoft.powershell.core/about/about_remote) gebruiken om een verbinding met een extern apparaat tot stand te brengen en deze opdrachten op afstand op dat apparaat uit te voeren.

1. Voer in een Power shell-sessie met verhoogde bevoegdheid elk van de volgende opdrachten uit om IoT Edge voor Linux te downloaden in Windows.

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
   > U kunt aangepaste IoT Edge voor Linux opgeven in Windows-installatie en VHDX-directory's door de para meters INSTALLDIR = "<FULLY_QUALIFIED_PATH>" en VHDXDIR = "<FULLY_QUALIFIED_PATH>" toe te voegen aan de bovenstaande installatie opdracht.

1. Als u de implementatie wilt uitvoeren, moet u het uitvoerings beleid op het doel apparaat instellen op `AllSigned` als dit nog niet is gebeurd. U kunt het huidige uitvoerings beleid controleren in een Power shell-prompt met verhoogde bevoegdheid met:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Als het uitvoerings beleid van `local machine` niet het geval is `AllSigned` , kunt u het uitvoerings beleid instellen met behulp van:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Maak de IoT Edge voor Linux op Windows-implementatie.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > U kunt deze opdracht zonder para meters uitvoeren of implementatie optioneel aanpassen met para meters. Raadpleeg [de naslag informatie over het IOT Edge voor Linux op Windows Power shell-script voor een overzicht van de](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) betekenissen en standaard waarden voor para meters.

1. Voer ' Y ' in om de licentie voorwaarden te accepteren.

1. Voer ' O ' of ' R ' in om **optionele diagnostische gegevens** in of uit te scha kelen, afhankelijk van uw voor keur. Hieronder vindt u een geslaagde implementatie.

   ![Bij een geslaagde implementatie worden aan het einde van de berichten ' implementatie voltooid ' weer](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Zodra de implementatie is voltooid, bent u klaar om uw apparaat in te richten.

---

Als u uw apparaat wilt inrichten, kunt u de onderstaande koppelingen volgen om naar de sectie voor de geselecteerde inrichtings methode te gaan:

* [Optie 1: hand matig inrichten met de connection string van uw IoT Edge apparaat](#option-1-provisioning-manually-using-the-connection-string)
* [Optie 2: automatisch inrichten met behulp van DPS (Device Provisioning Service) en symmetrische sleutels](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Optie 3: automatische inrichting met DPS en X. 509-certificaten](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Uw apparaat inrichten

Kies een methode voor het inrichten van uw apparaat en volg de instructies in de desbetreffende sectie. U kunt het Windows-beheer centrum of een Power shell-sessie met verhoogde bevoegdheid gebruiken om uw apparaten in te richten.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Optie 1: hand matig inrichten met behulp van de connection string

In deze sectie wordt beschreven hoe u uw apparaat hand matig inricht met behulp van de connection string van uw Azure IoT Edge apparaat.

# <a name="windows-admin-center"></a>[Windows-beheer centrum](#tab/windowsadmincenter)

1. Selecteer in het deel venster voor het **inrichten van apparaten Azure IOT Edge** de optie **verbindings reeks (hand matig)** in de vervolg keuzelijst inrichtings methode.

1. Ga in het [Azure Portal](https://ms.portal.azure.com/)naar het tabblad **IoT Edge** van de IOT hub.

1. Klik op de apparaat-ID van het apparaat. Kopieer het veld voor de **primaire verbindings reeks** .

1. Plak deze in het veld apparaat connection string in het Windows-beheer centrum. Kies vervolgens **inrichten met de geselecteerde methode**.

   ![Kies inrichten met de geselecteerde methode nadat u de connection string van het apparaat hebt geplakt](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Wanneer het inrichten is voltooid, selecteert u **volt ooien**. U gaat terug naar het hoofd dashboard. Nu moet er een nieuw apparaat worden weer gegeven, waarvan het type is `IoT Edge Devices` . U kunt het IoT Edge apparaat selecteren om verbinding mee te maken. U kunt op de pagina **overzicht** de **IOT Edge Module lijst** en **IOT Edge status** van uw apparaat weer geven.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Ga in het [Azure Portal](https://ms.portal.azure.com/)naar het tabblad **IoT Edge** van de IOT hub.

1. Klik op de apparaat-ID van het apparaat. Kopieer het veld voor de **primaire verbindings reeks** .

1. Plak de tekst van de tijdelijke aanduiding in de volgende opdracht en voer deze uit in een Power shell-sessie met verhoogde bevoegdheid op uw doel apparaat.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Optie 2: inrichten via DPS met behulp van symmetrische sleutels

In deze sectie wordt beschreven hoe u uw apparaat automatisch inricht met behulp van DPS en symmetrische sleutels.

# <a name="windows-admin-center"></a>[Windows-beheer centrum](#tab/windowsadmincenter)

1. Selecteer in het deel venster voor het **inrichten van apparaten Azure IOT Edge** de optie **symmetrische sleutel (DPS)** uit de vervolg keuzelijst voor de inrichtings methode.

1. Ga in het [Azure Portal](https://ms.portal.azure.com/)naar het DPS-exemplaar.

1. Kopieer op het tabblad **overzicht** de waarde van het **id-bereik** . Plak deze in het veld bereik-ID in het Windows-beheer centrum.

1. Selecteer op het tabblad **inschrijvingen beheren** in de Azure Portal de registratie die u hebt gemaakt. Kopieer de waarde van de **primaire sleutel** in de details van de inschrijving. Plak deze in het veld symmetrische sleutel in het Windows-beheer centrum.

1. Geef de registratie-ID van het apparaat op in het veld registratie-ID in het Windows-beheer centrum.

1. Kies **inrichten met de geselecteerde methode**.

   ![Kies inrichten met de geselecteerde methode nadat u de vereiste velden voor het inrichten van symmetrische sleutels hebt ingevuld](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Wanneer het inrichten is voltooid, selecteert u **volt ooien**. U gaat terug naar het hoofd dashboard. Nu moet er een nieuw apparaat worden weer gegeven, waarvan het type is `IoT Edge Devices` . U kunt het IoT Edge apparaat selecteren om verbinding mee te maken. U kunt op de pagina **overzicht** de **IOT Edge Module lijst** en **IOT Edge status** van uw apparaat weer geven.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopieer de volgende opdracht naar een teksteditor. Vervang de tijdelijke aanduiding voor tekst door uw gegevens zoals beschreven.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. Ga in het [Azure Portal](https://ms.portal.azure.com/)naar het DPS-exemplaar.

1. Kopieer op het tabblad **overzicht** de waarde van het **id-bereik** . Plak de tekst over de juiste tijdelijke aanduiding in de opdracht.

1. Selecteer op het tabblad **inschrijvingen beheren** in de Azure Portal de registratie die u hebt gemaakt. Kopieer de waarde van de **primaire sleutel** in de details van de inschrijving. Plak de tekst over de juiste tijdelijke aanduiding in de opdracht.

1. Geef de registratie-ID van het apparaat op om de juiste tijdelijke tekst in de opdracht te vervangen.

1. Voer de opdracht uit in een Power shell-sessie met verhoogde bevoegdheden op het doel apparaat.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Optie 3: inrichten via DPS met behulp van X. 509-certificaten

In deze sectie wordt beschreven hoe u uw apparaat automatisch inricht met behulp van DPS en X. 509-certificaten.

# <a name="windows-admin-center"></a>[Windows-beheer centrum](#tab/windowsadmincenter)

1. Selecteer in het deel venster voor het **inrichten van apparaten Azure IOT Edge** de optie **X. 509-certificaat (DPS)** in de vervolg keuzelijst voor de inrichtings methode.

1. Ga in het [Azure Portal](https://ms.portal.azure.com/)naar het DPS-exemplaar.

1. Kopieer op het tabblad **overzicht** de waarde van het **id-bereik** . Plak deze in het veld bereik-ID in het Windows-beheer centrum.

1. Geef de registratie-ID van het apparaat op in het veld registratie-ID in het Windows-beheer centrum.

1. Upload uw certificaat en persoonlijke sleutel bestanden.

1. Kies **inrichten met de geselecteerde methode**.

   ![Kies inrichten met de geselecteerde methode na het invullen van de vereiste velden voor X. 509-certificaat inrichting](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Wanneer het inrichten is voltooid, selecteert u **volt ooien**. U gaat terug naar het hoofd dashboard. Nu moet er een nieuw apparaat worden weer gegeven, waarvan het type is `IoT Edge Devices` . U kunt het IoT Edge apparaat selecteren om verbinding mee te maken. U kunt op de pagina **overzicht** de **IOT Edge Module lijst** en **IOT Edge status** van uw apparaat weer geven.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopieer de volgende opdracht naar een teksteditor. Vervang de tijdelijke aanduiding voor tekst door uw gegevens zoals beschreven.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. Ga in het [Azure Portal](https://ms.portal.azure.com/)naar het DPS-exemplaar.

1. Kopieer op het tabblad **overzicht** de waarde van het **id-bereik** . Plak de tekst over de juiste tijdelijke aanduiding in de opdracht.

1. Geef de registratie-ID van het apparaat op om de juiste tijdelijke tekst in de opdracht te vervangen.

1. Vervang de juiste tijdelijke aanduiding voor tekst door het absolute bronpad naar uw certificaat bestand.

1. Vervang de juiste tijdelijke aanduiding voor tekst door het absolute bronpad naar uw persoonlijke sleutel bestand.

1. Voer de opdracht uit in een Power shell-sessie met verhoogde bevoegdheden op het doel apparaat.

---

## <a name="verify-successful-configuration"></a>Geslaagde configuratie controleren

Controleer of IoT Edge voor Linux in Windows is geïnstalleerd en geconfigureerd op uw IoT Edge-apparaat.

# <a name="windows-admin-center"></a>[Windows-beheer centrum](#tab/windowsadmincenter)

1. Selecteer uw IoT Edge-apparaat in de lijst met verbonden apparaten in het Windows-beheer centrum om er verbinding mee te maken.

1. Op de overzichts pagina van het apparaat wordt een aantal informatie over het apparaat weer gegeven:

    1. In de sectie **lijst van IOT Edge module** worden de actieve modules op het apparaat weer gegeven. Wanneer de IoT Edge-service voor de eerste keer wordt gestart, ziet u alleen de **edgeAgent** -module. De edgeAgent-module wordt standaard uitgevoerd en helpt bij het installeren en starten van aanvullende modules die u op uw apparaat implementeert.
    1. In het gedeelte **IOT Edge status** ziet u de status van de service en moet deze worden gerapporteerd als **actief (wordt uitgevoerd)**.

1. Als u de IoT Edge-service moet oplossen, gebruikt u het **opdracht shell** -hulp programma op de pagina apparaat om SSH (Secure Shell) te gebruiken in de virtuele machine en voert u de Linux-opdrachten uit.

    1. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

       ```bash
       journalctl -u iotedge
       ```

    2. Gebruik het `check` hulp programma om de configuratie en verbindings status van het apparaat te controleren.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Meld u aan bij uw IoT Edge voor Linux op virtuele Windows-machine met behulp van de volgende opdracht in uw Power shell-sessie:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >Het enige account dat is toegestaan voor SSH naar de virtuele machine is de gebruiker die het heeft gemaakt.

1. Wanneer u bent aangemeld, kunt u de lijst met actieve IoT Edge-modules controleren met behulp van de volgende Linux-opdracht:

   ```bash
   iotedge list
   ```

1. Als u de IoT Edge-service moet oplossen, gebruikt u de volgende Linux-opdrachten.

    1. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

       ```bash
       journalctl -u iotedge
       ```

    2. Gebruik het `check` hulp programma om de configuratie en verbindings status van het apparaat te controleren.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Volgende stappen

* Ga door met het [implementeren van IOT Edge-modules](how-to-deploy-modules-portal.md) voor meer informatie over het implementeren van modules op uw apparaat.
* Meer informatie over het [beheren van certificaten op uw IOT Edge voor Linux op virtuele Windows-machines](how-to-manage-device-certificates.md) en over het overdragen van bestanden van het hostbesturingssysteem naar uw virtuele Linux-machine.
* Meer informatie over het [configureren van uw IOT edge-apparaten voor communicatie via een proxy server](how-to-configure-proxy-support.md).
