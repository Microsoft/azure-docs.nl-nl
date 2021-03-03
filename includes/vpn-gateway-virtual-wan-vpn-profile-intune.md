---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f64bb0dd0841e89d05a4399db4373a9eaaec48a2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751061"
---
U kunt profielen voor Azure VPN-clients (Windows 10) implementeren met behulp van Microsoft Intune. Dit artikel helpt u bij het maken van een intune-profiel met aangepaste instellingen.

## <a name="prerequisites"></a>Vereisten

* Apparaten zijn al Inge schreven bij intune MDM.
* De Azure VPN-client voor Windows 10 is al geïmplementeerd op de client computer.
* Alleen Windows versie 19H2 of hoger wordt ondersteund.

## <a name="modify-xml"></a><a name="xml"></a>XML wijzigen

In de volgende stappen gebruiken we een voor beeld-XML voor een aangepast OMA-URI-profiel voor intune met de volgende instellingen:

* Automatisch verbinding maken op
* Detectie van vertrouwd netwerk is ingeschakeld.

Zie het artikel over [VPNV2 CSP](/windows/client-management/mdm/vpnv2-csp) voor andere ondersteunde opties.

1. Down load het VPN-profiel vanuit het Azure Portal en pak het *azurevpnconfig.xml* bestand uit in het pakket.
1. Kopieer en plak de onderstaande tekst in een nieuw tekst editor-bestand.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Wijzig de vermelding tussen ```<ServerUrlList>``` en ```</ServerUrlList>``` met de vermelding van het gedownloade profiel (azurevpnconfig.xml). Wijzig de FQDN van ' TrustedNetworkDetection ' zodat deze overeenkomt met uw omgeving.
1. Open het door Azure gedownloade Profiel (azurevpnconfig.xml) en kopieer de volledige inhoud naar het klem bord door de tekst te markeren en op CTRL + C te drukken. 
1. Plak de gekopieerde tekst uit de vorige Step Into het bestand dat u in stap 2 hebt gemaakt tussen de ```<CustomConfiguration>  </CustomConfiguration>``` Tags. Sla het bestand op met een XML-extensie.
1. Noteer de waarde in de ```<name>  </name>``` Tags. Dit is de naam van het profiel. U hebt deze naam nodig bij het maken van het profiel in intune. Sluit het bestand en onthoud de locatie waar het wordt opgeslagen.

## <a name="create-intune-profile"></a>InTune-profiel maken

In deze sectie maakt u een Microsoft Intune profiel met aangepaste instellingen.

1. Meld u aan bij intune en ga naar **apparaten-> configuratie profielen**. Selecteer **+ profiel maken**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-profile.png" alt-text="Configuratieprofielen":::
1. Voor **Platform** selecteert u **Windows 10 en hoger**. Selecteer bij **profiel** de optie **aangepast**. Ten slotte selecteert u **Create**.
1. Geef een naam en beschrijving voor het profiel op en selecteer **volgende**.
1. Selecteer **toevoegen** op het tabblad **configuratie-instellingen** .

    * **Naam:** Voer een naam in voor de configuratie.
    * **Beschrijving:** Optionele beschrijving.
    * **oma-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (deze informatie vindt u in het azurevpnconfig.xml-bestand in de <name></name> tag).
    * **Gegevens type:** Teken reeks (XML-bestand).

   Selecteer het mappictogram en kies het bestand dat u in stap 6 in de [XML-](#xml) stappen hebt opgeslagen. Selecteer **Toevoegen**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png" alt-text="Configuratie-instellingen" lightbox="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png":::
1. Selecteer **Next**.
1. Selecteer onder **toewijzingen** de groep waarnaar u de configuratie wilt pushen. Selecteer vervolgens **Volgende**.
1. Regels voor toepasselijkheid zijn optioneel. Definieer eventuele regels, indien nodig, en selecteer **volgende**.
1. Selecteer op de pagina **controleren en maken** de optie **maken**.

    :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/create-profile.png" alt-text="Profiel maken":::
1. Uw aangepaste profiel wordt nu gemaakt. Zie [gebruikers-en apparaatprofielen toewijzen](/mem/intune/configuration/device-profile-assign)voor de Microsoft intune stappen voor het implementeren van dit profiel.