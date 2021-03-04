---
title: Een aangepaste DNS beveiligen met een TLS/SSL-binding
description: Beveilig de HTTPS-toegang tot uw aangepaste domein door een TLS/SSL-binding met een certificaat te maken. Verbeter de beveiliging van uw website door HTTPS of TLS 1.2 af te dwingen.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: a713d869a754f17f4030c531999c822a2cbb76ee
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039798"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service

In dit artikel wordt beschreven hoe u de [aangepaste domein](app-service-web-tutorial-custom-domain.md) in uw [App Service-app](./index.yml) of [functie-app](../azure-functions/index.yml) kunt beveiligen door een certificaatbinding te maken. Wanneer u klaar bent, hebt u toegang tot uw App Service-app op het `https://` eindpunt voor uw aangepaste DNS-naam (bijvoorbeeld `https://www.contoso.com`). 

![Web-app met aangepast TLS/SSL-certificaat](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Het beveiligen van een [aangepast domein](app-service-web-tutorial-custom-domain.md) met een certificaat bestaat uit twee stappen:

- [Voeg een persoonlijk certificaat toe aan App Service](configure-ssl-certificate.md) dat voldoet aan alle [vereisten voor persoonlijke certificaten](configure-ssl-certificate.md#private-certificate-requirements).
-  Maak een TLS-binding met het bijbehorende aangepaste domein. Deze tweede stap is opgenomen in dit artikel.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De prijscategorie van uw app upgraden
> * Een aangepast domein beveiligen met een certificaat
> * HTTPS afdwingen
> * TLS 1.1/1.2 afdwingen
> * TLS-beheer automatiseren met scripts

## <a name="prerequisites"></a>Vereisten

Voor het volgen van deze instructiegids:

- [Een App Service-app maken](./index.yml)
- [Een domeinnaam toewijzen aan uw app](app-service-web-tutorial-custom-domain.md) of [een domeinnaam kopen en configureren in Azure](manage-custom-dns-buy-domain.md)
- [Een persoonlijk certificaat toevoegen aan uw app](configure-ssl-certificate.md)

> [!NOTE]
> De eenvoudigste manier om een persoonlijk certificaat toe te voegen, is door [een gratis beheerd certificaat in App Service](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (preview-versie) te maken.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Een aangepast domein beveiligen

Voer de volgende stappen uit:

Selecteer in het linkermenu in <a href="https://portal.azure.com" target="_blank">Azure Portal</a> de optie **App Services** >  **\<app-name>** .

Open vanuit de linkernavigatiebalk van uw app het dialoogvenster **TLS/SSL-binding** door:

- **Aangepaste domeinen** > **Binding toevoegen** te selecteren
- **TLS/SSL-instellingen** > **TLS/SSL-binding toevoegen** te selecteren

![Binding aan domein toevoegen](./media/configure-ssl-bindings/secure-domain-launch.png)

Selecteer in **Aangepast domein** het aangepaste domein waarvoor u een binding wilt toevoegen.

Als uw app al een certificaat voor het geselecteerde aangepaste domein heeft, gaat u direct naar [Binding maken](#create-binding). Anders gaat u verder.

### <a name="add-a-certificate-for-custom-domain"></a>Een certificaat voor een aangepast domein toevoegen

Als uw app geen certificaat heeft voor het geselecteerde aangepaste domein, hebt u twee mogelijkheden:

- **PFX-certificaat uploaden** : Volg de stappen in [Een persoonlijk certificaat uploaden](configure-ssl-certificate.md#upload-a-private-certificate)en selecteer vervolgens deze optie.
- **App Service-certificaat** importeren: Volg de stappen in [Een App Service-certificaat importeren](configure-ssl-certificate.md#import-an-app-service-certificate) en selecteer vervolgens deze optie.

> [!NOTE]
> U kunt ook [Een gratis certificaat maken](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (preview) of [Een Key Vault certificaat importeren](configure-ssl-certificate.md#import-a-certificate-from-key-vault), maar u moet dit afzonderlijk doen en vervolgens terugkeren naar het dialoogvenster **TLS/SSL-binding** .

### <a name="create-binding"></a>Binding maken

Gebruik de volgende tabel om u te helpen de TLS-binding te configureren in het dialoogvenster **TLS/SSL-binding** en klik vervolgens op **Binding toevoegen**.

| Instelling | Beschrijving |
|-|-|
| Aangepast domein | De domeinnaam waaraan u de TLS/SSL-binding wilt toevoegen. |
| Vingerafdruk van het persoonlijke certificaat | Het certificaat dat moet worden gebonden. |
| TLS/SSL-type | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** : Er kunnen meerdere SNI SSL-bindingen worden toegevoegd. Met deze optie kunnen meerdere TLS/SSL-certificaten verschillende domeinen beveiligen op hetzelfde IP-adres. De meeste moderne browsers (waaronder Internet Explorer, Chrome, Firefox en Opera) ondersteunen SNI (Zie [Servernaamindicatie](https://wikipedia.org/wiki/Server_Name_Indication)) voor meer informatie.</li><li>**IP SSL** : Er kan slechts één IP SSL-binding worden toegevoegd. Met deze optie kan slechts één TLS/SSL-certificaat een specifiek openbaar IP-adres beveiligen. Nadat u de binding hebt geconfigureerd, volgt u de stappen in [Records voor IP SSL opnieuw toewijzen](#remap-records-for-ip-ssl).<br/>IP SSL wordt alleen ondersteund in **Standard** of hoger. </li></ul> |

Zodra de bewerking is voltooid, wordt de TLS/SSL-status van het aangepaste domein gewijzigd in **Beveiligd**.

![TLS/SSL-binding geslaagd](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Een **beveiligde** status voor de **aangepaste domeinen** houdt in dat deze zijn beveiligd met een certificaat, maar App Service controleert bijvoorbeeld niet of het certificaat zelfondertekend of verlopen is, waardoor browsers mogelijk een fout of waarschuwing kunnen weergeven.

## <a name="remap-records-for-ip-ssl"></a>Records opnieuw toewijzen voor IP SSL

Als u geen op IP gebaseerde SSL in uw app gebruikt, gaat u naar [HTTPS voor uw aangepaste domein testen](#test-https).

Er zijn mogelijk twee wijzigingen die u moet aanbrengen:

- Uw app maakt standaard gebruik van een gedeeld openbaar IP-adres. Wanneer u een certificaat met IP SSL verbindt, maakt App Service een nieuw, specifiek IP-adres voor uw app. Als u een A-record aan uw app hebt toegewezen, werkt u uw domeinregister bij met dit nieuwe, specifieke IP-adres.

    De pagina **Aangepast domein** van uw app wordt bijgewerkt met het nieuwe, specifieke IP-adres. [Kopieer dit IP-adres](app-service-web-tutorial-custom-domain.md#info) en [wijs de A-record opnieuw toe](app-service-web-tutorial-custom-domain.md#map-an-a-record) aan dit nieuwe IP-adres.

- Als u een SNI SSL binding hebt met `<app-name>.azurewebsites.net`, [kunt u in plaats daarvan een CNAME-toewijzing](app-service-web-tutorial-custom-domain.md#map-a-cname-record) toewijzen aan `sni.<app-name>.azurewebsites.net` (voeg het `sni`-voorvoegsel toe).

## <a name="test-https"></a>HTTPS testen

Zoek in verschillende browsers naar `https://<your.custom.domain>` om te controleren of het naar uw app leidt.

:::image type="content" source="./media/configure-ssl-bindings/app-with-custom-ssl.png" alt-text="Schermopname met een voorbeeld van het bladeren naar uw aangepaste domein met de URL contoso.com gemarkeerd.":::

Uw toepassingscode kan het protocol controleren via de header 'x-appservice-proto'. De header heeft de waarde `http` of `https`. 

> [!NOTE]
> Als uw app certificaatvalidatiefouten geeft, gebruikt u waarschijnlijk een zelfondertekend certificaat.
>
> Als dit niet het geval is, hebt u mogelijk tussenliggende certificaten weggelaten toen u uw certificaat naar het PFX-bestand exporteerde.

## <a name="prevent-ip-changes"></a>IP-wijzigingen voorkomen

Uw inkomende IP-adres kan worden gewijzigd wanneer u een binding verwijdert, zelfs als die binding IP SSL is. Dit is vooral belangrijk wanneer u een certificaat vernieuwt dat zich al in een IP SSL-binding bevindt. Als u een wijziging in het IP-adres van uw app wilt voorkomen, volgt u in volgorde de volgende stappen:

1. Upload het nieuwe certificaat.
2. Verbind het nieuwe certificaat aan het aangepaste domein dat u wilt, zonder het oude certificaat te verwijderen. Met deze actie wordt de oude binding vervangen en niet verwijderd.
3. Verwijder het oude certificaat. 

## <a name="enforce-https"></a>HTTPS afdwingen

Standaard heeft iedereen nog steeds toegang tot uw app via HTTP. U kunt alle HTTP-aanvragen omleiden naar de HTTPS-poort.

Selecteer in het linkernavigatievenster van de app-pagina **SSL-instellingen**. Klik op **Alleen HTTPS** en selecteer **Aan**.

![HTTPS afdwingen](./media/configure-ssl-bindings/enforce-https.png)

Wanneer de bewerking is voltooid, gaat u naar een van de HTTP-URL's die naar uw app verwijzen. Bijvoorbeeld:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS-versies afdwingen

Voor uw app is standaard [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 toegestaan, wat het aanbevolen TLS-niveau is volgens industrienormen zoals [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Als u andere TLS-versies wilt afdwingen, volgt u deze stappen:

Selecteer in het linkernavigatievenster van de app-pagina **SSL-instellingen**. Selecteer vervolgens bij **TLS-versie** de gewenste minimale TLS-versie. Met deze instelling controleert u alleen de inkomende oproepen. 

![TLS 1.1 of 1.2 afdwingen](./media/configure-ssl-bindings/enforce-tls1-2.png)

Als de bewerking is voltooid, worden in de app alle verbindingen met lagere TLS-versies geweigerd.

## <a name="handle-tls-termination"></a>TLS-beëindiging verwerken

In App Service vindt [TLS-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

Taalspecifieke configuratiehandleidingen, zoals de handleiding [Node.js-configuratie voor Linux](configure-language-nodejs.md#detect-https-session), laten zien hoe u een HTTPS-sessie in uw toepassingscode kunt detecteren.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Meer bronnen

* [Een TLS/SSL-certificaat gebruiken in uw code in Azure App Service](configure-ssl-certificate-in-code.md)
* [Veelgestelde vragen: App Service-certificaten](./faq-configuration-and-management.md)