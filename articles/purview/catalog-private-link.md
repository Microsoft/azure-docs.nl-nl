---
title: Privé-eind punten gebruiken voor beveiligde toegang tot controle sfeer liggen
description: In dit artikel wordt beschreven hoe u een privé-eind punt voor uw controle sfeer liggen-account kunt instellen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 09fa10e7f7751321601c5c4871b2cf36ccf6f01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720927"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Privé-eind punten gebruiken voor uw controle sfeer liggen-account

U kunt privé-eind punten voor uw controle sfeer liggen-accounts gebruiken om clients en gebruikers in een virtueel netwerk (VNet) in staat te stellen om veilig toegang te krijgen tot de catalogus via een privé-koppeling. Het persoonlijke eind punt gebruikt een IP-adres uit de VNet-adres ruimte voor uw controle sfeer liggen-account. Netwerk verkeer tussen de clients in het VNet en het controle sfeer liggen-account passeert over het VNet en een persoonlijke koppeling in het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen.

## <a name="create-purview-account-with-a-private-endpoint"></a>Controle sfeer liggen-account maken met een persoonlijk eind punt

1. Navigeer naar het [Azure Portal](https://portal.azure.com) en vervolgens naar uw controle sfeer liggen-account.

1. Algemene informatie vullen en connectiviteits methode instellen op privé-eind punt op het tabblad **netwerk** . Stel uw persoonlijke eind punten voor opname in door Details te verstrekken van het **abonnement, Vnet en subnet** dat u wilt koppelen aan uw persoonlijke eind punt.

    > [!NOTE]
    > Maak een inslikken persoonlijk eind punt alleen als u van plan bent om netwerk isolatie voor end-to-end-scan scenario's in te scha kelen voor uw Azure-en on-premises bronnen. We ondersteunen momenteel geen persoonlijke inslikken-eind punten die samen werken met uw AWS-bronnen.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Een persoonlijk eind punt maken in de Azure Portal":::

1. U kunt desgewenst ook een **privé-DNS zone** instellen voor elk privé-eind punt voor inslikken.

1. Klik op toevoegen om een persoonlijk eind punt toe te voegen voor uw controle sfeer liggen-account.

1. Op de pagina persoonlijk eind punt maken stelt u controle sfeer liggen subresource in op **account**, kiest u uw virtuele netwerk en subnet en selecteert u de privé-DNS zone waarin de DNS wordt geregistreerd (u kunt ook uw eigen DNS-servers gebruiken of DNS-records maken met behulp van host-bestanden op uw virtuele machines).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Selecties voor het maken van een persoonlijk eind punt":::

1. Selecteer **OK**.

1. Selecteer **Controleren + maken**. De pagina Beoordelen en maken wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

1. Als u het bericht Validatie geslaagd ziet, selecteert u **Maken**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Validatie door gegeven voor het maken van accounts":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Een persoonlijk eind punt maken voor de controle sfeer liggen-webportal

1. Ga naar het controle sfeer liggen-account dat u zojuist hebt gemaakt, selecteer de verbindingen voor privé-eind punten in de sectie instellingen.

1. Klik op + persoonlijk eind punt om een nieuw persoonlijk eind punt te maken.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Persoonlijk eind punt voor de portal maken":::

1. Vul basis informatie in.

1. Selecteer in het tabblad resource de optie Resource type als **micro soft. controle sfeer liggen/accounts**.

1. Selecteer de resource die u wilt maken als nieuw controle sfeer liggen-account en selecteer doel-subresource als **Portal**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Details voor het persoonlijk eind punt van de portal":::

1. Selecteer het virtuele netwerk en de zone Privé-DNS op het tabblad Configuratie. Ga naar de pagina samen vatting en klik op **maken** om het persoonlijke eind punt voor de portal te maken.

## <a name="enabling-access-to-azure-active-directory"></a>Toegang tot Azure Active Directory inschakelen

> [!NOTE]
> Als uw virtuele machine, VPN-gateway of VNET-peering gateway open bare Internet toegang heeft, heeft deze toegang tot de controle sfeer liggen-Portal en het controle sfeer liggen-account ingeschakeld met persoonlijke eind punten. u hoeft de overige instructies hieronder niet uit te voeren. Als voor uw particuliere netwerk regels voor netwerk beveiligings groepen zijn ingesteld om alle open bare Internet verkeer te weigeren, moet u enkele regels toevoegen om toegang tot AAD in te scha kelen. Volg de onderstaande instructies.

De onderstaande instructies zijn voor het veilig openen van controle sfeer liggen vanuit een Azure-VM. Er moeten soort gelijke stappen worden gevolgd als u VPN-of andere Vnet-peering-gateways gebruikt.

1. Navigeer naar uw virtuele machine in de Azure Portal en selecteer het tabblad netwerken onder instellingen. Selecteer vervolgens regels voor uitgaande poort en klik op regel voor uitgaande poort toevoegen.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Uitgaande regel toevoegen":::

2. Selecteer in de Blade uitgaande poort regel toevoegen de optie *doel* naar service-tag, doel service-tag als **AzureActiveDirectory**, doel poort bereik om toe te voegen, **prioriteit moet hoger zijn dan de regel die alle Internet verkeer heeft geweigerd**. Maak de regel.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Details van uitgaande regel toevoegen":::

3. Volg dezelfde stappen om een andere regel te maken voor het toestaan van de service label **AzureResourceManager**. Als u toegang nodig hebt tot de Azure Portal, kunt u ook een regel toevoegen voor de service label *AzurePortal*.

4. Maak verbinding met de virtuele machine, open de browser, ga naar de browser console (CTRL + SHIFT + J) en schakel over naar het tabblad netwerk om netwerk aanvragen te bewaken. Voer web.purview.azure.com in het vak URL in en probeer u aan te melden met uw AAD-referenties. Aanmelden kan waarschijnlijk mislukken en op het tabblad netwerk op de-console ziet u een AAD-poging om toegang te krijgen tot aadcdn.msauth.net, maar om te worden geblokkeerd.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Details van mislukte aanmelding":::

5. In dit geval opent u een opdracht prompt op de VM en pingt u deze URL (aadcdn.msauth.net), haalt u het IP-adres op en voegt u vervolgens een regel voor uitgaande poort toe voor het IP-adres voor de netwerk beveiligings regels van de virtuele machine. Stel de bestemming in op IP-adres en stel doel-IP-adressen in op IP van aadcdn. Vanwege load balancer en Traffic Manager kan het IP-adres van het AAD-CDN dynamisch zijn. Zodra u het IP-adres hebt ontvangen, is het beter om het toe te voegen aan het hostbestand van de virtuele machine om ervoor te zorgen dat de browser het IP-adres van AAD kan ophalen.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Ping testen":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="AAD CDN-regel":::

6. Zodra de nieuwe regel is gemaakt, gaat u terug naar de virtuele machine en probeert u zich opnieuw aan te melden met uw AAD-referenties. Als de aanmelding is geslaagd, is de controle sfeer liggen-Portal klaar voor gebruik. In sommige gevallen wordt AAD omgeleid naar andere domeinen om zich aan te melden op basis van het account type van de klant. Bijvoorbeeld: voor een live.com-account wordt AAD omgeleid naar live.com naar login, waarna deze aanvragen opnieuw worden geblokkeerd. Voor micro soft-werknemers accounts krijgt AAD toegang tot msft.sts.microsoft.com voor aanmeldings gegevens. Controleer het tabblad netwerk aanvragen in browser netwerk om te zien welke aanvragen van het domein worden geblokkeerd, voer de vorige stap opnieuw uit om het IP-adres op te halen en uitgaande poort regels toe te voegen aan de netwerk beveiligings groep om aanvragen voor dat IP-adres toe te staan. Als u de IP-adresbereiken van het precieze aanmeldings domein kent, kunt u ze ook rechtstreeks toevoegen aan netwerk regels.

7. Meld u nu aan bij AAD. De controle sfeer liggen-portal wordt geladen, maar het weer geven van alle controle sfeer liggen-accounts werkt niet omdat alleen toegang kan worden gekregen tot een specifiek controle sfeer liggen-account. Voer *Web. controle sfeer liggen. Azure. com/resource/{PurviewAccountName}* in om direct het controle sfeer liggen-account te bezoeken waarvoor u een persoonlijk eind punt hebt ingesteld.
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>Inslikken van persoonlijke eind punten en scan bronnen in particuliere netwerken, Vnets en achter persoonlijke eind punten

Als u ervoor wilt zorgen dat netwerk isolatie voor uw meta gegevens stroomt vanuit de bron die wordt gescand naar controle sfeer liggen DataMap, moet u deze stappen volgen:
1. Een **inslikken persoonlijk eind punt** inschakelen door de stappen in [deze](#creating-an-ingestion-private-endpoint) sectie te volgen
1. Scan de bron met een **zelf-hostende IR**.
 
    1. Alle on-premises bron typen, zoals SQL Server, Oracle, SAP en anderen, worden momenteel alleen ondersteund via zelf-hostende IR-scans. De zelf-hostende IR moet binnen uw particuliere netwerk worden uitgevoerd en moet vervolgens worden gekoppeld aan uw Vnet in Azure. Uw Azure vnet moet vervolgens worden ingeschakeld op uw inslikken persoonlijk eind punt door de [volgende stappen uit te voeren](#creating-an-ingestion-private-endpoint) 
    1. Voor alle **Azure** -bron typen zoals Azure Blob storage, Azure SQL database en anderen, moet u expliciet de scan uitvoeren met behulp van zelf-hostende IR om te zorgen voor netwerk isolatie. Volg de [onderstaande stappen om](manage-integration-runtimes.md) een zelf-hostende IR in te stellen. Stel vervolgens uw scan in op de Azure-bron door de zelf-hostende IR te kiezen in de vervolg keuzelijst **verbinding maken via Integration runtime** om te zorgen voor netwerk isolatie. 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Azure scan uitvoeren met behulp van zelf-hostende IR":::

> [!NOTE]
> We bieden momenteel geen ondersteuning voor de MSI-referentie methode wanneer u uw Azure-bronnen scant met een zelf-hostende IR. U moet een van de andere ondersteunde referentie methode voor die Azure-bron gebruiken.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Persoonlijk eind punt inschakelen voor bestaande controle sfeer liggen-accounts

Er zijn twee manieren om controle sfeer liggen persoonlijke eind punten toe te voegen nadat u uw controle sfeer liggen-account hebt gemaakt:

- De Azure Portal gebruiken (controle sfeer liggen-account)
- Het persoonlijke koppelings centrum gebruiken

### <a name="using-the-azure-portal-purview-account"></a>De Azure Portal gebruiken (controle sfeer liggen-account)

1. Ga naar het controle sfeer liggen-account in het Azure Portal, selecteer de verbindingen voor privé-eind punten in het gedeelte **netwerken** van **instellingen**.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Persoonlijk eind punt voor account maken":::

1. Klik op + persoonlijk eind punt om een nieuw persoonlijk eind punt te maken.

1. Vul basis informatie in.

1. Selecteer in het tabblad resource de optie Resource type als **micro soft. controle sfeer liggen/accounts**.

1. Selecteer de resource die als controle sfeer liggen-account moet worden en selecteer doel-subresource om **account** te worden.

1. Selecteer het **virtuele netwerk** en de **zone privé-DNS** op het tabblad Configuratie. Ga naar de pagina samen vatting en klik op **maken** om het persoonlijke eind punt voor de portal te maken.

> [!NOTE]
> U moet dezelfde stappen volgen als hierboven voor de doel subresource die als **Portal** is geselecteerd.

#### <a name="creating-an-ingestion-private-endpoint"></a>Een persoonlijk eind punt voor opname maken

1. Ga naar het controle sfeer liggen-account in het Azure Portal, selecteer de verbindingen voor privé-eind punten in het gedeelte **netwerken** van **instellingen**.
1. Ga naar het tabblad **opname van persoonlijk eind punt verbindingen** en klik op **+ Nieuw** om een nieuw inslikken persoonlijk eind punt te maken.

1. Vul basis gegevens en Vnet-Details in.
 
    :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Details van persoonlijk eind punt invullen":::

1. Klik op **maken** om de configuratie te volt ooien.

> [!NOTE]
> Privé-eind punten voor opname kunnen alleen worden gemaakt via de controle sfeer liggen Azure Portal-ervaring die hierboven wordt beschreven. Het kan niet worden gemaakt vanuit het persoonlijke koppelings centrum.

### <a name="using-the-private-link-center"></a>Het persoonlijke koppelings centrum gebruiken

1. Navigeer naar [Azure Portal](https://portal.azure.com).

2. Zoek in de zoek balk aan de bovenkant van de pagina naar ' persoonlijke koppeling ' en navigeer naar de Blade persoonlijke koppeling door te klikken op de eerste optie.

3. Klik op + toevoegen en vul de basis gegevens in.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="PE maken op basis van een persoonlijk koppelings centrum":::

4. Selecteer de resource die het al gemaakte controle sfeer liggen-account moet zijn en selecteer doel-subresource om **account** te maken.

5. Selecteer het virtuele netwerk en de zone Privé-DNS op het tabblad Configuratie. Ga naar de pagina samen vatting en klik op **maken** om het persoonlijke eind punt van het account te maken.

> [!NOTE]
> U moet dezelfde stappen volgen als hierboven voor de doel subresource die als **Portal** is geselecteerd.

## <a name="firewalls-to-restrict-public-access"></a>Firewalls voor het beperken van open bare toegang

Volg de onderstaande stappen om de toegang tot het controle sfeer liggen-account volledig van open bare Internet af te melden. Deze instelling is van toepassing op zowel privé-eind punten als opname van privé-eind punten.

1. Ga naar het controle sfeer liggen-account in het Azure Portal, selecteer de verbindingen voor privé-eind punten in het gedeelte **netwerken** van **instellingen**.
1. Ga naar het tabblad Firewall en zorg ervoor dat de wissel knop is ingesteld op **weigeren**.

    :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Firewall instellingen voor privé-eind punt":::

## <a name="next-steps"></a>Volgende stappen

- [Door de Azure controle sfeer liggen-Data Catalog bladeren](how-to-browse-catalog.md)

- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
