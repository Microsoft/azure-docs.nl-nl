---
title: Aanbevolen procedures voor aanbiedingen-micro soft Commercial Marketplace
description: Meer informatie over de beste prak tijken van Go-to-Market voor uw Microsoft AppSource en Azure Marketplace-aanbiedingen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 07/06/2020
ms.openlocfilehash: 3ea6a0035a9f9354be5c14699936c6a07dea1150
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94492086"
---
# <a name="offer-listing-best-practices"></a>Best practices voor aanbiedingsvermeldingen

Dit artikel bevat suggesties voor het maken en aanbieden van micro soft-Commercial Marketplace-aanbiedingen. De volgende tabellen bevatten de aanbevolen procedures voor het volt ooien van de aanbiedings gegevens in het partner centrum. Voor een analyse van hoe uw aanbiedingen worden uitgevoerd, gaat u naar het [Marketplace Insights-dash board](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights) in Partner Center. 

## <a name="online-store-offer-details"></a>Details van aanbieding voor online winkel

| Instelling | Best practice |
|:--- |:--- |  
| Naam van aanbieding | Geef voor apps een duidelijke titel op die Zoek woorden bevat waarmee klanten uw aanbieding kunnen ontdekken. <br> <br> Voor advies Services gaat u als volgt te werk: [naam aanbieding: [duration] [aanbiedings type] (bijvoorbeeld contoso: implementatie 2 weken) |
| Beschrijving van aanbieding | Geef een duidelijke beschrijving van de toegevoegde waarde van uw aanbieding in de eerste paar zinnen.  Houd er rekening mee dat deze zinnen kunnen worden gebruikt in resultaten van de zoek machine. De belangrijkste onderdelen van de toegevoegde waarde zijn: <ul> <li>Beschrijving van het product of de oplossing. </li> <li> Gebruikers die profiteren van het product of de oplossing. </li> <li> Klanten behoefte hebben of pijnen van de product-of oplossings adressen. </li> </ul> <br> Gebruik, indien mogelijk, terminologie op basis van de industrie norm of op voor delen.  Vertrouw niet op functies en functionaliteit om uw product te verkopen.  Richt u in plaats daarvan op de waarde die u levert. <br> <br> Voor advies Services moet u de professionele service die u levert duidelijk vermelden. |

> [!IMPORTANT]
> Zorg ervoor dat de naam van uw aanbieding en de beschrijving van de aanbieding voldoen aan de merken- **[en merk richtlijnen van micro soft](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** en andere relevante, productspecifieke richt lijnen bij het verwijzen naar handels merken van micro soft en de namen van micro soft-software,-producten en-services.

## <a name="online-store-listing-details"></a>Details van aanbieding voor online winkels

Categorieën en branches voor een andere online winkel zijn van toepassing op verschillende typen aanbiedingen.

| Online winkel | Categorieën <br>per online winkel | Categorieën <br>per online winkel | Branches <br> voor AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **Type aanbieding**   |  **Azure Marketplace**  | **AppSource**  |
| Azure-app | X | |
| Container | X | |
| Adviesservices | | | X |
| Dynamics 365 Customer engagement & Power platform | | X | X |
| Dynamics 365-Financiën & Supply Chain Management | | X | X | 
| Dynamics 365 Business Central | | X | X |
| IoT Edge modules | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Virtuele Azure-machine |  X |    |

### <a name="categories"></a>Categorieën

Microsoft AppSource en Azure Marketplace zijn online winkels die verschillende typen oplossingen bieden. Azure Marketplace biedt IT-oplossingen die zijn gebouwd op of voor Azure.  Microsoft AppSource bieden zakelijke oplossingen, zoals toepassingen voor de industrie-SaaS, Dynamics 365-invoeg toepassingen, Microsoft 365-invoeg toepassingen en Power platform-apps.

Categorieën en subcategorieën worden toegewezen aan elke online winkel op basis van het oplossings type. Uw aanbieding wordt gepubliceerd op Microsoft AppSource of Azure Marketplace, afhankelijk van het type aanbieding, de transactie mogelijkheden van de aanbieding en categorie/subcategorie. 

Selecteer categorieën en subcategorieën die het beste aansluiten bij uw oplossings type. U kunt het volgende selecteren:

* Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
* Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie is geselecteerd, kunt u nog steeds alleen de geselecteerde categorie detecteerbaar maken.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>BELANG rijk: SaaS-aanbiedingen en Microsoft 365-invoeg toepassingen

Zie trans acties [in de commerciële Marketplace](marketplace-commercial-transaction-capabilities-and-considerations.md) voor specifieke informatie over hoe de Transact-mogelijkheden van invloed kunnen zijn op hoe uw aanbieding kan worden weer gegeven en aangeschaft door klanten van Marketplace. Voor SaaS-aanbiedingen wordt de transactie capaciteit van de aanbieding en de categorie selectie bepaald door de online winkel waar uw aanbieding wordt gepubliceerd.


| Aanbieding voor SaaS    | Aanbieding voor SaaS   | Aanbieding voor SaaS  | Aanbieding voor SaaS   | Aanbieding voor SaaS   | Aanbieding voor SaaS   | Aanbieding voor SaaS    | Toepas bare online winkel| Toepas bare online winkel |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| Factuur met data limiet | Microsoft 365-invoeg toepassingen | Contact opnemen | Transact (ten minste 1 abonnement) | Persoonlijk abonnement | Alleen openbaar abonnement | Privé-abonnementen openbaar & | AppSource | Azure Marketplace |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1, 2</sup> |
|  |  | X |  |  |  |  | x<sup>1</sup> | X<sup>1</sup> | 

1. Afhankelijk van categorie/subcategorie en branche selectie
2. Aanbiedingen met persoonlijke abonnementen worden gepubliceerd op de Azure Portal


### <a name="industries"></a>Branches

Branche selectie geldt alleen voor aanbiedingen die zijn gepubliceerd op AppSource en Consulting Services die zijn gepubliceerd in azure Marketplace.  Selecteer branches en/of verticaal als uw aanbieding gericht is op de behoeften van de branche en die branchespecifieke mogelijkheden in uw aanbiedings beschrijving aanroept. U kunt Maxi maal twee (2) sectoren selecteren en twee (2) verticaal per branche geselecteerd.

>[!Note]
>Voor advies over service aanbiedingen in azure Marketplace zijn er geen verticale sectoren.

| **Branches** |  **Sectoren** |
| :------------------- | :----------------|
| **Landbouw** | |
| **Architectuur & constructie** | |
| **Auto's** | |
| **Distributie** | Handels <br> Pakket verzending van Parcel & |  
| **Onderwijs** | Hoger onderwijs <br> Primaire & secundair edu/K-12 <br> Bibliotheken & musea |
| **Financiële diensten** | Bank & kapitaal markten <br> Polis | 
| **Overheid** |  Verdediging & Intelligence <br> Burger overheid <br> Open bare veiligheid & Justitie |
| **Gezondheidszorg** | Status betaler <br> Health-provider <br> Pharmaceuticals | 
| **Horeca & reizen** | Reis & vervoer <br> Hotels & vrije tijd <br> Restaurants & voedsel Services | 
| **Resources voor productie &** | Chemische & Agrochemical <br> Discrete productie <br> Energie | 
| **Media & communicatie** | Media & entertainment <br> Telecommunicatie | 
| **Andere branches uit de open bare sector** | Bosbouw & visserij <br> Non-profit | 
| **Professionele services** | Partner Professional-Services <br> Juridisch | 
| **Onroerend goed** | |

Alleen voor Microsoft AppSource:

| **Branche** |  **Sectoren** |
| :------------------- | :----------------|
| **Retail & consumenten goederen** | Handelaren <br> Consumenten goederen |

### <a name="applicable-products"></a>Toepasselijke producten

Selecteer de producten die van toepassing zijn op uw app voor de aanbieding om weer te geven onder geselecteerde producten in AppSource.

### <a name="search-keywords"></a>Tref woorden zoeken

Tref woorden kunnen klanten helpen bij het zoeken naar uw aanbieding. Bepaal de belangrijkste Zoek woorden voor uw aanbieding, neem deze op in de samen vatting en beschrijving van uw aanbieding, evenals in het gedeelte tref woorden van de sectie met details van de aanbieding.

## <a name="online-store-marketing-details"></a>Marketing Details voor online winkels
| Instelling | Best practice |
|:--- |:--- |  
| Logo van aanbieding (PNG-indeling, van 216 x 216 tot 350 x 350 px): app-details pagina | Uw logo ontwerpen en optimaliseren voor een digitaal medium:<br>Upload het logo in PNG-indeling naar de pagina met details van de app van uw aanbieding. Het formaat van het partner centrum wordt gewijzigd in de vereiste logo grootten. |
| Logo van aanbieding (PNG-indeling, 48 x 48 pixels): zoek pagina | Het partner centrum genereert dit logo van het grote logo dat u hebt geüpload. U kunt dit eventueel later vervangen door een andere installatie kopie. |
| Documenten ' meer informatie ' | Ondersteunende verkoop-en marketing assets onder "meer informatie" bevatten enkele voor beelden:<ul><li>technische documenten</li><li> brochures</li><li>controle lijsten of</li><li> Power Point-presentaties</li></ul><br>Sla alle bestanden op in PDF-indeling. Het doel is hier om klanten te informeren en daar niet aan te verkopen.<br><br>Een koppeling naar uw app-landings pagina toevoegen aan al uw documenten en URL-para meters toevoegen om bezoekers en experimenten bij te houden. |
| Video's: AppSource, Consulting Services en SaaS biedt alleen | De krach tigste Video's communiceren de waarde van uw aanbieding in een verhalende vorm:<ul> <li> Maak uw klant, niet uw bedrijf, de held van het verhaal. </li> <li> Uw video moet de belangrijkste uitdagingen en doel stellingen van uw doel klant aanpakken. </li> <li> Aanbevolen lengte: 60-90 seconden.</li> <li> Belang rijke Zoek woorden opnemen die de naam van de Video's gebruiken. </li> <li> Overweeg extra Video's toe te voegen, zoals een procedure, aan de slag of klant ervaringen. </li> </ul> |
| Scherm afbeeldingen (1280 &nbsp; &times; &nbsp; 720) | Maxi maal vijf scherm opnamen toevoegen:<br>Belang rijke Zoek woorden opnemen in de bestands namen. |

## <a name="link-to-your-offer-page-from-your-website"></a>Koppeling naar uw aanbiedings pagina van uw website

Wanneer u een koppeling maakt van de AppSource of Azure Marketplace-badge op uw site naar uw vermelding in de commerciële Marketplace, kunt u een sterke analyse en rapportage ondersteunen door de volgende query parameters aan het einde van de URL op te nemen:
* **src**: Neem de bron op van waaruit het verkeer wordt doorgestuurd naar AppSource (bijvoorbeeld website, LinkedIn of Facebook).
* **mktcmpid**: uw marketing campagne-id, die Maxi maal 16 tekens kan bevatten in een combi natie van letters, cijfers, onderstrepings teken en afbreek streepjes (bijvoorbeeld *blogpost_12*).

De volgende voor beeld-URL bevat beide van de voor gaande query parameters: `https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

Door de para meters aan uw AppSource-URL toe te voegen, kunt u de effectiviteit van uw campagne controleren in het dash board Analytics in [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [voor delen van uw commerciële Marketplace](./gtm-your-marketplace-benefits.md).

Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken en te configureren.
