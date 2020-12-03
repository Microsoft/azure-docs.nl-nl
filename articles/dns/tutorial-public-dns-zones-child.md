---
title: 'Zelfstudie: Onderliggende Azure DNS-zones maken'
titleSuffix: Azure DNS
description: Zelfstudie over het maken van onderliggende DNS-zones in de Azure-portal.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: d805fb926131e047572887758b10506e4edd5944
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183376"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Zelfstudie: Een nieuwe onderliggende DNS-zone maken

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Aanmelden bij de Azure-portal
> * Onderliggende DNS-zone maken via nieuwe DNS-zone
> * Onderliggende DNS-zone maken via bovenliggende DNS-zone
> * NS-delegering voor nieuwe onderliggende DNS-zone verifiëren



## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement.  Als u geen account hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Bestaande bovenliggende Azure DNS-zone.  

Voor het doel van deze zelfstudie wordt contoso.com als de bovenliggende zone gebruikt en subdomain.contoso.com als de naam van het onderliggende domein.  Vervang *contoso.com* door de naam van uw bovenliggende domein en *subdomain* door uw onderliggende domein.  Als u nog geen bovenliggende DNS-zone hebt gemaakt, raadpleegt u de stappen voor het [maken van een DNS-zone met behulp van de Azure-portal](./dns-getstarted-portal.md#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).
Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint.

Er zijn twee manieren waarop u de onderliggende DNS-zone kunt maken.
1.  Via de portalpagina 'DNS-zone maken'
1.  Via de configuratiepagina van de bovenliggende DNS-zone


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Onderliggende DNS-zone maken via DNS-zone maken

In deze stap maakt u een nieuwe onderliggende DNS-zone met de naam **subdomain.contoso.com** en delegeert u de zone aan de bestaande bovenliggende DNS-zone, **contoso.com**. U maakt de DNS-zone met behulp van de tabbladen op de pagina **DNS-zone maken**.
1.  Selecteer in het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**. Het venster **Nieuw** wordt weergegeven.
1.  Selecteer **Netwerken**, **DNS-zone** en vervolgens de knop **Toevoegen**.

1.  Typ of selecteer de volgende waarden op het tabblad **Basisinformatie**:
    * **Abonnement**: Selecteer een abonnement waarin u de zone wilt maken.
    * **Resourcegroep**: Voer de bestaande resourcegroep in. U kunt ook een nieuwe maken door **Nieuwe maken** te selecteren, *MyResourceGroup* in te voeren en **OK** te selecteren. De naam van de resourcegroep moet uniek zijn binnen het Azure-abonnement.
    * Schakel dit selectievakje in: **Deze zone is een onderliggend element van een bestaande zone die al in Azure DNS wordt gehost**
    * **Abonnement voor bovenliggende zone**: Zoek en/of selecteer in deze vervolgkeuzelijst de naam van het abonnement waaronder de bovenliggende DNS-zone *contoso.com* is gemaakt.
    * **Bovenliggende zone**: Typ *contoso.com* in de zoekbalk om deze in vervolgkeuzelijst te laden. Als het is geladen, selecteert u *contoso.com* in de vervolgkeuzelijst.
    * **Naam:** Typ *subdomain* voor het voorbeeld in deze zelfstudie. U ziet dat de naam van de bovenliggende DNS-zone, *contoso.com*, automatisch als achtervoegsel aan de naam wordt toegevoegd wanneer in de bovenstaande stap de bovenliggende zone wordt geselecteerd.

1. Selecteer **Volgende: Beoordelen en maken**.
1. Controleer het overzicht op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
Het maken van de zone kan een paar minuten duren.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Schermafbeelding van de pagina DNS-zone maken." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Onderliggende DNS-zone maken via bovenliggende DNS-zone: overzichtspagina
U kunt ook een nieuwe onderliggende DNS-zone maken en deze in de bovenliggende DNS-zone delegeren met behulp van de knop **Onderliggende zone** op de pagina Overzicht bovenliggende zone. Als u deze knop gebruikt, worden de bovenliggende parameters voor de onderliggende zone automatisch vooraf ingevuld. 

1.  Open in de Azure-portal onder **Alle resources** de DNS-zone *contoso.com* in de resourcegroep **MyResourceGroup**. U kunt *contoso.com* invoeren in het vak **Filteren op naam** zodat u deze eenvoudiger kunt vinden.
1.  Selecteer op de pagina met het overzicht van de DNS-zone de knop **+Onderliggende zone**.

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Schermafbeelding van knop Onderliggende zone." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Vervolgens wordt de pagina DNS-zone maken geopend. De optie Onderliggende zone is al ingeschakeld. Het abonnement voor de bovenliggende zone en de bovenliggende zone zijn al op deze pagina ingevuld.
1.  Typ de naam als *onderliggend* voor het voorbeeld in deze zelfstudie. U ziet dat de naam van de bovenliggende DNS-zone, contoso.com, automatisch als voorvoegsel aan de naam wordt toegevoegd.
1.  Selecteer **Volgende: Tags** en vervolgens **Volgende: Beoordelen en maken**.
1.  Controleer het overzicht op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Schermopname van geselecteerde onderliggende zone" border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>Onderliggende DNS-zone verifiëren
U hebt nu een nieuwe onderliggende DNS-zone gemaakt: *subdomain.contoso.com*. Als u wilt controleren of de delegatie op de juiste wijze is uitgevoerd, controleert u of de records van de naamserver (NS) voor de onderliggende zone zich in de bovenliggende zone bevinden, zoals hieronder beschreven.  

**Naamservers van onderliggende DNS-zone ophalen:**

1.  Open in de Azure-portal onder **Alle resources** de DNS-zone *subdomain.contoso.com* in de resourcegroep **MyResourceGroup**. U kunt *subdomain.contoso.com* invoeren in het vak **Filteren op naam** zodat u deze eenvoudiger kunt vinden.
1.  Haal de naamservers op van de pagina met het overzicht van DNS-zones. In dit voorbeeld zijn de naamservers *ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org* en *ns4-08.azure-dns.info* aan de zone contoso.com toegewezen:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Schermopname van naamservers van de onderliggende zone" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Controleer de NS-record in de bovenliggende DNS-zone:**

In deze stap gaat u naar de bovenliggende DNS-zone *contoso.com* en controleert u of de vermelding voor de bijbehorende NS-recordset voor de naamservers van de onderliggende zones is gemaakt.

1. Open in de Azure-portal onder **Alle resources** de DNS-zone contoso.com in de resourcegroep **MyResourceGroup**. U kunt contoso.com invoeren in het vak **Filteren op naam** zodat u deze eenvoudiger kunt vinden.
1.  Op de pagina met het overzicht van de DNS-zones, *contoso.com*, controleert u op de recordsets.
1.  U ziet dat de recordset van het type NS en met de naam subdomain al in de bovenliggende DNS-zone is gemaakt. Controleer of de waarden voor deze recordset lijken op die in de lijst met naamservers die in de bovenstaande stap zijn opgehaald uit de onderliggende DNS-zone.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Schermopname van validatie van naamservers van de onderliggende zone" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u de resources die u in deze zelfstudie hebt gemaakt, niet meer nodig hebt, verwijdert u ze door de resourcegroep **MyResourceGroup** te verwijderen. Open de resourcegroep **MyResourceGroup** en selecteer **Resourcegroep verwijderen**.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Scenario's voor privé-DNS-zones in Azure](private-dns-scenarios.md)