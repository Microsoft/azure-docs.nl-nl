---
title: Een aangepast domein toevoegen aan uw Azure Front Door Standard/Premium SKU-configuratie
description: In deze zelfstudie leert u hoe u een aangepast domein kunt onboarden voor Azure Front Door Standard/Premium SKU.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 69e216c8893f9361a18354e5d165ecc0499601aa
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387918"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Maak een aangepast domein op Azure Front Door Standard/Premium SKU (preview) met behulp van de Azure Portal

> [!Note]
> Deze documentatie is voor Azure Front Door Standard/Premium (preview). Zoekt u informatie over Azure Front Door? Bekijk [hier](../front-door-overview.md).

Wanneer u Azure Front Door Standard/Premium gebruikt voor het leveren van toepassingen, is een aangepast domein nodig als u wilt dat uw eigen domeinnaam zichtbaar is in uw aanvragen van eindgebruikers. Een zichtbare domeinnaam kan handig zijn voor uw klanten en nuttig zijn voor branding-doelen.

Nadat u een standaard Azure Front Door/Premium-profiel hebt gemaakt, heeft de standaardfront-endhost een subdomein van azurefd.net. Dit subdomein wordt opgenomen in de URL wanneer Azure Front Door Standard/Premium standaard inhoud van uw back-end levert. Bijvoorbeeld `https://contoso-frontend.azurefd.net/activeusers.htm`. Voor uw gemak biedt Azure Front Door de mogelijkheid om een aangepast domein te koppelen met een standaardhost. Met deze optie levert u uw inhoud met een aangepast domein in uw URL in plaats van een Azure Front Door standard/Premium-domeinnaam. Bijvoorbeeld ' https://www.contoso.com/photo.png '.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (preview) is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [**Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten
* Voordat u de stappen in deze zelfstudie kunt voltooien, moet u een Front Door maken. Zie Quickstart: Een Front Door [Standard/Premium maken voor meer informatie.](create-front-door-portal.md)

* Als u nog geen aangepast domein hebt, moet u er eerst een aanschaffen met een domeinprovider. Zie bijvoorbeeld [Een aangepaste domeinnaam kopen](../../app-service/manage-custom-dns-buy-domain.md).

* Als u Azure gebruikt om uw [DNS-domeinen](../../dns/dns-overview.md)te hosten, moet u het domain name system (DNS) van de domeinprovider delegeren naar een Azure DNS. Zie [Delegate a domain to Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) (Een domein aan Azure DNS overdragen) voor meer informatie. Als u een domeinprovider gebruikt om uw DNS-domein af te handelen, moet u het domein handmatig valideren door gevraagde DNS TXT-records in te voegen.

## <a name="add-a-new-custom-domain"></a>Een nieuw aangepast domein toevoegen

> [!NOTE]
> In openbare preview wordt het gebruik Azure DNS apexdomeinen niet ondersteund op Azure Front Door Standard/Premium. Er zijn andere DNS-providers die ondersteuning bieden voor CNAME-afvlakking of DNS-ondersteuning, waardoor APEX-domeinen kunnen worden gebruikt voor Azure Front Door Standard/Premium.

Een aangepast domein wordt beheerd door de sectie Domeinen in de portal. Een aangepast domein kan worden gemaakt en gevalideerd voordat u een eindpunt aan een eindpunt kunt toevoegen. Een aangepast domein en de subdomeinen kunnen slechts aan één eindpunt tegelijk worden gekoppeld. U kunt echter verschillende subdomeinen van hetzelfde aangepaste domein gebruiken voor verschillende Front Doors. U kunt ook aangepaste domeinen met verschillende subdomeinen aan hetzelfde Front Door eindpunt.

1. Onder Instellingen voor uw Azure Front Door selecteert u *Domeinen*  en vervolgens de **knop Een domein** toevoegen.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Schermopname van de knop Domein toevoegen op de landingspagina van het domein.":::

1. De **pagina Een domein** toevoegen wordt weergegeven waar u informatie over het aangepaste domein kunt invoeren. U kunt kiezen voor door Azure beheerde DNS. Dit wordt aanbevolen of u kunt ervoor kiezen om uw eigen DNS-provider te gebruiken. Als u door Azure beheerde DNS kiest, selecteert u een bestaande DNS-zone en selecteert u vervolgens een aangepast subdomein of maakt u een nieuwe. Als u een andere DNS-provider gebruikt, voert u handmatig de aangepaste domeinnaam in. Selecteer **Toevoegen om** uw aangepaste domein toe te voegen.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Schermopname van een domeinpagina toevoegen.":::

    Er wordt een nieuw aangepast domein gemaakt met de validatietoestand **Verzenden.**

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Schermopname van het verzenden van de status van de domeinvalidatie.":::

    Wacht totdat de validatietoestand is gewijzigd in **In behandeling.** Deze bewerking kan enkele minuten duren.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Schermopname van de status van de domeinvalidatie in behandeling.":::

1. Selecteer de **validatietoestand** In behandeling. Er wordt een nieuwe pagina weergegeven met DNS TXT-recordgegevens die nodig zijn om het aangepaste domein te valideren. De TXT-record heeft de vorm `_dnsauth.<your_subdomain>` . Als u een zone Azure DNS gebruikt, selecteert u de knop Toevoegen. Er wordt een nieuwe TXT-record met de weergegeven recordwaarde gemaakt in Azure DNS zone.  Als u een andere DNS-provider gebruikt, maakt u handmatig een nieuwe TXT-record met naam met de `dnsauth.<your_subdomain>` recordwaarde zoals weergegeven op de pagina.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Schermopname van de pagina Aangepast domein valideren.":::

1. Selecteer de vernieuwingsstatus. Zodra het domein is gevalideerd met behulp van de DNS TXT-record, wordt de validatiestatus gewijzigd in **geverifieerd.** Het valideren van deze bewerking kan enkele minuten duren.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Schermopname van aangepast domein geverifieerd.":::

1. Sluit de pagina om terug te keren naar de landingspagina van de lijst met aangepaste domeinen. De inrichtingstoestand van het aangepaste domein moet worden gewijzigd **in Ingericht** en de validatietoestand moet worden gewijzigd in **Goedgekeurd.**

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Schermopname van de inrichtende en goedgekeurde status.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Het aangepaste domein koppelen aan uw Front Door-eindpunt

Nadat u uw aangepaste domein hebt gevalideerd, kunt u het toevoegen aan Azure Front Door Standard/Premium-eindpunt.

1. Zodra het aangepaste domein is gevalideerd, kunt u het koppelen aan een Azure Front Door Standard/Premium-eindpunt en -route. Selecteer de **koppeling Eindpuntkoppeling** om de pagina **Eindpunt en routes koppelen te** openen. Selecteer een eindpunt en routes die u wilt koppelen. Selecteer vervolgens **Koppelen.** Sluit de pagina zodra de bewerking voor koppelen is voltooid.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Schermopname van de pagina Eindpunt en routes koppelen.":::

    De status van de eindpunt-associatie moet worden gewijzigd om het eindpunt weer te geven waaraan het aangepaste domein momenteel is gekoppeld. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Schermopname van koppelingskoppeling naar eindpunt.":::

1. Selecteer de koppeling DNS-status.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Schermopname van de dns-statuskoppeling.":::

1. De **CNAME-recordpagina** toevoegen of bijwerken wordt weergegeven en geeft de CNAME-recordgegevens weer die moeten worden opgegeven voordat het verkeer kan stromen. Als u gehoste Azure DNS gebruikt, kunnen de CNAME-records worden gemaakt door de knop **Toevoegen** op de pagina te selecteren. Als u een andere DNS-provider gebruikt, moet u handmatig de CNAME-recordnaam en -waarde invoeren, zoals wordt weergegeven op de pagina.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Schermopname van CNAME-record toevoegen of bijwerken.":::

1. Zodra de CNAME-record is gemaakt en het aangepaste domein is gekoppeld aan het Azure Front Door eindpunt is voltooid, wordt de verkeersstroom weer stromend.

    > [!NOTE]
    > Als HTTPS is ingeschakeld, kan het inrichten en doorgeven van certificaten enkele minuten duren, omdat de certificaatverlening wordt uitgevoerd naar alle edge-locaties. 

## <a name="verify-the-custom-domain"></a>Het aangepaste domein verifiëren

Nadat u het aangepaste domein hebt gevalideerd en gekoppeld, controleert u of er correct naar het aangepaste domein wordt verwezen naar uw eindpunt.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Schermopname van het gevalideerde en gekoppelde aangepaste domein.":::

Controleer ten laatste of de inhoud van uw toepassing wordt weergegeven met behulp van een browser.

## <a name="next-steps"></a>Volgende stappen

Ga door met de volgende zelfstudie als u wilt leren hoe u HTTPS kunt inschakelen voor uw aangepaste domein.

> [!div class="nextstepaction"]
> [HTTPS inschakelen voor een aangepast domein]()
