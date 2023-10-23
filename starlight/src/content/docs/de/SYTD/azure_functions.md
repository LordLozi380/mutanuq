---
title: Azure Functions
sidebar:
    order: 2
---

Azure Functions sind Serverless Computing in Microsoft Azure. Serverless Computing ist ein eher irreführender Begriff. Serverless bedeutet in diesem Zusammenhang einfach, dass Ressourcen `on demand` ein- und ausgeschalten werden. Wenn also momentan viele Anfragen auf Ihre Website eintreffen, werden im Hintergrund mehrere Instanzen gestartet. Logischerweise werden auch hier Server verwendet, um diese Anfragen entgegen zu nehmen. Allerdings bekommt der Developer nichts davon mit, damit er/sie sich mehr auf die Applikation selbst konzentrieren kann.

Eine Azure Function, welche mittels [`NCRONTAB` expression](https://learn.microsoft.com/en-us/azure/azure-functions/functions-bindings-timer?tabs=python-v2%2Cisolated-process%2Cnodejs-v4&pivots=programming-language-csharp#ncrontab-expressions) alle 10 Sekunden getriggert wird, sieht zum Beispiel so:

```csharp
[Function("RecurringEvent")]
public static string Run(
	[TimerTrigger("*/10 * * * * *")] MyInfo myTimer,
	FunctionContext context)
{
	var logger = context.GetLogger("RecurringEvent");
	logger.LogInformation(
		$"Function executed at: {DateTime.Now}");
}
```

:::note
Kurzschreibweisen, wie zum Beispiel `AzureWebJobsStorage` oder `UseDevelopmentStorage=true` sind in `local.settings.json` definiert.
:::

## Input Trigger

Jede Azure Function ist eine einfache Funktion in C# oder [anderen Programmiersprachen](https://learn.microsoft.com/de-de/azure/azure-functions/supported-languages?tabs=isolated-process%2Cv4&pivots=programming-language-csharp). Dabei benötigt jede AF (Azure Function) einen Trigger, bei welchen sie ausgeführt werden soll. Es gibt viele verschiedene Arten von Triggern:

| Azure Function Trigger                                                                                   |
| -------------------------------------------------------------------------------------------------------- |
| HTTP Trigger (Ausführung bei HTTP Request)                                                               |
| Timer Trigger (Ausführung in Intervallen)                                                                |
| Queue Trigger (Ausführung bei neuer Message in [Queue Storage](/de/sytd/storage_account/#queue-storage)) |
| Blob Trigger (Ausführung bei Dateiupload in [Blob Storage](/en/sytd/storage_account/#blob-storage))      |
| Cosmos DB Trigger                                                                                        |
| Event Grid Trigger                                                                                       |
| Event Hub Trigger                                                                                        |
| Custom Bindings (.NET)                                                                                   |

### Timer Trigger

Wenn eine AF zum Beispiel alle 5 Minuten ausgeführt werden soll, kann man diese Function verwenden:

```csharp
[Function("Helloagain")]
public static string Run(
	[TimerTrigger("* */5 * * * *")] MyInfo myTimer,
	ILogger log)
{
	log.LogInformation(
		$"Function executed at: {DateTime.Now}");
}
```

### HTTP Trigger

Wenn eine AF zum Beispiel bei einem HTTP Request auf die Route `/trueberryless` ausgeführt werden soll, kann man diese Function verwenden:

```csharp
[Function("Iamcool")]
public static string Run(
	[HttpTrigger(AuthorizationLevel.Function, "get", Route = "trueberryless")] HttpRequest req,
	ILogger log)
{
    log.LogInformation($"C# HTTP trigger function processed a request.");
}
```

### Blob Trigger

Bei jeder neuen Datei im Container, wird diese AF durchgeführt:

```csharp
[Function("Didufindthissecret")]
public static string Run(
	[BlobTrigger("blobs/{name}", Connection = "AzureWebJobsStorage")] Stream imageStream,
	ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed a file.");
}
```

### Queue Trigger

Bei jeder neuen Datei im Container, wird diese AF durchgeführt:

```csharp
[Function("Firstmessage")]
public static string Run(
	[QueueTrigger("messages", Connection = "AzureWebJobsStorage")] string jsonString
	ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed a message.");
}
```