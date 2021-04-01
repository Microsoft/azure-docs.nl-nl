---
title: Een aangepaste domein naam kopen
description: Meer informatie over het kopen van een App Service domein en het gebruiken als aangepast domein voor uw app Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704851"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Aangepaste domeinnaam voor Azure App Service kopen

App Service domeinen zijn aangepaste domeinen die rechtstreeks in Azure worden beheerd. Ze maken het eenvoudig om aangepaste domeinen voor [Azure app service](overview.md)te beheren. Deze zelf studie laat zien hoe u een App Service domein kunt kopen en DNS-namen kunt toewijzen aan Azure App Service.

Zie [app service domein toewijzen aan Azure VM of Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage)voor Azure vm of Azure Storage. Zie voor Cloud Services [een aangepaste domein naam configureren voor een Azure-Cloud service](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* [Maak een App Service-app](./index.yml), of gebruik een app die u hebt gemaakt voor een andere zelfstudie. De app moet zich in een open bare Azure-regio bevinden. Op dit moment worden Azure National Clouds niet ondersteund.
* [Verwijder de bestedings limiet voor uw abonnement](../cost-management-billing/manage/spending-limit.md#remove). U kunt geen App Service domeinen kopen met gratis abonnements tegoeden.

## <a name="buy-an-app-service-domain"></a>Een App Service domein kopen

Ga voor prijs informatie over App Service domeinen naar de [pagina met app service prijzen](https://azure.microsoft.com/pricing/details/app-service/windows/) en schuif omlaag naar app service domein.

1. Open de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Zoek en selecteer **app service domeinen** in de zoek balk.

    ![Portal navigatie naar Azure App Service domeinen](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Klik in de weer gave **app service domeinen** op **toevoegen**.

    ![Klik in App Service domeinen op toevoegen](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Selecteer **klikken als u de nieuwere versie van de app service-domeinen ervaring wilt maken**.

    ![App Service domein maken met nieuwe ervaring](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Tabblad Basisbeginselen

1. Configureer op het tabblad **basis beginselen** de instellingen met behulp van de volgende tabel:  

   | Instelling  | Beschrijving |
   | -------- | ----------- |
   | **Abonnement** | Het abonnement dat moet worden gebruikt om het domein te kopen. |
   | **Resourcegroep** | De resource groep waarin het domein moet worden geplaatst. Bijvoorbeeld: de resource groep waarin uw app zich bevindt. |
   | **Domein** | Typ het gewenste domein. Bijvoorbeeld **contoso.com**. Als het gewenste domein niet beschikbaar is, kunt u een keuze uit een lijst met suggesties van beschik bare domeinen selecteren of een ander domein proberen. |

    > [!NOTE]
    > De volgende [domeinen op het hoogste niveau](https://wikipedia.org/wiki/Top-level_domain) worden ondersteund door app service domeinen: _com_, _net_, _co.uk_, _org_, _nl_, in _,_ _org.uk_ en  _co.in_.
    >
    >
    
2. Wanneer u klaar bent, klikt u op **volgende: contact gegevens**.

### <a name="contact-information-tab"></a>Tabblad contact gegevens

1. Geef uw gegevens op zoals vereist door [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) voor de domein registratie. 

    Het is belang rijk dat u alle verplichte velden zo nauw keurig mogelijk invult. Onjuiste gegevens voor contact gegevens kunnen ertoe leiden dat het kopen van het domein mislukt.

1. Wanneer u klaar bent, klikt u op **volgende: Geavanceerd**.

### <a name="advanced-tab"></a>Tabblad Geavanceerd

1. Configureer op het tabblad **Geavanceerd** de optionele instellingen:  

   | Instelling  | Beschrijving |
   | -------- | ----------- |
   | **Automatische verlenging** | Standaard ingeschakeld. Uw App Service domein wordt bij een interval van één jaar geregistreerd. Automatisch vernieuwen zorgt ervoor dat uw domein registratie niet verloopt en dat u het eigendom van het domein behoudt. Voor uw Azure-abonnement wordt automatisch de jaarlijkse registratie kosten in rekening gebracht op het moment van verlenging. Selecteer **uitschakelen** om u af te melden. Als automatische verlenging is uitgeschakeld, kunt u [Dit hand matig vernieuwen](#renew-the-domain). |
   | **Privacybescherming** | Standaard ingeschakeld. Met privacy-beveiliging worden de contact gegevens van uw domein registratie verborgen in de WHOIS-data base. Privacy-bescherming is al opgenomen in de jaarlijkse registratie kosten voor het domein. Selecteer **uitschakelen** om u af te melden. |

2. Wanneer u klaar bent, klikt u op **volgende: Tags**.

### <a name="finish"></a>Voltooien

1. Stel op het tabblad **labels** de labels in die u wilt instellen voor uw app service domein. Labels zijn niet vereist voor het gebruik van App Service domeinen, maar is een [functie in azure waarmee u uw resources kunt beheren](../azure-resource-manager/management/tag-resources.md).

1. Klik op **volgende: bekijken + maken**.

1. Controleer op het tabblad **controleren en maken** de volg orde van uw domein. Klik op **Create** als u klaar bent.

    > [!NOTE]
    > App Service domeinen gebruiken GoDaddy voor domein registratie en Azure DNS om de domeinen te hosten. Naast de jaarlijkse registratie kosten voor het domein, gelden de gebruiks kosten voor Azure DNS. Zie [Azure DNS prijzen](https://azure.microsoft.com/pricing/details/dns/)voor meer informatie.
    >
    >

1. Wanneer de registratie van het domein is voltooid, ziet u de knop **Ga naar resource** . Selecteer deze om de beheer pagina ervan weer te geven.

    ![App Service domein gemaakt. Ga naar resource](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

U bent nu klaar om een App Service-app toe te wijzen aan dit aangepaste domein.

## <a name="prepare-the-app"></a>De app voorbereiden

Om een aangepaste DNS-naam toe te wijzen aan een web-app, moet het [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) van de web-app een betaalde categorie zijn (Shared, Basic, Standard, Premium of Consumption voor Azure Functions). In deze stap zorgt u ervoor dat de App Service-app zich in de ondersteunde prijscategorie bevindt.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigeer naar de app in de Azure portal

1. Zoek in de bovenste zoek balk naar en selecteer **app Services**.

    ![Zoeken naar App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Selecteer de naam van de app.

    ![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/select-app.png)

    U ziet de beheerpagina van de App Service-app.  

### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

1. Schuif in de navigatiebalk links van de app-pagina naar de sectie **Instellingen** en selecteer **Opschalen (App Service-plan)** .

    ![Menu Opschalen](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. De huidige laag van de app wordt gemarkeerd door een blauwe rand. Controleer of de app zich niet in de **F1**-laag bevindt. Aangepaste DNS wordt niet ondersteund in de **F1**-laag. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Scherm afbeelding van het navigatie menu links van de app-pagina met omhoog schalen (App Service plan) geselecteerd.":::

1. Als het App Service plan zich niet in de **F1** -laag bevindt, sluit u de pagina **opschalen** en gaat u verder met [het kopen van het domein](#buy-an-app-service-domain).

### <a name="scale-up-the-app-service-plan"></a>Het App Service-plan opschalen

1. Selecteer een van de lagen die niet gratis zijn (**D1**, **B1**, **B2**, **B3** of een laag in de categorie **Productie**). Klik op **Aanvullende opties bekijken** voor aanvullende opties.

1. Klik op **Toepassen**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Scherm opname van de prijs categorieën voor aangepaste domeinen in de productie categorie met het tabblad productie, B1 plannen en de knop Toep assen gemarkeerd.":::

    Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

    ![Schaalbewerking bevestigen](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>App Service domein toewijzen aan uw app

Het is eenvoudig om een hostnaam in uw App Service domein toe te wijzen aan een App Service-app, zolang deze zich in hetzelfde abonnement bevinden. U wijst het App Service domein of een van de bijbehorende subdomeinen rechtstreeks toe aan uw app, en maakt Azure de benodigde DNS-records voor u.

> [!NOTE]
> Als het domein en de app zich in verschillende abonnementen bevinden, wijst u het App Service domein toe aan de app, net zoals bij het [toewijzen van een extern aangeschaft domein](app-service-web-tutorial-custom-domain.md). In dit geval is Azure DNS de externe domein provider en moet u [de vereiste DNS-records hand matig toevoegen](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Het domein toewijzen

1. Ga in de linkernavigatiebalk van de app-pagina naar het gedeelte **instellingen** en selecteer **aangepaste domeinen**.

    ![Schermopname met het menu Aangepaste domeinen.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selecteer **Aangepast domein toevoegen**.

    ![Schermopname van het item Hostnaam toevoegen.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Typ het App Service domein (zoals **contoso.com**) of een subdomein (bijvoorbeeld **www.contoso.com**) en klik op **valideren**.

    > [!NOTE]
    > Als u een type fout hebt gemaakt in de App Service domein naam, wordt aan de onderkant van de pagina een verificatie fout weer gegeven om u te laten weten dat er enkele DNS-records ontbreken. U hoeft deze records niet hand matig toe te voegen voor een App Service domein. Zorg ervoor dat u de domein naam juist hebt getypt en klik nogmaals op **valideren** .
    >
    > ![Schermopname waarin een verificatiefoutmelding wordt weergegeven.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Accepteer het **record type hostname** en klik op **aangepast domein toevoegen**.

    ![Schermopname van de knop Aangepast domein toevoegen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Het kan even duren voor het nieuwe aangepaste domein wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser om de gegevens bij te werken.

    ![Schermopname van het toevoegen van het CNAME-record.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Een **niet-beveiligd** label voor uw aangepaste domein betekent dat het nog niet is gebonden aan een TLS/SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt afhankelijk van de browser, een fout of waarschuwing. Zie [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md) om een TLS-binding toe te voegen.
    
### <a name="test-the-custom-domain"></a>Het aangepaste domein testen

Als u het aangepaste domein wilt testen, navigeert u ernaar in de browser.

## <a name="renew-the-domain"></a>Het domein vernieuwen

Het App Service domein dat u hebt gekocht, is gedurende één jaar geldig vanaf het moment van aankoop. Het domein is standaard geconfigureerd om automatisch te worden vernieuwd door uw betalings wijze voor het volgende jaar te laden. U kunt de domein naam hand matig verlengen.

Als u automatische verlenging wilt uitschakelen of als u uw domein hand matig wilt verlengen, volgt u de stappen hier.

1. Zoek en selecteer **app service domeinen** in de zoek balk.

    ![Portal navigatie naar Azure App Service domeinen](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Selecteer in de sectie **app service domeinen** het domein dat u wilt configureren.

1. Selecteer **domein vernieuwen** vanuit de linkernavigatiebalk van het domein. Als u het automatisch vernieuwen van uw domein wilt stoppen, selecteert u **uit**. De instelling wordt onmiddellijk van kracht.

    ![Scherm afbeelding met de optie voor het automatisch vernieuwen van uw domein.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Wanneer u de pagina verlaat, negeert u de fout melding ' uw niet-opgeslagen wijzigingen worden genegeerd ' door op **OK** te klikken.
    >

Als u uw domein hand matig wilt verlengen, selecteert u **domein vernieuwen**. Deze knop is echter niet actief tot [90 dagen vóór de verval datum van het domein](#when-domain-expires).

Als uw domein is vernieuwd, ontvangt u binnen 24 uur een e-mail melding.

## <a name="when-domain-expires"></a>Wanneer het domein verloopt

Azure behandelt App Service domeinen die verlopen of verlopen zijn als volgt:

* Als automatische verlenging is uitgeschakeld: 90 dagen vóór het verlopen van het domein, wordt er een e-mail bericht voor het vernieuwen van de e-mail naar u verzonden en wordt de knop voor het vernieuwen van het **domein** geactiveerd in de portal.
* Als automatische verlenging is ingeschakeld: op de dag na de verval datum van uw domein probeert Azure u te factureren voor het vernieuwen van de domein naam.
* Als er een fout optreedt tijdens het automatisch verlengen (bijvoorbeeld als uw kaart in het bestand is verlopen) of als automatische verlenging is uitgeschakeld en u toestaat dat het domein verloopt, ontvangt Azure u een melding van het domein verloop en Parken uw domein naam. U kunt uw domein [hand matig verlengen](#renew-the-domain) .
* Op de dag van de vierde en twaalfde dagen na verloop van tijd stuurt Azure u extra e-mail berichten. U kunt uw domein [hand matig verlengen](#renew-the-domain) . Op de vijfde dag na de verval datum wordt de DNS-omzetting gestopt voor het verlopen domein.
* Op de 19e dag na de verval datum blijft uw domein in de wacht staan, maar wordt het onderhouds tarief in rekening gebracht. U kunt klant ondersteuning aanroepen om uw domein naam te vernieuwen, afhankelijk van eventuele verlengings-en activerings kosten.
* Op de 25e dag na de verval datum brengt Azure uw domein in op veiling met een domeinnaam veiling service. U kunt klant ondersteuning aanroepen om uw domein naam te vernieuwen, afhankelijk van eventuele verlengings-en activerings kosten.
* Op de 30e dag na de verval datum kunt u uw domein niet meer inwisselen.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Aangepaste DNS-records beheren

In Azure worden DNS-records voor een App Service domein beheerd met behulp van [Azure DNS](https://azure.microsoft.com/services/dns/). U kunt DNS-records toevoegen, verwijderen en bijwerken, net als bij een extern aangeschaft domein. Aangepaste DNS-records beheren:

1. Zoek en selecteer **app service domeinen** in de zoek balk.

    ![Portal navigatie naar Azure App Service domeinen](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Selecteer in de sectie **app service domeinen** het domein dat u wilt configureren.

1. Selecteer op de pagina **overzicht** de optie **DNS-records beheren**.

    ![Scherm afbeelding die laat zien waar u toegang tot de DNS-records hebt.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Zie [DNS-zones beheren in de Azure Portal](../dns/dns-operations-dnszones-portal.md)voor meer informatie over het bewerken van DNS-records.

## <a name="cancel-purchase-delete-domain"></a>Aankoop annuleren (domein verwijderen)

Nadat u het App Service-domein hebt aangeschaft, hebt u vijf dagen de aankoop voor een volledige terugbetaling te annuleren. Na vijf dagen kunt u het App Service domein verwijderen, maar kan er geen restitutie worden ontvangen.

1. Zoek en selecteer **app service domeinen** in de zoek balk.

    ![Portal navigatie naar Azure App Service domeinen](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Selecteer in de sectie **app service domeinen** het domein dat u wilt configureren.

1. Selecteer in de linkernavigatiebalk van het domein de optie **hostname bindingen**. De hostname-bindingen van alle Azure-Services worden hier weer gegeven.

    ![Scherm opname van de pagina hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Verwijder elke hostnaam binding door te selecteren **...**  >  **Verwijderen**. Nadat alle bindingen zijn verwijderd, selecteert u **Opslaan**.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. Selecteer in de linkernavigatiebalk van het domein **overzicht**. 

1. Als de annulerings periode op het aangeschafte domein niet is verstreken, selecteert u **aankoop annuleren**. Als dat niet het geval is, ziet u in plaats daarvan de knop **verwijderen** . Als u het domein zonder restitutie wilt verwijderen, selecteert u **verwijderen**.

    ![Scherm afbeelding die laat zien waar u een aangekocht domein verwijdert of annuleert.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Bevestig de bewerking door **Ja** te selecteren.

    Nadat de bewerking is voltooid, wordt het domein vrijgegeven uit uw abonnement en kan iedereen het opnieuw aanschaffen. 

## <a name="direct-default-url-to-a-custom-directory"></a>Standaard-URL naar een aangepaste map omleiden

Standaard stuurt App Service webaanvragen naar de hoofdmap van uw app-code. Als u deze wilt omleiden naar een submap, bijvoorbeeld `public` , raadpleegt [u omleiden naar een aangepaste map](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het koppelen van een aangepast TLS/SSL-certificaat aan App Service.

> [!div class="nextstepaction"]
> [Een aangepaste DNS-naam beveiligen met een TLS-binding in Azure App Service](configure-ssl-bindings.md)
