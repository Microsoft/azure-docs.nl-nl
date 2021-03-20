---
title: 'Snelstartgids: een Privécloud maken'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Meer informatie over het maken en configureren van een Privécloud met Azure VMware-oplossingen op CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 487308aca3231650aee3fac5ae127006649e19b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073914"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Snelstartgids-een Privécloud configureren

In dit artikel leert u hoe u een CloudSimple-Privécloud maakt en uw Privécloud hebt ingesteld.

## <a name="before-you-begin"></a>Voordat u begint

Controleer de [netwerk vereisten](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Een privécloud maken

Een Privécloud is een geïsoleerde VMware-stack die ondersteuning biedt voor ESXi-hosts, vCenter, vSAN en NSX.

Persoonlijke Clouds worden beheerd via de CloudSimple-Portal. Ze hebben hun eigen vCenter-Server in een eigen beheer domein. De stack wordt uitgevoerd op toegewezen knoop punten en geïsoleerde bare metal hardware-knoop punten.

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
3. Selecteer de CloudSimple-service waarop u uw Privécloud wilt maken.
4. Klik in **overzicht** op **privécloud maken** om een nieuw browser tabblad voor CloudSimple portal te openen.  Meld u aan met uw aanmeldings referenties voor Azure als u hierom wordt gevraagd.  

    ![Een Privécloud maken van Azure](media/create-private-cloud-from-azure.png)

5. Geef in de CloudSimple-Portal een naam op voor de Privécloud.
6. Selecteer de **locatie** van de privécloud.
7. Selecteer het **knooppunt type** in overeenstemming met wat u in azure hebt ingericht.
8. Geef het **aantal knoop punten** op.  Er zijn ten minste drie knoop punten vereist voor het maken van een Privécloud.

    ![Een persoonlijke Cloud maken-basis gegevens](media/create-private-cloud-basic-info.png)

9. Klik op **volgende: geavanceerde opties**.
10. Voer het CIDR-bereik in voor vSphere/vSAN-subnetten. Zorg ervoor dat het CIDR-bereik niet overlapt met een van uw on-premises of andere Azure-subnetten (virtuele netwerken) of met het gateway-subnet.

    **Opties voor het CIDR-bereik:** /24,/23,/22 of/21. Een/24 CIDR-bereik ondersteunt Maxi maal 26 knoop punten, een/23 CIDR-bereik ondersteunt Maxi maal 58 knoop punten en een/22-en/21 CIDR-bereik ondersteunt 64-knoop punten (het maximum aantal knoop punten in een Privécloud).  Zie voor meer informatie en VLAN'S en subnetten het [overzicht van vlan's en subnetten](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > IP-adressen in het vSphere/vSAN CIDR-bereik zijn gereserveerd voor gebruik door de infra structuur van de Privécloud.  Gebruik het IP-adres in dit bereik op geen enkele virtuele machine.

11. Klik op **volgende: controleren en maken**.
12. Controleer de instellingen. Als u instellingen wilt wijzigen, klikt u op **vorige**.
13. Klik op **Create**.

Het inrichtings proces voor de privécloud wordt gestart.  Het kan tot twee uur duren voordat de Privécloud is ingericht.

## <a name="launch-cloudsimple-portal"></a>CloudSimple-Portal starten

U kunt de CloudSimple-Portal openen via Azure Portal.  De CloudSimple-portal wordt gestart met uw Azure-aanmeldings referenties met behulp van single Sign-On (SSO).  Voor toegang tot de CloudSimple-Portal moet u de **CloudSimple-service autorisatie** toepassing autoriseren.  Zie [instemming met de CloudSimple-service autorisatie toepassing](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)voor meer informatie over het verlenen van machtigingen.

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
3. Selecteer de CloudSimple-service waarop u uw Privécloud wilt maken.
4. Klik in overzicht op **Ga naar de CloudSimple-Portal** om een nieuw browser tabblad voor CloudSimple-portal te openen.  Meld u aan met uw aanmeldings referenties voor Azure als u hierom wordt gevraagd.  

    ![CloudSimple-Portal starten](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Punt-naar-site-VPN maken

Een punt-naar-site-VPN-verbinding is de eenvoudigste manier om vanaf uw computer verbinding te maken met uw Privécloud. Gebruik punt-naar-site-VPN-verbinding als u op afstand verbinding maakt met de Privécloud.  Volg de onderstaande stappen voor snelle toegang tot uw Privécloud.  U kunt toegang tot de CloudSimple-regio van uw on-premises netwerk doen met behulp van [site-naar-site-VPN](vpn-gateway.md) of [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Gateway maken

1. Start de CloudSimple-Portal en selecteer **netwerk**.
2. Selecteer **VPN gateway**.
3. Klik op **nieuw VPN gateway**.

    ![Een VPN-gateway maken](media/create-vpn-gateway.png)

4. Geef voor **Gateway configuratie** de volgende instellingen op en klik op **volgende**.

    * Selecteer **punt-naar-site-VPN** als het gateway type.
    * Voer een naam in om de gateway te identificeren.
    * Selecteer de Azure-locatie waar uw CloudSimple-service is geïmplementeerd.
    * Geef het client subnet op voor de punt-naar-site-gateway.  Wanneer u verbinding maakt, worden er DHCP-adressen van dit subnet gegeven.

5. Geef voor **verbinding/gebruiker** de volgende instellingen op en klik op **volgende**.

    * Als u wilt dat alle huidige en toekomstige gebruikers automatisch toegang hebben tot de Privécloud via deze punt-naar-site-gateway, selecteert u **automatisch alle gebruikers toevoegen**. Wanneer u deze optie selecteert, worden alle gebruikers in de lijst met gebruikers automatisch geselecteerd. U kunt de automatische optie onderdrukken door afzonderlijke gebruikers in de lijst uit te scha kelen.
    * Als u alleen afzonderlijke gebruikers wilt selecteren, klikt u op de selectie vakjes in de gebruikers lijst.

6. In het gedeelte VLAN'S/subnetten kunt u beheer-en gebruikers-VLAN'S of-subnetten voor de gateway en verbindingen opgeven.

    * Met de opties voor **automatisch toevoegen** wordt het globale beleid voor deze gateway ingesteld. De instellingen zijn van toepassing op de huidige gateway. De instellingen kunnen worden overschreven in het gebied **selecteren** .
    * Selecteer **vlan's voor beheer/subnetten van persoonlijke Clouds toevoegen**.
    * Klik op door de gebruiker gedefinieerde vlan's  **/subnetten toevoegen** om alle door de gebruiker gedefinieerde vlan's/subnetten toe te voegen.
    * De **selectie** -instellingen overschrijven de algemene instellingen onder **automatisch toevoegen**.

7. Klik op **volgende** om de instellingen te controleren. Klik op de pictogrammen bewerken om wijzigingen aan te brengen.
8. Klik op **maken** om de VPN-gateway te maken.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Verbinding maken met CloudSimple met behulp van punt-naar-site-VPN

VPN-client is nodig om vanaf uw computer verbinding te maken met CloudSimple.  Down load de [openvpn-client](https://openvpn.net/community-downloads/) voor Windows of [viscositeit](https://www.sparklabs.com/viscosity/download/) voor macOS en OS X.

1. Start de CloudSimple-Portal en selecteer **netwerk**.
2. Selecteer **VPN gateway**.
3. Klik in de lijst met VPN-gateways op de punt-naar-site-VPN-gateway.
4. Selecteer **Gebruikers**.
5. Klik op **mijn VPN-configuratie downloaden**.

    ![VPN-configuratie downloaden](media/download-p2s-vpn-configuration.png)

6. Importeer de configuratie op uw VPN-client.

    * Instructies voor het [importeren van de configuratie op Windows-client](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instructies voor het [importeren van de configuratie in macOS of OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Verbinding maken met CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Een VLAN maken voor uw werk belasting-Vm's

Nadat u een Privécloud hebt gemaakt, maakt u een VLAN waar u uw werk belasting/toepassings-Vm's implementeert.

1. Selecteer in de CloudSimple-Portal **netwerk**.
2. Klik op **VLAN/subnetten**.
3. Klik op **VLAN/subnet maken**.

    ![VLAN/subnet maken](media/create-new-vlan-subnet.png)

4. Selecteer de **privécloud** voor het nieuwe VLAN/subnet.
5. Selecteer een VLAN-ID in de lijst.  
6. Voer de naam van een subnet in om het subnet te identificeren.
7. Geef het CIDR-bereik en-masker van het subnet op.  Dit bereik mag niet overlappen met bestaande subnetten.
8. Klik op **Submit**

    ![Details van VLAN/subnet maken](media/create-new-vlan-subnet-details.png)

Het VLAN/subnet wordt gemaakt.  U kunt deze VLAN-ID nu gebruiken om een gedistribueerde poort groep te maken in uw Privécloud.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Uw omgeving verbinden met een virtueel Azure-netwerk

CloudSimple biedt u een ExpressRoute-circuit voor uw Privécloud. U kunt uw virtuele netwerk op Azure aansluiten op het ExpressRoute-circuit. Volg de stappen in [Azure Virtual Network-verbinding met ExpressRoute](./cloudsimple-azure-network-connection.md)voor volledige informatie over het instellen van de verbinding.

## <a name="sign-in-to-vcenter"></a>Aanmelden bij vCenter

U kunt zich nu aanmelden bij vCenter om virtuele machines en beleids regels in te stellen.

1. Als u toegang wilt krijgen tot vCenter, start u vanuit de CloudSimple-Portal. Klik op de start pagina onder **algemene taken** op **VSphere-client starten**.  Selecteer de privécloud en klik vervolgens op **vSphere-client starten** op de privécloud.

    ![VSphere-client starten](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecteer de gewenste vSphere-client om toegang te krijgen tot de vCenter en meld u aan met uw gebruikers naam en wacht woord.  De standaard waarden zijn:
    * Gebruikers naam: `CloudOwner@cloudsimple.local`
    * Wachtwoord: `CloudSimple123!`

De vCenter-schermen in de volgende procedures zijn afkomstig van de vSphere-client (HTML5).

## <a name="change-your-vcenter-password"></a>Uw vCenter-wacht woord wijzigen

CloudSimple raadt u aan uw wacht woord te wijzigen wanneer u zich voor de eerste keer aanmeldt bij vCenter.  
Het wacht woord dat u instelt, moet voldoen aan de volgende vereisten:

* Maximale levens duur: wacht woord moet elke 365 dagen worden gewijzigd
* Hergebruik beperken: gebruikers kunnen de eerdere vijf wacht woorden niet opnieuw gebruiken
* Lengte: 8-20 tekens
* Speciaal teken: ten minste één speciaal teken
* Alfabetische tekens: ten minste één hoofd letter, A-Z en minstens één kleine letter, a-z
* Cijfers: ten minste één numeriek teken, 0-9
* Maximum aantal identieke aangrenzende tekens: drie

    Voor beeld: CC of CC is acceptabel als onderdeel van het wacht woord, maar CCCC niet.

Als u een wacht woord instelt dat niet aan de vereisten voldoet:

* Als u de vSphere Flash-client gebruikt, wordt er een fout melding weer gegeven
* Als u de HTML5-client gebruikt, wordt er geen fout gerapporteerd. De client accepteert de wijziging niet en het oude wacht woord blijft werken.

## <a name="access-nsx-manager"></a>Toegang tot NSX Manager

NSX Manager wordt geïmplementeerd met een standaard wachtwoord. 

* Gebruikers naam: **beheerder**
* Wacht woord: **CloudSimple123!**

U vindt de Fully Qualified Domain Name (FQDN) en het IP-adres van NSX Manager op CloudSimple-Portal.

1. Start de CloudSimple-Portal en selecteer **resources**.
2. Klik op de privécloud die u wilt gebruiken.
3. **VSphere-beheer netwerk** selecteren
4. Gebruik de FQDN of het IP-adres van **NSX Manager** en maak verbinding via een webbrowser.

    ![FQDN van NSX Manager zoeken](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Een poort groep maken

Een gedistribueerde poort groep maken in vSphere:

1. Volg de instructies in ' een gedistribueerde poort groep toevoegen ' in de [vSphere-netwerk handleiding](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Wanneer u de gedistribueerde poort groep instelt, geeft u de VLAN-ID op die [u hebt gemaakt in een VLAN maken voor de vm's van uw werk belasting](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* [Verbinding maken met een on-premises netwerk met behulp van Azure ExpressRoute](on-premises-connection.md)
* [Site-naar-site-VPN van on-premises instellen](vpn-gateway.md)
