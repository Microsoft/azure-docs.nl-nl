---
title: Problemen met wijzigingen in een virtuele machine van Azure in Azure Automation oplossen | Microsoft Docs
description: Dit artikel laat u zien hoe u problemen met wijzigingen in een virtuele machine van Azure kunt oplossen.
services: automation
ms.subservice: change-inventory-management
keywords: wijziging, bijhouden, bijhouden van wijzigingen, inventaris, automatisering
ms.date: 03/21/2021
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 980740d387f9e953d1ea764327c8aa13f8650948
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775800"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Problemen met wijzigingen aan een virtuele machine van Azure oplossen

In deze zelfstudie leert u hoe u problemen met wijzigingen in een virtuele machine van Azure oplost. Als u Wijzigingen bijhouden en Inventaris inschakelt, kunt u wijzigingen in software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers bijhouden.
Door deze configuratiewijzigingen op te sporen, kunt operationele problemen in uw hele omgeving vaststellen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Wijzigingen bijhouden en Inventaris inschakelen voor een VM
> * Zoeken in wijzigingenlogboeken voor gestopte services
> * Wijzigingen bijhouden configureren
> * Verbinding met activiteitenlogboek inschakelen
> * Een gebeurtenis activeren
> * Wijzigingen weergeven
> * Waarschuwingen configureren

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een [Automation-account](./index.yml) voor het opslaan van de watcher- en actie-runbooks en de Watcher-taak.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) die u voor de functie wilt inschakelen.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

Voor deze zelfstudie moet u eerst Wijzigingen bijhouden en Inventaris inschakelen. Als u de functie al eerder hebt ingeschakeld, is deze stap niet nodig.

>[!NOTE]
>Als de velden niet beschikbaar zijn, is er een andere Automation-functie ingeschakeld voor de VM en moet u dezelfde werkruimte en hetzelfde Automation-account gebruiken.

1. Selecteer **Virtuele machines** en selecteer een virtuele machine in de lijst.
2. Selecteer in het menu links de optie **Inventaris** onder **Bewerkingen**. De pagina Inventaris wordt geopend.

    ![Wijziging inschakelen](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. Kies de [Log Analytics](../azure-monitor/logs/log-query-overview.md)-werkruimte. In deze werkruimte worden gegevens verzameld die worden gegenereerd door functies als Wijzigingen bijhouden en Inventaris. De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. Selecteer het Automation-account dat u wilt gebruiken.

5. Configureer de locatie voor de implementatie.

5. Klik op **Inschakelen** om de functie voor uw virtuele machine te implementeren. 

Tijdens de installatie wordt de virtuele machine ingericht met de Log Analytics-agent voor Windows en een [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Schakel Wijzigingen bijhouden en Inventaris in. Dit kan maximaal 15 minuten duren. Gedurende deze tijd mag u het browservenster niet sluiten.

Nadat de functie is ingeschakeld, wordt informatie over geïnstalleerde software en wijzigingen aan de VM-stromen naar Azure Monitor-logboeken verzonden.
Het duurt tussen 30 minuten en 6 uur voordat de gegevens beschikbaar zijn voor analyse.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Wijzigingen bijhouden en Inventaris gebruiken in Azure Monitor-logboeken

Wijzigingen bijhouden en Inventaris genereren logboekgegevens die naar Azure Monitor-logboeken worden verzonden. Als u de logboeken wilt doorzoeken door query's uit te voeren, selecteert u **Log Analytics** bovenaan de pagina Wijzigingen bijhouden. Gegevens van Wijzigingen bijhouden worden opgeslagen onder het type `ConfigurationChange`.

De volgende voorbeeldquery voor Log Analytics retourneert alle Windows-services die zijn gestopt.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Zie [Azure Monitor-logboeken](../azure-monitor/logs/log-query-overview.md) voor meer informatie over het uitvoeren en doorzoeken van logboekbestanden in Azure Monitor-logboeken.

## <a name="configure-change-tracking"></a>Wijzigingen bijhouden configureren

Met wijzigingen bijhouden kiest u de bestanden en registersleutels die u wilt verzamelen en volgen met **Instellingen bewerken** bovenaan de pagina Wijzigingen bijhouden op uw virtuele machine. U kunt Windows-registersleutels, Windows-bestanden of Linux-bestanden die u wilt opvolgen toevoegen op de pagina Werkruimteconfiguratie.

> [!NOTE]
> Zowel wijzigingen bijhouden als inventaris gebruiken dezelfde verzamelingsinstellingen en instellingen worden geconfigureerd op het niveau van de werkruimte.

### <a name="add-a-windows-registry-key"></a>Een Windows-registersleutel toevoegen

1. Selecteer **Toevoegen** op het tabblad **Windows-register**. 

1. Op de pagina Windows-register voor het bijhouden van wijzigingen toevoegen voert u de gegevens in die u wilt bijhouden voor de sleutel en klikt u op **Opslaan**

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Bepaalt of de instelling wordt toegepast        |
    |Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden        |
    |Groep     | De naam van een groep voor het logisch groeperen van bestanden        |
    |Windows-registersleutel   | Het pad voor het bestand, bijvoorbeeld: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup      |

### <a name="add-a-windows-file"></a>Een Windows-bestand toevoegen

1. Selecteer **Toevoegen** op het tabblad **Windows-bestanden**. 

1. Op de pagina Windows-bestand voor het bijhouden van wijzigingen toevoegen voert u de gegevens van het bestand of de map voor het bijhouden van wijzigingen in en klikt u op **Opslaan**

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Bepaalt of de instelling wordt toegepast        |
    |Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden        |
    |Groep     | De naam van een groep voor het logisch groeperen van bestanden        |
    |Pad invoeren     | Het pad voor het controleren op het bestand, bijvoorbeeld: "C:\temp\\\*.txt"<br>U kunt ook omgevingsvariabelen gebruiken zoals ' %winDir%\System32\\\*. * "         |
    |Recursie     | Bepaalt of recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden.        |
    |Bestandsinhoud uploaden voor alle instellingen| Schakelt uploaden van bestandsinhoud bij bijgehouden wijzigingen in of uit. Beschikbare opties: **Waar** of **Onwaar**.|

### <a name="add-a-linux-file"></a>Een Linux-bestand toevoegen

1. Selecteer **Toevoegen** op het tabblad **Linux-bestanden**. 

1. Op de pagina Linux-bestand voor het bijhouden van wijzigingen toevoegen voert u de gegevens van het bestand of de map voor het bijhouden van wijzigingen in en klikt u op **Opslaan**.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Bepaalt of de instelling wordt toegepast        |
    |Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden        |
    |Groep     | De naam van een groep voor het logisch groeperen van bestanden        |
    |Pad invoeren     | Het pad voor het controleren op het bestand, bijvoorbeeld: "/etc/*.conf"       |
    |Padtype     | Type item voor het bijhouden van wijzigingen. Mogelijke waarden zijn Bestand en Map        |
    |Recursie     | Bepaalt of recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden.        |
    |Sudo gebruiken     | Deze instelling bepaalt of sudo wordt gebruikt bij het controleren op het item.         |
    |Koppelingen     | Deze instelling bepaalt hoe symbolische koppelingen worden afgehandeld bij het doorlopen van mappen.<br> **Negeren** - Symbolische koppelingen worden genegeerd en de bestanden/mappen waarnaar wordt verwezen, worden niet opgenomen<br>**Volgen** - Symbolische koppelingen worden gevolgd tijdens recursie en de bestanden/mappen waarnaar wordt verwezen, worden opgenomen<br>**Beheren** - Symbolische koppelingen worden gevolgd en de afhandeling van de geretourneerde inhoud kan worden gewijzigd      |
    |Bestandsinhoud uploaden voor alle instellingen| Schakelt uploaden van bestandsinhoud bij bijgehouden wijzigingen in of uit. Beschikbare opties: Waar of Onwaar.|

   > [!NOTE]
   > De waarde **Beheren** voor de eigenschap **Koppelingen** is niet aanbevolen. Het ophalen van bestandsinhoud wordt niet ondersteund.

## <a name="enable-activity-log-connection"></a>Verbinding met activiteitenlogboek inschakelen

1. Selecteer **Verbinding met het activiteitenlogboek beheren** op de pagina Wijzigingen bijhouden op de virtuele machine. 

2. Klik **Verbinden** op de pagina Azure-activiteitenlogboek om Wijzigingen bijhouden en Inventaris te verbinden met het Azure-activiteitenlogboek voor uw virtuele machine.

3. Ga naar de pagina Overzicht voor de virtuele machine en selecteer **Stoppen** om de virtuele machine te stoppen. 

4. Wanneer u daarom wordt gevraagd, selecteert u **Ja** om de virtuele machine te stoppen. 

5. Wanneer de virtuele machine is vrijgegeven, selecteert u **Starten** om deze opnieuw op te starten. Wanneer een virtuele machine wordt gestart en gestopt, wordt een gebeurtenis geregistreerd in het Activiteitenlogboek. 

## <a name="view-changes"></a>Wijzigingen weergeven

1. Ga terug naar de pagina Wijzigingen bijhouden en selecteer het tabblad **Gebeurtenissen** onder aan de pagina. 

2. Na een tijdje worden de gebeurtenissen weergegeven in de grafiek en in de tabel. De grafiek toont wijzigingen die in de loop der tijd hebben plaatsgevonden. Het lijndiagram bovenaan toont de gebeurtenissen in het Azure-activiteitenlogboek. Elke rij in het staafdiagram vertegenwoordigt een ander type wijziging dat kan worden gevolgd. Deze typen zijn Linux-daemons, bestanden, Windows-registersleutels, software en Windows-services. Onder het tabblad wijzigingen zijn de details van de getoonde wijzigingen te vinden. De meest recente wijzigingen worden eerst getoond.

    ![Gebeurtenissen in het portal bekijken](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. Merk op dat het systeem meerdere wijzigingen heeft ondergaan, inclusief wijzigingen in services en software. U kunt de filters bovenaan de pagina gebruiken om de resultaten te filteren op **Type wijziging** of op tijdsbereik.

    ![Lijst met wijzigingen aan de virtuele machine](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. Selecteer een **WindowsServices**-wijziging. Dit opent de pagina Details wijzigingen met detail over de wijziging en de waarden voor en na de wijziging. In dit geval is de service Software Protection gestopt.

    ![Details van de wijziging weergeven in de portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Waarschuwingen configureren

Het kan handig zijn om wijzigingen te bekijken in Azure Portal, maar het is nóg handiger om waarschuwingen te ontvangen wanneer er iets verandert (bijvoorbeeld wanneer een service wordt gestopt). Laten we een waarschuwing voor een gestopte service toevoegen. 

1. Ga in de Azure-portal naar **Bewaken**. 

2. Selecteer **Waarschuwingen** onder **Gedeelde services** en klik dan op **+ Nieuwe waarschuwingsregel**.

3. Klik op **Selecteren** om een resource te kiezen. 

4. Kies  **Log Analytics** uit het vervolgkeuzemenu **Filteren op resourcetype** op de pagina Een resource selecteren. 

5. Selecteer de Log Analytics-werkruimte en klik vervolgens op **Gereed**.

    ![Een resource selecteren](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. Klik op **Voorwaarde toevoegen**.

7. Selecteer **Aangepast zoeken in logboeken** in de tabel op de pagina Signaal configureren. 

8. Voer de volgende query in het tekstvak zoekquery in:

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    Met deze query worden de computers geretourneerd waarbij de W3SVC-service is gestopt tijdens de opgegeven periode.

9. Voer onder **Waarschuwingslogica** voor **Drempelwaarde** het volgende in: **0**. Als u klaar bent, klikt u op **Gereed**.

    ![Signaallogica configureren](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. Selecteer **Nieuwe maken** onder **Actiegroepen**. Een actiegroep is een groep acties die u op meerdere waarschuwingen kunt toepassen. Deze acties kunnen bestaan uit, maar zijn niet beperkt tot, e-mailmeldingen, runbooks, webhooks en nog veel meer. Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/alerts/action-groups.md) voor meer informatie over actiegroepen.

11. Voer onder **Waarschuwingsdetails definiëren** een naam en beschrijving voor de waarschuwing in. 

12. Stel **Ernst** in op **Informatie (Sev 2)** , **Waarschuwing (Sev 1)** of **Kritiek (Sev 0)** .

13. Voer in het vak **Naam van actiegroep** een naam in voor de waarschuwing en een korte naam. De korte naam wordt gebruikt in plaats van een volledige naam van de actiegroep als er meldingen via deze groep worden verzonden.

14. Voer een naam in voor de actie, zoals **Beheerders een e-mail verzenden** voor **Acties**. 

15. Selecteer voor **actie type** **e-mail/SMS-bericht/push/Voice**. 

16. Selecteer onder **DETAILS** de optie **Details bewerken**.

    :::image type="content" source="./media/automation-tutorial-troubleshoot-changes/add-action-group.png" alt-text="Gebruik en geschatte kosten." lightbox="./media/automation-tutorial-troubleshoot-changes/add-action-group.png":::

17. Voer in het deel venster **e-mail/SMS-bericht/push/Voice** een naam in, selecteer het selectie vakje **e-mail** en voer vervolgens een geldig e-mail adres in. Wanneer u klaar bent, klikt u op **OK** in het deel venster en vervolgens op **OK** op de pagina **actie groep toevoegen** .

18. Selecteer **Acties aanpassen** om het onderwerp van de waarschuwingsmail aan te passen.

19. Voor **Regel maken** selecteert u **E-mailonderwerp** en vervolgens **Waarschuwingsregel maken**. De waarschuwing laat u weten wanneer een update-implementatie is voltooid en op welke machines deze update-implementatie is uitgevoerd. In de volgende afbeelding ziet u een e-mail die u zou kunnen ontvangen wanneer de W3SVC-service stopt.

    ![Schermopname toont een e-mailmelding die wordt ontvangen wanneer de W 3 S V C-services worden gestopt.](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Wijzigingen bijhouden en Inventaris inschakelen voor een VM
> * Zoeken in wijzigingenlogboeken voor gestopte services
> * Wijzigingen bijhouden configureren
> * Verbinding met activiteitenlogboek inschakelen
> * Een gebeurtenis activeren
> * Wijzigingen weergeven
> * Waarschuwingen configureren

Ga verder naar het overzicht van de functie Wijzigingen bijhouden en Inventaris als u er meer over wilt weten.

> [!div class="nextstepaction"]
> [Overzicht Wijzigingen bijhouden en Inventaris](change-tracking/overview.md)
