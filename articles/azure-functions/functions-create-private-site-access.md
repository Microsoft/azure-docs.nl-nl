---
title: Toegang tot Azure Functions via privésite inschakelen
description: Meer informatie over het instellen van toegang tot de privésite van Azure Virtual Network voor Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 766ad12daeb6d2763f7ed5fe026cd4a0021eaf33
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937039"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Zelfstudie: Toegang tot privésite van Azure Functions tot stand brengen

In deze zelfstudie wordt uitgelegd hoe u [toegang tot privésite](./functions-networking-options.md#private-endpoint-connections) kunt inschakelen met Azure Functions. Als u toegang tot privésite gebruikt, kunt u vereisen dat uw functiecode alleen wordt geactiveerd vanuit een specifiek virtueel netwerk.

Toegang tot de privésite is handig in scenario's waarbij de toegang tot de functie-app moet worden beperkt tot een specifiek virtueel netwerk. De functie-app kan bijvoorbeeld alleen van toepassing zijn op werknemers van een specifieke organisatie of op services die zich binnen het opgegeven virtuele netwerk bevinden (zoals een andere Azure-functie, een virtuele Azure-machine of een AKS-cluster).

Als een Functions-app toegang moet hebben tot Azure-resources in het virtuele netwerk, of moet worden verbonden via [service-eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md), dan is [integratie van virtueel netwerk](./functions-create-vnet.md) nodig.

In deze zelfstudie leert u hoe u toegang tot privésites kunt configureren voor uw functie-app:

> [!div class="checklist"]
> * Een virtuele machine maken
> * Een Azure Bastion-service maken
> * Een Azure Functions-app maken
> * Een service-eindpunten voor virtuele netwerken configureren
> * Een Azure Function maken en implementeren
> * De functie van buiten en binnen het virtuele netwerk aanroepen

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="topology"></a>Topologie

In het volgende diagram wordt de architectuur van de te maken toepassing weergegeven:

![Diagram van architectuur op hoog niveau voor de oplossing voor toegang tot privésite](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie is het belangrijk dat u inzicht hebt in IP-adressering en subnetten. U kunt beginnen met [dit artikel waarin de basisprincipes van adressering en subnetten worden beschreven](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Er zijn veel meer artikelen en video's online beschikbaar.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

De eerste stap in deze zelfstudie is het maken van een nieuwe virtuele machine in een virtueel netwerk.  De virtuele machine wordt gebruikt om toegang te krijgen tot uw functie zodra u de toegang tot de app hebt beperkt, zodat deze alleen beschikbaar is in het virtuele netwerk.

1. Selecteer de knop **Een resource maken**.

1. Typ **Windows Server** in het zoekveld en selecteer **Windows Server** in de zoekresultaten.

1. Selecteer **Windows Server 2019 Data Center** in de lijst met opties voor Windows Server en druk op de knop **Maken**.

1. Gebruik in het tabblad _Basisbeginselen_ de VM-instellingen die in de tabel onder de afbeelding zijn opgegeven:

    >[!div class="mx-imgBorder"]
    >![Tabblad Basisbeginselen voor een nieuwe Windows-VM](./media/functions-create-private-site-access/create-vm-3.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | _Abonnement_ | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. |
    | [_Resourcegroep_](../azure-resource-manager/management/overview.md) | myResourceGroup | Kies de resourcegroep die alle resources moet bevatten voor deze zelfstudie.  Door dezelfde resourcegroep te gebruiken, is het eenvoudiger om resources op te schonen wanneer u klaar bent met deze zelfstudie. |
    | _Naam van virtuele machine_ | myVM | De VM-naam moet uniek zijn in de resourcegroep |
    | [_Regio_](https://azure.microsoft.com/regions/) | (VS) VS - noord-centraal | Kies een regio bij u in de buurt of in de buurt van de functies die u wilt gebruiken. |
    | _Openbare binnenkomende poorten_ | Geen | Selecteer **Geen** om er zeker van te zijn dat er geen inkomende connectiviteit is met de virtuele machine via internet. Externe toegang tot de virtuele machine wordt geconfigureerd via de Azure Bastion-service. |

1. Kies het tabblad _Netwerken_ en selecteer **Nieuwe maken** om een nieuw virtueel netwerk te configureren.

    >[!div class="mx-imgBorder"]
    >![Schermopname van het tabblad 'Netwerken' met de actie 'Nieuwe maken' gemarkeerd in het gedeelte 'Virtueel netwerk'.](./media/functions-create-private-site-access/create-vm-networking.png)

1. Gebruik in _Virtueel netwerk maken_ de instellingen in de tabel onder de afbeelding:

    >[!div class="mx-imgBorder"]
    >![Maak een nieuw virtueel netwerk voor de nieuwe VM](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | _Naam_ | myResourceGroup-vnet | U kunt de standaardnaam gebruiken die voor het virtuele netwerk is gegenereerd. |
    | _Adresbereik_ | 10.10.0.0/16 | Gebruik één adresbereik voor het virtuele netwerk. |
    | _Subnetnaam_ | Zelfstudie | Naam van het subnet. |
    | _Adresbereik_ (subnet) | 10.10.1.0/24 | De subnetgrootte bepaalt hoeveel interfaces aan het subnet kunnen worden toegevoegd. Dit subnet wordt gebruikt door de virtuele machine. Een /24-subnet biedt 254 hostadressen. |

1. Selecteer **OK** om het virtuele netwerk te maken.
1. Schakel op het tabblad _Netwerken_ de optie **Geen** in voor _Openbare IP_.
1. Kies het tabblad _Beheer_ en kies in _Diagnostische opslagaccount_ de optie **Nieuwe maken** om een nieuw opslagaccount te maken.
1. Behoud de standaardwaarden voor de gedeelten _Identiteit_, _Automatisch afsluiten_ en _Back-up_.
1. Selecteer _Controleren + maken_. Nadat de validatie is voltooid, selecteert u **Maken**. Het proces voor het maken van de virtuele machine duurt een paar minuten.

## <a name="configure-azure-bastion"></a>Azure Bastion configureren

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) is een volledig beheerde Azure-service die rechtstreeks vanuit de Azure-portal beveiligde RDP- en SSH-toegang biedt tot virtuele machines. Als u de Azure Bastion-service gebruikt, is het niet meer nodig om netwerkinstellingen met betrekking tot RDP-toegang te configureren.

1. Kies in de portal **Toevoegen** bovenaan de resourcegroepweergave.
1. Typ **Bastion** in het zoekveld.
1. Selecteer **Bastion** in de zoekresultaten.
1. Selecteer **Maken** om het proces voor het maken van een nieuwe Azure Bastion-resource te starten. Er wordt een foutbericht weergegeven in het gedeelte _Virtueel netwerk_ omdat er nog geen AzureBastionSubnet-subnet is. Het subnet wordt in de volgende stappen gemaakt. Gebruik de instellingen van de tabel onder de afbeelding:

    >[!div class="mx-imgBorder"]
    >![Starten met het maken van Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | _Naam_ | myBastion | De naam van de nieuwe Bastion-resource |
    | _Regio_ | VS - noord-centraal | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |
    | _Virtueel netwerk_ | myResourceGroup-vnet | Het virtuele netwerk waarin de Bastion-resource wordt gemaakt |
    | _Subnet_ | AzureBastionSubnet | Het subnet in het virtuele netwerk waarop de nieuwe Bastion-hostresource wordt geïmplementeerd. U moet een subnet maken met de naamwaarde **AzureBastionSubnet**. Deze waarde geeft Azure aan op welk subnet de Bastion-resources moeten worden geïmplementeerd. U moet een subnet van minimaal **/27** of groter (/27, /26 enzovoort) gebruiken. |

    > [!NOTE]
    > Raadpleeg de zelfstudie [Een Azure Bastion-host maken](../bastion/tutorial-create-host-portal.md) voor een gedetailleerde, stapsgewijze handleiding voor het maken van een Azure Bastion-resource.

1. Maak een subnet waarin Azure de Azure Bastion-host kan inrichten. Als u **Subnet-configuratie beheren** kiest, wordt een nieuw deelvenster geopend waarin u een nieuw subnet kunt definiëren.  Kies **+ Subnet** om een nieuw subnet te maken.
1. Het subnet moet de naam **AzureBastionSubnet** hebben en het subnetvoorvoegsel moet ten minste **/27** zijn.  Selecteer **OK** om het subnet te maken.

    >[!div class="mx-imgBorder"]
    >![Subnet maken voor Azure Bastion-host](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. Selecteer op de pagina _Een bastion maken_ de zojuist gemaakte **AzureBastionSubnet** in de lijst met beschikbare subnetten.

    >[!div class="mx-imgBorder"]
    >![Een Azure Bastion-host met een specifiek subnet maken](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. Selecteer **Beoordelen en maken**. Wanneer de validatie is voltooid, selecteert u **Maken**. Het duurt enkele minuten voordat de Azure Bastion-resource is gemaakt.

## <a name="create-an-azure-functions-app"></a>Een Azure Functions-app maken

De volgende stap is het maken van een functie-app in Azure met behulp van het [Verbruiksabonnement](consumption-plan.md). Verderop in de zelfstudie implementeert u uw functiecode in deze resource.

1. Kies in de portal **Toevoegen** bovenaan de resourcegroepweergave.
1. Selecteer **Berekenen > Functie-app**
1. In het gedeelte _Basisbeginselen_ gebruikt u de instellingen voor de functie-app die in de onderstaande tabel zijn opgegeven.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | _Resourcegroep_ | myResourceGroup | Kies de resourcegroep die alle resources moet bevatten voor deze zelfstudie.  Door dezelfde resourcegroep te gebruiken voor de functie-app en de VM, is het eenvoudiger om resources op te schonen wanneer u klaar bent met deze zelfstudie. |
    | _Naam van de functie-app_ | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn a-z (hoofdlettergevoelig), 0-9 en -. |
    | _Publiceren_ | Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | _Runtimestack_ | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. |
    | _Regio_ | VS - noord-centraal | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    Selecteer de knop **Volgende: Hosting >** .
1. Selecteer voor het gedeelte _Hosting_ het juiste _Opslagaccount_, _Besturingssysteem_ en _Abonnement_, zoals wordt beschreven in de volgende tabel.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | _Opslagaccount_ | Wereldwijd unieke naam | Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat voldoet aan de [vereisten voor een opslagaccount](storage-considerations.md#storage-account-requirements). |
    | _Besturingssysteem_ | Voorkeurbesturingssysteem | Er wordt vooraf een besturingssysteem geselecteerd voor u op basis van de selectie van de runtimestack, maar u kunt de instelling wijzigen, indien nodig. |
    | _Plannen_ | Verbruik | Het [hostingabonnement](./functions-scale.md) bepaalt hoe de functie-app wordt geschaald en welke resources beschikbaar zijn voor elke instantie. |
1. Selecteer **Beoordelen + maken** om de selecties van appconfiguratie te controleren.
1. Selecteer **Maken** om de functie-app in te richten en te implementeren.

## <a name="configure-access-restrictions"></a>Toegangsbeperkingen configureren

De volgende stap is het configureren van [toegangsbeperkingen](../app-service/app-service-ip-restrictions.md) om ervoor te zorgen dat alleen resources in het virtuele netwerk de functie kunnen aanroepen.

Toegang tot de [privésite](functions-networking-options.md#private-endpoint-connections) wordt ingeschakeld door een Azure Virtual Network-[service-eindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) te maken tussen de functie-app en het opgegeven virtuele netwerk. Toegangsbeperkingen worden geïmplementeerd via service-eindpunten. Service-eindpunten zorgen ervoor dat alleen verkeer dat afkomstig is van binnen het opgegeven virtuele netwerk toegang heeft tot de aangewezen resource. In dit geval is de aangewezen resource de Azure-functie.

1. Selecteer in de functie-app de koppeling **Netwerk** onder de gedeelteheader _Instellingen_.
1. De pagina _Netwerken_ is het beginpunt voor het configureren van de Azure Front Door, de Azure CDN en ook toegangsbeperkingen.
1. Selecteer **Toegangsbeperkingen configureren** om toegang tot privésite te configureren.
1. Op de pagina _Toegangsbeperkingen_ ziet u alleen de standaardbeperking. De standaardbeperking bevat geen beperkingen voor toegang tot de functie-app.  Selecteer **Regel toevoegen** om een beperkingsconfiguratie voor toegang tot de privésite te maken.
1. Geef in het deelvenster _Toegangsbeperking toevoegen_ een _Naam_, _Prioriteit_ en _Beschrijving_ op voor de nieuwe regel.
1. Selecteer **Virtueel netwerk** in de vervolgkeuzelijst _Type_, selecteer het eerder gemaakte virtuele netwerk en selecteer vervolgens het subnet **Zelfstudie**. 
    > [!NOTE]
    > Het kan enkele minuten duren om het service-eindpunt in te schakelen.
1. Op de pagina _Toegangsbeperkingen_ wordt nu een nieuwe beperking weergegeven. Het kan enkele seconden duren voordat de status van het _Eindpunt_ wordt gewijzigd van Uitgeschakeld naar Inrichten tot Ingeschakeld.

    >[!IMPORTANT]
    > Elke functie-app heeft een [site voor Geavanceerd hulpprogramma (Kudu)](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site) die wordt gebruikt om de implementaties van functie-apps te beheren. Deze site wordt geopend via een URL zoals: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Als toegangsbeperkingen op de Kudu-site worden ingeschakeld, wordt de implementatie van de projectcode van een lokaal ontwikkelaarswerkstation voorkomen. Er is een agent binnen het virtuele netwerk vereist om de implementatie uit te voeren.

## <a name="access-the-functions-app"></a>De functie-app openen

1. Ga terug naar de eerder gemaakte functie-app.  Kopieer de URL in het gedeelte _Overzicht_.

    >[!div class="mx-imgBorder"]
    >![De URL van de functie-app ophalen](./media/functions-create-private-site-access/access-function-overview.png)

    Als u nu toegang probeert te krijgen tot de functie-app vanaf uw computer buiten het virtuele netwerk, ontvangt u een HTTP 403-pagina die aangeeft dat de toegang niet is toegestaan.
1. Ga terug naar de resourcegroep en selecteer de eerder gemaakte virtuele machine. Als u toegang wilt krijgen tot de site vanaf de virtuele machine, moet u verbinding maken met de virtuele machine via de Azure Bastion-service.
1. Selecteer **Verbinding maken** en kies vervolgens **Bastion**.
1. Geef de vereiste gebruikersnaam en het wachtwoord op om in te loggen bij de virtuele machine.
1. Selecteer **Verbinden**. Er wordt een nieuw browservenster weergegeven waarin u kunt communiceren met de virtuele machine.
Het is mogelijk om toegang te krijgen tot de site vanuit de webbrowser op de virtuele machine omdat de virtuele machine toegang heeft tot de site via het virtuele netwerk.  Hoewel de site alleen toegankelijk is vanuit het aangewezen virtuele netwerk, blijft een openbare DNS-vermelding aanwezig.

## <a name="create-a-function"></a>Een functie maken

De volgende stap in deze zelfstudie is het maken van een Azure-functie die door HTTP wordt geactiveerd. Het aanroepen van de functie via een HTTP GET of POST moet resulteren in een antwoord zoals 'Hallo {name}'.  

1. Volg een van de volgende quickstarts om uw Azure Functions-app te maken en te implementeren.

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Opdrachtregel](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. Wanneer u uw Azure Functions-project publiceert, kiest u de resource van de functie-app die u eerder in deze zelfstudie hebt gemaakt.
1. Controleer of de functie is geïmplementeerd.

    >[!div class="mx-imgBorder"]
    >![Geïmplementeerde functie in lijst met functies](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>De functie direct aanroepen

1. Als u de toegang tot de functie wilt testen, moet u de functie-URL kopiëren. Selecteer de geïmplementeerde functie en selecteer **Functie-URL ophalen**. Klik vervolgens op de knop **Kopiëren** om de URL naar het klembord te kopiëren.

    >[!div class="mx-imgBorder"]
    >![De functie-URL kopiëren](./media/functions-create-private-site-access/get-function-url.png)

1. Plak de URL in een webbrowser. Als u nu toegang probeert te krijgen tot de functie-app vanaf uw computer buiten het virtuele netwerk, ontvangt u een HTTP 403-antwoord dat aangeeft dat toegang tot de app niet is toegestaan.

## <a name="invoke-the-function-from-the-virtual-network"></a>De functie van binnen het virtuele netwerk aanroepen

Als u de functie opent via een webbrowser (door gebruik te maken van de Azure Bastion-service) op de geconfigureerde VM in het virtuele netwerk, lukt dit.

>[!div class="mx-imgBorder"]
>![Toegang tot de Azure-functie via Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen


> [!div class="nextstepaction"]
> [Meer informatie over de netwerkopties in Functions](./functions-networking-options.md)