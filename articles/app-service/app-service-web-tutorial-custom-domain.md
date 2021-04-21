---
title: 'Zelfstudie: Bestaande aangepaste DNS-naam toewijzen'
description: Informatie over het toevoegen van een bestaande aangepaste DNS-domeinnaam (vanity-domein) aan een web-app, back-end voor een mobiele app of een API-app in Azure App Service.
keywords: app service, azure app service, domeintoewijzing, domeinnaam, bestaand domein, hostnaam
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 8e310cb0507146eb53c7b55c2aaed492baa79521
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833238"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Zelfstudie: Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze zelfstudie ziet u hoe u een bestaande aangepaste DNS-naam (Domain Name System) toewijst aan App Service.

![Schermopname van Azure Portal-navigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een subdomein toewijzen (bijvoorbeeld `www.contoso.com`) met behulp van een CNAME-record.
> * Een hoofddomein toewijzen (bijvoorbeeld `contoso.com`) met behulp van een A-record.
> * Een wildcard-domein toewijzen (bijvoorbeeld `*.contoso.com`) met behulp van een CNAME-record.
> * Een standaard-URL omleiden naar een aangepaste map.
> * Een domeintoewijzing automatiseren met scripts.

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* [Maak een App Service-app](./index.yml), of gebruik een app die u hebt gemaakt voor een andere zelfstudie.
* Koop een domeinnaam en controleer of u toegang hebt tot het DNS-register voor uw domeinprovider (zoals GoDaddy).

  Als u bijvoorbeeld DNS-vermeldingen voor `contoso.com` en `www.contoso.com` wilt toevoegen, moet u de DNS-instellingen voor het hoofddomein van `contoso.com` kunnen configureren.

  > [!NOTE]
  > Als u geen bestaande domeinnaam heeft, overweeg dan om [via Azure Portal een domein aan te schaffen](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>De app voorbereiden

Om een aangepaste DNS-naam toe te wijzen aan een web-app, moet het [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) van de web-app een betaalde categorie zijn (Shared, Basic, Standard, Premium of Consumption voor Azure Functions). In deze stap zorgt u ervoor dat de App Service-app zich in de ondersteunde prijscategorie bevindt.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

  > [!NOTE]
  > Als u een aangepast domein verwijdert of toevoegt aan uw web-app, wordt uw site opnieuw opgestart.
### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="select-the-app-in-the-azure-portal"></a>Selecteer de app in de Azure-portal

1. Zoek en selecteer **App Services**.

   ![Schermopname van de selectie van App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Selecteer op de pagina **App Services** de naam van uw Azure-app.

   ![Schermopname van de portalnavigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/select-app.png)

U ziet de beheerpagina van de App Service-app.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

1. Schuif in het linkerdeelvenster op de app-pagina naar de sectie **Instellingen** en selecteer **Omhoog schalen (App Service-plan)** .

   ![Schermopname van het menu Omhoog schalen (App Service-plan).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. De huidige laag van de app wordt gemarkeerd door een blauwe rand. Controleer of de app zich niet in de **F1**-categorie bevindt. Aangepaste DNS wordt niet ondersteund in de **F1**-categorie.

   ![Schermopname van de aanbevolen prijscategorie.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Als het App Service-plan zich niet in de **F1**-categorie bevindt, sluit u de pagina **Omhoog schalen** en gaat u door met [Een CNAME-record toewijzen](#map-a-cname-record).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Het App Service-plan opschalen

1. Selecteer een van de lagen die niet gratis zijn (**D1**, **B1**, **B2**, **B3** of een laag in de categorie **Productie**). Selecteer **Aanvullende opties bekijken** voor aanvullende opties.

1. Selecteer **Toepassen**.

   ![Schermopname waarin de controle van de prijscategorie wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

   ![Schermopname waarin de bevestiging van de schaalbewerking wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Een verificatie-id van een domein ophalen

Als u een aangepast domein wilt toevoegen aan uw app, moet u verifiëren dat u de eigenaar van het domein bent door een verificatie-id als TXT-record toe te voegen bij uw domeinprovider. Selecteer **Aangepaste domeinen** in het linkerdeelvenster van de app-pagina. Kopieer de id in het vak **Verificatie-id aangepast domein** op de pagina **Aangepaste domeinen** voor de volgende stap.

![Schermopname met de id in het vak Verificatie-id aangepast domein.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Het toevoegen van domeinverificatie-id's aan uw aangepaste domein kan zwevende DNS-vermeldingen voorkomen en overnames van subdomeinen helpen voorkomen. Voor aangepaste domeinen die u eerder hebt geconfigureerd zonder deze verificatie-id, moet u deze beveiligen tegen ditzelfde risico door de verificatie-id toe te voegen aan uw DNS-record. Zie [Subdomein overnemen](../security/fundamentals/subdomain-takeover.md) voor meer informatie over deze veelvoorkomende, zeer kritieke bedreiging.

## <a name="map-your-domain"></a>Uw domein toewijzen

U kunt ofwel een CNAME-record of een A-record gebruiken voor het toewijzen van een aangepaste DNS-naam aan App Service. Volg de bijbehorende stappen:

- [Een CNAME-record toewijzen](#map-a-cname-record)
- [Een A-record toewijzen](#map-an-a-record)
- [Een wildcard-domein toewijzen (met behulp van een CNAME-record)](#map-a-wildcard-domain)

> [!NOTE]
> U moet CNAME-records gebruiken voor alle aangepaste DNS-namen, behalve voor het hoofddomein (bijvoorbeeld `contoso.com`). Voor hoofddomeinen gebruikt u A-records.

### <a name="map-a-cname-record"></a>Een CNAME-record toewijzen

In het voorbeeld van de zelfstudie voegt u toe een CNAME-record toe voor het subdomein `www` (bijvoorbeeld `www.contoso.com`).

Als u een ander subdomein hebt dan `www`, vervangt u `www` door het subdomein (bijvoorbeeld `sub` als uw aangepaste domein `sub.constoso.com` is).

#### <a name="access-dns-records-with-a-domain-provider"></a>Toegang tot DNS-records via een domeinprovider

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Het CNAME-record maken

Wijs een subdomein toe aan de standaarddomeinnaam van de app (`<app-name>.azurewebsites.net`, waarbij `<app-name>` de naam is van de app). Als u een CNAME-toewijzing voor het subdomein `www` wilt maken, maakt u twee records:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | De domeintoewijzing zelf. |
| TXT | `asuid.www` | [De verificatie-id die u eerder hebt ontvangen](#get-a-domain-verification-id) | De TXT-record `asuid.<subdomain>` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. |

Nadat u de CNAME en TXT-records hebt toegevoegd, lijkt de pagina met DNS-records op het volgende voorbeeld:

![Schermopname van de portalnavigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>De toewijzing van het CNAME-record in Azure inschakelen

1. Selecteer **Aangepaste domeinen** in het linkerdeelvenster van de app-pagina in Azure Portal.

    ![Schermopname van het menu Aangepaste domeinen.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Voeg op de pagina **Aangepaste domeinen** van de app de volledig gekwalificeerde aangepaste DNS-naam (`www.contoso.com`) toe aan de lijst.

1. Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van het item Hostnaam toevoegen.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Typ de volledig gekwalificeerde domeinnaam in waarvoor u een CNAME-record zoals `www.contoso.com` heeft toegevoegd.

1. Selecteer **Valideren**. De pagina **Aangepast domein toevoegen** wordt weergegeven.

1. Zorg ervoor dat **Hostnaam recordtype** is ingesteld op **CNAME (www\.example.com of elk subdomein)** . Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van de knop Aangepast domein toevoegen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Het kan even duren voor het nieuwe aangepaste domein wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser om de gegevens bij te werken.

    ![Schermopname van het toevoegen van het CNAME-record.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md) om een TLS-binding toe te voegen.

    Als u een stap hebt gemist of eerder ergens een typefout hebt gemaakt, wordt er een verificatiefoutmelding aan de onderkant van de pagina weergegeven.

    ![Schermopname waarin een verificatiefoutmelding wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Toewijzen van een A-record

In het voorbeeld van de zelfstudie voegt u toe een A-record toe voor het hoofddomein (bijvoorbeeld `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Het IP-adres van de app kopiëren

Als u een A-record wilt toewijzen, heeft u het externe IP-adres van de app nodig. U vindt dit IP-adres op pagina **Aangepaste domeinen** van de app in Azure Portal.

1. Selecteer **Aangepaste domeinen** in het linkerdeelvenster van de app-pagina in Azure Portal.

   ![Schermopname van het menu Aangepaste domeinen.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Op de pagina **Aangepaste domeinen** kopieert u het IP-adres van de app.

   ![Schermopname van de portalnavigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Toegang tot DNS-records via een domeinprovider

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Een A-record maken

Als u een A-record wilt toewijzen aan een app, meestal aan het hoofddomein, maakt u twee records:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| A | `@` | IP-adres uit [Het IP-adres van de app kopiëren](#info) | De domeintoewijzing zelf (`@` vertegenwoordigt doorgaans het hoofddomein). |
| TXT | `asuid` | [De verificatie-id die u eerder hebt ontvangen](#get-a-domain-verification-id) | De TXT-record `asuid.<subdomain>` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. Gebruik `asuid` voor het hoofddomein. |

> [!NOTE]
> Als u een subdomein (zoals `www.contoso.com`) wilt toevoegen met behulp van een A-record in plaats van een aanbevolen [CNAME-record](#map-a-cname-record), moeten uw A-record en TXT-record eruitzien zoals in de volgende tabel:
>
> | Recordtype | Host | Waarde |
> | - | - | - |
> | A | `www` | IP-adres uit [Het IP-adres van de app kopiëren](#info) |
> | TXT | `asuid.www` | [De verificatie-id die u eerder hebt ontvangen](#get-a-domain-verification-id) |
>

Nadat de records zijn toegevoegd, lijkt de pagina met DNS-records op het volgende voorbeeld:

![Schermopname met de pagina DNS-records.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>De toewijzing van de A-record in de app inschakelen

Voeg op de pagina **Aangepaste domeinen** van de app in Azure Portal de volledig gekwalificeerde aangepaste DNS-naam (bijvoorbeeld `contoso.com`) toe aan de lijst.

1. Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van het toevoegen van een hostnaam.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Typ de volledig gekwalificeerde domeinnaam in waarvoor u een A-record zoals `contoso.com` heeft toegevoegd. 

1. Selecteer **Valideren**. De pagina **Aangepast domein toevoegen** wordt weergegeven.

1. Zorg ervoor dat **Hostnaam recordtype** is ingesteld op **A-record (voorbeeld.com)** . Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van het toevoegen van een DNS-naam aan de app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Het kan even duren voor het nieuwe aangepaste domein wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser om de gegevens bij te werken.

    ![Schermopname van het toevoegen van een A-record.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md) om een TLS-binding toe te voegen.
    
    Als u een stap hebt gemist of eerder ergens een typefout hebt gemaakt, wordt er een verificatiefoutmelding aan de onderkant van de pagina weergegeven.
    
    ![Schermopname waarin een verificatiefoutmelding wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Een wildcard-domein toewijzen

In het voorbeeld van de zelfstudie, wijst u een [wildcard-DNS-naam](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (bijvoorbeeld `*.contoso.com`) toe aan de App Service-app door een CNAME-record toe te voegen.

#### <a name="access-dns-records-with-a-domain-provider"></a>Toegang tot DNS-records via een domeinprovider

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Het CNAME-record maken

Wijs een jokerteken-naam `*` toe aan de standaarddomeinnaam van de app (`<app-name>.azurewebsites.net`, waarbij `<app-name>` de naam is van de app). Maak twee records om de jokerteken-naam toe te wijzen:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | De domeintoewijzing zelf. |
| TXT | `asuid` | [De verificatie-id die u eerder hebt ontvangen](#get-a-domain-verification-id) | De TXT-record `asuid` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. |

Voor het `*.contoso.com` domeinvoorbeeld zal het CNAME-record de naam `*` toewijzen naar `<app-name>.azurewebsites.net`.

Wanneer de CNAME wordt toegevoegd, lijkt de pagina met DNS-records op het volgende voorbeeld:

![Schermopname van de navigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>De toewijzing van het CNAME-record in de app inschakelen

U kunt nu elk subdomein dat overeenkomt met de jokerteken-naam aan de app toevoegen (bijvoorbeeld: `sub1.contoso.com`, `sub2.contoso.com` en `*.contoso.com` komen allen overeen met `*.contoso.com`).

1. Selecteer **Aangepaste domeinen** in het linkerdeelvenster van de app-pagina in Azure Portal.

    ![Schermopname van het menu Aangepaste domeinen.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van het toevoegen van een hostnaam.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Typ een FQDN-naam die overeenkomt met het wildcard-domein (bijvoorbeeld `sub1.contoso.com`), en selecteer vervolgens **Valideren**.

    De knop **Aangepast domein toevoegen** wordt geactiveerd.

1. Zorg ervoor dat **Hostnaam recordtype** is ingesteld op **CNAME-record (www\.example.com of elk subdomein)** . Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van de toevoeging van een DNS-naam aan de app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Het kan even duren voor het nieuwe aangepaste domein wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser om de gegevens bij te werken.

1. Selecteer opnieuw het pictogram **+** om een ander aangepast domein toe te voegen dat overeenkomt met het wildcard-domein. Bijvoorbeeld: voeg `sub2.contoso.com` toe.

    ![Schermopname van het toevoegen van een CNAME-record.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md) om een TLS-binding toe te voegen.
    
## <a name="test-in-a-browser"></a>Testen in een browser

Blader naar de DNS-namen die u eerder hebt geconfigureerd (bijvoorbeeld `contoso.com`, `www.contoso.com`, `sub1.contoso.com` en `sub2.contoso.com`).

![Schermopname van de navigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>404-fout 'Niet gevonden' oplossen

Als u een HTTP 404-fout (niet gevonden) ontvangt wanneer u naar de URL van uw aangepaste domein bladert, controleert u of uw domein wordt opgelost naar het IP-adres van uw app met behulp <a href="https://www.nslookup.io/" target="_blank">van nslookup.io</a>. Zo niet, controleer dan of de A- en CNAME-records correct zijn geconfigureerd met behulp van dezelfde site. Als het IP-adres correct wordt opgelost, maar u nog steeds een 404-fout krijgt, heeft uw browser mogelijk het oude IP-adres van uw domein in de cache opgeslagen. Maak de cache leeg en test DNS-omzetting opnieuw. Op een Windows-computer, leegt u de cache met `ipconfig /flushdns`.

## <a name="migrate-an-active-domain"></a>Een actief domein migreren

Zie voor het zonder downtime migreren van een live site en de DNS-domeinnaam naar App Service [Een actieve DNS-naam migreren naar Azure App Service](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Een aangepaste map omleiden

Standaard stuurt App Service webaanvragen naar de hoofdmap van uw app-code. Bepaalde web-frameworks starten echter niet in de hoofdmap. Bijvoorbeeld: [Laravel](https://laravel.com/) start in de submap `public`. Om door te gaan met het DNS-voorbeeld `contoso.com`, is een dergelijke app toegankelijk op `http://contoso.com/public`, maar u moet in plaats daarvan eigenlijk `http://contoso.com` naar de map `public` sturen. Deze stap heeft geen betrekking op DNS-omzetting, maar gaat over het aanpassen van de virtuele map.

Als u een virtuele map voor Windows-apps wilt aanpassen, selecteert u **Toepassingsinstellingen** in het linkerdeelvenster van de web-app-pagina. 

> [!NOTE]
> Linux-apps hebben deze pagina niet. Zie de taalspecifieke configuratiehandleidingen[(bijvoorbeeld PHP)](configure-language-php.md?pivots=platform-linux#change-site-root)als u de hoofdmap van de site voor Linux-apps wilt wijzigen.

Aan de onderkant van de pagina verwijst de virtuele hoofdmap `/` standaard naar `site\wwwroot`. Dit is de hoofdmap van uw app-code. Wijzig deze om in plaats daarvan bijvoorbeeld te verwijzen naar de `site\wwwroot\public` en sla de wijzigingen op.

![Schermopname van het aanpassen van een virtuele map.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Nadat de bewerking is voltooid, moet uw app de juiste pagina op het hoofdpad (bijvoorbeeld `http://contoso.com`) retourneren.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt het beheer van aangepaste domeinen met scripts automatiseren met behulp van de [Azure CLI](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Azure CLI

De volgende opdracht voegt een geconfigureerde aangepaste DNS-naam toe aan een App Service-app.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Zie voor meer informatie [Een aangepast domein toewijzen aan een web-app](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende opdracht voegt een geconfigureerde aangepaste DNS-naam toe aan een App Service-app.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Zie voor meer informatie [Een aangepast domein toewijzen aan een web-app](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een subdomein toewijzen met behulp van een CNAME-record.
> * Een hoofddomein toewijzen met behulp van een A-record.
> * Een wildcard-domein toewijzen met behulp van een CNAME-record.
> * Een standaard-URL omleiden naar een aangepaste map.
> * Een domeintoewijzing automatiseren met scripts.

Ga door naar de volgende zelfstudie om te leren hoe u een aangepast TLS/SSL-certificaat aan een web-app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)
