---
title: De SendGrid-e-mail service gebruiken (Node.js) | Microsoft Docs
description: Meer informatie over het verzenden van e-mail met de SendGrid-e-mail service op Azure. Code voorbeelden die zijn geschreven met behulp van de Node.js-API.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015418"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>E-mail verzenden met SendGrid van Node.js

In deze hand leiding wordt gedemonstreerd hoe u algemene programmeer taken uitvoert met de SendGrid-e-mail service op Azure. De voor beelden zijn geschreven met behulp van de Node.js-API. De besproken scenario's zijn onder andere het **maken van e-mail**, het **verzenden van e-mail**, het toevoegen van **bijlagen**, het **gebruiken van filters** en het **bijwerken van eigenschappen**. Zie de sectie [volgende stappen](#next-steps) voor meer informatie over het SendGrid en verzenden van e-mail.

## <a name="what-is-the-sendgrid-email-service"></a>Wat is de SendGrid-e-mail service?

SendGrid is een [cloud-gebaseerde e-mail service] die betrouw bare [transactionele e-mail levering], schaal baarheid en real-time analyse biedt, samen met flexibele api's die eenvoudig aangepaste integratie maken. Veelvoorkomende scenario's voor SendGrid-gebruik zijn:

* Automatisch ontvangst bevestigingen verzenden naar klanten
* Distributie lijsten beheren voor het maandelijks verzenden van klanten per maand e-flyers en speciale aanbiedingen
* Realtime metrische gegevens verzamelen voor zaken als geblokkeerde e-mail en reactie tijd van klant
* Rapporten genereren om trends te identificeren
* Vragen van klanten door sturen
* E-mail meldingen van uw toepassing

Zie [https://sendgrid.com](https://sendgrid.com) voor meer informatie.

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Verwijzen naar de SendGrid-Node.js module

De SendGrid-module voor Node.js kan worden geïnstalleerd via de node Package Manager (NPM) met behulp van de volgende opdracht:

```bash
npm install sendgrid
```

Na de installatie kunt u de module in uw toepassing vereisen met behulp van de volgende code:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

De SendGrid-module exporteert  de SendGrid **-en e-mail** functies.
**SendGrid** is verantwoordelijk voor het verzenden van E-mail via web API, terwijl **e-mail** berichten een e-mail bericht inkapselen.

## <a name="how-to-create-an-email"></a>Procedure: een E-mail maken

Als u een e-mail bericht wilt maken met behulp van de SendGrid-module, moet u eerst een e-mail bericht maken met de functie E-mail en vervolgens verzenden met de functie SendGrid. Hier volgt een voor beeld van het maken van een nieuw bericht met behulp van de e-mail functie:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

U kunt ook een HTML-bericht opgeven voor clients die het ondersteunen door de HTML-eigenschap in te stellen. Bijvoorbeeld:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Het instellen van de tekst-en HTML-eigenschappen biedt een correcte terugval van tekst inhoud voor clients die geen HTML-berichten kunnen ondersteunen.

Zie [sendgrid-nodejs][sendgrid-nodejs]voor meer informatie over alle eigenschappen die door de e-mail functie worden ondersteund.

## <a name="how-to-send-an-email"></a>Procedure: een E-mail verzenden

Nadat u een e-mail bericht met de e-mail functie hebt gemaakt, kunt u het verzenden met de Web-API van SendGrid. 

### <a name="web-api"></a>Web-API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Hoewel de bovenstaande voor beelden worden weer gegeven in een e-mail object en een call back-functie, kunt u de functie Send ook rechtstreeks aanroepen door rechtstreeks e-mail eigenschappen op te geven. Bijvoorbeeld:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Procedure: een bijlage toevoegen
Bijlagen kunnen worden toegevoegd aan een bericht door de bestands naam en het pad (en) op te geven in de eigenschap **files** . In het volgende voor beeld ziet u hoe u een bijlage verzendt:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> Wanneer u de eigenschap **files** gebruikt, moet het bestand toegankelijk zijn via [FS. read file](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Als het bestand dat u wilt koppelen, wordt gehost in Azure Storage, zoals in een BLOB-container, moet u het bestand eerst kopiëren naar lokale opslag of naar een Azure-station voordat het kan worden verzonden als een bijlage met de eigenschap **files** .
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Procedure: filters gebruiken om voet teksten en tracering in te scha kelen

SendGrid biedt extra e-mail functionaliteit dankzij het gebruik van filters. Dit zijn instellingen die kunnen worden toegevoegd aan een e-mail bericht om specifieke functionaliteit in te scha kelen, zoals het inschakelen van klikken op bijhouden, Google Analytics, bijhouden van abonnementen, enzovoort. Zie [filter instellingen][Filter Settings]voor een volledige lijst met filters.

Filters kunnen worden toegepast op een bericht met behulp van de eigenschap **filters** .
Elk filter wordt opgegeven met een hash die filter-specifieke instellingen bevat.
In de volgende voor beelden ziet u de voet tekst en klikt u op traceer filters:

### <a name="footer"></a>Voettekst

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Klik op bijhouden

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Instructies: e-mail eigenschappen bijwerken

Sommige e-mail eigenschappen kunnen worden overschreven met **SetProperty** of worden toegevoegd met behulp van **addProperty**. U kunt bijvoorbeeld extra ontvangers toevoegen met behulp van

```javascript
email.addTo('jeff@contoso.com');
```

of een filter instellen met behulp van

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Zie [sendgrid-nodejs][sendgrid-nodejs]voor meer informatie.

## <a name="how-to-use-additional-sendgrid-services"></a>Procedure: extra SendGrid Services gebruiken

SendGrid biedt op web gebaseerde Api's die u kunt gebruiken om gebruik te maken van extra SendGrid-functionaliteit vanuit uw Azure-toepassing. Zie de [SENDGRID API-documentatie][SendGrid API documentation]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van de SendGrid-e-mail service hebt geleerd, volgt u deze koppelingen voor meer informatie.

* SendGrid Node.js module opslag plaats: [SendGrid-nodejs][sendgrid-nodejs]
* SendGrid API-documentatie: <https://sendgrid.com/docs>
* SendGrid speciale aanbieding voor Azure-klanten: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[e-mail service op basis van de Cloud]: https://sendgrid.com/email-solutions
[e-mail levering via een transactie]: https://sendgrid.com/transactional-email
