---
title: E-mail Azure Backup rapporten
description: Geautomatiseerde taken maken voor het ontvangen van periodieke rapporten via e-mail
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 8c18d4c7a3c7a9ba343296961fa9a44614366405
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510473"
---
# <a name="email-azure-backup-reports"></a>E-mail Azure Backup rapporten

Met de functie voor **e-mail** rapporten die beschikbaar is in back-uprapporten, kunt u geautomatiseerde taken maken voor het ontvangen van periodieke rapporten via e-mail. Deze functie werkt door een logische app te implementeren in uw Azure-omgeving die gegevens opvraagt van uw geselecteerde Log Analytics (LA)-werk ruimten, op basis van de invoer die u opgeeft. Meer [informatie over Logic apps en hun prijzen](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Aan de slag

Als u e-mail taken wilt configureren via back-uprapporten, voert u de volgende stappen uit:

1.  Navigeer naar **back-**  >  **uprapporten** van backup Center en klik op het tabblad **e-mail rapport** .
2.  Een taak maken door de volgende informatie op te geven:
    * **Taak Details** : de naam van de logische app die moet worden gemaakt, en het abonnement, de resource groep en de locatie waar deze moet worden gemaakt. Houd er rekening mee dat de logische app gegevens kan opvragen over meerdere abonnementen, resource groepen en locaties (zoals geselecteerd in de sectie rapport filters), maar wordt gemaakt in de context van één abonnement, resource groep en locatie.
    * **Te exporteren gegevens** : het tabblad dat u wilt exporteren. U kunt per tabblad één taak-app maken of alle tabbladen via één taak per e-mail verzenden door de optie **alle tabbladen** te selecteren.
    * **E-mail opties**: de e-mail frequentie, de e-mail-id van de ontvanger en het onderwerp van de e-mail.

    ![Tabblad e-mail](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Nadat u op **verzenden** en **bevestigen** hebt geklikt, wordt de logische app gemaakt. De logische app en de bijbehorende API-verbindingen worden gemaakt met de tag **UsedByBackupReports: True** voor gemakkelijke detectie. U moet een eenmalige autorisatie stap uitvoeren om de logische app te kunnen uitvoeren, zoals wordt beschreven in de volgende sectie.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Verbindingen met Azure Monitor-logboeken en Office 365 autoriseren

De logische app maakt gebruik van de [azuremonitorlogs](https://docs.microsoft.com/connectors/azuremonitorlogs/) -connector voor het uitvoeren van query's op de werk ruimte (n) en maakt gebruik van de [Office365 Outlook](https://docs.microsoft.com/connectors/office365connector/) -connector voor het verzenden van e-mail berichten. U moet een eenmalige autorisatie uitvoeren voor deze twee connectors. 
 
Volg de onderstaande stappen om de autorisatie uit te voeren:

1.  Navigeer naar **Logic apps** in het Azure Portal.
2.  Zoek naar de naam van de logische app die u hebt gemaakt en navigeer naar de resource.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Klik op het menu-item **API-verbindingen** .

    ![API-verbindingen](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  U ziet twee verbindingen met de indeling `<location>-azuremonitorlogs` en `<location>-office365` -dat wil zeggen, _Oost-azuremonitorlogs_ en _Oost-office365_.
5.  Navigeer naar elk van deze verbindingen en selecteer het menu-item **API-verbinding bewerken** . Selecteer in het scherm dat wordt weer gegeven, de optie **autoriseren** en sla de verbinding op nadat de autorisatie is voltooid.

    ![Verbinding machtigen](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Als u wilt testen of de logische app werkt na autorisatie, gaat u terug naar de logische app, opent u **overzicht** en selecteert u **trigger uitvoeren** in het bovenste deel venster om te testen of een e-mail bericht correct wordt gegenereerd.

## <a name="contents-of-the-email"></a>Inhoud van het e-mail bericht

* Alle grafieken en grafieken die in de portal worden weer gegeven, zijn beschikbaar als inline-inhoud in het e-mail bericht.
* De rasters die in de portal worden weer gegeven, zijn beschikbaar als *. CSV-bijlagen in het e-mail bericht.
* De gegevens die in het e-mail bericht worden weer gegeven, worden gebruikt voor alle filters op rapport niveau die door de gebruiker in het rapport zijn geselecteerd, op het moment dat de e-mail taak wordt gemaakt.
* Filters op tabblad niveau, zoals de naam van het **back-upexemplaar**, de naam van het **beleid** en dergelijke, worden niet toegepast. De enige uitzonde ring hierop is het raster **optimalisaties voor retentie** op het tabblad **Optimize** , waarbij de filters voor **dagelijkse**, **wekelijkse**, **maandelijkse** en **jaarlijkse** RP-retentie worden toegepast.
* Het tijds bereik en het aggregatie type (voor grafieken) zijn gebaseerd op de selectie van het tijds bereik van de gebruiker in de rapporten. Als de selectie van het tijds bereik bijvoorbeeld afgelopen 60 dagen is (vertalen naar een wekelijks aggregatie type) en de e-mail frequentie dagelijks is, ontvangt de ontvanger elke dag een e-mail met grafieken die de gegevens hebben verdeeld over de laatste periode van 60 dagen, met gegevens die per week worden geaggregeerd.

## <a name="troubleshooting-issues"></a>Problemen oplossen

Als u geen e-mail berichten ontvangt zoals verwacht, zelfs nadat de logische app is geïmplementeerd, kunt u de onderstaande stappen volgen om de configuratie op te lossen:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Scenario 1: u ontvangt geen e-mail bericht of fout-e-mail

* Dit probleem kan optreden omdat de API-connector van Outlook niet is gemachtigd. Volg de bovenstaande verificatie stappen voor het machtigen van de verbinding.

* Dit probleem kan ook optreden als u een onjuiste e-mail ontvanger hebt opgegeven tijdens het maken van de logische app. Als u wilt controleren of de e-mail ontvanger juist is opgegeven, kunt u naar de logische app in de Azure Portal gaan, de ontwerp functie voor logische apps openen en e-mail stap selecteren om te zien of de juiste e-mail-Id's worden gebruikt.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Scenario 2: een e-mail bericht ontvangen met de melding dat de logische app niet is uitgevoerd voor voltooiing

U kunt dit probleem als volgt oplossen:
1.  Ga naar de logische app in de Azure Portal.
2.  Aan de onderkant van het scherm **overzicht** ziet u een sectie met **uitvoerings geschiedenis** . U kunt openen in de meest recente uitvoering en bekijken welke stappen in de werk stroom zijn mislukt. Mogelijke oorzaken zijn:
    * **Azure monitor logboek connector is niet geautoriseerd**: als u dit probleem wilt verhelpen, volgt u de hierboven beschreven verificatie stappen.
    * **Fout in de la-query**: als u de logische app met uw eigen query's hebt aangepast, kan de logische app mislukken als gevolg van een fout in een van de la-query's. U kunt de relevante stap selecteren en de fout weer geven die ervoor zorgt dat de query onjuist wordt uitgevoerd.

Als de problemen zich blijven voordoen, neemt u contact op met micro soft ondersteuning.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over back-uprapporten](https://docs.microsoft.com/azure/backup/configure-reports)
