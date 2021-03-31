---
title: StorSimple Virtual array inrichten in Hyper-V | Microsoft Docs
description: Bij deze tweede zelf studie in de implementatie van een virtuele-StorSimple-matrix moet u een virtuele matrix inrichten in Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6dfc95820e911781ffa88e2207601703f165839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87070597"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>StorSimple Virtual array implementeren-inrichten in Hyper-V
![Diagram met de stappen die nodig zijn voor het implementeren van een virtuele matrix. Het eerste deel van de tweede stap is het label inrichten op Hyper-V en is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In deze zelf studie wordt beschreven hoe u een virtuele StorSimple-matrix kunt inrichten op een hostsysteem waarop Hyper-V wordt uitgevoerd op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2. Dit artikel is van toepassing op de implementatie van StorSimple virtuele arrays in Azure Portal en Microsoft Azure Government Cloud.

U hebt beheerders bevoegdheden nodig om een virtuele matrix in te richten en te configureren. De inrichting en de initiële installatie kan circa tien minuten duren.

## <a name="provisioning-prerequisites"></a>Vereisten inrichten
Hier vindt u de vereisten voor het inrichten van een virtuele matrix op een hostsysteem waarop Hyper-V wordt uitgevoerd op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie
Zorg voordat u begint voor het volgende:

* U hebt alle stappen [voor het voorbereiden van de virtuele StorSimple-matrix in de portal](storsimple-virtual-array-deploy1-portal-prep.md)voltooid.
* U hebt de virtuele-matrix installatie kopie voor Hyper-V gedownload van de Azure Portal. Zie voor meer informatie **stap 3: de installatie kopie van de virtuele matrix downloaden** van [de hand leiding voor het voorbereiden van de portal voor StorSimple Virtual array](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > De software die wordt uitgevoerd op de virtuele StorSimple-matrix mag alleen worden gebruikt met de StorSimple-Apparaatbeheer service.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Voor de virtuele StorSimple-matrix
Voordat u een virtuele matrix implementeert, moet u het volgende doen:

* U hebt toegang tot een hostsysteem waarop Hyper-V wordt uitgevoerd op Windows Server 2008 R2 of later dat kan worden gebruikt om een apparaat in te richten.
* Het hostsysteem kan de volgende resources voor het inrichten van uw virtuele matrix toewijzen:

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix te configureren als bestands server, ondersteunt 8 GB minder dan 2.000.000 bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface.
  * Een virtuele schijf van 500 GB voor gegevens.

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter
Voordat u begint, controleert u de netwerk vereisten voor het implementeren van een StorSimple-virtuele matrix en configureert u het Data Center-netwerk op de juiste manier. Zie [StorSimple Virtual array Network requirements](storsimple-ova-system-requirements.md#networking-requirements)(Engelstalig) voor meer informatie.

## <a name="step-by-step-provisioning"></a>Stap-voor-stap inrichting
Als u een virtuele matrix wilt inrichten en er verbinding mee wilt maken, moet u de volgende stappen uitvoeren:

1. Zorg ervoor dat het hostsysteem voldoende bronnen heeft om te voldoen aan de minimale vereisten voor virtuele matrix.
2. Een virtuele matrix inrichten in uw Hyper Visor.
3. Start de virtuele matrix en haal het IP-adres op.

Elk van deze stappen wordt uitgelegd in de volgende secties.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Stap 1: Zorg ervoor dat het hostsysteem voldoet aan de minimale vereisten voor de virtuele matrix
Als u een virtuele matrix wilt maken, hebt u het volgende nodig:

* De Hyper-V-functie die is geïnstalleerd op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2 SP1.
* Microsoft Hyper-V-beheer op een Microsoft Windows-client moet zijn verbonden met de host.

Zorg ervoor dat de onderliggende hardware (hostsysteem) waarop u de virtuele matrix maakt, de volgende resources aan uw virtuele array kan toewijzen:

* Minimaal 4 kerngeheugens.
* Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix te configureren als bestands server, ondersteunt 8 GB minder dan 2.000.000 bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
* Eén netwerkinterface.
* Een virtuele schijf van 500 GB voor systeem gegevens.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Stap 2: een virtuele matrix inrichten in Hyper Visor
Voer de volgende stappen uit om een apparaat in de hypervisor in te richten.

#### <a name="to-provision-a-virtual-array"></a>Een virtuele matrix inrichten
1. Kopieer de installatie kopie van de virtuele matrix op de Windows Server-host naar een lokaal station. U hebt deze installatie kopie (VHD of VHDX) gedownload via de Azure Portal. Noteer de locatie waar u de installatiekopie naartoe hebt gekopieerd, want u hebt deze installatiekopie verderop in de procedure nodig.
2. Open **Serverbeheer**. Klik in de rechterbovenhoek op **Hulpprogramma's** en selecteer **Hyper-V-beheer**.

   ![Scherm afbeelding van Serverbeheer waarin een menu met uitgebreide Hulpprogram Ma's wordt weer gegeven waarin het Hyper-V-beheer item is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Als u Windows Server 2008 R2 uitvoert, opent u Hyper-V-beheer. Klik in Serverbeheer op **rollen > hyper-v > hyper-v-beheer**.
3. In **Hyper-V-beheer** klikt u in het scopedeelvenster met de rechtermuisknop op uw systeemknooppunt om het contextmenu te openen. Klik vervolgens op **Nieuw** > **Virtuele machine**.

   ![Scherm afdruk van Hyper-V-beheer met het snelmenu van een systeem knooppunt, waarbij de nieuwe en virtuele-machine items zijn gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Klik op de pagina **Voordat u begint** van de wizard Nieuwe virtuele machine op **Volgende**.
5. Geef op de pagina **naam en locatie opgeven** een **naam** op voor de virtuele matrix. Klik op **Volgende**.

   ![Scherm afbeelding van de pagina naam en locatie opgeven, met daarin het vak naam is ingevuld en de knop Volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Kies op de pagina **generatie opgeven** het type installatie kopie van apparaat en klik vervolgens op **volgende**. Deze pagina wordt niet weer gegeven als u Windows Server 2008 R2 gebruikt.

   * Kies **generatie 2** als u een. vhdx-installatie kopie hebt gedownload voor Windows Server 2012 of hoger.
   * Kies **generatie 1** als u een VHD-installatie kopie hebt gedownload voor Windows Server 2008 R2 of hoger.

   ![Scherm afbeelding van de pagina generatie opgeven waarop de optie generatie 2 is geselecteerd en de knop Volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Op de pagina **Geheugen toewijzen** geeft u een **Opstartgeheugen** van ten minste **8192 MB** op. Schakel Dynamisch geheugen niet in en klik op **Volgende**.

   ![Scherm afbeelding van de pagina geheugen toewijzen met een waarde van 8192 in het vak opstart geheugen. De knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Op de pagina **Netwerk configureren** geeft de virtuele switch op die is verbonden met internet en klikt u op **Volgende**.

   ![Scherm afbeelding van de pagina netwerk configureren met het virtuele Hyper V-Switch-item dat is geselecteerd in het vak verbinding en de knop Volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Kies op de pagina **virtuele harde schijf aansluiten** de optie **een bestaande virtuele harde schijf gebruiken**, de locatie van de installatie kopie van de virtuele matrix (. vhdx of. VHD) opgeven en klik vervolgens op **volgende**.

   ![Scherm opname van de pagina virtuele harde schijf aansluiten met behulp van een bestaande virtuele harde schijf geselecteerd, een locatie die is ingevoerd en de knop Volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Controleer de **Samenvatting** en klik op **Voltooien** om de virtuele machine te maken.

    ![Scherm opname van de wizard pagina nieuwe virtuele machine volt ooien met een beschrijving van een virtuele machine. De knop volt ooien is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. U hebt 4 kerngeheugens nodig om te voldoen aan de minimale vereisten. Selecteer uw hostsysteem in het venster **Hyper-V-beheer** om 4 virtuele processors toe te voegen. Zoek in het rechterdeelvenster onder de lijst met **Virtuele Machines** de virtuele machine die u zojuist hebt gemaakt. Klik met de rechtermuisknop op de naam van de machine en selecteer **Instellingen**.

    ![Scherm afbeelding van het venster Hyper-V-beheer. Er is één computer geselecteerd in de lijst en het instellingen item is gemarkeerd in het snelmenu van de computer.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Klik op de pagina **Instellingen** in het linkerdeelvenster op **Processor**. Stel in het rechterdeelvenster **Aantal virtuele processors** in op 4 (of meer). Klik op **Toepassen**.

    ![Scherm afbeelding van de pagina instellingen waarvoor het processor item is gemarkeerd, het aantal virtuele processors dat is ingesteld op vier en de knop Toep assen gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Om aan de minimum vereisten te voldoen, moet u ook een virtuele schijf van 500 GB toevoegen. Op de pagina **Instellingen** doet u het volgende:

    1. Selecteer in het linkerdeelvenster de optie **SCSI-controller**.
    2. In het rechterdeelvenster selecteert u de optie **Harde schijf** en klikt u op **Toevoegen**.

    ![Scherm afbeelding van de pagina instellingen waarop het item SCSI-controller is geselecteerd, het item harde schijf geselecteerd en de knop toevoegen gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Op de pagina **Harde schijf** selecteert u de optie **Virtuele harde schijf** en klikt u op **Nieuw**. De **Wizard Nieuwe virtuele harde schijf** wordt gestart.

    ![Scherm afbeelding van de pagina instellingen met het item harde schijf geselecteerd, de optie virtuele harde schijf geselecteerd en de knop Nieuw gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Op de pagina **Voordat u begint** van de wizard Nieuwe virtuele harde schijf klikt u op **Volgende**.
16. Op de pagina **Schijfindeling kiezen** accepteert u de standaardindeling **VHDX**. Klik op **Volgende**. Dit scherm wordt niet weer gegeven als u Windows Server 2008 R2 uitvoert.

    ![Scherm afbeelding van de pagina schijf indeling kiezen met de optie VHDX geselecteerd en de knop Volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Op de pagina **Schijftype kiezen** stelt u het type virtuele harde schijf in op **Dynamisch uitbreidbaar** (aanbevolen). Een schijf van het type **Vaste grootte** werkt waarschijnlijk ook, maar dan moet u mogelijk lang wachten. U wordt geadviseerd om niet de optie **Differentiërende** te gebruiken. Klik op **Volgende**. In Windows Server 2012 R2 en Windows Server 2012 is **dynamisch uitbreiden** de standaard optie, terwijl in windows server 2008 R2 de standaard waarde is ingesteld op **vaste grootte**.

    ![Scherm afbeelding van de pagina schijf type kiezen met de optie dynamisch uitbreiden geselecteerd en de knop Volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Op de pagina **Naam en locatie opgeven** geeft u een **Naam** en een **Locatie** voor de gegevensschijf op (u kunt naar een locatie bladeren). Klik op **Volgende**.

    ![Scherm afbeelding van de pagina naam en locatie opgeven met de waarden in de vakken naam en locatie. De knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Op de pagina **schijf configureren** selecteert u de optie **een nieuwe lege virtuele harde schijf maken** en de grootte van **500 GB** (of meer) opgeven. Hoewel 500 GB de minimale vereiste is, kunt u altijd een grotere schijf inrichten. Houd er rekening mee dat u de schijf niet kunt uitbreiden of verkleinen wanneer deze eenmaal is ingericht. Raadpleeg de sectie grootte in het [document best practices](storsimple-ova-best-practices.md)voor meer informatie over de grootte van de schijf die moet worden ingericht. Klik op **Volgende**.

    ![Scherm afbeelding van de pagina schijf configureren met een nieuwe lege virtuele harde schijf maken geselecteerd, de grootte ingesteld op 500 en de knop Volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Op de pagina **Samenvatting** controleert u de details van uw virtuele gegevensschijf en als u tevreden bent, klikt u op **Voltooien** om de schijf te maken. De wizard wordt gesloten en er wordt een virtuele harde schijf aan uw machine toegevoegd.

    ![Scherm afbeelding van de wizard pagina nieuwe virtuele harde schijf volt ooien met een beschrijving van een virtuele harde schijf. De knop volt ooien is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Ga terug naar de pagina **Instellingen**. Klik op **OK** om de pagina **Instellingen** te sluiten en terug te keren naar het venster Hyper-V-beheer.

    ![Scherm afbeelding van de pagina instellingen. De knop OK is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Stap 3: de virtuele matrix starten en het IP-adres ophalen
Voer de volgende stappen uit om uw virtuele matrix te starten en er verbinding mee te maken.

#### <a name="to-start-the-virtual-array"></a>De virtuele matrix starten
1. Start de virtuele matrix.

   ![Scherm afbeelding van het venster Hyper-V-beheer. De nieuwe matrix wordt geselecteerd in de lijst en het begin item is gemarkeerd in het snelmenu van de matrix.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Wanneer het apparaat is gestart, selecteert u het apparaat, klikt u met de rechtermuisknop en selecteert u **Verbinding maken**.

   ![Scherm afbeelding van het venster Hyper-V-beheer. De nieuwe matrix wordt geselecteerd, de status wordt uitgevoerd en het Connect-item is gemarkeerd in het snelmenu.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Mogelijk moet u 5-10 minuten wachten totdat het apparaat klaar is. Er wordt een statusbericht op de console weergegeven om de voortgang aan te geven. Wanneer het apparaat gereed is, gaat u naar **Actie**. Druk op om u aan `Ctrl + Alt + Delete` te melden bij de virtuele matrix. De standaard gebruiker is *StorSimpleAdmin* en het standaard wachtwoord is *Wachtwoord1*.

   ![Scherm opname van het tabblad vSphere client console met onbepaalde tekens in het aanmeldings venster.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Uit veiligheidsoverwegingen verloopt het beheerderswachtwoord voor het apparaat na de eerste aanmelding. U wordt gevraagd om het wachtwoord te wijzigen.

   ![Scherm afbeelding van het tabblad vSphere client console. Tekst op de pagina geeft aan dat het wacht woord moet worden gewijzigd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Voer een wachtwoord in van minimaal acht tekens. Het wachtwoord moet voldoen aan ten minste 3 van de volgende 4 vereisten: hoofdletters, kleine letters, cijfers en speciale tekens. Voer het wachtwoord opnieuw in ter bevestiging. U ontvangt een melding dat het wachtwoord is gewijzigd.

   ![Scherm afbeelding van het tabblad vSphere client console. De tekst op de pagina geeft aan dat het wacht woord is gewijzigd.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Nadat het wacht woord is gewijzigd, wordt de virtuele matrix mogelijk opnieuw opgestart. Wacht tot het apparaat is gestart.

   ![Scherm afbeelding van de hoofd pagina van StorSimpleAdmin. Tekst op de pagina vraagt de gebruiker te wachten op de meldings service voor systeem gebeurtenissen.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    De Windows PowerShell-console van het apparaat wordt weergegeven, samen met een voortgangsbalk.

   ![Scherm opname waarin een console venster met een voortgangs balk wordt weer gegeven. De tekst in het venster geeft aan dat de eerste Setup actief is en vraagt de gebruiker te wachten.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. De stappen 6-8 gelden alleen als het apparaat wordt opgestart in een niet-DHCP-omgeving. In een DHCP-omgeving kunt u deze stappen overslaan en verder gaan met stap 9. Als u uw apparaat hebt opgestart in een niet-DHCP-omgeving, wordt het volgende scherm weer gegeven.

   ![Scherm opname van een console venster met tekst die het apparaat beschrijft. De opdracht prompt leest ' controller ' en wordt weer gegeven als gereed voor invoer.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Configureer vervolgens het netwerk.
7. Gebruik de `Get-HcsIpAddress` opdracht om de netwerk interfaces weer te geven die zijn ingeschakeld voor uw virtuele matrix. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

   ![Scherm opname met een console venster met de uitvoer van de opdracht Get-HcsIpAddress. ' Ethernet ' wordt vermeld als de naam van het apparaat.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Zie het volgende voorbeeld:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Scherm afbeelding met een console venster met de uitvoer van de Get-Help Set-HcsIpAddress opdracht en het juiste gebruik van de Set-HcsIpAddress opdracht.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Wanneer de initiële installatie is voltooid en het apparaat is opgestart, ziet u de bannertekst van het apparaat. Noteer het IP-adres en de URL die in de bannertekst worden weergegeven om het apparaat te beheren. Gebruik dit IP-adres om verbinding te maken met de Web-UI van uw virtuele matrix en de lokale installatie en registratie te volt ooien.

   ![Scherm opname van een console venster met de banner tekst van het apparaat. Deze tekst bevat het IP-adres en de URL van het apparaat.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. Beschrijving Voer deze stap alleen uit als u uw apparaat in de Government Cloud implementeert. U schakelt nu de FIPS-modus (Verenigde Staten Federal Information Processing Standard) op het apparaat in. De FIPS 140-standaard definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door Amerikaanse Federal Government-computer systemen voor de beveiliging van gevoelige gegevens.

    1. Als u de FIPS-modus wilt inschakelen, voert u de volgende cmdlet uit:

        `Enable-HcsFIPSMode`
    2. Start het apparaat opnieuw op nadat u de FIPS-modus hebt ingeschakeld, zodat de cryptografische validaties van kracht worden.

       > [!NOTE]
       > U kunt de FIPS-modus op het apparaat in-of uitschakelen. Het wisselen van het apparaat tussen FIPS en niet-FIPS-modus wordt niet ondersteund.
       >
       >

Als uw apparaat niet voldoet aan de minimale configuratie vereisten, ziet u de volgende fout code in de banner tekst (zie hieronder). Wijzig de apparaatconfiguratie zo dat de virtuele machine voldoende resources heeft om aan de minimale vereisten te voldoen. Daarna kunt u het apparaat opnieuw opstarten en verbinding maken met het apparaat. Raadpleeg de minimale configuratie vereisten in stap 1: Zorg ervoor dat het hostsysteem voldoet aan de minimale vereisten voor virtuele matrix.

![Scherm opname van een console venster met de banner tekst van het apparaat. Deze tekst bevat een fout bericht dat een URL biedt om het probleem op te lossen.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Als er tijdens de eerste configuratie een andere fout optreedt bij gebruik van de lokale webgebruikersinterface, raadpleegt u de volgende werkstromen:

* Diagnostische tests uitvoeren voor het [oplossen van problemen met de Web-UI-installatie](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Logboekpakket genereren en logboekbestanden weergeven](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Volgende stappen
* [Stel uw virtuele StorSimple-matrix in als een bestands server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Stel uw virtuele StorSimple-matrix in als een iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
