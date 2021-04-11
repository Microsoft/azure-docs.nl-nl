---
title: Wederzijdse verificatie op Azure-toepassing gateway via Portal configureren
description: Meer informatie over het configureren van een Application Gateway voor wederzijdse verificatie via de portal
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231434"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Wederzijdse verificatie met Application Gateway configureren via de portal (preview)

In dit artikel wordt beschreven hoe u de Azure Portal gebruikt voor het configureren van wederzijdse verificatie op uw Application Gateway. Wederzijdse verificatie betekent Application Gateway verificatie van de client die de aanvraag verzendt met behulp van het client certificaat dat u uploadt naar de Application Gateway. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Als u wederzijdse verificatie wilt configureren met een Application Gateway, hebt u een client certificaat nodig om te uploaden naar de gateway. Het client certificaat wordt gebruikt voor het valideren van het certificaat dat door de client wordt weer gegeven voor Application Gateway. Voor test doeleinden kunt u een zelfondertekend certificaat gebruiken. Dit wordt echter niet aanbevolen voor productie werkbelastingen, omdat ze moeilijker te beheren zijn en niet volledig zijn beveiligd. 

Zie [overzicht van wederzijdse verificatie met Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)voor meer informatie, met name wat voor soort client certificaten u kunt uploaden.

## <a name="create-a-new-application-gateway"></a>Een nieuwe Application Gateway maken

Maak eerst een nieuwe Application Gateway op dezelfde manier als u normaal gesp roken de portal zou gebruiken. er zijn geen extra stappen nodig om wederzijdse verificatie mogelijk te maken. Bekijk onze hand leiding voor de [Portal-Snelstartgids](./quick-create-portal.md)voor meer informatie over het maken van een Application Gateway in de portal.

## <a name="configure-mutual-authentication"></a>Wederzijdse verificatie configureren 

Als u een bestaande Application Gateway met wederzijdse verificatie wilt configureren, gaat u eerst naar het tabblad **SSL-instellingen (preview)** in de portal en maakt u een nieuw SSL-profiel. Wanneer u een SSL-profiel maakt, ziet u twee tabbladen: **client verificatie** en **SSL-beleid**. Op het tabblad **client verificatie** kunt u uw client certificaten uploaden. Het tabblad **SSL-beleid** is het configureren van een specifiek SSL-beleid voor een listener. voor meer informatie raadpleegt u [Configure a LISTENER specificive SSL Policy](./application-gateway-configure-listener-specific-ssl-policy.md).

> [!IMPORTANT]
> Zorg ervoor dat u de volledige CA-certificaat keten van de client in één bestand uploadt en slechts één keten per bestand.

1. Zoek naar **Application Gateway** in de portal, selecteer **toepassings gateways** en klik op uw bestaande Application Gateway.

2. Selecteer **SSL-instellingen (preview)** in het menu aan de linkerkant.

3. Klik op het plus teken naast **SSL-profielen** aan de bovenkant om een nieuw SSL-profiel te maken.

4. Voer een naam onder de naam van het **SSL-profiel** in. In dit voor beeld noemen we onze SSL-profiel *applicationGatewaySSLProfile*. 

5. Blijf op het tabblad **client verificatie** . Upload het PEM-certificaat dat u wilt gebruiken voor wederzijdse verificatie tussen de client en het Application Gateway met behulp van de knop **een nieuw certificaat uploaden** . 

    Zie voor meer informatie over het extra heren van certificaten voor vertrouwde client certificerings instanties die u hier kunt uploaden, een [vertrouwde CA-certificaat keten van de client ophalen](./mutual-authentication-certificate-management.md).

   > [!NOTE]
   > Als dit niet uw eerste SSL-profiel is en u andere client certificaten op uw Application Gateway hebt geüpload, kunt u ervoor kiezen om een bestaand certificaat op uw gateway opnieuw te gebruiken via het vervolg keuzemenu. 

6. Schakel het selectie vakje **DN van de uitgever van het client certificaat verifiëren** alleen in als u wilt dat Application Gateway de DN-naam van de onmiddellijke Uitgever van het client certificaat verifieert. 

7. Overweeg een listener-specifiek beleid toe te voegen. Zie de instructies bij het [instellen van een specifiek SSL-beleid](./application-gateway-configure-listener-specific-ssl-policy.md)voor de listener.

8. Selecteer **toevoegen** om op te slaan.
    > [!div class="mx-imgBorder"]
    > ![Client verificatie toevoegen aan SSL-profiel](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>Het SSL-profiel aan een listener koppelen

Nu we een SSL-profiel hebben gemaakt waarvoor wederzijdse verificatie is geconfigureerd, moeten we het SSL-profiel aan de listener koppelen om het instellen van wederzijdse verificatie te volt ooien. 

1. Navigeer naar uw bestaande Application Gateway. Als u zojuist de bovenstaande stappen hebt uitgevoerd, hoeft u hier niets te doen. 

2. Selecteer **listeners** in het menu aan de linkerkant. 

3. Klik op **listener toevoegen** als u nog geen HTTPS-listener hebt ingesteld. Als u al een HTTPS-listener hebt, klikt u op deze in de lijst. 

4. Vul de **naam** van de listener, **het frontend-IP-adres**, de **poort**, het **protocol** en andere https- **instellingen** in die voldoen aan uw vereisten.

5. Schakel het selectie vakje **SSL-profiel inschakelen** in zodat u kunt selecteren welk SSL-profiel moet worden gekoppeld aan de listener. 

6. Selecteer het SSL-profiel dat u zojuist hebt gemaakt in de vervolg keuzelijst. In dit voor beeld kiezen we het SSL-profiel dat we hebben gemaakt op basis van de eerdere stappen: *applicationGatewaySSLProfile*. 

7. Ga door met het configureren van de rest van de listener zodat deze aan uw vereisten voldoet. 

8. Klik op **toevoegen** om uw nieuwe listener op te slaan met het SSL-profiel dat eraan is gekoppeld. 

    > [!div class="mx-imgBorder"]
    > ![SSL-profiel aan nieuwe listener koppelen](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Verlopen client certificaten vernieuwen

In het geval dat het CA-certificaat van de client is verlopen, kunt u het certificaat op uw gateway bijwerken door de volgende stappen uit te voeren: 

1. Ga naar uw Application Gateway en ga naar het tabblad **SSL-instellingen (preview)** in het menu aan de linkerkant. 
 
1. Selecteer de bestaande SSL-profielen met het verlopen client certificaat. 
 
1. Selecteer **een nieuw certificaat uploaden** op het tabblad **client verificatie** en upload uw nieuwe client certificaat. 
 
1. Selecteer het prullenbak pictogram naast het verlopen certificaat. Hiermee wordt de koppeling van het certificaat uit het SSL-profiel verwijderd. 

1. Herhaal stap 2-4 hierboven met een ander SSL-profiel dat hetzelfde verlopen client certificaat gebruikt. U kunt het nieuwe certificaat dat u in stap 3 hebt geüpload, kiezen in het vervolg keuzemenu in andere SSL-profielen.

## <a name="next-steps"></a>Volgende stappen

- [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)