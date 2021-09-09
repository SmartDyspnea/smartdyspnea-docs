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

### List patients

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

### Checking patient tests

You can check the tests created for a particular patient using the backoffice API.

```bash
curl \
    -X GET \
    -H "Authorization: Bearer <client_token>"  \
    -H "Content-Type: application/json"  \
    https://api.smartdyspnea.com/beta/patients/810299fa-1b4b-4b99-86c3-73ac9a3af96c/test
```

```json
{
    "patient_id": "810299fa-1b4b-4b99-86c3-73ac9a3af96c",
    "tests": [
        {
            "updated_at": "2021-08-06T15:44:28.069940",
            "created_at": "2021-08-06T15:44:22.978767",
            "status": "success",
            "test_result": {
                "code": "C01",
                "text": "100-95",
                "confidence": 0.99
            },
            "patient_id": "810299fa-1b4b-4b99-86c3-73ac9a3af96c",
            "test_id": "dfd076ac-2959-43dd-a019-9250f860b5af"
        }
    ]
}
```

## Patient API (Frontend)

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
    https://api.smartdyspnea.com/beta/patient/tests
```

```json
{
    "updated_at": "2021-08-06T15:44:28.069940",
    "created_at": "2021-08-06T15:44:22.978767",
    "status": "pending",
    "patient_id": "810299fa-1b4b-4b99-86c3-73ac9a3af96c",
    "test_id": "dfd076ac-2959-43dd-a019-9250f860b5af"
}
```

## Asynchronous execution of tests

Creating a new test will return an immediante response with the id of the new tests. 

If you retrieve the lists of tests at that moment you will see that the value of `status` property will be `pending`.

At that moment the test is being scheduled and triggered asynchronously. It should resolve in a few seconds, changing its `status` to `success` and adding the resulting values.

A webhook will also be triggered if configured.
### Listing previous tests

This endpoint retrieves the list of past tests for the patient, can be used from the frontend to render the results if building a custom integration.

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
            "updated_at": "2021-08-06T15:44:28.069940",
            "created_at": "2021-08-06T15:44:22.978767",
            "status": "success",
            "test_result": {
                "code": "C01",
                "text": "100-95",
                "confidence": 0.99
            },
            "patient_id": "810299fa-1b4b-4b99-86c3-73ac9a3af96c",
            "test_id": "dfd076ac-2959-43dd-a019-9250f860b5af"
        }
    ]
}
```

## Interpreting test result

Our algorithm will return a prediction based on a particular interval for oxygen saturation, which should be interpreted __clinically__.

### Test results

- `code`: The identifier of the result, see below for a sample of a clinical interpretation.
- `text`: The textual representation of oxygen saturation interval.
- `confidence`: Statistical confidence of the result

#### Sample clinical interpretation:

Our algorithm is an approximation and should not be interpreted as a medical diagnostic. We strongly recommend adding a disclaimer text along with such interpretations:

__Spanish__

- __Dislaimer:__ Los resultados de esta prueba son aproximados y deben utilizarse
            únicamente con fines preventivos, si nota cualquier tipo de síntoma
            o cree que puede estar enfermo acuda a su médico lo antes posible.


- `C00`
    - __Resumen:__ El test ha fallado
    - __Explicación:__ Lo sentimos, pero no hemos podido procesar la grabación. Inténtelo de nuevo procurando grabar en un entorno silencioso y contando lo más rápido que pueda pero tratando de pronunciar correctamente todos los números del 1 al 30.',
> 

- `C01`
    - __Resumen:__ La saturación de oxígeno es la adecuada
    - __Explicación:__ No obstante, si nota algún síntoma respiratorio repita la prueba o consulte a su médico.

- `C02`
    - __Resumen:__ La saturación de oxígeno es más baja de lo habitual
    - __Explicación:__ Sería recomendable contactar con su médico para que pueda confirmarlo

- `C03`
    - __Resumen:__ La saturación de oxígeno es preocupantemente baja
    - __Explicación:__ Contacte con su médico lo antes posible para descartar cualquier problema respiratorio


__English__

Coming soon...