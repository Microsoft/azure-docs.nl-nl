---
title: Verbinding maken met Azure Analysis Services met een. ODC-bestand | Microsoft Docs
description: Meer informatie over hoe u een Office-gegevens verbindings bestand maakt om verbinding te maken met en gegevens op te halen van een Analysis Services-server in Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8fa657f3b343cdf49723dc68601bb1c9513ff504
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487334"
---
# <a name="create-an-office-data-connection-file"></a>Een Office-gegevens verbindings bestand maken

In dit artikel wordt beschreven hoe u een Office-gegevens verbindings bestand maakt om verbinding te maken met een Azure Analysis Services-server vanuit Excel 2016-versie nummer 16.0.7369.2117 of eerder of Excel 2013. Er is ook een bijgewerkte [Msolap. 7-provider](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) vereist.


1. Kopieer het onderstaande voorbeeld verbindings bestand en plak het in een tekst editor. 

2. `odc:ConnectionString`Wijzig in de volgende eigenschappen:

    *   `Data Source=asazure://<region>.asazure.windows.net/<servername>;`Wijzig in `<region>` de regio van uw Analysis Services-server en `<servername>` de naam van uw server.

    *   `Initial Catalog=<database>;`Wijzig in `<database>` de naam van uw data base.

3. `<odc:CommandText>Model</odc:CommandText>`Wijzig in `Model` de naam van uw model of perspectief. 

4. Sla het bestand op met een `.odc` extensie in de \\ map C:\Users *username*\Documents\My data sources.

5. Klik met de rechter muisknop op het bestand en klik vervolgens op **openen in Excel**. Of Klik in Excel op het lint met **gegevens** op **bestaande verbindingen**, selecteer het bestand en klik vervolgens op **openen**.



**Voorbeeld verbindings bestand**
```
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns="https://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/x-ms-odc; charset=utf-8">
<meta name=ProgId content=ODC.Cube>
<meta name=SourceType content=OLEDB>
<meta name=Catalog content="Database">
<meta name=Table content=Model>
<title>AzureAnalysisServicesConnection</title>
<xml id=docprops><o:DocumentProperties
  xmlns:o="urn:schemas-microsoft-com:office:office"
  xmlns="https://www.w3.org/TR/REC-html40">
  <o:Name>SampleAzureAnalysisServices</o:Name>
 </o:DocumentProperties>
</xml><xml id=msodc><odc:OfficeDataConnection
  xmlns:odc="urn:schemas-microsoft-com:office:odc"
  xmlns="https://www.w3.org/TR/REC-html40">
  <odc:Connection odc:Type="OLEDB">
   <odc:ConnectionString>Provider=MSOLAP.7;Data Source=asazure://<region>.asazure.windows.net/<servername>;Initial Catalog=<database>;</odc:ConnectionString>
   <odc:CommandType>Cube</odc:CommandType>
   <odc:CommandText>Model</odc:CommandText>
  </odc:Connection>
 </odc:OfficeDataConnection>
</xml>
<style>
<!--
    .ODCDataSource
    {
    behavior: url(dataconn.htc);
    }
-->
</style>
 
</head>

<body onload='init()' scroll=no leftmargin=0 topmargin=0 rightmargin=0 style='border: 0px'>
<table style='border: solid 1px threedface; height: 100%; width: 100%' cellpadding=0 cellspacing=0 width='100%'> 
  <tr> 
    <td id=tdName style='font-family:arial; font-size:medium; padding: 3px; background-color: threedface'> 
      &nbsp; 
    </td> 
     <td id=tdTableDropdown style='padding: 3px; background-color: threedface; vertical-align: top; padding-bottom: 3px'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td id=tdDesc colspan='2' style='border-bottom: 1px threedshadow solid; font-family: Arial; font-size: 1pt; padding: 2px; background-color: threedface'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td colspan='2' style='height: 100%; padding-bottom: 4px; border-top: 1px threedhighlight solid;'> 
      <div id='pt' style='height: 100%' class='ODCDataSource'></div> 
    </td> 
  </tr> 
</table> 

  
<script language='javascript'> 

function init() { 
  var sName, sDescription; 
  var i, j; 
  
  try { 
    sName = unescape(location.href) 
  
    i = sName.lastIndexOf(".") 
    if (i>=0) { sName = sName.substring(1, i); } 
  
    i = sName.lastIndexOf("/") 
    if (i>=0) { sName = sName.substring(i+1, sName.length); } 

    document.title = sName; 
    document.getElementById("tdName").innerText = sName; 

    sDescription = document.getElementById("docprops").innerHTML; 
  
    i = sDescription.indexOf("escription>") 
    if (i>=0) { j = sDescription.indexOf("escription>", i + 11); } 

    if (i>=0 && j >= 0) { 
      j = sDescription.lastIndexOf("</", j); 

      if (j>=0) { 
          sDescription = sDescription.substring(i+11, j); 
        if (sDescription != "") { 
            document.getElementById("tdDesc").style.fontSize="x-small"; 
          document.getElementById("tdDesc").innerHTML = sDescription; 
          } 
        } 
      } 
    } 
  catch(e) { 

    } 
  } 
</script> 

</body> 
 
</html>

```