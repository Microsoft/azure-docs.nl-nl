---
title: Server parameters configureren-Azure Portal-Azure Database for PostgreSQL-flexibele server
description: In dit artikel wordt beschreven hoe u de post gres-para meters in Azure Database for PostgreSQL flexibele server kunt configureren via de Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 29d85cc33dc55f3819bba6898a265c46b7e7ef85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607436"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Configureer Server parameters in Azure Database for PostgreSQL-flexibele server via de Azure Portal 

U kunt configuratie parameters voor een Azure Database for PostgreSQL flexibele server via de Azure Portal weer geven, tonen en bijwerken.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Flexibele server Azure Database for PostgreSQL](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Para meters weer geven en bewerken

1. Open de [Azure Portal](https://portal.azure.com).

2. Selecteer uw flexibele server.

3. Selecteer in de sectie **instellingen** de optie **server parameters**. Op de pagina ziet u een lijst met para meters, hun waarden en beschrijvingen.
![Overzichts pagina voor para meters](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecteer de **vervolg keuzelijst** om de mogelijke waarden voor enumerated-type para meters zoals client_min_messages weer te geven.
![Vervolg keuzelijst opsommen](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecteer of Beweeg de muis aanwijzer over de knop met het **i** (informatie) om het bereik van mogelijke waarden voor numerieke para meters zoals cpu_index_tuple_cost weer te geven.
![informatie knop](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Gebruik, indien nodig, het **zoekvak** om te beperken tot een specifieke para meter. De zoek opdracht bevindt zich op de naam en beschrijving van de para meters.
![Zoekresultaten](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Wijzig de parameter waarden die u wilt aanpassen. Alle wijzigingen die u aanbrengt in een sessie, worden in paars gemarkeerd. Wanneer u de waarden hebt gewijzigd, kunt u **Opslaan** selecteren. Of u kunt uw wijzigingen **negeren** .
![Wijzigingen opslaan of negeren](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Als u nieuwe waarden voor de para meters hebt opgeslagen, kunt u altijd terugkeren naar de standaard waarden door **alles opnieuw instellen op de standaard** waarde te selecteren.
![Alles opnieuw instellen op standaard waarden](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Overzicht van server parameters in Azure Database for PostgreSQL](concepts-servers.md)
- [Para meters configureren met de Azure CLI](howto-configure-server-parameters-using-cli.md)
