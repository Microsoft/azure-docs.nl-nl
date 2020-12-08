---
title: Herstel na nood geval met behulp van back-up en herstel in API Management implementeren
titleSuffix: Azure API Management
description: Meer informatie over het gebruik van back-up en herstel om herstel na nood gevallen in azure API Management uit te voeren.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2020
ms.author: apimpm
ms.openlocfilehash: 25356e7101293fc27d4107b3a618cfc481aee969
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779580"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Noodherstel implementeren met back-up en herstellen van services in Azure API Management

Door uw Api's via Azure API Management te publiceren en te beheren, profiteert u van de fout tolerantie en infrastructuur mogelijkheden die u op een andere manier hand matig ontwerpt, implementeert en beheert. Het Azure-platform vermindert een grote fractie van mogelijke fouten tegen een fractie van de kosten.

Als u de beschikbaarheids problemen wilt herstellen die van invloed zijn op de regio die als host fungeert voor uw API Management-service, kunt u op elk gewenst moment uw service in een andere regio maken. Afhankelijk van de beoogde herstel tijd wilt u mogelijk een stand-by-service in een of meer regio's blijven gebruiken. U kunt ook proberen de configuratie en inhoud te behouden in synchronisatie met de actieve service volgens uw Recovery Point Objective. De functies voor back-up en herstel van de service bieden de benodigde bouw stenen voor het implementeren van een strategie voor herstel na nood gevallen.

Back-up-en herstel bewerkingen kunnen ook worden gebruikt voor het repliceren van API Management service configuratie tussen de operationele omgevingen, zoals ontwikkeling en fase ring. Houd er rekening mee dat runtime gegevens, zoals gebruikers en abonnementen, ook worden gekopieerd, wat mogelijk niet altijd wenselijk is.

In deze hand leiding wordt beschreven hoe u back-up-en herstel bewerkingen automatiseert en hoe u een geslaagde verificatie van aanvragen voor back-up en herstel kunt garanderen door Azure Resource Manager.

> [!IMPORTANT]
> Met de herstel bewerking wordt de aangepaste hostname-configuratie van de doel service niet gewijzigd. We raden u aan om hetzelfde aangepaste hostname-en TLS-certificaat te gebruiken voor de services actief en stand-by, zodat het verkeer na het volt ooien van de herstel bewerking opnieuw kan worden omgeleid naar het stand-by-exemplaar door een eenvoudige DNS CNAME-wijziging.
>
> Met de back-upbewerking worden geen vooraf geaggregeerde logboek gegevens vastgelegd die worden gebruikt in rapporten die worden weer gegeven op de Blade Analytics in de Azure Portal.

> [!WARNING]
> Elke back-up verloopt na 30 dagen. Als u een back-up probeert te herstellen nadat de verval periode van 30 dagen is verstreken, mislukt de herstel bewerking met een `Cannot restore: backup expired` bericht.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure Resource Manager aanvragen verifiëren

> [!IMPORTANT]
> De REST API voor back-up en herstel maakt gebruik van Azure Resource Manager en heeft een ander verificatie mechanisme dan de REST Api's voor het beheren van uw API Management entiteiten. In de stappen in deze sectie wordt beschreven hoe u Azure Resource Manager-aanvragen verifieert. Zie voor meer informatie [verificatie van Azure Resource Manager aanvragen](/rest/api/index).

Alle taken die u op resources uitvoert met behulp van de Azure Resource Manager moeten worden geverifieerd met Azure Active Directory door de volgende stappen uit te voeren:

-   Een toepassing toevoegen aan de Azure Active Directory-Tenant.
-   Stel machtigingen in voor de toepassing die u hebt toegevoegd.
-   Het token ophalen voor het verifiëren van aanvragen voor het Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Ga met het abonnement dat uw API Management service-exemplaar bevat naar het tabblad **app-registraties** in **Azure Active Directory** (Azure Active Directory > beheer/app-registraties).

    > [!NOTE]
    > Als de standaard directory Azure Active Directory niet zichtbaar is voor uw account, neemt u contact op met de beheerder van het Azure-abonnement om de vereiste machtigingen voor uw account te verlenen.

3. Klik op **Nieuwe toepassing registreren**.

    Het venster **maken** wordt aan de rechter kant weer gegeven. Daar voert u de relevante gegevens van de AAD-app in.

4. Voer een naam in voor de toepassing.
5. Selecteer **systeem eigen** voor het toepassings type.
6. Voer een URL voor de tijdelijke aanduiding in `http://resources` , zoals voor de **omleidings-URI**, omdat het een vereist veld is, maar de waarde niet later wordt gebruikt. Klik op het selectie vakje om de toepassing op te slaan.
7. Klik op **Maken**.

### <a name="add-an-application"></a>Een toepassing toevoegen

1. Zodra de toepassing is gemaakt, klikt u op **API-machtigingen**.
2. Klik op **+ Een machtiging toevoegen**.
4. Klik op **micro soft-Api's selecteren**.
5. Kies **Azure Service Management**.
6. Druk op **selecteren**.

    ![Machtigingen toevoegen](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klik op **gedelegeerde machtigingen** naast de zojuist toegevoegde toepassing, schakel het selectie vakje in voor **toegang tot Azure Service Management (preview)**.
8. Druk op **selecteren**.
9. Klik op **machtigingen verlenen**.

### <a name="configuring-your-app"></a>Uw app configureren

Voordat u de Api's aanroept die de back-up genereren en deze herstellen, moet u een Token ophalen. In het volgende voor beeld wordt het NuGet-pakket [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) gebruikt om het token op te halen.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Vervang `{tenant id}` , `{application id}` en `{redirect uri}` Gebruik de volgende instructies:

1. Vervang door `{tenant id}` de Tenant-id van de Azure Active Directory toepassing die u hebt gemaakt. U kunt de id openen door te klikken op **app-registraties**-  ->  **eind punten**.

    ![Eindpunten][api-management-endpoint]

2. Vervang `{application id}` door de waarde die u krijgt door te navigeren naar de pagina **instellingen** .
3. Vervang de `{redirect uri}` door de waarde van het tabblad **omleidings-uri's** van uw Azure Active Directory-toepassing.

    Zodra de waarden zijn opgegeven, moet in het code voorbeeld een token worden geretourneerd dat lijkt op het volgende voor beeld:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Het token verloopt mogelijk na een bepaalde periode. Voer het code voorbeeld opnieuw uit om een nieuw token te genereren.

## <a name="calling-the-backup-and-restore-operations"></a>De back-up-en herstel bewerkingen aanroepen

De REST-Api's zijn [API Management-service-Backup](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) -en [API Management-service-herstellen](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Voordat u de bewerkingen ' back-up maken en terugzetten ' aanroept die in de volgende secties worden beschreven, stelt u de header van de autorisatie aanvraag in voor uw REST-aanroep.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Een back-up maken van een API Management-service

Als u een back-up wilt maken van een API Management service, geeft u de volgende HTTP-aanvraag:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Hierbij

-   `subscriptionId` -ID van het abonnement dat de API Management-service bevat waarvan u een back-up wilt maken
-   `resourceGroupName` -de naam van de resource groep van uw Azure API Management-service
-   `serviceName` -de naam van de API Management-service waarvan u een back-up wilt maken die is opgegeven op het moment dat deze wordt gemaakt
-   `api-version` -vervangen door `2019-12-01`

Geef in de hoofd tekst van de aanvraag de doel-Azure Storage-account naam, de toegangs sleutel, de naam van de BLOB-container en de naam van de back-up op:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Stel de waarde van de `Content-Type` aanvraag header in op `application/json` .

Het maken van een back-up is een langlopende bewerking die langer dan een minuut kan duren. Als de aanvraag is voltooid en het back-upproces is gestart, ontvangt u een `202 Accepted` antwoord status code met een `Location` header. Stel GET-aanvragen naar de URL in de `Location` header in om de status van de bewerking te bepalen. Terwijl de back-up wordt uitgevoerd, blijft de status code ' 202 geaccepteerd ' ontvangen. Met een antwoord code `200 OK` wordt aangegeven dat de back-upbewerking is voltooid.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Een API Management-service herstellen

Als u een API Management-service van een eerder gemaakte back-up wilt herstellen, moet u de volgende HTTP-aanvraag doen:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Hierbij

-   `subscriptionId` -ID van het abonnement dat de API Management-service bevat waarvan u een back-up wilt herstellen
-   `resourceGroupName` -de naam van de resource groep die de Azure API Management-service bevat waarvan u een back-up wilt herstellen
-   `serviceName` -de naam van de API Management-service die wordt hersteld op de aanmaak tijd die wordt gemaakt
-   `api-version` -vervangen door `api-version=2019-12-01`

In de hoofd tekst van de aanvraag geeft u de locatie van het back-upbestand op. Dat wil zeggen, de Azure Storage-account naam, de toegangs sleutel, de naam van de BLOB-container en de naam van de back-up toevoegen:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Stel de waarde van de `Content-Type` aanvraag header in op `application/json` .

Herstellen is een langlopende bewerking die Maxi maal 30 minuten kan duren. Als de aanvraag is voltooid en het herstel proces is gestart, ontvangt u een `202 Accepted` antwoord status code met een `Location` header. Stel GET-aanvragen naar de URL in de `Location` header in om de status van de bewerking te bepalen. Terwijl het herstel wordt uitgevoerd, blijft de status code ' 202 accepted ' ontvangen. Met een antwoord code `200 OK` wordt aangegeven dat de herstel bewerking is voltooid.

> [!IMPORTANT]
> **De SKU** van de service die wordt hersteld in **moet overeenkomen met** de SKU van de back-upservice die wordt hersteld.
>
> **Wijzigingen** die zijn aangebracht in de service configuratie (bijvoorbeeld api's, beleids regels, ontwikkelaars Portal vormgeving) terwijl de herstel bewerking wordt uitgevoerd, **kunnen worden overschreven**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Back-up-en herstel bewerkingen kunnen ook worden uitgevoerd met respectievelijk Power shell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) en [_Restore-AzApiManagement-_](/powershell/module/az.apimanagement/restore-azapimanagement) opdrachten.

## <a name="constraints-when-making-backup-or-restore-request"></a>Beperkingen bij het maken van een back-up-of herstel aanvraag

-   De in de hoofd tekst van de aanvraag opgegeven **container** **moet bestaan**.
-   Terwijl de back-up wordt uitgevoerd, kunt u **beheer wijzigingen in de service voor komen** , zoals SKU-upgrade of downgrade, wijzigt u in domein naam en nog veel meer.
-   Het terugzetten van een **back-up wordt alleen 30 dagen** na het moment van maken gegarandeerd.
-   **Wijzigingen** in de service configuratie (bijvoorbeeld api's, beleids regels en ontwikkelaars Portal) terwijl de back-upbewerking wordt uitgevoerd, wordt **mogelijk uitgesloten van de back-up en gaat verloren**.
-   Als [firewall][azure-storage-ip-firewall] is ingeschakeld voor het Azure Storage-account, moet de klant de set met [Azure API Management IP-adressen voor het beheer vlak][control-plane-ip-address] op hun opslag account **toestaan** voor het werken met back-up of herstellen van. Het Azure Storage-account kan zich in een Azure-regio bevinden, behalve de naam van de API Management-service. Als bijvoorbeeld de API Management-service zich in VS West bevindt, kan het Azure Storage-account zich in VS West 2 bevinden en moet de klant het IP-13.64.39.16 van het besturings vlak (API Management besturings element van het westen van de VS) in de firewall openen. Dit komt doordat de aanvragen voor Azure Storage niet worden omgezet naar een openbaar IP-adres van Compute (Azure API Management Control-vlak) in dezelfde Azure-regio. De opslag aanvraag voor meerdere regio's wordt omgezet naar het open bare IP-adres.
-   De functie voor het [delen van cross-Origin-resources (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) moet **niet** zijn ingeschakeld op de BLOB-service in het Azure Storage-account.
-   **De SKU** van de service die wordt hersteld in **moet overeenkomen met** de SKU van de back-upservice die wordt hersteld.

## <a name="what-is-not-backed-up"></a>Waarvan wordt geen back-up gemaakt?
-   **Gebruiks gegevens** die worden gebruikt voor het maken van analyse rapporten, worden **niet opgenomen** in de back-up. Gebruik [Azure API Management rest API][azure api management rest api] om periodiek analyse rapporten op te halen voor het bewaren van gegevens.
-   [TLS/SSL-certificaten voor aangepast domein](configure-custom-domain.md)
-   [Aangepast CA-certificaat](api-management-howto-ca-certificates.md), dat tussenliggende of basis certificaten bevat die zijn geüpload door de klant
-   Instellingen voor integratie met [virtuele netwerken](api-management-using-with-vnet.md) .
-   Configuratie van [beheerde identiteit](api-management-howto-use-managed-service-identity.md) .
-   [Azure monitor diagnose](api-management-howto-use-azure-monitor.md) Configuratie.
-   [Protocollen en coderings](api-management-howto-manage-protocols-ciphers.md) instellingen.
-   Inhoud van de [ontwikkelaars Portal](api-management-howto-developer-portal.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) .

De frequentie waarmee u Service back-ups uitvoert, is van invloed op uw Recovery Point Objective. Om het te minimaliseren, wordt u aangeraden regel matige back-ups te implementeren en back-ups op aanvraag uit te voeren nadat u wijzigingen hebt aangebracht in uw API Management-service.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende bronnen voor verschillende scenario's van het back-up-en herstel proces.

-   [Azure API Management-accounts repliceren](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Back-up maken en terugzetten in API Management automatiseren met Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: een back](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) -up maken van de configuratie 
     en deze herstellen _De benadering die wordt gedetailleerd door Stuart komt niet overeen met de officiële richt lijnen, maar is wel interessant._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
