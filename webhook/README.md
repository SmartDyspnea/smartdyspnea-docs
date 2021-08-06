# Webhook Documentation

When purchasing your license you can configure a webhook integration by proving us with an url.

Our system will `post` a request whenever a new test is processed.

The payload included in the webhook contains the test information along with the patient identification:

```json
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
```

## Security

Webhooks must be secured on the customer's end by verifying a signature token that is included in the header `SmartDyspnea-Signature`.

The signature can be verified by using a webhook secret that will be provided along with your credentials. The signature is the HMAC of the secret plus the encoded payload.

### Check for replay attacks

We provide a second header `SmartDyspnea-CreatedAt` that you can verify in order to avoid replay attacks, you can check the payload's `created_at` field with the header to check for a long delay.

Do not accept incoming webhooks more than 10 minutes old.

### Code Samples

#### Python

```python
import hmac
import hashlib
import base64

# Desearilized headers as a dict
headers = ...
# Desearilized body as a dict
body = ...
# Provided plain text secret
secret = ...

webhook_signature = headers["Smartdyspnea-Signature"]

message = bytes(json.dumps(body), "utf-8")

signature = base64.b64encode(
    hmac.new(secret.encode("utf-8"), message, digestmod=hashlib.sha256)
    .hexdigest()
    .encode("utf-8")
)

assert hmac.compare_digest(webhook_signature.encode("utf-8"), signature)
```