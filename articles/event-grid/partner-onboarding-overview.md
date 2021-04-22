---
title: Overzicht van onboarding van partners (Azure Event Grid)
description: Biedt een overzicht van hoe u onboarding kunt Event Grid partner.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 9af80efc1ba342768f9bb6d504f921b52494d955
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890704"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Overzicht van onboarding van partners (Azure Event Grid)

In dit artikel wordt beschreven hoe u privé de resources van Azure Event Grid partner gebruikt en hoe u een openbaar beschikbaar partneronderwerptype wordt.

U hebt geen speciale machtiging nodig om te beginnen met het gebruik van Event Grid resourcetypen die zijn gekoppeld aan het publiceren van gebeurtenissen als Event Grid partner. U kunt ze vandaag nog gebruiken om gebeurtenissen privé te publiceren naar uw eigen Azure-abonnementen en om het resourcemodel te testen als u overweegt een partner te worden.

> [!NOTE]
> Zie Onboarden als [Event Grid-partner (Azure Portal)](onboard-partner.md)voor stapsgewijs instructies over onboarding als Event Grid-partner met behulp van de Azure Portal. 

## <a name="how-partner-events-work"></a>Hoe partnergebeurtenissen werken
De functie Partnergebeurtenissen gebruikt de bestaande architectuur die Event Grid al gebruikt voor het publiceren van gebeurtenissen uit Azure-resources, zoals Azure Storage en Azure IoT Hub, en maakt deze hulpprogramma's openbaar beschikbaar voor iedereen. Het gebruik van deze hulpprogramma's is standaard alleen privé voor uw Azure-abonnement. Als u uw gebeurtenissen openbaar beschikbaar wilt maken, vult u het formulier in en [neem contact op met Event Grid team](mailto:gridpartner@microsoft.com).

Met de functie Partnergebeurtenissen kunt u gebeurtenissen publiceren naar Azure Event Grid voor multitenantverbruik.

## <a name="onboarding-and-event-publishing-overview"></a>Overzicht van onboarding en gebeurtenispublicatie

### <a name="partner-flow"></a>Partnerstroom

1. Maak een Azure-tenant als u er nog geen hebt.
1. Gebruik de Azure CLI om een nieuwe `partnerRegistration` Event Grid. Deze resource bevat informatie zoals weergavenaam, beschrijving, installatie-URI, en meer.

    ![Een partneronderwerp maken](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Maak een of meer partnernaamruimten in elke regio waar u gebeurtenissen wilt publiceren. De Event Grid service voor het publiceren van een eindpunt (bijvoorbeeld `https://contoso.westus-1.eventgrid.azure.net/api/events` ) en toegangssleutels.

    ![Een partnernaamruimte maken](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Bied klanten een manier om zich in uw systeem te registreren voor een partneronderwerp.
1. Neem contact op Event Grid team om hen te laten weten dat u wilt dat uw partneronderwerptype openbaar wordt.

### <a name="customer-flow"></a>Klantstroom

1. Uw klant bezoekt de Azure Portal om de azure-abonnements-id en resourcegroep te noteren waarin ze het partneronderwerp willen maken.
1. De klant vraagt een partneronderwerp aan via uw systeem. Als reactie maakt u een gebeurtenistunnel naar de naamruimte van uw partner.
1. Event Grid maakt een **partneronderwerp in** behandeling in het Azure-abonnement en de resourcegroep van de klant.

    ![Een gebeurteniskanaal maken](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. De klant activeert het partneronderwerp via de Azure Portal. Gebeurtenissen kunnen nu van uw service naar het Azure-abonnement van de klant stromen.

    ![Een partneronderwerp activeren](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Resourcemodel
Het volgende resourcemodel is voor partnergebeurtenissen.

### <a name="partner-registrations"></a>Partnerregistraties
* Resource: `partnerRegistrations`
* Gebruikt door: Partners
* Beschrijving: legt de globale metagegevens van de SaaS-partner (software as a service) vast (bijvoorbeeld naam, weergavenaam, beschrijving, URI instellen).
    
    Het maken of bijwerken van een partnerregistratie is een self-serve bewerking voor de partners. Met deze self-serve-mogelijkheid kunnen partners de volledige end-to-end-stroom bouwen en testen.
    
    Alleen door Microsoft goedgekeurde partnerregistraties kunnen worden gevonden door klanten.
* Bereik: gemaakt in het Azure-abonnement van de partner. Metagegevens zijn zichtbaar voor klanten nadat deze openbaar zijn gemaakt.

### <a name="partner-namespaces"></a>Partnernaamruimten
* Resource: `partnerNamespaces`
* Gebruikt door: Partners
* Beschrijving: biedt een regionale resource voor het publiceren van klantgebeurtenissen naar. Elke partnernaamruimte heeft een publicatie-eindpunt en auth-sleutels. De naamruimte is ook de manier waarop de partner een partneronderwerp aanvraagt voor een bepaalde klant en een lijst met actieve klanten bevat.
* Bereik: het abonnement van de partner is van toepassing.

### <a name="event-channel"></a>Gebeurteniskanaal
* Resource: `partnerNamespaces/eventChannels`
* Gebruikt door: Partners
* Beschrijving: de gebeurteniskanalen zijn een mirror van het partneronderwerp van de klant. Door een gebeurteniskanaal te maken en het Azure-abonnement en de resourcegroep van de klant op te geven in de metagegevens, geeft u een signaal aan Event Grid een partneronderwerp voor de klant te maken. Event Grid een aanroep Azure Resource Manager om een bijbehorend partneronderwerp te maken in het abonnement van de klant. Het partneronderwerp wordt gemaakt met de status In behandeling. Er is een een-op-een-koppeling tussen elk gebeurteniskanaal en partneronderwerp.
* Bereik: het abonnement van de partner is van toepassing.

### <a name="partner-topics"></a>Partneronderwerpen
* Resource: `partnerTopics`
* Gebruikt door: Klanten
* Beschrijving: Partneronderwerpen zijn vergelijkbaar met aangepaste onderwerpen en systeemonderwerpen in Event Grid. Elk partneronderwerp is gekoppeld aan een specifieke bron (bijvoorbeeld ) en een specifiek `Contoso:myaccount` partneronderwerptype (bijvoorbeeld Contoso). Klanten maken gebeurtenisabonnementen op het partneronderwerp om gebeurtenissen naar verschillende gebeurtenis-handlers te leiden.

    Klanten kunnen deze resource niet rechtstreeks maken. De enige manier om een partneronderwerp te maken, is via een partnerbewerking die een gebeurteniskanaal maakt.
* Bereik: Het abonnement van de klant is van toepassing.

### <a name="partner-topic-types"></a>Partneronderwerptypen
* Resource: `partnerTopicTypes`
* Gebruikt door: Klanten
* Beschrijving: Partneronderwerptypen zijn tenantbrede resourcetypen waarmee klanten de lijst met goedgekeurde partneronderwerptypen kunnen vinden. De URL ziet er als uit https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Bereik: Globaal

## <a name="publish-events-to-event-grid"></a>Gebeurtenissen publiceren naar Event Grid
Wanneer u een partnernaamruimte maakt in een Azure-regio, krijgt u een regionaal eindpunt en bijbehorende auth-sleutels. Publiceer batches met gebeurtenissen naar dit eindpunt voor alle gebeurteniskanalen van klanten in die naamruimte. Op basis van het bronveld in de gebeurtenis Azure Event Grid elke gebeurtenis aan de bijbehorende partneronderwerpen.

### <a name="event-schema-cloudevents-v10"></a>Gebeurtenisschema: CloudEvents v1.0
Gebeurtenissen publiceren naar Azure Event Grid met behulp van het CloudEvents 1.0-schema. Event Grid ondersteunt zowel de gestructureerde modus als de batchmodus. CloudEvents 1.0 is het enige ondersteunde gebeurtenisschema voor partnernaamruimten.

### <a name="example-flow"></a>Voorbeeldstroom

1.  De publicatieservice doet een HTTP POST naar `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
1.  Neem in de aanvraag een headerwaarde op met de naam aeg-sas-key die een sleutel voor verificatie bevat. Deze sleutel wordt ingericht tijdens het maken van de partnernaamruimte. Een geldige headerwaarde is bijvoorbeeld aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg===.
1.  Stel de Content-Type-header in op 'application/cloudevents-batch+json; charset=UTF-8a".
1.  Voer een HTTP POST-query uit naar de publicatie-URL met een batch gebeurtenissen die overeenkomen met die regio. Bijvoorbeeld:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Nadat u een bericht hebt geplaatst op het eindpunt van de partnernaamruimte, ontvangt u een antwoord. Het antwoord is een standaard HTTP-antwoordcode. Enkele veelvoorkomende antwoorden zijn:

| Resultaat                             | Antwoord              |
|------------------------------------|-----------------------|
| Geslaagd                            | 200 OK                |
| Gebeurtenisgegevens hebben een onjuiste indeling    | 400 Ongeldige aanvraag       |
| Ongeldige toegangssleutel                 | 401 Onbevoegd      |
| Onjuist eindpunt                 | 404 Niet gevonden         |
| Matrix of gebeurtenis overschrijdt groottelimieten | 413 Nettolading is te groot |

## <a name="references"></a>Referenties

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM-sjabloon](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM-sjabloonschema](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST-API's](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [CLI-extensie](/cli/azure/eventgrid)

### <a name="sdks"></a>SDK's
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Volgende stappen
- [Overzicht van partneronderwerpen](partner-events-overview.md)
- [Onboardingformulier voor partneronderwerpen](https://aka.ms/gridpartnerform)
- [Auth0-partneronderwerp](auth0-overview.md)
- [Het onderwerp Auth0-partner gebruiken](auth0-how-to.md)
