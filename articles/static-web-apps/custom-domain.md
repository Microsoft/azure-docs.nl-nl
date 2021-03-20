---
title: Een aangepast domein instellen in de statische Azure-Web Apps
description: Meer informatie over het toewijzen van een aangepast domein aan Azure static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92173687"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Een aangepast domein instellen in de preview-versie van Azure Static Web Apps

Standaard biedt Azure static Web Apps een automatisch gegenereerde domein naam. In dit artikel wordt beschreven hoe u een aangepaste domein naam toewijst aan een statische Azure Web Apps-toepassing.

## <a name="prerequisites"></a>Vereisten

- Een aangeschafte domein naam
- Toegang tot de eigenschappen van de DNS-configuratie voor uw domein

Bij het configureren van domein namen worden A-records gebruikt om hoofd domeinen (bijvoorbeeld, `example.com` ) toe te wijzen aan een IP-adres. Hoofd domeinen moeten rechtstreeks aan een IP-adres worden toegewezen omdat de DNS-specificatie geen toewijzing van het ene domein naar het andere toestaat.

## <a name="dns-configuration-options"></a>Opties voor DNS-configuratie

Er zijn een aantal verschillende typen DNS-configuraties beschikbaar voor een toepassing.

| Als u wilt | Kies |
|--|--|
| Ondersteuning `www.example.com` of `blog.example.net` | [Een CNAME-record toewijzen](#map-a-cname-record) |
| Voor `example.com` | [Een hoofd domein configureren](#configure-a-root-domain) |
| Alle subdomeinen naar `www.example.com` | [Een Joker teken toewijzen](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>Een CNAME-record toewijzen

Een CNAME-record wijst één domein aan een andere toe. U kunt een CNAME-record gebruiken om `www.example.com` , `blog.example.com` of een ander subdomein, toe te wijzen aan het automatisch gegenereerde domein dat wordt geleverd door Azure static web apps.

1. Open de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. **Statische web apps** zoeken en selecteren

1. Selecteer op de pagina _statische web apps_ de naam van uw app.

1. Klik op **aangepaste domeinen** in het menu.

1. Klik op de knop **toevoegen**

1. In het venster _aangepaste domeinen_ kopieert u de URL in het veld **waarde** .

### <a name="configure-dns-provider"></a>DNS-provider configureren

1. Meld u aan bij de website van uw domeinprovider.

2. Ga naar de pagina voor het beheren van DNS-records. Elke domeinprovider heeft zijn eigen interface voor het beheren van DNS-records. Raadpleeg daarom de documentatie van de provider. Doorgaans heeft het sitegedeelte waar u moet zijn, een naam als **Domain Name**, **DNS** of **Name Server Management**.

3. Vaak kunt u de pagina met DNS-records vinden door uw account gegevens weer te geven en vervolgens naar een koppeling zoals **mijn domeinen** te zoeken. Ga naar deze pagina en zoek naar een koppeling met de naam vergelijkbaar met **zone bestand**, **DNS-records** of **Geavanceerde configuratie**.

    In de schermafbeelding hieronder wordt een voorbeeld van een pagina met DNS-records weergegeven:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Voor beeld van een DNS-provider configuratie":::

4. Maak een nieuwe **CNAME** -record met de volgende waarden...

    | Instelling             | Waarde                     |
    | ------------------- | ------------------------- |
    | Type                | CNAME                     |
    | Host                | www                       |
    | Waarde               | Plakken vanaf het klem bord |
    | TTL (indien van toepassing) | De standaard waarde laten staan    |

5. Sla de wijzigingen op met uw DNS-provider.

### <a name="validate-cname"></a>CNAME valideren

1. Ga terug naar het venster _aangepaste domeinen_ in de Azure Portal.

1. Voer uw domein in, inclusief het `www` gedeelte in het gedeelte _aangepast domein valideren_ .

1. Klik op de knop **valideren** .

Nu het aangepaste domein is geconfigureerd, kan het enkele uren duren voordat de DNS-provider de wijzigingen wereld wijd heeft door gegeven. U kunt de status van de doorgifte controleren door naar [dnspropagation.net](https://dnspropagation.net)te gaan. Voer uw aangepaste domein `www` in, inclusief de, Selecteer CNAME in de vervolg keuzelijst en selecteer **starten**.

Als uw DNS-wijzigingen zijn ingevuld, retourneert de website de automatisch gegenereerde URL van uw statische web-app (bijvoorbeeld _Random-name-123456789c.azurestaticapps.net_).

## <a name="configure-a-root-domain"></a>Een hoofd domein configureren

Hoofd domeinen zijn uw domein min elk subdomein, met inbegrip van `www` . Bijvoorbeeld: het hoofd domein voor `www.example.com` is `example.com` . Dit wordt ook wel een ' APEX ' domein genoemd.

Hoewel ondersteuning voor het hoofd domein niet beschikbaar is tijdens de preview, ziet u het blog bericht [basis domeinen configureren in azure Static web apps](https://burkeholland.github.io/posts/static-app-root-domain) voor meer informatie over het configureren van ondersteuning voor het hoofd domein met een statische web-app.

## <a name="map-a-wildcard-domain"></a>Een wildcard-domein toewijzen

Soms wilt u al het verkeer dat naar een subdomein wordt verzonden, naar een ander domein sturen. Een veelvoorkomend voor beeld is het toewijzen van elk subdomein-verkeer aan `www.example.com` . Op deze manier, zelfs als iemand typen `w.example.com` in plaats van `www.example.com` , wordt de aanvraag verzonden naar `www.example.com` .

### <a name="configure-dns-provider"></a>DNS-provider configureren

1. Meld u aan bij de website van uw domeinprovider.

2. Ga naar de pagina voor het beheren van DNS-records. Elke domeinprovider heeft zijn eigen interface voor het beheren van DNS-records. Raadpleeg daarom de documentatie van de provider. Doorgaans heeft het sitegedeelte waar u moet zijn, een naam als **Domain Name**, **DNS** of **Name Server Management**.

3. Vaak kunt u de pagina met DNS-records vinden door uw account gegevens weer te geven en vervolgens naar een koppeling zoals **mijn domeinen** te zoeken. Ga naar deze pagina en zoek vervolgens naar een koppeling met de naam vergelijkbaar met **zone bestand**, **DNS-records** of **Geavanceerde configuratie**.

    In de schermafbeelding hieronder wordt een voorbeeld van een pagina met DNS-records weergegeven:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Voor beeld van een DNS-provider configuratie":::

4. Maak een nieuwe **CNAME** -record met de volgende waarden en vervang `www.example.com` deze door de aangepaste domein naam.

    | Instelling | Waarde                  |
    | ------- | ---------------------- |
    | Type    | CNAME                  |
    | Host    | \*                     |
    | Waarde   | www.example.com        |
    | TTL     | De standaard waarde laten staan |

5. Sla de wijzigingen op met uw DNS-provider.

Nu het Joker teken domein is geconfigureerd, kan het enkele uren duren voordat de wijzigingen wereld wijd worden door gegeven. U kunt de status van de doorgifte controleren door naar [dnspropagation.net](https://dnspropagation.net)te gaan. Voer uw domein aangepaste domein in met een wille keurig subdomein (behalve `www` ), Selecteer CNAME in de vervolg keuzelijst en selecteer **starten**.

Als uw DNS-wijzigingen zijn ingevuld, retourneert de website uw aangepaste domein dat is geconfigureerd voor uw statische web-app (bijvoorbeeld `www.example.com` ).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-instellingen configureren](application-settings.md)
