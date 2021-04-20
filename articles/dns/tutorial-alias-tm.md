---
title: 'Zelfstudie: Een aliasrecord maken ter ondersteuning van apex-domeinnamen - Traffic Manager'
titleSuffix: Azure DNS
description: In deze zelfstudie wordt uitgelegd hoe u een Azure DNS-aliasrecord kunt configureren om het gebruik van uw hoofddomeinnaam met Traffic Manager te ondersteunen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: e0101133c68142845a8ada50d9921d341cf10ad0
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738788"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Zelfstudie: een Azure DNS-aliasrecord configureren om het gebruik van hoofddomeinnaam met Traffic Manager te ondersteunen 

U kunt een aliasrecord maken voor uw hoofddomeinnaam om te verwijzen naar een Azure Traffic Manager-profiel. Bijvoorbeeld: contoso.com. In plaats van hiervoor een omleidingsservice te gebruiken, kunt u Azure DNS zo configureren dat deze rechtstreeks vanuit uw zone naar een Traffic Manager-profiel verwijst. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een host-VM en netwerkinfrastructuur maken
> * Een Traffic Manager-profiel maken.
> * Een aliasrecord maken.
> * De aliasrecord testen.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
U moet een beschikbare domeinnaam hebben die u in Azure DNS kunt hosten om te testen. U moet het volledige beheer over dit domein hebben. Volledig beheer betekent ook de mogelijkheid om naamserverrecords (NS) voor het domein in te stellen.

Zie voor instructies voor het hosten van uw domein in Azure DNS [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md).

Het voorbeelddomein dat wordt gebruikt voor deze zelfstudie is contoso.com, maar u moet uw eigen domeinnaam gebruiken.

## <a name="create-the-network-infrastructure"></a>De netwerkinfrastructuur maken

Maak eerst een virtueel netwerk en een subnet waaraan u de webservers gaat toevoegen.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer helemaal linksboven in de portal **Een resource maken**. Typ *resourcegroep* in het zoekvak en maak een resourcegroep met de naam **RG-DNS-Alias-TM**.
3. Selecteer **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
4. Maak een virtueel netwerk met de naam **VNet-Servers**. Plaats het in de resourcegroep **RG-DNS-Alias-TM** en geef het subnet de naam **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Maak twee virtuele machines die als webserver dienen

1. Selecteer **Een resource maken** > **Windows Server 2016 VM**.
2. Geef **Web-01** op als naam en plaats de virtuele machine in de resourcegroep **RG-DNS-Alias-TM**. Voer een gebruikersnaam en wachtwoord in en selecteer **OK**.
3. Voor **Grootte**, selecteert u een SKU met 8 GB RAM-geheugen.
4. Selecteer voor **Instellingen** het virtuele netwerk **VNet-Servers** en het subnet **SN-Web**.
5. Selecteer **Openbaar IP-adres**. Bij **Toewijzing** selecteer t u **Statisch** en selecteert u vervolgens **OK**.
6. Voor openbare binnenkomende poorten selecteert u **HTTP (80)**  >  **HTTPS (443)**  >  **RDP (3389)** en vervolgens **OK.**
7. Op de pagina **Overzicht** selecteert u **Maken**. Deze procedure duurt een paar minuten.

Herhaal dit proces om nog een virtuele machine te maken, genaamd **Web-02**.

### <a name="add-a-dns-label"></a>Een DNS-label toevoegen

De openbare IP-adressen hebben een DNS-label nodig om met Traffic Manager te kunnen werken.
1. Selecteer in de resourcegroep **RG-DNS-Alias-TM** het openbare IP-adres **Web-01-ip**.
2. Selecteer onder **Instellingen** de optie **Configuratie**.
3. Geef in het tekstvak voor het DNS-naamlabel **web01pip** op.
4. Selecteer **Opslaan**.

Herhaal deze procedure voor het openbare IP-adres **Web-02-ip** en gebruik daarvoor **web02pip** als het DNS-naamlabel.

### <a name="install-iis"></a>IIS installeren

Installeer IIS op zowel **Web-01** als **Web-02**.

1. Maak verbinding met **Web-01** en meld u aan.
2. Selecteer op het dashboard **Serverbeheer** de optie **Functies en onderdelen toevoegen**.
3. Selecteer drie keer **Volgende**. Selecteer op de pagina **Serverfuncties** de optie **Webserver (IIS)**.
4. Selecteer **Onderdelen toevoegen** en selecteer **Volgende**.
5. Selecteer vier keer **Volgende**. Selecteer vervolgens **Installeren**. Deze procedure duurt een paar minuten.
6. Als de installatie is voltooid, selecteert u **Sluiten**.
7. Open een webbrowser. Ga naar **localhost** om te controleren of de IIS-standaardwebpagina wordt weergegeven.

Herhaal deze procedure om IIS te installeren op **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

1. Open de resourcegroep **RG-DNS-Alias-TM** en selecteer het openbare IP-adres **Web-01-ip**. Noteer het IP-adres voor later gebruik. Herhaal deze stap voor het openbare IP-adres **Web-02-ip**.
1. Selecteer **Een resource maken** > **Netwerken** > **Traffic Manager-profiel**.
2. Voer **TM-alias-test** in als naam. Plaats dit in de resourcegroep **RG-DNS-Alias-TM**.
3. Selecteer **Maken**.
4. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.
5. Selecteer op de Traffic Manager-profielpagina bij **Instellingen****Eindpunten**.
6. Selecteer **Toevoegen**.
7. Selecteer **Extern eindpunt** voor **Type** en typ **EP-Web01** als **naam**.
8. Voer in het tekstvak **Fully-qualified domain name (FQDN) of IP** het IP-adres voor **Web-01-ip** in dat u eerder hebt genoteerd.
9. Selecteer dezelfde **locatie** als uw andere resources en selecteer vervolgens **OK**.

Herhaal deze procedure om het eindpunt **Web-02** toe te voegen met behulp van het IP-adres dat u eerder hebt genoteerd voor **Web-02-ip**.

## <a name="create-an-alias-record"></a>Een aliasrecord maken

Maak een aliasrecord dat naar het Traffic Manager-profiel verwijst.

1. Selecteer uw Azure DNS-zone om de zone te openen.
2. Selecteer **Recordset**.
3. Laat het tekstvak **Naam** leeg om de hoofddomeinnaam te vertegenwoordigen. Bijvoorbeeld: contoso.com.
4. Laat het **Type** een **A**-record.
5. Schakel het selectievakje **Alias Record Set** in.
6. Selecteer **Azure-service kiezen** en selecteer het Traffic Manager-profiel **TM-alias-test**.

## <a name="test-the-alias-record"></a>Het aliasrecord testen

1. Blader in een webbrowser naar uw hoofddomeinnaam. Bijvoorbeeld: contoso.com. De IIS-standaardwebpagina wordt nu weergegeven. Sluit de webbrowser.
2. Sluit de virtuele machine **Web-01**. Wacht een paar minuten totdat deze volledig is afgesloten.
3. Open een nieuwe webbrowser en blader weer naar uw hoofddomeinnaam.
4. De standaard IIS-pagina wordt opnieuw weergegeven, omdat Traffic Manager de situatie heeft afgehandeld en verkeer naar **Web-02** heeft geleid.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep **RG-DNS-Alias-TM** als u de resources die u in deze zelfstudie hebt gemaakt, niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een aliasrecord gemaakt om uw hoofddomeinnaam te gebruiken om naar een Traffic Manager-profiel te verwijzen. Als u meer wilt weten over Azure DNS en web-apps gaat u verder met de zelfstudie voor web-apps.

> [!div class="nextstepaction"]
> [Web-apps met gelijke taakverdeling in het toppunt van de zone hosten](./dns-alias-appservice.md)
