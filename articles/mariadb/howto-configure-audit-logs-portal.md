---
title: Access-controle logboeken-Azure Portal-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u de audit Logboeken in Azure Database for MariaDB kunt configureren en openen vanuit de Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: f1f3cda4ed9cdac6f6e03e88601b0a35d5c53faf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021160"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Controle logboeken configureren en openen in de Azure Portal

U kunt de [Azure database for MariaDB controle logboeken](concepts-audit-logs.md) en diagnostische instellingen van de Azure Portal configureren.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:

- [Azure Database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

>[!IMPORTANT]
> Het is raadzaam om alleen de gebeurtenis typen en gebruikers die vereist zijn voor controle doeleinden te registreren om ervoor te zorgen dat de prestaties van uw server niet sterk worden beïnvloed.

Controle logboek registratie inschakelen en configureren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer uw Azure Database for MariaDB-server.

1. Selecteer in de sectie **instellingen** van de zijbalk **server parameters**.
    ![Serverparameters](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Werk de para meter **audit_log_enabled** bij naar aan.
    ![Audit logboeken inschakelen](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecteer de [gebeurtenis typen](concepts-audit-logs.md#configure-audit-logging) die moeten worden geregistreerd door de **audit_log_events** -para meter bij te werken.
    ![Controle logboek gebeurtenissen](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Voeg MariaDB-gebruikers toe die moeten worden uitgesloten van logboek registratie door de para meter **audit_log_exclude_users** bij te werken. Geef gebruikers op door hun MariaDB-gebruikers naam op te geven.
    ![Gebruikers met controle logboek uitsluiten](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Wanneer u de para meters hebt gewijzigd, kunt u op **Opslaan** klikken. Of u kunt uw wijzigingen **negeren** .
    ![Opslaan](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

1. Selecteer in de sectie **bewaking** van de zijbalk **Diagnostische instellingen**.

1. Klik op ' + Diagnostische instelling toevoegen ' ![ Diagnostische instelling toevoegen](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Geef een naam op voor de diagnostische instelling.

1. Geef op welke gegevens moeten worden gesinkt om de controle Logboeken (opslag account, Event Hub en/of Log Analytics-werk ruimte) te verzenden.

1. Selecteer ' MySqlAuditLogs ' als het logboek type.
![Diagnostische instelling configureren](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Zodra u de gegevens sinks hebt geconfigureerd om de controle logboeken te pipeen naar, kunt u op **Opslaan** klikken.
![Diagnostische instelling opslaan](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Open de audit logboeken door ze te verkennen in de gegevens-sinks die u hebt geconfigureerd. Het kan tot tien minuten duren voordat de logboeken worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [audit logboeken](concepts-audit-logs.md) in azure database for MariaDB
- Meer informatie over het configureren van audit Logboeken in de [Azure cli](howto-configure-audit-logs-cli.md)