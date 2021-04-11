---
title: Azure Functions uitgaande IP beheren met een NAT-gateway van het virtuele netwerk van Azure
description: Een stapsgewijze zelf studie waarin wordt uitgelegd hoe u NAT kunt configureren voor een functie die is verbonden met een virtueel Azure-netwerk
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658114"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Zelf studie: controleren Azure Functions uitgaand IP-adres met een NAT-gateway van virtueel netwerk

Virtuele Network Address Translation (NAT) vereenvoudigt alleen uitgaande internet connectiviteit voor virtuele netwerken. Indien geconfigureerd op een subnet, maken alle uitgaande verbindingen gebruik van uw opgegeven statische openbare IP-adressen. Een NAT kan handig zijn voor Azure Functions of Web Apps die een service van derden nodig hebben die gebruikmaakt van een allowlist van IP-adres als beveiligings maatregel. Zie [Wat is Virtual Network NAT?](../virtual-network/nat-overview.md)voor meer informatie.

Deze zelf studie laat zien hoe u virtuele netwerk-Nat's kunt gebruiken om uitgaand verkeer te routeren vanuit een door HTTP geactiveerde functie. Met deze functie kunt u het eigen uitgaande IP-adres controleren. Tijdens deze zelf studie voert u de volgende handelingen uit:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * Een Premium plan-functie-app maken
> * Een openbaar IP-adres maken
> * Een NAT-gateway maken
> * Functie-app configureren om uitgaand verkeer via de NAT-gateway te routeren

## <a name="topology"></a>Topologie

In het volgende diagram ziet u de architectuur van de oplossing die u maakt:

![Gebruikers interface voor NAT gateway-integratie](./media/functions-how-to-use-nat-gateway/topology.png)

Functies die worden uitgevoerd in het Premium-abonnement hebben dezelfde hosting mogelijkheden als web-apps in Azure App Service, waaronder de functie VNet-integratie. Zie [uw app integreren met een virtueel Azure-netwerk](../app-service/web-sites-integrate-with-vnet.md)voor meer informatie over VNet-integratie, met inbegrip van probleem oplossing en geavanceerde configuratie.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie is het belangrijk dat u inzicht hebt in IP-adressering en subnetten. U kunt beginnen met [dit artikel waarin de basisprincipes van adressering en subnetten worden beschreven](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Er zijn veel meer artikelen en video's online beschikbaar.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u de functie integratie al hebt voltooid [met een virtuele Azure-netwerk](./functions-create-vnet.md) zelf studie, kunt u door gaan om [een http-trigger te maken](#create-function).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **Een resource maken** in het menu van de Azure-portal. Selecteer **Netwerk** > **Virtueel netwerk** in Azure Marketplace.

1. Typ of Selecteer in het **virtuele netwerk maken** de instellingen die zijn opgegeven, zoals weer gegeven in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Naam | Voer *myResourceGroup-vnet* in. |
    | Locatie | Selecteer **VS - oost**.|

1. Selecteer **volgende: IP-adressen** en voer voor **IPv4-adres ruimte** *10.10.0.0/16* in.

1. Selecteer **subnet toevoegen** en voer *zelf studie-net* voor **subnet naam** en *10.10.1.0/24* in voor het **adres bereik** van het subnet.

    ![Tabblad IP-adressen voor het maken van een vnet](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Selecteer **Toevoegen** en selecteer vervolgens **Controleren en maken**. Laat voor de rest de standaardwaarden staan en selecteer **Maken**.

1. Selecteer in **Virtueel netwerk maken** de optie **Maken**.

Vervolgens maakt u een functie-app in het [Premium-abonnement](functions-premium-plan.md). Dit abonnement biedt serverloze schalen en ondersteunt de integratie van virtuele netwerken.

## <a name="create-a-function-app-in-a-premium-plan"></a>Een functie-app maken in een Premium-abonnement

> [!NOTE]  
> Voor de beste ervaring in deze zelf studie kiest u .NET for runtime stack en kiest u Windows voor het besturings systeem. Maak ook functie-app in dezelfde regio als het virtuele netwerk.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>De functie-app verbinden met het virtuele netwerk

U kunt nu verbinding maken met uw functie-app in het virtuele netwerk.

1. Selecteer in de functie-app **netwerken** in het linkermenu en selecteer vervolgens onder **VNet-integratie** de optie **Klik hier om te configureren**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Kies netwerken in de functie-app":::

1. Selecteer **VNET toevoegen** op de pagina **VNET-integratie** .

1. In de status van de **netwerk functie** gebruikt u de instellingen in de tabel onder de installatie kopie:

    ![Het virtuele netwerk van de functie-app definiëren](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | Dit virtuele netwerk is de versie die u eerder hebt gemaakt. |
    | **Subnet** | Nieuw subnet maken | Maak een subnet in het virtuele netwerk voor de functie-app die u wilt gebruiken. VNet-integratie moet worden geconfigureerd voor het gebruik van een leeg subnet. |
    | **Subnetnaam** | Function-Net | Naam van het nieuwe subnet. |
    | **Adres blok van virtueel netwerk** | 10.10.0.0/16 | Er mag slechts één adres blok zijn gedefinieerd. |
    | **Adres blok van het subnet** | 10.10.2.0/24   | De grootte van het subnet beperkt het totale aantal exemplaren dat kan worden uitgeschaald door uw Premium-plan functie-app. In dit voor beeld wordt een `/24` subnet met 254 beschik bare host-adressen gebruikt. Dit subnet is te veel ingericht, maar kan eenvoudig worden berekend. |

1. Selecteer **OK** om het subnet toe te voegen. Sluit de pagina's van de **VNet-integratie** en de status van de **netwerk functie** om terug te gaan naar uw functie-app-pagina.

De functie-app heeft nu toegang tot het virtuele netwerk. Vervolgens voegt u een door HTTP geactiveerde functie toe aan de functie-app.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Een HTTP-triggerfunctie maken

1. Selecteer in het menu links van het venster **Functies** de optie **Functies** en selecteer vervolgens **Toevoegen** in het bovenste menu. 
 
1. Selecteer in het venster **nieuwe functie** de optie **http-trigger** en accepteer de standaard naam voor **nieuwe functie**, of voer een nieuwe naam in. 

1. Vervang in **code + test** de door de sjabloon gegenereerde C#-script code (. CSX) door de volgende code: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Met deze code wordt een externe website aangeroepen die het IP-adres van de aanroeper retourneert, in dit geval deze functie. Met deze methode kunt u eenvoudig bepalen welk uitgaand IP-adres wordt gebruikt door uw functie-app.

U bent nu klaar om de functie uit te voeren en de huidige uitgaande Ip's te controleren.

## <a name="verify-current-outbound-ips"></a>Huidige uitgaande Ip's verifiëren

U kunt de functie nu uitvoeren. Controleer eerst de portal en bekijk welke uitgaande IP-adressen worden gebruikt door de functie-app.  

1. In de functie-app selecteert u **Eigenschappen** en bekijkt u het veld **uitgaande IP-adressen** .

    ![Uitgaande IP-adressen van functie-apps weer geven](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Ga nu terug naar de functie voor HTTP-triggers, selecteer **code + test** en vervolgens **testen/uitvoeren**.

    ![Test functie](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Selecteer **uitvoeren** om de functie uit te voeren en schakel over naar de **uitvoer**. 

    ![Functie-uitvoer testen](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Controleer of het IP-adres in de hoofd tekst van het HTTP-antwoord een van de waarden van de uitgaande IP-adressen is die u eerder hebt bekeken.

U kunt nu een openbaar IP-adres maken en een NAT-gateway gebruiken om deze uitgaande IP-adressen te wijzigen.

## <a name="create-public-ip"></a>Openbaar IP-adres maken

1. Selecteer **toevoegen** in de resource groep, Zoek in de Azure Marketplace naar het **open bare IP-adres** en selecteer **maken**. Gebruik de instellingen van de tabel onder de afbeelding:

    ![Openbaar IP-adres maken](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Instelling      | Voorgestelde waarde  |
    | ------------ | ---------------- |
    | **IP-versie** | IPv4 |
    | **SKU** | Standard |
    | **Laag** | Regionaal |
    | **Naam** | Uitgaand-IP |
    | **Abonnement** | controleren of uw abonnement wordt weer gegeven | 
    | **Resourcegroep** | myResourceGroup (of naam die u aan de resource groep hebt toegewezen) |
    | **Locatie** | VS-Oost (of locatie die u hebt toegewezen aan uw andere resources) |
    | **Beschikbaarheidszone** | Geen zone |

1. Selecteer **maken** om de implementatie te verzenden.

1. Zodra de implementatie is voltooid, gaat u naar de nieuwe resource voor het open bare IP-adres en bekijkt u het IP-adres in het **overzicht**.

    ![Openbaar IP-adres weer geven](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>NAT-gateway maken

Nu gaan we de NAT-gateway maken. Wanneer u begint met de voor [gaande zelf studie voor virtuele netwerken](functions-create-vnet.md), `Function-Net` was de voorgestelde subnetnaam en `MyResourceGroup-vnet` was de voorgestelde virtuele netwerk naam in die zelf studie.

1. Selecteer **toevoegen** in de resource groep, Zoek in azure Marketplace naar **NAT-gateway** en selecteer **maken**. Gebruik de instellingen in de tabel onder de afbeelding om het tabblad **basis beginselen** te vullen:

    ![NAT-gateway maken](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Instelling      | Voorgestelde waarde  |
    | ------------ | ---------------- |  
    | **Abonnement** | Uw abonnement | 
    | **Resourcegroep** | myResourceGroup (of naam die u aan de resource groep hebt toegewezen) |
    | **Naam van NAT-gateway** | myNatGateway |
    | **Regio** | VS-Oost (of locatie die u hebt toegewezen aan uw andere resources) |
    | **Beschikbaarheidszone** | Geen |

1. Selecteer **volgende: uitgaand IP-adres**. Selecteer in het veld **open bare IP-adressen** het eerder gemaakte open bare IP-adres. Zorg ervoor dat **open bare IP-voor voegsels** niet geselecteerd zijn.

1. Selecteer **volgende: subnet**. Selecteer de *myResourceGroup-vnet-* resource in het veld voor het **virtuele netwerk** en de *functie-net-* subnet.

    ![Subnet selecteren](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Selecteer **controleren + maken** en vervolgens **maken** om de implementatie in te dienen.

Zodra de implementatie is voltooid, is de NAT-gateway klaar om verkeer te routeren vanuit het subnet van de functie-app naar het internet.

## <a name="update-function-configuration"></a>Functie configuratie bijwerken

Nu moet u een toepassings instelling `WEBSITE_VNET_ROUTE_ALL` instellen op een waarde van `1` .  Met deze instelling wordt uitgaand verkeer via het virtuele netwerk en de gekoppelde NAT-gateway afgedwongen. Zonder deze instelling wordt Internet verkeer niet via het geïntegreerde virtuele netwerk doorgestuurd en worden dezelfde uitgaande IP-adressen weer geven. 

1. Navigeer naar uw functie-app in het Azure Portal en selecteer **configuratie** in het menu links.

1. Onder **Toepassings instellingen** selecteert u **+ nieuwe toepassings instelling** en voert u de volgende waarden in om de velden in te vullen:

    |Veldnaam  |Waarde |
    |---|---|
    |**Naam**    |WEBSITE_VNET_ROUTE_ALL|
    |**Waarde**   |1|

1. Selecteer **OK** om het dialoog venster nieuwe toepassings instelling te sluiten.

1. Selecteer **Opslaan** **en sla de instellingen op.**

De functie-app is nu geconfigureerd om verkeer te routeren via het bijbehorende virtuele netwerk.

## <a name="verify-new-outbound-ips"></a>Nieuwe uitgaande Ip's verifiëren

Herhaal [de stappen eerder](#verify-current-outbound-ips) om de functie opnieuw uit te voeren. U ziet nu het uitgaande IP-adres dat u hebt geconfigureerd in de NAT die wordt weer gegeven in de functie-uitvoer.

## <a name="clean-up-resources"></a>Resources opschonen

U hebt resources gemaakt om deze zelf studie te volt ooien. U wordt gefactureerd voor deze resources, afhankelijk van de [account status](https://azure.microsoft.com/account/) en [service prijzen](https://azure.microsoft.com/pricing/). Om te voor komen dat er extra kosten in rekening worden gebracht, verwijdert u de resources wanneer u deze meer nodig hebt. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Netwerkopties van Azure Functions](functions-networking-options.md)
