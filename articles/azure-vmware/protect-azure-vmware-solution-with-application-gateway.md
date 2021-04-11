---
title: Azure-toepassing gateway gebruiken voor het beveiligen van uw web-apps op de Azure VMware-oplossing
description: Configureer Azure-toepassing gateway zo dat uw web-apps die worden uitgevoerd op een Azure VMware-oplossing, veilig zichtbaar zijn.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: f92027ee46fdaae275939acaea10e144b6bde101
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601913"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Azure-toepassing gateway gebruiken voor het beveiligen van uw web-apps op de Azure VMware-oplossing

[Azure-toepassing gateway](https://azure.microsoft.com/services/application-gateway/) is een laag 7-webverkeer Load Balancer waarmee u verkeer naar uw webtoepassingen kunt beheren. Het wordt aangeboden in zowel Azure VMware-oplossing v 1.0 als v 2.0. Beide versies getest met web-apps die worden uitgevoerd op de Azure VMware-oplossing.

De mogelijkheden zijn onder andere: 
- Sessie affiniteit op basis van cookies
- URL-gebaseerde routering
- Web Application Firewall (WAF)

Zie [Azure-toepassing gateway functies](../application-gateway/features.md)voor een volledige lijst met functies. 

Dit artikel laat u zien hoe u Application Gateway voor een webserver farm kunt gebruiken om een web-app te beveiligen die wordt uitgevoerd op de Azure VMware-oplossing. 

## <a name="topology"></a>Topologie
In het diagram ziet u hoe Application Gateway wordt gebruikt voor het beveiligen van virtuele machines (Vm's) van Azure IaaS, virtuele-machine schaal sets van Azure of on-premises servers. Application Gateway behandelt Vm's van Azure VMware-oplossing als on-premises servers. 

![Diagram waarin wordt getoond hoe Application Gateway Azure IaaS virtuele machines (Vm's), virtuele-machine schaal sets van Azure of on-premises servers beveiligt.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure-toepassing gateway is momenteel de enige ondersteunde methode om web-apps weer te geven die worden uitgevoerd op virtuele machines van Azure VMware-oplossingen.

Het diagram toont het test scenario dat wordt gebruikt om de Application Gateway te valideren met de webtoepassingen van Azure VMware Solution.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Diagram van het test scenario dat wordt gebruikt om de Application Gateway te valideren met Azure VMware Solution-webtoepassingen." border="false":::

Het Application Gateway exemplaar wordt geïmplementeerd op de hub in een toegewezen subnet. Het heeft een openbaar IP-adres van Azure. Het wordt aanbevolen [Azure DDoS Protection standaard](../ddos-protection/ddos-protection-overview.md) voor het virtuele netwerk te activeren. De webserver wordt gehost op een privécloud van Azure VMware-oplossing achter NSX t0 en T1-gateways. De Azure VMware-oplossing maakt gebruik van [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) om communicatie met de hub en on-premises systemen mogelijk te maken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement.
- Een Azure VMware-oplossing privécloud geïmplementeerd en wordt uitgevoerd.

## <a name="deployment-and-configuration"></a>Implementatie en configuratie

1. Zoek in het Azure Portal naar **Application Gateway** en selecteer **toepassings gateway maken**.

2. Geef de basis gegevens op zoals in de volgende afbeelding. Selecteer vervolgens **volgende: front-end>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Scherm opname van de pagina Toepassings gateway maken in Azure Portal.":::

3. Kies het type van het frontend-IP-adres. Kies voor openbaar een bestaand openbaar IP-adres of maak een nieuw account. Selecteer **volgende: back**-ends>.

    > [!NOTE]
    > Alleen standaard-en WAF-Sku's (Web Application firewall) worden ondersteund voor privé-front-end.

4. Voeg een back-end-groep toe van de virtuele machines die worden uitgevoerd op de infra structuur van Azure VMware-oplossingen. Geef de details op van webservers die worden uitgevoerd in de privécloud van de Azure VMware-oplossing en selecteer **toevoegen**.  Selecteer vervolgens **volgende: configuratie>**.

5. Selecteer op het tabblad **configuratie** **een regel voor het toevoegen van een route ring**.

6. Geef op het tabblad **listener** de details op voor de listener. Als HTTPS is geselecteerd, moet u een certificaat opgeven, hetzij vanuit een PFX-bestand of een bestaand Azure Key Vault certificaat. 

7. Selecteer het tabblad **backend-doelen** en selecteer de back-end-groep die u eerder hebt gemaakt. Selecteer voor het veld **http-instellingen** de optie **nieuwe toevoegen**.

8. Configureer de para meters voor de HTTP-instellingen. Selecteer **Toevoegen**.

9. Als u op paden gebaseerde regels wilt configureren, selecteert u **meerdere doelen toevoegen om een op pad gebaseerde regel te maken**. 

10. Voeg een op een pad gebaseerde regel toe en selecteer **toevoegen**. Herhaal dit om meer op pad gebaseerde regels toe te voegen. 

11. Wanneer u klaar bent met het toevoegen van op het pad gebaseerde regels, selecteert u opnieuw **toevoegen** . Selecteer vervolgens **volgende: labels>**. 

12. Voeg labels toe en selecteer **volgende: controleren +>maken**.

13. Er wordt een validatie uitgevoerd op uw Application Gateway. Als dit lukt, selecteert u **maken** om te implementeren.

## <a name="configuration-examples"></a>Configuratie voorbeelden

Nu gaan we Application Gateway configureren met Vm's uit de Azure VMware-oplossing als back-endservers voor de volgende use-cases: 

- [Meerdere sites hosten](#hosting-multiple-sites)
- [Route ring op URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Meerdere sites hosten

Met deze procedure wordt beschreven hoe u back-mailadres groepen kunt definiëren met behulp van Vm's die worden uitgevoerd op een Azure VMware-oplossing privécloud op een bestaande toepassings gateway. 

>[!NOTE]
>Bij deze procedure wordt ervan uitgegaan dat u meerdere domeinen hebt, dus we gebruiken voor beelden van www.contoso.com en www.fabrikam.com.


1. Maak in uw privécloud twee verschillende groepen Vm's. De ene vertegenwoordigt Contoso en de tweede fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="Scherm afbeelding met een overzicht van de details van een webserver in de VSphere-client.":::

    We hebben Windows Server 2016 gebruikt met de functie Internet Information Services (IIS) geïnstalleerd. Zodra de Vm's zijn geïnstalleerd, voert u de volgende Power shell-opdrachten uit om IIS op elk van de virtuele machines te configureren. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Selecteer in een bestaand toepassings gateway-exemplaar **Back-upgroepen** in het linkermenu, selecteer  **toevoegen** en voer de details van de nieuwe groepen in. Selecteer **toevoegen** in het rechterdeel venster.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="Scherm opname van de pagina met back-endservers voor het toevoegen van back-end-Pools." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. Maak in de sectie **listeners** een nieuwe listener voor elke website. Geef de Details voor elke listener op en selecteer **toevoegen**.

4. Selecteer aan de linkerkant **http-instellingen** en selecteer **toevoegen** in het linkerdeel venster. Vul de details in om een nieuwe HTTP-instelling te maken en selecteer **Opslaan**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="Scherm opname van de pagina met HTTP-instellingen om een nieuwe HTTP-instelling te maken." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. Maak de regels in het gedeelte **regels** van het menu links. Koppel elke regel aan de bijbehorende listener. Selecteer **Toevoegen**.

6. Configureer de bijbehorende back-end-en HTTP-instellingen. Selecteer **Toevoegen**.

7. Test de verbinding. Open uw favoriete browser en navigeer naar de verschillende websites die worden gehost op uw Azure VMware-oplossings omgeving, bijvoorbeeld http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="Scherm afbeelding van browser pagina waarin de verbinding is geslaagd getest.":::

### <a name="routing-by-url"></a>Route ring op URL

De volgende stappen worden gebruikt voor het definiëren van back-end-adres groepen met virtuele machines die worden uitgevoerd op een Azure VMware-oplossing privécloud. De privécloud bevindt zich op een bestaande toepassings gateway. Vervolgens maakt u routeringsregels die ervoor zorgen dat webverkeer wordt omgeleid naar de servers in de pools.

1. Maak in uw privécloud een virtuele-machine groep om de webfarm aan te duiden. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Scherm afbeelding van de pagina in VMSphere-client met een samen vatting van een andere virtuele machine.":::

    Windows Server 2016 met IIS-rol geïnstalleerd is gebruikt ter illustratie van deze zelf studie. Zodra de Vm's zijn geïnstalleerd, voert u de volgende Power shell-opdrachten uit om IIS te configureren voor elke VM-zelf studie. 

    De eerste virtuele machine, contoso-Web-01, wordt als host voor de hoofd website gehost.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    De tweede virtuele machine, contoso-Web-02, fungeert als host voor de site met installatie kopieën.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    De derde virtuele machine, contoso-Web-03, host de video-site.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Voeg drie nieuwe back-endservers toe aan een bestaand toepassings gateway-exemplaar. 

   1. Selecteer **Back-endpools** in het linkermenu. 
   1. Selecteer **toevoegen** en voer de details in van de eerste groep **Contoso-Web**. 
   1. Voeg één virtuele machine toe als doel. 
   1. Selecteer **Toevoegen**. 
   1. Herhaal dit proces voor **Contoso-images** en **Contoso-video** en voeg één unieke virtuele machine toe als doel. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Scherm opname van de pagina met Back-upgroepen met drie nieuwe back-endservers." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. Maak in de sectie **listeners** een nieuwe listener van het type Basic met behulp van poort 8080.

4. Selecteer in het linkernavigatievenster de optie **http-instellingen** en selecteer **toevoegen** in het linkerdeel venster. Vul de details in om een nieuwe HTTP-instelling te maken en selecteer **Opslaan**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Scherm opname van de pagina HTTP-instelling toevoegen met configuratie van HTTP-instellingen.":::

5. Maak de regels in het gedeelte **regels** van het menu links. Koppel elke regel aan de eerder gemaakte listener. Configureer vervolgens de belangrijkste back-end-en HTTP-instellingen. Selecteer **Toevoegen**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Scherm afbeelding van de pagina een routerings regel toevoegen om routerings regels te configureren voor een back-end-doel.":::

6. Test de configuratie. Toegang tot de toepassings gateway op de Azure Portal en het open bare IP-adres kopiëren in het gedeelte **overzicht** . 

   1. Open een nieuw browser venster en voer de URL in `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="Scherm afbeelding van browser pagina met een geslaagde test van de configuratie.":::

   1. Wijzig de URL in `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Scherm opname van een andere geslaagde test met de nieuwe URL.":::

   1. Wijzig de URL opnieuw in `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Scherm opname van geslaagde test met de uiteindelijke URL.":::

## <a name="next-steps"></a>Volgende stappen

Nu u hebt gebruikgemaakt van Application Gateway voor het beveiligen van een web-app die wordt uitgevoerd op de Azure VMware-oplossing, kunt u het volgende doen:

- [Azure-toepassing gateway configureren voor verschillende scenario's](../application-gateway/configuration-overview.md).
- [Implementatie van Traffic Manager om werk belastingen van Azure VMware-oplossingen te verdelen](deploy-traffic-manager-balance-workloads.md).
- [Integratie van Azure NetApp files met op Azure VMware-oplossingen gebaseerde workloads](netapp-files-with-azure-vmware-solution.md).
- [Azure-resources in virtuele netwerken beveiligen](../ddos-protection/ddos-protection-overview.md).
