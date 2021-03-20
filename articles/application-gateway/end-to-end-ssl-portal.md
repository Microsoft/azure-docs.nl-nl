---
title: End-to-end TLS-versleuteling configureren met behulp van de portal
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van de Azure Portal om een toepassings gateway te maken met end-to-end TLS-code ring.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6e2d1828acefacb03cc2f42193b8cd8897578b6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397498"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>End-to-end-TLS configureren met Application Gateway met behulp van de portal

In dit artikel wordt beschreven hoe u met behulp van de Azure Portal end-to-end-Transport Layer Security (TLS)-versleuteling, voorheen bekend als Secure Sockets Layer (SSL)-code ring, kunt configureren via Azure-toepassing gateway v1 SKU.

> [!NOTE]
> Voor de SKU van Application Gateway v2 zijn vertrouwde basis certificaten vereist om end-to-end-configuratie in te scha kelen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Als u end-to-end TLS wilt configureren met een toepassings gateway, hebt u een certificaat nodig voor de gateway. Certificaten zijn ook vereist voor de back-endservers. Het gateway certificaat wordt gebruikt voor het afleiden van een symmetrische sleutel in overeenstemming met de TLS-protocol specificatie. De symmetrische sleutel wordt vervolgens gebruikt voor het versleutelen en ontsleutelen van het verkeer dat naar de gateway wordt verzonden. 

Voor end-to-end TLS-versleuteling moeten de juiste back-endservers zijn toegestaan in de toepassings gateway. Als u deze toegang wilt toestaan, uploadt u het open bare certificaat van de back-endservers, ook wel bekend als verificatie certificaten (v1) of vertrouwde basis certificaten (v2), naar de toepassings gateway. Door het certificaat toe te voegen, zorgt u ervoor dat de Application Gateway alleen communiceert met bekende back-end-exemplaren. Deze configuratie beveiligt end-to-end-communicatie.

Zie [overzicht van TLS-beëindiging en end-to-end-TLS met Application Gateway](./ssl-overview.md)voor meer informatie.

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Een nieuwe toepassings gateway maken met end-to-end TLS

Als u een nieuwe toepassings gateway met end-to-end TLS-versleuteling wilt maken, moet u eerst TLS-beëindiging inschakelen tijdens het maken van een nieuwe toepassings gateway. Met deze actie wordt TLS-versleuteling ingeschakeld voor communicatie tussen de client en de toepassings gateway. Vervolgens moet u in de lijst met veilige ontvangers de certificaten voor de back-endservers in de HTTP-instellingen opnemen. Deze configuratie maakt TLS-versleuteling mogelijk voor communicatie tussen de toepassings gateway en de back-endservers. Waarmee de end-to-end TLS-versleuteling wordt uitgevoerd.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>TLS-beëindiging inschakelen tijdens het maken van een nieuwe toepassings gateway

Zie [TLS-beëindiging inschakelen tijdens het maken van een nieuwe toepassings gateway](./create-ssl-portal.md)voor meer informatie.

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Verificatie/basis certificaten van back-endservers toevoegen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **http-instellingen** in het menu aan de linkerkant. Azure heeft automatisch een standaard HTTP-instelling, **appGatewayBackendHttpSettings**, gemaakt wanneer u de toepassings gateway hebt gemaakt. 

3. Selecteer **appGatewayBackendHttpSettings**.

4. Onder **protocol** selecteert u **https**. Er wordt een deel venster voor **back-end-verificatie certificaten of vertrouwde basis certificaten** weer gegeven.

5. Selecteer **Nieuw maken**.

6. Voer in het veld **naam** een geschikte naam in.

7. Selecteer het certificaat bestand in het vak **CER-certificaat uploaden** .

   Voor Standard-en WAF (v1)-toepassings gateways moet u de open bare sleutel van uw back-endserver certificaat uploaden in. CER-indeling.

   ![Certificaat toevoegen](./media/end-to-end-ssl-portal/addcert.png)

   Voor Standard_v2 en WAF_v2 toepassings gateways moet u het basis certificaat van het back-endserver certificaat uploaden in. CER-indeling. Als het back-end-certificaat is uitgegeven door een bekende certificerings instantie (CA), kunt u het selectie vakje **goed bekend CA-certificaat gebruiken** selecteren en vervolgens geen certificaat uploaden.

   ![Vertrouwd basis certificaat toevoegen](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Basis certificaat](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selecteer **Opslaan**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>End-to-end TLS inschakelen voor een bestaande toepassings gateway

Als u een bestaande toepassings gateway met end-to-end TLS-versleuteling wilt configureren, moet u eerst TLS-beëindiging inschakelen in de listener. Met deze actie wordt TLS-versleuteling ingeschakeld voor communicatie tussen de client en de toepassings gateway. Vervolgens plaatst u deze certificaten voor back-endservers in de HTTP-instellingen in de lijst met veilige geadresseerden. Deze configuratie maakt TLS-versleuteling mogelijk voor communicatie tussen de toepassings gateway en de back-endservers. Waarmee de end-to-end TLS-versleuteling wordt uitgevoerd.

U moet een listener gebruiken met het HTTPS-protocol en een certificaat voor het inschakelen van TLS-beëindiging. U kunt een bestaande listener gebruiken die aan deze voor waarden voldoet of een nieuwe listener maken. Als u de optie voormalig selecteert, kunt u de volgende para graaf ' TLS-beëindiging inschakelen in een bestaande toepassings gateway ' negeren en rechtstreeks naar de sectie ' verificatie/vertrouwde basis certificaten voor back-endservers toevoegen ' gaan.

Als u de laatste optie kiest, moet u de stappen in de volgende procedure Toep assen.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>TLS-beëindiging inschakelen in een bestaande toepassings gateway

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **listeners** in het menu aan de linkerkant.

3. Selecteer een **Basic** -of **multi-site** -listener, afhankelijk van uw vereisten.

4. Onder **protocol** selecteert u **https**. Er wordt een deel venster voor het **certificaat** weer gegeven.

5. Upload het PFX-certificaat dat u wilt gebruiken voor het beëindigen van TLS tussen de client en de toepassings gateway.

   > [!NOTE]
   > Voor test doeleinden kunt u een zelfondertekend certificaat gebruiken. Dit wordt echter niet aanbevolen voor productie werkbelastingen, omdat ze moeilijker te beheren zijn en niet volledig zijn beveiligd. Zie [een zelfondertekend certificaat maken](./create-ssl-portal.md#create-a-self-signed-certificate)voor meer informatie.

6. Voeg andere vereiste instellingen voor de **listener** toe, afhankelijk van uw vereisten.

7. Selecteer **OK** om op te slaan.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Verificatie/vertrouwde basis certificaten van back-endservers toevoegen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **http-instellingen** in het menu aan de linkerkant. U kunt certificaten in een bestaande back-end-HTTP-instelling in de lijst met veilige geadresseerden plaatsen of een nieuwe HTTP-instelling maken. (In de volgende stap wordt het certificaat voor de standaard-HTTP-instelling **appGatewayBackendHttpSettings** toegevoegd aan de lijst met veilige geadresseerden.)

3. Selecteer **appGatewayBackendHttpSettings**.

4. Onder **protocol** selecteert u **https**. Er wordt een deel venster voor **back-end-verificatie certificaten of vertrouwde basis certificaten** weer gegeven. 

5. Selecteer **Nieuw maken**.

6. Voer in het veld **naam** een geschikte naam in.

7. Selecteer het certificaat bestand in het vak **CER-certificaat uploaden** .

   Voor Standard-en WAF (v1)-toepassings gateways moet u de open bare sleutel van uw back-endserver certificaat uploaden in. CER-indeling.

   ![Certificaat toevoegen](./media/end-to-end-ssl-portal/addcert.png)

   Voor Standard_v2 en WAF_v2 toepassings gateways moet u het basis certificaat van het back-endserver certificaat uploaden in. CER-indeling. Als het back-end-certificaat wordt uitgegeven door een bekende certificerings instantie, kunt u het selectie vakje **goed bekende CA-certificaat gebruiken** selecteren en vervolgens een certificaat niet uploaden.

   ![Vertrouwd basis certificaat toevoegen](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)