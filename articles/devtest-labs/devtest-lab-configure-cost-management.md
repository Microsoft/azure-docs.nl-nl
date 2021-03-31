---
title: Bekijk de maandelijkse geschatte Lab-kosten trend in Azure DevTest Labs
description: In dit artikel vindt u informatie over het bijhouden van de kosten van uw Lab (maandelijkse geschatte kosten trend grafiek) in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6a2a9bef9e54ef7deda123aad34cf0c576fd158f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98892334"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Kosten bijhouden die zijn gekoppeld aan een lab in Azure DevTest Labs
Dit artikel bevat informatie over het bijhouden van de kosten van uw Lab. U ziet hoe u de geschatte kosten trend voor de huidige kalender maand voor het lab weergeeft. In dit artikel ziet u ook hoe u de kosten per resource kunt weer geven in het lab.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>De maandelijkse geschatte Lab-kosten trend weer geven 
In deze sectie leert u hoe u het **maandelijkse geschatte kosten trend** diagram kunt gebruiken om de geschatte kosten van de huidige kalender maand en de verwachte kosten voor het eind van de maand voor de huidige kalender maand weer te geven. U leert ook hoe u de kosten voor het lab beheert door de bestedings doelen en drempel waarden in te stellen die, wanneer deze zijn bereikt, DevTest Labs activeren om de resultaten aan u te melden.

Voer de volgende stappen uit om het diagram met maandelijkse geschatte kosten trend weer te geven: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs uw Lab.  
4. Selecteer **configuratie en beleid** in het menu links.  
4. Selecteer **kosten trend** in het gedeelte **kosten bijhouden** in het menu links. De volgende scherm afbeelding toont een voor beeld van een kosten diagram. 
   
    ![Kosten diagram](./media/devtest-lab-configure-cost-management/graph.png)

    De **geschatte kosten** waarde is de geschatte kosten tot heden van de huidige kalender maand. De verwachte **kosten** zijn de geschatte kosten voor de gehele huidige kalender maand, berekend met behulp van de kosten van het lab voor de afgelopen vijf dagen.

    De kosten bedragen worden naar boven afgerond op het dichtstbijzijnde gehele getal. Bijvoorbeeld: 

   * 5,01 rondt op 6 
   * 5,50 rondt op 6
   * 5,99 rondt op 6

     De kosten die standaard worden weer gegeven in de grafiek *, worden berekend* op basis van de tarieven voor [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) -aanbiedingen. U kunt ook uw eigen bestedings doelen instellen die worden weer gegeven in de grafieken door [de kosten doelen voor uw Lab te beheren.](#managing-cost-targets-for-your-lab)

     De volgende kosten zijn *niet* opgenomen in de berekening van kosten:

   * CSP-en DreamSpark-abonnementen worden momenteel niet ondersteund, omdat Azure DevTest Labs de Azure billing-Api's gebruikt om de Lab-kosten te berekenen. Dit biedt geen ondersteuning voor CSP-of DreamSpark-abonnementen.
   * Uw aanbod tarieven. Op dit moment kunt u de aanbiedings tarieven (weer gegeven onder uw abonnement) niet gebruiken die u hebt onderhandeld met micro soft-of micro soft-partners. Alleen betalen naar gebruik-tarieven worden gebruikt.
   * Uw belastingen
   * Uw kortingen
   * Uw facturerings valuta. De kosten voor het lab worden op dit moment alleen weer gegeven in de valuta USD.

### <a name="managing-cost-targets-for-your-lab"></a>Kosten doelen voor uw Lab beheren
Met DevTest Labs kunt u de kosten in uw Lab beter beheren door een bestedings doel in te stellen dat u vervolgens kunt weer geven in het diagram maandelijkse geschatte kosten trend. DevTest Labs kan u ook een melding sturen wanneer de opgegeven doel uitgave of drempel waarde is bereikt. 

1. Selecteer op de pagina **kosten trend** de optie **doel beheren**.

    ![Knop doel beheren](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Geef op de pagina **doel beheren** een bestedings doel en drempel waarden op. U kunt ook instellen of elke geselecteerde drempel waarde wordt gerapporteerd in het diagram met de kosten trend of via een webhook-melding.

    ![Doel venster beheren](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecteer een tijds periode gedurende welke de kosten doelen moeten worden bijgehouden.
      - **Maandelijks**: kosten doelen worden per maand bijgehouden.
      - **Opgelost**: de kosten doelen worden bijgehouden voor het datum bereik dat u opgeeft in de begin-en eind datum. Normaal gesp roken geven deze waarden aan hoe lang het uitvoeren van het project is gepland.
   - Geef de **doel kosten** op. Hoeveel u bijvoorbeeld van plan bent te best Eden aan dit lab in de periode die u hebt gedefinieerd.
   - Selecteer deze optie om elke drempel waarde in of uit te scha kelen die u wilt rapporteren: in stappen van 25% tot 125% van de opgegeven **doel kosten**.
      - **Melding**: als aan deze drempel waarde wordt voldaan, wordt u gewaarschuwd door de webhook-URL die u opgeeft.
      - **Tekenen op diagram**: wanneer aan deze drempel waarde wordt voldaan, worden de resultaten weer gegeven in de grafiek met kosten trends die u kunt bekijken, zoals wordt beschreven in het diagram met maandelijkse geschatte kosten trend weer geven.
   - Als u een **melding wilt ontvangen** wanneer aan de drempel wordt voldaan, moet u een webhook-URL opgeven. Selecteer in het gebied kosten integraties **hier klikken om een integratie toe te voegen**. Geef een **webhook-URL** op in het deel venster melding configureren en selecteer vervolgens **OK**.

       ![Het deel venster meldingen configureren](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - Als u **melden** opgeeft, moet u een webhook-URL definiëren.
     - Als u een webhook-URL definieert, moet u **op** dezelfde manier **melding** instellen in het deel venster kosten drempel.
     - U moet een webhook maken voordat u deze hier invoert.  

       Zie [een webhook of API Azure function maken](../azure-functions/functions-bindings-http-webhook.md)voor meer informatie over webhooks. 

## <a name="view-cost-by-resource"></a>Kosten per resource weer geven 
Met de functie maandelijkse kosten trend in Labs kunt u zien hoeveel u hebt besteed in de huidige kalender maand. Het toont ook de projectie van de besteding tot het einde van de maand, op basis van uw uitgaven in de afgelopen zeven dagen. Om u te helpen begrijpen waarom de uitgaven in het lab in de loop van een vroeg stadium voldoen aan drempel waarden, kunt u de functie **kosten per resource** gebruiken waarmee u de maand tot heden **per resource** in een tabel weergeeft.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het gewenste Lab.  
4. Selecteer **configuratie en beleid** in het menu links.
5. Selecteer **kosten per resource** in het gedeelte **kosten bijhouden** van het linkermenu. U ziet de kosten voor elke resource die aan een Lab is gekoppeld. 

    ![Kosten per resource](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Met deze functie kunt u eenvoudig de resources identificeren die het meeste kosten, zodat u acties kunt ondernemen om de Lab-uitgaven te verminderen. De kosten van een virtuele machine zijn bijvoorbeeld gebaseerd op de grootte van de virtuele machine. Hoe groter de virtuele machine, meer is de kosten. U kunt eenvoudig de grootte van een virtuele machine en de eigenaar vinden, zodat u kunt communiceren met de VM-eigenaar om te begrijpen waarom de VM-grootte nodig is en of er een kans is om de grootte te verlagen.

Met het [beleid voor automatisch afsluiten](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) kunt u de kosten verlagen door Lab-vm's op een bepaald tijdstip van de dag te afsluiten. Een Lab-gebruiker kan zich echter afmelden van het afsluit beleid, waardoor de kosten voor het uitvoeren van de virtuele machine toenemen. U kunt een virtuele machine in de tabel selecteren om te zien of deze is afgemeld bij het beleid voor automatisch afsluiten. Als dat het geval is, kunt u contact opnemen met de eigenaar van de virtuele machine om te achterhalen waarom de virtuele machine aan het beleid is afgemeld.
 
## <a name="next-steps"></a>Volgende stappen
Hier volgen enkele dingen om volgende te proberen:

* [Lab-beleid definiëren](devtest-lab-set-lab-policy.md) : informatie over het instellen van de verschillende beleids regels die worden gebruikt om te bepalen hoe uw Lab en de virtuele machines worden gebruikt. 
* [Aangepaste installatie kopie maken](devtest-lab-create-template.md) : wanneer u een virtuele machine maakt, geeft u een basis op. Dit kan een aangepaste installatie kopie of een Marketplace-installatie kopie zijn. In dit artikel wordt beschreven hoe u een aangepaste installatie kopie maakt op basis van een VHD-bestand.
* [Marketplace-installatie kopieën configureren](devtest-lab-configure-marketplace-images.md) : DevTest Labs biedt ondersteuning voor het maken van vm's op basis van Azure Marketplace-installatie kopieën. In dit artikel wordt beschreven hoe u kunt opgeven welke Azure Marketplace-installatie kopieën kunnen worden gebruikt bij het maken van Vm's in een lab.
* [Een virtuele machine maken in een Lab](devtest-lab-add-vm.md) : illustreert hoe u een virtuele machine maakt op basis van een standaard installatie kopie (een aangepaste of Marketplace) en hoe u met artefacten in uw virtuele machine werkt.
