---
title: Veilige toegang tot resources in spoke VNets voor P2S-clients beheren
titleSuffix: Azure Virtual WAN
description: Dit artikel helpt u bij het gebruik van virtuele WAN-en Azure Firewall-regels van Azure voor het beheren van beveiligde toegang tot virtuele netwerken voor VPN-clients (punt-naar-site).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cherylmc
ms.openlocfilehash: 751d11fcd4b5d4c33145ee7f2b7b49971b8927ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048252"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Veilige toegang tot bronnen in spoke VNets voor VPN-clients van gebruikers beheren

In dit artikel wordt beschreven hoe u virtuele WAN-en Azure Firewall regels en filters gebruikt voor het beheren van beveiligde toegang voor verbindingen met uw resources in azure via punt-naar-site IKEv2 of open VPN-verbindingen. Deze configuratie is handig als u externe gebruikers hebt waarvoor u de toegang tot Azure-resources wilt beperken of als u uw resources wilt beveiligen in Azure.

In de stappen in dit artikel vindt u informatie over het maken van de architectuur in het volgende diagram waarmee gebruikers VPN-clients toegang kunnen krijgen tot een specifieke resource (VM1) in een spoke-VNet dat is verbonden met de virtuele hub, maar niet andere resources (VM2). Gebruik dit voor beeld van een architectuur als basis richtlijn.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagram: beveiligde virtuele hub" :::

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* U hebt de beschik bare waarden voor de verificatie configuratie die u wilt gebruiken. Bijvoorbeeld een RADIUS-server, Azure Active Directory-verificatie of [genereren en exporteren van certificaten](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Een virtueel WAN maken

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>P2S-configuratie parameters definiëren

De punt-naar-site-configuratie (P2S) definieert de para meters voor het verbinden van externe clients. In deze sectie leert u hoe u P2S-configuratie parameters definieert en vervolgens de configuratie maakt die wordt gebruikt voor het VPN-client profiel. De instructies die u volgt, zijn afhankelijk van de verificatie methode die u wilt gebruiken.

### <a name="authentication-methods"></a>Verificatiemethoden

Wanneer u de verificatie methode selecteert, hebt u drie opties. Voor elke methode gelden specifieke vereisten. Selecteer een van de volgende methoden en voer de stappen uit.

* **Azure Active Directory-verificatie:** Het volgende te verkrijgen:

   * De **toepassings-id** van de Azure VPN-bedrijfs toepassing die in uw Azure AD-Tenant is geregistreerd.
   * De **verlener**. Bijvoorbeeld: `https://sts.windows.net/your-Directory-ID`.
   * De **Azure AD-Tenant**. Bijvoorbeeld: `https://login.microsoftonline.com/your-Directory-ID`.

* **RADIUS-gebaseerde verificatie:** Haal het IP-adres, het RADIUS-server geheim en de certificaat gegevens van de RADIUS-server op.

* **Azure-certificaten:** Voor deze configuratie zijn certificaten vereist. U moet certificaten genereren of ophalen. Voor elke client is een client certificaat vereist. Daarnaast moeten de gegevens van het basis certificaat (open bare sleutel) worden geüpload. Zie [certificaten genereren en exporteren](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)voor meer informatie over de vereiste certificaten.

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>De hub en gateway maken

In deze sectie maakt u de virtuele hub met een punt-naar-site-gateway. Bij het configureren van kunt u de volgende voorbeeld waarden gebruiken:

* **Privé-IP-adres ruimte hub:** 10.0.0.0/24
* **Client-adres groep:** 10.5.0.0/16
* **Aangepaste DNS-servers:** U kunt Maxi maal 5 DNS-servers weer geven

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Configuratie bestanden voor VPN-clients genereren

In deze sectie kunt u de configuratie profiel bestanden genereren en downloaden. Deze bestanden worden gebruikt voor het configureren van de systeem eigen VPN-client op de client computer. Zie [punt-naar-site-configuratie-certificaten](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md)voor meer informatie over de inhoud van de client profiel bestanden.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>VPN-clients configureren

Gebruik het gedownloade profiel om de clients voor externe toegang te configureren. De procedure verschilt per besturingssysteem. Volg daarom de instructies voor uw systeem.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>De spoke-VNet verbinden

In deze sectie koppelt u het virtuele spoke-netwerk aan de virtuele WAN-hub.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Virtuele machines maken

In deze sectie maakt u twee virtuele machines in uw VNet, VM1 en VM2. In het netwerk diagram gebruiken we 10.18.0.4 en 10.18.0.5. Bij het configureren van uw Vm's, moet u ervoor zorgen dat u het virtuele netwerk selecteert dat u hebt gemaakt (op het tabblad netwerk). Zie [Snelstartgids: een virtuele machine maken](../virtual-machines/windows/quick-create-portal.md)voor stappen voor het maken van een virtuele machine.

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>De virtuele hub beveiligen

Een standaard virtuele hub heeft geen ingebouwd beveiligings beleid om de resources in spoke Virtual Networks te beveiligen. Een beveiligde virtuele hub maakt gebruik van Azure Firewall of een externe provider voor het beheren van inkomend en uitgaand verkeer om uw resources in azure te beveiligen.

Converteer de hub naar een beveiligde hub met behulp van het volgende artikel: [configureer Azure firewall in een virtuele WAN-hub](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Regels maken voor het beheren en filteren van verkeer

Regels maken waarmee het gedrag van Azure Firewall wordt bepaald. Door de hub te beveiligen, zorgen we ervoor dat alle pakketten die de virtuele hub invoeren, zijn onderworpen aan de verwerking van de firewall voordat ze toegang krijgen tot uw Azure-resources.

Nadat u deze stappen hebt voltooid, hebt u een architectuur gemaakt waarmee VPN-gebruikers toegang kunnen krijgen tot de virtuele machine met het privé-IP-adres 10.18.0.4, maar **geen** toegang tot de virtuele machine met een privé IP-adres 10.18.0.5

1. Ga in het Azure Portal naar **firewall beheer**.
1. Selecteer **Azure firewall beleid** onder beveiliging.
1. Selecteer **Azure Firewall-beleid maken**.
1. Typ onder **beleids Details** een naam en selecteer de regio waarin uw virtuele hub is geïmplementeerd.
1. Selecteer **Volgende: DNS-instellingen (preview)** .
1. Selecteer **Volgende: Regels**.
1. Op het tabblad **Regels** selecteert u **Een regelverzameling toevoegen**.
1. Geef een naam op voor de verzameling. Stel het type **netwerk** in. Voeg een prioriteits waarde van **100** toe.
1. Vul de naam van de regel, bron type, bron, protocol, doel poort en doel type in, zoals wordt weer gegeven in het onderstaande voor beeld. Selecteer vervolgens **toevoegen**. Met deze regel kan elk IP-adres van de VPN-client groep toegang hebben tot de virtuele machine met het privé-IP-adres 10.18.04, maar niet een andere resource die is verbonden met de virtuele hub. Maak de gewenste regels die passen bij uw gewenste regels voor architectuur en machtigingen.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Firewall-regels" :::

1. Selecteer **Volgende: Bedreigingsinformatie**.
1. Selecteer **Volgende: Hubs**.
1. Op het tabblad **Hubs** selecteert u **Virtuele hubs koppelen**.
1. Selecteer de virtuele hub die u eerder hebt gemaakt en selecteer vervolgens **toevoegen**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

Het kan vijf minuten of langer duren voordat dit proces is voltooid.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Verkeer routeren via Azure Firewall

In deze sectie moet u ervoor zorgen dat het verkeer wordt gerouteerd via Azure Firewall.

1. Selecteer in de portal, vanuit **firewall beheer**, de optie **beveiligde virtuele hubs**.
1. Selecteer de virtuele hub die u hebt gemaakt.
1. Onder **Instellingen** selecteert u **Beveiligingsconfiguratie**.
1. Onder **Privéverkeer** selecteert u **Verzenden via Azure Firewall**.
1. Controleer of de VNet-verbinding en het privé verkeer van de filiaal verbinding worden beveiligd door Azure Firewall.
1. Selecteer **Opslaan**.

## <a name="validate"></a><a name="validate"></a>Valideren

Controleer de installatie van de beveiligde hub.

1. Verbinding maken met de **beveiligde virtuele hub** via VPN vanaf uw client apparaat.
1. Ping het IP-adres **10.18.0.4** van de client. Er wordt een antwoord weer geven.
1. Ping het IP-adres **10.18.0.5** van de client. Het is niet mogelijk om een antwoord te zien.

### <a name="considerations"></a>Overwegingen

* Zorg ervoor dat de **tabel met actieve routes** op de beveiligde virtuele hub de volgende hop voor privé verkeer door de firewall heeft. Als u toegang wilt krijgen tot de tabel met actieve routes, gaat u naar de **virtuele-hub** -resource. Onder **connectiviteit** selecteert u **route ring** en selecteert u vervolgens de **juiste routes**. Selecteer vervolgens de **standaard** route tabel.
* Controleer of u regels hebt gemaakt in de sectie [regels maken](#create-rules) . Als deze stappen worden gemist, worden de regels die u hebt gemaakt niet echt gekoppeld aan de hub en worden de route tabel en de pakket stroom niet gebruikt Azure Firewall.

## <a name="next-steps"></a>Volgende stappen

* Zie de [Veelgestelde vragen over virtuele WAN](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie de [Azure firewall Veelgestelde vragen](../firewall/firewall-faq.yml)voor meer informatie over Azure firewall.
