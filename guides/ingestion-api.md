---
title: Data Ingestion API
description: Sending event data from your application.
---

Our primary event ingestion endpoint is used for sending JSON payloads, based on the different [event definitions](/core/event-definitions) we support, as activity is happening in your product.

The endpoint to use is: `https://ingest.valued.app/events`

Two headers are required:

- `Authorization` header, for your Projects API token in the form of `Bearer {token}` as described in [RFC-6750](https://www.rfc-editor.org/rfc/rfc6750.html).
- `Content-Type` header, set to `application/json`.

For example (using `cURL`):

```bash
curl -X POST https://ingest.valued.app/events \
-H 'Authorization: Bearer my-project-token' \
-H 'Content-Type: application/json' \
-d '{"category": "action", "key": "account.signin", "user": {"id": 123}}'
```

Our ingestion API does not validate the JSON data you send us, instead we accept the data and add it to our queue for processing.

This allows us to deploy highly available `listeners` in various global regions which respond within milliseconds, and process the event data without the possibility of affecting the API response time.

We currently support the following `listener` regions:

- Sydney, Australia
- Brussels, Europe
- North Virginia, USA

If you require a `listener` in a different region, please contact us.

---

## Client Libraries 

### Python

Install the SDK

```
pip install valued
```

Setup a client

```python
import os
import valued
# Get the API key for authentication.
token = os.environ.get("VALUED_API_KEY") 
# Create a client
client = valued.Client(token=token)
```

### Ruby

Install the SDK

```ruby
gem "valued-client"
gem "concurrent-ruby-ext", "~> 1.1" # optional, but will speed up processing
```

Setup a client

```ruby
require "valued"
# Get the token for authentication.
token = ENV.fetch("VALUED_API_KEY") # or wherever you store credentials
# Create a client 
client = Valued::Client.new(token)
```
