
This document explains how to run and test the Audio Redaction using Docker. No local Python, ML frameworks, or model downloads are required.

---

## 1. What This System Does

The platform performs:

* Automatic Speech Recognition (ASR)
* Multiple parallel Named Entity Recognition (NER) models
* Timestamp alignment (word-level)
* Output will be json file containing entities(Example is given below)

All services run as isolated Docker containers and communicate over an internal Docker network.

---


## 2. Required Software

Only the following is required:

* Docker Desktop (latest version)

Install from:
[https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

After installation, ensure Docker engine is running.

Verify installation:

```bash
docker --version
docker compose version
```


## 3. Files You Will Receive

You only need the following files:

```
project-root/
├── docker-compose.yml
├── README.md

No source code is required to run the system.

---

## 4. Pull All Docker Images

Run the following commands once:

```bash
docker pull darpanvora/audio-redaction-aggregator:1.0
docker pull darpanvora/whisper-medical-asr:1.0
docker pull darpanvora/blaze-ner:1.0
docker pull darpanvora/xlmr-ner:1.0
docker pull darpanvora/gliner-ner:1.0
docker pull darpanvora/openmed-anatomy-ner:1.0
```

This may take several minutes depending on internet speed.

---

## 5. Start the System

From the folder containing `docker-compose.yml`:

```bash
docker compose up 
```

This will start:

* Aggregator API
* Whisper Medical ASR
* Blaze Clinical NER
* XLM-R NER
* GLiNER NER
* OpenMed Anatomy NER

Check status:

```bash
docker compose ps
```

All services should show `running`.

---

## 6. Access the API (Swagger UI)

Open a browser and go to:

```
http://localhost:8000/docs
```

This opens the interactive Swagger interface for testing the pipeline.

---

## 7. How to Test (Sample Request)

Use the `/redact` endpoint.
Here window size means how many segments do we merge and overlap = window size - stride
### Example JSON Input
```json
{
  "input": {
    "type": "url",
    "value": "https://raw.githubusercontent.com/Darpan2311/demofile/main/clean_audio.wav"
  },
  "ner_config": {
    "models": [
      "blaze_cli",
      "xlmr_names",
      "device_gliner",
      "anatomy_openmed"
    ],
    "params": {
      "xlmr_names": { "window_size": 5, "stride": 2 },
      "blaze_cli": { "window_size": 5, "stride": 2 }
    }
  }
}
```

Click **Execute** and wait for processing.

---

you should see output like below

```json
{ 
  "entities": 
  [ 
    { 
      "text": "Johnson", 
      "start_time": 6.547,
      "end_time": 6.99, 
      "category": "PERSON", 
      "confidence": 0.94 
    },
    { 
      "text": "pain in my chest", 
      "start_time": 13.516, 
      "end_time": 14.6, 
      "category": "CONDITION", 
      "confidence": 0.23 
    }
  ]
}

```
