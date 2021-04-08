---
title: Lead beheer met een HTTPS-eind punt-micro soft Commercial Marketplace
description: Meer informatie over het gebruik van energie automatisering en een HTTPS-eind punt voor het beheren van leads van Microsoft AppSource en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 5bea2cf256e30bd896957bbee0e0ad824057a569
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247179"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Een HTTPS-eind punt gebruiken om leads voor commerciële Marketplace te beheren

Als uw Customer Relationship Management-systeem (CRM) niet expliciet wordt ondersteund in het partner centrum om Microsoft AppSource en Azure Marketplace-leads te ontvangen, kunt u een HTTPS-eind punt in [automatische energie automatisering](https://powerapps.microsoft.com/automate-processes/) gebruiken om deze leads af te handelen. Met een HTTPS-eind punt kunnen commerciële Marketplace-leads worden verzonden als een e-mail melding of ze kunnen worden geschreven naar een CRM-systeem dat wordt ondersteund door het automatiseren van de stroom.

In dit artikel wordt uitgelegd hoe u een nieuwe stroom maakt in automatische energie voor het genereren van de HTTP POST-URL die u gebruikt om leads te configureren in het partner centrum. Het bevat ook stappen voor het testen van uw stroom met behulp van [postman](https://www.getpostman.com/downloads/).

>[!NOTE]
>Voor de stroom voor het automatisch uitvoeren van connectors die in deze instructies worden gebruikt, is een betaald abonnement vereist om te automatiseren. Zorg ervoor dat u dit account hebt voordat u deze stroom configureert.

## <a name="create-a-flow-by-using-power-automate"></a>Een stroom maken met behulp van energie automatisering

1. Open de webpagina voor [automatische energie automatisering](https://flow.microsoft.com/) . Selecteer **Aanmelden**. Als u nog geen account hebt, selecteert u **Aanmelden gratis registreren** om een gratis energiebeheer account te maken.

1. Meld u aan en selecteer **mijn stromen** in het menu.

    ![Meld u aan bij mijn stromen](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Onder **+ Nieuw** selecteert u **+ direct, leeg**.

    ![Mijn stromen + geautomatiseerd--leeg](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Geef uw stroom een naam en selecteer vervolgens onder **kiezen hoe deze stroom moet worden geactiveerd**, **Wanneer een HTTP-aanvraag wordt ontvangen**.

    ![Een automatische stroom venster knop overs Laan maken](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Klik op de stap van de stroom om deze uit te vouwen.

    ![De stroom stap uitvouwen](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Gebruik een van de volgende methoden om het JSON-schema van de **aanvraag tekst** te configureren:

    - Kopieer het JSON-schema in het tekstvak aanvraag tekst van **JSON-schema** .
    - Selecteer **Voorbeeldnettolading om een schema te genereren**. Plak in het tekstvak **een voor beeld van een JSON-nettolading invoeren of plakken** in het JSON-voor beeld. Selecteer **gereed** om het schema te maken.

    **JSON-schema**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "Description": {
          "id": "/properties/Description",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **JSON-voor beeld**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>U kunt op dit punt in de configuratie selecteren om verbinding te maken met een CRM-systeem of een e-mail melding te configureren. Volg de overige instructies op basis van uw keuze.

### <a name="connect-to-a-crm-system"></a>Verbinding maken met een CRM-systeem

1. Selecteer **+ nieuwe stap**.
1. Kies het CRM-systeem van uw keuze door ernaar te zoeken waar **Zoek connectors en acties** worden vermeld. Selecteer deze op het tabblad **acties** met de actie om een nieuwe record te maken. In het volgende scherm ziet u **een nieuwe record (Dynamics 365)** als voor beeld.

    ![Een nieuwe record maken](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Geef de **naam** van de organisatie op die aan het CRM-systeem is gekoppeld. Selecteer **leads** in de vervolg keuzelijst **entiteits naam** .

    ![Leads selecteren](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Bij het automatiseren van de stroom wordt een formulier weer gegeven voor het leveren van lead gegevens. U kunt items van de invoer aanvraag toewijzen door dynamische inhoud toe te voegen. In het volgende scherm ziet u **OfferTitle** als voor beeld.

    ![Dynamische inhoud toevoegen](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Wijs de gewenste velden toe en selecteer **Opslaan** om uw stroom op te slaan. Er wordt een HTTP POST-URL gemaakt en deze is toegankelijk in het venster **Wanneer een HTTP-aanvraag wordt ontvangen** . Kopieer deze URL met behulp van het Kopieer besturings element, dat zich rechts van de URL voor HTTP POST bevindt. Het is belang rijk dat u het Kopieer besturings element gebruikt, zodat u geen deel van de volledige URL mist. Sla deze URL op omdat u deze nodig hebt bij het configureren van het beheer van leads in de portal voor publiceren.

    ![Wanneer een HTTP-aanvraag wordt ontvangen](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>E-mail melding instellen

1. Nu u het JSON-schema hebt voltooid, selecteert u **+ nieuwe stap**.
1. Onder **Kies een actie** selecteert u **acties**.
1. Selecteer **een E-mail verzenden (Office 365 Outlook)** op het tabblad **acties** .

    >[!NOTE]
    >Als u een andere e-mail provider wilt gebruiken, zoekt en selecteert u in plaats daarvan **een e-mail melding (e-mail) verzenden** als actie.

    ![Een e-mailactie maken](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. Configureer in het venster **een E-mail verzenden** de volgende vereiste velden:

   - **Aan**: Voer ten minste één geldig e-mail adres in waarnaar de leads worden verzonden.
   - **Onderwerp**: energie automatisering biedt u de mogelijkheid om dynamische inhoud toe te voegen, zoals **LeadSource** weer gegeven in het volgende scherm. Begin door een veld naam in te voeren. Selecteer vervolgens de lijst met dynamische inhoud in het pop-upvenster. 

        >[!NOTE] 
        > Wanneer u veld namen toevoegt, kunt u elke naam volgen met een dubbele punt (:) en selecteer vervolgens **Enter** om een nieuwe rij te maken. Nadat u de veld namen hebt toegevoegd, kunt u elke gekoppelde para meter toevoegen uit de dynamische keuze lijst.

        ![Een e-mail actie toevoegen met behulp van dynamische inhoud](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Hoofd tekst**: Voeg de gewenste informatie toe aan de hoofd tekst van het e-mail bericht in de lijst met dynamische inhoud. Gebruik bijvoorbeeld LastName, FirstName, email en Company. Wanneer u klaar bent met het instellen van de e-mail melding, ziet u in het voor beeld in het volgende scherm.


       ![Voorbeeld van e-mailmelding](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Selecteer **Opslaan** om de stroom te volt ooien. Er wordt een HTTP POST-URL gemaakt en deze is toegankelijk in het venster **Wanneer een HTTP-aanvraag wordt ontvangen** . Kopieer deze URL met behulp van het Kopieer besturings element, dat zich rechts van de URL voor HTTP POST bevindt. Het is belang rijk dat u dit besturings element gebruikt, zodat u geen deel van de volledige URL hoeft te missen. Sla deze URL op omdat u deze nodig hebt bij het configureren van het beheer van leads in de portal voor publiceren.

   ![HTTP POST-URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testen

U kunt uw configuratie testen met behulp van [postman](https://app.getpostman.com/app/download/win64). Er is een online down load van Postman beschikbaar voor Windows. 

1. Start postman en selecteer **nieuwe**  >  **aanvraag** om uw test programma in te stellen. 

   ![Aanvraag voor het instellen van uw test programma](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Vul het formulier **aanvraag opslaan** in en sla het bestand op in de map die u hebt gemaakt.

   ![Aanvraag formulier opslaan](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Selecteer **bericht** in de vervolg keuzelijst. 

   ![Mijn stroom testen](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Plak de URL van de HTTP-POST uit de stroom die u hebt gemaakt in energie automatisering, waar de **aanvraag-URL wordt ingevoerd**.

   ![De HTTP POST-URL plakken](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Ga terug naar [Automatische stroom](https://flow.microsoft.com/). Zoek de stroom die u hebt gemaakt voor het verzenden van leads door naar **mijn stromen** te gaan via de menu balk voor het automatisch uitvoeren van de stroom. Selecteer het beletsel teken naast de naam van de stroom om meer opties weer te geven en selecteer **bewerken**.


1. Selecteer in de rechter bovenhoek de optie **testen** , selecteer **Ik voer de trigger actie uit** en selecteer vervolgens **testen**. Boven aan het scherm ziet u een indicatie dat de test is gestart.

   ![Ik voer de trigger actie optie uit](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Ga terug naar uw postman-app en selecteer **verzenden**.

   ![Knop verzenden](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Ga terug naar uw stroom en controleer het resultaat. Als alles werkt zoals verwacht, ziet u een bericht dat aangeeft dat de stroom is geslaagd.

   ![Resultaten controleren](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. U hebt ook een e-mail bericht ontvangen. Controleer uw postvak in. 

    >[!NOTE] 
    >Als u geen e-mail bericht van de test ziet, controleert u uw spam en ongewenste e-mail. In het volgende scherm ziet u alleen de veldlabels die u hebt toegevoegd tijdens het configureren van de e-mail melding. Als dit een daad werkelijke lead is die is gegenereerd op basis van uw aanbieding, ziet u ook de werkelijke gegevens van de contact persoon van de lead in de hoofd tekst en op de regel onderwerp.

   ![E-mail ontvangen](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Uw aanbieding configureren om leads naar het HTTPS-eind punt te verzenden

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, voert u de volgende stappen uit.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecteer uw aanbieding en ga naar het tabblad **installatie** van de aanbieding.

1. Selecteer in het gedeelte **klant leads** de optie **verbinding maken**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Leads van klanten":::

1. Selecteer in het pop-upvenster **verbindings Details** het **https-eind punt** voor de **doel locatie** van de lead. Plak de URL van de HTTP POST uit de stroom die u hebt gemaakt door eerdere stappen in het veld **https-eind punt URL** te volgen.
    ![Verbindings gegevens contact opnemen met e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. Voer onder **contact-e-mail** de e-mail adressen in voor personen in uw bedrijf die e-mail meldingen moeten ontvangen wanneer er een nieuwe lead wordt ontvangen. U kunt meerdere e-mail berichten opgeven door deze te scheiden met een punt komma.

1. Selecteer **OK**.

Selecteer de knop **valideren** om te controleren of u verbinding hebt gemaakt met een doel van een lead. Als dat lukt, hebt u een test lead in de doel locatie van de lead.

>[!NOTE] 
>U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.

Wanneer leads worden gegenereerd, stuurt micro soft leads naar de stroom. De leads worden doorgestuurd naar het CRM-systeem of het e-mail adres dat u hebt geconfigureerd.
