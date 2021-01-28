---
title: Apache Kafka REST-proxy-Azure HDInsight
description: Leer hoe u Apache Kafka bewerkingen uitvoert met behulp van een Kafka REST-proxy op Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: a9a007d33226c508e193368b08b189001bf53401
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944066"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interactie met Apache Kafka clusters in azure HDInsight met behulp van een REST-proxy

Met Kafka REST proxy kunt u met uw Kafka-cluster communiceren via een REST API via HTTPS. Deze actie houdt in dat uw Kafka-clients zich buiten uw virtuele netwerk kunnen bevinden. Clients kunnen eenvoudige, beveiligde HTTPS-aanroepen naar het Kafka-cluster maken, in plaats van te vertrouwen op Kafka-bibliotheken. In dit artikel wordt uitgelegd hoe u een Kafka-cluster maakt waarop een REST-proxy is ingeschakeld. Bevat ook een voorbeeld code die laat zien hoe u aanroepen naar REST proxy.

## <a name="rest-api-reference"></a>Naslaginformatie over REST-API

Voor bewerkingen die worden ondersteund door de Kafka-REST API, Zie [HDInsight Kafka rest proxy API Reference](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Achtergrond

![Kafka REST-proxy ontwerp](./media/rest-proxy/rest-proxy-architecture.png)

Zie [Apache Kafka rest proxy API](/rest/api/hdinsight-kafka-rest-proxy)voor een volledige specificatie van bewerkingen die worden ondersteund door de API.

### <a name="rest-proxy-endpoint"></a>REST-proxy-eind punt

Het maken van een HDInsight Kafka-cluster met REST proxy maakt een nieuw openbaar eind punt voor uw cluster, dat u in de **Eigenschappen** van uw HDInsight-cluster kunt vinden op de Azure Portal.

### <a name="security"></a>Beveiliging

De toegang tot de Kafka REST-proxy wordt beheerd met Azure Active Directory-beveiligings groepen. Wanneer u het Kafka-cluster maakt, geeft u de Azure AD-beveiligings groep op met REST endpoint Access. Kafka-clients die toegang nodig hebben tot de REST proxy moeten door de groeps eigenaar bij deze groep worden geregistreerd. De groeps eigenaar kan zich registreren via de portal of via Power shell.

Voor REST proxy-eindpunt aanvragen moet client toepassingen een OAuth-Token ophalen. Het token wordt gebruikt om het lidmaatschap van een beveiligings groep te controleren. Hieronder vindt u een voor beeld van een [client toepassing](#client-application-sample) waarin wordt uitgelegd hoe u een OAuth-token ophaalt. De client toepassing geeft het OAuth-token in de HTTPS-aanvraag door aan de REST-proxy.

> [!NOTE]
> Zie [toegang tot apps en bronnen beheren met Azure Active Directory groepen](../../active-directory/fundamentals/active-directory-manage-groups.md)voor meer informatie over Aad-beveiligings groepen. Zie [toegang tot Azure Active Directory webtoepassingen toestaan met de OAuth 2,0 code subsidie flow](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)voor meer informatie over de werking van OAuth-tokens.

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Kafka REST-proxy met netwerk beveiligings groepen
Als u uw eigen VNet en netwerk verkeer met netwerk beveiligings groepen wilt beheren, kunt u **Inkomend** verkeer op poort **9400** naast poort 443 toestaan. Dit zorgt ervoor dat de Kafka REST-proxy server bereikbaar is.

## <a name="prerequisites"></a>Vereisten

1. U registreert een toepassing met Azure AD. De clienttoepassingen die u schrijft om te communiceren met de Kafka REST-proxy gebruiken de id en het geheim van deze toepassing voor verificatie bij Azure.

1. Maak een Azure AD-beveiligingsgroep. Voeg de toepassing die u hebt geregistreerd bij Azure AD aan de beveiligings groep toe als **lid** van de groep. Deze beveiligingsgroep wordt gebruikt om te bepalen welke toepassingen mogen communiceren met de REST-proxy. Zie [Een basisgroep maken en leden toevoegen met Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie over het maken van Azure AD-groepen.

    Controleer of de groep van het type **Security** is.
    ![Beveiligings groep](./media/rest-proxy/rest-proxy-group.png)

    Controleer of de toepassing lid is van de groep.
    ![Lidmaatschap controleren](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Een Kafka-cluster maken waarop REST-proxy is ingeschakeld

In de onderstaande stappen wordt gebruikgemaakt van de Azure Portal. Zie voor een voor beeld met behulp van Azure CLI [Apache Kafka rest-proxy cluster maken met behulp van Azure cli](tutorial-cli-rest-proxy.md).

1. Schakel tijdens de werk stroom voor het maken van het Kafka-cluster op het tabblad **beveiliging en netwerk** de optie **Kafka rest proxy inschakelen** in.

     ![Scherm afbeelding toont de pagina H D Insight-cluster maken met beveiliging en netwerken geselecteerd.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klik op **beveiligings groep selecteren**. Selecteer in de lijst met beveiligings groepen de beveiligings groep waartoe u toegang wilt hebben tot de REST-proxy. U kunt het zoekvak gebruiken om de juiste beveiligings groep te vinden. Klik onderaan op de knop **selecteren** .

     ![Scherm afbeelding toont de pagina H D Insight-cluster maken met de optie om een beveiligings groep te selecteren.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Voltooi de resterende stappen voor het maken van uw cluster zoals beschreven in [Apache Kafka cluster maken in azure HDInsight met behulp van Azure Portal](./apache-kafka-get-started.md).

1. Zodra het cluster is gemaakt, gaat u naar de eigenschappen van het cluster om de Kafka REST proxy-URL op te nemen.

     ![REST proxy-URL weer geven](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Voor beeld van client toepassing

U kunt de python-code hieronder gebruiken om te communiceren met de REST-proxy op uw Kafka-cluster. Als u het code voorbeeld wilt gebruiken, volgt u deze stappen:

1. Sla de voorbeeld code op een computer waarop python is geïnstalleerd.
1. Installeer de vereiste python-afhankelijkheden door uit te voeren `pip3 install msal` .
1. Wijzig het gedeelte code om **deze eigenschappen te configureren** en de volgende eigenschappen voor uw omgeving bij te werken:

    |Eigenschap |Beschrijving |
    |---|---|
    |Tenant-id|De Azure-Tenant waar uw abonnement zich bevindt.|
    |Client-id|De ID voor de toepassing die u hebt geregistreerd in de beveiligings groep.|
    |Clientgeheim|Het geheim voor de toepassing die u hebt geregistreerd in de beveiligings groep.|
    |Kafkarest_endpoint|Haal deze waarde op via het tabblad **Eigenschappen** in het cluster overzicht, zoals beschreven in de [sectie implementatie](#create-a-kafka-cluster-with-rest-proxy-enabled). Deze moet de volgende indeling hebben: `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Voer vanaf de opdracht regel het python-bestand uit door het uit te voeren `sudo python3 <filename.py>`

Deze code voert de volgende actie uit:

1. Haalt een OAuth-token op uit Azure AD.
1. Laat zien hoe u een aanvraag voor Kafka REST-proxy kunt indienen.

Zie [python AuthenticationContext-klasse](/python/api/adal/adal.authentication_context.authenticationcontext)voor meer informatie over het ophalen van OAuth-tokens in python. U ziet mogelijk een vertraging terwijl `topics` deze niet worden gemaakt of verwijderd via de Kafka rest-proxy. Deze vertraging wordt veroorzaakt door het vernieuwen van de cache. Het veld **waarde** van de producer-API is verbeterd. Nu worden JSON-objecten en alle geserialiseerde formulieren geaccepteerd.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Hieronder vindt u een voor beeld van het ophalen van een token van Azure voor REST-proxy met behulp van een krul opdracht. **U hebt de `scope=https://hib.azurehdinsight.net/.default` opgegeven tijdens het ophalen van een token nodig.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Volgende stappen

* [Kafka REST proxy API-naslag documenten](/rest/api/hdinsight-kafka-rest-proxy/)
