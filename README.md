# Unknot OneShot REST API

Simple REST endpoint to retrieve location from WiFi data.

## Predict location

`POST https://http-test-api.unknot.id/core-positioning/position`

Required headers:

```http
X-API-Key: <account API key>
Content-Type: application/json
```

### Request body

```json
{
  "device_serial": "unique-id",
  "timestamp": 1760000000000,
  "wifi": [
    { "bssid": "aa:bb:cc:dd:ee:ff", "rssi": -58.0 },
    { "bssid": "11:22:33:44:55:66", "rssi": -71.0 }
  ]
}
```

| Field | Type | Description                                                                                                                                                     |
| --- | --- |-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `device_serial` | string | Device identifier. Any unique string.                                                                                                                           |
| `timestamp` | integer | Time of the prediction request in Unix epoch milliseconds. <br/>NOTE: This value must be within 24 hours of real time, else an error response will be returned. |
| `wifi` | array | Non-empty list of access points observed in one scan.                                                                                                           |
| `wifi[].bssid` | string | Access-point BSSID (MAC address).                                                                                                                               |
| `wifi[].rssi` | number | Received signal strength from the scan, in dBm.                                                                                                                 |

### Example

```bash
curl --request POST 'https://http-test-api.unknot.id/core-positioning/position' \
  --header 'X-API-Key: <account API key>' \
  --header 'Content-Type: application/json' \
  --data '{
    "device_serial": "android-device-id",
    "timestamp": 1760000000000,
    "wifi": [
      { "bssid": "aa:bb:cc:dd:ee:ff", "rssi": -58.0 }
    ]
  }'
```

### Successful response

On succesful location predicition, the server will return a response with HTTP status `2xx` and JSON
body with the following fields. 

```json
{
  "timestamp": "2025-10-09T08:53:20Z",
  "id": "prediction-id",
  "x": 12.34,
  "y": 56.78,
  "latitude": 14.6134,
  "longitude": 121.0576,
  "map_id": 123,
  "layer": "Level 1",
  "floor_level": 1,
  "zone": "Lobby",
  "location_id": 456,
  "location_name": "Main entrance"
}
```

| Field | Type | Notes                    |
| --- | --- |--------------------------|
| `timestamp` | string | ISO-8601 timestamp.      |
| `id` | string | Prediction identifier.   |
| `x`, `y` | number | Map-local coordinates.   |
| `latitude`, `longitude` | number | Geographic coordinates.  |
| `map_id` | integer or null | Optional map identifier. |
| `layer` | string | Layer name.              |
| `floor_level` | integer or null | Optional floor number.   |
| `zone` | string or null | Optional zone name.      |
| `location_id` | integer | Location identifier.     |
| `location_name` | string | Location display name.   |

### Error behavior

If location prediction fails a `4xx` response will be returned with error details in the body.