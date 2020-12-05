---
title: Azure-cache voor redis met persoonlijke Azure-koppeling (preview-versie)
description: Persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met Azure cache voor redis die worden aangestuurd door een persoonlijke Azure-koppeling. In dit artikel leert u hoe u een Azure-cache, een Azure-Virtual Network en een persoonlijk eind punt maakt met behulp van de Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 31ae4605b6cc9e26c89beea692fe61fcbda49c4c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621498"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure-cache voor redis met persoonlijke Azure-koppeling (open bare preview)
In dit artikel leert u hoe u een virtueel netwerk en een Azure-cache maakt voor een redis-exemplaar met een persoonlijk eind punt met behulp van de Azure Portal. U leert ook hoe u een persoonlijk eind punt kunt toevoegen aan een bestaand Azure-cache geheugen voor redis-instantie.

Persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met Azure cache voor redis die worden aangestuurd door een persoonlijke Azure-koppeling. 

## <a name="prerequisites"></a>Vereisten
* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Als u persoonlijke eind punten wilt gebruiken, moet uw Azure-cache voor redis-exemplaar zijn gemaakt na 28 juli 2020.
> Momenteel wordt geo-replicatie, firewall regels, ondersteuning voor de portal console, meerdere eind punten per geclusterde cache, persistentie naar Firewall en door VNet geïnjecteerde caches niet ondersteund. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Een persoonlijk eind punt maken met een nieuwe Azure-cache voor redis-exemplaar 

In deze sectie maakt u een nieuw Azure-cache geheugen voor een redis-exemplaar met een persoonlijk eind punt.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Create a resource**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecteer een resource maken.":::

2. Selecteer op de pagina **Nieuw** de optie **netwerken** en selecteer vervolgens **virtueel netwerk**.

3. Selecteer **toevoegen** om een virtueel netwerk te maken.

4. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit virtuele netwerk wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resource groep waarin u het virtuele netwerk en andere resources wilt maken. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Naam** | Voer een naam voor het virtuele netwerk in. | De naam moet beginnen met een letter of cijfer, eindigen met een letter, cijfer of onderstrepings teken en mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. | 
   | **Regio** | Vervolg keuzelijst en selecteer een regio. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gebruikmaken van uw virtuele netwerk. |

5. Selecteer het tabblad **IP-adressen** of klik op de knop **volgende: IP-adressen** aan de onderkant van de pagina.

6. Geef op het tabblad **IP-adressen** de **IPv4-adres ruimte** op als een of meer adres voorvoegsels in CIDR-notatie (bijvoorbeeld 192.168.1.0/24).

7. Klik onder **subnet naam** op **standaard** om de eigenschappen van het subnet te bewerken.

8. Geef in het deel venster **subnet bewerken** de **naam** van een subnet en het **adres bereik** van het subnet op. Het adres bereik van het subnet moet de CIDR-notatie hebben (bijvoorbeeld 192.168.1.0/24). Het moet deel uitmaken van de adres ruimte van het virtuele netwerk.

9. Selecteer **Opslaan**.

10. Selecteer het tabblad **controleren + maken** of klik op de knop **beoordeling + maken** .

11. Controleer of alle gegevens juist zijn en klik op **maken** om het virtuele netwerk in te richten.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Een Azure-cache maken voor een redis-exemplaar met een persoonlijk eind punt
Volg deze stappen om een cache-exemplaar te maken.

1. Ga terug naar de Azure Portal start pagina of open het menu Sidebar en selecteer vervolgens **een resource maken**. 
   
1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecteer Azure Cache voor Redis.":::
   
1. Configureer op de pagina **Nieuwe Redis-cache** de instellingen voor de nieuwe cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Open de vervolgkeuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
   | **Prijscategorie** | Open de vervolgkeuzelijst en selecteer een [Prijscategorie](https://azure.microsoft.com/pricing/details/cache/). |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |

1. Selecteer het tabblad **Netwerken** of klik op de knop **Netwerken** onderaan de pagina.

1. Selecteer op het tabblad **netwerken** het **persoonlijke eind punt** voor de verbindings methode.

1. Klik op de knop **toevoegen** om uw persoonlijke eind punt te maken.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Voeg in netwerken een persoonlijk eind punt toe.":::

1. Configureer op de pagina **een persoonlijk eind punt maken** de instellingen voor uw persoonlijke eind punt met het virtuele netwerk en het subnet dat u hebt gemaakt in de laatste sectie en selecteer **OK**. 

1. Selecteer het tabblad **Volgende: Geavanceerd** of klik op de knop **Volgende: Geavanceerd** onderaan de pagina.

1. Selecteer in het tabblad **Geavanceerd** voor een basic of standard cache-exemplaar de schakeloptie inschakelen als u een niet-TLS-poort wilt inschakelen.

1. Configureer in het tabblad **Geavanceerd** voor premium cache-exemplaar de instellingen voor een niet-TLS-poort, clustering en gegevenspersistentie.

1. Selecteer het tabblad **Volgende: Tags** of klik op de knop **Volgende: Tags** onderaan de pagina.

1. Voer desgewenst in het tabblad **Tags** de naam en waarde in om de resource te categoriseren. 

1. Selecteer **Controleren + maken**. Het tabblad Beoordelen + maken wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

1. Selecteer **Maken** nadat het groene bericht Validatie geslaagd verschijnt.

Het duurt even voor de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken. 
    
> [!IMPORTANT]
> 
> Er is een `publicNetworkAccess` vlag die `Disabled` standaard is. 
> Deze vlag is bedoeld om u in staat te stellen zowel open bare als privé-eind punten toegang te geven tot de cache als deze is ingesteld op `Enabled` . Als deze eigenschap is ingesteld op `Disabled` , is toegang tot privé-eind punten alleen toegestaan. U kunt de waarde instellen op `Disabled` of `Enabled` met de volgende patch-aanvraag. Bewerk de waarde om aan te geven welke vlag u voor uw cache wilt.
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> Als u verbinding wilt maken met een geclusterde cache, `publicNetworkAccess` moet deze worden ingesteld op `Disabled` en kan er slechts één particuliere eindpunt verbinding zijn. 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Een persoonlijk eind punt maken met een bestaand Azure-cache geheugen voor redis-exemplaar 

In deze sectie voegt u een persoonlijk eind punt toe aan een bestaand Azure-cache geheugen voor redis-instantie. 

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken 
Voer de volgende stappen uit om een virtueel netwerk te maken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Create a resource**.

2. Selecteer op de pagina **Nieuw** de optie **netwerken** en selecteer vervolgens **virtueel netwerk**.

3. Selecteer **toevoegen** om een virtueel netwerk te maken.

4. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit virtuele netwerk wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resource groep waarin u het virtuele netwerk en andere resources wilt maken. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Naam** | Voer een naam voor het virtuele netwerk in. | De naam moet beginnen met een letter of cijfer, eindigen met een letter, cijfer of onderstrepings teken en mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. | 
   | **Regio** | Vervolg keuzelijst en selecteer een regio. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gebruikmaken van uw virtuele netwerk. |

5. Selecteer het tabblad **IP-adressen** of klik op de knop **volgende: IP-adressen** aan de onderkant van de pagina.

6. Geef op het tabblad **IP-adressen** de **IPv4-adres ruimte** op als een of meer adres voorvoegsels in CIDR-notatie (bijvoorbeeld 192.168.1.0/24).

7. Klik onder **subnet naam** op **standaard** om de eigenschappen van het subnet te bewerken.

8. Geef in het deel venster **subnet bewerken** de **naam** van een subnet en het **adres bereik** van het subnet op. Het adres bereik van het subnet moet de CIDR-notatie hebben (bijvoorbeeld 192.168.1.0/24). Het moet deel uitmaken van de adres ruimte van het virtuele netwerk.

9. Selecteer **Opslaan**.

10. Selecteer het tabblad **controleren + maken** of klik op de knop **beoordeling + maken** .

11. Controleer of alle gegevens juist zijn en klik op **maken** om het virtuele netwerk in te richten.

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken 

Volg deze stappen om een persoonlijk eind punt te maken.

1. Zoek in het Azure Portal naar **Azure cache voor redis** en druk op ENTER of selecteer dit in de zoek suggesties.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Zoek naar Azure cache voor redis.":::

2. Selecteer het cache-exemplaar waaraan u een persoonlijk eind punt wilt toevoegen.

3. Selecteer aan de linkerkant van het scherm het **persoonlijke eind punt (preview-versie)**.

4. Klik op de knop **persoonlijk eind punt** om uw persoonlijke eind punt te maken.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Persoonlijk eind punt toevoegen.":::

5. Configureer op de **pagina een persoonlijk eind punt maken** de instellingen voor uw persoonlijke eind punt.

   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit privé-eind punt wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resource groep waarin u uw persoonlijke eind punt en andere resources wilt maken. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Naam** | Voer de naam van een persoonlijk eind punt in. | De naam moet beginnen met een letter of cijfer, eindigen met een letter, cijfer of onderstrepings teken en mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. | 
   | **Regio** | Vervolg keuzelijst en selecteer een regio. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die uw persoonlijke eind punt gaan gebruiken. |

6. Klik op de knop **volgende: resource** aan de onderkant van de pagina.

7. Selecteer uw abonnement op het tabblad **resource** , kies het resource type als `Microsoft.Cache/Redis` en selecteer vervolgens de cache waarmee u het persoonlijke eind punt wilt verbinden.

8. Klik onder aan de pagina op de knop **volgende: Configuratie** .

9. Op het tabblad **configuratie** selecteert u het virtuele netwerk en het subnet dat u in de vorige sectie hebt gemaakt.

10. Klik op de knop **volgende: Labels** onder aan de pagina.

11. Voer desgewenst in het tabblad **Tags** de naam en waarde in om de resource te categoriseren.

12. Selecteer **Controleren + maken**. U gaat naar het tabblad **controleren + maken** , waar Azure uw configuratie valideert.

13. Wanneer het bericht groene **validatie is voltooid** wordt weer gegeven, selecteert u **maken**.

## <a name="faq"></a>Veelgestelde vragen

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Waarom kan ik geen verbinding maken met een privé-eind punt?
Als uw cache al een VNet-geïnjecteerde cache is, kunnen privé-eind punten niet worden gebruikt met uw cache-exemplaar. Als uw cache-exemplaar gebruikmaakt van een niet-ondersteunde functie (zie hieronder), kunt u geen verbinding maken met uw exemplaar van een persoonlijk eind punt. Daarnaast moeten cache-exemplaren na juli 27 worden gemaakt om persoonlijke eind punten te kunnen gebruiken.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Welke functies worden niet ondersteund met persoonlijke eind punten?
Geo-replicatie, firewall regels, ondersteuning voor de portal console, meerdere eind punten per geclusterde cache, persistentie voor firewall regels en zone redundantie. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Hoe kan ik mijn privé-eind punt wijzigen zodat deze wordt uitgeschakeld of ingeschakeld voor open bare netwerk toegang?
Er is een `publicNetworkAccess` vlag die `Disabled` standaard is. Deze vlag is bedoeld om u in staat te stellen zowel open bare als privé-eind punten toegang te geven tot de cache als deze is ingesteld op `Enabled` . Als deze eigenschap is ingesteld op `Disabled` , is toegang tot privé-eind punten alleen toegestaan. U kunt de waarde instellen op `Disabled` of `Enabled` met de volgende patch-aanvraag. Bewerk de waarde om aan te geven welke vlag u voor uw cache wilt.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Zijn netwerk beveiligings groepen (NSG) ingeschakeld voor privé-eind punten?
Nee, ze zijn uitgeschakeld voor privé-eind punten. Als er echter andere bronnen op het subnet aanwezig zijn, wordt NSG Enforcement toegepast op deze resources.

### <a name="how-can-i-connect-to-a-clustered-cache"></a>Hoe kan ik verbinding maken met een geclusterde cache?
`publicNetworkAccess` moet worden ingesteld op `Disabled` en er mag slechts één particuliere eindpunt verbinding zijn.

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Hoe wordt het exemplaar van mijn privé-eind punt niet in mijn VNet?
Het is alleen gekoppeld aan uw VNet. Omdat deze zich niet in uw VNet bevindt, hoeven NSG-regels niet te worden gewijzigd voor afhankelijke eind punten.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>Hoe kan ik mijn door VNet geïnjecteerde cache naar een persoonlijke eindpunt cache migreren?
U moet uw door VNet geïnjecteerde cache verwijderen en een nieuw cache-exemplaar maken met een persoonlijk eind punt.

## <a name="next-steps"></a>Volgende stappen

* Zie de [documentatie van Azure private link](../private-link/private-link-overview.md)voor meer informatie over persoonlijke Azure-koppelingen.
* Zie [Azure cache for redis Network-isolatie opties](cache-network-isolation.md)voor meer informatie over het vergelijken van verschillende netwerk isolatie opties voor uw cache-exemplaar.