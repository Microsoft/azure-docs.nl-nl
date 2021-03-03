---
title: Problemen met Azure Monitor waarschuwingen en meldingen oplossen
description: Veelvoorkomende problemen met Azure Monitor waarschuwingen en mogelijke oplossingen.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: d3f8da7e985e62ce0b40c6dddcd137cce8561e59
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717856"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Problemen met Azure Monitor waarschuwingen oplossen

In dit artikel worden veelvoorkomende problemen met Azure Monitor waarschuwingen en meldingen besproken.

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. Zie [overzicht van waarschuwingen in Microsoft Azure](./alerts-overview.md)voor meer informatie over waarschuwingen.

Raadpleeg de onderstaande artikelen als u een probleem ondervindt met het activeren van een waarschuwing of het niet starten van de verwachte. U kunt ' geactiveerde ' waarschuwingen weer geven in de Azure Portal.

- [Problemen met Azure Monitor metrische waarschuwingen in Microsoft Azure oplossen](alerts-troubleshoot-metric.md)  
- [Problemen met Azure Monitor logboek waarschuwingen in Microsoft Azure oplossen](alerts-troubleshoot-metric.md)

Als de waarschuwing wordt geactiveerd volgens de Azure Portal maar de juiste meldingen niet worden uitgevoerd, gebruikt u de informatie in de rest van dit artikel om het probleem op te lossen.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>De actie of melding bij mijn waarschuwing werkt niet zoals verwacht

Als u een gebrande waarschuwing in de Azure Portal kunt zien, maar een probleem met een aantal acties of meldingen hebt, raadpleegt u de volgende secties.

## <a name="did-not-receive-expected-email"></a>Er is geen verwachte e-mail ontvangen

Als u een geactiveerde waarschuwing in de Azure-portal ziet, maar u het e-mailbericht dat u hebt geconfigureerd niet heeft ontvangen, volgt u de volgende stappen:

1. **Is de e-mail onderdrukt door een [actie regel](../alerts/alerts-action-rules.md)**?

    Controleer door op de geactiveerde waarschuwing in de portal te klikken en bekijk het tabblad geschiedenis voor onderdrukte [actiegroepen](./action-groups.md):

    ![Geschiedenis van onderdrukking van waarschuwings actie regel](media/alerts-troubleshoot/history-action-rule.png)

1. **Is het type actie ' e-mail Azure Resource Manager role '?**

    Met deze actie wordt alleen gezocht naar Azure Resource Manager roltoewijzingen die zich op het abonnements bereik bevinden, en van het type *gebruiker*.  Zorg ervoor dat u de rol op abonnementsniveau hebt toegewezen en niet op resourceniveau of resourcegroepniveau.

1. **Accepteren uw e-mail server en Postvak externe e-mail berichten?**

    Controleer of e-mailberichten van deze drie adressen niet worden geblokkeerd:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Het is gebruikelijk dat interne mailinglijsten of distributielijsten e-mails van externe e-mailadressen blokkeren. U moet e-mail van de bovenstaande e-mail adressen toestaan.  
    Als u wilt testen, voegt u een normaal zakelijk e-mailadres (geen adressenlijst) toe aan de actiegroep en controleert u of er waarschuwingen voor deze e-mail binnenkomen.

1. **Is het e-mail bericht verwerkt door de regels voor het postvak in of een spam filter?**

    Controleer of er geen regels voor het Postvak IN zijn die ervoor zorgen dat de e-mailberichten worden verwijderd of verplaatst naar een side-map. Regels voor Postvak in kunnen bijvoorbeeld specifieke afzenders of specifieke woorden in het onderwerp ondervangen.

    Controleer ook het volgende:

   - de spaminstellingen van uw e-mailclient (zoals Outlook, Gmail)
      - de verzender limieten/instellingen voor ongewenste e-mail/quarantaine-instellingen van uw e-mail server (zoals Exchange, Microsoft 365, G-suite)
      - de instellingen van uw e-mail beveiligings apparaat (zoals Barracuda, Cisco).

1. **Hebt u per ongeluk afgemeld voor de actie groep?**

    In de waarschuwingsmails vindt u een link waarmee u zich kunt afmelden bij de actiegroep. Als u wilt controleren of u zich per ongeluk hebt afgemeld voor deze actiegroep:

    1. Open de actiegroep in de portal en controleer de kolom Status:

    ![kolom Status van actie groep](media/alerts-troubleshoot/action-group-status.png)

    2. Zoek in uw e-mail naar de afmeldingsbevestiging:

    ![afgemeld van de waarschuwingsactiegroep](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Als u zich opnieuw wilt aanmelden, gebruikt u de link in de bevestigingsmail voor afmelden die u hebt ontvangen of verwijdert u het e-mailadres uit de actiegroep en voegt u het vervolgens opnieuw toe. 
 
1. **Hebt u minder geclassificeerd als gevolg van veel e-mails die naar één e-mail adres worden verzonden?**

    E-mail heeft een [frequentielimiet](alerts-rate-limiting.md) van max. 100 e-mails per uur naar elk e-mailadres. Als u deze drempelwaarde overschrijdt, worden extra e-mails verwijderd.  Controleer of u een bericht hebt ontvangen waarin staat dat uw frequentielimiet is bereikt: 
 
   ![Frequentielimiet bereikt](media/alerts-troubleshoot/email-paused.png)

   Als u een hoog volume aan meldingen wilt ontvangen zonder frequentielimiet, kunt u overwegen een andere actie te gebruiken, zoals een webhook, een logische app, Azure-functie of Automation-runbooks, die allemaal geen frequentielimiet hebben. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Er is geen verwachte SMS, telefoon oproep of push melding ontvangen

Als u een geactiveerde waarschuwing in de portal ziet, maar geen sms-bericht, spraakoproep of pushmelding heeft ontvangen die u hebt geconfigureerd, volgt u de volgende stappen: 

1. **Is de actie onderdrukt door een [actie regel](../alerts/alerts-action-rules.md)?**

    Controleer door op de geactiveerde waarschuwing in de portal te klikken en bekijk het tabblad geschiedenis voor onderdrukte [actiegroepen](./action-groups.md): 

    ![Geschiedenis van onderdrukking van waarschuwings actie regel](media/alerts-troubleshoot/history-action-rule.png)

   Als dat onbedoeld is, kunt u de actieregel wijzigen, uitschakelen of verwijderen.
 
1. **SMS/Voice: is uw telefoon nummer correct?**

   Controleer de sms-actie op typfouten in het land- of telefoonnummer.
 
1. **SMS/Voice: hebt u een beperkt aantal beperkingen?**

    Sms-berichten en spraakoproepen hebben een frequentielimiet van max. 1 melding per 5 minuten per telefoonnummer. Als u deze drempel overschrijdt, worden de meldingen verwijderd.

      - Spraakoproep: controleer de oproepgeschiedenis en kijk of u in de voorgaande vijf minuten een andere oproep van Azure hebt ontvangen.
      - Sms-bericht: controleer uw sms-geschiedenis op een bericht dat aangeeft dat uw telefoonnummer een frequentielimiet heeft.

    Als u een hoog volume aan meldingen wilt ontvangen zonder frequentielimiet, kunt u overwegen een andere actie te gebruiken, zoals een webhook, een logische app, Azure-functie of Automation-runbooks, die allemaal geen frequentielimiet hebben. 
 
1. **SMS: hebt u per ongeluk afgemeld bij de actie groep?**

    Open uw SMS-geschiedenis en controleer of u geen SMS-levering van deze specifieke actie groep hebt uitgevoerd (met behulp van de action_group_short_name reply uitschakelen) of vanuit alle actie groepen (met behulp van het STOP bericht). Als u zich opnieuw wilt aanmelden, verzendt u de relevante sms-opdracht (action_group_short_name INSCHAKELEN of STARTEN) of verwijdert u de SMS-actie uit de actiegroep en voegt u deze vervolgens weer toe.  Zie voor meer informatie [gedrag van sms-waarschuwingen in actiegroepen](alerts-sms-behavior.md).

1. **Hebt u per ongeluk de meldingen op uw telefoon geblokkeerd?**

   De meeste mobiele telefoons bieden u de mogelijkheid om oproepen of sms-berichten van specifieke telefoonnummers of korte codes te blokkeren, of om pushmeldingen van specifieke apps te blokkeren (zoals de mobiele Azure-toepassing). Als u wilt controleren of u per ongeluk de meldingen op uw telefoon hebt geblokkeerd, zoekt u dit op in de documentatie die specifiek is voor het besturingssysteem en het model van uw telefoon, of test u dit met een andere telefoon -en ander telefoonnummer.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Er wordt een ander type actie verwacht, maar dit is niet 
   
Als u een geactiveerde waarschuwing in de portal ziet, maar de geconfigureerde actieregel niet werd geactiveerd, volgt u deze stappen:

1. **Is de actie onderdrukt door een actie regel?**

    Controleer door op de geactiveerde waarschuwing in de portal te klikken en bekijk het tabblad geschiedenis voor onderdrukte [actiegroepen](./action-groups.md):

    ![Geschiedenis van onderdrukking van waarschuwings actie regel](media/alerts-troubleshoot/history-action-rule.png)
 
    Als dat onbedoeld is, kunt u de actieregel wijzigen, uitschakelen of verwijderen.

1. **Is er geen webhook geactiveerd?**

    1. **Zijn de IP-bronadressen geblokkeerd?**
    
       Voeg de [IP-adressen](./action-groups.md#action-specific-information) waarvan de webhook wordt aangeroepen toe aan uw acceptatie lijst.

    1. **Werkt uw webhook-eindpunt correct?**

       Controleer of het geconfigureerde webhook-eindpunt correct is en of het eindpunt correct werkt. Controleer de logboeken van uw webhook of instrumenteer de code ervan, zodat u deze kunt onderzoeken (bijvoorbeeld de binnenkomende nettolading registreren).

    1. **Roept u Slack of Microsoft Teams aan?**  
    Elk van deze eindpunten verwacht een specifieke JSON-indeling. Volg [deze instructies](../alerts/action-groups-logic-app.md) om in plaats daarvan een actie van een logische app te configureren.

    1. **Is uw webhook niet meer reageert of fouten geretourneerd?** 

        Onze time-outperiode voor een webhook bedraagt 10 seconden. De webhook-oproep wordt nog 2 keer herhaald wanneer de volgende HTTP-statuscodes worden geretourneerd: 408, 429, 503, 504 of als het HTTP-eindpunt niet reageert. De eerste poging vindt plaats na 10 seconden. De tweede en laatste poging vindt plaats na 100 seconden. Als de tweede poging mislukt, wordt het eindpunt 30 minuten lang niet meer opgeroepen voor een actiegroep.

## <a name="action-or-notification-happened-more-than-once"></a>De actie of melding is meer dan een keer opgetreden 

Als u een melding voor een waarschuwing (zoals een e-mailbericht of SMS) meer dan een keer hebt ontvangen of de actie van de waarschuwing (zoals webhook of Azure-functie) meerdere keren is geactiveerd, voert u de volgende stappen uit: 

1. **Is de waarschuwing echt hetzelfde?** 

    In sommige gevallen worden meerdere vergelijkbare waarschuwingen rond dezelfde tijd geactiveerd. Dan lijkt het net alsof de actie meerdere keren is geactiveerd door dezelfde waarschuwing. Zo kan een waarschuwingsregel voor een activiteitenlogboek zodanig worden geconfigureerd dat deze beide worden geactiveerd wanneer een gebeurtenis is gestart, en wanneer deze is voltooid (geslaagd of mislukt) door niet te filteren op het veld status van gebeurtenis. 

    Als u wilt controleren of deze acties of meldingen afkomstig zijn van verschillende waarschuwingen, bekijkt u de details van de waarschuwing, zoals de timestamp en de id van de waarschuwing of de correlatie-id. U kunt ook de lijst met geactiveerde waarschuwingen in de portal controleren. Als dat het geval is, moet u de logica van de waarschuwings regel aanpassen of de bron van de waarschuwing anders configureren. 

1. **Herhaalt de actie in meerdere actie groepen?** 

    Wanneer een waarschuwing wordt geactiveerd, worden alle actiegroepen onafhankelijk van elkaar verwerkt. Als er dus een actie (zoals een e-mail adres) wordt weergegeven in meerdere geactiveerde actiegroepen, wordt deze eenmaal per actiegroep aangeroepen. 

    Controleer het tabblad waarschuwingsgeschiedenis om te controleren welke actiegroepen zijn geactiveerd. U ziet dat er actiegroepen worden gedefinieerd in de waarschuwingsregel, en actiegroepen die zijn toegevoegd aan de waarschuwing door actieregels: 

    ![Actie herhaald in meerdere actie groepen](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Actie of melding heeft onverwachte inhoud

Als u de waarschuwing hebt ontvangen, maar denkt dat sommige velden ontbreken of onjuist zijn, voert u de volgende stappen uit: 

1. **Hebt u de juiste indeling gekozen voor de actie?** 

    Elk actie type (e-mail adres, webhook, enzovoort) heeft twee indelingen: de standaard, verouderde indeling en de [nieuwere algemene schema-indeling](../alerts/alerts-common-schema.md). Wanneer u een actiegroep maakt, geeft u de gewenste indeling per actie op: verschillende acties in de actiegroepen hebben mogelijk verschillende indelingen. 

    Bijvoorbeeld voor de actie webhook: 

    ![optie voor actie schema van webhook](media/alerts-troubleshoot/webhook.png)

    Controleer of de indeling die u voor de actie hebt opgegeven, overeenkomt met uw verwachting. Het is bijvoorbeeld mogelijk dat u code hebt ontwikkeld die reageert op waarschuwingen (webhook, functie, logische app, enzovoort), en waarvoor u een bepaalde indeling verwacht, maar u of iemand anders later in de actie een andere indeling opgeeft.  

    Controleer ook de payload-indeling (JSON) voor [waarschuwingen voor activiteitenlogboeken](../alerts/activity-log-alerts-webhook.md), voor [zoekwaarschuwingen voor logboeken](../alerts/alerts-log-webhook.md) (zowel Application Insights als Log Analytics), voor [metrische waarschuwingen](alerts-metric-near-real-time.md#payload-schema), voor het [algemene schema voor waarschuwingen](../alerts/alerts-common-schema-definitions.md), en voor de afgeschafte [klassieke metrische waarschuwingen](./alerts-webhooks.md).

 
1. **Waarschuwingen voor activiteiten logboeken: is de informatie beschikbaar in het activiteiten logboek?** 

    [Waarschuwingen voor activiteiten logboeken](./activity-log-alerts.md) zijn waarschuwingen die zijn gebaseerd op gebeurtenissen die worden geschreven naar het Azure-activiteiten logboek, zoals gebeurtenissen over het maken, bijwerken of verwijderen van Azure-resources, service status-en resource status gebeurtenissen of bevindingen van Azure Advisor en Azure Policy. Als u een waarschuwing hebt ontvangen op basis van het activiteitenlogboek, maar sommige velden die u nodig hebt ontbreken of onjuist zijn, moet u eerst de gebeurtenissen in het activiteitenlogboek zelf controleren. Als de Azure-resource de velden die u zoekt niet in de gebeurtenis in het activiteitenlogboek heeft geregistreerd, worden deze velden niet opgenomen in de bijbehorende waarschuwing. 

## <a name="action-rule-is-not-working-as-expected"></a>Actieregel werkt niet zoals verwacht 

Als u een geactiveerde waarschuwing in de portal ziet, maar een gerelateerde actieregel niet zoals verwacht werkt, volgt u deze stappen: 

1. **Is de actie regel ingeschakeld?** 

    Controleer de kolom status van de actieregel om te controleren of de gerelateerde actierol is ingeschakeld. 

    ![afbeelding](media/alerts-troubleshoot/action-rule-status.png) 

    Als deze niet is ingeschakeld, kunt u de actieregel inschakelen door deze te selecteren en op inschakelen te klikken. 

1. **Is het een service status waarschuwing?** 

    Statusmelding voor de service (Service bewaken = "Servicestatus") worden niet beïnvloed door actieregels. 

1. **Reageerde de actieregel op uw waarschuwing?** 

    Controleer of de actieregel uw waarschuwing heeft verwerkt door op de geactiveerde waarschuwing in de portal te klikken en bekijk het tabblad geschiedenis.

    Hier volgt een voorbeeld van een actieregel die alle actiegroepen onderdrukt: 
 
     ![Geschiedenis van onderdrukking van waarschuwings actie regel](media/alerts-troubleshoot/history-action-rule.png)

    Hier volgt een voorbeeld van een actieregel die een andere actiegroep toevoegt:

    ![Actie herhaald in meerdere actie groepen](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Komen het bereik en filter van de actieregel overeen met de geactiveerde waarschuwing?** 

    Als u denkt dat de actieregel geactiveerd had moeten worden, maar niet is uitgevoerd, of dat deze niet is geactiveerd had moeten worden maar dit wel is gebeurd, controleert u zorgvuldig het bereik van de actieregel en filtervoorwaarden, vergeleken met de eigenschappen van de geactiveerde waarschuwing. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>De waarschuwings-ID van een geactiveerde waarschuwing zoeken

Wanneer u een case opent over een specifieke gebrande waarschuwing (bijvoorbeeld: als u de e-mail melding niet hebt ontvangen), moet u de waarschuwings-ID opgeven. 

Ga als volgt te werk om het bestand te zoeken:

1. Navigeer in het Azure Portal naar de lijst met geactiveerde waarschuwingen en zoek deze specifieke waarschuwing. U kunt de filters gebruiken om u te helpen bij het zoeken. 

1. Klik op de waarschuwing om de details van de waarschuwing te openen. 

1. Schuif omlaag in de waarschuwings velden van het eerste tabblad (het tabblad samen vatting) totdat u deze hebt gevonden en kopieer deze. Dit veld bevat ook de Help-knop ' kopiëren naar het klem bord ' die u kunt gebruiken.  

    ![waarschuwings-ID zoeken](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Probleem bij het maken, bijwerken of verwijderen van actie regels in de Azure Portal

Als er een fout is opgetreden bij het maken, bijwerken of verwijderen van een [actie regel](../alerts/alerts-action-rules.md), voert u de volgende stappen uit: 

1. **Hebt u een machtigings fout ontvangen?**  

    U moet de [ingebouwde rol voor het bijhouden van Inzender](../../role-based-access-control/built-in-roles.md#monitoring-contributor)hebben of de specifieke machtigingen met betrekking tot actie regels en waarschuwingen.

1. **Hebt u de para meters voor de actie regel gecontroleerd?**  

    Raadpleeg de [documentatie over de actie regel](../alerts/alerts-action-rules.md)of de [actie regel Power shell set-AzActionRule](/powershell/module/az.alertsmanagement/set-azactionrule?view=azps-3.5.0) opdracht. 


## <a name="next-steps"></a>Volgende stappen
- Als u een logboek waarschuwing gebruikt, raadpleegt u ook [logboek waarschuwingen voor problemen oplossen](./alerts-troubleshoot-log.md).
- Ga terug naar de [Azure Portal](https://portal.azure.com) om te controleren of u het probleem hebt opgelost met bovenstaande richt lijnen