---
title: Herstel na noodgevallen voor aangepaste onderwerpen in Azure Event Grid
description: In deze zelfstudie wordt u begeleid bij het instellen van uw gebeurtenisarchitectuur bij herstel als de Event Grid-service in een regio niet goed functioneert.
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e37cb6a0679ee2e249de4ed8fa31c40d5082ea4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020140"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Uw eigen herstel na noodgevallen bouwen voor aangepaste onderwerpen in Azure Event Grid
Herstel na noodgevallen is gericht op het herstellen van een ernstig verlies van de functionaliteit van de toepassing. In deze zelfstudie wordt u begeleid bij het instellen van uw gebeurtenisarchitectuur bij herstel als de Event Grid-service in een bepaalde regio niet goed functioneert.

In deze zelfstudie leert u hoe u een actief-passief-failover-architectuur voor aangepaste onderwerpen in Event Grid maakt. U bereikt failover door uw onderwerpen en abonnementen voor twee regio's te spiegelen en vervolgens failover te beheren als een onderwerp niet meer in orde is. In deze zelfstudie wordt door de architectuur op alle verkeer failover toegepast. Bij deze instelling is het belangrijk te weten dat gebeurtenissen die al actief zijn, pas worden hersteld als de verdachte regio weer in orde is.

> [!NOTE]
> Event Grid ondersteunt nu automatisch herstel na geonoodgeval (GeoDR) aan de serverzijde. U kunt de logica voor noodherstel aan de clientzijde nog steeds implementeren als u meer controle wilt over het failoverproces. Zie [Herstel na geonoodgeval aan de serverzijde in Azure Event Grid](geo-disaster-recovery.md) voor meer informatie over automatische GeoDR.

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voor het testen van uw failoverconfiguratie hebt u een eindpunt nodig waar u uw gebeurtenissen kunt ontvangen. Het eindpunt maakt geen deel uit van uw failoverinfrastructuur, maar fungeert als de gebeurtenis-handler om de test eenvoudiger te laten verlopen.

Ter vereenvoudiging van de test gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>

1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`
Schrijf deze URL op, want u hebt deze later nodig.

1. De site wordt weergegeven, maar er zijn nog geen gebeurtenissen op gepubliceerd.

   ![Nieuwe site weergeven](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Primaire en secundaire onderwerpen maken

Maak eerst twee Event Grid-onderwerpen. Deze onderwerpen fungeren als primaire en secundaire onderwerp. Uw gebeurtenissen stromen standaard door uw primaire onderwerp. Bij uitval van service in de primaire regio, neemt het secundaire onderwerp het over.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

1. Ga naar de linkerbovenhoek van het Azure-hoofdmenu, kies **Alle services** > zoek naar **Event Grid** > selecteer **Event Grid-onderwerpen**.

   ![Menu Event Grid-onderwerpen](./media/custom-disaster-recovery/select-topics-menu.png)

    Selecteer de ster naast Event Grid-onderwerpen om deze aan het resourcemenu toe te voegen, zodat u het later snel bij de hand hebt.

1. Selecteer **+Toevoegen** in het menu Event Grid-onderwerpen om het primaire onderwerp te maken.

   * Geef het onderwerp een logische naam en voeg er '-primaire' als achtervoegsel aan toe om het makkelijker terug te kunnen vinden.
   * De regio voor dit onderwerp wordt uw primaire regio.

     ![Dialoogvenster voor het maken van een primaire Event Grid-onderwerp](./media/custom-disaster-recovery/create-primary-topic.png)

1. Als het onderwerp is gemaakt, gaat u er naartoe en kopieert u het **Eindpunt onderwerp**. U hebt de URL later nog nodig.

    ![Primair onderwerp van Event Grid](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Haal de toegangssleutel voor het onderwerp op. Deze hebt u later ook nog nodig. Klik op **Toegangssleutels** in het resourcemenu en kopieer Sleutel 1.

    ![Sleutel voor primaire onderwerp ophalen](./media/custom-disaster-recovery/get-primary-access-key.png)

1. Klik in de blade onderwerp op **+Gebeurtenisabonnement** om een abonnement te maken waarmee verbinding wordt gemaakt met het proces waarmee u zich abonneert op de website voor de gebeurtenisontvanger die u in de vereisten voor de zelfstudie hebt gemaakt.

   * Geef het gebeurtenisabonnement een logische naam en voeg er '-primaire' aan toe om het makkelijker terug te kunnen vinden.
   * Selecteer Webhook van eindpunttype.
   * Stel het eindpunt in op de gebeurtenis-URL van de gebeurtenisontvanger. Deze moet eruitzien als: `https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Schermopname van de pagina Gebeurtenisabonnement maken - Basis met waarden Naam, Eindpunttype en Eindpunt gemarkeerd.](./media/custom-disaster-recovery/create-primary-es.png)

1. Herhaal dezelfde stroom om het secundaire onderwerp en abonnement te maken. Vervang hier het achtervoegsel 'primaire' door 'secundaire' om het makkelijker te kunnen vinden. Controleer ten slotte of u het in een andere Azure-regio hebt ondergebracht. Hoewel u het overal in kunt onderbrengen, wordt u aangeraden [Gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md) te gebruiken. Als u het secundaire onderwerp en abonnement in een andere regio hebt ondergebracht, weet u zeker dat de nieuwe gebeurtenissen zullen stromen, ook als de primaire regio uitvalt.

U beschikt nu over:

   * Een website voor een gebeurtenisontvanger om mee te testen.
   * Een primair onderwerp in uw primaire regio.
   * Een primair gebeurtenisabonnement waardoor uw primaire onderwerp is verbonden met de website met de gebeurtenisontvanger.
   * Een secundair onderwerp in uw secundaire regio.
   * Een secundair gebeurtenisabonnement waardoor uw primaire onderwerp is verbonden met de website met de gebeurtenisontvanger.

## <a name="implement-client-side-failover"></a>Failover aan clientzijde implementeren

U hebt nu een regionaal redundant paar onderwerpen en abonnementen ingesteld. U bent nu klaar failover aan de clientzijde te implementeren. U kunt dit op verschillende manieren doen, maar alle implementaties van failovers hebben een overeenkomstige eigenschap: als het ene onderwerp niet meer in orde is, wordt het verkeer omgeleid naar het andere.

### <a name="basic-client-side-implementation"></a>Eenvoudige implementatie aan de clientzijde

De volgende voorbeeldcode is een eenvoudige .NET-uitgever die altijd zal proberen om eerst uw primaire onderwerp te publiceren. Als dit niet lukt, wordt er failover uitgevoerd naar het secundaire onderwerp. In beide gevallen wordt ook de status-API van het andere onderwerp gecontroleerd door een GET uit te voeren op `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`. Een onderwerp dat in orde is, reageert altijd met **200 OK** als er een GET wordt uitgevoerd op het **/api/health**-eindpunt.

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Uitproberen

Nu alle onderdelen op hun plaats staan, kunt u de implementatie van de failover testen. Voer het bovenstaande voorbeeld uit in Visual Studio Code of in uw favoriete omgeving. Vervang de volgende vier waarden door de eindpunten en sleutels van uw onderwerpen:

   * primaryTopic: het eindpunt voor uw primaire onderwerp.
   * secondaryTopic: het eindpunt voor uw secundaire onderwerp.
   * primaryTopicKey: de sleutel voor uw primaire onderwerp.
   * secondaryTopicKey: de sleutel voor uw secundaire onderwerp.

Voer de gebeurtenisuitgever uit. Als het goed is, ziet u uw testgebeurtenissen weergegeven in de Event Grid-viewer zoals hieronder.

![Primair gebeurtenisabonnement van Event Grid](./media/custom-disaster-recovery/event-grid-viewer.png)

Om te controleren of de failover werkt, kunt u een paar tekens in het primaire onderwerp wijzigen om deze ongeldig te maken. Voer de gebeurtenisuitgever nogmaals uit. Als het goed is ziet u nog steeds nieuwe gebeurtenissen in de Event Grid-viewer verschijnen. Maar als u naar de console kijkt, ziet u dat ze nu via het secundaire onderwerp worden gepubliceerd.

### <a name="possible-extensions"></a>Mogelijke uitbreidingen

U kunt dit voorbeeld op verschillende manieren naar behoefte uitbreiden. Voor scenario's met grote volumes is het mogelijk de status-API van het onderwerp onafhankelijk te controleren. Op die manier hoeft u een uitgevallen onderwerp niet voor elke afzonderlijke publicatie te controleren. Als u weet dat een onderwerp niet in orde is, kunt u terugvallen op publicatie naar het secundaire onderwerp.

U kunt ook failback-logica implementeren op basis van uw behoeften. Als u het belangrijk vindt te publiceren naar het dichtstbijzijnde datacentrum teneinde de latentie te verminderen, kunt u de status-API van een onderwerp waarvoor failover is uitgevoerd, periodiek testen. Zodra de API weer in orde is, weet u dat het veilig is het meer nabij gelegen datacentrum weer te kunnen gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Informatie over het [ontvangen van gebeurtenissen op een HTTP-eindpunt](./receive-events.md)
- Leer [gebeurtenissen routeren naar Hybride verbindingen](./custom-event-to-hybrid-connection.md)
- Informatie over [herstel na noodgevallen met Azure DNS en Traffic Manager](../networking/disaster-recovery-dns-traffic-manager.md)
