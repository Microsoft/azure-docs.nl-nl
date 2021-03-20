---
title: Gegevens beveiligen die zijn opgeslagen in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Meer informatie over het beveiligen van gegevens in Azure Data Lake Storage Gen1 met behulp van groepen en toegangs beheer lijsten
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: ac7666f4c4e68d24499f9c097dc9bd021d270355
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370692"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Gegevens beveiligen die zijn opgeslagen in Azure Data Lake Storage Gen1
Het beveiligen van gegevens in Azure Data Lake Storage Gen1 is een benadering van drie stappen.  Zowel op rollen gebaseerd toegangs beheer (Azure RBAC) en toegangs beheer lijsten (Acl's) van Azure moeten worden ingesteld om toegang tot gegevens voor gebruikers en beveiligings groepen volledig in te scha kelen.

1. Begin met het maken van beveiligings groepen in Azure Active Directory (Azure AD). Deze beveiligings groepen worden gebruikt voor het implementeren van op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure in de Azure Portal. Zie [Azure RBAC](../role-based-access-control/role-assignments-portal.md)voor meer informatie.
2. Wijs de Azure AD-beveiligings groepen toe aan het Data Lake Storage Gen1-account. Hiermee beheert u de toegang tot het Data Lake Storage Gen1-account vanuit de portal-en beheer bewerkingen van de portal of Api's.
3. Wijs de Azure AD-beveiligings groepen toe als Acl's (toegangs beheer lijsten) op het Data Lake Storage Gen1 bestands systeem.
4. Daarnaast kunt u ook een IP-adres bereik instellen voor clients die toegang hebben tot de gegevens in Data Lake Storage Gen1.

In dit artikel vindt u instructies voor het gebruik van de Azure Portal om de bovenstaande taken uit te voeren. Zie [beveiliging in azure data Lake Storage gen1](data-lake-store-security-overview.md)voor gedetailleerde informatie over de wijze waarop data Lake Storage gen1 beveiliging implementeert op het niveau van account en gegevens. Zie [overzicht van Access Control in data Lake Storage gen1](data-lake-store-access-control.md)voor uitgebreide informatie over de implementatie van acl's in data Lake Storage gen1.

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.

## <a name="create-security-groups-in-azure-active-directory"></a>Beveiligings groepen maken in Azure Active Directory
Zie [beveiligings groepen beheren in azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor instructies over het maken van Azure AD-beveiligings groepen en het toevoegen van gebruikers aan de groep.

> [!NOTE] 
> U kunt zowel gebruikers als andere groepen toevoegen aan een groep in azure AD met behulp van de Azure Portal. Als u een Service-Principal wilt toevoegen aan een groep, gebruikt u echter [de Power shell-module van Azure AD](../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Gebruikers of beveiligings groepen toewijzen aan Data Lake Storage Gen1 accounts
Wanneer u gebruikers of beveiligings groepen toewijst aan Data Lake Storage Gen1 accounts, beheert u de toegang tot de beheer bewerkingen op het account met behulp van de Azure Portal-en Azure Resource Manager-Api's. 

1. Open een Data Lake Storage Gen1-account. Klik in het linkerdeel venster op **alle resources** en klik vervolgens op de Blade alle resources op de account naam waaraan u een gebruiker of beveiligings groep wilt toewijzen.

2. Klik op de Blade Data Lake Storage Gen1 account op **Access Control (IAM)**. In de Blade worden standaard de abonnements eigenaren weer gegeven als de eigenaar.
   
    ![Beveiligings groep toewijzen aan Azure Data Lake Storage Gen1 account](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Beveiligings groep toewijzen aan Azure Data Lake Storage Gen1 account")

3. Klik op de Blade **Access Control (IAM)** op **toevoegen** om de Blade **machtigingen toevoegen** te openen. Selecteer op de Blade **machtigingen toevoegen** een **rol** voor de gebruiker/groep. Zoek naar de beveiligings groep die u eerder hebt gemaakt in Azure Active Directory en selecteer deze. Als u een groot aantal gebruikers en groepen wilt doorzoeken, gebruikt u het tekstvak **selecteren** om op de groeps naam te filteren. 
   
    ![Een rol voor de gebruiker toevoegen](./media/data-lake-store-secure-data/adl.add.user.1.png "Een rol voor de gebruiker toevoegen")
   
    De rol **eigenaar** en **Inzender** bieden toegang tot diverse beheer functies voor het data Lake-account. Voor gebruikers die met gegevens in het data Lake werken, maar wel account beheer gegevens moeten weer geven, kunt u ze toevoegen aan de rol van **lezer** . Het bereik van deze rollen is beperkt tot de beheer bewerkingen die betrekking hebben op het Data Lake Storage Gen1-account.
   
    Voor gegevens bewerkingen worden de afzonderlijke bestandssysteem machtigingen gedefinieerd wat gebruikers kunnen doen. Daarom kan een gebruiker met een rol van lezer alleen de beheer instellingen weer geven die zijn gekoppeld aan het account, maar kunnen ze gegevens lezen en schrijven op basis van bestandssysteem machtigingen die eraan zijn toegewezen. Data Lake Storage Gen1 machtigingen voor bestands systemen worden beschreven in [beveiligings groep als Acl's toewijzen aan het Azure data Lake Storage gen1 bestands systeem](#filepermissions).

    > [!IMPORTANT]
    > Alleen de rol **eigenaar** maakt automatisch toegang tot het bestands systeem mogelijk. De **Inzender**, **lezer** en alle andere rollen hebben acl's nodig om elk niveau van toegang tot mappen en bestanden in te scha kelen.  De rol van **eigenaar** biedt bestands-en mapmachtigingen voor Super gebruikers die niet kunnen worden overschreven via acl's. Zie voor meer informatie over hoe Azure RBAC-beleid wordt toegewezen aan gegevens toegang [Azure RBAC voor account beheer](data-lake-store-security-overview.md#azure-rbac-for-account-management).

4. Als u een groep/gebruiker wilt toevoegen die niet wordt weer gegeven op de Blade **machtigingen toevoegen** , kunt u ze uitnodigen door hun e-mail adres te typen in het tekstvak **selecteren** en deze vervolgens te selecteren in de lijst.
   
    ![Een beveiligings groep toevoegen](./media/data-lake-store-secure-data/adl.add.user.2.png "Een beveiligings groep toevoegen")
   
5. Klik op **Opslaan**. U ziet nu de beveiligings groep die u hebt toegevoegd, zoals hieronder wordt weer gegeven.
   
    ![Beveiligings groep toegevoegd](./media/data-lake-store-secure-data/adl.add.user.3.png "Beveiligings groep toegevoegd")

6. Uw gebruiker/beveiligings groep heeft nu toegang tot het Data Lake Storage Gen1-account. Als u toegang wilt verlenen aan specifieke gebruikers, kunt u deze toevoegen aan de beveiligings groep. Als u de toegang voor een gebruiker wilt intrekken, kunt u deze uit de beveiligings groep verwijderen. U kunt ook meerdere beveiligings groepen toewijzen aan een account. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Gebruikers of beveiligings groepen als Acl's toewijzen aan het Data Lake Storage Gen1 bestands systeem
Door gebruikers/beveiligings groepen toe te wijzen aan het Data Lake Storage Gen1 bestands systeem, stelt u toegangs beheer in voor de gegevens die zijn opgeslagen in Data Lake Storage Gen1.

1. Klik op de Blade Data Lake Storage Gen1 account op **Data Explorer**.
   
    ![Gegevens weer geven via Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Gegevens weer geven via Data Explorer")
2. Klik op de Blade **Data Explorer** op de map waarvoor u de ACL wilt configureren en klik vervolgens op **toegang**. Als u Acl's wilt toewijzen aan een bestand, moet u eerst op het bestand klikken om het te bekijken en vervolgens op **toegang** klikken op de Blade **voor beeld van bestand** .
   
    ![Acl's op Data Lake Storage Gen1 bestands systeem instellen](./media/data-lake-store-secure-data/adl.acl.1.png "Acl's op Data Lake Storage Gen1 bestands systeem instellen")
3. Op de Blade **toegang** worden de eigen aren en toegewezen machtigingen vermeld die al aan de root zijn toegewezen. Klik op het pictogram **toevoegen** om extra toegangs-acl's toe te voegen.
    > [!IMPORTANT]
    > Het instellen van toegangs machtigingen voor één bestand geeft niet noodzakelijkerwijs een gebruiker/groep toegang tot het bestand. Het pad naar het bestand moet toegankelijk zijn voor de toegewezen gebruiker/groep. Zie [algemene scenario's met betrekking tot machtigingen](data-lake-store-access-control.md#common-scenarios-related-to-permissions)voor meer informatie en voor beelden.
   
    ![Standaard-en aangepaste toegang weer geven](./media/data-lake-store-secure-data/adl.acl.2.png "Standaard-en aangepaste toegang weer geven")
   
   * De **eigen aren** en **iedereen** hebben toegang tot UNIX-stijl, waarbij u lezen, schrijven, uitvoeren (LSU) opgeeft voor drie afzonderlijke gebruikers klassen: eigenaar, groep en anderen.
   * **Toegewezen machtigingen** komen overeen met de POSIX-acl's waarmee u machtigingen kunt instellen voor specifieke benoemde gebruikers of groepen buiten de eigenaar of groep van het bestand. 
     
     Zie voor meer informatie [HDFS acl's](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Zie [Access Control in data Lake Storage gen1](data-lake-store-access-control.md)voor meer informatie over de implementatie van acl's in data Lake Storage gen1.
4. Klik op het pictogram **toevoegen** om de Blade **machtigingen toewijzen** te openen. Klik in deze Blade op **gebruiker of groep selecteren** en zoek in de Blade **gebruiker of groep** naar de beveiligings groep die u eerder hebt gemaakt in azure Active Directory. Als u veel groepen wilt doorzoeken, gebruikt u het tekstvak bovenaan om de groeps naam te filteren. Klik op de groep die u wilt toevoegen en klik vervolgens op **selecteren**.
   
    ![Een groep toevoegen](./media/data-lake-store-secure-data/adl.acl.3.png "Een groep toevoegen")
5. Klik op **machtigingen selecteren**, selecteer de machtigingen, of de machtigingen moeten worden toegepast op recursief en of u de machtigingen wilt toewijzen als toegangs-ACL, standaard-ACL of beide. Klik op **OK**.
   
    ![Scherm afbeelding van de Blade machtigingen toewijzen met de optie machtigingen selecteren en de Blade machtigingen selecteren met de optie OK.](./media/data-lake-store-secure-data/adl.acl.4.png "Machtigingen toewijzen aan groep")
   
    Zie [Access Control in data Lake Storage gen1](data-lake-store-access-control.md)voor meer informatie over machtigingen in data Lake Storage gen1 en standaard-acl's.
6. Nadat u op **OK** hebt geklikt op de Blade **machtigingen selecteren** , worden de zojuist toegevoegde groep en de bijbehorende machtigingen nu weer gegeven op de Blade **toegang** .
   
    ![Scherm afbeelding van de Access-Blade met de optie voor gegevens techniek genoemd.](./media/data-lake-store-secure-data/adl.acl.5.png "Machtigingen toewijzen aan groep")
   
   > [!IMPORTANT]
   > In de huidige versie kunt u Maxi maal 28 vermeldingen onder **toegewezen machtigingen** hebben. Als u meer dan 28 gebruikers wilt toevoegen, moet u beveiligings groepen maken, gebruikers toevoegen aan beveiligings groepen en toegang bieden tot deze beveiligings groepen voor het Data Lake Storage Gen1-account.
   > 
   > 
7. Indien nodig kunt u ook de toegangs machtigingen wijzigen nadat u de groep hebt toegevoegd. Schakel het selectie vakje voor elk type machtiging (lezen, schrijven, uitvoeren) in of uit, op basis van het feit of u die machtiging wilt verwijderen of toewijzen aan de beveiligings groep. Klik op **Opslaan** om de wijzigingen op te slaan of op **verwijderen** om de wijzigingen ongedaan te maken.

## <a name="set-ip-address-range-for-data-access"></a>IP-adres bereik instellen voor toegang tot gegevens
Met Data Lake Storage Gen1 kunt u de toegang tot uw gegevens archief op netwerk niveau verder vergren delen. U kunt de firewall inschakelen, een IP-adres opgeven of een IP-adres bereik definiëren voor uw vertrouwde clients. Zodra deze functie is ingeschakeld, kunnen alleen clients met de IP-adressen binnen het gedefinieerde bereik verbinding maken met de Store.

![Firewall instellingen en IP-toegang](./media/data-lake-store-secure-data/firewall-ip-access.png "Firewall instellingen en IP-adres")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Beveiligings groepen voor een Data Lake Storage Gen1 account verwijderen
Wanneer u beveiligings groepen uit Data Lake Storage Gen1 accounts verwijdert, wijzigt u alleen de toegang tot de beheer bewerkingen op het account met behulp van de Azure Portal-en Azure Resource Manager-Api's.  

Toegang tot gegevens is ongewijzigd en wordt nog steeds beheerd door de toegangs-Acl's.  De uitzonde ring hierop zijn gebruikers/groepen in de rol eigen aren.  Gebruikers/groepen die zijn verwijderd uit de rol eigen aren zijn niet langer super gebruikers en hun toegang tot de toegangs beheer lijst instellingen wordt hersteld. 

1. Klik op de Blade Data Lake Storage Gen1 account op **Access Control (IAM)**. 
   
    ![Beveiligings groep toewijzen aan Data Lake Storage Gen1 account](./media/data-lake-store-secure-data/adl.select.user.icon.png "Beveiligings groep toewijzen aan Data Lake Storage Gen1 account")
2. Klik op de Blade **Access Control (IAM)** op de beveiligings groep (en) die u wilt verwijderen. Klik op **Verwijderen**.
   
    ![De beveiligings groep is verwijderd](./media/data-lake-store-secure-data/adl.remove.group.png "De beveiligings groep is verwijderd")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Acl's van beveiligings groepen verwijderen uit een Data Lake Storage Gen1 bestands systeem
Wanneer u de Acl's van beveiligings groepen uit een Data Lake Storage Gen1 bestands systeem verwijdert, wijzigt u de toegang tot de gegevens in het Data Lake Storage Gen1-account.

1. Klik op de Blade Data Lake Storage Gen1 account op **Data Explorer**.
   
    ![Mappen maken in Data Lake Storage Gen1-account](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Mappen maken in Data Lake Storage Gen1-account")
2. Klik op de Blade **Data Explorer** op de map waarvoor u de ACL wilt verwijderen en klik vervolgens op **toegang**. Als u Acl's voor een bestand wilt verwijderen, moet u eerst op het bestand klikken om het te bekijken en vervolgens op **toegang** klikken op de Blade **voor beeld van bestand** . 
   
    ![Acl's op Data Lake Storage Gen1 bestands systeem instellen](./media/data-lake-store-secure-data/adl.acl.1.png "Acl's op Data Lake Storage Gen1 bestands systeem instellen")
3. Klik op de Blade **toegang** op de beveiligings groep die u wilt verwijderen. Klik op de Blade **toegangs gegevens** op **verwijderen**.
   
    ![Scherm opname van de Access-Blade met de optie voor gegevens techniek en de Blade toegangs Details met de optie verwijderen.](./media/data-lake-store-secure-data/adl.remove.acl.png "Machtigingen toewijzen aan groep")

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aan de slag met Data Lake Storage Gen1 met behulp van PowerShell](data-lake-store-get-started-powershell.md)
* [Aan de slag met Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Diagnostische logboeken openen voor Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)
