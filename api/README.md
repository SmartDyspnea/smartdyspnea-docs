# SmartDyspnea - API integration docs

We provide an unified API for our services that can be separated into two different functions:

- __Backoffice API:__ To be called only on behalf of a secured server-side application or internal backoffice service. This API is used to manage and configure the service and end-users.
- __Patient API:__ To be called on behalf of the end-user (patient) or via secured and authenticated applications, this API can be used to consume the main respiratory tests.

## API Docs

A comprehensive OpenAPI documentation can be accessed live in: 

You can download the OpenAPI specs from [](./smartdyspnea-openapi.json) to integrate if into postman or you preferred service.

You can access the rendered [swagger docs](docs.smartdyspnea.com)


## Backoffice (Client) API

## Authentication

This API implements the OAuth2 using the `Client Credentials` authentication flow, in order to retrieve a token you need both a issued `Client ID` and `Client Secret`. Talk to smartdyspnea sales at info@smartdyspnea.com if you are interested in purchasing a license.

```bash
curl \
    -u <CLIENT ID>:<CLIENT SECRET> \
    -X POST \
    -H "Content-Type: application/x-www-form-urlencoded"  \
    -d  'grant_type=client_credentials&scope=smartdyspnea/patient.read smartdyspnea/patient.write smartdyspnea/patient.token' \
    https://auth.smartdyspnea.com/oauth2/token
```

```json
{"access_token": "<sample_token>", "expires_in":3600, "token_type":"Bearer"}
```

This will return a JSON object including an `access_token` that can be used to call the backend API

## Creating patients

You must sign-up and manage your own users within SmartDyspnea. For that you can register a patient item that you can use later in order to authorize the frontend application with the API. Each patient will keep an individual record for the performed tests.

You must relate each one of your users with a patient in your own system.

### Register a patient

We do not store any data about the patients, you may register a patient by calling the following API:

```bash
curl \
    -X POST \
    -H "Authorization: Bearer <client_token>"  \
    -H "Content-Type: application/json"  \
    https://api.smartdyspnea.com/beta/patients
```

This will return a new patient_id that you must store and assign to your own user.

```json
{"patientId": "810299fa-1b4b-4b99-86c3-73ac9a3af96c", "createdAt": "2021-06-16 09:11:42"}
```

### List patient

You can access the list of registered patients

```bash
curl \
    -X GET \
    -H "Authorization: Bearer <client_token>"  \
    -H "Content-Type: application/json"  \
    https://api.smartdyspnea.com/beta/patients
```

```json
{"items": [
    {"patientId": "810299fa-1b4b-4b99-86c3-73ac9a3af96c", "createdAt": "2021-06-16 09:11:42"}
]}
```

### Authenticate patients

A `patient_token` must be issue for each end-user in order to use a frontend application on behalf of your account, you can use the following endpoint.

__IMPORTANT:__ Never expose your client credentials beyond a server side secure application.

```bash
curl \
    -X GET \
    -H "Authorization: Bearer <client_token>"  \
    -H "Content-Type: application/json"  \
    https://api.smartdyspnea.com/beta/patients/810299fa-1b4b-4b99-86c3-73ac9a3af96c/token
```

This will return a token that you can further pass to your frontend application for that particular user session

```json
{"token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiI4MTAyOTlmYS0xYjRiLTRiOTktODZjMy03M2FjOWEzYWY5NmMiLCJleHAiOjE2MjM4Mzg2MTJ9.T7KOsz7ywDW0a87Qc24kjXdF9jHAI15VIH8cYmv6tHs"}
```

## Frontend (patient) API

This API can be used on behalf of users or integrated into your own frontend. If you are integrating one of our widgets into your application this is the API that will be used underneath.

You must a Bearer authorization header using a valid `patient_token`

### Creating tests

This endpoint allows the creation of a new test by sending an encoded mp3 file to the API

```bash
curl \
    -X POST \
    -H "Authorization: Bearer <patient_token>"  \
    -H "Content-Type: application/json"  \
    -d '{"audio": "<mp3_bytes>"}' \
    https://api.smartdyspnea.com/beta/patients/810299fa-1b4b-4b99-86c3-73ac9a3af96c/tests
```

```json
{
  "testId": "test-id-1234",
  "patientId": "810299fa-1b4b-4b99-86c3-73ac9a3af96c",
  "createdAt": "01-01-2021",
  "test": {
    "code": "C01",
    "text": "100-95",
    "confidence": 0.99
  }
}
```

### Listing previous tests

This endpoint retrieves the list of past tests

```bash
curl \
    -X GET \
    -H "Authorization: Bearer <patient_token>"  \
    https://api.smartdyspnea.com/beta/patients/810299fa-1b4b-4b99-86c3-73ac9a3af96c/tests
```

```json
{
  "patientId": "810299fa-1b4b-4b99-86c3-73ac9a3af96c",
  "tests": [
    {
      "testId": "test-id-1234",
      "patientId": "810299fa-1b4b-4b99-86c3-73ac9a3af96c",
      "createdAt": "01-01-2021",
      "test": {
        "code": "C01",
        "text": "100-95",
        "confidence": 0.99
      }
    }
  ]
}
```