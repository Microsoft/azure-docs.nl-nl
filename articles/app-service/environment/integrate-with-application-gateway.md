---
title: Met Application Gateway integreren
description: Meer informatie over het integreren van een app in uw ILB-App Service Environment met een Application Gateway in deze end-to-end-instructies.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3b73d528802a8aa33c6122eaf5edfa9d046b6753
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962074"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Uw ILB App Service-omgeving integreren met Azure Application Gateway #

De [app service Environment](./intro.md) is een implementatie van Azure app service in het subnet van het virtuele Azure-netwerk van een klant. Het kan worden geïmplementeerd met een openbaar of persoonlijk eind punt voor toegang tot apps. De implementatie van de App Service Environment met een persoonlijk eind punt (dat wil zeggen, een interne load balancer) wordt een ILB App Service Environment genoemd.  

Met firewalls voor webtoepassingen kunt u uw webtoepassingen beveiligen door inkomend webverkeer te inspecteren om SQL-injecties, cross-site scripting, uploads van malware & toepassings DDoS en andere aanvallen te blok keren. Ook worden de antwoorden van de back-end-webservers voor preventie van gegevens verlies (DLP) gecontroleerd. U kunt een WAF-apparaat verkrijgen via de Azure Marketplace of u kunt de [Azure-toepassing gateway][appgw]gebruiken.

De Azure-toepassing-gateway is een virtueel apparaat dat laag 7 taak verdeling, TLS/SSL-offloading en Web Application Firewall (WAF) beveiliging biedt. Het kan Luis teren naar een openbaar IP-adres en verkeer door sturen naar het eind punt van de toepassing. In de volgende informatie wordt beschreven hoe u een met WAF geconfigureerde toepassings gateway integreert met een app in een ILB-App Service Environment.  

De integratie van de toepassings gateway met de ILB-App Service Environment bevindt zich op een app-niveau. Wanneer u de toepassings gateway configureert met uw ILB-App Service Environment, voert u deze uit voor specifieke apps in uw ILB App Service Environment. Deze techniek maakt het mogelijk om beveiligde multi tenant-toepassingen in één ILB-App Service Environment te hosten.  

![Application Gateway die verwijst naar de app op een ILB-App Service Environment][1]

In deze procedure gaat u het volgende doen:

* Een Azure-toepassing gateway maken.
* Configureer de Application Gateway zodanig dat deze verwijst naar een app in uw ILB App Service Environment.
* Configureer uw app om te voldoen aan de aangepaste domein naam.
* Bewerk de naam van de open bare DNS-host die naar uw toepassings gateway verwijst.

## <a name="prerequisites"></a>Vereisten

Als u uw Application Gateway wilt integreren met uw ILB-App Service Environment, hebt u het volgende nodig:

* Een ILB-App Service Environment.
* Een app die wordt uitgevoerd in de ILB-App Service Environment.
* Een Internet routeerbaar domein naam die wordt gebruikt voor uw app in de ILB-App Service Environment.
* Het ILB-adres dat uw ILB-App Service Environment gebruikt. Deze informatie bevindt zich in de app service Environment portal onder **instellingen**  >  **IP-adressen**:

    ![Een voor beeld van een lijst met IP-adressen die worden gebruikt door de ILB-App Service Environment][9]
    
* Een open bare DNS-naam die later wordt gebruikt om naar uw Application Gateway te verwijzen. 

Zie [een ILB app service Environment maken en gebruiken][ilbase]voor meer informatie over het maken van een ILB-app service environment.

In dit artikel wordt ervan uitgegaan dat u een Application Gateway wilt in hetzelfde virtuele Azure-netwerk waar de App Service Environment wordt geïmplementeerd. Kies of maak een subnet dat u gaat gebruiken om de gateway te hosten voordat u begint met het maken van de Application Gateway. 

U moet een subnet gebruiken dat niet de naam GatewaySubnet heeft. Als u de Application Gateway in GatewaySubnet plaatst, kunt u later geen virtuele netwerk gateway maken. 

U kunt de gateway ook niet in het subnet plaatsen dat door uw ILB-App Service Environment wordt gebruikt. Het App Service Environment is het enige wat in dit subnet kan zijn.

## <a name="configuration-steps"></a>Configuratiestappen ##

1. Ga in het Azure Portal naar **Nieuw**  >  **netwerk**  >  **Application Gateway**.

2. In het gebied **basis** :

   a. Voer bij **naam** de naam van de Application Gateway in.

   b. Selecteer bij **laag** de optie **WAF**.

   c. Selecteer voor het **abonnement** hetzelfde abonnement dat door het virtuele app service Environment-netwerk wordt gebruikt.

   d. Voor **resource groep**, maakt of selecteert u de resource groep.

   e. Selecteer bij **locatie** de locatie van het app service environment virtuele netwerk.

   ![Basis beginselen van nieuwe Application Gateway maken][2]

3. In het gebied **instellingen** :

   a. Selecteer voor **virtueel netwerk** het app service environment virtuele netwerk.

   b. Selecteer bij **subnet** het subnet waar de Application Gateway moet worden geïmplementeerd. Gebruik GatewaySubnet niet om te voor komen dat VPN-gateways worden gemaakt.

   c. Selecteer **openbaar** bij **IP-adres type**.

   d. Selecteer een openbaar IP-adres voor **openbaar IP-adres**. Als u er nog geen hebt, maakt u er nu een.

   e. Selecteer voor **protocol** **http** of **https**. Als u configureert voor HTTPS, moet u een PFX-certificaat opgeven.

   f. Voor **Web Application firewall** kunt u de firewall inschakelen en deze ook instellen voor **detectie** of voor **komen** .

   ![Nieuwe instellingen voor het maken van Application Gateway][3]
    
4. Controleer de instellingen in de sectie **samen vatting** en selecteer **OK**. Het volt ooien van de installatie van uw Application Gateway kan iets meer dan 30 minuten duren.  

5. Nadat de installatie van Application Gateway is voltooid, gaat u naar uw Application Gateway portal. Selecteer de **back-end-pool**. Voeg het ILB-adres voor uw ILB-App Service Environment toe.

   ![Back-end-groep configureren][4]

6. Nadat het proces voor het configureren van uw back-end-pool is voltooid, selecteert u **status controles**. Maak een status test voor de domein naam die u wilt gebruiken voor uw app. 

   ![Statuscontroles configureren][5]
    
7. Nadat het proces van het configureren van de status tests is voltooid, selecteert u **http-instellingen**. Bewerk de bestaande instellingen, selecteer **aangepaste test gebruiken** en kies de test die u hebt geconfigureerd.

   ![HTTP-instellingen configureren][6]
    
8. Ga naar de sectie **overzicht** van de Application Gateway en kopieer het open bare IP-adres dat door uw Application Gateway wordt gebruikt. Stel dat IP-adres in als een record voor de domein naam van uw app of gebruik de DNS-naam voor dat adres in een CNAME-record. Het is eenvoudiger om het open bare IP-adres te selecteren en dit te kopiëren van de gebruikers interface van het open bare IP-adres in plaats van deze te kopiëren van de koppeling in de sectie **overzicht** van de Application Gateway. 

   ![Application Gateway portal][7]

9. Stel de aangepaste domein naam voor uw app in uw ILB-App Service Environment in. Ga naar uw app in de portal en selecteer **aangepaste domeinen** onder **instellingen**.

   ![Aangepaste domein naam voor de app instellen][8]

Er is informatie over het instellen van aangepaste domein namen voor uw web-apps in het artikel [instelling van aangepaste domein namen voor uw web-app][custom-domain]. Maar voor een app in een ILB-App Service Environment is er geen validatie voor de domein naam. Omdat u de eigenaar bent van de DNS-server die de app-eind punten beheert, kunt u daar wat u wilt opnemen. De aangepaste domein naam die u in dit geval toevoegt, hoeft niet in uw DNS te zijn, maar moet wel worden geconfigureerd met uw app. 

Nadat de installatie is voltooid en u een korte tijd hebt om uw DNS-wijzigingen door te geven, kunt u toegang krijgen tot uw app met behulp van de aangepaste domein naam die u hebt gemaakt. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md