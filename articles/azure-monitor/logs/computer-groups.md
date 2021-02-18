---
title: Computer groepen in Azure Monitor-logboek query's | Microsoft Docs
description: Met computer groepen in Azure Monitor kunt u logboek query's op een bepaalde set computers bereiken.  In dit artikel worden de verschillende methoden beschreven die u kunt gebruiken voor het maken van computer groepen en het gebruik ervan in een logboek query.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: 92603165ac399415ec4fb6daeea1641065671a83
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611367"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Computer groepen in Azure Monitor-logboek query's
Met computer groepen in Azure Monitor kunt u [logboek query's](../log-query/log-query-overview.md) op een bepaalde set computers bereiken.  Elke groep wordt gevuld met computers met behulp van een query die u definieert of door groepen te importeren uit verschillende bronnen.  Wanneer de groep is opgenomen in een logboek query, zijn de resultaten beperkt tot records die overeenkomen met de computers in de groep.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Een computer groep maken
U kunt een computer groep maken in Azure Monitor met behulp van een van de methoden in de volgende tabel.  In de volgende secties vindt u meer informatie over elke methode. 

| Methode | Beschrijving |
|:--- |:--- |
| Logboekquery |Een logboek query maken waarmee een lijst met computers wordt geretourneerd. |
| API voor zoeken in logboeken |Gebruik de API voor zoeken in Logboeken om programmatisch een computer groep te maken op basis van de resultaten van een logboek query. |
| Active Directory |Automatisch het groepslid maatschap scannen van alle agent computers die lid zijn van een Active Directory domein en een groep in Azure Monitor maken voor elke beveiligings groep. (Alleen Windows-computers)|
| Configuration Manager | Importeer verzamelingen van micro soft endpoint Configuration Manager en maak een groep in Azure Monitor voor elk. |
| Windows Server Update Services |Automatisch WSUS-servers of-clients scannen voor doel groepen en een groep maken in Azure Monitor voor elk. |

### <a name="log-query"></a>Logboekquery
Computer groepen die zijn gemaakt op basis van een logboek query bevatten alle computers die worden geretourneerd door een query die u definieert.  Deze query wordt uitgevoerd telkens wanneer de computer groep wordt gebruikt, zodat alle wijzigingen die zijn aangebracht sinds de groep is gemaakt, worden weer gegeven.  

U kunt elke query voor een computer groep gebruiken, maar deze moet een afzonderlijke set computers retour neren met behulp van `distinct Computer` .  Hier volgt een typisch voor beeld van een query die u als computer groep zou kunnen gebruiken.

```kusto
Heartbeat | where Computer contains "srv" | distinct Computer
```

Gebruik de volgende procedure om een computer groep te maken op basis van een zoek opdracht in Logboeken in de Azure Portal.

1. Klik in het menu **Azure monitor** van de Azure Portal op **Logboeken** .
1. Maak een query en voer deze uit om de computers te retour neren die u in de groep wilt gebruiken.
1. Klik boven aan het scherm op **Opslaan** .
1. Wijzig **Opslaan als** in **functie** en selecteer **deze query opslaan als een computer groep**.
1. Geef waarden op voor elke eigenschap voor de computer groep die in de tabel wordt beschreven en klik op **Opslaan**.

In de volgende tabel worden de eigenschappen beschreven die een computer groep definiëren.

| Eigenschap | Beschrijving |
|:---|:---|
| Name   | De naam van de query die moet worden weer gegeven in de portal. |
| Functiealias | Een unieke alias die wordt gebruikt om de computer groep in een query te identificeren. |
| Categorie       | Categorie voor het ordenen van de query's in de portal. |


### <a name="active-directory"></a>Active Directory
Wanneer u Azure Monitor configureert om Active Directory groepslid maatschappen te importeren, analyseert het groepslid maatschap van alle Windows-domein computers die zijn toegevoegd aan de Log Analytics-agent.  Er wordt een computer groep gemaakt in Azure Monitor voor elke beveiligings groep in Active Directory en elke Windows-computer wordt toegevoegd aan de computer groepen die zijn gekoppeld aan de beveiligings groepen waarvan ze lid zijn.  Dit lidmaatschap wordt elke 4 uur voortdurend bijgewerkt.  

> [!NOTE]
> Geïmporteerde Active Directory groepen bevatten alleen Windows-machines.

U configureert Azure Monitor om Active Directory beveiligings groepen te importeren uit **Geavanceerde instellingen** in uw log Analytics-werk ruimte in de Azure Portal.  Selecteer **computer groepen**, **Active Directory** en importeer vervolgens **Active Directory groepslid maatschappen van computers**.  Er is geen verdere configuratie nodig.

![Computer groepen van Active Directory](media/computer-groups/configure-activedirectory.png)

Wanneer groepen zijn geïmporteerd, wordt in het menu het aantal computers weer gegeven waarvoor groepslid maatschap is gedetecteerd en het aantal geïmporteerde groepen.  U kunt op een van deze koppelingen klikken om de **ComputerGroup** -records met deze gegevens te retour neren.

### <a name="windows-server-update-service"></a>Windows Server updateservice
Wanneer u Azure Monitor configureert om WSUS-groepslid maatschappen te importeren, analyseert het het lidmaatschap van de doel groep van alle computers met de Log Analytics agent.  Als u doel items aan de client zijde gebruikt, wordt voor elke computer die is verbonden met Azure Monitor en die deel uitmaakt van een WSUS-doel groep, het groepslid maatschap geïmporteerd in Azure Monitor. Als u doel items aan de server zijde gebruikt, moet de Log Analytics-agent worden geïnstalleerd op de WSUS-server om de gegevens van het groepslid maatschap te importeren in Azure Monitor.  Dit lidmaatschap wordt elke 4 uur voortdurend bijgewerkt. 

U configureert Azure Monitor voor het importeren van WSUS-groepen uit **Geavanceerde instellingen** in uw log Analytics-werk ruimte in de Azure Portal.  Selecteer **computer groepen**, **WSUS** en vervolgens **WSUS-groepslid maatschappen importeren**.  Er is geen verdere configuratie nodig.

![Computer groepen van WSUS](media/computer-groups/configure-wsus.png)

Wanneer groepen zijn geïmporteerd, wordt in het menu het aantal computers weer gegeven waarvoor groepslid maatschap is gedetecteerd en het aantal geïmporteerde groepen.  U kunt op een van deze koppelingen klikken om de **ComputerGroup** -records met deze gegevens te retour neren.

### <a name="configuration-manager"></a>Configuration Manager
Wanneer u Azure Monitor configureert om Configuration Manager verzamelings lidmaatschappen te importeren, wordt er voor elke verzameling een computer groep gemaakt.  De gegevens van het verzamelings lidmaatschap worden elke drie uur opgehaald om de computer groepen actueel te laten blijven. 

Voordat u Configuration Manager verzamelingen kunt importeren, moet u [Configuration Manager verbinden met Azure monitor](collect-sccm.md).  

![Computer groepen van SCCM](media/computer-groups/configure-sccm.png)

Wanneer verzamelingen zijn geïmporteerd, wordt in het menu het aantal computers weer gegeven waarvoor groepslid maatschap is gedetecteerd en het aantal geïmporteerde groepen.  U kunt op een van deze koppelingen klikken om de **ComputerGroup** -records met deze gegevens te retour neren.

## <a name="managing-computer-groups"></a>Computer groepen beheren
U kunt computer groepen weer geven die zijn gemaakt op basis van een logboek query of de API voor zoeken in Logboeken van **Geavanceerde instellingen** in uw log Analytics-werk ruimte in de Azure Portal.  Selecteer **computer groepen** en vervolgens **opgeslagen groepen**.  

Klik op de **x** in de kolom **verwijderen** om de computer groep te verwijderen.  Klik op het pictogram **leden weer geven** voor een groep om de zoek opdracht van het logboek van de groep uit te voeren die de leden retourneert.  U kunt een computer groep niet wijzigen, maar moet u in plaats daarvan verwijderen en vervolgens opnieuw maken met de gewijzigde instellingen.

![Opgeslagen computer groepen](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Een computer groep gebruiken in een logboek query
U gebruikt een computer groep die is gemaakt op basis van een logboek query in een query door de alias te behandelen als een functie, meestal met de volgende syntaxis:

```kusto
Table | where Computer in (ComputerGroup)
```

U kunt bijvoorbeeld het volgende gebruiken om update Summary-records te retour neren voor alleen computers in een computer groep met de naam mycomputergroup.

```kusto
UpdateSummary | where Computer in (mycomputergroup)
```

Geïmporteerde computer groepen en de bijbehorende computers worden opgeslagen in de tabel **ComputerGroup** .  Met de volgende query wordt bijvoorbeeld een lijst met computers in de groep domein computers uit Active Directory geretourneerd. 

```kusto
ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer
```

De volgende query retourneert update Summary-records voor alleen computers in domein computers.

```kusto
let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
```

## <a name="computer-group-records"></a>Records computer groep
Er wordt een record gemaakt in de werk ruimte Log Analytics voor elk lidmaatschap van de computer groep dat is gemaakt op basis van Active Directory of WSUS.  Deze records hebben het type **ComputerGroup** en hebben de eigenschappen in de volgende tabel.  Er worden geen records gemaakt voor computer groepen op basis van logboek query's.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |De naam van de lidcomputer. |
| `Group` |De naam van de groep. |
| `GroupFullName` |Volledig pad naar de groep met inbegrip van de bron-en bron naam. |
| `GroupSource` |Bron waarvan de groep is verzameld. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |De naam van de bron waaruit de groep is verzameld.  Voor Active Directory is dit de domein naam. |
| `ManagementGroupName` |Naam van de beheergroep voor SCOM-agents.  Voor andere agents is dit AOI-\<workspace ID\> |
| `TimeGenerated` |De datum en tijd waarop de computer groep is gemaakt of bijgewerkt. |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.  

