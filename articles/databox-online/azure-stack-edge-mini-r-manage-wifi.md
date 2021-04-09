---
title: Azure Stack Edge mini-R-Wi-Fi beheer
description: Hierin wordt beschreven hoe u de Azure Portal kunt gebruiken om Wi-Fi op uw Azure Stack rand te beheren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: a2cc0707c344c3ca537795666a3f60f648026596
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043764"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>De lokale web-UI gebruiken voor het beheren van draadloze connectiviteit op uw Azure Stack Edge mini maal R

In dit artikel wordt beschreven hoe u draadloze netwerk verbindingen beheert op uw Azure Stack Edge mini-R-apparaat. U kunt de lokale web-UI op uw Azure Stack Edge mini-R-apparaat gebruiken via om Wi-Fi profielen toe te voegen, te verbinden en te verwijderen.

## <a name="about-wi-fi"></a>Over Wi-Fi

Uw Azure Stack Edge mini-R-apparaat kan worden gebruikt wanneer het netwerk is bekabeld of via een draadloos netwerk. Het apparaat heeft een Wi-Fi poort die moet worden ingeschakeld om het apparaat in staat te stellen verbinding te maken met een draadloos netwerk. 

Uw apparaat heeft vijf poorten, poort 1 t/m poort 4 en een vijfde Wi-Fi poort. Hier volgt een diagram van het back-upvlak van een mini-R-apparaat wanneer er verbinding is met een draadloos netwerk.

![Bekabeling voor wifi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Toevoegen, verbinding maken met Wi-Fi profiel

Voer de volgende stappen uit in de lokale gebruikers interface van uw apparaat om een Wi-Fi profiel toe te voegen en er verbinding mee te maken.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Aan de slag**. Selecteer op de tegel **Netwerk** de optie **Configureren**.  
    
    Er zijn vijf netwerkinterfaces op het fysieke apparaat. Poort 1 en poort 2 zijn 1-Gbps-netwerkinterfaces. Poort 3 en poort 4 zijn beide 10-Gbps-netwerkinterfaces. De vijfde poort is de Wi-Fi-poort. 

    [![Pagina 1, 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Selecteer de Wi-Fi-poort en configureer de poortinstellingen. 
    
    > [!IMPORTANT]
    > We raden u sterk aan een vast IP-adres voor de Wi-Fi-poort te configureren.  

    ![Pagina 2, 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    De pagina **Netwerk** wordt bijgewerkt nadat u de instellingen voor de Wi-Fi-poort hebt toegepast.

    ![Pagina 3, 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. Selecteer **Wi-Fi-profiel toevoegen** en upload uw Wi-Fi-profiel. 

    ![Netwerkinstellingen van Wi-Fi-poort van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Het profiel van een draadloos netwerk bevat de SSID (netwerknaam), wachtwoordsleutel en beveiligingsgegevens waarmee verbinding kan worden gemaakt met een draadloos netwerk. U kunt het Wi-Fi-profiel voor uw omgeving van uw netwerkbeheerder krijgen.

    Zie voor meer informatie over het voorbereiden van uw Wi-Fi profielen [Wi-Fi profielen gebruiken met Azure stack Edge mini-R-apparaten](azure-stack-edge-mini-r-use-wifi-profiles.md).

    ![Netwerkinstellingen van poort 2 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Nadat het profiel is toegevoegd, wordt de lijst met Wi-Fi-profielen bijgewerkt om het nieuwe profiel weer te geven. In het profiel moet de **Verbindingsstatus** worden weergegeven als **Niet-verbonden**. 

    ![Netwerkinstellingen van poort 3 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. Nadat het profiel van het draadloos netwerk is geladen, maakt u verbinding met dit profiel. Selecteer **Verbinding maken met Wi-Fi-profiel**. 

    ![Netwerkinstellingen van poort 4 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Selecteer het Wi-Fi-profiel dat u in de vorige stap hebt toegevoegd en selecteer **Toepassen**. 

    ![Netwerkinstellingen van poort 5 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    De **Verbindingsstatus** moet worden bijgewerkt naar **Verbonden**. De signaalsterkte wordt bijgewerkt om de kwaliteit van het signaal aan te geven. 

    ![Netwerkinstellingen van poort 6 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Als u grote hoeveelheden gegevens wilt overdragen, wordt u aangeraden een bekabelde verbinding te gebruiken in plaats van het draadloze netwerk. 


## <a name="download-wi-fi-profile"></a>Wi-Fi profiel downloaden

U kunt een Wi-Fi profiel downloaden dat u voor de draadloze netwerk verbinding gebruikt.

1. Ga in de lokale web-UI van uw apparaat naar **configuratie > netwerk**. 

2. Selecteer **profiel downloaden** onder Wi-Fi Profiel instellingen. Het Wi-Fi-profiel dat u momenteel gebruikt, moet worden gedownload.


## <a name="delete-wi-fi-profile"></a>Wi-Fi profiel verwijderen

U kunt een Wi-Fi profiel dat u gebruikt voor de verbinding met draadloze netwerken verwijderen.


1. Ga in de lokale web-UI van uw apparaat naar **configuratie > netwerk**. 

2. Selecteer **Wi-Fi profiel verwijderen** onder Wi-Fi Profiel instellingen.

3. Kies op de Blade **Wi-Fi profiel verwijderen** het profiel dat u wilt verwijderen. Selecteer **Toepassen**.


## <a name="configure-cisco-wi-fi-profile"></a>Cisco Wi-Fi-profiel configureren

Hier vindt u een aantal richt lijnen voor het beheren en configureren van een Cisco draadloze controller en een toegangs punt op uw apparaat. 

### <a name="dhcp-bridging-mode"></a>DHCP-bridging-modus

Als u een draadloze Cisco-controller voor uw apparaat wilt gebruiken, moet u DHCP-bridging-modus (Dynamic Host Configuration Protocol) inschakelen op de draadloze LAN-controller (WLC).

Zie de [DHCP-bridging modus](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9)voor meer informatie.

#### <a name="bridging-configuration-example"></a>Voor beeld van bridging-configuratie

Als u de functionaliteit voor DHCP-bridging wilt inschakelen op de controller, moet u de DHCP-proxy functie op de controller uitschakelen. Inschakelen van DHCP-bridging via de opdracht regel:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Als de DHCP-server niet bestaat op hetzelfde laag 2 (L2)-netwerk als de-client, moet de broadcast worden doorgestuurd naar de DHCP-server op de client gateway met behulp van een IP-helper. Dit is een voor beeld van deze configuratie:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

De functie voor DHCP-bridging is een globale instelling, zodat dit van invloed is op alle DHCP-trans acties in de controller. U moet IP-helper-instructies toevoegen aan de bekabelde infra structuur voor alle benodigde VLAN-s (Virtual Local Area Network) op de controller.

### <a name="enable-the-passive-client-for-wlan"></a>De passieve client inschakelen voor WLAN

De functie passieve client inschakelen voor draadloze lokale netwerken (WLAN) op een draadloze Cisco-controller:

* Voor de interface die aan het WLAN is gekoppeld, moet een VLAN-label zijn ingeschakeld.
* Multi cast-VLAN moet zijn ingeschakeld voor het WLAN.
* Het door sturen van GARP moet zijn ingeschakeld op de WLC.

Zie [multi cast VLAN information about multi cast Optimization](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html)(Engelstalig) voor meer informatie.

### <a name="troubleshoot"></a>Problemen oplossen

Als u problemen ondervindt met IP-adres toewijzingen op Vm's die worden uitgevoerd op een Azure Stack Edge mini maal R-apparaat, moeten de bovenstaande configuratie-instellingen in uw netwerk omgeving worden gevalideerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren van Azure stack Edge mini-R-apparaat](azure-stack-edge-mini-r-deploy-prep.md).
