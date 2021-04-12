---
title: Een listener-specifiek SSL-beleid op Azure-toepassing gateway configureren via de portal
description: Meer informatie over het configureren van een listener-specifiek SSL-beleid op Application Gateway via de portal
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231467"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Listener-specifiek SSL-beleid configureren op Application Gateway via de portal (preview)

In dit artikel wordt beschreven hoe u de Azure Portal gebruikt voor het configureren van een listener-specifiek SSL-beleid op uw Application Gateway. Met listener-specifiek SSL-beleid kunt u specifieke Listeners configureren voor het gebruik van verschillende SSL-beleids regels van elkaar. U kunt nog steeds een standaard SSL-beleid instellen dat alle listeners gebruiken tenzij ze worden overschreven door het listener-specifieke SSL-beleid. 

> [!NOTE]
> Alleen Standard_v2 en WAF_v2 Sku's ondersteunen listener-specifiek beleid als listener-specifiek beleid maakt deel uit van SSL-profielen en SSL-profielen worden alleen ondersteund op v2-gateways. 



Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-new-application-gateway"></a>Een nieuwe Application Gateway maken

Maak eerst een nieuwe Application Gateway die u doorgaans via de portal zou uitvoeren. er zijn geen extra stappen nodig bij het maken van een listener-specifiek SSL-beleid configureren. Bekijk onze hand leiding voor de [Portal-Snelstartgids](./quick-create-portal.md)voor meer informatie over het maken van een Application Gateway in de portal.

## <a name="set-up-a-listener-specific-ssl-policy"></a>Een listener-specifiek SSL-beleid instellen

Als u een listener-specifiek SSL-beleid wilt instellen, gaat u eerst naar het tabblad **SSL-instellingen (preview)** in de portal en maakt u een nieuw SSL-profiel. Wanneer u een SSL-profiel maakt, ziet u twee tabbladen: **client verificatie** en **SSL-beleid**. Het tabblad **SSL-beleid** is het configureren van een listener-specifiek SSL-beleid. Op het tabblad **client verificatie** wordt aangegeven waar u een of meer client certificaten voor wederzijdse verificatie kunt uploaden. voor meer informatie raadpleegt u [een wederzijdse verificatie configureren](./mutual-authentication-portal.md).

> [!NOTE]
> U kunt het beste TLS 1,2 gebruiken als TLS 1,2 in de toekomst. 

1. Zoek naar **Application Gateway** in de portal, selecteer **toepassings gateways** en klik op uw bestaande Application Gateway.

2. Selecteer **SSL-instellingen (preview)** in het menu aan de linkerkant.

3. Klik op het plus teken naast **SSL-profielen** aan de bovenkant om een nieuw SSL-profiel te maken.

4. Voer een naam onder de naam van het **SSL-profiel** in. In dit voor beeld noemen we onze SSL-profiel *applicationGatewaySSLProfile*. 

5. Ga naar het tabblad **SSL-beleid** en schakel het selectie vakje **LISTENER-specifiek SSL-beleid inschakelen** in. 

6. Stel uw listener-specifiek SSL-beleid in op basis van uw vereisten. U kunt kiezen tussen vooraf gedefinieerd SSL-beleid en uw eigen SSL-beleid aanpassen. Ga voor meer informatie over SSL-beleid naar [overzicht van SSL-beleid](./application-gateway-ssl-policy-overview.md). U kunt het beste TLS 1,2 gebruiken

7. Selecteer **toevoegen** om op te slaan.

    > [!NOTE]
    > U hoeft geen client verificatie te configureren voor een SSL-profiel om het te koppelen aan een listener. U kunt alleen client verificatie configureren of alleen listener-specifiek SSL-beleid geconfigureerd, of beide zijn geconfigureerd in uw SSL-profiel.  

    ![Listener-specifiek SSL-beleid toevoegen aan SSL-profiel](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>Het SSL-profiel aan een listener koppelen

Nu we een SSL-profiel met een listener-specifiek SSL-beleid hebben gemaakt, moeten we het SSL-profiel aan de listener koppelen om het listener-specifieke beleid in actie te kunnen plaatsen. 

1. Navigeer naar uw bestaande Application Gateway. Als u zojuist de bovenstaande stappen hebt uitgevoerd, hoeft u hier niets te doen. 

2. Selecteer **listeners** in het menu aan de linkerkant. 

3. Klik op **listener toevoegen** als u nog geen HTTPS-listener hebt ingesteld. Als u al een HTTPS-listener hebt, klikt u op deze in de lijst. 

4. Vul de **naam** van de listener, **het frontend-IP-adres**, de **poort**, het **protocol** en andere https- **instellingen** in die voldoen aan uw vereisten.

5. Schakel het selectie vakje **SSL-profiel inschakelen** in zodat u kunt selecteren welk SSL-profiel moet worden gekoppeld aan de listener. 

6. Selecteer het SSL-profiel dat u hebt gemaakt in de vervolg keuzelijst. In dit voor beeld kiezen we het SSL-profiel dat we hebben gemaakt op basis van de eerdere stappen: *applicationGatewaySSLProfile*. 

7. Ga door met het configureren van de rest van de listener zodat deze aan uw vereisten voldoet. 

8. Klik op **toevoegen** om uw nieuwe listener op te slaan met het SSL-profiel dat eraan is gekoppeld. 

    ![SSL-profiel aan nieuwe listener koppelen](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)