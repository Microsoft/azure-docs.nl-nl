---
title: 'Zelfstudie: Bestaande aangepaste DNS-naam toewijzen'
description: Informatie over het toevoegen van een bestaande aangepaste DNS-domeinnaam (vanity-domein) aan een web-app, back-end voor een mobiele app of een API-app in Azure App Service.
keywords: app service, azure app service, domeintoewijzing, domeinnaam, bestaand domein, hostnaam, vanity-domein
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1fcf8c681f4fad65209c27663045d4974be633f7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833239"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Zelfstudie: Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service

In deze zelfstudie ziet u hoe u bestaande <abbr title="Een domeinnaam die u hebt aangeschaft bij een domeinregistrar, zoals GoDaddy, of een subdomein van uw aangeschafte domein.">aangepaste DNS-domeinnaam</abbr> tot <abbr title="Een OP HTTP gebaseerde service voor het hosten van webtoepassingen, REST API's en mobiele back-endtoepassingen.">Azure App Service</abbr>.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een subdomein in kaart brengen met behulp van een <abbr title="Met een canonieke DNS-naamrecord wordt de ene domeinnaam aan de andere toe te voegen.">CNAME-record</abbr>.
> * Een hoofddomein in kaart brengen met behulp van een <abbr title="Een adresrecord in DNS wordt een hostnaam aan een IP-adres.">A-record</abbr>.
> * Een wildcard-domein toewijzen met behulp van een CNAME-record.
> * Een standaard-URL omleiden naar een aangepaste map.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Uw omgeving voorbereiden

* [Maak een App Service-app](./index.yml), of gebruik een app die u hebt gemaakt voor een andere zelfstudie.
* Zorg ervoor dat u DNS-records voor uw aangepaste domein kunt bewerken. Als u nog geen aangepast domein hebt, kunt u een App Service [aanschaffen.](manage-custom-dns-buy-domain.md)

    <details>
        <summary>Wat heb ik nodig om DNS-records te bewerken?</summary>
        Vereist toegang tot het DNS-register voor uw domeinprovider, zoals GoDaddy. Als u bijvoorbeeld DNS-vermeldingen voor <code>contoso.com</code> en <code>www.contoso.com</code> wilt toevoegen, moet u de DNS-instellingen voor het hoofddomein van <code>contoso.com</code> kunnen configureren.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. De app voorbereiden

Als u een aangepaste DNS-naam wilt aan een app, moet u de naam van de app <abbr title="Hiermee geeft u de locatie, grootte en functies op van de webserverfarm die als host voor uw app wordt gebruikt.">App Service-plan</abbr> moet een betaalde laag zijn (niet <abbr title="Een Azure App Service-laag waarin uw app wordt uitgevoerd op dezelfde VM's als andere apps, waaronder apps van andere klanten. Deze laag is bedoeld voor ontwikkeling en testen.">**Gratis (F1)**</abbr>). Zie overzicht van Azure App Service [voor meer informatie.](overview-hosting-plans.md)

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

1. Als het App Service plan zich niet in de **F1-laag,** sluit u de pagina Omhoog schalen en gaat u naar  [3. Haal een domeinverificatie-id op.](#3-get-a-domain-verification-id)

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Het App Service-plan opschalen

1. Selecteer een van de lagen die niet gratis zijn (**D1**, **B1**, **B2**, **B3** of een laag in de categorie **Productie**). Selecteer **Aanvullende opties bekijken** voor aanvullende opties.

1. Selecteer **Toepassen**.

   ![Schermopname waarin de controle van de prijscategorie wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

   ![Schermopname waarin de bevestiging van de schaalbewerking wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Een domeinverificatie-id op halen

Als u een aangepast domein wilt toevoegen aan uw app, moet u verifiëren dat u de eigenaar van het domein bent door een verificatie-id als TXT-record toe te voegen bij uw domeinprovider. 

1. Selecteer **Aangepaste domeinen** in het linkerdeelvenster van de app-pagina. 
1. Kopieer de id in het vak **Verificatie-id aangepast domein** op de pagina **Aangepaste domeinen** voor de volgende stap.

    ![Schermopname met de id in het vak Verificatie-id aangepast domein.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Waarom heb ik deze nodig?</summary>
        Het toevoegen van domeinverificatie-id's aan uw aangepaste domein kan zwevende DNS-vermeldingen voorkomen en overnames van subdomeinen helpen voorkomen. Voor aangepaste domeinen die u eerder hebt geconfigureerd zonder deze verificatie-id, moet u deze beveiligen tegen ditzelfde risico door de verificatie-id toe te voegen aan uw DNS-record. Zie <a href="/azure/security/fundamentals/subdomain-takeover">Subdomein overnemen</a> voor meer informatie over deze veelvoorkomende, zeer kritieke bedreiging.
    </details>
    
<a name="info"></a>

3. **(Alleen een record)** Een <abbr title="Een adresrecord in DNS wordt een hostnaam aan een IP-adres.">A-record</abbr>, hebt u het externe IP-adres van de app nodig. Kopieer op **de pagina Aangepaste** domeinen de waarde van **IP-adres**.

   ![Schermopname van de portalnavigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. De DNS-records maken

1. Meld u aan bij de website van uw domeinprovider.

    <details>
        <summary>Kan ik DNS van mijn domeinprovider beheren met behulp van Azure?</summary>
        Als u wilt, kunt u Azure DNS DNS-records voor uw domein beheren en een aangepaste DNS-naam configureren voor Azure App Service. Zie Zelfstudie: Uw <a href="/azure/dns/dns-delegate-domain-azure-dns">domein hosten in </a>Azure DNS>voor meer Azure DNS>.
    </details>

1. Ga naar de pagina voor het beheren van DNS-records. 

    <details>
        <summary>Hoe kan ik de pagina vinden?</summary>
        <p>Elke domeinprovider heeft zijn eigen interface voor het beheren van DNS-records. Raadpleeg daarom de documentatie van de provider. Doorgaans heeft het sitegedeelte waar u moet zijn, een naam als <strong>Domain Name</strong>, <strong>DNS</strong> of <strong>Name Server Management</strong>.</p>
        <p>Vaak kunt u de pagina met DNS-records vinden door uw accountgegevens te bekijken en te zoeken naar een link als <strong>My domains</strong> (of iets vergelijkbaars). Ga naar de betreffende pagina en kijk of u daar een link ziet zoals <strong>Zone file</strong>, <strong>DNS Records</strong> of <strong>Advanced configuration</strong>.</p>
    </details>

   In de schermafbeelding hieronder wordt een voorbeeld van een pagina met DNS-records weergegeven:

   ![Schermopname met een voorbeeld van de pagina DNS-records.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Selecteer **Toevoegen** of de juiste widget om een record te maken. 

1. Selecteer het type record dat u wilt maken en volg de instructies. U kunt een van beide <abbr title="Een Canonical Name-record in DNS wijs één domeinnaam (een alias) toe aan een andere (de canonieke naam).">CNAME-record</abbr> of een <abbr title="Een adresrecord in DNS wordt een hostnaam aan een IP-adres.">A-record</abbr> om een aangepaste DNS-naam toe te App Service. 

    <details>
        <summary>Welke record moet ik kiezen?</summary>
        <div>
            <ul>
            <li>Als u het hoofddomein wilt toevoegen (bijvoorbeeld <code>contoso.com</code> ), gebruikt u een A-record. Gebruik de CNAME-record niet voor de hoofdrecord (zie de <a href="https://en.wikipedia.org/wiki/CNAME_record">Wikipedia-vermelding voor meer informatie).</a></li>
            <li>Gebruik een CNAME-record om een subdomein toe <code>www.contoso.com</code> te geven (bijvoorbeeld ).</li>
            <li>U kunt een subdomein rechtstreeks met een A-record aan het IP-adres van de app toe te wijsen, maar het is mogelijk dat het <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">IP-adres wijzigt.</a> De CNAME wordt in plaats daarvan toe te staan aan de hostnaam van de app, die minder vatbaar is voor wijziging.</li>
            <li>Gebruik een <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">CNAME-record om</a> een wildcard-domein toe te <code>*.contoso.com</code> geven (bijvoorbeeld ).</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Maak voor een subdomein zoals `www` in twee records op basis van de volgende `www.contoso.com` tabel:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| CNAME | `<subdomain>` (bijvoorbeeld `www` ) | `<app-name>.azurewebsites.net` | De domeintoewijzing zelf. |
| TXT | `asuid.<subdomain>` (bijvoorbeeld `asuid.www` ) | [De verificatie-id die u eerder hebt ontvangen](#3-get-a-domain-verification-id) | De TXT-record `asuid.<subdomain>` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. |

![Schermopname van de portalnavigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Maak voor een hoofddomein `contoso.com` zoals twee records op basis van de volgende tabel:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| A | `@` | IP-adres uit [Het IP-adres van de app kopiëren](#3-get-a-domain-verification-id) | De domeintoewijzing zelf (`@` vertegenwoordigt doorgaans het hoofddomein). |
| TXT | `asuid` | [De verificatie-id die u eerder hebt ontvangen](#3-get-a-domain-verification-id) | De TXT-record `asuid.<subdomain>` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. Gebruik `asuid` voor het hoofddomein. |

![Schermopname met de pagina DNS-records.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Wat moet ik doen als ik een subdomein wil aan een A-record?</summary>
Als u een subdomein zoals wilt toe te wijsen aan een A-record in plaats van een aanbevolen CNAME-record, moeten uw A-record en TXT-record er als volgt `www.contoso.com` uitzien:

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

# <a name="wildcard-cname"></a>[Jokerteken (CNAME)](#tab/wildcard)

Maak voor een jokertekennaam, zoals `*` in , twee records op basis van de volgende `*.contoso.com` tabel:

| Recordtype | Host | Waarde | Opmerkingen |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | De domeintoewijzing zelf. |
| TXT | `asuid` | [De verificatie-id die u eerder hebt ontvangen](#3-get-a-domain-verification-id) | De TXT-record `asuid` wordt geopend met App Service om te verifiëren dat u de eigenaar bent van het aangepaste domein. |

![Schermopname van de navigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Mijn wijzigingen worden gewist nadat ik de pagina heb verlaten.</summary>
<p>Bij bepaalde providers, zoals GoDaddy, worden wijzigingen in DNS-records pas van kracht wanneer u op een afzonderlijke link <strong>Save Changes</strong> klikt.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. De toewijzing inschakelen in uw app

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
        <summary>Wat is er met het <strong>waarschuwingslabel Niet</strong> beveiligd?</summary>
        Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie <a href="/azure/app-service/configure-ssl-bindings">Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service</a> om een TLS-binding toe te voegen.
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
        <summary>Wat is er met het <strong>waarschuwingslabel Niet</strong> beveiligd?</summary>
        Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie <a href="/azure/app-service/configure-ssl-bindings">Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service</a> om een TLS-binding toe te voegen.
    </details>
    
    Als u een stap hebt gemist of eerder ergens een typefout hebt gemaakt, wordt er een verificatiefoutmelding aan de onderkant van de pagina weergegeven.
    
    ![Schermopname waarin een verificatiefoutmelding wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Jokerteken (CNAME)](#tab/wildcard)

3. Typ een volledig gekwalificeerde domeinnaam die overeenkomt met het wildcard-domein. In het voorbeeld kunt u bijvoorbeeld , , of een andere tekenreeks gebruiken die `*.contoso.com` `sub1.contoso.com` overeenkomt met het `sub2.contoso.com` `*.contoso.com` jokertekenpatroon. Selecteer vervolgens **Valideren.**

    De knop **Aangepast domein toevoegen** wordt geactiveerd.

1. Zorg ervoor dat **Hostnaam recordtype** is ingesteld op **CNAME-record (www\.example.com of elk subdomein)** . Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van de toevoeging van een DNS-naam aan de app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Het kan even duren voor het nieuwe aangepaste domein wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser om de gegevens bij te werken.

    <details>
        <summary>Wat is er met het <strong>waarschuwingslabel Niet</strong> beveiligd?</summary>
        Een waarschuwingslabel voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie <a href="/azure/app-service/configure-ssl-bindings">Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service</a> om een TLS-binding toe te voegen.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. Testen in een browser

Blader naar de DNS-namen die u eerder hebt geconfigureerd.

![Schermopname van de navigatie naar een Azure-app.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Ik krijg een HTTP 404-fout (niet gevonden).</summary>
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
<summary>Heb ik deze nodig?</summary>
<p>Dit is afhankelijk van uw app. Standaard stuurt App Service webaanvragen naar de hoofdmap van uw app-code. Bepaalde web-frameworks worden echter&#39;niet in de hoofdmap. Bijvoorbeeld: <a href="https://laravel.com/">Laravel</a> start in de submap <code>public</code>. Om door te gaan met het DNS-voorbeeld <code>contoso.com</code>, is een dergelijke app toegankelijk op <code>http://contoso.com/public</code>, maar u moet in plaats daarvan eigenlijk <code>http://contoso.com</code> naar de map <code>public</code> sturen. </p>
</details>

Hoewel dit een veelvoorkomende situatie is, heeft dit niet echt betrekking op aangepaste domeintoewijzing, maar gaat het om het aanpassen van de virtuele map in uw app.

1. Selecteer **Toepassingsinstellingen** in het linkerdeelvenster van uw web-app-pagina.

1. Aan de onderkant van de pagina verwijst de virtuele hoofdmap `/` standaard naar `site\wwwroot`. Dit is de hoofdmap van uw app-code. Wijzig deze om in plaats daarvan bijvoorbeeld te verwijzen naar de `site\wwwroot\public` en sla de wijzigingen op.

    ![Schermopname van het aanpassen van een virtuele map.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Nadat de bewerking is uitgevoerd, controleert u dit door te navigeren naar het hoofdpad van uw app in de browser (bijvoorbeeld `http://contoso.com` of `http://<app-name>.azurewebsites.net` ).

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
