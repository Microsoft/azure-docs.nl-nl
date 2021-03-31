---
title: Code schrijven voor het bijhouden van aanvragen met Azure-toepassing Insights | Microsoft Docs
description: Schrijf code voor het bijhouden van aanvragen met Application Insights zodat u profielen voor uw aanvragen kunt ophalen.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.custom: devx-track-csharp
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aaa1d6df9faa20b1a561bfccdfea682af7645c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88930244"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Code schrijven voor het bijhouden van aanvragen met Application Insights

Om profielen voor uw toepassing weer te geven op de pagina prestaties, moet Azure-toepassing Insights aanvragen voor uw toepassing bijhouden. Application Insights kunt automatisch aanvragen bijhouden voor toepassingen die zijn gebouwd op al-instrumentale frameworks. Er zijn twee voor beelden van ASP.NET en ASP.NET Core. 

Voor andere toepassingen, zoals Azure Cloud Services worker-rollen en Service Fabric stateless Api's, moet u code schrijven om Application Insights te geven waar uw aanvragen beginnen en eindigen. Nadat u deze code hebt geschreven, wordt de telemetrie van aanvragen verzonden naar Application Insights. U kunt de telemetrie weer geven op de pagina prestaties en er worden profielen verzameld voor deze aanvragen. 

Ga als volgt te werk om aanvragen hand matig bij te houden:

  1. Voeg de volgende code toe in de levens duur van de toepassing:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Zie [service Fabric gebruiken met Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)voor meer informatie over deze configuratie van de algemene instrumentatie sleutel.  

  1. Voor elk stukje code dat u wilt instrumenteren, voegt u een `StartOperation<RequestTelemetry>` instructie **using** toe, zoals wordt weer gegeven in het volgende voor beeld:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Aanroepen `StartOperation<RequestTelemetry>` binnen een ander `StartOperation<RequestTelemetry>` bereik wordt niet ondersteund. U kunt `StartOperation<DependencyTelemetry>` in plaats daarvan in het geneste bereik gebruiken. Bijvoorbeeld:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
