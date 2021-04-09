---
title: Aangepaste fout pagina's voor Azure-toepassing gateway maken
description: In dit artikel wordt beschreven hoe u Application Gateway aangepaste fout pagina's maakt. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 355caeb54f09797ae719f21401ceebb7d53d745a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592717"
---
# <a name="create-application-gateway-custom-error-pages"></a>Application Gateway aangepaste fout pagina's maken

Met Application Gateway kunt u aangepaste foutpagina's maken in plaats van standaardfoutpagina's weer te geven. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken.

U kunt bijvoorbeeld uw eigen onderhouds pagina definiëren als uw webtoepassing niet bereikbaar is. U kunt ook een niet-gemachtigde toegangs pagina maken als er een schadelijke aanvraag wordt verzonden naar een webtoepassing.

Aangepaste fout pagina's worden ondersteund voor de volgende twee scenario's:

- **Onderhouds pagina** : deze aangepaste fout pagina wordt verzonden in plaats van een 502-ongeldige gateway pagina. Deze wordt weer gegeven wanneer Application Gateway geen back-end heeft om verkeer te routeren. Bijvoorbeeld wanneer er sprake is van gepland onderhoud of wanneer een onvoorzien probleem gevolgen heeft voor de back-end-pool.
- **Onbevoegde toegang pagina** : deze aangepaste fout pagina wordt verzonden in plaats van een 403-onbevoegde toegang pagina. Deze wordt weer gegeven wanneer de Application Gateway WAF schadelijk verkeer detecteert en blokkeert.

Als er een fout is opgetreden op de back-endservers, wordt deze door gegeven aan een ongewijzigde back-up naar de aanroeper. Er wordt geen aangepaste fout pagina weer gegeven. Application Gateway kan een aangepaste fout pagina weer geven wanneer een aanvraag de back-end niet kan bereiken.

Aangepaste fout pagina's kunnen worden gedefinieerd op globaal niveau en op het niveau van de listener:

- **Global level** : de fout pagina is van toepassing op verkeer voor alle webtoepassingen die zijn geïmplementeerd op die toepassings gateway.
- **Listener-niveau** : de fout pagina wordt toegepast op verkeer dat is ontvangen op die listener.
- **Beide** : de aangepaste fout pagina die is gedefinieerd op het niveau van de listener, overschrijft de ene set op wereld wijd niveau.

Als u een aangepaste fout pagina wilt maken, hebt u het volgende nodig:

- de status code van een HTTP-antwoord.
- de bijbehorende locatie voor de fout pagina. 
- een openbaar toegankelijke Azure Storage-BLOB voor de locatie.
- een extensie van het type *. htm of *. html. 

De fout pagina moet kleiner zijn dan 1 MB. Als er afbeeldingen zijn gekoppeld aan de fout pagina, moeten deze openbaar toegankelijke absolute Url's of base64-gecodeerde afbeelding inline zijn op de aangepaste fout pagina. Relatieve koppelingen met afbeeldingen op dezelfde BLOB-locatie worden momenteel niet ondersteund. 

Nadat u een fout pagina hebt opgegeven, wordt deze door de toepassings gateway gedownload vanaf de locatie van de opslag-Blob en opgeslagen in de lokale toepassings gateway cache. Vervolgens wordt de fout pagina rechtstreeks vanuit de toepassings gateway bediend. Als u een bestaande aangepaste fout pagina wilt wijzigen, moet u verwijzen naar een andere blob-locatie in de configuratie van de toepassings gateway. De toepassings gateway controleert de BLOB-locatie niet regel matig om nieuwe versies op te halen.

## <a name="portal-configuration"></a>Portal configuratie

1. Navigeer naar Application Gateway in de portal en kies een toepassings gateway.

    ![Scherm afbeelding toont de overzichts pagina voor een toepassings gateway.](media/custom-error/ag-overview.png)
2. Klik op **listeners** en navigeer naar een bepaalde listener waar u een fout pagina wilt opgeven.

    ![Application Gateway listeners](media/custom-error/ag-listener.png)
3. Een aangepaste fout pagina configureren voor een 403 WAF-fout of een 502-onderhouds pagina op het niveau van de listener.

    > [!NOTE]
    > Het maken van aangepaste fout pagina's op globaal niveau vanuit het Azure Portal wordt momenteel niet ondersteund.

4. Geef een openbaar toegankelijke BLOB-URL op voor een bepaalde fout status code en klik op **Opslaan**. De Application Gateway is nu geconfigureerd met de aangepaste fout pagina.

   ![Application Gateway fout codes](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell-configuratie

U kunt Azure PowerShell gebruiken om een aangepaste fout pagina te configureren. Bijvoorbeeld een algemene aangepaste fout pagina:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Of een fout pagina op listener-niveau:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Zie [add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror) en [add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Application Gateway diagnostische gegevens de status van de [back-end, Diagnostische logboeken en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md).
