---
title: Gebruikers toevoegen aan een Azure Data Lake Analytics-account
description: Meer informatie over het correct toevoegen van gebruikers aan uw Data Lake Analytics-account met behulp van de wizard gebruiker toevoegen en Azure PowerShell.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: c04b1bbd62e156aeb8d3a0ebb244cfbc753dec52
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020820"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Een gebruiker toevoegen in de Azure-portal

## <a name="start-the-add-user-wizard"></a>De wizard gebruiker toevoegen starten
1. Open uw Azure Data Lake Analytics via https://portal.azure.com .
2. Klik op **wizard gebruiker toevoegen**.
3. Zoek in de stap **gebruiker selecteren** de gebruiker die u wilt toevoegen. Klik op **Selecteren**.
4. de stap **functie selecteren** , kies **Data Lake Analytics ontwikkel aars**. Deze rol heeft de minimale set machtigingen die nodig zijn om U-SQL-taken te verzenden/controleren/beheren. Toewijzen aan deze rol als de groep niet is bedoeld voor het beheren van Azure-Services.
5. Selecteer in de stap **machtigingen voor catalogus selecteren** de aanvullende data bases waartoe de gebruiker toegang moet hebben. Lees-en schrijf toegang tot de standaard statische data base met de naam ' Master ' is vereist voor het verzenden van taken. Wanneer u klaar bent, klikt u op **OK**.
6. Bekijk in de laatste stap de naam **Assign selected permissions** de wijzigingen die door de wizard worden aangebracht. Klik op **OK**.


## <a name="configure-acls-for-data-folders"></a>Acl's voor gegevens mappen configureren
Ken ' R-X ' of ' LSU ', indien nodig, toe aan mappen met invoer gegevens en uitvoer gegevens.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>U kunt de gebruiker eventueel toevoegen aan **de rol van Azure data Lake Storage gen1 rol.**
1.  Zoek uw Azure Data Lake Storage Gen1-account.
2.  Klik op **Gebruikers**.
3. Klik op **Add**.
4.  Selecteer een Azure-rol om deze groep toe te wijzen.
5.  Toewijzen aan rol van lezer. Deze rol heeft de minimale set machtigingen die zijn vereist voor het bladeren door en het beheren van gegevens die zijn opgeslagen in ADLSGen1. Toewijzen aan deze rol als de groep niet is bedoeld voor het beheren van Azure-Services.
6.  Typ de naam van de groep.
7.  Klik op **OK**.

## <a name="adding-a-user-using-powershell"></a>Een gebruiker toevoegen met Power shell

1. Volg de instructies in deze hand leiding: een [Azure PowerShell installeren en configureren](/powershell/azure/).
2. Down load het Power shell-script voor [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) .
3. Voer het Power shell-script uit. 

De voorbeeld opdracht om gebruikers toegang te geven tot het verzenden van taken, het weer geven van de meta gegevens van nieuwe taken en het weer geven van oude meta gegevens is:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Data Lake Analytics](data-lake-analytics-overview.md)
* [Aan de slag met Data Lake Analytics met behulp van de Azure Portal](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
