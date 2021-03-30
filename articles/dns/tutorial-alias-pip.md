---
title: 'Zelfstudie: een Azure DNS-aliasrecord maken, zodat deze naar een openbaar IP-adres in Azure verwijst'
description: In deze zelfstudie leert u hoe u een Azure DNS-aliasrecord zo configureert dat deze naar een openbaar Azure-IP-adres verwijst.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: d3017d09e94040d16950598dad360fe32930c16b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80985436"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Zelfstudie: Een aliasrecord zo configureren dat deze naar een openbaar Azure-IP-adres verwijst 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een netwerkinfrastructuur maken.
> * Een virtuele webserver maken met een openbaar IP-adres.
> * Een aliasrecord maken dat naar het openbare IP-adres verwijst.
> * De aliasrecord testen.


Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
U moet een beschikbare domeinnaam hebben die u in Azure DNS kunt hosten om te testen. U moet het volledige beheer over dit domein hebben. Volledig beheer betekent ook de mogelijkheid om naamserverrecords (NS) voor het domein in te stellen.

Zie voor instructies voor het hosten van uw domein in Azure DNS [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md).

Het voorbeelddomein dat wordt gebruikt voor deze zelfstudie is contoso.com, maar u moet uw eigen domeinnaam gebruiken.

## <a name="create-the-network-infrastructure"></a>De netwerkinfrastructuur maken
Maak eerst een virtueel netwerk en een subnet waaraan u de webservers gaat toevoegen.
1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer helemaal linksboven in de portal **Een resource maken**. Typ *resourcegroep* in het zoekvak en maak een resourcegroep met de naam **RG-DNS-Alias-pip**.
3. Selecteer **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
4. Maak een virtueel netwerk met de naam **VNet-Server**. Plaats het in de resourcegroep **RG-DNS-Alias-pip** en geef het subnet de naam **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Een virtuele webserver maken
1. Selecteer **Een resource maken** > **Windows Server 2016 VM**.
2. Geef **Web-01** op als naam en plaats de virtuele machine in de resourcegroep **RG-DNS-Alias-TM**. Voer een gebruikersnaam en wachtwoord in en selecteer **OK**.
3. Voor **Grootte**, selecteert u een SKU met 8 GB RAM-geheugen.
4. Selecteer voor **Instellingen** het virtuele netwerk **VNet-Servers** en het subnet **SN-Web**. Selecteer voor openbare inkomende poorten **HTTP** > **HTTPS** > **RDP (3389)** en selecteer vervolgens **OK**.
5. Op de pagina **Overzicht** selecteert u **Maken**.

Deze procedure duurt een paar minuten. Aan de virtuele machine is een NIC gekoppeld. Deze heeft een eenvoudig, dynamisch, openbaar IP-adres met de naam Web-01-ip. Het openbare IP-adres wordt telkens gewijzigd wanneer de virtuele machine opnieuw wordt opgestart.

### <a name="install-iis"></a>IIS installeren

Installeren IIS op **Web01**.

1. Maak verbinding met **Web-01** en meld u aan.
2. Selecteer op het dashboard **Serverbeheer** de optie **Functies en onderdelen toevoegen**.
3. Selecteer drie keer **Volgende**. Selecteer op de pagina **Serverfuncties** de optie **Webserver (IIS)**.
4. Selecteer **Onderdelen toevoegen** en selecteer **Volgende**.
5. Selecteer viermaal **Volgende** en selecteer vervolgens **Installeren**. Deze procedure duurt een paar minuten.
6. Nadat de installatie is voltooid, selecteert u **Sluiten**.
7. Open een webbrowser. Ga naar **localhost** om te controleren of de IIS-standaardwebpagina wordt weergegeven.

## <a name="create-an-alias-record"></a>Een aliasrecord maken

Maak een aliasrecord dat naar het openbare IP-adres verwijst.

1. Selecteer uw Azure DNS-zone om de zone te openen.
2. Selecteer **Recordset**.
3. Selecteer **web01** in het tekstvak **Naam**.
4. Laat het **Type** een **A**-record.
5. Schakel het selectievakje **Alias Record Set** in.
6. Selecteer **Azure-service kiezen** en selecteer vervolgens het openbare IP-adres **Web-01-ip**.

## <a name="test-the-alias-record"></a>Het aliasrecord testen

1. Selecteer in de resourcegroep **RG-DNS-Alias-pip** de virtuele machine **Web-01**. Noteer het openbare IP-adres.
1. Ga in een webbrowser naar de Fully Qualified Domain Name voor de virtuele machine Web01-01. Een voorbeeld is **web01.contoso.com**. De IIS-standaardwebpagina wordt nu weergegeven.
2. Sluit de webbrowser.
3. Stop de virtuele machine **Web-01** en start deze opnieuw.
4. Nadat de virtuele machine opnieuw is opgestart, noteert u het nieuwe openbare IP-adres van de virtuele machine.
5. Open een nieuwe browser. Ga opnieuw naar de Fully Qualified Domain Name voor de virtuele machine Web01-01. Een voorbeeld is **web01.contoso.com**.

Deze procedure werkt, omdat u een aliasrecord hebt gebruikt om te verwijzen naar de openbare IP-adresresource en niet naar een standaard A-record.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep **RG-DNS-Alias-pip** als u de resources die u in deze zelfstudie hebt gemaakt, niet meer nodig hebt.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een aliasrecord gemaakt die naar een openbaar Azure-IP-adres verwijst. Als u meer wilt weten over Azure DNS en web-apps gaat u verder met de zelfstudie voor web-apps.

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)
