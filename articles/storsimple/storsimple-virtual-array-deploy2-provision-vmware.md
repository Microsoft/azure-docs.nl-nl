---
title: StorSimple Virtual array inrichten in VMware
description: In deze tweede zelf studie in StorSimple Virtual array implementation Series wordt een virtueel apparaat ingericht in VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9810a34021aa039354aad24f84aff373229c0190
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87021474"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>StorSimple Virtual array implementeren-inrichten in VMware
![Diagram met de stappen die nodig zijn voor het implementeren van een virtuele matrix. Het tweede deel van de tweede stap is voorzien van het label inrichten op VMware en is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In deze zelf studie wordt beschreven hoe u een virtuele StorSimple-matrix inricht en verbindt op een hostsysteem met VMware ESXi 5,0, 5,5, 6,0 of 6,5. Dit artikel is van toepassing op de implementatie van StorSimple virtuele arrays in Azure Portal en de Microsoft Azure Government Cloud.

U hebt beheerdersmachtigingen nodig voor het inrichten van een virtueel apparaat en om er verbinding mee te maken. De inrichting en de initiële installatie kan circa tien minuten duren.

## <a name="provisioning-prerequisites"></a>Vereisten inrichten
De vereisten voor het inrichten van een virtueel apparaat op een hostsysteem met VMware ESXi 5,0, 5,5, 6,0 of 6,5, zijn als volgt.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie
Zorg voordat u begint voor het volgende:

* U hebt alle stappen [voor het voorbereiden van de virtuele StorSimple-matrix in de portal](storsimple-virtual-array-deploy1-portal-prep.md)voltooid.
* U hebt de installatie kopie van het virtuele apparaat voor VMware gedownload van de Azure Portal. Zie voor meer informatie **stap 3: de installatie kopie van het virtuele apparaat downloaden** van [de hand leiding voor het maken van de portal voor StorSimple Virtual array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Voor het virtuele StorSimple-apparaat
Voordat u een virtueel apparaat implementeert, controleert u of:

* U hebt toegang tot een hostsysteem waarop Hyper-V (2008 R2 of hoger) wordt uitgevoerd en dat kan worden gebruikt om een apparaat in te richten.
* Het hostsysteem kan de volgende resources reserveren voor het inrichten van uw virtuele apparaat:

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix te configureren als bestands server, ondersteunt 8 GB minder dan 2.000.000 bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface.
  * Een virtuele schijf van 500 GB voor systeem gegevens.

### <a name="for-the-network-in-datacenter"></a>Voor het netwerk in het datacenter
Zorg voordat u begint voor het volgende:

* U hebt de netwerk vereisten voor het implementeren van een virtueel StorSimple-apparaat gecontroleerd en het Data Center-netwerk volgens de vereisten geconfigureerd. 

## <a name="step-by-step-provisioning"></a>Stap-voor-stap inrichting
Als u een virtueel apparaat wilt inrichten en er verbinding mee wilt maken, moet u de volgende stappen uitvoeren:

1. Zorg ervoor dat het hostsysteem voldoende bronnen heeft om te voldoen aan de minimale vereisten voor virtuele apparaten.
2. Een virtueel apparaat inrichten in uw Hyper Visor.
3. Start het virtuele apparaat en haal het IP-adres op.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Stap 1: controleren of het hostsysteem voldoet aan de minimale vereisten voor virtuele apparaten
Als u een virtueel apparaat wilt maken, hebt u het volgende nodig:

* Toegang tot een hostsysteem met VMware ESXi Server 5,0, 5,5, 6,0 of 6,5.
* Een VMware vSphere-client op uw systeem voor het beheren van de ESXi-host.

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix te configureren als bestands server, ondersteunt 8 GB minder dan 2.000.000 bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface die is verbonden met het netwerk en verkeer naar internet kan routeren. De minimale Internet bandbreedte moet 5 Mbps zijn om optimaal te kunnen werken met het apparaat.
  * Een virtuele schijf van 500 GB voor gegevens.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Stap 2: een virtueel apparaat inrichten in Hyper Visor
Voer de volgende stappen uit voor het inrichten van een virtueel apparaat in de hypervisor.

1. Kopieer de installatiekopie van het virtuele apparaat naar uw systeem. U hebt deze virtuele installatie kopie gedownload via de Azure Portal.

   1. Zorg ervoor dat u het meest recente afbeeldings bestand hebt gedownload. Als u de afbeelding eerder hebt gedownload, downloadt u deze opnieuw om te controleren of u over de meest recente installatie kopie beschikt. De nieuwste afbeelding heeft twee bestanden (in plaats van één).
   2. Noteer de locatie waar u de installatiekopie naartoe hebt gekopieerd, want u hebt deze installatiekopie verderop in de procedure nodig.

2. Meld u aan bij de ESXi-server met behulp van de vSphere-client. U moet beheerdersbevoegdheden hebben om een virtuele machine te kunnen maken.

   ![Scherm afbeelding van de aanmeldings pagina van de vSphere-client. De vakken IP-adres, gebruikers naam en wacht woord bevatten waarden en de knop Aanmelden is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Selecteer in de vSphere-client in de sectie inventaris in het linkerdeel venster de ESXi-server.

   ![Scherm afbeelding van de hoofd pagina van de vSphere-client. In de sectie inventarisatie wordt de ESXi-server gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Upload de VMDK naar de ESXi-server. Ga naar het tabblad **configuratie** in het rechterdeel venster. Onder **Hardware** selecteert u **opslag**.

   ![Scherm opname van het tabblad Configuratie van de vSphere-client. In de sectie hardware is opslag gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. In het rechterdeelvenster bij **Gegevensopslag** selecteert u de gegevensopslag waarin u de VMDK wilt uploaden. Het gegevens archief moet voldoende beschik bare ruimte hebben voor het besturings systeem en de gegevens schijven.

   ![Scherm opname met de opslag pagina van de vSphere-client. Het tabblad gegevens opslag is geopend en bevat een lijst met gegevens opslag. Er is één Data Store geselecteerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klik met de rechtermuisknop op **Browsen in de gegevensopslag** en selecteer deze.

   ![Scherm afbeelding van het geselecteerde snelmenu van de gegevens opslag. Het item zoeken in Data Store is geselecteerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Een venster met de **gegevensopslagbrowser** wordt weergegeven.

   ![Scherm opname van een Data Store-browser. De mappen in de gegevens opslag zijn zichtbaar.](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Klik in de werk balk op :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png"::: pictogram om een nieuwe map te maken. Geef de mapnaam op en noteer deze. U gebruikt deze mapnaam later bij het maken van een virtuele machine (aanbevolen best practices). Klik op **OK**.

   ![Scherm afbeelding van een browser data store met het pictogram nieuwe map gemarkeerd. In een dialoog venster is de naam van de map ingevuld en de knop OK gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. De nieuwe map wordt weergegeven in het linkerdeelvenster van de **gegevensopslagbrowser**.

   ![Scherm opname van een browser data store met de nieuwe map die wordt weer gegeven in de mappen hiërarchie.](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klik op het pictogram uploaden :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png"::: en selecteer **bestand uploaden**.

    ![Scherm afbeelding van het snelmenu van het pictogram uploaden. Het item upload file is geselecteerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Blader en wijs naar de VMDK-bestanden die u hebt gedownload. Er zijn twee bestanden. Selecteer een bestand dat u wilt uploaden.

    ![Scherm opname van een dialoog venster met mappen en twee V M D K-bestanden. Een van de bestanden is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klik op **Openen**. Het uploaden van het VMDK-bestand met de opgegeven gegevensopslag wordt gestart. Het duurt enkele minuten om het bestand te uploaden.
13. Nadat het uploaden is voltooid, ziet u het bestand in de gegevensopslag in de map die u hebt gemaakt.

    ![Scherm opname van een Data Store-browser. De nieuwe map is gemarkeerd in de mappen hiërarchie en het geüploade bestand is zichtbaar in die map.](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Upload nu het tweede VMDK-bestand naar dezelfde gegevensopslag.
14. Ga terug naar het venster van de vSphere-client. Als ESXi-server is geselecteerd, klikt u met de rechter muisknop en selecteert u **nieuwe virtuele machine**.

    ![Scherm afbeelding van het snelmenu van een ESXi-server. Het nieuwe item van de virtuele machine is geselecteerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Er wordt een nieuw venster voor de **virtuele machine maken** weer gegeven. Selecteer op de pagina **configuratie** de optie **aangepast** . Klik op **Volgende**.
    ![Scherm afbeelding van de configuratie pagina van het venster nieuwe virtuele machine maken. De optie aangepast is geselecteerd en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Geef op de pagina **naam en locatie** de naam van uw virtuele machine op. Deze naam moet overeenkomen met de mapnaam (aanbevolen best practice) die u eerder hebt opgegeven in stap 8.

    ![Scherm afbeelding van de pagina naam en locatie van het venster nieuwe virtuele machine maken. Het vak naam is ingevuld en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Selecteer op de pagina **opslag** een gegevens opslag die u wilt gebruiken om uw vm in te richten.

    ![Scherm afbeelding van de pagina opslag van het venster nieuwe virtuele machine maken. Er wordt een gegevens opslag geselecteerd en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Selecteer op de pagina **versie van virtuele machine** de versie van de **virtuele machine: 8**.

    ![Scherm afbeelding van de versie pagina van de virtuele machine. De optie versie 8 van de virtuele machine is geselecteerd en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Op de pagina **gast besturingssysteem** selecteert u het **gast besturingssysteem** als **Windows**. Selecteer voor **versie** in de vervolg keuzelijst de optie **micro soft Windows Server 2012 (64-bits)**.

    ![Scherm afbeelding van de pagina gast besturingssysteem met Windows geselecteerd, de versie die is ingesteld op micro soft Windows Server 2012 (64-bits) en volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Pas op de pagina **cpu's** het **aantal virtuele sockets** en het **aantal kernen per virtuele socket** aan zodat het **totale aantal kernen** 4 (of meer) is. Klik op **Volgende**.

    ![Scherm afbeelding van de Cpu's-pagina met één virtuele socket, vier kernen per virtuele socket en vier totale kernen. De knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Geef op de pagina **geheugen** 8 GB (of meer) RAM-geheugen op. Klik op **Volgende**.

    ![Scherm afbeelding van de pagina geheugen. Een waarde van 8 GB wordt voor de geheugen grootte ingevuld.](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Geef op de pagina **netwerk** het aantal netwerk interfaces op. De minimale vereiste is één netwerk interface.

    ![Scherm afbeelding van de pagina netwerk. Het aantal netwerk interfaces is ingesteld op één en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Accepteer op de pagina **SCSI-controller** de standaard **LSI Logic SAS-controller**.

    ![Scherm afbeelding van de pagina SCSI-controller. De optie L S I Logic S A S is geselecteerd en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Kies op de pagina **een schijf selecteren** de optie **een bestaande virtuele schijf gebruiken**. Klik op **Volgende**.

    ![Scherm afbeelding van de pagina een schijf selecteren met de optie een bestaande virtuele schijf gebruiken geselecteerd en de knop Volgende gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Klik op de pagina **bestaande schijf selecteren** onder **schijf bestands pad** op **Bladeren**. Hiermee opent u het dialoog venster **Bladeren in data stores** . Navigeer naar de locatie waar u de VMDK hebt geüpload. U ziet nu slechts één bestand in de gegevens opslag als de twee bestanden die u in eerste instantie hebt geüpload, zijn samengevoegd. Selecteer het bestand en klik op **OK**. Klik op **Volgende**.

    ![Scherm afbeelding van de pagina bestaande schijf selecteren. De knop Bladeren is gemarkeerd en een dialoog venster bevat één bestand en een gemarkeerde knop OK.](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Accepteer de standaard instellingen op de pagina **Geavanceerde opties** en klik op **volgende**.

    ![Scherm afbeelding van de pagina Geavanceerde opties. De knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Op de pagina **Gereed om te voltooien** controleert u alle instellingen voor de nieuwe virtuele machine. Controleer **de instellingen voor de virtuele machine voordat u de bewerking hebt voltooid**. Klik op **Continue**.

    ![Scherm afbeelding van de pagina gereed voor volt ooien met een gemarkeerde knop door gaan. De optie de instellingen voor de virtuele machine bewerken voordat deze is voltooid is ingeschakeld.](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Zoek op de pagina **virtual machines eigenschappen** op het tabblad **Hardware** de hardware van het apparaat. Selecteer **nieuwe harde schijf**. Klik op **Add**.

    ![Scherm afbeelding van het tabblad Hardware van de pagina met Virtual Machines eigenschappen. Er wordt een nieuwe harde schijf geselecteerd in de hardwarelijst. De knop toevoegen is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. U ziet het venster **Hardware toevoegen** . Selecteer op de pagina **type apparaat** onder **Kies het type apparaat dat u wilt toevoegen de** optie **harde schijf** en klik op **volgende**.

    ![Scherm afbeelding van de pagina apparaattype van het venster hardware toevoegen. De harde schijf is geselecteerd en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Kies op de pagina **een schijf selecteren** de optie **een nieuwe virtuele schijf maken**. Klik op **Volgende**.

    ![Scherm afbeelding van de pagina een schijf selecteren. De optie een nieuwe virtuele schijf maken is geselecteerd en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Op de pagina **een schijf maken** wijzigt u de **schijf grootte** in 500 GB (of meer). Hoewel 500 GB de minimale vereiste is, kunt u altijd een grotere schijf inrichten. Houd er rekening mee dat u de schijf niet kunt uitbreiden of verkleinen wanneer deze eenmaal is ingericht. Raadpleeg de sectie grootte in het [document best practices](storsimple-ova-best-practices.md)voor meer informatie over de grootte van de schijf die moet worden ingericht. Onder **schijf inrichting** selecteert u **Thin Provisioning**. Klik op **Volgende**.

    ![Scherm afbeelding van de pagina een schijf maken. De schijf grootte is ingesteld op 500 GB, de optie voor thin provisioning is geselecteerd en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Accepteer de standaard instellingen op de pagina **Geavanceerde opties** .

    ![Scherm afbeelding van de pagina Geavanceerde opties. Het knoop punt van het virtuele apparaat is ingesteld op SCSI (0:0) en de knop volgende is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Controleer op de pagina **gereed om te volt ooien** de schijf opties. Klik op **Voltooien**.

    ![Scherm afbeelding van de pagina gereed om te volt ooien. Er wordt een overzicht van de schijf opties weer gegeven en de knop volt ooien is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Ga terug naar de pagina eigenschappen van de virtuele machine. Er wordt een nieuwe harde schijf toegevoegd aan uw virtuele machine. Klik op **Voltooien**.

    ![Scherm afbeelding van de eigenschappen pagina van de virtuele machine. De lijst met hardware bevat de nieuwe harde schijf en de knop volt ooien is gemarkeerd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Als uw virtuele machine in het rechterdeel venster is geselecteerd, gaat u naar het tabblad **samen vatting** . Controleer de instellingen voor de virtuele machine.

    ![Scherm afbeelding van het tabblad samen vatting van de vSphere-client. De nieuwe virtuele machine wordt gemarkeerd en de bijbehorende resources en algemene eigenschappen zijn zichtbaar.](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Uw virtuele machine is nu ingericht. De volgende stap is om deze machine te laten werken en om het IP-adres op te halen.

> [!NOTE]
> U wordt aangeraden VMware-hulpprogram ma's niet te installeren op uw virtuele array (zoals hierboven ingericht). Als u VMware-hulpprogramma's installeert, leidt dat tot een niet-ondersteunde configuratie.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Stap 3: het virtuele apparaat starten en het IP-adres ophalen
Voer de volgende stappen uit om uw virtuele apparaat te starten en verbinding te maken met het apparaat.

#### <a name="to-start-the-virtual-device"></a>Het virtuele apparaat starten
1. Start het virtuele apparaat. Selecteer uw apparaat in de vSphere Configuration Manager in het linkerdeel venster en klik met de rechter muisknop om het context menu weer te geven. Selecteer **Aanzetten** en selecteer vervolgens **Inschakelen**. Nu zou uw virtuele machine moeten zijn ingeschakeld. U kunt de status bekijken in het deel venster met de onderste **recente taken** van de vSphere-client.

   ![Scherm opname van het snelmenu van het apparaat. Het energie-item is geselecteerd. Er is een aaneengesloten menu zichtbaar, waarbij het selectie item is ingeschakeld.](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Het duurt enkele minuten voordat de installatie taken zijn voltooid. Wanneer het apparaat wordt uitgevoerd, gaat u naar het tabblad **console** . Ctrl + Alt + Delete verzenden om u aan te melden bij het apparaat. U kunt ook de cursor naar het console venster wijzen en op CTRL + ALT + INSERT drukken. De standaard gebruiker is *StorSimpleAdmin* en het standaard wachtwoord is *Wachtwoord1*.

   ![Scherm afbeelding van het tabblad vSphere client console. Het vak wacht woord is leeg.](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Uit veiligheidsoverwegingen verloopt het beheerderswachtwoord voor het apparaat na de eerste aanmelding. U wordt gevraagd om het wachtwoord te wijzigen.

   ![Scherm afbeelding van het tabblad vSphere client console. Tekst op de pagina geeft aan dat het wacht woord moet worden gewijzigd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Voer een wachtwoord in van minimaal acht tekens. Het wacht woord moet drie van de vier van de volgende vereisten bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. Voer het wachtwoord opnieuw in ter bevestiging. Er wordt een melding weer gegeven dat het wacht woord is gewijzigd.

   ![Scherm afbeelding van het tabblad vSphere client console. De tekst op de pagina geeft aan dat het wacht woord is gewijzigd.](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Nadat het wacht woord is gewijzigd, wordt het virtuele apparaat mogelijk opnieuw opgestart. Wacht tot de computer opnieuw is opgestart. De Windows Power shell-console van het apparaat kan samen met een voortgangs balk worden weer gegeven.

   ![Scherm opname waarin een console venster met een voortgangs balk wordt weer gegeven. De tekst in het venster geeft aan dat de eerste Setup actief is en vraagt de gebruiker te wachten.](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. De stappen 6-8 gelden alleen als het apparaat wordt opgestart in een niet-DHCP-omgeving. In een DHCP-omgeving kunt u deze stappen overslaan en verder gaan met stap 9. Als u uw apparaat hebt opgestart in een niet-DHCP-omgeving, wordt het volgende scherm weer gegeven.

   ![Scherm opname van een console venster met tekst die het apparaat beschrijft. De opdracht prompt leest ' controller ' en wordt weer gegeven als gereed voor invoer.](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Configureer vervolgens het netwerk.
7. Gebruik de `Get-HcsIpAddress` opdracht om de netwerk interfaces weer te geven die zijn ingeschakeld op het virtuele apparaat. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

   ![Scherm opname met een console venster met de uitvoer van de opdracht Get-HcsIpAddress. ' Ethernet ' wordt vermeld als de naam van het apparaat.](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Hieronder kunt u een voorbeeld bekijken:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Scherm afbeelding met een console venster met de uitvoer van de Get-Help Set-HcsIpAddress opdracht en het juiste gebruik van de Set-HcsIpAddress opdracht.](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Wanneer de initiële installatie is voltooid en het apparaat is opgestart, ziet u de bannertekst van het apparaat. Noteer het IP-adres en de URL die in de bannertekst worden weergegeven om het apparaat te beheren. U gebruikt dit IP-adres om verbinding te maken met de Web-UI van uw virtuele apparaat en de lokale installatie en registratie te volt ooien.

   ![Scherm opname van een console venster met de banner tekst van het apparaat. Deze tekst bevat het IP-adres en de URL van het apparaat.](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Beschrijving Voer deze stap alleen uit als u uw apparaat in de Government Cloud implementeert. U schakelt nu de FIPS-modus (Verenigde Staten Federal Information Processing Standard) op het apparaat in. De FIPS 140-standaard definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door Amerikaanse Federal Government-computer systemen voor de beveiliging van gevoelige gegevens.

    1. Als u de FIPS-modus wilt inschakelen, voert u de volgende cmdlet uit:

        `Enable-HcsFIPSMode`
    2. Start het apparaat opnieuw op nadat u de FIPS-modus hebt ingeschakeld, zodat de cryptografische validaties van kracht worden.

       > [!NOTE]
       > U kunt de FIPS-modus op het apparaat in-of uitschakelen. Het wisselen van het apparaat tussen FIPS en niet-FIPS-modus wordt niet ondersteund.
       >
       >

Als uw apparaat niet voldoet aan de minimale configuratievereisten, wordt er een fout weergegeven in de bannertekst (zie hieronder). U moet de apparaatconfiguratie wijzigen zodat er voldoende resources zijn om aan de minimale vereisten te voldoen. Daarna kunt u het apparaat opnieuw opstarten en verbinding maken met het apparaat. Raadpleeg de minimale configuratievereisten in [Stap 1: Controleren of het hostsysteem voldoet aan minimale vereisten voor virtuele apparaten](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![Scherm opname van een console venster met de banner tekst van het apparaat. Deze tekst bevat een fout bericht dat een URL biedt om het probleem op te lossen.](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Als er tijdens de eerste configuratie een andere fout optreedt bij gebruik van de lokale webgebruikersinterface, raadpleegt u de volgende werkstromen:

* Diagnostische tests uitvoeren voor het [oplossen van problemen met de Web-UI-installatie](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Logboekpakket genereren en logboekbestanden weergeven](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Volgende stappen
* [Stel uw virtuele StorSimple-matrix in als een bestands server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Stel uw virtuele StorSimple-matrix in als een iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
