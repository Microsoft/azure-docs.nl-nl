---
title: Een back-up maken van een Exchange-server via System Center DPM
description: Meer informatie over het maken van een back-up van een Exchange-server Azure Backup met System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a89c37b8447b318c44faf0d4e0b1921d305e7f59
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519388"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Met System Center 2012 R2 DPM een back-up maken van een Exchange-server in Azure Backup

In dit artikel wordt beschreven hoe u een System Center 2012 R2 Data Protection Manager-server (DPM) configureert om een back-up te maken van een Microsoft Exchange-server naar Azure Backup.  

## <a name="updates"></a>Updates

Als u de DPM-server wilt registreren bij Azure Backup, moet u het meest recente update rollup installeren voor System Center 2012 R2 DPM en de nieuwste versie van de Azure Backup Agent. Haal het meest recente update-rollup op uit de [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Voor de voorbeelden in dit artikel is versie 2.0.8719.0 van de Azure Backup-agent geïnstalleerd en update rollup 6 geïnstalleerd op System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet [](backup-azure-dpm-introduction.md#prerequisites-and-limitations) u ervoor zorgen dat aan alle vereisten voor het gebruik van Microsoft Azure Backup voor het beveiligen van workloads is voldaan. Deze vereisten zijn onder andere:

* Er is een back-upkluis op de Azure-site gemaakt.
* Agent- en kluisreferenties zijn gedownload naar de DPM-server.
* De agent wordt geïnstalleerd op de DPM-server.
* De kluisreferenties zijn gebruikt om de DPM-server te registreren.
* Als u Exchange 2016 bebeveiligen, moet u een upgrade uitvoeren naar DPM 2012 R2 UR9 of hoger.

## <a name="dpm-protection-agent"></a>DPM-beveiligingsagent

Volg deze stappen om de DPM-beveiligingsagent op de Exchange-server te installeren:

1. Zorg ervoor dat de firewalls juist zijn geconfigureerd. Zie [Firewall-uitzonderingen configureren voor de agent](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Installeer de agent op de Exchange-server door **Beheeragents > te > installeren** in DPM Administrator-console. Zie [De DPM-beveiligingsagent installeren](/system-center/dpm/deploy-dpm-protection-agent) voor gedetailleerde stappen.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Een beveiligingsgroep maken voor de Exchange-server

1. Selecteer in DPM Administrator-console beveiliging **en** selecteer  vervolgens Nieuw op het lint met hulpprogramma's om de wizard **Nieuwe beveiligingsgroep maken te** openen.
2. Selecteer volgende **op** het welkomstscherm van de **wizard.**
3. Selecteer in **het scherm Type beveiligingsgroep selecteren** de optie **Servers** en selecteer **Volgende.**
4. Selecteer de Exchange-serverdatabase die u wilt beveiligen en selecteer **Volgende.**

   > [!NOTE]
   > Als u Exchange 2013 bebeveiligent, controleert u de vereisten voor [Exchange 2013.](/system-center/dpm/back-up-exchange)
   >
   >

    In het volgende voorbeeld is de Exchange 2010-database geselecteerd.

    ![Groepsleden selecteren](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecteer de methode voor gegevensbeveiliging.

    Noem de beveiligingsgroep en selecteer vervolgens beide van de volgende opties:

   * Ik wil kortetermijnbeveiliging met schijf.
   * Ik wil onlinebeveiliging.
6. Selecteer **Next**.
7. Selecteer de **optie Eseutil uitvoeren om de gegevensintegriteit te** controleren als u de integriteit van de Exchange Server-databases wilt controleren.

    Nadat u deze optie hebt geselecteerd, wordt de consistentiecontrole voor back-ups uitgevoerd op de DPM-server om het I/O-verkeer te voorkomen dat wordt gegenereerd door de **eseutil-opdracht** uit te voeren op de Exchange-server.

   > [!NOTE]
   > Als u deze optie wilt gebruiken, moet u de Ese.dll- en Eseutil.exe-bestanden kopiëren naar de map C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin op de DPM-server. Anders wordt de volgende fout geactiveerd:  
   > ![eseutil-fout](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Selecteer **Next**.
9. Selecteer de database voor **Back-up kopiëren** en selecteer vervolgens **Volgende.**

   > [!NOTE]
   > Als u geen volledige back-up selecteert voor ten minste één DAG-kopie van een database, worden logboeken niet afgekapt.
   >
   >
10. Configureer de doelstellingen **voor kortetermijnback-up** en selecteer **volgende.**
11. Controleer de beschikbare schijfruimte en selecteer **volgende.**
12. Selecteer het tijdstip waarop de DPM-server de initiële replicatie maakt en selecteer vervolgens **Volgende.**
13. Selecteer de opties voor consistentiecontrole en selecteer vervolgens **Volgende.**
14. Kies de database van wie u een back-up wilt maken naar Azure en selecteer **vervolgens Volgende.** Bijvoorbeeld:

    ![Onlinebeveiligingsgegevens opgeven](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definieer het **schema Azure Backup**, en selecteer vervolgens **Volgende.** Bijvoorbeeld:

    ![Online back-upschema opgeven](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Opmerking Online herstelpunten zijn gebaseerd op express volledige herstelpunten. Daarom moet u het online herstelpunt plannen na de tijd die is opgegeven voor het express-volledige herstelpunt.
    >
    >
16. Configureer het **retentiebeleid voor Azure Backup** en selecteer **vervolgens Volgende.**
17. Kies een onlinereplicatieoptie en selecteer **Volgende.**

    Als u een grote database hebt, kan het lang duren voordat de eerste back-up via het netwerk is gemaakt. U kunt dit probleem voorkomen door een offline back-up te maken.  

    ![Onlineretentiebeleid opgeven](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bevestig de instellingen en selecteer vervolgens **Groep maken.**
19. Selecteer **Sluiten**.

## <a name="recover-the-exchange-database"></a>De Exchange-database herstellen

1. Als u een Exchange-database wilt herstellen, **selecteert u** Herstel in DPM Administrator-console.
2. Zoek de Exchange-database die u wilt herstellen.
3. Selecteer een online herstelpunt in de *vervolgkeuzelijst* hersteltijd.
4. Selecteer **Herstellen om** de wizard Herstellen te **starten.**

Voor online herstelpunten zijn er vijf hersteltypen:

* **Herstellen naar oorspronkelijke Exchange Server-locatie:** De gegevens worden hersteld naar de oorspronkelijke Exchange-server.
* **Herstellen naar een andere database op een Exchange-server:** De gegevens worden hersteld naar een andere database op een andere Exchange-server.
* **Herstellen naar een hersteldatabase:** De gegevens worden hersteld naar een Exchange Recovery Database (RDB).
* **Kopiëren naar een netwerkmap:** De gegevens worden hersteld naar een netwerkmap.
* **Kopiëren naar tape:** Als u een tapebibliotheek of een stand-alone tapestation hebt aangesloten en geconfigureerd op de DPM-server, wordt het herstelpunt gekopieerd naar een vrije tape.

    ![Onlinereplicatie kiezen](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure Backup veelgestelde vragen](backup-azure-backup-faq.yml)
