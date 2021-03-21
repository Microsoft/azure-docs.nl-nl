---
title: Zelfstudie over het inrichten van Azure Data Box Gateway in VMware | Microsoft Docs
description: In de tweede zelfstudie moet voor het implementeren van Azure Data Box Gateway een virtueel apparaat in VMware worden ingericht.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 948005942be193c8b15c363282aa06c006eb244e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727614"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware"></a>Zelfstudie: Azure Data Box Gateway inrichten in VMware

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt beschreven hoe u een Data Box Gateway op een hostsysteem met VMware ESXi 6.0, 6.5 of 6.7 kunt inrichten. 

U hebt beheerdersmachtigingen nodig voor het inrichten van een virtueel apparaat en om er verbinding mee te maken. De inrichting en de initiële installatie kan circa tien minuten duren. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleren of de host voldoet aan de minimale apparaatvereisten
> * Een virtueel apparaat inrichten in VMware
> * Het virtuele apparaat starten en het IP-adres ophalen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

De vereisten voor het inrichten van een virtueel apparaat op een hostsysteem met VMware ESXi 6.0, 6.5 of 6.7 zijn als volgt.

### <a name="for-the-data-box-gateway-resource"></a>Voor de Data Box Gateway-resource

Zorg voordat u begint voor het volgende:

* U hebt alle stappen in [De portal voorbereiden voor Data Box Gateway](data-box-gateway-deploy-prep.md) uitgevoerd.
* U hebt de installatiekopie van het virtuele apparaat gedownload voor VMware vanuit de Azure-portal zoals wordt beschreven in [De portal voorbereiden voor Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > De software die wordt uitgevoerd op de Data Box Gateway kan alleen worden gebruikt met de Data Box Gateway-resource.

### <a name="for-the-data-box-gateway-virtual-device"></a>Voor het virtuele Data Box Gateway-apparaat

Voordat u een virtueel apparaat implementeert, controleert u of:

* U toegang hebt tot een hostsysteem met VMware (ESXi 6.0, 6.5 of 6.7) dat kan worden gebruikt voor het inrichten van een apparaat.
* Het hostsysteem kan de volgende resources reserveren voor het inrichten van uw virtuele apparaat:

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. We raden u ten zeerste aan om ten minste 16 GB RAM-geheugen.
  * Eén netwerkinterface.
  * Een besturingssysteemschijf van 250 GB.
  * Een virtuele schijf van 2 TB voor systeemgegevens

### <a name="for-the-network-in-datacenter"></a>Voor het netwerk in het datacenter

Voordat u begint:

- Controleer de netwerkvereisten voor het implementeren van een Data Box Gateway en configureer het netwerk van het datacenter aan de hand van die vereisten. Zie [Netwerkvereisten voor Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements) voor meer informatie.
- Voor een optimale werking van het apparaat heeft internet een minimale bandbreedte van 20 Mbps nodig.

## <a name="check-the-host-system"></a>Het hostsysteem controleren

Voor het maken van een virtueel apparaat hebt u het volgende nodig:

* Toegang tot een hostsysteem met VMware ESXi Server 6.0, 6.5 of 6.7. Het hostsysteem kan de volgende resources volledig toewijzen aan uw virtuele apparaat:
 
  * Minimaal 4 virtuele processors.
  * Ten minste 8 GB RAM-geheugen. 
  * Eén netwerkinterface die is verbonden met het netwerk en verkeer naar internet kan routeren.
  * Een besturingssysteemschijf van 250 GB
  * Een virtuele schijf van 2 TB voor gegevens
* Een VMware vSphere-client op uw systeem voor het beheren van de ESXi-host.


## <a name="provision-a-virtual-device-in-hypervisor"></a>Een virtueel apparaat in hypervisor inrichten

Voer de volgende stappen uit voor het inrichten van een virtueel apparaat in de hypervisor.

1. Kopieer de installatiekopie van het virtuele apparaat naar uw systeem. U hebt deze virtuele-installatiekopie (twee bestanden) via de Azure-portal gedownload. Noteer de locatie waar u de installatiekopie naartoe hebt gekopieerd, want u hebt deze installatiekopie verderop in de procedure nodig.

2. Meld u aan bij de ESXi-server via een browser op de volgende URL: `https://<IP address of the ESXi server>`. U moet beheerdersbevoegdheden hebben om een virtuele machine te kunnen maken.

   ![Aanmeldingspagina](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. Upload de VMDK naar de ESXi-server. Selecteer in het navigatievenster **Opslag**.

   ![Schermopname van een pagina op de ESXi-serversite waarop het navigatievenster met de geselecteerde opslagoptie wordt weergegeven.](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. In het rechterdeelvenster bij **Gegevensopslag** selecteert u de gegevensopslag waarin u de VMDK wilt uploaden. 

    - De gegevensopslag moet van het type VMFS5 zijn. 
    - De gegevensopslag moet ook voldoende vrije ruimte hebben voor het besturingssysteem en gegevensschijven.
   
5. Klik met de rechtermuisknop op **Browsen in de gegevensopslag** en selecteer deze.

   ![Door gegevensarchief bladeren](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. Een venster met de **gegevensopslagbrowser** wordt weergegeven.

   ![Gegevensopslagbrowser](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. Klik in de werkbalk op het pictogram **Map maken** om een nieuwe map te maken. Geef de mapnaam op en noteer deze. U gebruikt deze mapnaam later bij het maken van een virtuele machine (aanbevolen best practices). Klik op **Map maken**.

   ![Map maken](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. De nieuwe map wordt weergegeven in het linkerdeelvenster van de **gegevensopslagbrowser**. Klik op het pictogram **Uploaden** en selecteer **Bestand uploaden**.

    ![Bestand uploaden](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. Blader en wijs naar de VMDK-bestanden die u hebt gedownload. Er zijn twee bestanden. Selecteer een bestand dat u wilt uploaden.

    ![Selecteer bestand dat u wilt uploaden](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. Klik op **Openen**. Het uploaden van het VMDK-bestand met de opgegeven gegevensopslag wordt gestart. Het duurt enkele minuten om het bestand te uploaden.
11. Nadat het uploaden is voltooid, ziet u het bestand in de gegevensopslag in de map die u hebt gemaakt. Upload nu het tweede VMDK-bestand naar dezelfde gegevensopslag. Als beide bestanden zijn geüpload, worden de twee bestanden samengevoegd tot één bestand. U ziet nu één bestand in de map.

    ![Twee VMDK-bestanden worden samengevoegd in één bestand](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. Ga terug naar het venster van de vSphere-client. Selecteer in het navigatievenster **Virtuele machines**. Klik in het rechter deelvenster op **Virtuele machine maken/registreren**.

    ![VM maken of registreren](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. Er wordt een **nieuwe virtuele machine** weergegeven. Selecteer bij Aanmaaktype selecteren, **Een nieuwe virtuele machine maken** en klik op **Volgende**.
    ![Pagina maaktype selecteren](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. Op de pagina **Een naam en de naam van het besturingssysteem en locatie selecteren** geeft u de **naam** van uw virtuele machine op. Deze naam moet overeenkomen met de mapnaam (aanbevolen best practice) die u eerder in stap 7 hebt opgegeven. Kies als **gastbesturingssysteemserie** Windows en als **gastbesturingssysteemversie** Microsoft Windows Server 2016 (64-bits). Klik op **Volgende**.

    ![Pagina Naam, Naam van het besturingssysteem en Locatie selecteren](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. Selecteer op de pagina **Opslag selecteren** een gegevensopslag die u wilt gebruiken voor het inrichten van uw virtuele machine. Klik op **Volgende**.

    ![Pagina opslag selecteren](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. Op de pagina **Instellingen aanpassen** stelt u de **CPU** in op 4, het **geheugen** op 8192 MB (of meer) en de **harde schijf 1** op 2 TB (of meer). Kies **SCSI harde schijf** om toe te voegen. In dit geval was dat LSI Logic SAS. **De statische IDE-schijven worden niet ondersteund.** De **harde schijf 1** is de virtuele gegevensschijf. Houd er rekening mee dat u een ingerichte schijf niet kunt verkleinen. Een poging om de schijf te verkleinen resulteert in verlies van alle lokale gegevens op het apparaat. 

    ![Pagina Instellingen aanpassen](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    Op dezelfde pagina klikt u op **Harde schijf toevoegen** en selecteert u vervolgens **Bestaande harde schijf**. Selecteer het VMDK-bestand in de gegevensopslag. Hiermee wordt een besturingssysteemschijf toegevoegd. 

     ![Instellingen aanpassen - Een harde schijf toevoegen](./media/data-box-gateway-deploy-provision-vmware/customize-settings-add-hard-disk.png)

    Schuif omlaag totdat u de **nieuwe vaste schijf** ziet en vouw deze uit om de instellingen te kunnen zien. Stel het **knooppunt van het virtuele apparaat** in op **IDE-controller 0**.

     ![Instellingen aanpassen - Een nieuwe harde schijf configureren](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk.png)

17. (Optioneel) *Voer deze stap alleen uit als u VMware ESXi-Server 6.7 gebruikt*. Op de pagina **Instellingen aanpassen** klikt u op **VM-opties**. Ga naar **Opstartopties > Firmware** en wijzig deze in **BIOS**. De waarde is standaard ingesteld op EFI. Klik op **Volgende**.

    ![Instellingenpagina aanpassen als VMware ESXi-server 6.7 wordt uitgevoerd](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk-esxi.png)

18. Op de pagina **Gereed om te voltooien** controleert u alle instellingen voor de nieuwe virtuele machine. Controleer of CPU 4 is, het geheugen 8192 MB is, de netwerkinterface 1 is en harde schijf 2 IDE-controller 0 is. Klik op **Voltooien**.
   
    ![Pagina Gereed om te voltooien](./media/data-box-gateway-deploy-provision-vmware/image16.png)
    ![Pagina Gereed om te voltooien 2](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Uw virtuele machine is nu ingericht. U ziet als resultaat nu een melding dat de nieuwe virtuele machine is toegevoegd aan de lijst met virtuele machines.

![Nieuwe virtuele machine toegevoegd aan de lijst met Vm's](./media/data-box-gateway-deploy-provision-vmware/image17.png)

De volgende stap is om deze virtuele machine in te schakelen en om het IP-adres op te halen.

> [!NOTE]
> Het wordt niet aanbevolen om VMware-hulpprogramma's op uw virtuele apparaat te installeren (zoals hierboven is ingericht). Als u VMware-hulpprogramma's installeert, leidt dat tot een niet-ondersteunde configuratie.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Het virtuele apparaat starten en het IP-adres ophalen

Voer de volgende stappen uit om uw virtuele apparaat te starten en verbinding te maken met het apparaat.

#### <a name="to-start-the-virtual-device"></a>Het virtuele apparaat starten
1. Start het virtuele apparaat. In het rechterdeelvenster selecteert u uw apparaat in de lijst met virtuele machines en klikt u met de rechtermuisknop erop om het contextmenu weer te geven. Selecteer **Aanzetten** en selecteer vervolgens **Inschakelen**. Nu zou uw virtuele machine moeten zijn ingeschakeld. U kunt de status bekijken in het onderste deelvenster van de webclient.

    ![Het virtuele apparaat inschakelen](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. Selecteer nogmaals uw virtuele machine. Klik met de rechtermuisknop op **Console** en selecteer deze. Selecteer vervolgens **In een nieuw venster openen**.

    ![De console van het virtuele apparaat openen](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. De console van de virtuele machine wordt in een nieuw venster geopend. 

    ![Contsole van het virtuele apparaat](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. Zodra het apparaat wordt uitgevoerd, wijst u met de cursor het tabblad midden boven in het consolevenster aan en klikt u erop. Selecteer **Gastbesturingssysteem > Sleutels verzenden > Ctrl+Alt+Delete**. Hiermee ontgrendelt u de virtuele machine.

   ![Het virtuele apparaat ontgrendelen](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. Geef het wachtwoord op om u aan te melden bij de virtuele machine. Het standaardapparaatwachtwoord is *Wachtwoord1*.

   ![Wachtwoord voor het virtuele apparaat invoeren](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. De stappen 6-8 gelden alleen als het apparaat wordt opgestart in een niet-DHCP-omgeving. In een DHCP-omgeving kunt u deze stappen overslaan en verder gaan met stap 9. Als u uw apparaat hebt opgestart in een niet-DHCP-omgeving wordt er een bericht van deze strekking weergegeven: **Gebruik de cmdlet Set-HcsIPAddress om het netwerk te configureren**. 
   
7. Gebruik voor het configureren van het netwerk bij de opdrachtprompt de opdracht `Get-HcsIpAddress` om een lijst met netwerkinterfaces weer te geven die zijn ingeschakeld op uw virtuele apparaat. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

8. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Hieronder kunt u een voorbeeld bekijken:

    `Set-HcsIpAddress –Name Ethernet0 –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. Nadat de initiële installatie voltooid is en het apparaat is opgestart, ziet u de bannertekst van het apparaat. Noteer het IP-adres en de URL die wordt weergegeven in de bannertekst om het apparaat te beheren. U gaat dit IP-adres gebruiken om verbinding te maken met de webgebruikersinterface van uw virtuele apparaat en om de lokale instellingen en activering te voltooien.

   ![Bannertekst en verbindings-URL voor virtueel apparaat](./media/data-box-gateway-deploy-provision-vmware/image24.png)

Als uw apparaat niet voldoet aan de minimale configuratievereisten, wordt er een fout weergegeven in de bannertekst (zie hieronder). U moet de apparaatconfiguratie wijzigen zodat er voldoende resources zijn om aan de minimale vereisten te voldoen. Daarna kunt u het apparaat opnieuw opstarten en verbinding maken met het apparaat. Raadpleeg de minimale configuratievereisten in [Controleren of het hostsysteem voldoet aan minimale vereisten voor virtuele apparaten](#check-the-host-system).

Als er tijdens de eerste configuratie een andere fout optreedt bij gebruik van de lokale webgebruikersinterface, raadpleegt u de volgende werkstromen:

- [Diagnostische tests uitvoeren om problemen met configuratie via webgebruikersinterface op te lossen](data-box-gateway-troubleshoot.md#run-diagnostics).
- [Logboekpakket genereren en logboekbestanden weergeven](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Gateway, zoals:

> [!div class="checklist"]
> * Controleren of de host voldoet aan de minimale apparaatvereisten
> * Een virtueel apparaat inrichten in VMware
> * Het virtuele apparaat starten en het IP-adres ophalen

Ga naar de volgende zelfstudie voor informatie over het maken van verbinding met en het instellen en activeren van uw virtuele apparaat.

* [Uw Data Box Gateway instellen en verbinding maken met shares hierop](data-box-gateway-deploy-connect-setup-activate.md)

