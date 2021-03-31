---
title: Meerdere sites op Azure Application Gateway hosten
description: Dit artikel bevat een overzicht van de ondersteuning voor meerdere sites van Azure-toepassing gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87474322"
---
# <a name="application-gateway-multiple-site-hosting"></a>Meerdere sites in Application Gateway hosten

Als u meerdere sites host, kunt u meer dan één webtoepassing configureren op dezelfde poort van een toepassings gateway. Dit stelt u in staat om een efficiëntere topologie voor uw implementaties te configureren door maximaal 100 websites toe te voegen aan één toepassingsgateway. Elke website kan worden omgeleid naar een eigen back-endpool. Drie domeinen - contoso.com, fabrikam.com en adatum.com - wijzen bijvoorbeeld naar het IP-adres van de toepassingsgateway. U maakt dat drie listeners voor meerdere sites, en configureert elke listener voor de respectieve poort en protocolinstelling. 

U kunt ook hostnamen met jokertekens definiëren in een listener voor meerdere sites (maximaal vijf hostnamen per listener). Zie [namen van hostnamen in listener](#wildcard-host-names-in-listener-preview)voor meer informatie.

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="Een toepassingsgateway voor meerdere sites maken":::

> [!IMPORTANT]
> Regels worden verwerkt in de volg orde waarin ze worden weer gegeven in de portal voor de V1-SKU. Voor de v2-SKU hebben exacte overeenkomsten een hogere prioriteit. Het is raadzaam om eerst listeners voor meerdere locaties te configureren voordat u een basislistener configureert.  Dit zorgt ervoor dat verkeer naar de juiste back-end wordt geleid. Als een basislistener als eerste wordt weergegeven en overeenkomt met een inkomende aanvraag, wordt deze door die listener verwerkt.

Aanvragen voor `http://contoso.com` worden gerouteerd naar ContosoServerPool en aanvragen voor `http://fabrikam.com` worden gerouteerd naar FabrikamServerPool.

Op dezelfde manier kunt u meerdere subdomeinen van hetzelfde bovenliggende domein hosten op dezelfde toepassings gateway-implementatie. U kunt bijvoorbeeld hosten `http://blog.contoso.com` en `http://app.contoso.com` op één toepassings Gateway implementeren.

## <a name="wildcard-host-names-in-listener-preview"></a>Namen van hostnamen in de listener (preview-versie)

Application Gateway staat route ring op basis van een host met behulp van HTTP (S)-listener op meerdere locaties toe. U hebt nu de mogelijkheid joker tekens als sterretje (*) en vraag teken (?) te gebruiken in de hostnaam en Maxi maal vijf hostnamen per multi-site HTTP (S)-listener. Bijvoorbeeld `*.contoso.com`.

Als u een Joker teken in de hostnaam gebruikt, kunt u meerdere hostnamen in één listener vinden. `*.contoso.com`Kan bijvoorbeeld overeenkomen met en `ecom.contoso.com` , enzovoort `b2b.contoso.com` `customer1.b2b.contoso.com` . Als u een matrix met hostnamen gebruikt, kunt u meer dan één hostnaam voor een listener configureren om aanvragen naar een back-end-groep te routeren. Een listener kan bijvoorbeeld bevatten `contoso.com, fabrikam.com` waarmee aanvragen voor beide hostnamen worden geaccepteerd.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="Joker-listener":::

>[!NOTE]
> Deze functie is in Preview en is alleen beschikbaar voor Standard_v2 en WAF_v2 SKU van Application Gateway. Zie hier voor meer informatie over het [gebruik van voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)beelden.

>[!NOTE]
>Deze functie is momenteel alleen beschikbaar via [Azure PowerShell](tutorial-multiple-sites-powershell.md) en [Azure cli](tutorial-multiple-sites-cli.md). Portal ondersteuning is binnenkort beschikbaar.
> Omdat Portal ondersteuning niet volledig beschikbaar is, als u alleen de HostNamen-para meter gebruikt, wordt de listener weer gegeven als een Basic-listener in de portal en de kolom hostnaam in de lijst weergave van de listener geeft niet de hostnamen weer die zijn geconfigureerd. Zorg ervoor dat u Azure PowerShell of CLI gebruikt totdat de listener wordt ondersteund in de portal.

In [Azure PowerShell](tutorial-multiple-sites-powershell.md)moet u in `-HostNames` plaats van gebruiken `-HostName` . Met HostNamen kunt u Maxi maal 5 hostnamen als door komma's gescheiden waarden vermelden en Joker tekens gebruiken. Bijvoorbeeld: `-HostNames "*.contoso.com,*.fabrikam.com"`

In [Azure cli](tutorial-multiple-sites-cli.md)moet u `--host-names` in plaats van gebruiken `--host-name` . Met host-namen kunt u Maxi maal 5 hostnamen als door komma's gescheiden waarden vermelden en Joker tekens gebruiken. Bijvoorbeeld: `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>Toegestane tekens in het veld hostnamen:

* `(A-Z,a-z,0-9)` -alfanumerieke tekens
* `-` -koppel teken of minteken
* `.` -punt als scheidings teken
*   `*` -kan overeenkomen met meerdere tekens in het toegestane bereik
*   `?` -kan overeenkomen met één teken in het toegestane bereik

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>Voor waarden voor het gebruik van joker tekens en meerdere hostnamen in een listener:

*   U kunt Maxi maal 5 hostnamen in één listener vermelden
*   Sterretjes `*` kunnen slechts één keer worden vermeld in een onderdeel van een domein stijl naam of hostnaam. Bijvoorbeeld Component1 *. component2*. component3. `(*.contoso-*.com)` is geldig.
*   De naam van een host mag Maxi maal twee sterretjes bevatten `*` . `*.contoso.*`Is bijvoorbeeld geldig en `*.contoso.*.*.com` is ongeldig.
*   Een hostnaam mag Maxi maal 4 joker tekens bevatten. Zo zijn bijvoorbeeld `????.contoso.com` `w??.contoso*.edu.*` geldig, maar dit `????.contoso.*` is ongeldig.
*   Het gebruik `*` van een sterretje en vraag teken `?` in een onderdeel van een hostnaam `*?` ( `?*` of `**` ) is ongeldig. `*?.contoso.com`En `**.contoso.com` zijn bijvoorbeeld ongeldig.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>Overwegingen en beperkingen van het gebruik van joker tekens of meerdere hostnamen in een listener:

*   [SSL-beëindiging en end-to-end SSL](ssl-overview.md) vereist dat u het protocol CONFIGUREERT als HTTPS en een certificaat uploadt dat moet worden gebruikt in de configuratie van de listener. Als het een multi-site-listener is, kunt u ook de hostnaam invoeren. Dit is meestal de CN van het SSL-certificaat. Wanneer u meerdere hostnamen in de listener opgeeft of joker tekens gebruikt, moet u rekening houden met het volgende:
    *   Als de hostnaam een Joker teken is, zoals *. contoso.com, moet u een certificaat met Joker tekens uploaden met CN als *. contoso.com
    *   Als meerdere hostnamen worden vermeld in dezelfde listener, moet u een SAN-certificaat (alternatieve namen onderwerp) uploaden met de CNs die overeenkomt met de vermelde hostnamen.
*   U kunt geen reguliere expressie gebruiken om de hostnaam te vermelden. U kunt alleen joker tekens als sterretje (*) en vraag teken (?) gebruiken om het patroon van de hostnaam te maken.
*   Voor back-end status controle kunt u niet meerdere [aangepaste tests](application-gateway-probe-overview.md) per http-instellingen koppelen. In plaats daarvan kunt u een van de websites op de back-end testen of "127.0.0.1" gebruiken om de localhost van de back-endserver te testen. Wanneer u echter joker tekens of meerdere hostnamen in een listener gebruikt, worden de aanvragen voor alle opgegeven domein patronen doorgestuurd naar de back-end-groep, afhankelijk van het regel type (op basis of op basis van een pad).
*   De eigenschappen ' hostname ' hebben één teken reeks als invoer, waarbij u slechts één niet-Joker teken domein naam kunt opgeven en ' hostnames ' een matrix van teken reeksen als invoer krijgt, waar u Maxi maal vijf joker tekens domein namen kunt vermelden. De eigenschappen kunnen echter niet tegelijk worden gebruikt.
*   U kunt geen [omleidings](redirect-overview.md) regel maken met een doel-listener waarvoor joker tekens of meerdere hostnamen worden gebruikt.

Zie [meerdere sites maken met behulp van Azure PowerShell](tutorial-multiple-sites-powershell.md) of [Azure CLI gebruiken](tutorial-multiple-sites-cli.md) voor stapsgewijze instructies voor het configureren van hostnamen in een multi-site-listener.

## <a name="host-headers-and-server-name-indication-sni"></a>Hostheaders en Servernaamindicatie (SNI)

Er zijn drie algemene mechanismen om het hosten van meerdere sites in te schakelen op dezelfde infrastructuur.

1. Het is mogelijk meerdere webtoepassingen te hosten, elk op een uniek IP-adres.
2. Gebruik de hostnaam voor het hosten van meerdere webtoepassingen op hetzelfde IP-adres.
3. Gebruik verschillende poorten voor het hosten van meerdere webtoepassingen op hetzelfde IP-adres.

Momenteel Application Gateway ondersteunt één openbaar IP-adres waar het verkeer wordt geluisterd. Meerdere toepassingen, elk met een eigen IP-adres, worden op dit moment niet ondersteund. 

Application Gateway ondersteunt meerdere toepassingen elke die op verschillende poorten luistert, maar dit scenario vereist dat de toepassingen verkeer accepteren op niet-standaard poorten. Dit is vaak geen configuratie die u wilt.

Application Gateway maakt gebruik van HTTP 1.1-hostheaders voor het hosten van meer dan één website op hetzelfde openbare IP-adres en dezelfde poort. De sites die worden gehost op Application Gateway kunnen ook TLS-offload ondersteunen met de Servernaamindicatie (SNI) TLS-extensie. Dit scenario houdt in dat de clientbrowser en back-end-webfarm de HTTP/1.1- en TLS-extensie moeten ondersteunen zoals gedefinieerd in RFC 6066.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van meerdere hosting van sites in Application Gateway
* [Azure Portal gebruiken](create-multiple-sites-portal.md)
* [Azure PowerShell gebruiken](tutorial-multiple-sites-powershell.md) 
* [Azure CLI gebruiken](tutorial-multiple-sites-cli.md)

U kunt ook [Resource Manager-sjabloon met het hosten van meerdere sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) bezoeken voor een end-to-end implementatie op basis van sjablonen.
