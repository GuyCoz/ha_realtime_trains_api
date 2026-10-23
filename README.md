# realtime_trains_api
api.rtt.io Home Assistant integration

It provides detailed live train departures and journey stats:

```yaml
station_code: WAT
calling_at: WAL
next_trains:
  - origin_name: London Waterloo
    destination_name: Basingstoke
    service_uid: Q46478
    scheduled: 21-01-2022 20:12
    estimated: 21-01-2022 20:12
    minutes: 3
    platform: '10'
    operator_name: South Western Railway
    stops_of_interest: []
    scheduled_arrival: 21-01-2022 20:37
    estimate_arrival: 21-01-2022 20:36
    journey_time_mins: 24
    stops: 2
  - origin_name: London Waterloo
    destination_name: Woking
    service_uid: Q46174
    scheduled: 21-01-2022 20:20
    estimated: 21-01-2022 20:20
    minutes: 11
    platform: '4'
    operator_name: South Western Railway
    stops_of_interest:
      - stop: VXH
        name: Vauxhall
        scheduled_stop: 21-01-2022 20:23
        estimate_stop: 21-01-2022 20:23
        journey_time_mins: 3
        stops: 0
    scheduled_arrival: 21-01-2022 20:54
    estimate_arrival: 21-01-2022 20:53
    journey_time_mins: 33
    stops: 7
unit_of_measurement: min
icon: mdi:train
friendly_name: Next Waterloo train data
```

This Home Assistant integration is only made possible by the brilliant Realtime Trains API (https://api.rtt.io also see https://www.realtimetrains.co.uk) which is maintained by Tom Cairns under swlines Ltd (https://twitter.com/swlines).

Alternatively, you can use the built-in `uk_transport` integration (see https://www.home-assistant.io/integrations/uk_transport/).  NOTE: Unlike this `realtime_trains_api` integration, `uk_transport` cannot provide additional journey details such as stops, journey durations and arrival times.

# Guide

## Installation & Usage

1. Signup to https://api.rtt.io/ and retrieve your API Auth Credentials.
2. Add repository to HACS (see https://hacs.xyz/docs/faq/custom_repositories) - use "https://github.com/megakid/ha_realtime_trains_api" as the repository URL.
3. Install the `realtime_trains_api` integration inside HACS
4. To your HA `configuration.yaml`, add the following:
```yaml
sensor:
  - platform: realtime_trains_api
    refresh_token: !secret rtt_refresh_token

    # Polling interval. Be mindful of the Realtime Trains API rate limits,
    # particularly if using multiple queries and journey detail lookups.
    scan_interval:
      minutes: 3

    # If no departures are returned, reduce the polling frequency.
    auto_adjust_scans: true

    queries:
      - origin: KGX
        destination: YRK

        # Retrieve detailed journey information for the next two trains.
        journey_data_for_next_X_trains: 2

        # Optional intermediate stations to include in the journey details.
        stops_of_interest:
          - PBO

        sensor_name: kings_cross_to_york
```
5. To your `secrets.yaml` add:
```yaml
rtt_refresh_token: "YOUR_REFRESH_TOKEN"
```
7. Restart HA
8. Your `sensor` will be named something like `sensor.kings_cross_to_york` (unless you specified a `sensor_name`) for each query you defined in your configuration.

In the example above:

KGX is the origin station (London King's Cross).
YRK is the destination station (York).
PBO is included as a stop of interest (Peterborough).
journey_data_for_next_X_trains controls how many upcoming services have detailed journey information retrieved, including destination arrival forecasts and stops of interest.
Service-detail responses are cached temporarily to reduce API usage.


