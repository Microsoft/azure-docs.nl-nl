---
title: Azure Notification Hubs gebruiken met Java
description: Meer informatie over het gebruik van Azure Notification Hubs van een Java-back-end.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-java
ms.openlocfilehash: 09553f587916e8204541b36f259a450d72c1b270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87322938"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Notification Hubs van Java gebruiken

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

In dit onderwerp worden de belangrijkste functies van de nieuwe volledig ondersteunde officiële Azure notification hub Java SDK beschreven.
Dit project is een open-source project en u kunt de volledige SDK-code weer geven op de [Java-SDK].

Over het algemeen hebt u toegang tot alle Notification Hubs-functies van een Java/PHP/python/ruby-back-end met behulp van de REST-interface van de notification hub, zoals beschreven in het MSDN-onderwerp [Notification hubs rest-api's](/previous-versions/azure/reference/dn223264(v=azure.100)). Deze Java-SDK biedt een smalle wrapper voor deze REST-interfaces in Java.

De SDK biedt momenteel ondersteuning voor:

* RUW op Notification Hubs
* RUW op registraties
* Installatie beheer
* Registraties importeren/exporteren
* Normale verzen dingen
* Geplande verzen dingen
* Asynchrone bewerkingen via Java NIO
* Ondersteunde platforms: APNS (iOS), FCM (Android), WNS (Windows Store-apps), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android zonder Google Services)

## <a name="sdk-usage"></a>SDK-gebruik

### <a name="compile-and-build"></a>Compileren en bouwen

[Maven] gebruiken

Bouwen:

```cmd
mvn package
```

## <a name="code"></a>Code

### <a name="notification-hub-cruds"></a>Notification hub-ruw

**Een NamespaceManager maken:**

```java
NamespaceManager namespaceManager = new NamespaceManager("connection string")
```

**Notification hub maken:**

```java
NotificationHubDescription hub = new NotificationHubDescription("hubname");
hub.setWindowsCredential(new WindowsCredential("sid","key"));
hub = namespaceManager.createNotificationHub(hub);
```

 OF

```java
hub = new NotificationHub("connection string", "hubname");
```

**Notification hub ophalen:**

```java
hub = namespaceManager.getNotificationHub("hubname");
```

**Notification hub bijwerken:**

```java
hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
hub = namespaceManager.updateNotificationHub(hub);
```

**Notification hub verwijderen:**

```java
namespaceManager.deleteNotificationHub("hubname");
```

### <a name="registration-cruds"></a>RUWE afschrijvingen

**Een notification hub-client maken:**

```java
hub = new NotificationHub("connection string", "hubname");
```

**Windows-registratie maken:**

```java
WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

**IOS-registratie maken:**

```java
AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

Op dezelfde manier kunt u registraties maken voor Android (FCM), Windows Phone (MPNS) en Kindle Fire (ADM).

**Sjabloon registraties maken:**

```java
WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
reg.getHeaders().put("X-WNS-Type", "wns/toast");
hub.createRegistration(reg);
```

**Registraties maken met registratie-ID maken en upsert patroon:**

Dubbele waarden worden verwijderd als gevolg van verloren antwoorden bij het opslaan van registratie-Id's op het apparaat:

```java
String id = hub.createRegistrationId();
WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
hub.upsertRegistration(reg);
```

**Registraties bijwerken:**

```java
hub.updateRegistration(reg);
```

**Registraties verwijderen:**

```java
hub.deleteRegistration(regid);
```

**Query registraties:**

* **Eén registratie ophalen:**

```java
hub.getRegistration(regid);
```

* **Alle registraties ophalen in de hub:**

```java
hub.getRegistrations();
```

* **Registraties ophalen met de tag:**

```java
hub.getRegistrationsByTag("myTag");
```

* **Registraties per kanaal ophalen:**

```java
hub.getRegistrationsByChannel("devicetoken");
```

Alle verzamelings query's ondersteunen $top en vervolg tokens.

### <a name="installation-api-usage"></a>Gebruik van installatie-API

Installatie-API is een alternatief mechanisme voor registratie beheer. In plaats van meerdere registraties te onderhouden, die niet gelastig zijn en eenvoudig onjuist of onefficiënt kunnen worden uitgevoerd, is het nu mogelijk om één installatie object te gebruiken.

De installatie bevat alles wat u nodig hebt: push Channel (apparaat-token), tags, sjablonen, secundaire tegels (voor WNS en APNS). U hoeft de service niet aan te roepen om ID meer op te halen: u hoeft alleen een GUID of andere id te genereren, deze op het apparaat te laten staan en te verzenden naar uw back-end met een push kanaal (apparaat-token).

Op de back-end moet u slechts één aanroep doen naar `CreateOrUpdateInstallation` ; het is een volledig idempotent, zodat u het opnieuw kunt proberen als dat nodig is.

Bijvoorbeeld voor Amazon Kindle Fire:

```java
Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
hub.createOrUpdateInstallation(installation);
```

Als u deze wilt bijwerken:

```java
installation.addTag("foo");
installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
hub.createOrUpdateInstallation(installation);
```

Voor geavanceerde scenario's gebruikt u de functie gedeeltelijke update, waarmee u alleen bepaalde eigenschappen van het installatie object kunt wijzigen. Gedeeltelijke update is een subset van JSON-patch bewerkingen die u kunt uitvoeren tegen een installatie object.

```java
PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
```

Installatie verwijderen:

```java
hub.deleteInstallation(installation.getInstallationId());
```

`CreateOrUpdate`, `Patch` , en `Delete` zijn uiteindelijk consistent met `Get` . De aangevraagde bewerking gaat gewoon naar de systeem wachtrij tijdens de aanroep en wordt op de achtergrond uitgevoerd. Get is niet ontworpen voor het hoofd runtime-scenario, maar alleen voor fout opsporing en probleem oplossing, wordt het nauw keurig beperkt door de service.

Verzend stroom voor installaties is hetzelfde als voor registraties. Als u een melding wilt ontvangen voor de specifieke installatie, gebruikt u tag "InstallationId: {desired-id}". Voor dit geval is de code:

```java
Notification n = Notification.createWindowsNotification("WNS body");
hub.sendNotification(n, "InstallationId:{installation-id}");
```

Voor een van de volgende sjablonen:

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("value3", "some value");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
```

### <a name="schedule-notifications-available-for-standard-tier"></a>Meldingen plannen (beschikbaar voor de laag standaard)

Hetzelfde als normaal verzenden, maar met één extra para meter-scheduledTime, wat aangeeft wanneer de melding moet worden bezorgd. De service aanvaardt enige tijd tussen nu + 5 minuten en nu + 7 dagen.

**Een systeem eigen melding van Windows plannen:**

```java
Calendar c = Calendar.getInstance();
c.add(Calendar.DATE, 1);
Notification n = Notification.createWindowsNotification("WNS body");
hub.scheduleNotification(n, c.getTime());
```

### <a name="importexport-available-for-standard-tier"></a>Importeren/exporteren (beschikbaar voor de laag standaard)

U moet mogelijk een bulk bewerking uitvoeren op basis van registraties. Normaal gesp roken is het voor integratie met een ander systeem of een massale oplossing om de tags bij te werken. Het is niet raadzaam om de stroom get/update te gebruiken als er duizenden registraties zijn betrokken. De import/export-functie van het systeem is ontworpen om het scenario te behandelen. U krijgt toegang tot een BLOB-container onder uw opslag account als bron van binnenkomende gegevens en locatie voor uitvoer.

**Een export taak verzenden:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Een import taak verzenden:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
job.setImportFileUri("input file uri with SAS signature");
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Wachten tot een taak is voltooid:**

```java
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}
```

**Alle taken ophalen:**

```java
List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
```

**URI met SAS-hand tekening:**

 Deze URL is de URL van een blob-bestand of BLOB-container plus een set para meters zoals machtigingen en verloop tijd plus hand tekening van al deze dingen die zijn gemaakt met behulp van de SAS-sleutel van het account. Azure Storage Java SDK biedt uitgebreide mogelijkheden, waaronder het maken van deze Uri's. Als eenvoudig alternatief bekijkt u de `ImportExportE2E` test klasse (vanaf de locatie van de GitHub) met basis-en compacte implementatie van het Ondertekeningsalgoritme.

### <a name="send-notifications"></a>Meldingen verzenden

Het meldings object is simpelweg een hoofd tekst met kopteksten, sommige hulp methoden helpen bij het bouwen van de systeem eigen en sjabloon meldingen objecten.

* **Windows Store en Windows Phone 8,1 (niet-Silverlight)**

```java
String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
Notification n = Notification.createWindowsNotification(toast);
hub.sendNotification(n);
```

* **iOS**

```java
String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
Notification n = Notification.createAppleNotification(alert);
hub.sendNotification(n);
```

* **Android**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createFcmNotification(message);
hub.sendNotification(n);
```

* **Windows Phone 8,0 en 8,1 Silverlight**

```java
String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
            "<wp:Notification xmlns:wp=\"WPNotification\">" +
                "<wp:Toast>" +
                    "<wp:Text1>Hello from Java!</wp:Text1>" +
                "</wp:Toast> " +
            "</wp:Notification>";
Notification n = Notification.createMpnsNotification(toast);
hub.sendNotification(n);
```

* **Kindle Fire**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createAdmNotification(message);
hub.sendNotification(n);
```

* **Naar Tags verzenden**
  
```java
Set<String> tags = new HashSet<String>();
tags.add("boo");
tags.add("foo");
hub.sendNotification(n, tags);
```

* **De expressie Send to tag**

```java
hub.sendNotification(n, "foo && ! bar");
```

* **Sjabloon melding verzenden**

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("prop1", "v1");
prop.put("prop2", "v2");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n);
```

Als u uw Java-code uitvoert, moet er nu een melding op uw doel apparaat worden weer gegeven.

## <a name="next-steps"></a><a name="next-steps"></a>Volgende stappen

In dit onderwerp wordt uitgelegd hoe u een eenvoudige Java-REST-client maakt voor Notification Hubs. Hier kunt u het volgende doen:

* Down load de volledige [Java-SDK], die de volledige SDK-code bevat.
* Spelen met de voor beelden:
  * [Aan de slag met Notification Hubs]
  * [Laatste nieuws verzenden]
  * [Gelokaliseerd afgebroken Nieuws verzenden]
  * [Meldingen verzenden naar geverifieerde gebruikers]
  * [Kruis platform meldingen verzenden naar geverifieerde gebruikers]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Aan de slag met Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Laatste nieuws verzenden]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Gelokaliseerd afgebroken Nieuws verzenden]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Meldingen verzenden naar geverifieerde gebruikers]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Kruis platform meldingen verzenden naar geverifieerde gebruikers]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
