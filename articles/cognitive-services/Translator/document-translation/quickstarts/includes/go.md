---
title: "Quickstart: Document Translation Go"
description: 'Document translation processing using the REST API and Go programming language'
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: include
ms.date: 12/16/2022
ms.author: lajanuar
recommendations: false
---

## Set up your Go environment

You can use any text editor to write Go applications. We recommend using the latest version of [Visual Studio Code and the Go extension](/azure/developer/go/configure-visual-studio-code).

> [!TIP]
>
> If you're new to Go, try the [Get started with Go](/training/modules/go-get-started/) Learn module.

1. If you haven't already done so, [download and install Go](https://go.dev/doc/install).

    * Download the Go version for your operating system.
    * Once the download is complete, run the installer.
    * Open a command prompt and enter the following to confirm Go was installed:

        ```console
          go version
        ```

## Translate all documents in a storage container

1. In a console window (such as cmd, PowerShell, or Bash), create a new directory for your app called **document-translation-qs**, and navigate to it.

1. Create a new Go file named **document-translation.go** in the **document-translation-qs** directory.

1. Copy and paste the document translation [code sample](#code-sample) into your **document-translation.go** file.

    * Update **`{your-document-translation-endpoint}`** and **`{your-key}`** with values from your Azure portal Translator instance.

    * Update the **`{your-source-container-SAS-URL}`** and **`{your-target-container-SAS-URL}`** with values from your Azure portal Storage account containers instance.

## Code sample

  > [!IMPORTANT]
  > Remember to remove the key from your code when you're done, and never post it publicly. For production, use a secure way of storing and accessing your credentials like [Azure Key Vault](../../../../../key-vault/general/overview.md). For more information, *see* Cognitive Services [security](../../../../../cognitive-services/security-features.md).
  
```go

package main

import (
    "bytes"
  "encoding/json"
    "fmt"
    "net/http"
)

func main() {

    httpposturl := "{your-document-translation-endpoint}/translator/text/batch/v1.0/batches"
    fmt.Println("Response", httpposturl)

    var jsonData = []byte(`{
    "inputs": [
        {
            "source": {
                "sourceUrl": "{your-source-container-SAS-URL}"
            },
            "targets": [
                {
                    "{your-target-container-SAS-URL}",
                    "language": "fr"
                }
            ]
        }
    ]
}`)

    request, error := http.NewRequest("POST", httpposturl, bytes.NewBuffer(jsonData))
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("Ocp-Apim-Subscription-Key", "{your-key}")

    client := &http.Client{}
    response, error := client.Do(request)
    if error != nil {
        panic(error)
    }
    defer response.Body.Close()

    fmt.Println("response Status:", response.Status)
    //fmt.Println("response Headers:", response.Header)
  var printHeader = (response.Header)
    //body, _ := ioutil.ReadAll(response.body)
    prettyJSON, _ := json.MarshalIndent(printHeader, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
  }


```

## Run your Go application

* Once you've added a code sample to your application, your Go program can be executed in a command or terminal prompt. Make sure your prompt's path is set to the **document-translation-qs** folder and use the following command:

    ```console
     go run document-translation.go
    ```

* The successful POST method returns a `202 Accepted` response code indicating that the batch request was created by the service. 

* The POST request also returns response headers including `Operation-Location` that provides a value used in subsequent GET requests. 

* The translated documents will be listed in your target container.
