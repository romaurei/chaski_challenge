# Chaski Challenge for Data Engineer

The goal of the challenge is to create a web service to receive, process and display data from Chaski.

## How should the system work?

The system has a client and a server side. You have to implement the server side.

* A transaction starts with a client sending a **POST** request with some parameters and a file with the data in **CSV** format.

* The server receives the data, processes it and stores it internally (you are free to choose how) and generates a report, which is then sent to the client as the response of the **POST** request in **JSON** format.

* Within the report, one of the fields sent as **JSON** must be an **URL** to access a visual report.

* The visual report is meant to be accessed by the user via a web browser.

Below you can see a diagram with the flow just described:

![Process flow](https://github.com/romaurei/chaski_challenge/blob/main/flow.png?raw=true)

## POST Request

The **POST** request is a multipart request with the following fields:

* **duration**: the duration of the session in seconds.
* **maxBpmZone0**: Threshold for zone 0.
* **maxBpmZone1**: Threshold for zone 1.
* **maxBpmZone2**: Threshold for zone 2.
* **maxBpmZone3**: Threshold for zone 3.
* **DevMAC**: Hardware ID of the sender.
* **samplePeriod**: Sample period in microseconds between rows of the CSV.
* **startTimestamp**: Timestamp of the moment when the session started.
* **uploadFile**: CSV file with the data.

[**You can find the CSV with the sample data here**](https://github.com/romaurei/chaski_challenge/blob/main/chaski_data.csv?raw=true)

As an example, a **CURL** command to send a correct **POST** request would be like this:

```
curl --location --request POST 'https://example.com/' \
--form 'duration="1565"' \
--form 'maxBpmZone0="33"' \
--form 'maxBpmZone1="42"' \
--form 'maxBpmZone2="54"' \
--form 'DevMAC="AA:AA:AA:AA:AA:AA"' \
--form 'samplePeriod="100000"' \
--form 'startTimestamp="1627834149000"' \
--form 'uploadFile=@"/C:/Users/user/Desktop/data.csv"'
```

## What columns of the CSV should I use?

You only need to process two columns of the CSV:

* ‘timeSeconds’ : Time in seconds since the session started.

* ’tempOral’ : ignore

* ‘tempNasal’ : ignore

* ‘signalPeriod’ : ignore

* ‘signalFrequencyBpm’ : Respiratory rate (RR), in breaths per minute.

* ‘dateTime’ : ignore

## What is in the report?

Your report should include:

* The total time of the session.

* Minimum RR.

* Maximum RR.

* Average RR.

* The timestamp of the session start.

* Zone Thresholds: An array with the thresholds for the different zones.

* Zones: An array with the relative amount of time spent on each zone (numbers between 0 and 1)

* Report URL: URL to access the visual report you created.

* Histogram: An histogram with up to 100 sample points (properly filtered) with the RR activity vs. time.

For the provided CSV file, the report in JSON should look like this:

```
{
	"avg": 25.772290429952,
	"histogram": [...],
	"max": 50.5618,
	"min": 12.1294,
	"zones": [0.85728444003964, 0.12289395441031, 0.01982160555005, 0, 0],
	"total_time": "1008",
	"date_time": "1636389198989",
	"zone_threshold": [33, 42, 54, 36],
	"visual_report": "https://myservice.domain/..."
}
```

The full **JSON** response is available [**here**](https://github.com/romaurei/chaski_challenge/blob/main/report_example.json)

## What is in the visual report?

Basically the same as in the JSON report but with a plot with the histogram and any useful graphic you wish to add to enhance the user experience.

Here is an example:

![Visual report example](https://github.com/romaurei/chaski_challenge/blob/main/visual_report.png?raw=true)

## How are we going to test it?

You should share with us the URL of the endpoint for the service you developed and we will send a **POST** request with a different dataset than the one we provided you.

We will check that the JSON response complies with the requirements and the correctness of the calculations.

Then we will check the URL of the visual report you sent in the JSON with a web browser and check that it is consistent with the dataset.
