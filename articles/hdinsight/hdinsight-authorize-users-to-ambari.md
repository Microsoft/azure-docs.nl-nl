---
title: Gebruikers autoriseren voor Ambari-weer gaven-Azure HDInsight
description: Ambari-gebruikers-en groeps machtigingen voor HDInsight-clusters beheren waarvoor ESP is ingeschakeld.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 44f98692131070940514498b6bf648936bea0806
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872231"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Gebruikers machtigen voor Apache Ambari-weergaven

[Enterprise Security Package (ESP) ingeschakelde HDInsight-clusters](./domain-joined/hdinsight-security-overview.md) bieden mogelijkheden op ondernemings niveau, waaronder op Azure Active Directory gebaseerde verificatie. U kunt [nieuwe gebruikers synchroniseren](hdinsight-sync-aad-users-to-cluster.md) die zijn toegevoegd aan Azure ad-groepen die toegang tot het cluster hebben gekregen, zodat deze specifieke gebruikers bepaalde acties kunnen uitvoeren. Werken met gebruikers, groepen en machtigingen in [Apache Ambari](https://ambari.apache.org/) wordt ondersteund voor zowel ESP HDInsight-clusters als standaard HDInsight-clusters.

Active Directory kunnen gebruikers zich met hun domein referenties aanmelden bij de cluster knooppunten. Ze kunnen ook hun domein referenties gebruiken om cluster interacties te verifiëren met andere goedgekeurde eind punten, zoals [tint](https://gethue.com/), Ambari WEERGAVEN, ODBC, JDBC, Power shell en rest api's.

> [!WARNING]  
> Wijzig het wacht woord van de Ambari-watchdog (hdinsightwatchdog) niet in uw HDInsight-cluster op basis van Linux. Als u het wacht woord wijzigt, is het niet meer mogelijk om script acties te gebruiken of om schaal bewerkingen uit te voeren met uw cluster.

Als u dit nog niet hebt gedaan, volgt u [deze instructies](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) om een nieuw ESP-cluster in te richten.

## <a name="access-the-ambari-management-page"></a>Toegang tot de Ambari-beheer pagina

Als u naar de **Ambari-beheer pagina** van de [Apache AMBARI-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)wilt gaan, bladert u naar `https://CLUSTERNAME.azurehdinsight.net` . Voer de gebruikers naam en het wacht woord in van de Cluster beheerder die u hebt gedefinieerd bij het maken van het cluster. Selecteer vervolgens in het Ambari-dash board de optie **Ambari beheren** onder het menu **beheerder** :

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png" alt-text="Apache Ambari-dash board beheren":::

## <a name="add-users"></a>Gebruikers toevoegen

### <a name="add-users-through-the-portal"></a>Gebruikers toevoegen via de portal

1. Selecteer op de pagina beheer de optie **gebruikers**.

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png" alt-text="Gebruikers van Apache Ambari-beheer pagina":::

1. Selecteer **+ lokale gebruiker maken**.

1. Geef een **gebruikers naam** en **wacht woord** op. Selecteer **Opslaan**.

### <a name="add-users-through-powershell"></a>Gebruikers toevoegen via Power shell

Bewerk de onderstaande variabelen door te vervangen `CLUSTERNAME` , `NEWUSER` en `PASSWORD` met de juiste waarden.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Gebruikers toevoegen via krul

Bewerk de onderstaande variabelen door te vervangen `CLUSTERNAME` , `ADMINPASSWORD` , `NEWUSER` en `USERPASSWORD` met de juiste waarden. Het script is ontworpen om te worden uitgevoerd met bash. Er zijn kleine wijzigingen nodig voor een Windows-opdracht prompt.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Machtigingen verlenen voor het Apache Hive weer gaven

Ambari wordt geleverd met View instances voor [Apache Hive](https://hive.apache.org/) en [Apache TEZ](https://tez.apache.org/), onder andere. Ga naar de **beheer pagina** van het Ambari om toegang te verlenen aan een of meer Hive-exemplaren.

1. Selecteer op de pagina beheer de koppeling **weer gaven** in de kop van het menu **weer gaven** aan de linkerkant.

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png" alt-text="Koppelingen voor weer gave Apache Ambari-weer gaven":::

2. Vouw op de pagina weer gaven de **Hive** -rij uit. Er is één standaard Hive-weer gave die wordt gemaakt wanneer de component service aan het cluster wordt toegevoegd. U kunt indien nodig ook meer Hive-weergave-instanties maken. Een Hive-weer gave selecteren:

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png" alt-text="HDInsight-weer gaven-Apache Hive weer gave":::

3. Schuif naar de onderkant van de pagina weer geven. In het gedeelte *machtigingen* hebt u twee opties voor het verlenen van domein gebruikers hun machtigingen voor de weer gave:

**Machtigingen verlenen aan deze gebruikers** :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png" alt-text="machtigingen verlenen aan deze gebruikers":::

**Machtigingen verlenen aan deze groepen** :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png" alt-text="machtiging verlenen aan deze groepen":::

1. Als u een gebruiker wilt toevoegen, selecteert u de knop **gebruiker toevoegen** .

   * Begin met het typen van de gebruikers naam en er wordt een vervolg keuzelijst met eerder gedefinieerde namen weer geven.

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png" alt-text="Apache Ambari-gebruiker automatisch aanvullen":::

   * Selecteer of voltooi de gebruikers naam. Selecteer de knop **Nieuw** om deze gebruikers naam toe te voegen als een nieuwe gebruiker.

   * Schakel het **selectie vakje blauw** in om uw wijzigingen op te slaan.

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png" alt-text="Apache Ambari gebruikers machtigingen verlenen":::

1. Selecteer de knop **groep toevoegen** om een groep toe te voegen.

   * Begin met het typen van de groeps naam. Het proces van het selecteren van een bestaande groeps naam of het toevoegen van een nieuwe groep is hetzelfde als voor het toevoegen van gebruikers.
   * Schakel het **selectie vakje blauw** in om uw wijzigingen op te slaan.

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png" alt-text="Apache Ambari Grant-machtigingen":::

Het rechtstreeks toevoegen van gebruikers aan een weer gave is handig wanneer u machtigingen wilt toewijzen aan een gebruiker om die weer gave te gebruiken, maar niet wilt dat ze lid zijn van een groep die extra machtigingen heeft. Om de hoeveelheid administratieve overhead te verminderen, is het eenvoudiger om machtigingen toe te wijzen aan groepen.

## <a name="grant-permissions-to-apache-tez-views"></a>Machtigingen verlenen voor Apache TEZ-weer gaven

Met de [Apache TEZ](https://tez.apache.org/) View-instanties kunnen gebruikers alle TEZ-taken bewaken en fouten opsporen, verzonden door [Apache Hive](https://hive.apache.org/) query's en [Apache Pig](https://pig.apache.org/) -scripts. Er is één standaard TEZ weer gave-exemplaar dat wordt gemaakt wanneer het cluster wordt ingericht.

Als u gebruikers en groepen wilt toewijzen aan een instantie van een TEZ-weer gave, vouwt u de rij **TEZ** op de pagina weer gaven uit, zoals eerder is beschreven.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png" alt-text="HDInsight-weer gaven-Apache TEZ View":::

Herhaal de stappen 3-5 in de vorige sectie om gebruikers of groepen toe te voegen.

## <a name="assign-users-to-roles"></a>Gebruikers toewijzen aan rollen

Er zijn vijf beveiligings rollen voor gebruikers en groepen, die worden vermeld in volg orde van het afnemen van toegangs machtigingen:

* Cluster beheerder
* Cluster operator
* Servicebeheerder
* Service operator
* Cluster gebruiker

Als u rollen wilt beheren, gaat u naar de **pagina beheer van Ambari** en selecteert u vervolgens de koppeling **rollen** in de menu groep *clusters* aan de linkerkant.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png" alt-text="Menu koppelingen voor Apache Ambari-rollen":::

Als u de lijst met machtigingen wilt zien die aan elke rol zijn gegeven, klikt u op het blauwe vraag teken naast de **tabelkop tabel op de pagina rollen.**

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png" alt-text="Menu opdracht koppelings machtigingen voor Apache Ambari-rollen":::

Op deze pagina zijn er twee verschillende weer gaven die u kunt gebruiken voor het beheren van rollen voor gebruikers en groepen: blok keren en lijst.

### <a name="block-view"></a>Blok weergave

In de blok weergave wordt elke rol in een eigen rij weer gegeven en worden de rollen **toegewezen aan deze gebruikers** , en **kunnen rollen aan deze groepen worden toegewezen** , zoals eerder beschreven.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png" alt-text="Blok weergave van Apache Ambari-rollen":::

### <a name="list-view"></a>Lijstweergave

De lijst weergave biedt snelle bewerkings mogelijkheden in twee categorieën: gebruikers en groepen.

* De categorie gebruikers van de lijst weergave bevat een lijst met alle gebruikers, zodat u een rol voor elke gebruiker in de vervolg keuzelijst kunt selecteren.

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png" alt-text="Weer gave Apache Ambari-rollen lijst-gebruikers":::

* In de categorie groepen van de lijst weergave worden alle groepen weer gegeven, evenals de rol die aan elke groep is toegewezen. In ons voor beeld wordt de lijst met groepen gesynchroniseerd vanuit de Azure AD-groepen die zijn opgegeven in de eigenschap **gebruikers groep** van het cluster. Zie [een HDInsight-cluster maken waarvoor ESP is ingeschakeld](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png" alt-text="Apache Ambari roles list view-groups":::

    In de bovenstaande afbeelding is de groep ' hiveusers ' toegewezen aan de gebruikersrol *cluster* . Dit is een alleen-lezen rol waarmee de gebruikers van die groep de service configuraties en cluster metrieken kunnen bekijken, maar niet wijzigen.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Meld u aan bij Ambari als alleen-lezen gebruiker

We hebben onze machtigingen voor de Azure AD-domein gebruiker ' hiveuser1 ' toegewezen aan Hive-en TEZ-weer gaven. Wanneer we de Ambari-webgebruikersinterface starten en de domein referenties van deze gebruiker invoeren (Azure AD-gebruikers naam in e-mail indeling en wacht woord), wordt de gebruiker omgeleid naar de pagina Ambari weergaven. Hier kan de gebruiker elke toegankelijke weer gave selecteren. De gebruiker kan geen ander deel van de site bezoeken, met inbegrip van het dash board, de services, hosts, waarschuwingen of de beheer pagina's.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png" alt-text="Apache Ambari-gebruiker met alleen weer gaven":::

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Meld u aan bij Ambari als een cluster gebruiker

We hebben onze Azure AD-domein gebruiker ' hiveuser2 ' toegewezen aan de gebruikersrol *cluster* . Deze rol kan toegang krijgen tot het dash board en alle menu-items. Een cluster gebruiker heeft minder toegestane opties dan een beheerder. Hiveuser2 kan bijvoorbeeld configuraties voor elk van de services weer geven, maar niet bewerken.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png" alt-text="Apache Ambari-dashboard weergave":::

## <a name="next-steps"></a>Volgende stappen

* [Apache Hive beleid in HDInsight configureren met ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight-clusters beheren](./domain-joined/apache-domain-joined-manage.md)
* [De weer gave Apache Hive gebruiken met Apache Hadoop in HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Azure AD-gebruikers synchroniseren met het cluster](hdinsight-sync-aad-users-to-cluster.md)
* [HDInsight-clusters beheren met behulp van de Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)