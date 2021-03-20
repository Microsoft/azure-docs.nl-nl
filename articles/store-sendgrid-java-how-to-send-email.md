---
title: De SendGrid-e-mail service (Java) gebruiken | Microsoft Docs
description: Meer informatie over het verzenden van e-mail met de SendGrid-e-mail service op Azure. Code voorbeelden geschreven in Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.custom: devx-track-java
ms.openlocfilehash: 9ff006b74b6202b02a2767aee4d853b1206ce60d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015448"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>E-mail verzenden met SendGrid vanuit Java
In deze hand leiding wordt gedemonstreerd hoe u algemene programmeer taken uitvoert met de SendGrid-e-mail service op Azure. De voor beelden zijn geschreven in Java. De besproken scenario's zijn onder andere het **maken van e-mail**, het **verzenden van e-mail**, het toevoegen van **bijlagen**, het **gebruiken van filters** en het **bijwerken van eigenschappen**. Zie de sectie [volgende stappen](#next-steps) voor meer informatie over het SendGrid en verzenden van e-mail.

## <a name="what-is-the-sendgrid-email-service"></a>Wat is de SendGrid-e-mail service?
SendGrid is een [cloud-gebaseerde e-mail service] die betrouw bare [transactionele e-mail levering], schaal baarheid en real-time analyse biedt, samen met flexibele api's die eenvoudig aangepaste integratie maken. Veelvoorkomende scenario's voor SendGrid-gebruik zijn:

* Automatisch ontvangst bevestigingen verzenden naar klanten
* Distributie lijsten beheren voor het maandelijks verzenden van klanten per maand e-flyers en speciale aanbiedingen
* Realtime metrische gegevens verzamelen voor zaken als geblokkeerde e-mail en reactie tijd van klant
* Rapporten genereren om trends te identificeren
* Vragen van klanten door sturen
* E-mail meldingen van uw toepassing

Voor meer informatie raadpleegt u <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Procedure: de bibliotheken javax. mail gebruiken
Zorg ervoor dat u de Java. mail-bibliotheken kunt ophalen, bijvoorbeeld uit <https://www.oracle.com/technetwork/java/javamail> en importeren in uw code. Op hoog niveau is het proces voor het gebruik van de javax. mail-bibliotheek voor het verzenden van e-mail via SMTP de volgende handelingen uit te voeren:

1. Geef de SMTP-waarden op, met inbegrip van de SMTP-server, die voor SendGrid is smtp.sendgrid.net.

    ```java
    import java.util.Properties;
    import javax.mail.*;
    import javax.mail.internet.*;

    public class MyEmailer {
        private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
        private static final String SMTP_AUTH_USER = "your_sendgrid_username";
        private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

        public static void main(String[] args) throws Exception{
            new MyEmailer().SendMail();
        }

        public void SendMail() throws Exception
        {
            Properties properties = new Properties();
                properties.put("mail.transport.protocol", "smtp");
                properties.put("mail.smtp.host", SMTP_HOST_NAME);
                properties.put("mail.smtp.port", 587);
                properties.put("mail.smtp.auth", "true");
                // …
    ```

1. Breid de klasse *javax. mail. Authenticator* uit en retour neer uw SendGrid-gebruikers naam en-wacht woord in uw implementatie van de *getPasswordAuthentication* -methode.  

    ```java
    private class SMTPAuthenticator extends javax.mail.Authenticator {
    public PasswordAuthentication getPasswordAuthentication() {
        String username = SMTP_AUTH_USER;
        String password = SMTP_AUTH_PWD;
        return new PasswordAuthentication(username, password);
    }
    ```
2. Een geverifieerde e-mail sessie maken via een *Java. mail. Session-* object.  

    ```java
    Authenticator auth = new SMTPAuthenticator();
    Session mailSession = Session.getDefaultInstance(properties, auth);
    ```
3. Uw bericht maken en toewijzen **aan** waarden, **van**, **onderwerp** en inhoud. Dit wordt weer gegeven in de sectie [procedure: een E-mail maken](#how-to-create-an-email) .
4. Het bericht verzenden via een *javax. mail. Trans Port-* object. Dit wordt weer gegeven in de sectie [How to: Send a E-mail] [#how-to-send-a-email].

## <a name="how-to-create-an-email"></a>Procedure: een e-mail maken
Hieronder ziet u hoe u waarden voor een e-mail kunt opgeven.

```java
MimeMessage message = new MimeMessage(mailSession);
Multipart multipart = new MimeMultipart("alternative");
BodyPart part1 = new MimeBodyPart();
part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
BodyPart part2 = new MimeBodyPart();
part2.setContent(
    "<p>Hello,</p>
    <p>Your Contoso order has <b>shipped</b>.</p>
    <p>Thank you,<br>John</br></p>",
    "text/html");
multipart.addBodyPart(part1);
multipart.addBodyPart(part2);
message.setFrom(new InternetAddress("john@contoso.com"));
message.addRecipient(Message.RecipientType.TO,
    new InternetAddress("someone@example.com"));
message.setSubject("Your recent order");
message.setContent(multipart);
```

## <a name="how-to-send-an-email"></a>Procedure: een e-mail verzenden
Hieronder ziet u hoe u een e-mail verzendt.

```java
Transport transport = mailSession.getTransport();
// Connect the transport object.
transport.connect();
// Send the message.
transport.sendMessage(message, message.getAllRecipients());
// Close the connection.
transport.close();
```

## <a name="how-to-add-an-attachment"></a>Procedure: een bijlage toevoegen
De volgende code laat zien hoe u een bijlage kunt toevoegen.

```java
// Local file name and path.
String attachmentName = "myfile.zip";
String attachmentPath = "c:\\myfiles\\";
MimeBodyPart attachmentPart = new MimeBodyPart();
// Specify the local file to attach.
DataSource source = new FileDataSource(attachmentPath + attachmentName);
attachmentPart.setDataHandler(new DataHandler(source));
// This example uses the local file name as the attachment name.
// They could be different if you prefer.
attachmentPart.setFileName(attachmentName);
multipart.addBodyPart(attachmentPart);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Procedure: filters gebruiken om voet teksten, tracering en analyses in te scha kelen
SendGrid biedt extra e-mail functionaliteit dankzij het gebruik van *filters*. Dit zijn instellingen die kunnen worden toegevoegd aan een e-mail bericht om specifieke functionaliteit in te scha kelen, zoals het inschakelen van klikken op bijhouden, Google Analytics, bijhouden van abonnementen, enzovoort. Zie [filter instellingen][Filter Settings]voor een volledige lijst met filters.

* Hieronder ziet u hoe u een voet tekst filter kunt invoegen waarmee HTML-tekst wordt weer gegeven aan de onderkant van het e-mail bericht dat wordt verzonden.

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"footer\":
        {\"settings\":
        {\"enable\":1,\"text/html\":
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
    ```
* Een ander voor beeld van een filter is klikken op bijhouden. Stel dat uw e-mail tekst een Hyper link bevat, zoals de volgende, en u de klik frequentie wilt volgen:

    ```java
    messagePart.setContent(
        "Hello,
        <p>This is the body of the message. Visit
        <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
        Thank you.",
        "text/html");
    ```
* Als u de klik tracering wilt inschakelen, gebruikt u de volgende code:

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"clicktrack\":
        {\"settings\":
        {\"enable\":1}}}}");
    ```

## <a name="how-to-update-email-properties"></a>Instructies: e-mail eigenschappen bijwerken
Sommige e-mail eigenschappen kunnen worden overschreven met behulp van **eigenschap instellen** of toegevoegd met behulp van de **eigenschap add**.

Als u bijvoorbeeld **ReplyTo** -adressen wilt opgeven, gebruikt u het volgende:

```java
InternetAddress addresses[] =
    { new InternetAddress("john@contoso.com"),
        new InternetAddress("wendy@contoso.com") };

message.setReplyTo(addresses);
```

Gebruik het volgende om een **CC** -ontvanger toe te voegen:

```java
message.addRecipient(Message.RecipientType.CC, new
InternetAddress("john@contoso.com"));
```

## <a name="how-to-use-additional-sendgrid-services"></a>Procedure: extra SendGrid Services gebruiken
SendGrid biedt op web gebaseerde Api's die u kunt gebruiken om gebruik te maken van extra SendGrid-functionaliteit vanuit uw Azure-toepassing. Zie de [SENDGRID API-documentatie][SendGrid API documentation]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van de SendGrid-e-mail service hebt geleerd, volgt u deze koppelingen voor meer informatie.

* Voor beeld van het gebruik van SendGrid in een Azure-implementatie: [e-mail verzenden met SendGrid vanuit java in een Azure-implementatie](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-documentatie: <https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid speciale aanbieding voor Azure-klanten: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[e-mail service op basis van de Cloud]: https://sendgrid.com/email-solutions
[e-mail levering via een transactie]: https://sendgrid.com/transactional-email
