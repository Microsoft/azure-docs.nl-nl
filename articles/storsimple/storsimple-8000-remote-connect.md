---
title: Extern verbinding maken met uw StorSimple-apparaat
description: Hierin wordt uitgelegd hoe u uw apparaat configureert voor extern beheer en hoe u verbinding maakt met Windows PowerShell voor StorSimple via HTTP of HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da688dfb00b26ca6b561d5aa0fb548c221381c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514580"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Extern verbinding maken met uw StorSimple 8000 Series-apparaat

## <a name="overview"></a>Overzicht

Via Windows Power shell kunt u op afstand verbinding maken met uw apparaat. Wanneer u op deze manier verbinding maakt, wordt er geen menu weer geven. (U ziet een menu alleen als u de seriële console op het apparaat gebruikt om verbinding te maken.) Met Windows Power shell Remoting maakt u verbinding met een specifieke runs Pace. U kunt ook de weergave taal opgeven.

Ga voor meer informatie over het gebruik van Windows Power shell voor externe toegang tot het beheren van uw apparaat naar [Windows PowerShell voor StorSimple gebruiken om uw StorSimple-apparaat te](storsimple-8000-windows-powershell-administration.md)beheren.

In deze zelf studie wordt uitgelegd hoe u uw apparaat configureert voor extern beheer en hoe u verbinding maakt met Windows PowerShell voor StorSimple. U kunt HTTP of HTTPS gebruiken om extern verbinding te maken via Windows Power shell. Houd echter rekening met de volgende informatie als u wilt bepalen hoe u verbinding maakt met Windows PowerShell voor StorSimple:

* Rechtstreeks verbinding maken met de seriële console van het apparaat is beveiligd, maar het maken van verbinding met de seriële console via netwerk switches is niet. Wees voorzichtig met het beveiligings risico wanneer u verbinding maakt met de seriële console van het apparaat via netwerk switches.
* Het maken van een verbinding via een HTTP-sessie kan meer beveiliging bieden dan via de seriële console via het netwerk verbinding kan maken. Hoewel dit niet de veiligste methode is, is het acceptabel voor vertrouwde netwerken.
* Het is de veiligste en aanbevolen optie om verbinding te maken via een HTTPS-sessie met een zelfondertekend certificaat.

U kunt extern verbinding maken met de Windows Power shell-interface. Externe toegang tot uw StorSimple-apparaat via de Windows Power shell-interface is echter niet standaard ingeschakeld. U moet eerst extern beheer op het apparaat inschakelen en vervolgens op de client die wordt gebruikt om toegang te krijgen tot uw apparaat.

De stappen die in dit artikel worden beschreven, zijn uitgevoerd op een hostsysteem met Windows Server 2012 R2.

## <a name="connect-through-http"></a>Verbinding maken via HTTP

Verbinding maken met Windows PowerShell voor StorSimple via een HTTP-sessie biedt meer beveiliging dan wanneer u verbinding maakt via de seriële console van uw StorSimple-apparaat. Hoewel dit niet de veiligste methode is, is het acceptabel voor vertrouwde netwerken.

U kunt de Azure Portal of de seriële console gebruiken voor het configureren van extern beheer. Selecteer een van de volgende procedures:

* Het Azure Portal gebruiken om extern beheer via HTTP in te scha kelen
* [De seriële console gebruiken om extern beheer via HTTP in te scha kelen](#use-the-serial-console-to-enable-remote-management-over-http)

Nadat u extern beheer hebt ingeschakeld, gebruikt u de volgende procedure om de-client voor te bereiden voor een externe verbinding.

* [De client voorbereiden voor externe verbinding](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Het Azure Portal gebruiken om extern beheer via HTTP in te scha kelen

Voer de volgende stappen uit in de Azure Portal om extern beheer via HTTP in te scha kelen.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Extern beheer via de Azure Portal inschakelen

1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer **apparaten** en selecteer en klik vervolgens op het apparaat dat u wilt configureren voor extern beheer. Ga naar **apparaatinstellingen > beveiliging**.
2. Klik op **extern beheer** op de Blade **beveiligings instellingen** .
3. Stel op de Blade **extern beheer** **extern beheer inschakelen** in op **Ja**.
4. U kunt nu verbinding maken via HTTP. (De standaard instelling is om verbinding te maken via HTTPS.) Zorg ervoor dat HTTP is geselecteerd.
   
   > [!NOTE]
   > Verbinding maken via HTTP is alleen acceptabel in vertrouwde netwerken.
   
5. Klik op **Opslaan** en selecteer **Ja** als u om bevestiging wordt gevraagd.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>De seriële console gebruiken om extern beheer via HTTP in te scha kelen
Voer de volgende stappen uit op de seriële console van het apparaat om extern beheer in te scha kelen.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Extern beheer via de seriële console van het apparaat inschakelen
1. Selecteer optie 1 in het menu van de seriële console. Ga voor meer informatie over het gebruik van de seriële console op het apparaat naar [verbinding maken met Windows PowerShell voor StorSimple via de seriële console](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)van het apparaat.
2. Typ het volgende bij de prompt: `Enable-HcsRemoteManagement –AllowHttp`
3. U wordt gewaarschuwd over de beveiligings problemen van het gebruik van HTTP om verbinding te maken met het apparaat. Wanneer u hierom wordt gevraagd, bevestigt u door **Y** te typen.
4. Controleer of HTTP is ingeschakeld door het volgende te typen: `Get-HcsSystem`
5. Controleer of in  het veld RemoteManagementMode **HttpsAndHttpEnabled** wordt weer gegeven. In de volgende afbeelding ziet u deze instellingen in PuTTy.
   
     ![Seriële HTTPS en HTTP ingeschakeld](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>De client voorbereiden voor externe verbinding
Voer de volgende stappen uit op de client om extern beheer in te scha kelen.

#### <a name="to-prepare-the-client-for-remote-connection"></a>De client voorbereiden voor externe verbinding
1. Start een Windows PowerShell-sessie als beheerder. Als u een Windows 10-client gebruikt, wordt de Windows Remote Management-service standaard ingesteld op hand matig. Mogelijk moet u de service starten door het volgende te typen:

    `Start-Service WinRM`
    
2. Typ de volgende opdracht om het IP-adres van het StorSimple-apparaat toe te voegen aan de lijst met vertrouwde hosts van de client:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Vervang <*device_ip*> met het IP-adres van uw apparaat. bijvoorbeeld: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Typ de volgende opdracht om de referenties van het apparaat op te slaan in een variabele: 
   
    ```
    $cred = Get-Credential
    ```
    
4. In het dialoog venster dat wordt weer gegeven:
   
   1. Typ de gebruikers naam in deze indeling: *device_ip \ssadmin*.
   2. Typ het beheerders wachtwoord van het apparaat dat is ingesteld toen het apparaat werd geconfigureerd met de installatie wizard. Het standaardapparaatwachtwoord is *Wachtwoord1*.
5. Start een Windows Power shell-sessie op het apparaat door de volgende opdracht te typen:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Als u een Windows Power shell-sessie wilt maken voor gebruik met het virtuele StorSimple-apparaat, voegt `–Port` u de para meter toe en geeft u de open bare poort op die u hebt geconfigureerd in externe toegang voor StorSimple-virtueel apparaat.
   
   
Op dit moment moet u een actieve externe Windows Power shell-sessie op het apparaat hebben.
   
![Externe communicatie met Power shell met HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Verbinding maken via HTTPS

Verbinding maken met Windows PowerShell voor StorSimple via een HTTPS-sessie is de veiligste en aanbevolen methode om op afstand verbinding te maken met uw Microsoft Azure StorSimple apparaat. In de volgende procedures wordt uitgelegd hoe u de seriële console en client computers instelt, zodat u HTTPS kunt gebruiken om verbinding te maken met Windows PowerShell voor StorSimple.

U kunt de Azure Portal of de seriële console gebruiken voor het configureren van extern beheer. Selecteer een van de volgende procedures:

* Het Azure Portal gebruiken om extern beheer via HTTPS in te scha kelen
* [De seriële console gebruiken om extern beheer via HTTPS in te scha kelen](#use-the-serial-console-to-enable-remote-management-over-https)

Nadat u extern beheer hebt ingeschakeld, moet u de volgende procedures gebruiken om de host voor te bereiden voor een extern beheer en verbinding te maken met het apparaat vanaf de externe host.

* [De host voorbereiden voor extern beheer](#prepare-the-host-for-remote-management)
* [Verbinding maken met het apparaat vanaf de externe host](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Het Azure Portal gebruiken om extern beheer via HTTPS in te scha kelen

Voer de volgende stappen uit in de Azure Portal om extern beheer via HTTPS in te scha kelen.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Extern beheer via HTTPS inschakelen via de Azure Portal

1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer **apparaten** en selecteer en klik vervolgens op het apparaat dat u wilt configureren voor extern beheer. Ga naar **apparaatinstellingen > beveiliging**.
2. Klik op **extern beheer** op de Blade **beveiligings instellingen** .
3. Stel **Extern beheer inschakelen** in op **Ja**.
4. U kunt nu verbinding maken met behulp van HTTPS. (De standaard instelling is om verbinding te maken via HTTPS.) Controleer of HTTPS is geselecteerd.
5. Klik op... en klik vervolgens op **certificaat voor extern beheer downloaden**. Geef een locatie op voor het opslaan van dit bestand. U moet dit certificaat installeren op de client of hostcomputer die u gaat gebruiken om verbinding te maken met het apparaat.
6. Klik op **Opslaan** en klik vervolgens op **Ja** als u om bevestiging wordt gevraagd.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>De seriële console gebruiken om extern beheer via HTTPS in te scha kelen

Voer de volgende stappen uit op de seriële console van het apparaat om extern beheer in te scha kelen.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Extern beheer via de seriële console van het apparaat inschakelen
1. Selecteer optie 1 in het menu van de seriële console. Ga voor meer informatie over het gebruik van de seriële console op het apparaat naar [verbinding maken met Windows PowerShell voor StorSimple via de seriële console](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)van het apparaat.
2. Typ het volgende bij de prompt:
   
     `Enable-HcsRemoteManagement`
   
    Hierdoor moet HTTPS op uw apparaat worden ingeschakeld.
3. Controleer of HTTPS is ingeschakeld door het volgende te typen: 
   
     `Get-HcsSystem`
   
    Zorg ervoor dat in het veld **RemoteManagementMode** de **HttpsEnabled** wordt weer gegeven. In de volgende afbeelding ziet u deze instellingen in PuTTy.
   
     ![Seriële HTTPS ingeschakeld](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Kopieer vanaf de uitvoer van het `Get-HcsSystem` serie nummer van het apparaat en sla het op voor later gebruik.
   
   > [!NOTE]
   > Het serie nummer wordt toegewezen aan de CN-naam in het certificaat.
   
5. Verkrijg een extern beheer certificaat door het volgende te typen: 
   
     `Get-HcsRemoteManagementCert`
   
    Er wordt een certificaat weer gegeven dat lijkt op het volgende.
   
    ![Certificaat voor extern beheer ophalen](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Kopieer de informatie in het certificaat van **-----begin certificaat-----** naar **-----eind certificaat-----** in een tekst editor zoals Klad blok en sla het op als een CER-bestand. (Als u de host voorbereidt, kopieert u dit bestand naar de externe host.)
   
   > [!NOTE]
   > Als u een nieuw certificaat wilt genereren, gebruikt u de `Set-HcsRemoteManagementCert` cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>De host voorbereiden voor extern beheer

Voer de volgende procedures uit om de hostcomputer voor te bereiden voor een externe verbinding die gebruikmaakt van een HTTPS-sessie:

* [Importeer het CER-bestand in het basis archief van de-client of de externe host](#to-import-the-certificate-on-the-remote-host).
* [Voeg de serie nummers van het apparaat toe aan het hosts-bestand op uw externe host](#to-add-device-serial-numbers-to-the-remote-host).

De bovenstaande procedures worden hieronder beschreven.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Het certificaat op de externe host importeren
1. Klik met de rechtermuisknop op het CER-bestand en selecteer **Certificaat installeren**. Hiermee start u de wizard Certificaat importeren.
   
    ![Wizard Certificaat importeren 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Selecteer bij **Archieflocatie** de optie **Lokale computer** en klik op **Volgende**.
3. Selecteer **Alle certificaten in het onderstaande archief opslaan** en klik op **Bladeren**. Navigeer naar het basisarchief van de externe host en klik vervolgens op **Volgende**.
   
    ![Wizard Certificaat importeren 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klik op **Voltooien**. Er wordt een bericht weergegeven dat het importeren is geslaagd.
   
    ![Wizard Certificaat importeren 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Serie nummers van apparaten toevoegen aan de externe host
1. Start Klad blok als beheerder en open vervolgens het hosts-bestand dat zich bevindt in \Windows\System32\Drivers\etc.
2. Voeg de volgende drie vermeldingen toe aan het hosts-bestand: **Data 0 IP-adres**, **controller 0 vast IP-adres** en **Controller 1 vast IP-adres**.
3. Voer het serie nummer van het apparaat in dat u eerder hebt opgeslagen. Wijs dit toe aan het IP-adres, zoals wordt weer gegeven in de volgende afbeelding. Voor controller 0 en controller 1 voegt u **Controller0** en **Controller1** toe aan het einde van het serie nummer (CN-naam).
   
    ![De CN-naam wordt toegevoegd aan het hosts-bestand](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Sla het bestand met hosts op.

### <a name="connect-to-the-device-from-the-remote-host"></a>Verbinding maken met het apparaat vanaf de externe host

Gebruik Windows Power shell en TLS om een SSAdmin-sessie op uw apparaat in te voeren vanaf een externe host of client. De SSAdmin-sessie wordt toegewezen aan optie 1 in het menu van de [seriële console](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) van het apparaat.

Voer de volgende procedure uit op de computer van waaruit u de externe Windows Power shell-verbinding wilt maken.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Een SSAdmin-sessie op het apparaat invoeren door gebruik te maken van Windows Power shell en TLS
1. Start een Windows PowerShell-sessie als beheerder. Als u een Windows 10-client gebruikt, wordt de Windows Remote Management-service standaard ingesteld op hand matig. Mogelijk moet u de service starten door het volgende te typen:

    `Start-Service WinRM`

2. Voeg het IP-adres van het apparaat toe aan de vertrouwde hosts van de client door het volgende te typen:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Waarbij <*device_ip*> het IP-adres van uw apparaat is; bijvoorbeeld: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Als u een nieuwe referentie wilt maken, typt u:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Waarbij <*IP van het doel apparaat*> het IP-adres van de gegevens 0 voor uw apparaat is. bijvoorbeeld **10.126.173.90** zoals weer gegeven in de voor gaande afbeelding van het hosts-bestand. Geef ook het beheerders wachtwoord voor uw apparaat op.
4. Maak een sessie door het volgende te typen:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Geef voor de para meter-ComputerName in de cmdlet het <*serie nummer van het doel apparaat* op>. Dit serie nummer is toegewezen aan het IP-adres van de gegevens 0 in het hosts-bestand op uw externe host; bijvoorbeeld **SHX0991003G44MT** zoals weer gegeven in de volgende afbeelding.
5. Type:
   
     `Enter-PSSession $session`
6. U moet een paar minuten wachten en vervolgens via HTTPS via TLS verbinding maken met uw apparaat. Er wordt een bericht weer gegeven met de melding dat u verbinding hebt met uw apparaat.
   
    ![Externe communicatie met Power shell met HTTPS en TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het gebruik van Windows Power shell voor het beheren van uw StorSimple-apparaat](storsimple-8000-windows-powershell-administration.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

