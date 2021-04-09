---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ee92e145acd981bb8c84b43986b90306b994c262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560070"
---
#### <a name="to-configure-and-register-the-device"></a>Het apparaat configureren en registreren

1. Open de Windows PowerShell-interface op de seriële console van het StorSimple-apparaat. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) voor instructies. **Volg de procedure exact, omdat u anders geen toegang hebt tot de console.**

2. Druk in de geopende sessie eenmaal op **Enter** om een opdrachtprompt weer te geven.

3. U wordt gevraagd de taal te kiezen die u voor uw apparaat wilt instellen. Geef de taal op en druk op **Enter**.

4. Kies in het weergegeven menu van de seriële console optie 1 om **u aan te melden met volledige toegang**.
     Voer de stappen 5 tot en met 12 uit om de minimale vereiste netwerkinstellingen voor uw apparaat te configureren. **Deze configuratiestappen moeten worden uitgevoerd op de actieve controller van het apparaat.** In het menu van de seriële console wordt de controllerstatus in het bannerbericht aangegeven. Als u niet met de actieve controller verbonden bent, verbreekt u de verbinding en maakt u vervolgens verbinding met de actieve controller.

5. Typ uw wachtwoord bij de opdrachtprompt. Het standaardapparaatwachtwoord is **Password1**.

6. Typ de volgende opdracht: `Invoke-HcsSetupWizard`.

7. Er wordt een instellingenwizard weergegeven om u te helpen bij het configureren van de netwerkinstellingen voor het apparaat. Geef de volgende informatie op:
   
   * IP-adres voor de DATA 0-netwerkinterface
   * Subnetmasker
   * Gateway
   * IP-adres voor de primaire DNS-server

   Een voorbeeld van de uitvoer wordt hieronder weergegeven.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    In het voorgaande voorbeeld van de uitvoer kunt u zien dat de netwerkinstellingen na elke stap in het proces worden gevalideerd.

     > [!NOTE]
     > U moet misschien een paar minuten wachten tot het subnetmasker en de DNS-instellingen zijn toegepast. Als het foutbericht Controleer de netwerkverbinding met Data 0 wordt weergegeven, controleert u de fysieke netwerkverbinding voor de DATA 0-netwerkinterface van uw actieve controller.

8. Configureer desgewenst uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar **houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd**. Zie [Configure web proxy for your device](../articles/storsimple/storsimple-8000-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie.
9. Configureer een primaire NTP-server voor uw apparaat. NTP-servers zijn vereist, omdat uw apparaat de tijd moet synchroniseren voor verificatie met uw cloudserviceproviders. Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet. Als dit niet mogelijk is, geeft u een interne NTP-server op.

    Hieronder ziet u een voorbeeld van de uitvoer.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Uit veiligheidsoverwegingen is het beheerderswachtwoord van het apparaat na de eerste sessie verlopen en moet u het nu wijzigen. Als daarom wordt gevraagd, geeft u een beheerderswachtwoord voor het apparaat op. Een geldig beheerderswachtwoord voor een apparaat moet 8 tot 15 tekens bevatten. Het wachtwoord moet drie van de volgende elementen bevatten: kleine letters, hoofdletters, cijfers en speciale tekens.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. Bij de laatste stap in de instellingenwizard wordt uw apparaat geregistreerd met de StorSimple-apparaatbeheerfunctie. Hiervoor hebt u de serviceregistratiesleutel nodig die u in stap 2 hebt verkregen. Nadat u de registratiesleutel hebt opgegeven, moet u mogelijk twee tot drie minuten wachten voordat het apparaat is geregistreerd.
    
    > [!NOTE]
    > U kunt op elk gewenst moment op Ctrl+C drukken om de instellingenwizard af te sluiten. Als u alle netwerkinstellingen (IP-adres voor Data 0, subnetmasker en gateway) hebt ingevoerd, blijven uw gegevens behouden.
    
    Hieronder ziet u een voorbeeld van de uitvoer.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. Nadat het apparaat is geregistreerd, wordt er een gegevensversleutelingssleutel van de service weergegeven. Kopieer deze sleutel en bewaar deze op een veilige plaats. **Deze sleutel is vereist bij de serviceregistratiesleutel om extra apparaten te registreren met de StorSimple Manager-apparaatbeheerfunctie.** Zie [StorSimple security](../articles/storsimple/storsimple-8000-security.md) (StorSimple-beveiliging) voor meer informatie over deze sleutel.
    
    ![Apparaat 7 registreren met StorSimple](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Kopieer de tekst uit het venster van de seriële console gewoon door de tekst te selecteren. U kunt de tekst vervolgens op het klembord of in een teksteditor plakken. Kopieer de gegevensversleutelingssleutel van de service NIET met Ctrl+C. Als u op Ctrl+C drukt, wordt de instellingenwizard afgesloten. Als gevolg hiervan wordt het beheerderswachtwoord van het apparaat niet gewijzigd en wordt het standaardwachtwoord opnieuw ingesteld.
    
13. Sluit de seriële console af.
14. Ga terug naar de Azure Portal en voer de volgende stappen uit:
    
    1. Ga naar uw StorSimple-apparaatbeheerservice.
    2. Klik op **Apparaten**.
    3. Controleer in de lijst in tabelvorm met apparaten aan de hand van de status of het apparaat verbinding heeft met de service. Het apparaat moet de status **Gereed voor configuratie** hebben.
       
        ![Pagina StorSimple-apparaten](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        U moet mogelijk enkele minuten wachten totdat de apparaatstatus is gewijzigd in **Gereed voor configuratie**.
       
        Als het apparaat niet in deze lijst wordt weergegeven, moet u ervoor zorgen dat uw firewallnetwerk is geconfigureerd zoals wordt beschreven in [Netwerkvereisten voor uw StorSimple-apparaat](../articles/storsimple/storsimple-8000-system-requirements.md). Controleer of poort 9354 open is voor uitgaande communicatie, omdat de servicebus deze poort gebruikt voor de communicatie van de StorSimple-apparaatbeheerfunctie naar het apparaat.