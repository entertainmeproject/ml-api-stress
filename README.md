# Capstone Stress Prediction ML API
This is a repository containing the deployment of the machine learning model used by the backend API. The model runs in Python and is deployed using a Flask web server listening on port 5000.


## Disclaimer
You can provide a `.env` file to set an API key for the model. Just provide an `API_KEY` environmental variable and the API will automatically use it to validate incoming requests.
All requests is validated through a `key` query param in the url like so [https://modelapiurlhere.com?key=this+api+key](https://modelapiurlhere.com?key=this+api+key)

> [!IMPORTANT]
> If no `API_KEY` environmental variable is provided, the server will launch without validating requests.


## API Documentation
This assumes that the API is running on [http://localhost:5000](http://localhost:5000), if your deployed API is in another location then feel free to change the url.

### Check (GET)

    http://localhost:5000/check

This always returns a 200 HTTP response, use it to check if the server is up or not. The response schema is as follows:
```
{
    message: "api is up and running"
}
```

### Recommend (POST)

    http://localhost:5000/predict

This endpoint receives a POST request with a json payload and returns the model output from that payload. The payload schema is as follows:
```
{
    "sleep_duration": 6,
    "sleep_quality": 7,
    "physical_activity": 10,
    "bmi": 1,
    "blood_pressure": 10,
    "heart_rate": 65,
    "daily_steps": 1000,
    "sleep_disorder": 0
}
```

- sleep_duration: how many hours spent sleeping
- sleep_quality: the quality of sleep (0-10)
- physical_activity: how physically active is the user (10-100)
- bmi: weight category (0-2) with greater number skewing from the average weight
- blood_pressure: blood pressure (0-24)
- heart_rate: the user's heart rate (30-95)
- daily_steps: the number of steps taken per day (1000-10000)
- sleep_disorder: 1 for yes, 0 for no

> [!IMPORTANT]
> All of the properties are **mandatory**.

The endpoint then returns an json object with the following schema:

```
{
    "status": "success",
    "stress_level": 4
}
```
The stress level is rated from 1-10, higher numbers meaning higher stress.

## Local Installation
Clone the repository

    git clone https://github.com/entertainmeproject/ml-api-stress.git

Move into the directory

    cd ml-api-stress

Run the application

    python main.py

The machine learning model API will run on [http://localhost:5000](http://localhost:5000) by default.

## Containerize The Application
Run the following command in the directory where the model API is located.

    docker build -t container_tag .

Run the built Docker image

## GCP Installation
Clone the repository

    git clone https://github.com/entertainmeproject/ml-api-stress.git

Move into the directory

    cd ml-api-stress

Containerize the application as follows. 
> [!NOTE]
> Replace anything fully capitalized and preceded with a `$` sign with the appropriate names

    docker build -t $REGION-docker.pkg.dev/$GCP_PROJECT/$ARTIFACT_REGISTRY_REPO/$IMAGE:$TAG .

Make sure that you've created a repository in artifact registry inside the google cloud project that you can use to store the docker images.

Afterwards, push the image to artifact registry with

    docker push $REGION-docker.pkg.dev/$GCP_PROJECT/$ARTIFACT_REGISTRY_REPO/$IMAGE:$TAG

If everything is successful, you should be able to deploy the ML API to Cloud Run. Assign at least 1GB of RAM to the instance so it runs smoothly.
