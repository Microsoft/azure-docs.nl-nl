---
title: 'Zelfstudie: E-mailmeldingen voor Apache Ambari configureren in Azure HDInsight'
description: In dit artikel wordt beschreven hoe u SendGrid gebruikt met Apache Ambari voor e-mailmeldingen.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 63d0f87925f14fddf3dbd722fa72326ee8b04f31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946974"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Zelfstudie: E-mailmeldingen voor Apache Ambari configureren in Azure HDInsight

In deze zelfstudie configureert u e-mailmeldingen voor Apache Ambari met behulp van SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) vereenvoudigt het beheer en de bewaking van een HDInsight-cluster door een gebruiksvriendelijke webgebruikersinterface en REST API te bieden. Ambari is opgenomen in HDInsight-clusters en wordt gebruikt om het cluster te bewaken en configuratiewijzigingen aan te brengen. [SendGrid](https://sendgrid.com/solutions/) is een gratis cloudgebaseerde e-mailservice die betrouwbare transactionele e-maillevering, schaalbaarheid en realtime analyse biedt, plus flexibele API's die aangepaste integratie eenvoudig maken. Azure-klanten kunnen iedere maand 25.000 gratis e-mails ontgrendelen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Sendgrid-gebruikersnaam ophalen
> * E-mailmeldingen voor Apache Ambari configureren

## <a name="prerequisites"></a>Vereisten

* Een SendGrid-e-mailaccount. Zie [E-mail verzenden via SendGrid met Azure](../sendgrid-dotnet-how-to-send-email.md) voor instructies.

* Een HDInsight-cluster. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>SendGrid-gebruikersnaam ophalen

1. Vanuit [Azure Portal](https://portal.azure.com) gaat u naar uw SendGrid-resource.

1. Selecteer op de pagina Overzicht de optie **Beheren** om naar de SendGrid-webpagina voor uw account te gaan.

    ![Overzicht van SendGrid in Azure Portal](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. Ga in het linkermenu naar uw accountnaam en klik op **Accountdetails**.

    ![SendGrid-dashboardnavigatie](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Registreer op de pagina **Accountdetails** de **Gebruikersnaam**.

    ![SendGrid-accountdetails](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>E-mailmelding voor Ambari configureren

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Selecteer in de vervolgkeuzelijst **Acties** de optie **Meldingen beheren**.

1. Selecteer in het venster **Waarschuwingsmeldingen beheren** het pictogram **+** .

    ![Schermafbeelding van het dialoogvenster Waarschuwingsmeldingen beheren.](./media/apache-ambari-email/azure-portal-create-notification.png)

1. Geef in het dialoogvenster **Waarschuwingsmelding maken** de volgende informatie op:

    |Eigenschap |Beschrijving |
    |---|---|
    |Naam|Voer een naam in voor de melding.|
    |Groepen|Configureer naar wens.|
    |Ernst|Configureer naar wens.|
    |Beschrijving|Optioneel.|
    |Methode|Laat de ingestelde versie staan op **EMAIL**.|
    |E-mailen naar|Geef een of meer e-mailadressen op om meldingen te ontvangen, gescheiden door een komma.|
    |SMTP-server|`smtp.sendgrid.net`|
    |SMTP-poort|25 of 587 (voor niet-versleutelde/TLS-verbindingen).|
    |E-mail van|Geef een e-mailadres op. Het adres hoeft niet authentiek te zijn.|
    |Verificatie gebruiken|Schakel dit selectievakje in.|
    |Gebruikersnaam|Geef de SendGrid-gebruikersnaam op.|
    |Wachtwoord|Geef het wachtwoord op dat u hebt gebruikt bij het maken van de SendGrid-resource in Azure.|
    |Wachtwoord bevestigen|Voer het wachtwoord opnieuw in.|
    |TLS starten|Schakel dit selectievakje in|

    ![Schermafbeelding van het dialoogvenster Waarschuwingsmeldingen maken.](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Selecteer **Opslaan**. U gaat terug naar het venster **Waarschuwingsmeldingen beheren**.

1. In het venster **Waarschuwingsmeldingen beheren** selecteert u **Sluiten**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u e-mailmeldingen voor Apache Ambari met behulp van SendGrid kunt configureren. Gebruik de volgende documenten voor meer informatie over Apache Ambari:

* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](./hdinsight-hadoop-manage-ambari.md)

* [Een waarschuwingsmelding maken](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)