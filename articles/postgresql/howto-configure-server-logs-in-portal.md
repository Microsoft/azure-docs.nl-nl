---
title: Logboeken beheren-Azure Portal-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u de server Logboeken (logboek bestanden) in Azure Database for PostgreSQL-één server kunt configureren en openen vanuit de Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3b52cea1d440506caf5b8244c9643719edd8755c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999243"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Azure Database for PostgreSQL-logboeken met één server configureren en gebruiken vanuit de Azure Portal

U kunt de [Azure database for PostgreSQL logboeken](concepts-server-logs.md) van de Azure Portal configureren, weer geven en downloaden.

## <a name="prerequisites"></a>Vereisten
Voor de stappen in dit artikel moet [Azure database for postgresql server](quickstart-create-server-database-portal.md)zijn geïnstalleerd.

## <a name="configure-logging"></a>Logboek registratie configureren
De toegang tot de query logboeken en fout logboeken configureren. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer uw Azure Database for PostgreSQL-server.

3. Selecteer in de sectie **bewaking** in de zijbalk **Server logboeken**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Scherm opname van opties voor Server logboeken":::

4. Selecteer om de server parameters weer te geven de optie **Klik hier om Logboeken in te scha kelen en logboek parameters te configureren**.

5. Wijzig de para meters die u wilt aanpassen. Alle wijzigingen die u aanbrengt in deze sessie, worden in paars gemarkeerd.

   Nadat u de para meters hebt gewijzigd, selecteert u **Opslaan**. Of u kunt uw wijzigingen negeren. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Scherm opname van opties voor server parameters":::

Op de pagina **server parameters** kunt u teruggaan naar de lijst met Logboeken door de pagina te sluiten.

## <a name="view-list-and-download-logs"></a>Lijst weer geven en logboeken downloaden
Nadat de logboek registratie is gestart, kunt u een lijst met beschik bare logboeken bekijken en afzonderlijke logboek bestanden downloaden. 

1. Open Azure Portal.

2. Selecteer uw Azure Database for PostgreSQL-server.

3. Selecteer in de sectie **bewaking** in de zijbalk **Server logboeken**. Op de pagina wordt een lijst met uw logboek bestanden weer gegeven.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Scherm opname van de pagina Server logboeken, met de lijst met gemarkeerde logboeken":::

   > [!TIP]
   > De naam Conventie van het logboek is **postgresql-jjjj-mm-dd_hh0000. log**. De datum en tijd die in de bestands naam wordt gebruikt, is het tijdstip waarop het logboek is uitgegeven. De logboek bestanden draaien elk uur of 100 MB, afhankelijk van wat het eerste komt.

4. Gebruik, indien nodig, het zoekvak om snel naar een specifiek logboek te beperken, op basis van datum en tijd. De zoek opdracht bevindt zich op de naam van het logboek.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="Scherm opname van de pagina Server logboeken, met zoekvak en resultaten gemarkeerd":::

5. Als u afzonderlijke logboek bestanden wilt downloaden, selecteert u het pictogram met de pijl-omlaag naast elk logboek bestand in de tabelrij.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="Scherm opname van de pagina Server logboeken, met pictogram met de pijl-omlaag gemarkeerd":::

## <a name="next-steps"></a>Volgende stappen
- Zie [toegangs server Logboeken in cli](howto-configure-server-logs-using-cli.md) voor meer informatie over het programmatisch downloaden van Logboeken.
- Meer informatie over [Server logboeken](concepts-server-logs.md) in azure database for PostgreSQL. 
- Zie de PostgreSQL-documentatie over [fout rapportage en logboek registratie](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)voor meer informatie over de parameter definities en postgresql-logboek registratie.

