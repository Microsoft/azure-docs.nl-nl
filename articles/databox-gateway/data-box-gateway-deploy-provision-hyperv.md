---
title: Zelfstudie over het inrichten van Azure Data Box Gateway in Hyper-V| Microsoft Docs
description: In de tweede zelfstudie moet voor het implementeren van Azure Data Box Gateway een virtueel apparaat in Hyper-V worden ingericht.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 9d1d22d57464266239aea96f427020351eb749d5
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740654"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>Zelfstudie: Azure Data Box Gateway inrichten in Hyper-V

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt beschreven hoe u een Data Box Gateway inricht op een hostsysteem waarop Hyper-V wordt uitgevoerd in Windows Server 2016, Windows Server 2012 R2 of Windows Server 2012.

U hebt beheerdersmachtigingen nodig om een virtueel apparaat in te richten en te configureren. De inrichting en de initiële installatie kan circa tien minuten duren. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Controleren of de host voldoet aan de minimale apparaatvereisten
> * Een virtueel apparaat in hypervisor inrichten
> * Het virtuele apparaat starten en het IP-adres ophalen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De vereisten om een virtueel apparaat in te richten op een hostsysteem waarop Hyper-V wordt uitgevoerd in Windows Server 2016 of Windows Server 2012 R2, zijn de volgende.

### <a name="for-the-data-box-gateway-resource"></a>Voor de Data Box Gateway-resource

Zorg voordat u begint voor het volgende:

* U hebt alle stappen in [De portal voorbereiden voor Data Box Gateway](data-box-gateway-deploy-prep.md) uitgevoerd.
* U hebt de installatiekopie van het virtuele apparaat voor Hyper-V vanuit de Azure Portal gedownload zoals is beschreven in [De portal voorbereiden voor Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > De software die wordt uitgevoerd op de Data Box Gateway kan alleen worden gebruikt met de Data Box Gateway-resource.

### <a name="for-the-data-box-gateway-virtual-device"></a>Voor het virtuele Data Box Gateway-apparaat

Voordat u een apparaat implementeert, controleert u het volgende:

* U hebt toegang tot een hostsysteem waarop Hyper-V wordt uitgevoerd in Windows Server 2012 R2 of hoger, dat kan worden gebruikt voor het inrichten van een apparaat.
* Het hostsysteem kan de volgende resources reserveren voor het inrichten van uw virtuele apparaat:

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen.
  * Eén netwerkinterface.
  * Een besturingssysteemschijf van 250 GB.
  * Een virtuele schijf van 2-TB voor gegevens.

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter

Voordat u begint:

* Controleer de netwerkvereisten voor het implementeren van een Data Box Gateway en configureer het netwerk van het datacenter aan de hand van die vereisten. Zie [Netwerkvereisten voor Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements) voor meer informatie.
* Voor een optimale werking van het apparaat heeft internet een minimale bandbreedte van 20 Mbps nodig.

## <a name="check-the-host-system"></a>Het hostsysteem controleren

Voor het maken van een virtueel apparaat hebt u het volgende nodig:

* De Hyper-V-rol moet zijn geïnstalleerd op Windows Server 2016, Windows Server 2012 R2 of Windows Server 2012.
* Microsoft Hyper-V-beheer op een Microsoft Windows-client moet zijn verbonden met de host.
* Controleer of de onderliggende hardware (hostsysteem) waarop u het virtuele apparaat maakt, de volgende resources kan reserveren voor uw virtuele apparaat:

  * Minimaal 4 virtuele processors.
  * Ten minste 8 GB RAM-geheugen.
  * Eén netwerkinterface die is verbonden met het netwerk en verkeer naar internet kan routeren.
  * Een besturingssysteemschijf van 250 GB.
  * Een virtuele schijf van 2-TB voor systeemgegevens.

## <a name="bitlocker-considerations"></a>Aandachtspunten voor BitLocker

* Het wordt aangeraden om BitLocker in te schakelen op uw virtuele machine met de Data Box Gateway. BitLocker is standaard niet ingeschakeld. Zie voor meer informatie:
  * [Instellingen voor ondersteuning van versleuteling in Hyper-V Manager](hhttps://docs.microsoft.com/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [BitLocker-ondersteuning op een virtuele machine](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>Een virtueel apparaat in hypervisor inrichten

Voer de volgende stappen uit om een apparaat in de hypervisor in te richten.

1. Kopieer de installatiekopie van het virtuele apparaat op de Windows Server-host naar een lokaal station. U hebt deze VHDX-installatiekopie gedownload via de Azure Portal. Noteer de locatie waar u de installatiekopie naartoe hebt gekopieerd, want u hebt deze installatiekopie verderop in de procedure nodig.

2. Open **Serverbeheer**. Klik in de rechterbovenhoek op **Hulpprogramma's** en selecteer **Hyper-V-beheer**.

    ![Selecteer Hyper-V-beheer in Serverbeheer](./media/data-box-gateway-deploy-provision-hyperv/image1.png)

3. In **Hyper-V-beheer** klikt u in het scopedeelvenster met de rechtermuisknop op uw systeemknooppunt om het contextmenu te openen. Klik vervolgens op **Nieuw** > **Virtuele machine**.

   ![Een nieuwe virtuele machine maken in Hyper-V-beheer](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. Klik op de pagina **Voordat u begint** van de wizard Nieuwe virtuele machine op **Volgende**.
5. Geef op de pagina **Naam en locatie opgeven** een **Naam** op voor het virtuele apparaat. Klik op **Volgende**.

   ![Pagina voor opgeven van naam en locatie](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. Op de pagina **Generatie opgeven** kiest u **Generatie 2** als het .vhdx-installatiekopietype van het apparaat en klikt u op **Volgende**.    

   ![Pagina voor opgeven van generatie](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. Op de pagina **Geheugen toewijzen** geeft u een **Opstartgeheugen** van ten minste **8192 MB** op. Schakel Dynamisch geheugen niet in en klik op **Volgende**.

   ![Pagina voor toewijzen van geheugen](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. Op de pagina **Netwerk configureren** geeft de virtuele switch op die is verbonden met internet en klikt u op **Volgende**.

   ![Pagina voor configureren van netwerk](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. Op de pagina **Virtuele harde schijf aansluiten** kiest u **Een bestaande virtuele harde schijf gebruiken**, geeft u de locatie op van de installatiekopie van het virtuele apparaat en klikt u op **Volgende**.

   ![Pagina voor verbinden van virtuele harde schijf](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. Controleer de **Samenvatting** en klik op **Voltooien** om de virtuele machine te maken.

    ![De pagina Nieuwe virtuele machine van de wizard voltooien](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. U hebt vier virtuele processors nodig om te voldoen aan de minimale vereisten. Selecteer uw hostsysteem in het venster **Hyper-V-beheer** om vier virtuele processors toe te voegen. Zoek in het rechterdeelvenster onder de lijst met **Virtuele Machines** de virtuele machine die u zojuist hebt gemaakt. Klik met de rechtermuisknop op de naam van de machine en selecteer **Instellingen**.

    ![Instellingen voor virtuele machines](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. Klik op de pagina **Instellingen** in het linkerdeelvenster op **Processor**. Stel in het rechterdeelvenster **Aantal virtuele processors** in op 4 (of meer). Klik op **Toepassen**.

    ![Het aantal virtuele processors instellen op de pagina Instellingen](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. Om aan de minimale vereisten te voldoen, moet u ook een virtuele gegevensschijf van 2-TB toevoegen. Op de pagina **Instellingen** doet u het volgende:

    1. Selecteer in het linkerdeelvenster de optie **SCSI-controller**.
    2. In het rechterdeelvenster selecteert u de optie **Harde schijf** en klikt u op **Toevoegen**.

    ![Harde schijf toevoegen op de pagina Instellingen](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. Op de pagina **Harde schijf** selecteert u de optie **Virtuele harde schijf** en klikt u op **Nieuw**. De **Wizard Nieuwe virtuele harde schijf** wordt gestart.

    ![Wizard Nieuwe virtuele harde schijf](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. Op de pagina **Voordat u begint** van de wizard Nieuwe virtuele harde schijf klikt u op **Volgende**.
16. Op de pagina **Schijfindeling kiezen** accepteert u de standaardindeling **VHDX**. Klik op **Volgende**.
17. Op de pagina **Schijftype kiezen** stelt u het type virtuele harde schijf in op **Dynamisch uitbreidbaar** (aanbevolen). Een schijf van het type **Vaste grootte** werkt waarschijnlijk ook, maar dan moet u mogelijk lang wachten. U wordt geadviseerd om niet de optie **Differentiërende** te gebruiken. Klik op **Volgende**.

    ![Pagina voor kiezen van schijftype](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. Op de pagina **Naam en locatie opgeven** geeft u een **Naam** en een **Locatie** voor de gegevensschijf op (u kunt naar een locatie bladeren). Klik op **Volgende**.

    ![Pagina voor opgeven van naam en locatie](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. Op de pagina **Schijf configureren** selecteert u de optie **Nieuwe lege virtuele harde schijf maken** en geeft u een grootte op van **2 TB** (of meer).

    Hoewel 2 TB de minimumvereiste is, kunt u altijd een grotere schijf inrichten. Houd er rekening mee dat u een schijf niet kunt verkleinen zodra deze is ingericht. Een poging om de schijf te verkleinen resulteert in verlies van alle lokale gegevens op het apparaat. Uitbreiding van een gegevensschijf wordt niet ondersteund. Klik op **Volgende**.

    ![Pagina voor configureren van schijfopslag](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. Op de pagina **Samenvatting** controleert u de details van uw virtuele gegevensschijf en als u tevreden bent, klikt u op **Voltooien** om de schijf te maken. De wizard wordt gesloten en er wordt een virtuele harde schijf aan uw machine toegevoegd.

    ![Pagina Nieuwe virtuele harde schijf van wizard voltooien](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. Ga terug naar de pagina **Instellingen**. Klik op **OK** om de pagina **Instellingen** te sluiten en terug te keren naar het venster Hyper-V-beheer.

    ![De pagina Instellingen](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

Uw virtuele machine is nu volledig geconfigureerd.

> [!NOTE]
> U kunt geen nieuwe Data Box Gateway inrichten door de geconfigureerde VHD te kopiëren. Elk nieuw virtueel Data Box Gateway-apparaat moet worden ingericht op basis van een installatiekopie voor een virtueel apparaat voor Hyper-V, die wordt gedownload vanuit Azure Portal.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Het virtuele apparaat starten en het IP-adres ophalen

Voer de volgende stappen uit om uw virtuele apparaat te starten en verbinding te maken met het apparaat.

#### <a name="to-start-the-virtual-device"></a>Het virtuele apparaat starten

1. Start het virtuele apparaat.

   ![Virtueel apparaat starten](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. Wanneer het apparaat is gestart, selecteert u het apparaat, klikt u met de rechtermuisknop en selecteert u **Verbinding maken**.

3. Het kan 10-15 minuten duren voor het apparaat gereed is. Er wordt een statusbericht op de console weergegeven om de voortgang aan te geven. Wanneer het apparaat gereed is, gaat u naar **Actie**. Druk op `Ctrl + Alt + Delete` om u aan te melden bij het virtuele apparaat. De standaardwaarde is *EdgeUser* en het standaardwachtwoord is *Password1*.

   ![Aanmelden bij het virtuele apparaat](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. De stappen 5 - 7 gelden alleen als het opstarten in een niet-DHCP-omgeving gebeurt. In een DHCP-omgeving kunt u deze stappen overslaan. Als u uw apparaat hebt opgestart in een niet-DHCP-omgeving wordt er een bericht van die strekking weergegeven.

5. Om het netwerk te configureren, gebruikt u de opdracht `Get-HcsIpAddress` om een lijst weer te geven met netwerkinterfaces die zijn ingeschakeld op uw virtuele apparaat. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

6. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Zie het volgende voorbeeld:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. Wanneer de initiële installatie is voltooid en het apparaat is opgestart, ziet u de bannertekst van het apparaat. Noteer het IP-adres en de URL die in de bannertekst worden weergegeven om het apparaat te beheren. U hebt dit IP-adres nodig om verbinding te maken met de webgebruikersinterface van uw virtuele apparaat en voor de lokale configuratie en activering.

   ![Banner met virtuele apparaten met IP-adres en verbindings-URL](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

Als uw apparaat niet voldoet aan de minimale configuratievereisten, wordt er een fout weergegeven in de bannertekst. Wijzig de apparaatconfiguratie zo dat de virtuele machine voldoende resources heeft om aan de minimale vereisten te voldoen. Daarna kunt u het apparaat opnieuw opstarten en verbinding maken met het apparaat. Raadpleeg de minimale configuratievereisten in [Controleren of het hostsysteem voldoet aan minimale vereisten voor virtuele apparaten](#check-the-host-system).

Als er tijdens de eerste configuratie een andere fout optreedt bij gebruik van de lokale webgebruikersinterface, raadpleegt u de volgende werkstromen:

* [Diagnostische tests uitvoeren om problemen met configuratie via webgebruikersinterface op te lossen](data-box-gateway-troubleshoot.md#run-diagnostics).
* [Logboekpakket genereren en logboekbestanden weergeven](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Gateway, zoals:

> [!div class="checklist"]
> * Controleren of de host voldoet aan de minimale apparaatvereisten
> * Een virtueel apparaat in hypervisor inrichten
> * Het virtuele apparaat starten en het IP-adres ophalen

Ga naar de volgende zelfstudie voor informatie over het verbinden, instellen en activeren van uw virtuele apparaat.

> [!div class="nextstepaction"]
> [Een Data Box Gateway verbinden en instellen](./data-box-gateway-deploy-connect-setup-activate.md)
