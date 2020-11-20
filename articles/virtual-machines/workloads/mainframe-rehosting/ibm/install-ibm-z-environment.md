---
title: IBM zD&T dev/test-omgeving installeren op Azure | Microsoft Docs
description: Implementeer de IBM Z-ontwikkelings-en test omgeving (zD&T) op de infra structuur van Azure virtual machine (VM) als een service (IaaS).
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: c12a812f66345a26701b2a1623487fd262ede4be
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968329"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>IBM zD&T dev/test-omgeving installeren op Azure

Als u een ontwikkel-en test omgeving wilt maken voor mainframe workloads op IBM Z-systemen, kunt u de IBM Z-ontwikkelings-en test omgeving (zD&T) implementeren op de infra structuur van Azure virtual machine (VM) als een service (IaaS).

Met zD&T kunt u profiteren van de kosten besparingen van het x86-platform voor uw minder kritieke ontwikkel-en test omgevingen en vervolgens de updates vervolgens terugsturen naar een systeem productie omgeving van Z. Zie de installatie-instructies voor [IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)voor meer informatie.

Azure en Azure Stack ondersteunen de volgende versies:

- zD&T Personal Edition
- zD&T parallelle Sysplex
- zD&T Enter prise Edition

Alle versies van zD&T alleen worden uitgevoerd op x86 Linux-systemen, niet op Windows Server. Enter prise Edition wordt ondersteund op een Red Hat Enterprise Linux (RHEL) of Ubuntu/Debian. Er zijn zowel RHEL-als Debian-VM-installatie kopieën beschikbaar voor Azure.

Dit artikel laat u zien hoe u zD&T Enter prise Edition op Azure kunt instellen, zodat u de zD&T Enter prise Edition gebruikt voor het maken en beheren van omgevingen. Wanneer u zD&T installeert, worden er geen omgevingen geïnstalleerd. U moet deze afzonderlijk maken als installatie pakketten. Ontwikkel aars van toepassings ontwikkelaars (ADCD) zijn bijvoorbeeld volume kopieën van test omgevingen. Ze zijn opgenomen in zip-installatie kopieën op de media distributie die beschikbaar is via IBM. Zie [een ADCD-omgeving instellen in azure](demo.md).

Zie voor meer informatie het [overzicht van zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) in het IBM-kennis centrum.

In dit artikel wordt beschreven hoe u de Enter prise-editie voor Z Development and test (zD&T) kunt instellen op Azure. Vervolgens kunt u de zD&T Enter prise Edition-webserver gebruiken voor het maken en beheren van op Z gebaseerde omgevingen op Azure.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> Met IBM kan zD&T Enter prise Edition alleen worden geïnstalleerd in ontwikkel-en test omgevingen,*niet* in productie omgevingen.

- Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- U hebt toegang tot de media nodig, die alleen beschikbaar is voor klanten en partners van IBM. Voor meer informatie neemt u contact op met uw IBM-vertegenwoordiger of raadpleegt u de contact gegevens op de website van [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) .

- Een [licentie server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Dit is vereist voor toegang tot de omgevingen. De manier waarop u deze maakt, is afhankelijk van hoe u een licentie voor de software van IBM hebt:

     - Op **hardware gebaseerde licentie server** vereist een USB-apparaat met de rationele tokens die nodig zijn voor toegang tot alle delen van de software. U moet dit verkrijgen van IBM.

     - **Licentie server op basis van software** vereist dat u een gecentraliseerde server instelt voor het beheer van de licentie sleutels. Deze methode verdient de voor keur en hiervoor moet u de sleutels instellen die u van IBM wilt ontvangen op de-beheer server.

## <a name="create-the-base-image-and-connect"></a>De basis installatie kopie maken en verbinding maken

1. In Azure Portal [maakt u een virtuele machine](../../../linux/quick-create-portal.md) met de gewenste configuratie van het besturings systeem. In dit artikel wordt ervan uitgegaan dat er een B4ms-VM is (met 4 Vcpu's en 16 GB geheugen) waarop Ubuntu 16,04 wordt uitgevoerd.

2. Nadat de VM is gemaakt, opent u voor de webserver binnenkomende poorten 22 voor SSH, 21 voor FTP en 9443.

3. Ontvang de SSH-referenties die worden weer gegeven op de Blade **overzicht** van de virtuele machine via de knop **verbinding maken** . Selecteer het tabblad **SSH** en kopieer de SSH-aanmeldings opdracht naar het klem bord.

4. Meld u aan bij een [bash-shell](../../../../cloud-shell/quickstart.md) vanaf uw lokale PC en plak de opdracht. Deze wordt weer in de vorm **van \<user id\> \@ \<IP Address\> SSH**. Wanneer u om referenties wordt gevraagd, voert u deze in om een verbinding met uw basismap tot stand te brengen.

## <a name="copy-the-installation-file-to-the-server"></a>Kopieer het installatie bestand naar de server

Het installatie bestand voor de webserver is **ZDT \_ install \_ ee \_ v 12.0.0.1. tgz**. Het is opgenomen in de media die door IBM worden geleverd. U moet dit bestand uploaden naar uw Ubuntu-VM.

1. Voer vanaf de opdracht regel de volgende opdracht in om ervoor te zorgen dat alles up-to-date is in de zojuist gemaakte installatie kopie:

    ```
    sudo apt-get update
    ```

2. Maak de directory waarin u wilt installeren:

    ```
    mkdir ZDT
    ```

3. Kopieer het bestand van uw lokale computer naar de VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Met deze opdracht kopieert u het installatie bestand naar de map ZDT in uw basismap. Dit is afhankelijk van het feit of uw client Windows of Linux uitvoert.

## <a name="install-the-enterprise-edition"></a>De Enter prise-editie installeren

1. Ga naar de map ZDT en decomprimeer het \_ bestand ZDT install \_ ee \_ v 12.0.0.1. tgz met de volgende opdrachten:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Voer het installatie programma uit:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selecteer **1** om de Enter prise-server te installeren.

4. Druk op **Enter** en lees de licentie overeenkomsten aandachtig door. Voeg aan het einde van de licentie **Ja** toe om door te gaan.

5. Wanneer u wordt gevraagd het wacht woord voor de zojuist gemaakte gebruiker **ibmsys1** te wijzigen, gebruikt u de opdracht **sudo passwd ibmsys1** en voert u het nieuwe wacht woord in.

6. Als u wilt controleren of de installatie is voltooid, voert u

    ```
    dpkg -l | grep zdtapp
    ```

7. Controleer of de uitvoer de teken reeks **zdtapp 12.0.0.0** bevat, waarmee wordt aangegeven dat het pakket gas is geïnstalleerd

### <a name="starting-enterprise-edition"></a>Enter prise Edition starten

Wanneer de webserver wordt gestart, wordt deze uitgevoerd onder de zD&T-gebruikers-ID die is gemaakt tijdens het installatie proces.

1. Als u de webserver wilt starten, gebruikt u de hoofd gebruikers-ID om de volgende opdracht uit te voeren:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Kopieer de URL-uitvoer door het script. dit ziet er als volgt uit:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Plak de URL in een webbrowser om het beheer onderdeel voor uw zD&T-installatie te openen.

## <a name="next-steps"></a>Volgende stappen

[Een toepassings ontwikkelaars ADCD (Controlled Distribution) instellen in IBM zD&T v1](./demo.md)
