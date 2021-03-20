---
title: Een aangepast domein toevoegen aan uw Azure front deur Standard/Premium SKU-configuratie
description: In deze zelf studie leert u hoe u een aangepast domein kunt voorbereiden voor de Azure front deur Standard/Premium SKU.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 36cb5720e409c86fcb4bc1a97863e5d3523cd3bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588746"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Een aangepast domein maken op basis van Azure front deur Standard/Premium SKU (preview) met behulp van de Azure Portal

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Wanneer u Azure front deur Standard/Premium gebruikt voor het leveren van toepassingen, is een aangepast domein nodig als u wilt dat uw eigen domein naam zichtbaar is in de aanvragen van uw eind gebruikers. Een zichtbare domeinnaam kan handig zijn voor uw klanten en nuttig zijn voor branding-doelen.

Nadat u een Azure front deur Standard/Premium-profiel hebt gemaakt, heeft de standaard frontend-host een subdomein van azurefd.net. Dit subdomein wordt opgenomen in de URL wanneer de Azure front-deur Standard/Premium standaard inhoud levert van uw back-end. Bijvoorbeeld `https://contoso-frontend.azurefd.net/activeusers.htm`. Voor uw gemak biedt Azure Front Door de mogelijkheid om een aangepast domein te koppelen met een standaardhost. Met deze optie levert u uw inhoud aan met een aangepast domein in uw URL in plaats van een Azure front deur Standard/Premium-domein naam in eigendom. Bijvoorbeeld https://www.contoso.com/photo.png.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [**Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten
* Voordat u de stappen in deze zelfstudie kunt voltooien, moet u een Front Door maken. Zie [Quick Start: een voor deur standaard/Premium maken](create-front-door-portal.md)voor meer informatie.

* Als u nog geen aangepast domein hebt, moet u er eerst een aanschaffen bij een domein provider. Zie bijvoorbeeld [Een aangepaste domeinnaam kopen](../../app-service/manage-custom-dns-buy-domain.md).

* Als u Azure gebruikt voor het hosten van uw [DNS-domeinen](../../dns/dns-overview.md), moet u de DNS-naam (Domain Name System) van de domein provider overdragen aan een Azure DNS. Zie [Delegate a domain to Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) (Een domein aan Azure DNS overdragen) voor meer informatie. Als u echter een domein provider gebruikt voor het verwerken van uw DNS-domein, moet u het domein hand matig valideren door desgevraagd DNS TXT-records in te voeren.

## <a name="add-a-new-custom-domain"></a>Een nieuw aangepast domein toevoegen

> [!NOTE]
> In de open bare preview-versie wordt het gebruik van Azure DNS voor het maken van Apex-domeinen niet ondersteund in de Azure front-deur standaard/Premium. Er zijn andere DNS-providers die ondersteuning bieden voor CNAME afvlakking of DNS-Chasing, waarmee APEX-domeinen kunnen worden gebruikt voor de standaard/Premium van Azure front deur.

De sectie een aangepast domein wordt beheerd door domeinen in de portal. Een aangepast domein kan worden gemaakt en gevalideerd vóór de koppeling met een eind punt. Een aangepast domein en de bijbehorende subdomeinen kunnen alleen worden gekoppeld aan één eind punt tegelijk. U kunt echter verschillende subdomeinen van hetzelfde aangepaste domein gebruiken voor verschillende front deuren. U kunt ook aangepaste domeinen met verschillende subdomeinen toewijzen aan hetzelfde eind punt van de voor deur.

1. Onder instellingen voor het profiel voor de Azure-front-deur selecteert u *domeinen*  en vervolgens de knop **een domein toevoegen** .

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Scherm opname van de knop domein toevoegen op de domein landings pagina.":::

1. De pagina **een domein toevoegen** wordt weer gegeven, waar u informatie kunt invoeren over het aangepaste domein. U kunt door Azure beheerde DNS kiezen. dit wordt aanbevolen of u kunt ervoor kiezen om uw eigen DNS-provider te gebruiken. Als u door Azure beheerde DNS kiest, selecteert u een bestaande DNS-zone en selecteert u vervolgens een aangepast subdomein of maakt u een nieuwe. Als u een andere DNS-provider gebruikt, voert u hand matig de aangepaste domein naam in. Selecteer **toevoegen** om uw aangepaste domein toe te voegen.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Scherm opname van de pagina een domein toevoegen.":::

    Er wordt een nieuw aangepast domein gemaakt met de validatie status **verzenden**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Scherm opname van domein validatie status verzenden.":::

    Wacht tot de validatie status is gewijzigd in **in behandeling**. Deze bewerking kan enkele minuten duren.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Scherm opname van domein validatie status in behandeling.":::

1. Selecteer de validatie status **in behandeling** . Er wordt een nieuwe pagina weer gegeven met de DNS TXT-record gegevens die nodig zijn om het aangepaste domein te valideren. De TXT-record bevindt zich in de vorm van `_dnsauth.<your_subdomain>` . Als u gebruikmaakt van Azure DNS zone, selecteert u de knop **toevoegen** en wordt er een nieuwe TXT-record met de weer gegeven record waarde gemaakt in de zone Azure DNS. Als u een andere DNS-provider gebruikt, maakt u hand matig een nieuwe TXT-record met `dnsauth.<your_subdomain>` de naam met de record waarde zoals op de pagina wordt weer gegeven.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Scherm afbeelding van de pagina aangepast domein valideren.":::

1. Selecteer de status vernieuwen. Zodra het domein is gevalideerd met behulp van de DNS TXT-record, wordt de validatie status gewijzigd in **gecontroleerd**. Het valideren van deze bewerking kan enkele minuten duren.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Scherm opname van aangepast domein gecontroleerd.":::

1. Sluit de pagina om terug te gaan naar de landings pagina voor de lijst met aangepaste domeinen. De inrichtings status van het aangepaste domein moet worden gewijzigd in **ingericht** en de validatie status moet worden gewijzigd in **goedgekeurd**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Scherm opname van ingerichte en goedgekeurde status.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Het aangepaste domein koppelen aan het eind punt van de voor deur

Nadat u uw aangepaste domein hebt gevalideerd, kunt u het toevoegen aan uw Azure front-deur Standard/Premium-eind punt.

1. Zodra het aangepaste domein is gevalideerd, kunt u het koppelen aan een bestaand Azure front-deur standaard/Premium-eind punt en-route. Selecteer de koppeling **eind punt koppeling** om de pagina **eind punt koppelen en routes** te openen. Selecteer een eind punt en routes die u wilt koppelen aan. Selecteer vervolgens **koppelen**. Sluit de pagina zodra de koppelings bewerking is voltooid.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Scherm opname van de pagina eind punt koppelen en routes.":::

    De status van de eind punt koppeling moet worden gewijzigd om het eind punt weer te spie gelen waaraan het aangepaste domein momenteel is gekoppeld. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Scherm opname van koppeling van eind punt koppeling.":::

1. Selecteer de koppeling voor de DNS-status.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Scherm opname van de koppeling van de DNS-status.":::

1. De pagina **CNAME-record toevoegen of bijwerken** wordt weer gegeven en de CNAME-record gegevens weer geven die moeten worden verstrekt voordat het verkeer kan worden gestart. Als u Azure DNS gehoste zones gebruikt, kunnen de CNAME-records worden gemaakt door de knop **toevoegen** op de pagina te selecteren. Als u een andere DNS-provider gebruikt, moet u hand matig de naam en waarde van de CNAME-record invoeren, zoals op de pagina wordt weer gegeven.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Scherm opname van het toevoegen of bijwerken van een CNAME-record.":::

1. Zodra de CNAME-record wordt gemaakt en het aangepaste domein is gekoppeld aan het eind punt van de Azure front-deur, wordt de verkeers stroom gestart.

    > [!NOTE]
    > Als HTTPS is ingeschakeld, kan het inrichten en door geven van certificaten enkele minuten duren omdat de doorgifte naar alle Edge-locaties wordt uitgevoerd. 

## <a name="verify-the-custom-domain"></a>Het aangepaste domein verifiëren

Nadat u het aangepaste domein hebt gevalideerd en gekoppeld, controleert u of het aangepaste domein correct naar uw eind punt wordt verwezen.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Scherm opname van het gevalideerde en gekoppelde aangepaste domein.":::

Controleer ten slotte of de inhoud van uw toepassing wordt aangeboden via een browser.

## <a name="next-steps"></a>Volgende stappen

Ga door met de volgende zelfstudie als u wilt leren hoe u HTTPS kunt inschakelen voor uw aangepaste domein.

> [!div class="nextstepaction"]
> [HTTPS inschakelen voor een aangepast domein]()
