---
title: 'Zelfstudie: Bestaande aangepaste DNS-naam toewijzen'
description: Informatie over het toevoegen van een bestaande aangepaste DNS-domeinnaam (vanity-domein) aan een web-app, back-end voor een mobiele app of een API-app in Azure App Service.
keywords: app service, Azure app service, domein toewijzing, domein naam, bestaand domein, hostnaam, Vanity domein
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ca1308c969227336bfb4970f7c5c77b9f2e0cc22
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216527"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Zelfstudie: Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service

Deze zelf studie laat zien hoe u bestaande <abbr title="Een domein naam die u hebt aangeschaft bij een domein registratie, zoals GoDaddy of een subdomein van uw aangeschafte domein.">aangepaste DNS-domein naam</abbr> tot <abbr title="Een HTTP-gebaseerde service voor het hosten van webtoepassingen, REST-Api's en mobiele back-end-toepassingen.">Azure App Service</abbr>.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een subdomein toewijzen met behulp van een <abbr title="Een canonieke DNS-naam record wijst één domein naam aan een andere toe.">CNAME-record</abbr>.
> * Een hoofd domein toewijzen met behulp van een <abbr title="Een adres record in DNS wijst een hostnaam toe aan een IP-adres.">A-record</abbr>.
> * Een wildcard-domein toewijzen met behulp van een CNAME-record.
> * Een standaard-URL omleiden naar een aangepaste map.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

* [Maak een App Service-app](./index.yml), of gebruik een app die u hebt gemaakt voor een andere zelfstudie.
* Zorg ervoor dat u DNS-records voor uw aangepaste domein kunt bewerken. Als u nog geen aangepast domein hebt, kunt u [een app service domein aanschaffen](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Wat heb ik nodig om DNS-records te bewerken?</summary>
        Vereist toegang tot het DNS-REGI ster voor uw domein provider, zoals GoDaddy. Als u bijvoorbeeld DNS-vermeldingen voor <code>contoso.com</code> en <code>www.contoso.com</code> wilt toevoegen, moet u de DNS-instellingen voor het hoofddomein van <code>contoso.com</code> kunnen configureren.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. de app voorbereiden

Om een aangepaste DNS-naam toe te wijzen aan een app, wordt de app <abbr title="Hiermee geeft u de locatie, de grootte en de functies van de webserver farm die als host fungeert voor uw app.">App Service-plan</abbr> moet een betaalde laag zijn (niet <abbr title="Een Azure App Service-laag waarin uw app wordt uitgevoerd op dezelfde Vm's als andere apps, waaronder apps van andere klanten. Deze laag is bedoeld voor ontwikkelen en testen.">**Gratis (F1)**</abbr>). Zie [Azure app service plan Overview](overview-hosting-plans.md)(Engelstalig) voor meer informatie.

#### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

#### <a name="select-the-app-in-the-azure-portal"></a>Selecteer de app in de Azure-portal

1. Zoek en selecteer **App Services**.

   ![Schermopname van de selectie van App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Selecteer op de pagina **App Services** de naam van uw Azure-app.

   ![Schermopname van de portalnavigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    U ziet de beheerpagina van de App Service-app.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

1. Schuif in het linkerdeelvenster op de app-pagina naar de sectie **Instellingen** en selecteer **Omhoog schalen (App Service-plan)** .

   ![Schermopname van het menu Omhoog schalen (App Service-plan).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. De huidige laag van de app wordt gemarkeerd door een blauwe rand. Controleer of de app zich niet in de **F1**-categorie bevindt. Aangepaste DNS wordt niet ondersteund in de **F1**-categorie.

   ![Schermopname met de aanbevolen prijscategorieën.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Als het App Service plan zich niet in de **F1** -laag bevindt, sluit u de pagina **opschalen** en gaat u verder met [3. Een domein verificatie-ID ophalen](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Het App Service-plan opschalen

1. Selecteer een van de lagen die niet gratis zijn (**D1**, **B1**, **B2**, **B3** of een laag in de categorie **Productie**). Selecteer **Aanvullende opties bekijken** voor aanvullende opties.

1. Selecteer **Toepassen**.

   ![Schermopname waarin de controle van de prijscategorie wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

   ![Schermopname waarin de bevestiging van de schaalbewerking wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. een domein verificatie-ID ophalen

Als u een aangepast domein wilt toevoegen aan uw app, moet u verifiëren dat u de eigenaar van het domein bent door een verificatie-id als TXT-record toe te voegen bij uw domeinprovider. 

1. Selecteer **Aangepaste domeinen** in het linkerdeelvenster van de app-pagina. 
1. Kopieer de id in het vak **Verificatie-id aangepast domein** op de pagina **Aangepaste domeinen** voor de volgende stap.

    ![Schermopname met de id in het vak Verificatie-id aangepast domein.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Waarom heb ik dit nodig?</summary>
        Het toevoegen van domeinverificatie-id's aan uw aangepaste domein kan zwevende DNS-vermeldingen voorkomen en overnames van subdomeinen helpen voorkomen. Voor aangepaste domeinen die u eerder hebt geconfigureerd zonder deze verificatie-id, moet u deze beveiligen tegen ditzelfde risico door de verificatie-id toe te voegen aan uw DNS-record. Zie <a href="/azure/security/fundamentals/subdomain-takeover">Subdomein overnemen</a> voor meer informatie over deze veelvoorkomende, zeer kritieke bedreiging.
    </details>
    
<a name="info"></a>

3. **(Alleen een record)** Een toewijzing van een <abbr title="Een adres record in DNS wijst een hostnaam toe aan een IP-adres.">A-record</abbr>, hebt u het externe IP-adres van de app nodig. Kopieer op de pagina **aangepaste domeinen** de waarde van **IP-adres**.

   ![Schermopname van de portalnavigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. de DNS-records maken

1. Meld u aan bij de website van uw domeinprovider.

    <details>
        <summary>Kan ik DNS van mijn domein provider beheren met Azure?</summary>
        Als u wilt, kunt u Azure DNS gebruiken voor het beheren van DNS-records voor uw domein en het configureren van een aangepaste DNS-naam voor Azure App Service. Zie voor meer informatie <a href="https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns">zelf studie: host uw domein in Azure DNS></a>.
    </details>

1. Ga naar de pagina voor het beheren van DNS-records. 

    <details>
        <summary>De pagina Hoe kan ik vinden?</summary>
        <p>Elke domeinprovider heeft zijn eigen interface voor het beheren van DNS-records. Raadpleeg daarom de documentatie van de provider. Doorgaans heeft het sitegedeelte waar u moet zijn, een naam als <strong>Domain Name</strong>, <strong>DNS</strong> of <strong>Name Server Management</strong>.</p>
        <p>Vaak kunt u de pagina met DNS-records vinden door uw accountgegevens te bekijken en te zoeken naar een link als <strong>My domains</strong> (of iets vergelijkbaars). Ga naar de betreffende pagina en kijk of u daar een link ziet zoals <strong>Zone file</strong>, <strong>DNS Records</strong> of <strong>Advanced configuration</strong>.</p>
    </details>

   In de schermafbeelding hieronder wordt een voorbeeld van een pagina met DNS-records weergegeven:

   ![Schermopname met een voorbeeld van de pagina DNS-records.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Selecteer **toevoegen** of de juiste widget om een record te maken. 

1. Selecteer het type record dat u wilt maken en volg de instructies. U kunt een van de twee gebruiken <abbr title="Een canonieke naam record in DNS wijst één domein naam (een alias) aan een andere (de canonieke naam) toe.">CNAME-record</abbr> of een <abbr title="Een adres record in DNS wijst een hostnaam toe aan een IP-adres.">A-record</abbr> een aangepaste DNS-naam toewijzen aan App Service. 

    <details>
        <summary>Welke record moet ik kiezen?</summary>
        <div>
            <ul>
            <li>Als u het hoofd domein wilt toewijzen (bijvoorbeeld <code>contoso.com</code> ), gebruikt u een A-record. Gebruik niet de CNAME-record voor de hoofd record (Zie de Wikipedia- <a href="https://en.wikipedia.org/wiki/CNAME_record">vermelding</a>voor informatie).</li>
            <li>Als u een subdomein wilt toewijzen (bijvoorbeeld <code>www.contoso.com</code> ), gebruikt u een CNAME-record.</li>
            <li>U kunt een subdomein rechtstreeks toewijzen aan het IP-adres van de app met een A-record, maar het is mogelijk dat <a href="https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">het IP-adres wordt gewijzigd</a>. In plaats daarvan wordt de CNAME toegewezen aan de hostnaam van de app. Dit is minder gevoelig voor wijziging.</li>
            <li>Als u een <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">Joker teken domein</a> wilt toewijzen (bijvoorbeeld <code>*.contoso.com</code> ), gebruikt u een CNAME-record.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Voor een subdomein zoals `www` in `www.contoso.com` , maakt u twee records volgens de volgende tabel:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| CNAME | `<subdomain>` (bijvoorbeeld `www` ) | `<app-name>.azurewebsites.net` | De domeintoewijzing zelf. |
| TXT | `asuid.<subdomain>` (bijvoorbeeld `asuid.www` ) | [De verificatie-id die u eerder hebt ontvangen](#3-get-a-domain-verification-id) | De TXT-record `asuid.<subdomain>` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. |

![Schermopname van de portalnavigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Voor een hoofd domein `contoso.com` , zoals, maakt u twee records op basis van de volgende tabel:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| A | `@` | IP-adres uit [Het IP-adres van de app kopiëren](#3-get-a-domain-verification-id) | De domeintoewijzing zelf (`@` vertegenwoordigt doorgaans het hoofddomein). |
| TXT | `asuid` | [De verificatie-id die u eerder hebt ontvangen](#3-get-a-domain-verification-id) | De TXT-record `asuid.<subdomain>` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. Gebruik `asuid` voor het hoofddomein. |

![Schermopname met de pagina DNS-records.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Wat moet ik doen als ik een subdomein met een A-record wil toewijzen?</summary>
Als u een subdomein wilt toewijzen `www.contoso.com` , zoals met een record in plaats van een aanbevolen CNAME-record, moet uw record-en TXT-record eruitzien als in de volgende tabel:

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">Tabel 3</caption>
<thead>
<tr>
<th>Recordtype</th>
<th>Host</th>
<th>Waarde</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td><code>&lt;subdomain&gt;</code> (bijvoorbeeld <code>www</code> )</td>
<td>IP-adres uit <a href="#info" data-linktype="self-bookmark">Het IP-adres van de app kopiëren</a></td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (bijvoorbeeld <code>asuid.www</code> )</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">De verificatie-id die u eerder hebt ontvangen</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Joker teken (CNAME)](#tab/wildcard)

Voor de naam van een Joker teken zoals `*` in `*.contoso.com` , maakt u twee records volgens de volgende tabel:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | De domeintoewijzing zelf. |
| TXT | `asuid` | [De verificatie-id die u eerder hebt ontvangen](#3-get-a-domain-verification-id) | De TXT-record `asuid` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. |

![Schermopname van de navigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Mijn wijzigingen worden gewist nadat ik de pagina verlaat.</summary>
<p>Bij bepaalde providers, zoals GoDaddy, worden wijzigingen in DNS-records pas van kracht wanneer u op een afzonderlijke link <strong>Save Changes</strong> klikt.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. de toewijzing in uw app inschakelen

1. Selecteer **Aangepaste domeinen** in het linkerdeelvenster van de app-pagina in Azure Portal.

    ![Schermopname met het menu Aangepaste domeinen.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van het item Hostnaam toevoegen.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Typ de volledig gekwalificeerde domeinnaam in waarvoor u een CNAME-record zoals `www.contoso.com` heeft toegevoegd.

1. Selecteer **Valideren**. De pagina **Aangepast domein toevoegen** wordt weergegeven.

1. Zorg ervoor dat **Hostnaam recordtype** is ingesteld op **CNAME (www\.example.com of elk subdomein)** . Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van de knop Aangepast domein toevoegen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Het kan even duren voor het nieuwe aangepaste domein wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser om de gegevens bij te werken.

    ![Schermopname van het toevoegen van het CNAME-record.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Wat is het waarschuwings label <strong>niet veilig</strong> ?</summary>
        Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service</a> om een TLS-binding toe te voegen.
    </details>

    Als u een stap hebt gemist of eerder ergens een typefout hebt gemaakt, wordt er een verificatiefoutmelding aan de onderkant van de pagina weergegeven.

    ![Schermopname waarin een verificatiefoutmelding wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Typ de volledig gekwalificeerde domeinnaam in waarvoor u een A-record zoals `contoso.com` heeft toegevoegd. 

1. Selecteer **Valideren**. De pagina **Aangepast domein toevoegen** wordt weergegeven.

1. Zorg ervoor dat **Hostnaam recordtype** is ingesteld op **A-record (voorbeeld.com)** . Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van het toevoegen van een DNS-naam aan de app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Het kan even duren voor het nieuwe aangepaste domein wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser om de gegevens bij te werken.

    ![Schermopname van het toevoegen van een A-record.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Wat is het waarschuwings label <strong>niet veilig</strong> ?</summary>
        Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service</a> om een TLS-binding toe te voegen.
    </details>
    
    Als u een stap hebt gemist of eerder ergens een typefout hebt gemaakt, wordt er een verificatiefoutmelding aan de onderkant van de pagina weergegeven.
    
    ![Schermopname waarin een verificatiefoutmelding wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Joker teken (CNAME)](#tab/wildcard)

3. Typ een Fully Qualified Domain Name die overeenkomt met het Joker teken domein. Voor het voor beeld `*.contoso.com` kunt u bijvoorbeeld `sub1.contoso.com` , `sub2.contoso.com` , `*.contoso.com` of een andere teken reeks gebruiken die overeenkomt met het Joker teken patroon. Selecteer vervolgens **valideren**.

    De knop **Aangepast domein toevoegen** wordt geactiveerd.

1. Zorg ervoor dat **Hostnaam recordtype** is ingesteld op **CNAME-record (www\.example.com of elk subdomein)** . Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van de toevoeging van een DNS-naam aan de app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Het kan even duren voor het nieuwe aangepaste domein wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser om de gegevens bij te werken.

    <details>
        <summary>Wat is het waarschuwings label <strong>niet veilig</strong> ?</summary>
        Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service</a> om een TLS-binding toe te voegen.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. test in een browser

Blader naar de DNS-namen die u eerder hebt geconfigureerd.

![Schermopname van de navigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Ik krijg de fout HTTP 404 (niet gevonden).</summary>
<ul>
<li>Bij het geconfigureerde aangepaste domein ontbreekt een A-record of een CNAME-record.</li>
<li>De browserclient heeft het oude IP-adres van uw domein in de cache. Maak de cache leeg en test DNS-omzetting opnieuw. Op een Windows-computer, leegt u de cache met <code>ipconfig /flushdns</code>.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Een actief domein migreren

Zie voor het zonder downtime migreren van een live site en de DNS-domeinnaam naar App Service [Een actieve DNS-naam migreren naar Azure App Service](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Een aangepaste map omleiden

<details>
<summary>Heb ik dit nodig?</summary>
<p>Dit is afhankelijk van uw app. Standaard stuurt App Service webaanvragen naar de hoofdmap van uw app-code. Maar bepaalde webframeworks&#39;t in de hoofdmap. Bijvoorbeeld: <a href="https://laravel.com/">Laravel</a> start in de submap <code>public</code>. Om door te gaan met het DNS-voorbeeld <code>contoso.com</code>, is een dergelijke app toegankelijk op <code>http://contoso.com/public</code>, maar u moet in plaats daarvan eigenlijk <code>http://contoso.com</code> naar de map <code>public</code> sturen. </p>
</details>

Hoewel dit een veelvoorkomend scenario is, omvat het geen aangepaste domein toewijzing, maar de virtuele map in de App aanpassen.

1. Selecteer **Toepassings instellingen** in het linkerdeel venster van de web-app-pagina.

1. Aan de onderkant van de pagina verwijst de virtuele hoofdmap `/` standaard naar `site\wwwroot`. Dit is de hoofdmap van uw app-code. Wijzig deze om in plaats daarvan bijvoorbeeld te verwijzen naar de `site\wwwroot\public` en sla de wijzigingen op.

    ![Schermopname van het aanpassen van een virtuele map.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Nadat de bewerking is voltooid, controleert u of u het pad naar de hoofdmap van de app in de browser (bijvoorbeeld of) hebt door genavigeerd `http://contoso.com` `http://<app-name>.azurewebsites.net` .

<hr/> 

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt het beheer van aangepaste domeinen met scripts automatiseren met behulp van de [Azure CLI](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/).

#### <a name="azure-cli"></a>Azure CLI

De volgende opdracht voegt een geconfigureerde aangepaste DNS-naam toe aan een App Service-app.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Zie voor meer informatie [Een aangepast domein toewijzen aan een web-app](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende opdracht voegt een geconfigureerde aangepaste DNS-naam toe aan een App Service-app.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Zie voor meer informatie [Een aangepast domein toewijzen aan een web-app](scripts/powershell-configure-custom-domain.md).

<hr/> 

## <a name="next-steps"></a>Volgende stappen

Ga door naar de volgende zelfstudie om te leren hoe u een aangepast TLS/SSL-certificaat aan een web-app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)
