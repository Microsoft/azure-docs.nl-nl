---
title: Azure front deur Standard/Premium-eind punt configureren met eindpunt beheerder
description: In dit artikel wordt beschreven hoe u een eind punt configureert met eindpunt beheerder.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098988"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Een Azure front deur Standard/Premium-eind punt (preview) configureren met eindpunt beheerder

> [!NOTE]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? **[Documenten voor de front-deur van Azure](../front-door-overview.md)** weer geven.

Dit artikel laat u zien hoe u een eind punt kunt maken voor een bestaand Azure front deur Standard/Premium-profiel met eindpunt beheerder.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voordat u een Azure front deur Standard/Premium-eind punt kunt maken met eindpunt beheerder, moet u Mini maal één Azure front deur-profiel hebben gemaakt. Het profiel moet ten minste één of meer Azure front-deur standaard/Premium-eind punten hebben. U kunt meerdere profielen gebruiken om de standaard-en Premium-eind punten van de Azure front-deur te organiseren op basis van Internet domein, webtoepassing of andere criteria. 

Zie [een nieuw Azure front deur Standard/Premium-profiel maken](create-front-door-portal.md)om een Azure front-deur profiel te maken.

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Een nieuw Azure front deur Standard/Premium-eind punt maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure front deur Standard/Premium-profiel.

1. Selecteer **eindpunt beheerder**. Selecteer vervolgens **een eind punt toevoegen** om een nieuw eind punt te maken.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Scherm opname van een eind punt toevoegen via eindpunt beheerder.":::

1. Voer op de pagina **een eind punt toevoegen** in en selecteer de volgende instellingen.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Scherm afbeelding van de pagina een eind punt toevoegen.":::

    | Instellingen | Waarde |
    | -------- | ----- |
    | Naam | Voer een unieke naam in voor het nieuwe Azure front-deur Standard/Premium-eind punt. Deze naam wordt gebruikt om toegang te krijgen tot uw resources in de cache in het domein `<endpointname>.az01.azurefd.net` |
    | Time-out van oorsprong (SEC) | Voer een time-outwaarde in seconden in die door Azure front deur moet worden gewacht voordat de verbinding met de oorsprong een time-out heeft. |
    | Status | Schakel het selectie vakje in om dit eind punt in te scha kelen. |

## <a name="add-domains-origin-group-routes-and-security"></a>Domeinen, oorsprongs groep, routes en beveiliging toevoegen

1. Selecteer **eind punt bewerken** op het eind punt om de route te configureren.

1. Selecteer op de pagina **eind punt bewerken** **+ toevoegen** onder domeinen.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Scherm afbeelding van domein selecteren op eind punt bewerken.":::

### <a name="add-domain"></a>Domein toevoegen

1. Kies op de pagina **domein toevoegen** een domein *uit uw Azure front deur-profiel* koppelen of *een nieuw domein toevoegen*. Voor informatie over het maken van een gloed nieuw domein, Zie [een nieuw Azure front-deur Standard/Premium-aangepast domein maken](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Scherm opname van de pagina een domein toevoegen.":::

1. Selecteer **toevoegen** om het domein aan het huidige eind punt toe te voegen. Het geselecteerde domein moet worden weer gegeven in het deel venster domein.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Scherm opname van domeinen in de domein weergave.":::

### <a name="add-origin-group"></a>Oorsprongs groep toevoegen

1. Selecteer **toevoegen** in de weer gave oorsprongs groepen. De pagina **een oorspronkelijke groep toevoegen** wordt weer gegeven 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Scherm afbeelding van de pagina een originele groep toevoegen":::

1. Voer bij **naam** een unieke naam in voor de nieuwe oorspronkelijke groep

1. Selecteer **een oorsprong toevoegen** om een nieuwe oorsprong aan de huidige groep toe te voegen.
 
#### <a name="health-probes"></a>Status controles
De voor deur verzendt periodieke HTTP/HTTPS-test aanvragen naar elk van uw oorsprong. Test aanvragen bepalen de nabijheid en status van elke oorsprong voor taak verdeling van uw aanvragen voor eind gebruikers. De status test instellingen voor een oorspronkelijke groep bepalen hoe we de status van de app-oorsprong controleren. De volgende instellingen zijn beschikbaar voor de configuratie van de taak verdeling:

> [!WARNING]
> Omdat de voor deur veel Edge-omgevingen wereld wijd heeft, kan het status controlevolume voor uw oorsprong een hoge mate van 25 aanvragen elke minuut tot Maxi maal 1200 aanvragen per minuut hebben, afhankelijk van de frequentie van de Health probe die is geconfigureerd. Met de standaard test frequentie van 30 seconden moet het test volume van uw oorsprong ongeveer 200 aanvragen per minuut bedragen.

* **Status**: Geef op of de status controle moet worden ingeschakeld. Als u één herkomst hebt in uw oorspronkelijke groep, kunt u ervoor kiezen om de status controles uit te scha kelen die de belasting van de back-end van uw toepassing verminderen. Zelfs als u meerdere oorsprongen in de groep hebt, maar slechts één van deze heeft de status ingeschakeld, kunt u de status controles uitschakelen.
   
* **Pad**: de URL die wordt gebruikt voor test aanvragen voor alle oorsprong in deze oorsprongs groep. Als een van uw oorsprongen bijvoorbeeld contoso-westus.azurewebsites.net is en het pad is ingesteld op/probe/test.aspx, worden de front-deur omgevingen, ervan uitgaande dat het protocol is ingesteld op HTTP, status test aanvragen verzonden naar `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protocol**: definieert of de Health probe-aanvragen van de front-deur naar uw oorsprong met het HTTP-of HTTPS-protocol moeten worden verzonden.
   
* **Test methode**: de HTTP-methode die moet worden gebruikt voor het verzenden van status controles. De opties zijn GET of HEAD (standaard).

    > [!NOTE]
    > Voor een lagere belasting en kosten op basis van uw oorsprong raadt u aan de voor deur te gebruiken voor het gebruik van HEAD-aanvragen voor status controles.

* **Interval (in seconden)**: definieert de frequentie van status controles aan uw oorsprong of de intervallen waarin elk van de voor deur omgevingen een sonde verzendt.
   
    >[!NOTE]
    >Voor snellere failovers stelt u het interval in op een lagere waarde. Hoe lager de waarde, hoe hoger het Health Probe-volume dat uw oorsprong ontvangt. Als het interval bijvoorbeeld is ingesteld op 30 seconden met zeggen, 100 front deur Pop's wereld wijd, ontvangt elke back-end ongeveer 200 test aanvragen per minuut.

#### <a name="load-balancing"></a>Taakverdeling
Met de instellingen voor taak verdeling voor de oorspronkelijke groep definieert u hoe de status tests worden geëvalueerd. Deze instellingen bepalen of de back-end in orde of beschadigd is. Ze controleren ook hoe de taak verdeling van verkeer tussen verschillende oorsprongen in de oorspronkelijke groep verloopt. De volgende instellingen zijn beschikbaar voor de configuratie van de taak verdeling:

- **Voorbeeld grootte**. Hiermee wordt aangegeven hoeveel steek proeven van Health tests moeten worden genomen om de status van de oorsprong te evalueren.

- De **voorbeeld grootte is voltooid**. Hiermee definieert u de voorbeeld grootte zoals eerder vermeld, het aantal geslaagde steek proeven dat nodig is om de oorsprong in orde aan te roepen. Stel dat het interval voor de status test voor de voor deur 30 seconden is, de voorbeeld grootte 5 is en een geslaagde voorbeeld grootte is ingesteld op 3. Telkens wanneer we de status controles voor uw oorsprong evalueren, kijken we naar de laatste vijf voor beelden over 150 seconden (5 x 30). Ten minste drie geslaagde tests zijn vereist voor het declareren van de backend als in orde.

- **Latentie gevoeligheid (extra latentie)**. Hiermee definieert u of u de aanvraag wilt verzenden naar de oorsprong binnen het gevoeligheids bereik voor latentie meting of door sturen van de aanvraag naar de dichtstbijzijnde back-end.

Selecteer **toevoegen** om de oorspronkelijke groep toe te voegen aan het huidige eind punt. De oorspronkelijke groep moet worden weer gegeven in het deel venster van de oorspronkelijke groep

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Scherm opname van oorsprongen in de oorspronkelijke groep.":::

### <a name="add-route"></a>Route toevoegen

Selecteer **toevoegen** in de weer gave routes, de pagina **een route toevoegen** wordt weer gegeven. Zie [een nieuwe Azure front-deur route maken](how-to-configure-route.md) voor informatie over het koppelen van het domein en de oorspronkelijke groep.

### <a name="add-security"></a>Beveiliging toevoegen

1. Selecteer **toevoegen** op de beveiligings weergave, de pagina **een WAF-beleid toevoegen** wordt weer gegeven
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Scherm afbeelding van de pagina een WAF-beleid toevoegen.":::

1. **WAF-beleid**: Selecteer een WAF-beleid dat u wilt Toep assen voor het geselecteerde domein binnen dit eind punt. 
  
   Selecteer **nieuwe maken** om een gloed nieuw WAF-beleid te maken.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Scherm opname van een nieuw WAF-beleid maken.":::
   
    **Naam**: Voer een unieke naam in voor het nieuwe WAF-beleid. U kunt dit beleid bewerken met meer configuratie op de pagina Web Application firewall.

    **Domeinen**: Selecteer het domein waarop u het WAF-beleid wilt Toep assen.

1. Selecteer knop **toevoegen** . Het WAF-beleid moet worden weer gegeven in het deel venster beveiliging

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Scherm opname van het WAF-beleid in de beveiligings weergave.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u een eind punt wilt verwijderen wanneer het niet meer nodig is, selecteert u **eind punt verwijderen** aan het einde van de rij van het eind punt. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Scherm opname van het verwijderen van een eind punt.":::

## <a name="next-steps"></a>Volgende stappen

Ga verder met [het toevoegen van een aangepast domein](how-to-add-custom-domain.md)voor meer informatie over aangepaste domeinen.
