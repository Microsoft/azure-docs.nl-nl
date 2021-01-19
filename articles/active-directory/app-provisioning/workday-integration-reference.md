---
title: Naslag informatie over de integratie van Azure Active Directory en workday
description: Technisch diep gaande kennis van het inrichten van werk dagen op basis van werk uren
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: 251e1d4249373ec52afb3d7edaa2325c992b66f1
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570158"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Hoe Azure Active Directory inrichting integreert met workday

[Azure Active Directory User Provisioning Service](../app-provisioning/user-provisioning.md) kan worden geïntegreerd met [workday HCM](https://www.workday.com) om de identiteits levenscyclus van gebruikers te beheren. Azure Active Directory biedt drie vooraf samengestelde integraties: 

* [Werk dagen voor een on-premises Active Directory gebruikers inrichten](../saas-apps/workday-inbound-tutorial.md)
* [Workday Azure Active Directory gebruikers inrichten](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)

In dit artikel wordt uitgelegd hoe de integratie werkt en hoe u het inrichtings gedrag kunt aanpassen voor verschillende HR-scenario's. 

## <a name="establishing-connectivity"></a>Verbinding maken 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Beperken van workday API-toegang tot Azure AD-eind punten
De Azure AD-inrichtings service maakt gebruik van basis verificatie om verbinding te maken met de Web Services API-eind punten van workday.  

Als u de connectiviteit tussen Azure AD Provisioning Service en workday verder wilt beveiligen, kunt u de toegang beperken, zodat de aangewezen integratie systeem gebruiker alleen toegang heeft tot de workday-Api's vanuit de toegestane Azure AD IP-adresbereiken. Stel uw werkdag beheerder in om de volgende configuratie in uw workday-Tenant te volt ooien. 

1. Down load de [meest recente IP-bereiken](https://www.microsoft.com/download/details.aspx?id=56519) voor de open bare Azure-Cloud. 
1. Open het bestand en zoek naar tag **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP-bereik](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Kopieer alle IP-adresbereiken die worden vermeld in het element *addressPrefixes* en gebruik het bereik om uw IP-adres lijst samen te stellen.
1. Meld u aan bij de workday-beheer Portal. 
1. Open de taak **IP-bereiken onderhouden** om een nieuw IP-adres bereik te maken voor Azure-data centers. Geef de IP-bereiken (met behulp van CIDR-notatie) op als een door komma's gescheiden lijst.  
1. Open de taak **verificatie beleid beheren** om een nieuw verificatie beleid te maken. Gebruik in het verificatie beleid **verificatie white list** om het IP-adres bereik van Azure AD op te geven en de beveiligings groep waartoe toegang wordt verleend vanuit dit IP-bereik. Sla de wijzigingen op. 
1. Open de taak **alle wachtende verificatie beleids wijzigingen activeren** om de wijzigingen te bevestigen.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>De toegang tot werk gegevens in workday beperken met behulp van beperkte beveiligings groepen

De standaard stappen voor [het configureren van de workday-integratie systeem gebruiker](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) verleent toegang tot het ophalen van alle gebruikers in uw workday-Tenant. In bepaalde integratie scenario's is het mogelijk dat u de toegang wilt beperken, zodat gebruikers die deel uitmaken van bepaalde organisaties van toezicht, worden geretourneerd door de Get_Workers-API-aanroep en worden verwerkt door de werkdag Azure AD connector. 

U kunt aan deze vereiste voldoen door te werken met uw werkdag-beheerder en beveiligings groepen met beperkte integratie te configureren. Raadpleeg voor meer informatie over hoe dit wordt gedaan, het [Community-artikel van de](https://community.workday.com/forums/customer-questions/620393) werkdag (workday-community-*aanmeldings referenties zijn vereist voor toegang tot dit artikel*)

Deze strategie voor het beperken van toegang met beperkte ISSG (beveiligings groepen voor integratie systeem) is met name handig in de volgende scenario's: 
* **Scenario met gefaseerde implementatie**: u hebt een grote werkdag Tenant en u bent van plan een gefaseerde implementatie van de werkdag uit te voeren voor automatische inrichting van Azure AD. In dit scenario wordt het aanbevolen om beperkte ISSG te configureren in plaats van gebruikers die niet binnen het bereik van de huidige fase vallen met Azure AD-bereik filters, zodat alleen in-Scope werk rollen zichtbaar zijn voor Azure AD.
* **Scenario voor meerdere inrichtings taken**: u hebt een grote werkdag en meerdere AD-domeinen die elk een andere bedrijfs eenheid/divisie/bedrijf ondersteunen. Ter ondersteuning van deze topologie zou u meerdere werk dagen wilt uitvoeren voor Azure AD-inrichtings taken met elke taak die een specifieke set met gebruikers inricht. In dit scenario kunt u in plaats van het gebruik van Azure AD-scoping filters om werknemers gegevens uit te sluiten, het configureren van beperkte ISSG, zodat alleen de relevante werk gegevens zichtbaar zijn voor Azure AD. 

### <a name="workday-test-connection-query"></a>Test verbindings query voor workday

Voor het testen van de connectiviteit met workday verzendt Azure AD de volgende *Get_Workers* -aanvraag voor workday-webservices. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Hoe volledige synchronisatie werkt

**Volledige synchronisatie** in de context van op werk dagen gerichte inrichting verwijst naar het proces van het ophalen van alle identiteiten uit workday en het bepalen van de inrichtings regels die moeten worden toegepast op elk worker-object. Volledige synchronisatie vindt plaats wanneer u het inrichten voor de eerste keer inschakelt en ook wanneer u de *inrichting opnieuw opstart* vanuit de Azure portal of met Graph api's. 

Azure AD verzendt de volgende *Get_Workers* workday-webservices aanvragen om gegevens van werk nemers op te halen. De query zoekt naar het transactie logboek van de werkdag op alle effectief gedateerde werk items vanaf het tijdstip dat overeenkomt met de volledige synchronisatie. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
Het knoop punt *Response_Group* wordt gebruikt om op te geven welke werk attributen van workday moeten worden opgehaald. Raadpleeg de [documentatie bij Workday GET_WORKERS API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)voor een beschrijving van elke vlag in het *Response_Group* knooppunt. 

Bepaalde vlag waarden die zijn opgegeven in het *Response_Group* knoop punt, worden berekend op basis van de kenmerken die zijn geconfigureerd in de WORKDAY Azure AD-inrichtings toepassing. Raadpleeg de sectie over *ondersteunde entiteiten* voor de criteria die zijn gebruikt om de vlag waarden in te stellen. 

Het *Get_Workers* antwoord van workday voor de bovenstaande query bevat het aantal werknemerregistraties en het aantal pagina's.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Om de volgende pagina van de resultatenset op te halen, geeft de volgende *Get_Workers* query het pagina nummer op als een para meter in de *Response_Filter*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
De Azure AD-inrichtings service verwerkt elke pagina en doorloopt de alle effectief werkende werk rollen tijdens volledige synchronisatie. Voor elk werk item dat is geïmporteerd uit workday:
* De [XPath-expressie](workday-attribute-reference.md) wordt toegepast om kenmerk waarden op te halen uit workday.
* De kenmerk toewijzing en de overeenkomende regels worden toegepast en 
* De service bepaalt welke bewerking moet worden uitgevoerd in het doel (Azure AD/AD). 

Zodra de verwerking is voltooid, wordt de tijds tempel die is gekoppeld aan het begin van de volledige synchronisatie als een water merk opgeslagen. Dit water merk fungeert als uitgangs punt voor de incrementele synchronisatie cyclus. 

## <a name="how-incremental-sync-works"></a>Hoe incrementele synchronisatie werkt

Na een volledige synchronisatie wordt de Azure AD-inrichtings service onderhouden `LastExecutionTimestamp` en gebruikt om Delta query's te maken om incrementele wijzigingen op te halen. Tijdens incrementele synchronisatie verzendt Azure AD de volgende typen query's naar workday: 

* [Query's uitvoeren op hand matige updates](#query-for-manual-updates)
* [Query's uitvoeren op effectief gedateerde updates en beëindigingen](#query-for-effective-dated-updates-and-terminations)
* [Query voor toekomstige huur](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Query's uitvoeren op hand matige updates

De volgende *Get_Workers* vragen om query's voor hand matige updates die zijn opgetreden tussen de laatste uitvoering en de huidige uitvoerings tijd. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Query's uitvoeren op effectief gedateerde updates en beëindigingen

De volgende *Get_Workers* vragen om query's voor effectief gedateerde updates die zijn opgetreden tussen de laatste uitvoering en de huidige uitvoerings tijd. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Query voor toekomstige huur

Als een van de bovenstaande query's een toekomstig huur retourneert, wordt de volgende *Get_Workers* aanvraag gebruikt om informatie op te halen over een nieuwe huur bewerking in de toekomst. Het *wid* -kenmerk van het nieuwe huur proces wordt gebruikt om de zoek opdracht uit te voeren en de ingangs datum wordt ingesteld op de datum en tijd van het in te stellen. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="retrieving-worker-data-attributes"></a>Kenmerken van worker-gegevens ophalen

De *Get_Workers* -API kan verschillende gegevens sets retour neren die aan een werk nemer zijn gekoppeld. Afhankelijk van de [XPath API-expressies](workday-attribute-reference.md) die zijn geconfigureerd in het inrichtings schema, bepaalt de Azure AD-inrichtings service welke gegevens sets uit workday moeten worden opgehaald. Daarom worden de *Response_Group* -vlaggen ingesteld in de *Get_Workers* -aanvraag. 

De volgende tabel bevat richt lijnen voor het configureren van de configuratie om een specifieke gegevensset op te halen. 

| \# | Workday-entiteit                       | Standaard opgenomen | XPATH-patroon dat moet worden opgegeven in de toewijzing voor het ophalen van niet-standaard entiteiten             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Persoons gegevens                        | Ja                 | Word: worker \_ -gegevens/WD: persoons \_ gegevens                                             |
| 2  | Gegevens van dienst verband                      | Ja                 | Word: worker \_ -gegevens/WD: dienstverband \_ gegevens                                           |
| 3  | Aanvullende taak gegevens                  | Ja                 | Word: worker \_ -gegevens/WD: dienstverband \_ gegevens/WD: werk \_ taak \_ gegevens \[ @wd:Primary \_ taak = 0\]|
| 4  | Organisatie gegevens                    | Ja                 | Word: worker \_ -gegevens/WD: organisatie \_ gegevens                                         |
| 5  | Beheer keten gegevens                | Ja                 | Word: worker \_ -gegevens/WD: beheer \_ keten \_ gegevens                                    |
| 6  | Toezichthoudende organisatie             | Ja                 | College                                                                 |
| 7  | Bedrijf                              | Ja                 | BEDRIJFS                                                                     |
| 8  | Bedrijfsonderdeel                        | Nee                  | BEDRIJFS \_ eenheid                                                              |
| 9  | Hiërarchie van bedrijfs eenheden              | Nee                  | ' BUSINESS \_ Unit- \_ hiërarchie '                                                   |
| 10 | Bedrijfs hiërarchie                    | Nee                  | BEDRIJFS \_ hiërarchie                                                          |
| 11 | Cost Center                          | Nee                  | KOSTEN \_ centrum                                                                |
| 12 | Kostenplaats hiërarchie                | Nee                  | COST \_ Center- \_ hiërarchie                                                     |
| 13 | Beta                                 | Nee                  | Beta                                                                        |
| 14 | Fonds hiërarchie                       | Nee                  | ' fonds \_ hiërarchie '                                                             |
| 15 | Geschenk bonnen                                 | Nee                  | GESCHENK BONNEN                                                                        |
| 16 | Cadeau hiërarchie                       | Nee                  | ' cadeau \_ hiërarchie '                                                             |
| 17 | Verlenen                                | Nee                  | Geef                                                                       |
| 18 | Toekennings hiërarchie                      | Nee                  | GRANT- \_ hiërarchie                                                            |
| 19 | Hiërarchie van zakelijke site              | Nee                  | ' \_ hiërarchie bedrijfs site \_ '                                                   |
| 20 | Matrix organisatie                  | Nee                  | OVERZICHT                                                                      |
| 21 | Betalings groep                            | Nee                  | ' betaal \_ groep '                                                                  |
| 22 | Programma's                             | Nee                  | MA'S                                                                    |
| 23 | Programma hiërarchie                    | Nee                  | ' programma \_ hiërarchie '                                                          |
| 24 | Regio                               | Nee                  | hiërarchie van REGIO'S \_                                                           |
| 25 | Locatie hiërarchie                   | Nee                  | ' locatie \_ hiërarchie '                                                         |
| 26 | Account inrichtings gegevens            | Nee                  | Word: worker \_ -gegevens/Word: account \_ inrichtings \_ gegevens                                |
| 27 | Gegevens op de achtergrond controleren                | Nee                  | Word: worker \_ -gegevens/WD \_ : \_ gegevens controleren op de achtergrond                                    |
| 28 | Geschiktheids gegevens voor een vergoeding             | Nee                  | Word: worker \_ -gegevens/WD: \_ geschiktheids gegevens voor een voor deel \_                                 |
| 29 | Inschrijvings gegevens voor vergoedingen              | Nee                  | Word: worker \_ -gegevens/WD: \_ inschrijvings gegevens voor vergoedingen \_                                  |
| 30 | Carrière gegevens                          | Nee                  | Word: worker \_ -gegevens/WD: carrière \_ gegevens                                               |
| 31 | Compensatie gegevens                    | Nee                  | Word: worker \_ -gegevens/WD: compensatie \_ gegevens                                         |
| 32 | Gegevens van de belasting dienst voor voorwaardelijke werk nemer | Nee                  | Word: worker \_ -gegevens/WD \_ : \_ \_ \_ \_ gegevens formulier type \_ voorwaardelijke werk belasting dienst       |
| 33 | Gegevens van ontwikkelings items                | Nee                  | Word: worker \_ -gegevens/WD: gegevens van ontwikkelings \_ items \_                                    |
| 34 | Gegevens van werknemers contracten              | Nee                  | Word: worker \_ -gegevens/WD: gegevens van werk nemers- \_ contracten \_                                  |
| 35 | Beoordelings gegevens werk nemer                 | Nee                  | Word: worker \_ -gegevens/WD: \_ beoordelings gegevens werk nemer \_                                     |
| 36 | Feedback ontvangen gegevens               | Nee                  | Word: worker \_ -gegevens/WD: feedback \_ ontvangen \_ gegevens                                   |
| 37 | Gegevens van werknemers doelstellingen                     | Nee                  | Word: worker \_ -gegevens/WD: gegevens van werknemers \_ doelstellingen \_                                         |
| 38 | Foto gegevens                           | Nee                  | Word: worker \_ -gegevens/WD: foto \_ gegevens                                                |
| 39 | Kwalificatie gegevens                   | Nee                  | Word: worker \_ -gegevens/WD: kwalificatie \_ gegevens                                        |
| 40 | Gegevens van gerelateerde persoon                 | Nee                  | Word: worker \_ -gegevens/WD: gerelateerde \_ persoons \_ gegevens                                     |
| 41 | Functie gegevens                            | Nee                  | Word: worker \_ -gegevens/WD: functie \_ gegevens                                                 |
| 42 | Vaardigheids gegevens                           | Nee                  | Word: worker \_ -gegevens/WD: vaardigheids \_ gegevens                                                |
| 43 | Achtereenvolgende profiel gegevens              | Nee                  | Word: worker \_ -gegevens/WD: achter elkaar \_ profiel \_ gegevens                                  |
| 44 | Gegevens van ontalende evaluatie               | Nee                  | Word: worker \_ -gegevens/WD: talen \_ beoordelings \_ gegevens                                   |
| 45 | Gegevens van gebruikers account                    | Nee                  | Word: worker \_ -gegevens/WD: gegevens van gebruikers \_ accounts \_                                        |
| 46 | Document gegevens van werk nemer                 | Nee                  | Word: worker \_ -gegevens/WD: \_ document gegevens van werk nemer \_                                     |

Hier volgen enkele voor beelden van hoe u de workday-integratie kunt uitbreiden om te voldoen aan specifieke vereisten. 

**Voorbeeld 1**

Stel dat u de volgende gegevens sets wilt ophalen uit workday en wilt gebruiken in uw inrichtings regels:

* Kostenplaats
* Kostenplaats hiërarchie
* Betalings groep

De bovenstaande gegevens sets zijn niet standaard opgenomen. Deze gegevens sets ophalen:
1. Meld u aan bij de Azure Portal en open uw werkdag met AD/Azure AD-App voor gebruikers inrichting. 
1. Bewerk op de Blade inrichten de toewijzingen en open de kenmerk lijst workday vanuit de sectie Geavanceerd. 
1. Voeg de volgende kenmerken definities toe en markeer deze als ' vereist '. Deze kenmerken worden niet toegewezen aan een kenmerk in AD of Azure AD. Ze fungeren net als signalen voor de connector voor het ophalen van de kosten plaats, de kostenplaats hiërarchie en de informatie over de salaris groep. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Kenmerknaam | XPATH API-expressie |
     >|---|---|
     >| CostCenterHierarchyFlag  |  Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER ']/WD: Organization_Data/WD: Organization_Code/text () |
     >| PayGroupFlag  |  Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' PAY_GROUP ']/WD: Organization_Data/WD: Organization_Reference_ID/text () |

1. Zodra de gegevensset Data Center en betalings groep beschikbaar is in het *Get_Workers* -antwoord, kunt u de onderstaande XPath-waarden gebruiken om de naam van de kosten plaats, de kostenplaats code en de betalings groep op te halen. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Kenmerknaam | XPATH API-expressie |
     >|---|---|
     >| CostCenterName  | Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' Cost Center ']/WD: Organization_Name/text () |
     >| CostCenterCode | Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' Cost Center ']/WD: Organization_Code/text () |
     >| PayGroup | Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' betalen groep ']/WD: Organization_Name/text () |

**Voorbeeld 2**

Stel dat u certificeringen wilt ophalen die aan een gebruiker zijn gekoppeld. Deze informatie is beschikbaar als onderdeel van de set met *kwalificaties* . Als u deze gegevensset wilt ophalen als onderdeel van de *Get_Workers* -reactie, gebruikt u het volgende XPath: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Voorbeeld 3**

Stel dat u *inrichtings groepen* wilt ophalen die aan een werk nemer zijn toegewezen. Deze informatie is beschikbaar als onderdeel van de gegevensset voor *account inrichting* . Als u deze gegevensset wilt ophalen als onderdeel van de *Get_Workers* -reactie, gebruikt u het volgende XPath: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het configureren van workday voor het Active Directory inrichten](../saas-apps/workday-inbound-tutorial.md)
* [Meer informatie over het configureren van write-back naar workday](../saas-apps/workday-writeback-tutorial.md)
* [Informatie over ondersteunde Workday-kenmerken voor inkomende inrichting](workday-attribute-reference.md)

