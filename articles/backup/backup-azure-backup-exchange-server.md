---
title: Een back-up maken van een Exchange-server via System Center DPM
description: Meer informatie over het maken van een back-up van een Exchange-Server naar Azure Backup met behulp van System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ee89af311619922fa6ca585381d70ca66955f36a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91271644"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Met System Center 2012 R2 DPM een back-up maken van een Exchange-server in Azure Backup

In dit artikel wordt beschreven hoe u een System Center 2012 R2 Data Protection Manager (DPM)-server configureert om een back-up te maken van een micro soft Exchange-Server naar Azure Backup.  

## <a name="updates"></a>Updates

Als u de DPM-server met Azure Backup wilt registreren, moet u het meest recente update pakket voor System Center 2012 R2 DPM en de nieuwste versie van de Azure Backup-Agent installeren. Down load het meest recente update pakket van de [micro soft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Voor de voor beelden in dit artikel is versie 2.0.8719.0 van de Azure Backup-Agent geïnstalleerd en update pakket 6 geïnstalleerd op System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet u ervoor zorgen dat aan alle [vereisten](backup-azure-dpm-introduction.md#prerequisites-and-limitations) voor het gebruik van Microsoft Azure backup voor het beveiligen van werk belastingen is voldaan. Dit zijn onder andere de volgende vereisten:

* Er is een back-upkluis gemaakt op de Azure-site.
* De agent-en kluis referenties zijn gedownload naar de DPM-server.
* De agent is geïnstalleerd op de DPM-server.
* De kluis referenties zijn gebruikt voor het registreren van de DPM-server.
* Als u Exchange 2016 wilt beveiligen, moet u een upgrade uitvoeren naar DPM 2012 R2 UR9 of hoger.

## <a name="dpm-protection-agent"></a>DPM-beveiligings agent

Voer de volgende stappen uit om de DPM-beveiligings agent te installeren op de Exchange-Server:

1. Zorg ervoor dat de firewalls correct zijn geconfigureerd. Zie [firewall-uitzonde ringen voor de agent configureren](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Installeer de agent op de Exchange-Server door **beheer > agents** te selecteren > in DPM Administrator-console te installeren. Zie [de DPM-beveiligings agent installeren](/system-center/dpm/deploy-dpm-protection-agent) voor gedetailleerde stappen.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Een beveiligings groep maken voor de Exchange-Server

1. Selecteer in het DPM Administrator-console de optie **beveiliging** en selecteer vervolgens **Nieuw** op het lint met hulp middelen om de wizard **nieuwe beveiligings groep maken** te openen.
2. Selecteer **volgende** in het **welkomst** scherm van de wizard.
3. Selecteer op het scherm **type beveiligings groep selecteren** de optie **servers** en selecteer **volgende**.
4. Selecteer de Exchange Server-Data Base die u wilt beveiligen en selecteer **volgende**.

   > [!NOTE]
   > Als u Exchange 2013 wilt beveiligen, controleert u de [vereisten voor exchange 2013](/system-center/dpm/back-up-exchange).
   >
   >

    In het volgende voor beeld is de Exchange 2010-data base geselecteerd.

    ![Groeps leden selecteren](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecteer de methode voor gegevens beveiliging.

    Geef de beveiligings groep een naam en selecteer beide van de volgende opties:

   * Ik wil kortetermijnbeveiliging met schijf.
   * Ik wil online beveiliging.
6. Selecteer **Next**.
7. Selecteer de optie **Eseutil uitvoeren om gegevens integriteit te controleren** als u de integriteit van de Exchange server-data bases wilt controleren.

    Nadat u deze optie hebt geselecteerd, wordt er een consistentie controle van de back-up uitgevoerd op de DPM-server om te voor komen dat het I/O-verkeer dat wordt gegenereerd door de opdracht **Eseutil** uit te voeren op de Exchange-Server.

   > [!NOTE]
   > Als u deze optie wilt gebruiken, moet u de Ese.dll-en Eseutil.exe-bestanden kopiëren naar de map C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin op de DPM-server. Anders wordt de volgende fout geactiveerd:  
   > ![Eseutil-fout](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Selecteer **Next**.
9. Selecteer de Data Base voor **back-up kopiëren** en selecteer vervolgens **volgende**.

   > [!NOTE]
   > Als u geen volledige back-up voor ten minste één DAG kopie van een Data Base selecteert, worden de logboeken niet afgekapt.
   >
   >
10. Configureer de doel stellingen voor **back-up op korte termijn** en selecteer **volgende**.
11. Controleer de beschik bare schijf ruimte en selecteer **volgende**.
12. Selecteer het tijdstip waarop de DPM-server de initiële replicatie maakt, en selecteer vervolgens **volgende**.
13. Selecteer de opties voor consistentie controle en selecteer **volgende**.
14. Kies de data base waarvan u een back-up wilt maken naar Azure en selecteer **volgende**. Bijvoorbeeld:

    ![Gegevens voor online beveiliging opgeven](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definieer het schema voor **Azure backup** en selecteer vervolgens **volgende**. Bijvoorbeeld:

    ![Online back-upschema opgeven](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Houd er rekening mee dat online herstel punten zijn gebaseerd op snelle volledige herstel punten. Daarom moet u het online herstel punt plannen na het tijdstip dat is opgegeven voor het snelle volledige herstel punt.
    >
    >
16. Configureer het Bewaar beleid voor **Azure backup** en selecteer vervolgens **volgende**.
17. Kies een optie voor online replicatie en selecteer **volgende**.

    Als u een grote data base hebt, kan het enige tijd duren voordat de eerste back-up via het netwerk is gemaakt. Als u dit probleem wilt voor komen, kunt u een offline back-up maken.  

    ![Online Bewaar beleid opgeven](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bevestig de instellingen en selecteer vervolgens **groep maken**.
19. Selecteer **Sluiten**.

## <a name="recover-the-exchange-database"></a>De Exchange-data base herstellen

1. Als u een Exchange-Data Base wilt herstellen, selecteert u **herstel** in het DPM Administrator-console.
2. Zoek de Exchange-data base die u wilt herstellen.
3. Selecteer een online herstel punt in de vervolg keuzelijst *herstel tijd* .
4. Selecteer **herstellen** om de **wizard herstellen** te starten.

Voor online herstel punten zijn er vijf herstel typen:

* **Herstellen naar de oorspronkelijke Exchange Server-locatie:** De gegevens worden hersteld op de oorspronkelijke Exchange-Server.
* **Herstellen naar een andere Data Base op een Exchange-Server:** De gegevens worden hersteld naar een andere Data Base op een andere Exchange-Server.
* **Herstellen naar een herstel database:** De gegevens worden hersteld naar een Exchange Recovery-Data Base (RDB).
* **Kopiëren naar een** netwerkmap: De gegevens worden hersteld naar een netwerkmap.
* **Kopiëren naar tape:** Als u een tape wisselaar of een zelfstandig tape station hebt aangesloten en geconfigureerd op de DPM-server, wordt het herstel punt naar een vrije tape gekopieerd.

    ![Kies online replicatie](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over Azure Backup](backup-azure-backup-faq.md)
