---
title: Een app voor toepassings ontwikkelaars (ADCD) instellen in IBM zD&T v1 | Microsoft Docs
description: Voer een zD&T-omgeving (IBM Z Development and Test Environment) uit op Azure Virtual Machines (Vm's).
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: b1391f81c571879534258c289436d2d086b84bef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045940"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Een toepassings ontwikkelaars ADCD (Controlled Distribution) instellen in IBM zD&T v1

U kunt een zD&T-omgeving (IBM Z Development and Test Environment) uitvoeren op Azure Virtual Machines (Vm's). In deze omgeving wordt de architectuur van de IBM Z-serie geëmuleerd. Het kan een groot aantal besturings systemen van de Z-serie of-installaties (ook wel Z-instanties of pakketten genoemd) hosten, die beschikbaar worden gesteld via aangepaste bundels, de beheerde distributies van IBM-toepassings ontwikkelaars (ADCDs).

In dit artikel wordt beschreven hoe u een ADCD-exemplaar instelt in een zD&T-omgeving in Azure. ADCDs maken volledige Z-reeks implementaties van het besturings systeem voor ontwikkel-en test omgevingen die worden uitgevoerd in zD&T.

Net zoals zD&T, zijn ADCDs alleen beschikbaar voor klanten en partners van IBM en zijn uitsluitend bedoeld voor ontwikkelings-en test doeleinden. Ze hoeven niet te worden gebruikt voor productie omgevingen. Talloze IBM-installatie pakketten kunnen worden gedownload via [Pass Port Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) of [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- De [zD&T-omgeving][ibm-install-z] is eerder ingesteld op Azure. In dit artikel wordt ervan uitgegaan dat u dezelfde Ubuntu 16,04 VM-installatie kopie gebruikt die u eerder hebt gemaakt.

- Toegang tot de ADCD-media via IBM PartnerWorld of pass Port Advantage.

- Een [licentie server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Dit is vereist om IBM zD&T uit te voeren. De manier waarop u deze maakt, is afhankelijk van hoe u een licentie voor de software van IBM hebt:

  - Op **hardware gebaseerde licentie server** vereist een USB-apparaat met de rationele tokens die nodig zijn voor toegang tot alle delen van de software. U moet dit verkrijgen van IBM.

  - **Licentie server op basis van software** vereist dat u een gecentraliseerde server instelt voor het beheer van de licentie sleutels. Deze methode verdient de voor keur en hiervoor moet u de sleutels instellen die u van IBM wilt ontvangen op de-beheer server.

## <a name="download-the-installation-packages-from-passport-advantage"></a>De installatie pakketten downloaden van het Pass Port-voor deel

Toegang tot de ADCD-media is vereist. In de onderstaande stappen wordt ervan uitgegaan dat u IBM-klanten bent en gebruik kunt maken van Pass Port. IBM-partners kunnen [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)gebruiken.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat een Windows-PC wordt gebruikt voor toegang tot Azure Portal en om de IBM-media te downloaden. Als u een Mac-of Ubuntu-Desktop gebruikt, kunnen de opdrachten en het proces voor het verkrijgen van de IBM-media enigszins verschillen.

1. Meld u aan bij [Pass Port-voor delen](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selecteer **software downloads** en **media toegang**.

3. Selecteer **programma aanbieding en overeenkomst nummer** en klik op **door gaan**.

4. Voer de beschrijving van het onderdeel of het onderdeel nummer in en klik op **Finder**.

5. Klik desgewenst op de alfabetische volg orde van de lijst om het product op naam weer te geven en weer te geven.

6. Selecteer **alle besturings systemen** in het **veld besturings systeem** en **alle talen** in het **veld talen**. Klik vervolgens op **Go**.

7. Klik op **afzonderlijke bestanden selecteren** om de lijst uit te vouwen en de afzonderlijke media weer te geven die u wilt downloaden.

8. Controleer de pakket (en) die u wilt downloaden, selecteer **downloaden** en down load de bestanden naar de gewenste map.

## <a name="upload-the-adcd-packages"></a>De ADCD-pakket (en) uploaden

Nu u de pakket (en) hebt, moet u deze uploaden naar uw VM in Azure.

1. In de Azure Portal initieert u een **SSH** -sessie met de Ubuntu-VM die u hebt gemaakt. Ga naar uw virtuele machine, selecteer de Blade **overzicht** en selecteer vervolgens **verbinding maken**.

2. Selecteer het tabblad **SSH** en kopieer de SSH-opdracht naar het klem bord.

3. Meld u aan bij uw virtuele machine met uw referenties en de gewenste [SSH-client](../../../linux/use-remote-desktop.md) . Deze demo maakt gebruik van de Linux-uitbrei dingen voor Windows 10, waarmee een bash-shell wordt toegevoegd aan de Windows-opdracht prompt. PuTTy werkt net zo goed.

4. Wanneer u bent aangemeld, maakt u een map voor het uploaden van de IBM-pakketten. Houd er rekening mee dat Linux hoofdletter gevoelig is. Deze demo gaat er bijvoorbeeld van uit dat de pakketten worden geüpload naar:

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. Upload de bestanden met behulp van een SSH-client, zoals[WinSCP](https://winscp.net/eng/index.php). Aangezien SCP deel uitmaakt van SSH, wordt poort 22 gebruikt. Dit is wat SSH gebruikt. Als uw lokale computer niet Windows is, typt u de [SCP-opdracht](http://man7.org/linux/man-pages/man1/scp.1.html) in uw SSH-sessie.

6. Start de upload naar de Azure VM-map die u hebt gemaakt, die de installatie kopie voor zD&T wordt.

    > [!NOTE]
    > Zorg ervoor dat **ADCDTOOLS.XML** is opgenomen in de upload naar de map **Home/MyUserID/ZDT/adcd/nov2017** . U hebt dit later nodig.

7. Wacht tot de bestanden zijn geüpload. Dit kan enige tijd duren, afhankelijk van uw verbinding met Azure.

8. Wanneer de uploads zijn voltooid, gaat u naar de map volumes en decomprimeert u alle **gz** -volumes:

    ```console
    gunzip \*.gz
    ```
    
![Bestanden Verkenner met gedecomprimeerde gz-volumes](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>De installatie kopie opslag configureren

De volgende stap is het configureren van zD&T voor het gebruik van de geüploade pakket (en). Met het proces voor het opslaan van installatie kopieën in zD&T kunt u de installatie kopieën koppelen en gebruiken. U kunt SSH of FTP gebruiken.

1. Start de **zDTServer**. Hiervoor moet u zich op het hoofd niveau bevindt. Voer de volgende twee opdrachten in de aangegeven volg orde in:
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. Noteer de URL-uitvoer van de opdracht en gebruik deze URL voor toegang tot de webserver. Dit ziet er ongeveer als volgt uit:
     > https://(uw VM-naam of IP-adres): 9443/ZDTMC/index.html
     >
     > Vergeet niet dat uw webtoegang gebruikmaakt van poort 9443. Gebruik deze om u aan te melden bij de webserver. De gebruikers-ID voor ZD&T is **zdtadmin** en het wacht woord is **wacht woord**.

    ![Welkomst scherm voor IBM zD&T Enter prise Edition](media/02-welcome.png)

3. Selecteer op de pagina **snel starten** , onder **configureren**, **installatie kopie opslag**.

     ![ZD Snel starten scherm IBM-&T Enter prise Edition](media/03-quickstart.png)

4. Selecteer op de pagina **installatie kopie opslag configureren** de optie **SSH-File Transfer Protocol**.

5. Voor **hostnaam** typt u **localhost** en voert u het mappad in voor de locatie waar u de installatie kopieën hebt geüpload. Bijvoorbeeld/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Voer de **gebruikers-id** en het **wacht woord** voor de virtuele machine in. Gebruik niet de ZD&T-gebruikers-ID en wacht woord.

7. Test de verbinding om er zeker van te zijn dat u toegang hebt en selecteer vervolgens **Opslaan** om de configuratie op te slaan.

## <a name="configure-the-target-environments"></a>De doel omgevingen configureren

De volgende stap is het configureren van de zD&T-doel omgeving. Deze geëmuleerde gehoste omgeving is waar uw installatie kopieën worden uitgevoerd.

1. Selecteer op de pagina **snel starten** onder **configureren** de optie **doel omgevingen**.

2. Selecteer op de pagina **doel omgevingen configureren** de optie **doel toevoegen**.

3. Selecteer **Linux**. IBM ondersteunt twee typen omgevingen: Linux en Cloud (Open stack), maar deze demo wordt uitgevoerd op Linux.

4. Voer op de pagina **doel omgeving toevoegen** voor **hostnaam de waarde** **localhost** in. Blijf **SSH-poort** ingesteld op **22**.

5. Voer in het vak **Label doel omgeving** een label in, zoals **MyCICS.**

     ![Scherm doel omgeving toevoegen](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD configureren en implementeren

Nadat u de vorige configuratie stappen hebt voltooid, moet u zD&T configureren voor het gebruik van de pakket (en) en de doel omgeving. Opnieuw gebruikt u het proces voor installatie kopie opslag in zD&T, waarmee u de installatie kopieën kunt koppelen en gebruiken. U kunt SSH of FTP gebruiken.

1. Selecteer op de pagina **snel starten** onder **configureren** de optie **ADCD**. Er wordt een set instructies weer gegeven met de stappen die moeten worden voltooid voordat een ADCD-pakket kan worden gekoppeld. In dit onderwerp wordt uitgelegd waarom we de doel directory op de eerder genoemde manier hebben genoemd.

2. Ervan uitgaande dat alle afbeeldingen zijn geüpload naar de juiste directory's, klikt u op de koppeling **afbeelding van ADCD** in de rechter benedenhoek (weer gegeven in stap 7 van de volgende scherm afbeelding).

     ![IBM zD&T Enter prise Edition: ADCD-scherm configureren](media/05-adcd.png)

## <a name="create-the-image"></a>De installatiekopie maken

Wanneer de vorige configuratie stap is voltooid, wordt de pagina **een installatie kopie maken met behulp van ADCD-onderdelen** weer gegeven.

1. Selecteer in dit geval het volume (nov 2017) om de verschillende pakketten in dat volume weer te geven.

2. Voor deze demo selecteert u **Customer Information Control System (CICS)-5,3**.

3. Typ in het vak **afbeeldings naam** een naam voor de afbeelding, zoals **MyCICS-afbeelding**.

4. Selecteer de knop **afbeelding maken** in de rechter benedenhoek.

     ![IBM zD&T Enter prise Edition: een installatie kopie maken met behulp van ADCD-onderdelen scherm](media/06-adcd.png)

5. In het venster dat wordt weer gegeven, met de melding dat de installatie kopie is geïmplementeerd, kiest u **installatie kopieën implementeren**.

6. Selecteer op de pagina **een installatie kopie implementeren op een doel omgeving** de installatie kopie die u hebt gemaakt op de vorige pagina (**MyCICS-installatie kopie**) en de doel omgeving die u eerder hebt gemaakt (**MyCICS**).

7. Geef in het volgende scherm uw referenties op voor de virtuele machine (dat wil zeggen, niet de ztadmin-referentie).

8. Voer in het deel venster Eigenschappen het aantal **centrale processors (CPS)**, de hoeveelheid **systeem geheugen (GB)** en de **implementatie Directory** voor de actieve installatie kopie in. Aangezien dit een demo is, moet u deze klein blijven.

9. Zorg ervoor dat het selectie vakje is ingeschakeld voor **IPL-opdracht automatisch verlenen aan z/o's na de implementatie**.

     ![Scherm Eigenschappen](media/07-properties.png)

10. Selecteer **volt ooien**.

11. Selecteer **installatie kopie implementeren** vanaf de pagina **een installatie kopie implementeren op een doel omgeving** .

Uw installatie kopie kan nu worden geïmplementeerd en kan worden gekoppeld door een 3270-terminal emulator.

> [!NOTE]
> Als er een fout bericht wordt weer gegeven met de melding dat er onvoldoende schijf ruimte is, moet u er rekening mee houden dat de regio 151 GB vereist.

Gefeliciteerd U voert nu een IBM mainframe-omgeving uit op Azure.

## <a name="learn-more"></a>Lees meer

- [Mainframe migratie: mythen en feiten](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale op Azure](ibm-db2-purescale-azure.md)
- [Problemen oplossen](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [Ontrafelen mainframe naar Azure-migratie](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md