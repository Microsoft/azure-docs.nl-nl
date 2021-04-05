---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 338332eb90fda9afe7655f9f2ad19ed726419b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95555056"
---
### <a name="to-configure-and-register-the-device"></a>Het apparaat configureren en registreren
1. Open de Windows PowerShell-interface op de seriële console van het StorSimple-apparaat. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) voor instructies. **Volg de procedure exact, omdat u anders geen toegang hebt tot de console.**
2. Druk in de geopende sessie eenmaal op **Enter** om een opdrachtprompt weer te geven.
3. U wordt gevraagd de taal te kiezen die u voor uw apparaat wilt instellen. Geef de taal op en druk op **Enter**.
   
    ![Apparaat 1 configureren en registreren met StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. Kies in het weergegeven menu van de seriële console optie 1 om **u aan te melden met volledige toegang**.
   
    ![Apparaat 2 registreren met StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Voer de volgende stappen uit om de minimale vereiste netwerk instellingen voor uw apparaat te configureren.
   
   > [!IMPORTANT]
   > Deze configuratiestappen moeten worden uitgevoerd op de actieve controller van het apparaat. In het menu van de seriële console wordt de controllerstatus in het bannerbericht aangegeven. Als u geen verbinding met de actieve controller hebt, verbreekt u de verbinding met de actieve controller en maakt u deze opnieuw.
   
   1. Typ uw wachtwoord bij de opdrachtprompt. Het standaardapparaatwachtwoord is **Password1**.
   2. Typ de volgende opdracht:
      
        `Invoke-HcsSetupWizard`
   3. Er wordt een instellingenwizard weergegeven om u te helpen bij het configureren van de netwerkinstellingen voor het apparaat. Geef de volgende informatie op:
      
      * IP-adres voor DATA 0-netwerk interface
      * Subnetmasker
      * Gateway
      * IP-adres voor de primaire DNS-server
      * IP-adres voor de primaire NTP-server
      
      > [!NOTE]
      > Mogelijk moet u enkele minuten wachten voordat het subnetmasker en de DNS-instellingen worden toegepast.
    
   4. Configureer eventueel uw webproxyserver.
      
      > [!IMPORTANT]
      > De configuratie van uw webproxy is weliswaar optioneel, maar houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd. Zie [Configure web proxy for your device](../articles/storsimple/storsimple-8000-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie.
     
6. Druk op CTRL + C om de wizard Setup af te sluiten.
8. Voer de volgende cmdlet uit om het apparaat te laten verwijzen naar de Microsoft Azure Government-Portal (omdat het standaard verwijst naar de klassieke open bare Azure-Portal). Hiermee worden beide controllers opnieuw opgestart. We raden u aan twee PuTTy-sessies te gebruiken om gelijktijdig verbinding te maken met beide controllers, zodat u kunt zien wanneer elke controller opnieuw wordt opgestart.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Er wordt een bevestigings bericht weer gegeven. Accepteer de standaard waarde (**Y**).
9. Voer de volgende cmdlet uit om de installatie te hervatten:
   
    `Invoke-HcsSetupWizard`
   
    ![Wizard Setup hervatten](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. De netwerk instellingen accepteren. U ziet een validatie bericht nadat u elke instelling hebt geaccepteerd.
11. Uit veiligheidsoverwegingen is het beheerderswachtwoord van het apparaat na de eerste sessie verlopen en moet u het nu wijzigen. Als daarom wordt gevraagd, geeft u een beheerderswachtwoord voor het apparaat op. Een geldig beheerderswachtwoord voor een apparaat moet 8 tot 15 tekens bevatten. Het wachtwoord moet drie van de volgende elementen bevatten: kleine letters, hoofdletters, cijfers en speciale tekens.
    
    <br/>![Apparaat 5 registreren met StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. Bij de laatste stap in de instellingenwizard wordt uw apparaat geregistreerd met de StorSimple-apparaatbeheerfunctie. Hiervoor hebt u de service registratie sleutel nodig die u hebt verkregen in [stap 2: de service registratie sleutel ophalen](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Nadat u de registratiesleutel hebt opgegeven, moet u mogelijk twee tot drie minuten wachten voordat het apparaat is geregistreerd.
    
    > [!NOTE]
    > U kunt op elk gewenst moment op Ctrl+C drukken om de instellingenwizard af te sluiten. Als u alle netwerkinstellingen (IP-adres voor Data 0, subnetmasker en gateway) hebt ingevoerd, blijven uw gegevens behouden.
    
    ![Voortgang van registratie van StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Nadat het apparaat is geregistreerd, wordt er een gegevensversleutelingssleutel van de service weergegeven. Kopieer deze sleutel en bewaar deze op een veilige plaats. **Deze sleutel is vereist bij de service registratie sleutel om extra apparaten te registreren bij de StorSimple-Apparaatbeheer service.** Zie [StorSimple security](../articles/storsimple/storsimple-8000-security.md) (StorSimple-beveiliging) voor meer informatie over deze sleutel.
    
    ![Apparaat 7 registreren met StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Kopieer de tekst uit het venster van de seriële console gewoon door de tekst te selecteren. U kunt de tekst vervolgens op het klembord of in een teksteditor plakken.
    > 
    > Gebruik niet **CTRL + C** om de versleutelings sleutel voor de service gegevens te kopiëren. Als u **CTRL + C** gebruikt, wordt de wizard Setup afgesloten. Als gevolg hiervan wordt het beheerderswachtwoord van het apparaat niet gewijzigd en wordt het standaardwachtwoord opnieuw ingesteld.
    
14. Sluit de seriële console af.
15. Ga terug naar de Azure Government Portal en voer de volgende stappen uit:
    
    1. Ga naar uw StorSimple-apparaatbeheerservice.
    2. Klik op **Apparaten**. Zoek in de lijst met apparaten het apparaat dat u implementeert. Controleer of het apparaat is verbonden met de service door de status op te zoeken. Het apparaat moet de status **Online** hebben.
            
        Als de status van het apparaat **Offline** is, moet u een paar minuten wachten totdat het apparaat online is.
       
        Als het apparaat na een paar minuten nog steeds offline is, moet u ervoor zorgen dat uw firewallnetwerk is geconfigureerd zoals wordt beschreven in [Netwerkvereisten voor uw StorSimple-apparaat](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Controleer of poort 9354 open is voor uitgaande communicatie, omdat de servicebus deze poort gebruikt voor de communicatie van de StorSimple-apparaatbeheerfunctie naar het apparaat.