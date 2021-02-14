---
title: 'ML Studio (klassiek): REST API fout codes-Azure'
description: Deze fout codes kunnen worden geretourneerd door een bewerking op een Azure Machine Learning-webservice.
keywords: ''
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: c5a457a673500c542dd6130d5cfadd73ab677f7b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518348"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klassiek) REST API fout codes

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


De volgende fout codes kunnen worden geretourneerd door een bewerking op een webservice Azure Machine Learning Studio (klassiek).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-status code 400)
 
Er is een ongeldig argument gegeven.
 
Deze klasse van fouten betekent dat een argument dat ergens wordt gegeven, ongeldig is. Dit kan een referentie of locatie zijn van Azure Storage naar iets dat is door gegeven aan de webservice. Raadpleeg het fout veld ' code ' in de sectie ' Details ' om vast te stellen welke specifieke argumenten ongeldig zijn.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| BadParameterValue | De opgegeven parameter waarde voldoet niet aan de parameter regel voor de para meter |
| BadSubscriptionId | De abonnements-id die wordt gebruikt om een score te geven, komt niet voor in de resource |
| BadVersionCall | Er is een ongeldige versie parameter door gegeven tijdens de API-aanroep: {0} . Controleer de Help-pagina van de API voor het door geven van de juiste versie en probeer het opnieuw. |
| BatchJobInputsNotSpecified | De volgende vereiste invoer (nen) is niet opgegeven bij de aanvraag: {0} . Controleer of alle invoer gegevens zijn opgegeven en probeer het opnieuw. |
| BatchJobInputsTooManySpecified | De aanvraag heeft meer invoer opgegeven dan is gedefinieerd in de service. Lijst met geaccepteerde invoer (n): {0} . Controleer of alle invoer gegevens juist zijn opgegeven en probeer het opnieuw. |
| BlobNameTooLong | Het opslag traject voor Azure Blob dat is ingevoerd voor de diagnostische uitvoer is te lang: {0} . Verklein het pad en probeer het opnieuw. |
| BlobNotFound | Kan geen toegang krijgen tot de beschik bare Azure-Blob {0} .  Azure-fout bericht: {1} . |
| ContainerIsEmpty | Er is geen Azure storage-container naam gegeven. Geef een geldige container naam op en probeer het opnieuw. |
| ContainerSegmentInvalid | Ongeldige container naam. Geef een geldige container naam op en probeer het opnieuw. |
| ContainerValidationFailed | Validatie van de BLOB-container is mislukt met de volgende fout: {0} . |
| DataTypeNotSupported | Er is een niet-ondersteund gegevens type gegeven. Geef geldige gegevens typen op en probeer het opnieuw. |
| DuplicateInputInBatchCall | De batch-aanvraag is ongeldig. U kunt niet zowel één als meerdere invoer tegelijk opgeven. Verwijder een van deze items uit de aanvraag en probeer het opnieuw. |
| ExpiryTimeInThePast | De gegeven verloop tijd bevindt zich in het verleden: {0} . Geef een toekomstige verloop tijd in UTC op en probeer het opnieuw. Stel de verloop tijd in op NULL om nooit te verlopen. |
| IncompleteSettings | De diagnostische instellingen zijn onvolledig. |
| InputBlobRelativeLocationInvalid | Er is geen blob-naam voor Azure Storage gegeven. Geef een geldige naam voor de BLOB op en probeer het opnieuw. |
| InvalidBlob | Ongeldige BLOB-specificatie voor blob: {0} . Controleer of connection string/relatieve pad of SAS-token specificatie juist is en probeer het opnieuw. |
| InvalidBlobConnectionString | De connection string die is opgegeven voor een van de blobs voor invoer/uitvoer, is ongeldig: {0} . Corrigeer dit en probeer het opnieuw. |
| InvalidBlobExtension | De BLOB-verwijzing: {0} heeft een ongeldige of ontbrekende bestands extensie. Ondersteunde bestands extensies voor dit uitvoer type zijn: {1} . |
| InvalidInputNames | Ongeldige service-invoer naam (en) opgegeven in de aanvraag: {0} . Wijs de invoer gegevens toe aan de juiste service-invoer en probeer het opnieuw. |
| InvalidOutputOverrideName | Ongeldige uitvoer onderdrukkings naam: {0} . De service heeft geen uitvoer knooppunt met deze naam. Geef een juiste naam op voor het uitvoer knooppunt dat moet worden overschreven (hoofdletter gevoeligheid is van toepassing). |
| InvalidQueryParameter | Ongeldige query parameter {0} . {1} |
| MissingInputBlobInformation | Ontbrekende informatie over Azure Storage-blob. Geef een geldig connection string en een relatief pad of URI op en probeer het opnieuw. |
| MissingJobId | Er is geen taak-id gegeven. Er wordt een taak-id geretourneerd wanneer een taak voor de eerste keer is verzonden. Controleer of de taak-id juist is en probeer het opnieuw. |
| MissingKeys | Er is geen sleutel of een primaire of secundaire sleutel gegeven. |
| MissingModelPackage | Er is geen model pakket-id of model pakket opgenomen. Geef een geldige model pakket-id of model pakket op en probeer het opnieuw. |
| MissingOutputOverrideSpecification | In de aanvraag ontbreekt de BLOB-specificatie voor het overschrijven van de uitvoer {0} . Geef een geldige BLOB-locatie op met de aanvraag of verwijder de uitvoer specificatie als er geen locatie is die u wilt overschrijven. |
| MissingRequestInput | De webservice verwacht een invoer, maar er is geen invoer opgegeven. Zorg ervoor dat geldige invoer waarden worden opgegeven op basis van de gepubliceerde invoer poorten in het model en probeer het opnieuw. |
| MissingRequiredGlobalParameters | Niet alle vereiste web service-para meter (s) opgegeven. Controleer of de para meter (s) die worden verwacht voor de module (s) juist zijn en probeer het opnieuw. |
| MissingRequiredOutputOverrides | Bij het aanroepen van een versleuteld service-eind punt is het verplicht om de uitvoer onderdrukkingen voor alle uitvoer van de service door te geven. Ontbrekende onderdrukkingen op dit moment voor deze uitvoer: {0} |
| MissingWebServiceGroupId | Er is geen groeps-id voor de webservice gegeven. Geef een geldige id voor de webservice-groep op en probeer het opnieuw. |
| MissingWebServiceId | Er is geen webservice-id gegeven. Geef een geldige webservice-id op en probeer het opnieuw. |
| MissingWebServicePackage | Er is geen pakket voor de webservice opgenomen. Geef een geldig webservice-pakket op en probeer het opnieuw. |
| MissingWorkspaceId | Er is geen werk ruimte-id gegeven. Geef een geldige werk ruimte-id op en probeer het opnieuw. |
| ModelConfigurationInvalid | Ongeldige model configuratie in het model pakket. Zorg ervoor dat de model configuratie een definitie van een uitvoer eindpunt, een Std-fout eindpunt en een standaard eind punt bevat en probeer het opnieuw. |
| ModelPackageIdInvalid | Ongeldige model pakket-id. Controleer of de model pakket-id juist is en probeer het opnieuw. |
| RequestBodyInvalid | Er is geen aanvraag tekst gegeven of er is een fout opgetreden bij het deserialiseren van de aanvraag tekst. |
| RequestIsEmpty | Geen aanvraag ontvangen. Geef een geldige aanvraag op en probeer het opnieuw. |
| UnexpectedParameter | Er zijn onverwachte para meters opgegeven. Controleer of alle parameter namen juist zijn gespeld, alleen de verwachte para meters worden door gegeven en probeer het opnieuw. |
| UnknownError | Onbekende fout. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Kan de vereisten voor gelijktijdige aanvragen niet wijzigen voor de {0} webservice. |
| WebServiceIdInvalid | Ongeldige webservice-id gegeven. De webservice-id moet een geldige GUID zijn. |
| WebServiceTooManyConcurrentRequestRequirement | Kan niet gelijktijdige aanvraag vereisten instellen op meer dan {0} . |
| WebServiceTypeInvalid | Er is een ongeldig type webservice gegeven. Controleer of het geldige type Web service juist is en probeer het opnieuw. Geldige typen web-service: {0} . |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-status code 400)
 
Er is een ongeldig gebruikers argument gegeven.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| InputMismatchError | De invoer gegevens komen niet overeen met het invoer poort schema. |
| InputParseError | Het parseren van invoer vector is mislukt.  Controleer of de invoer vector het juiste aantal kolommen en gegevens typen heeft.  Aanvullende details: {0} . |
| MissingRequiredGlobalParameters | De para meter (s) die worden verwacht door de webservice ontbreken. Controleer of alle vereiste para meters die worden verwacht door de webservice juist zijn en probeer het opnieuw. |
| UnexpectedParameter | Controleer of alleen de vereiste para meters die door de webservice worden verwacht, worden door gegeven en probeer het opnieuw. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-status code 400)
 
De aanvraag is ongeldig in de huidige context.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| CannotStartJob | De taak kan niet worden gestart omdat deze de {0} status heeft. |
| IncompatibleModel | Het model is niet compatibel met de aanvraag versie. De aanvraag versie ondersteunt alleen uitvoer modellen met één DataTable. |
| MultipleInputsNotAllowed | Het model staat geen meerdere invoer toe. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-status code 400)
 
Er is een interne bibliotheek fout opgetreden tijdens de uitvoering van de module.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-status code 400)
 
Er is een fout opgetreden bij het uitvoeren van de module.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-status code 400)
 
Ongeldig web service-pakket. Controleer of het meegeleverde pakket voor de webservice juist is en probeer het opnieuw.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| FormatError | Het webservice-pakket is onjuist gevormd. Nadere {0} |
| RuntimesError | De grafiek van het web service-pakket is ongeldig. Nadere {0} |
| ValidationError | De grafiek van het web service-pakket is ongeldig. Nadere {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Niet geautoriseerd (HTTP-status code 401)
 
De aanvraag is niet gemachtigd om toegang te krijgen tot de resource.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| AdminRequestUnauthorized | Niet geautoriseerd |
| ManagementRequestUnauthorized | Niet geautoriseerd |
| ScoreRequestUnauthorized | Er zijn ongeldige referenties gegeven. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-status code 404)
 
De resource is niet gevonden.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| ModelPackageNotFound | Model pakket niet gevonden. Controleer of de model pakket-id juist is en probeer het opnieuw. |
| WebServiceIdNotFoundInWorkspace | De webservice onder deze werk ruimte is niet gevonden. De webServiceId en de workspaceId komen niet overeen. Controleer of de meegeleverde webservice deel uitmaakt van de werk ruimte en probeer het opnieuw. |
| WebServiceNotFound | De webservice is niet gevonden. Controleer of de webservice-id juist is en probeer het opnieuw. |
| WorkspaceNotFound | De werk ruimte is niet gevonden. Controleer of de werk ruimte-id juist is en probeer het opnieuw. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-status code 408)
 
De bewerking kan niet worden voltooid binnen de toegestane tijd.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| RequestCanceled | De aanvraag is geannuleerd door de client. |
| ScoreRequestTimeout | Time-out bij uitvoerings aanvraag. |
 
## <a name="conflict-http-status-code-409"></a>Conflict (HTTP-status code 409)
 
De resource bestaat al.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Ongeldige naam van uitvoer parameter. Gebruik de module meta gegevens editor om de namen van kolommen te wijzigen en probeer het opnieuw. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-status code 413)
 
Het model heeft het toegewezen geheugen quotum overschreden.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| OutOfMemoryLimit | Het model heeft meer geheugen verbruikt dan het meest geschikt is voor IT. Het Maxi maal toegestane geheugen voor het model is {0} MB. Raadpleeg uw model voor problemen. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-status code 500)
 
Er is een interne fout opgetreden in de uitvoering.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Het container proces is vastgelopen met een systeem fout |
| ContainerProcessTerminatedWithUnknownError | Het container proces is vastgelopen met een onbekende fout |
| ContainerValidationFailed | Validatie van de BLOB-container is mislukt met de volgende fout: {0} . |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Er zijn geen argumenten gegeven. Controleer of de geldige argumenten zijn door gegeven en probeer het opnieuw. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Poort-id = {0} heeft een niet-ondersteund gegevens type: {1} . |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Kan Swagger niet genereren, Details: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Onbekende taak status code {0} . |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, Details: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-status code 500)
 
Er is een interne fout opgetreden in de uitvoering. Systeem weinig geheugen. Probeer het opnieuw.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-status code 500)
 
Ongeldig model pakket. Controleer of het gegeven model pakket juist is en probeer het opnieuw.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-status code 500)
 
Ongeldig web service-pakket. Controleer of het gegeven webpakket juist is en probeer het opnieuw.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| ModuleError | De grafiek van het web service-pakket is ongeldig. Nadere {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP-status code 503)
 
De aanvraag kan niet worden uitgevoerd omdat de containers worden geïnitialiseerd.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP-status code 503)
 
De service is tijdelijk niet beschikbaar.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| NoMoreResources | Er zijn geen resources beschikbaar voor de aanvraag. |
| RequestThrottled | De aanvraag is beperkt voor het {0} eind punt. De maximale gelijktijdigheid voor het eind punt is {1} . |
| TooManyConcurrentRequests | Er zijn te veel gelijktijdige aanvragen verzonden. |
| TooManyHostsBeingInitialized | Er worden te veel hosts tegelijk geïnitialiseerd. Overweeg het beperken/opnieuw proberen. |
| TooManyHostsBeingInitializedPerModel | Er worden te veel hosts tegelijk geïnitialiseerd. Overweeg het beperken/opnieuw proberen. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-status code 504)
 
De bewerking kan niet worden voltooid binnen de toegestane tijd.
 
| Foutcode | Gebruikers bericht |
| ---------- |--------------|
| BackendInitializationTimeout | De initialisatie van de webservice kan niet worden voltooid binnen de toegestane tijd. |
| BackendScoreTimeout | De uitvoering van de webservice-aanvraag kan niet worden voltooid binnen de toegestane tijd. |
