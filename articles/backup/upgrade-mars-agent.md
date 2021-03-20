---
title: De Microsoft Azure Recovery Services-agent (MARS) upgraden
description: Meer informatie over het upgraden van de Microsoft Azure Recovery Services-agent (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: a1ee26db962781643e9599069282647658301bac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89181473"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>De Microsoft Azure Recovery Services-agent (MARS) upgraden

In dit artikel leert u het volgende:

* Servers met eerdere versies van de MARS-agent identificeren
* MARS-installaties op deze servers bijwerken

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Servers met eerdere versies van de MARS-agent identificeren

Voor installaties van Azure Backup Agent en Azure Backup Server:

1. Ga naar de Recovery Services kluis waar u servers hebt geregistreerd waarvan mogelijk een back-up wordt gemaakt door oudere versies van de agent. U kunt een representatieve lijst met kluizen vinden met oudere Azure Backup agents in de Azure Backup update-waarschuwingen van Azure.
1. Selecteer in de sectie **instellingen** aan de linkerkant van de Recovery Services kluis **back-upinfrastructuur** onder de sectie **beheren** .
1. Als u Azure Backup agents wilt detecteren die zijn geïnstalleerd als onderdeel van Azure Backup-Server installaties, gaat u naar **back-upbeheer servers** onder **beheerser vers**. Hiermee worden de servers met Azure Backup Server installaties weer geven samen met het versie nummer van de gekoppelde Azure Backup Agent.

    ![Lijst met MARS-agents die zijn geïnstalleerd als onderdeel van Azure Backup Server-installaties](./media/upgrade-mars-agent/backup-management-servers.png)

1. Ga naar **beveiligde servers** onder **beheerser** vers om de installatie van agents voor Microsoft Azure Recovery Services (MARS) of de Azure backup agent te controleren. Selecteer vervolgens **Azure backup agent** onder type back-upbeheer. Hiermee worden de servers weer geven die Azure Backup Agent-installaties hebben en het versie nummer van de installatie.

    ![Lijst met servers waarop de MARS-agent is geïnstalleerd](./media/upgrade-mars-agent/protected-servers.png)

1. Sorteer de kolom Azure Backup Agent versie door de kolom **Agent versie** te selecteren voor installatie van Mars agent of de kolom **Azure Backup Agent-versie** voor Azure backup server-installaties.

1. In de vorige stap krijgt u de lijst met servers met Azure Backup-agents met een lagere versie dan 2.0.9083.0 of agent versies vermeld als lege waarden. Dit zijn de servers waar Azure Backup agents moeten worden bijgewerkt.

## <a name="update-the-mars-agent-installation-on-the-server"></a>De installatie van de MARS-agent op de server bijwerken

Zodra u hebt vastgesteld welke servers een Azure Backup Agent update nodig hebben, voert u de volgende stappen uit voor elke geïdentificeerde server (met behulp van Azure Backup-Server of de MARS-agent). [Down load de nieuwste versie van de Azure backup agent](https://aka.ms/azurebackup_agent) voordat u de volgende stappen uitvoert.

1. Selecteer een rij met Azure Backup Agent kleiner dan 2.0.9083.0 of leeg is. Hiermee wordt het scherm Server Details geopend.

    ![Beveiligde servers met verouderde agent versies](./media/upgrade-mars-agent/old-agent-version.png)

    ![Azure Backup-servers met verouderde agent versies](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Selecteer **verbinding maken** om een extern bureau blad-verbindings bestand te ontvangen om verbinding te maken met de server of maak rechtstreeks verbinding met de server via de Extern bureaublad verbinding op uw server.

    ![Verbinding maken met de server via verbinding met extern bureau blad](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Als de vermelde server niet bestaat of uit bedrijf is genomen, kunt u de resterende stappen negeren en door gaan naar de volgende server.

1. Voer de aanmeldings gegevens voor de beheerder in en meld u aan.

1. Als uw server of server proxy beperkte internet toegang heeft, zorgt u ervoor dat de firewall-instellingen op de server/proxy zo zijn geconfigureerd dat de URL die geschikt is voor de Azure-Cloud die u gebruikt, wordt toegestaan:

    Azure-cloud | URL
    --- | ---
    Azure-Cloud (openbaar) |   `https://login.windows.net`
    Azure China 21Vianet-Cloud   | `https://login.chinacloudapi.cn`
    Azure-cloud van de Amerikaanse overheid |   `https://login.microsoftonline.us`
    Cloud van Azure Duitsland  |  `https://login.microsoftonline.de`

1. Kopieer het installatie programma voor de Azure Backup Agent-update naar de server.

    ![Azure Backup installatie programma voor agent-update naar server kopiëren](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Voer het installatieprogramma uit. De wizard upgrade van de Microsoft Azure Recovery Services-agent wordt geopend.

    ![De wizard upgrade van de Microsoft Azure Recovery Services-agent](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Selecteer **Next**.

1. Selecteer **Upgraden**.

    ![Installatie van de Microsoft Azure Recovery Services-agent](./media/upgrade-mars-agent/upgrade-installation.png)

1. In het laatste bevestigings scherm wordt aangegeven dat de Azure Backup-Agent is bijgewerkt.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Voor klanten van System Center Data Protection Manager (SC DPM)

Als u Azure Backup-agents hebt geïnstalleerd op de System Center Data Protection Manager (SC DPM)-servers, moet u de onderstaande stappen volgen om te bepalen of er een Azure Backup Agent-update nodig is voor uw DPM-servers:

1. Meld u als beheerder aan bij uw SC DPM-server.
2. Open de DPM-console.
3. Selecteer **beheer** in de linkerbenedenhoek van de-console.
4. Zoek in de informatie die wordt weer gegeven in de linkernavigatiebalk naar de versie gegevens van de Azure Backup Agent.
5. Als de versie lager is dan 2.0.9083.0, downloadt u het installatie programma van de nieuwste Azure Backup Agent en voert u het installatie programma uit op de DPM-server om de Azure Backup-Agent bij te werken.

Herhaal de bovenstaande stappen voor alle DPM-servers in uw omgeving.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van een back-up van Windows-machines met behulp van de Azure backup Mars-agent](backup-windows-with-mars-agent.md)
