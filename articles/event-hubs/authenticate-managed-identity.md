---
title: Een beheerde identiteit verifiëren met Azure Active Directory
description: Dit artikel bevat informatie over het verifiëren van een beheerde identiteit met Azure Active Directory om toegang te krijgen tot Azure Event Hubs-resources
ms.topic: conceptual
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2070cfd94b39a08afb86ffd3579f1116faac72d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805311"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Event Hubs bronnen
Azure Event Hubs ondersteunt Azure Active Directory (Azure AD)-verificatie met [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md). Beheerde identiteiten voor Azure-resources kunnen toegang tot Event Hubs resources toestaan met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in azure Virtual Machines (Vm's), functie-apps, Virtual Machine Scale Sets en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.

In dit artikel wordt beschreven hoe u toegang kunt verlenen tot een Event Hub met behulp van een beheerde identiteit van een Azure-VM.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen
Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om Event Hubs-resources van uw virtuele machine te autoriseren, moet u eerst beheerde identiteiten voor Azure-resources inschakelen op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Machtigingen verlenen aan een beheerde identiteit in azure AD
Als u een aanvraag wilt indienen voor het Event Hubs service van een beheerde identiteit in uw toepassing, moet u eerst Azure RBAC-instellingen (op rollen gebaseerd toegangs beheer) voor die beheerde identiteit configureren. Azure Event Hubs definieert Azure-rollen die machtigingen omvatten voor het verzenden en lezen van Event Hubs. Wanneer de Azure-rol wordt toegewezen aan een beheerde identiteit, krijgt de beheerde identiteit toegang tot Event Hubs gegevens in het juiste bereik.

Zie [verifiëren met Azure Active Directory voor toegang tot Event hubs resources](authorize-access-azure-active-directory.md)voor meer informatie over het toewijzen van Azure-rollen.

## <a name="use-event-hubs-with-managed-identities"></a>Event Hubs gebruiken met beheerde identiteiten
Als u Event Hubs met beheerde identiteiten wilt gebruiken, moet u de rol en het juiste bereik toewijzen aan de identiteit. De procedure in deze sectie maakt gebruik van een eenvoudige toepassing die wordt uitgevoerd onder een beheerde identiteit en die toegang heeft tot Event Hubs resources.

Hier gebruiken we een voor beeld-webtoepassing die wordt gehost in [Azure app service](https://azure.microsoft.com/services/app-service/). Zie [een ASP.net core web-app maken in azure](../app-service/quickstart-dotnetcore.md) voor stapsgewijze instructies voor het maken van een webtoepassing

Wanneer de toepassing is gemaakt, voert u de volgende stappen uit: 

1. Ga naar **instellingen** en selecteer **identiteit**. 
1. Selecteer de **status** die moet worden **ingeschakeld**. 
1. Selecteer **Opslaan** om de instelling op te slaan. 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="Beheerde identiteit voor een web-app":::
4. Selecteer **Ja** in het informatie bericht. 

    Zodra u deze instelling hebt ingeschakeld, wordt er een nieuwe service-identiteit gemaakt in uw Azure Active Directory (Azure AD) en geconfigureerd in de App Service host.

    Wijs deze service-identiteit nu toe aan een rol in het vereiste bereik in uw Event Hubs resources.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure-rollen toewijzen met behulp van de Azure Portal
Als u een rol wilt toewijzen aan Event Hubs resources, gaat u naar die resource in de Azure Portal. Geef de Access Control (IAM)-instellingen voor de resource weer en volg deze instructies voor het beheren van roltoewijzingen:

> [!NOTE]
> Met de volgende stappen wordt een service-identiteits functie toegewezen aan uw Event Hubs-naam ruimten. U kunt dezelfde stappen volgen om een rollen bereik toe te wijzen aan een Event Hubs resource. 

1. Ga in het Azure Portal naar uw Event Hubs naam ruimte en geef het **overzicht** voor de naam ruimte weer. 
1. Selecteer **Access Control (IAM)** in het menu links om de instellingen voor toegangs beheer voor de Event hub weer te geven.
1.  Selectter het tabblad **Roltoewijzingen** om de lijst met roltoewijzingen te zien.
3.  Selecteer **toevoegen** en selecteer vervolgens * * roltoewijzing toevoegen * * *.
4.  Voer op de pagina **roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer bij **rol** de Event hubs rol die u wilt toewijzen. In dit voor beeld is de **gegevens eigenaar van Azure Event hubs**.
    1. Selecteer **app service** onder door het **systeem toegewezen beheerde identiteit** voor het veld **toegang toewijzen aan** . 
    1. Selecteer het **abonnement** waarin de beheerde identiteit voor de web-app is gemaakt.
    1. Selecteer de **beheerde identiteit** voor de web-app die u hebt gemaakt. De standaard naam voor de identiteit is hetzelfde als de naam van de web-app. 
    1. Selecteer vervolgens **Opslaan**. 
    
        ![Pagina roltoewijzing toevoegen](./media/authenticate-managed-identity/add-role-assignment-page.png)

    Zodra u de rol hebt toegewezen, heeft de webtoepassing toegang tot de Event Hubs resources onder het gedefinieerde bereik. 

    > [!NOTE]
    > Zie [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)voor een lijst met services die beheerde identiteiten ondersteunen.

### <a name="test-the-web-application"></a>De webtoepassing testen
1. Een Event Hubs-naamruimte en een Event Hub maken. 
2. De web-app implementeren in Azure. Zie de volgende sectie met tabbladen voor koppelingen naar de webtoepassing op GitHub. 
3. Zorg ervoor dat SendReceive. aspx is ingesteld als het standaard document voor de web-app. 
3. De **identiteit** voor de web-app inschakelen. 
4. Wijs deze identiteit toe aan de **Event hubs rol gegevens eigenaar** op het niveau van de naam ruimte of het event hub niveau. 
5. Voer de webtoepassing uit, voer de naam van de naam ruimte en Event Hub naam, een bericht in en selecteer **verzenden**. Selecteer **ontvangen** om de gebeurtenis te ontvangen. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure. Messa ging. Event hubs (meest recent)](#tab/latest)
U kunt nu de webtoepassing starten en uw browser naar de aspx-voorbeeld pagina wijzen. U kunt de voor beeld-webtoepassing vinden waarmee gegevens worden verzonden en ontvangen van Event Hubs-resources in de [github opslag plaats](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Installeer het nieuwste pakket van [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)en begin met het verzenden van gebeurtenissen naar Event hubs met **EventHubProducerClient** en het ontvangen van gebeurtenissen via **EventHubConsumerClient**. 

> [!NOTE]
> Zie [gebeurtenissen publiceren met Azure Identity sample op github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)voor een Java-voor beeld dat gebruikmaakt van een beheerde identiteit voor het publiceren van gebeurtenissen in een event hub.

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (verouderd)](#tab/old)
U kunt nu de webtoepassing starten en uw browser naar de aspx-voorbeeld pagina wijzen. U kunt de voor beeld-webtoepassing vinden waarmee gegevens worden verzonden en ontvangen van Event Hubs-resources in de [github opslag plaats](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Installeer het meest recente pakket van [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)en begin met het verzenden naar en ontvangen van gegevens van Event hubs met behulp van de EventHubClient, zoals wordt weer gegeven in de volgende code: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs voor Kafka
U kunt Apache Kafka-toepassingen gebruiken om berichten te verzenden naar en berichten te ontvangen van Azure Event Hubs met behulp van beheerde identiteit OAuth. Zie het volgende voor beeld op GitHub: [Event hubs voor Kafka-berichten verzenden en ontvangen met beheerde identiteit OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Voorbeelden
- Voor beelden van **Azure. Messa ging. Event hubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Micro soft. Azure. Event hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)-voor beelden. 
    
    In deze voor beelden wordt de oude bibliotheek van **micro soft. Azure. Event hubs** gebruikt, maar u kunt deze eenvoudig bijwerken met de meest recente **Azure. Messa ging. Event hubs** -bibliotheek. Zie de [hand leiding voor het migreren van micro soft. Azure. Event hubs naar Azure. Messa ging. Event hubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)om het voor beeld te verplaatsen van het gebruik van de oude bibliotheek naar een nieuwe.
    Dit voor beeld is bijgewerkt om de meest recente **Azure. Messa ging. Event hubs** -bibliotheek te gebruiken.
- [Event Hubs voor Kafka-berichten verzenden en ontvangen met beheerde identiteit OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Volgende stappen
- Zie het volgende artikel voor meer informatie over beheerde identiteiten voor Azure-resources: [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
- Raadpleeg de volgende verwante artikelen:
    - [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met behulp van Azure Active Directory](authenticate-application.md)
    - [Aanvragen voor Azure Event Hubs verifiëren met behulp van hand tekeningen voor gedeelde toegang](authenticate-shared-access-signature.md)
    - [Toegang tot Event Hubs resources autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Toegang tot Event Hubs resources autoriseren met behulp van hand tekeningen voor gedeelde toegang](authorize-access-shared-access-signature.md)
