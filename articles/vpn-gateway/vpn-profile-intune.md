---
title: Een intune-profiel maken voor Azure VPN-clients
titleSuffix: Azure VPN Gateway
description: Meer informatie over het maken van een aangepast intune-profiel voor het implementeren van Azure VPN-client profielen
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/15/2020
ms.author: cherylmc
ms.openlocfilehash: 63b1d7f95711017feba52cad97f05128d83734da
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655167"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>Een intune-profiel maken voor het implementeren van VPN-client profielen

U kunt profielen voor Azure VPN-clients (Windows 10) implementeren met behulp van Microsoft Intune. Dit artikel helpt u bij het maken van een intune-profiel met aangepaste instellingen.

> [!NOTE]
> Deze methode werkt alleen voor het implementeren van profielen die gebruikmaken van Azure Active Directory of een gemeen schappelijk certificaat voor client verificatie. Als er unieke client certificaten worden gebruikt, moet elke gebruiker het juiste certificaat hand matig selecteren in de Azure VPN-client.
>

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

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="Configuratieprofielen":::
1. Voor **Platform** selecteert u **Windows 10 en hoger**. Selecteer bij **profiel** de optie **aangepast**. Ten slotte selecteert u **Create**.
1. Geef een naam en beschrijving voor het profiel op en selecteer **volgende**.
1. Selecteer **toevoegen** op het tabblad **configuratie-instellingen** .

    * **Naam:** Voer een naam in voor de configuratie.
    * **Beschrijving:** Optionele beschrijving.
    * **oma-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (deze informatie vindt u in het azurevpnconfig.xml-bestand in de <name></name> tag).
    * **Gegevens type:** Teken reeks (XML-bestand).

   Selecteer het mappictogram en kies het bestand dat u in stap 6 in de [XML-](#xml) stappen hebt opgeslagen. Selecteer **Toevoegen**.

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="Configuratie-instellingen" lightbox="./media/create-profile-intune/configuration-settings.png":::
1. Selecteer **Next**.
1. Selecteer onder **toewijzingen** de groep waarnaar u de configuratie wilt pushen. Selecteer vervolgens **Volgende**.
1. Regels voor toepasselijkheid zijn optioneel. Definieer eventuele regels, indien nodig, en selecteer **volgende**.
1. Selecteer op de pagina **controleren en maken** de optie **maken**.

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="Profiel maken":::
1. Uw aangepaste profiel wordt nu gemaakt. Zie [gebruikers-en apparaatprofielen toewijzen](/mem/intune/configuration/device-profile-assign)voor de Microsoft intune stappen voor het implementeren van dit profiel.
 
## <a name="next-steps"></a>Volgende stappen

Zie [about Point-to-site](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site.