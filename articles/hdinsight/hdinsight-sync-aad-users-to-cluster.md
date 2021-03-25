---
title: Azure Active Directory gebruikers synchroniseren met HDInsight-cluster
description: Synchroniseer geverifieerde gebruikers van Azure Active Directory naar een HDInsight-cluster.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 2a753a33e9ddf16cc277ab10c1f91049a6382066
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871942"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster

[HDInsight-clusters met Enterprise Security Package (ESP)](./domain-joined/hdinsight-security-overview.md) kunnen gebruikmaken van sterke verificatie met Azure Active Directory-gebruikers (Azure AD), maar ook *op rollen gebaseerd toegangs beheer (Azure RBAC)* . Wanneer u gebruikers en groepen aan Azure AD toevoegt, kunt u de gebruikers synchroniseren die toegang nodig hebben tot uw cluster.

## <a name="prerequisites"></a>Vereisten

Als u dit nog niet hebt gedaan, [maakt u een HDInsight-cluster met Enterprise Security Package](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="add-new-azure-ad-users"></a>Nieuwe Azure AD-gebruikers toevoegen

Als u uw hosts wilt weer geven, opent u de Ambari-webgebruikersinterface. Elk knoop punt wordt bijgewerkt met de nieuwe instellingen voor de installatie zonder toezicht.

1. Navigeer vanuit het [Azure Portal](https://portal.azure.com)naar de Azure AD-adres lijst die is gekoppeld aan uw ESP-cluster.

2. Selecteer **alle gebruikers** in het menu aan de linkerkant en selecteer vervolgens **nieuwe gebruiker**.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png" alt-text="Alle gebruikers en groepen Azure Portal":::

3. Vul het nieuwe gebruikers formulier in. Selecteer groepen die u hebt gemaakt voor het toewijzen van machtigingen op basis van het cluster. In dit voor beeld maakt u een groep met de naam ' HiveUsers ', waaraan u nieuwe gebruikers kunt toewijzen. De [voorbeeld instructies](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) voor het maken van een ESP-cluster zijn onder andere het toevoegen van twee groepen, `HiveUsers` en `AAD DC Administrators` .

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png" alt-text="Gebruikers deel venster Azure Portal groepen selecteren":::

4. Selecteer **Maken**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>De Apache Ambari-REST API gebruiken om gebruikers te synchroniseren

Gebruikersgroepen die zijn opgegeven tijdens het maken van het cluster, worden op dat moment gesynchroniseerd. Synchronisatie van gebruikers vindt automatisch één keer per uur plaats. Als u de gebruikers onmiddellijk wilt synchroniseren of als u een andere groep wilt synchroniseren dan de groepen die zijn opgegeven tijdens het maken van het cluster, gebruikt u de Ambari-REST API.

De volgende methode maakt gebruik van POST met de Ambari-REST API. Zie [HDInsight-clusters beheren met behulp van de Apache Ambari rest API](hdinsight-hadoop-manage-ambari-rest-api.md)voor meer informatie.

1. Gebruik de [ssh-opdracht](hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` te vervangen door de naam van uw cluster.Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nadat de verificatie is uitgevoerd, voert u de volgende opdracht in:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    Het antwoord moet er als volgt uitzien:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Als u de synchronisatie status wilt bekijken, voert u een nieuwe `curl` opdracht uit:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    Het antwoord moet er als volgt uitzien:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Dit geeft aan dat de status is **voltooid**, er een nieuwe gebruiker is gemaakt en er een lidmaatschap aan de gebruiker is toegewezen. In dit voor beeld wordt de gebruiker toegewezen aan de gesynchroniseerde LDAP-groep ' HiveUsers ', omdat de gebruiker is toegevoegd aan dezelfde groep in azure AD.

    > [!NOTE]  
    > De vorige methode synchroniseert alleen de Azure AD-groepen die zijn opgegeven in de eigenschap **gebruikers groep** van het domein instellingen tijdens het maken van het cluster. Zie  [een HDInsight-cluster maken](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)voor meer informatie.

## <a name="verify-the-newly-added-azure-ad-user"></a>De zojuist toegevoegde Azure AD-gebruiker verifiëren

Open de [Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md) om te controleren of de nieuwe Azure AD-gebruiker is toegevoegd. Open de Ambari-webgebruikersinterface door te bladeren naar **`https://CLUSTERNAME.azurehdinsight.net`** . Voer de gebruikers naam en het wacht woord van de Cluster beheerder in.

1. Selecteer in het Ambari-dash board **Ambari beheren** onder het menu **beheer** .

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png" alt-text="Apache Ambari-dash board Ambari beheren":::

2. Selecteer **gebruikers** onder de menu groep **gebruikers-en groeps beheer** aan de linkerkant van de pagina.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png" alt-text="Het menu gebruikers en groepen van HDInsight":::

3. De nieuwe gebruiker moet worden weer gegeven in de tabel gebruikers. Het type wordt ingesteld op `LDAP` in plaats van  `Local` .

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png" alt-text="Overzicht van de pagina HDInsight Aad-gebruikers":::

## <a name="log-in-to-ambari-as-the-new-user"></a>Meld u aan bij Ambari als de nieuwe gebruiker

Wanneer de nieuwe gebruiker (of een andere domein gebruiker) zich aanmeldt bij Ambari, gebruiken ze hun volledige gebruikers naam en domein referenties voor Azure AD.  In Ambari wordt een gebruikers alias weer gegeven. Dit is de weergave naam van de gebruiker in azure AD.
De nieuwe voorbeeld gebruiker heeft de gebruikers naam `hiveuser3@contoso.com` . In Ambari wordt deze nieuwe gebruiker weer gegeven als, `hiveuser3` maar de gebruiker meldt zich aan bij Ambari als `hiveuser3@contoso.com` .

## <a name="see-also"></a>Zie ook

* [Apache Hive beleid in HDInsight configureren met ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [HDInsight-clusters beheren met ESP](./domain-joined/apache-domain-joined-manage.md)
* [Gebruikers autoriseren voor Apache Ambari](hdinsight-authorize-users-to-ambari.md)