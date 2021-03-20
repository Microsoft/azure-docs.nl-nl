---
title: Een aangepaste test maken met behulp van de portal
titleSuffix: Azure Application Gateway
description: Meer informatie over het maken van een aangepaste test voor Application Gateway met behulp van de portal
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95975952"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Een aangepaste test maken voor Application Gateway met behulp van de portal

> [!div class="op_single_selector"]
> * [Azure-portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

In dit artikel voegt u een aangepaste status test toe aan een bestaande toepassings gateway via de Azure Portal. Azure-toepassing gateway controleert de status van de resources in de back-end-pool met behulp van de status controles.

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen toepassings gateway hebt, gaat u naar [een Application Gateway maken](./quick-create-portal.md) om een toepassings gateway te maken waarmee u kunt werken.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Test maken voor de SKU van Application Gateway v2

Tests worden in een proces in twee stappen geconfigureerd via de portal. De eerste stap is het invoeren van de waarden die nodig zijn voor de test configuratie. In de tweede stap test u de backend-status met behulp van deze test configuratie en slaat u de test op. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Test eigenschappen invoeren

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis proef versie van één maand](https://azure.microsoft.com/free)

2. Klik in het deelvenster Favorieten van Azure Portal op Alle resources. Klik in de Blade alle resources op de toepassings gateway. Als het abonnement dat u hebt geselecteerd, al verschillende resources heeft, kunt u partners.contoso.net invoeren in het vak Filteren op naam… voor eenvoudige toegang tot de toepassingsgateway.

3. Selecteer **status controles** en selecteer **toevoegen** om een nieuwe status test toe te voegen.

   ![Nieuwe test toevoegen][4]

4. Vul op de pagina **status test toevoegen** de vereiste informatie voor de test in en klik op **OK** als u klaar bent.

   |**Instelling** | **Waarde** | **Details**|
   |---|---|---|
   |**Naam**|customProbe|Deze waarde is een beschrijvende naam die wordt gegeven aan de test die toegankelijk is in de portal.|
   |**Protocol**|HTTP of HTTPS | Het protocol dat wordt gebruikt door de status test. |
   |**Host**|dat wil zeggen contoso.com|Deze waarde is de naam van de virtuele host (die afwijkt van de naam van de VM-host) die op de toepassings server wordt uitgevoerd. De test wordt verzonden naar \<protocol\> :// \<host name\> :\<port\>/\<urlPath\>|
   |**Kies een hostnaam uit de back-end-HTTP-instellingen**|Ja of nee|Hiermee stelt u de *host* -header in de test in op de hostnaam van de HTTP-instellingen waaraan deze test is gekoppeld. Speciaal vereist in het geval van back-endservers met meerdere tenants, zoals Azure app service. [Meer informatie](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Poort kiezen uit back-end-HTTP-instellingen**| Ja of nee|Hiermee stelt u de *poort* van de status test in op de poort van de HTTP-instellingen waaraan deze test is gekoppeld. Als u Nee kiest, kunt u een aangepaste doel poort opgeven die u wilt gebruiken |
   |**Poort**| 1-65535 | Aangepaste poort die moet worden gebruikt voor de status controles | 
   |**Pad**|/of een geldig pad|De rest van de volledige URL voor de aangepaste test. Er begint een geldig pad met '/'. Gebruik voor het standaardpad van http: \/ /contoso.com alleen '/' |
   |**Interval (sec.)**|30|Hoe vaak de test wordt uitgevoerd om de status te controleren. Het wordt afgeraden om de lagere dan 30 seconden in te stellen.|
   |**Time-out (SEC)**|30|De hoeveelheid tijd die de test wacht voordat een time-out optreedt. Als er binnen deze time-outperiode geen geldig antwoord wordt ontvangen, wordt de test als mislukt gemarkeerd. Het time-outinterval moet hoog genoeg zijn dat er een http-aanroep kan worden uitgevoerd om ervoor te zorgen dat de status van de back-end beschikbaar is. Houd er rekening mee dat de time-outwaarde niet groter mag zijn dan de interval waarde die is gebruikt in deze test instelling of de waarde van de time-out van de aanvraag in de HTTP-instelling die wordt gekoppeld aan deze test.|
   |**Drempelwaarde voor onjuiste status**|3|Aantal opeenvolgende mislukte pogingen om te worden beschouwd als een slechte status. De drempel waarde kan worden ingesteld op 1 of meer.|
   |**Vergelijkings voorwaarden voor testen gebruiken**|Ja of nee|Standaard wordt een HTTP (S)-antwoord met de status code tussen 200 en 399 als gezond beschouwd. U kunt het acceptabele bereik van back-end-respons code of back-end-antwoord tekst wijzigen. [Meer informatie](./application-gateway-probe-overview.md#probe-matching)|
   |**HTTP-instellingen**|selectie uit vervolg keuzelijst|De test wordt gekoppeld aan de HTTP-instelling (en) die u hier selecteert, waardoor de status van de back-end-groep die is gekoppeld aan de geselecteerde HTTP-instelling wordt gecontroleerd. Het gebruikt de poort voor de test aanvraag, zoals die wordt gebruikt in de geselecteerde HTTP-instelling. U kunt alleen die HTTP-instellingen kiezen die niet zijn gekoppeld aan een andere aangepaste test. <br>Houd er rekening mee dat alleen deze HTTP-instellingen beschikbaar zijn voor koppelingen die hetzelfde protocol hebben als het protocol dat is gekozen in deze test configuratie en dezelfde status hebben als de *naam van de gekozen host van de back-end-HTTP-instelling* .|
   
   > [!IMPORTANT]
   > Met de test wordt de status van de back-end alleen gecontroleerd wanneer deze is gekoppeld aan een of meer HTTP-instellingen. Hiermee worden de back-endservers gecontroleerd van de back-endservers die zijn gekoppeld aan de HTTP-instelling (en) waaraan deze test is gekoppeld. De test aanvraag wordt verzonden als \<protocol\> :// \<hostName\> : \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>De status van de back-end testen met de test

Nadat u de eigenschappen van de test hebt ingevoerd, kunt u de status van de back-end-bronnen testen om te controleren of de test configuratie juist is en of de back-end-resources werken zoals verwacht.

1. Selecteer **testen** en noteer het resultaat van de test. De toepassings gateway test de status van alle back-endservers in de back-endservers die zijn gekoppeld aan de HTTP-instelling (en) die voor deze test worden gebruikt. 

   ![Status van backend testen][5]

2. Als er niet-stateful back-endservers zijn, controleert u de kolom **Details** om de reden voor de slechte status van de resource te begrijpen. Als de bron slecht is gemarkeerd als gevolg van een onjuiste test configuratie, selecteert u de koppeling **Ga terug naar probe** en bewerkt u de test configuratie. Als de bron niet is gemarkeerd vanwege een probleem met de back-end, lost u de problemen met de back-end-bron op en test u vervolgens de back-end opnieuw door de koppeling **Ga terug naar test te** selecteren en **test** te selecteren.

   > [!NOTE]
   > U kunt ervoor kiezen om de test op te slaan, zelfs met een beschadigde back-end-bron, maar dit wordt niet aanbevolen. Dit komt doordat de Application Gateway geen aanvragen doorstuurt naar de back-end-servers uit de back-end-groep, waarvan wordt vastgesteld dat de test de status niet in orde heeft. Als er geen in orde zijnde resources in een back-end-groep zijn, hebt u geen toegang tot uw toepassing en ontvangt u een HTTP 502-fout.

   ![Test resultaat weer geven][6]

3. Selecteer **toevoegen** om de test op te slaan. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Test maken voor Application Gateway v1-SKU

Tests worden in een proces in twee stappen geconfigureerd via de portal. De eerste stap is het maken van de test. In de tweede stap voegt u de test toe aan de back-end-http-instellingen van de toepassings gateway.

### <a name="create-the-probe"></a><a name="createprobe"></a>De test maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis proef versie van één maand](https://azure.microsoft.com/free)

2. Selecteer in het deelvenster Favorieten in Azure Portal **Alle resources**. Selecteer de toepassings gateway op de pagina **alle resources** . Als het abonnement dat u hebt geselecteerd, al verschillende resources heeft, kunt u partners.contoso.net invoeren in het vak Filteren op naam… voor eenvoudige toegang tot de toepassingsgateway.

3. Selecteer **tests** en selecteer **toevoegen** om een test toe te voegen.

   ![Blade test toevoegen met ingevulde gegevens][1]

4. Vul op de Blade **status test toevoegen** de vereiste informatie voor de test in en klik op **OK** als u klaar bent.

   |**Instelling** | **Waarde** | **Details**|
   |---|---|---|
   |**Naam**|customProbe|Deze waarde is een beschrijvende naam die wordt gegeven aan de test die toegankelijk is in de portal.|
   |**Protocol**|HTTP of HTTPS | Het protocol dat wordt gebruikt door de status test. |
   |**Host**|dat wil zeggen contoso.com|Deze waarde is de naam van de virtuele host (die afwijkt van de naam van de VM-host) die op de toepassings server wordt uitgevoerd. De test wordt verzonden naar (Protocol)://(hostnaam):(poort van httpsetting)/urlPath.  Dit is van toepassing wanneer meerdere locaties op Application Gateway zijn geconfigureerd. Als de Application Gateway is geconfigureerd voor één site, voert u 127.0.0.1 in.|
   |**Kies een hostnaam uit de back-end-HTTP-instellingen**|Ja of nee|Hiermee stelt u de *host* -header in de test in op de hostnaam van de back-end-bron in de back-end-groep die is gekoppeld aan de http-instelling waaraan deze test is gekoppeld. Speciaal vereist in het geval van back-endservers met meerdere tenants, zoals Azure app service. [Meer informatie](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Pad**|/of een geldig pad|De rest van de volledige URL voor de aangepaste test. Er begint een geldig pad met '/'. Gebruik voor het standaardpad van http: \/ /contoso.com alleen '/' |
   |**Interval (sec.)**|30|Hoe vaak de test wordt uitgevoerd om de status te controleren. Het wordt afgeraden om de lagere dan 30 seconden in te stellen.|
   |**Time-out (SEC)**|30|De hoeveelheid tijd die de test wacht voordat een time-out optreedt. Als er binnen deze time-outperiode geen geldig antwoord wordt ontvangen, wordt de test als mislukt gemarkeerd. Het time-outinterval moet hoog genoeg zijn dat er een http-aanroep kan worden uitgevoerd om ervoor te zorgen dat de status van de back-end beschikbaar is. Houd er rekening mee dat de time-outwaarde niet groter mag zijn dan de interval waarde die is gebruikt in deze test instelling of de waarde van de time-out van de aanvraag in de HTTP-instelling die wordt gekoppeld aan deze test.|
   |**Drempelwaarde voor onjuiste status**|3|Aantal opeenvolgende mislukte pogingen om te worden beschouwd als een slechte status. De drempel waarde kan worden ingesteld op 1 of meer.|
   |**Vergelijkings voorwaarden voor testen gebruiken**|Ja of nee|Standaard wordt een HTTP (S)-antwoord met de status code tussen 200 en 399 als gezond beschouwd. U kunt het acceptabele bereik van back-end-respons code of back-end-antwoord tekst wijzigen. [Meer informatie](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > De hostnaam is niet hetzelfde als de naam van de server. Deze waarde is de naam van de virtuele host die wordt uitgevoerd op de toepassings server. De test wordt verzonden naar \<protocol\> :// \<hostName\> :\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Test toevoegen aan de gateway

Nu de test is gemaakt, is het tijd om deze toe te voegen aan de gateway. De test instellingen worden ingesteld op de back-end-http-instellingen van de toepassings gateway.

1. Klik op **http-instellingen** op de Application Gateway om de Blade configuratie weer te geven. Klik op de huidige back-end-http-instellingen in het venster.

   ![venster https-instellingen][2]

2. Schakel op de pagina **appGatewayBackEndHttpSettings** -instellingen het selectie vakje **aangepaste test gebruiken** in en kies de test die u hebt gemaakt in het gedeelte [de test maken](#createprobe) in de vervolg keuzelijst **aangepaste test** .
   Wanneer u klaar bent, klikt u op **Opslaan** en worden de instellingen toegepast.

## <a name="next-steps"></a>Volgende stappen

Bekijk de status van de back-end-bronnen zoals bepaald door de test met behulp van de [status weergave back-end](./application-gateway-diagnostics.md#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
