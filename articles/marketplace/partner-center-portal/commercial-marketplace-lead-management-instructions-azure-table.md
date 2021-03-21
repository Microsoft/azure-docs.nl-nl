---
title: Lead beheer met Azure-tabel opslag-micro soft Commercial Marketplace
description: Meer informatie over het gebruik van Azure-tabel opslag voor het configureren van leads voor Microsoft AppSource en Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/25/2020
ms.openlocfilehash: 3c8b9444344e5bae414145e2b3367c265114a423
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586847"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Azure-tabel opslag gebruiken om leads voor commerciële Marketplace te beheren

Als uw Customer Relationship Management-systeem (CRM) niet expliciet wordt ondersteund in het partner centrum om Microsoft AppSource en Azure Marketplace-leads te ontvangen, kunt u Azure Table Storage gebruiken om deze leads af te handelen. U kunt de gegevens vervolgens exporteren en importeren in uw CRM-systeem. In dit artikel wordt uitgelegd hoe u een Azure-opslag account en een tabel maakt onder dat account. Daarnaast kunt u een nieuwe stroom maken met behulp van automatische stroom voor het verzenden van een e-mail melding wanneer uw aanbieding een lead ontvangt.

## <a name="configure-an-azure-storage-account"></a>Een Azure-opslag account configureren

1. Als u geen Azure-account hebt, kunt u [een gratis proef account maken](https://azure.microsoft.com/pricing/free-trial/).
1. Nadat uw Azure-account actief is, meldt u zich aan bij de [Azure Portal](https://portal.azure.com).
1. Maak in de Azure Portal een opslag account met behulp van de volgende procedure:

    1. Selecteer **+ een resource maken** in de menu balk links. Het **nieuwe** deel venster wordt aan de rechter kant weer gegeven.
    1. Selecteer **opslag** in het deel venster **Nieuw** . Aan de rechter kant wordt een **Aanbevolen** lijst weer gegeven.
    1. Selecteer **opslag account** om te beginnen met het maken van het account. Volg de instructies in [een opslag account maken](../../storage/common/storage-account-create.md?tabs=azure-portal).

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Stappen voor het maken van een Azure-opslag account.":::

        Zie Quick Start- [zelf studie](../../storage/index.yml)voor meer informatie over opslag accounts. Zie [prijzen voor opslag](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen voor opslag.

1. Wacht tot uw opslag account is ingericht. Dit proces duurt doorgaans enkele minuten.

## <a name="create-a-table-in-your-storage-account"></a>Een tabel in uw opslag account maken

1. Selecteer op de **Start** pagina van de Azure Portal **alle resources weer geven** om toegang te krijgen tot uw opslag account. U kunt ook **alle resources** selecteren in de linkermenu van de Azure Portal.

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Toegang tot uw Azure Storage-account.":::

1. Selecteer in het deel venster opslag account de optie **toegangs sleutels** en kopieer de **verbindings reeks** waarde voor de sleutel. Sla deze waarde op omdat het de waarde voor de **verbindings reeks voor het opslag account** is die u moet opgeven in de portal voor publiceren om leads voor uw Azure Marketplace-aanbieding te ontvangen.

    Hier volgt een voor beeld van een connection string.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Azure-opslag sleutel.":::


1. Selecteer in het deel venster opslag account de optie **tabellen** en selecteer **+ tabel** om een tabel te maken. Voer een naam in voor de tabel en selecteer **OK**. Sla deze waarde op omdat u deze nodig hebt als u een stroom wilt configureren voor het ontvangen van e-mail meldingen wanneer leads worden ontvangen.

    ![Azure-tabellen](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    U kunt [Azure Storage Explorer](https://www.storageexplorer.com) of een ander hulp programma gebruiken om de gegevens in uw opslag tabel te bekijken. U kunt ook de gegevens in de Azure-tabel exporteren.

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>Beschrijving Energie automatisering gebruiken om lead meldingen op te halen

U kunt [automatische energie automatisering](/flow/) gebruiken om meldingen te automatiseren telkens wanneer een lead wordt toegevoegd aan de Azure Storage tabel. Als u geen account hebt, kunt u [zich aanmelden voor een gratis account](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Voor beeld van lead meldingen

In het voor beeld wordt een stroom gemaakt waarmee automatisch een e-mail melding wordt verzonden wanneer een nieuwe lead wordt toegevoegd aan Azure-tabel opslag. In dit voor beeld wordt een terugkeer patroon ingesteld om elk uur lead gegevens te verzenden als de tabel opslag wordt bijgewerkt.

1. Meld u aan bij uw energiebeheer account.
1. Selecteer in de linker balk **mijn stromen**.
1. Selecteer op de bovenste balk **+ Nieuw**.
1. Selecteer in de vervolg keuzelijst **+ gepland--leeg**.

   ![Mijn stromen + gepland--van leeg](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Selecteer in het venster **een geplande stroom maken** voor **herhalen elke** **1** voor het interval en **uur** voor de frequentie. Geef ook de stroom een naam als u wilt. Selecteer **Maken**.

   >[!NOTE]
   >Hoewel in dit voor beeld een interval van één uur wordt gebruikt, kunt u het interval en de frequentie selecteren die het meest geschikt is voor uw bedrijfs behoeften.

   ![Een geplande stroom bouwen](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Selecteer **+ nieuwe stap**.
1. Zoek in het venster **een actie kiezen** naar **vorige tijd ophalen**. Selecteer vervolgens onder **acties** **vorige tijd ophalen**.

   ![Kies een actie](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. Stel in het venster **laatste tijd ophalen** het **interval** in op **1**. Selecteer in de vervolg keuzelijst **tijds eenheid** de optie **uur**.

    >[!IMPORTANT]
    >Zorg ervoor dat het interval en de tijds eenheid die u in stap 8 hebt verzonden, overeenkomen met het interval en de frequentie die u in stap 5 voor het terugkeer patroon hebt geconfigureerd.

    ![Het tijds interval voor ophalen instellen](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >U kunt de stroom op elk gewenst moment controleren om te controleren of elke stap correct is geconfigureerd. Als u de stroom wilt controleren, selecteert u **stroom controle** in de menu balk van de **stroom** .

   In de volgende reeks stappen maakt u verbinding met uw tabel en stelt u de verwerkings logica in voor het verwerken van nieuwe leads.

1. Selecteer **+ nieuwe stap**. Zoek vervolgens naar **Get entities** in het venster **een actie kiezen** .
1. Onder **acties**, selecteert u **entiteiten ophalen (Azure Table Storage)**.
1. Geef in het venster **Azure Table Storage** informatie op voor de volgende vakken en selecteer **maken**:

    * **Verbindings naam**: Geef een beschrijvende naam op voor de verbinding die u tot stand brengt tussen deze stroom en de tabel.
    * **Naam van opslag account**: Geef de naam op van het opslag account voor de tabel. U kunt deze naam vinden op de pagina **toegangs sleutels** van het opslag account.
    * **Gedeelde opslag sleutel**: Geef de sleutel waarde voor uw opslag account voor de tabel op. U kunt deze waarde vinden op de pagina **toegangs sleutels** van het opslag account.

      ![Azure Table Storage-venster](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Nadat u **maken** hebt geselecteerd, wordt het venster **entiteiten ophalen** weer gegeven. Selecteer hier **Geavanceerde opties weer geven** en geef informatie op over de volgende vakken:

   * **Tabel**: Selecteer de naam van de tabel (uit [een tabel maken](#create-a-table-in-your-storage-account)). In de volgende afbeelding ziet u de vraag wanneer `marketplaceleads` de tabel voor dit voor beeld wordt geselecteerd.

     ![Het venster entiteiten ophalen](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Filter query**: Schakel dit selectie vakje in en plak deze functie in het vak: `Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Het dialoog venster entiteiten ophalen, query filteren](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Nu u klaar bent met het instellen van de verbinding met de Azure-tabel, selecteert u **nieuwe stap** om een voor waarde toe te voegen voor het scannen van de Azure-tabel voor nieuwe leads.

1. Selecteer in het venster **actie kiezen** de optie **acties**. Selecteer vervolgens **besturings element voor waarde**.

    ![Een actie venster kiezen](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. Selecteer **een waarde kiezen** in het venster **voor waarde** . Selecteer vervolgens **expressie** in het pop-upvenster.

1. Plak `length(body('Get_entities')?['value'])` het in het **FX** -vak. Selecteer **OK** om deze functie toe te voegen.

1. Het instellen van de voor waarde volt ooien:
    1. Select **is groter dan** in de vervolg keuzelijst.
    2. Voer **0** in als waarde.

        ![Venster voor waarde](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   In de volgende stappen stelt u de actie in die moet worden uitgevoerd op basis van het resultaat van de voor waarde:

   * Als de voor waarde wordt omgezet in **als Nee**, moet u niets doen.
   * Als de voor waarde wordt omgezet in **Indien ja**, moet u een actie activeren die uw werk-of school account verbindt om een e-mail te verzenden. 

1. Selecteer **een actie toevoegen** onder **als ja**.

    ![Voor waarde-venster, indien ja, een actie toevoegen](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Selecteer **een E-mail verzenden (Office 365 Outlook)**.

    ![Voor waarde-venster, indien ja, een e-mail verzenden](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Als u een andere e-mail provider wilt gebruiken, zoekt en selecteert u in plaats daarvan **een e-mail melding (e-mail) verzenden** als actie. De instructies laten zien hoe u kunt configureren met Office 365 Outlook, maar de instructies zijn vergelijkbaar voor een andere e-mail provider.

1. Geef in het Office 365 Outlook-venster informatie op over de volgende vakken:

    1. **Naar**: Voer een e-mail adres in voor iedereen die deze melding ontvangt.
    1. **Onderwerp**: Geef een onderwerp op voor het e-mail adres. Een voor beeld is **nieuwe leads.**
    1. **Hoofd** tekst: Voeg de tekst toe die u in elk e-mail bericht wilt toevoegen (optioneel) en plak deze vervolgens in `body('Get_entities')?['value']` .

    >[!NOTE]
    >U kunt aanvullende statische of dynamische gegevens punten invoegen naar de hoofd tekst van deze e-mail.

    ![Venster voor waarde, indien ja, Office 365 Outlook-venster](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Selecteer **Opslaan** om de stroom op te slaan. Met energie automatisch worden de stroom op fouten getest. Als er fouten zijn, wordt de stroom uitgevoerd nadat deze is opgeslagen.

In de volgende afbeelding ziet u een voor beeld van hoe de uiteindelijke stroom eruit moet zien.

![Een voor beeld van de laatste stroom](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Uw stroom beheren

Het is eenvoudig om uw stroom te beheren nadat deze is uitgevoerd. U hebt volledige controle over uw stroom. U kunt deze bijvoorbeeld stoppen, bewerken, een uitvoerings geschiedenis bekijken en analyses ophalen. In de volgende afbeelding ziet u de opties die beschikbaar zijn voor het beheren van een stroom.

 ![Een stroom beheren](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

De stroom blijft actief totdat u deze stopt met de optie **stroom** uitschakelen.

Als u geen e-mail meldingen over leads krijgt, betekent dit dat er nieuwe leads zijn toegevoegd aan de Azure-tabel. Als er stroom fouten zijn, krijgt u een e-mail zoals dit voor beeld.

 ![E-mail melding voor stroom fout](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Uw aanbieding configureren voor het verzenden van leads naar de Azure-tabel

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, voert u de volgende stappen uit.

1. Ga naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.

1. Selecteer in het gedeelte **klant leads** de optie **verbinding maken**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="Leads van klanten":::

1. Selecteer in het pop-upvenster **verbindings Details** de optie **Azure-tabel** voor de **doel locatie** van de lead. 
     ![Lead beheer, verbindings Details](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. Plak de connection string uit het Azure Storage-account dat u hebt gemaakt door de eerdere stappen in het vak **opslag account Connection String** te volgen.
     ![Lead beheer, opslag account voor verbindings Details](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **E-mail adres van contact persoon**: Geef e-mails op voor personen in uw bedrijf die e-mail meldingen moeten ontvangen wanneer er een nieuwe lead wordt ontvangen. U kunt meerdere e-mail berichten opgeven door deze te scheiden met een punt komma.

1. Selecteer **OK**.

Selecteer de knop **valideren** om te controleren of u verbinding hebt gemaakt met een doel van een lead. Als dat lukt, hebt u een test lead in de doel locatie van de lead.

>[!NOTE]
>U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.

Wanneer leads worden gegenereerd, stuurt micro soft leads naar de Azure-tabel. Als u een stroom hebt geconfigureerd, wordt er ook een e-mail bericht verzonden naar het e-mail adres dat u hebt geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over Lead beheer en probleem oplossing](../lead-management-faq.md)
