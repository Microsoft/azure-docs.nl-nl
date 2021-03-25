---
title: Failover over meerdere eind punten met Traffic Manager
titleSuffix: Azure Content Delivery Network
description: Meer informatie over het configureren van failover in meerdere Azure Content Delivery Network-eind punten met behulp van Azure Traffic Manager.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a003becba0bc1e42d8fe0c0c5b199402a430a8e1
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034755"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Failover over meerdere eind punten met Azure Traffic Manager

Wanneer u Azure Content Delivery Network (CDN) configureert, kunt u de optimale provider en prijs categorie voor uw behoeften selecteren. 

Azure CDN, met de wereld wijd gedistribueerde infra structuur, maakt standaard lokale en geografische redundantie en wereld wijde taak verdeling om de beschik baarheid en prestaties van de service te verbeteren. 

Als een locatie niet beschikbaar is voor het leveren van inhoud, worden aanvragen automatisch doorgestuurd naar een andere locatie. Het optimale aanwezigheids punt (POP) wordt gebruikt om elke client aanvraag te verwerken. De automatische route ring is gebaseerd op factoren als aanvraag locatie en server belasting.
 
Als u meerdere CDN-profielen hebt, kunt u de beschik baarheid en prestaties verder verbeteren met Azure Traffic Manager. 

Gebruik Azure Traffic Manager met Azure CDN voor taak verdeling tussen meerdere CDN-eind punten voor:
 
* Failover
* Geo-taak verdeling 

In een typisch failover-scenario worden alle client aanvragen omgeleid naar het primaire CDN-profiel. 

Als het profiel niet beschikbaar is, worden aanvragen omgeleid naar het secundaire profiel.  Aanvragen worden hervat naar uw primaire profiel wanneer het weer online is.

Als u Azure Traffic Manager op deze manier gebruikt, zorgt u ervoor dat uw webtoepassing altijd beschikbaar is. 

Dit artikel bevat richt lijnen en een voor beeld van het configureren van failover met profielen vanuit: 

* **Azure CDN Standard van Verizon**
* **Azure CDN Standard van Akamai**

**Azure CDN van micro soft** wordt ook ondersteund.

## <a name="create-azure-cdn-profiles"></a>Azure CDN profielen maken
Maak twee of meer Azure CDN profielen en eind punten met verschillende providers.

1. Twee CDN-profielen maken:
    * **Azure CDN Standard van Verizon**
    * **Azure CDN Standard van Akamai** 

    Maak de profielen door de stappen in [een nieuw CDN-profiel maken](cdn-create-new-endpoint.md#create-a-new-cdn-profile)te volgen.
 
   ![Meerdere CDN-profielen](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Maak ten minste één eind punt in elk van de nieuwe profielen door de stappen in [een nieuw CDN-eind punt maken](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)te volgen.

## <a name="create-traffic-manager-profile"></a>Traffic Manager-profiel maken
Een Azure Traffic Manager-profiel maken en taak verdeling configureren voor uw CDN-eind punten. 

1. Maak een Azure Traffic Manager-profiel door de stappen in [een Traffic Manager profiel maken](../traffic-manager/quickstart-create-traffic-manager-profile.md)te volgen. 

    * **Routerings methode**, selecteer **prioriteit**.

2. Voeg uw CDN-eind punten toe aan uw Traffic Manager profiel door de stappen in [Traffic Manager-eind punten toevoegen](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints) te volgen

    * **Type**, selecteer **externe eind punten**.
    * Voer een waarde in.

    Maak bijvoorbeeld **cdndemo101akamai.azureedge.net** met prioriteit **1** en **cdndemo101verizon.azureedge.net** met prioriteit **2**.

   ![CDN Traffic Manager-eind punten](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Aangepast domein configureren op Azure CDN en Azure Traffic Manager
Nadat u uw CDN-en Traffic Manager-profielen hebt geconfigureerd, volgt u deze stappen om DNS-toewijzing toe te voegen en aangepast domein te registreren voor de CDN-eind punten. Voor dit voor beeld is de aangepaste domein naam **cdndemo101.dustydogpetcare.online**.

1. Ga naar de website voor de domein provider van uw aangepaste domein, zoals GoDaddy, en maak twee DNS CNAME-vermeldingen. 

    a. Wijs voor de eerste CNAME-vermelding uw aangepaste domein, met het subdomein cdnverify, toe aan uw CDN-eind punt. Dit item is een vereiste stap voor het registreren van het aangepaste domein bij het CDN-eind punt dat u in stap 2 aan Traffic Manager hebt toegevoegd.

      Bijvoorbeeld: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Voor de tweede CNAME-vermelding wijst u uw aangepaste domein, zonder het subdomein cdnverify, toe aan uw CDN-eind punt. Met deze vermelding wordt het aangepaste domein toegewezen aan Traffic Manager. 

      Bijvoorbeeld: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Als uw domein momenteel Live is en niet kan worden onderbroken, voert u deze stap als laatste uit. Controleer of de CDN-eind punten en Traffic Manager-domeinen Live zijn voordat u uw aangepaste domein-DNS bijwerkt naar Traffic Manager.
    >
   
    > [!NOTE]
    > Voor implemeting deze failover over scenerio moeten beide eind punten zich in verschillende profielen benemen en moeten de verschillende profielen door verschillende CDN-providers zijn om domein naam conflicten te voor komen.
    > 

2.  Selecteer in uw Azure CDN profiel het eerste CDN-eind punt (Akamai). Selecteer **aangepast domein** en invoer **cdndemo101.dustydogpetcare.online** toevoegen. Controleer of het selectie vakje voor het valideren van het aangepaste domein groen is. 

    Azure CDN gebruikt het subdomein **cdnverify** om de DNS-toewijzing te valideren om dit registratie proces te volt ooien. Zie [een CNAME DNS-record maken](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)voor meer informatie. Met deze stap zorgt u ervoor dat Azure CDN het aangepaste domein herkent zodat het kan reageren op aanvragen.
    
    > [!NOTE]
    > Als u TLS wilt inschakelen voor een **Azure CDN van Akamai** -profielen, moet u het aangepaste domein rechtstreeks naar uw eind punt CNAME. cdnverify voor het inschakelen van TLS wordt nog niet ondersteund. 
    >

3.  Ga terug naar de website voor de domein provider van uw aangepaste domein. Werk de eerste DNS-toewijzing die u hebt gemaakt bij. Wijs het aangepaste domein toe aan het tweede CDN-eind punt.
                             
    Bijvoorbeeld: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Selecteer in uw Azure CDN profiel het tweede CDN-eind punt (Verizon) en herhaal stap 2. Selecteer **aangepast domein toevoegen** en voer **cdndemo101.dustydogpetcare.online** in.
 
Nadat u deze stappen hebt voltooid, wordt de multi-CDN-service met failover-mogelijkheden geconfigureerd met Azure Traffic Manager. 

U kunt de test-Url's openen vanuit uw aangepaste domein. 

Als u de functionaliteit wilt testen, schakelt u het primaire CDN-eind punt uit en controleert u of de aanvraag juist is verplaatst naar het secundaire CDN-eind punt. 

## <a name="next-steps"></a>Volgende stappen
U kunt andere routerings methoden, zoals geografische, configureren om de belasting te verdelen over verschillende CDN-eind punten. 

Zie [Configure the geografische verkeers routerings methode met Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)voor meer informatie.
