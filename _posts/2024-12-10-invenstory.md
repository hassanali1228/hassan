---
layout: post
title: Invenstory - decentralized inventory tracking with WiFi fingerprinting
date: 2024-12-11
description:
tags: project
giscus_comments: true
related_posts: false
mermaid:
  enabled: true
---

Making a lean inventory tracking system that will monotonically imporve with time.

A warehouse operates as a decentralized system with a shared state. Each asset moves inventory independently with a plan agreed upon between all assets. An _asset_ here can be a forklift, pallet jack, etc... There is a lot of active [research](https://uwaterloo.ca/analytics-optimization-lab/projects/adding-data-analytics-warehouse-logistics) going into how to optimize inventory management in these facilities. I will not bore you with collected data and research developments, but will present to you a fun solution to generate the data needed for these optimizations.

How does one start with tracking the inventory? With tracking the assets that move the inventory.

## How do you track location?

This is a geolocation problem. As inventory is commonly stored in indoor spaces, GPS is out the door. A question at the root of designing the positioning system is:

> Should the system track the location of each asset centrally, or should each asset independently determine and report its own location?

**System tracks each asset**

This would be a centralized system tracking the location of each asset in the facility. Example: cameras strategically placed around the warehouse tracking the location of all assets using computer vision. This approach has high overhead and is prone to vision obstruction, but it demonstrates centralized tracking clearly.

The benefit is that you have a fixed cost which won't scale with your number of assets. Only one "computer" is required to monitor all activities in the warehouse.

**Asset reports to system**

This means assets tracking their own location and reporting it to an endpoint. Example: cameras placed on each asset that decode location by reading barcodes placed around facility.

The benefit here is the reduced complexity as each agent finds their location independently, rather than a central system tracking all states simultaneously and having to differentiate between assets. You have all the other benefits that you get with a decentralized system like fault tolerance (central server goes down), scalability (no central compute contraint except for location ingestion pipeline), etc...

One thing that's not easy to see yet is that it opens up doors to an exciting new-ish tracking method, [WiFi fingerprinting](https://en.wikipedia.org/wiki/Wi-Fi_positioning_system). It is because the asset can track its own location, we can use this method.

---

**Chosen Approach: Asset reports to system**

This method leverages each asset's ability to independently determine and report its location, facilitating a scalable and fault-tolerant solution.

Let's take a step back here, and see what options we have to implement a decentralized tracking system?

1.  Cameras placed on each asset to read barcodes placed around facility

Camera feed processing will require compute power on each asset incurring overhead, and barcode maintenance and obstruction is concerning as well. This solution is not feasible.

2. RFID/Bluetooth antennas placed on each asset that scan RFID/BLE "checkpoints" scattered around facility to determine asset location.

Minimal compute required on assets, but process of placing RFID tags around facility is not easily scalable and requires ovehead on both the facility and asset side to work.

3. WiFi antennas placed on each asset to measure signal strength to existing routers in facility which will determine location of asset, using WiFi fingerprinting.

This solution solves problems with previous solutions, as it scales with WiFi routers and is cheap to implement on forklifts with a simple [ESP32](https://en.wikipedia.org/wiki/ESP32) microcontroller on them. The drawback here is lower precision, dependent on factors like humidity. Considering how WiFi fingerprinting is an emerging technology, the research here will only make this solution better to implement in the future years.

### WiFi fingerprinting

Each asset will measure received signal strength (RSS) to all access points (APs), which will be a vector mapping from AP MAC addresses to RSS values. This RSS vector will be sent into a generalized pipeline for computing asset location. The pipeline also requires RSS values from predetermined reference points for comparison, which are stored in the "fingerprint database". Here is a flowchart of the pipeline:

```mermaid
flowchart LR
    id2[[computeLocation]]
    id0["`**RSS vector**_i
    (RSSi1, MAC1)
    (RSSi2, MAC2)
    ...`"]
    id1[("`**FINGERPRINT DATABASE**
    [x0, y0], (RSS01, MAC1), (RSS02, MAC2), ...
    [x1, y1], (RSS11, MAC1), (RSS13, MAC3), ...
    ...`")]
    id0 --> id2
    id1 --> id2
    id2 --> id3["Location: [xi, yi]"]
```

What's to be noted here is that choosing the location of reference points (RPs) is tricky. Wifi signal attenuates exponentially with distance, which means RPs need to placed with non-linear spacing to get a good coverage of RSS values in the facility. There will also be walls and obstructions which will bounce signals creating multiple spots with similar RSS values. Here, the multiple APs will help in triangulating asset location. RPs should be placed further from APs, so that the reduced gradient in RSS values near an AP over distance is avoided. These observations were captured in a simulation using the [path lass model](https://en.wikipedia.org/wiki/Path_loss):

<figure>
    <img src="/assets/img/wifi_fingerprinting.png"
         alt="WiFi fingerprinting simulation"
         width="500">
</figure>

The `computeLocation` function in the above flowchart will apply a K-Nearest-Neighbors algorithm to compute location of the asset. Here is some pseudocode:

```python
def computeLocation(
    observedRss: List[ Tuple[ int, str ] ],
    fingerprintDB: List[ Tuple[
        # Location, RSS vector
        Tuple[ float, float ], List[ Tuple[ int, str ] ] ] ]
) -> Tuple[ float, float ]:
    # For each point in the database:
    #     a. Calculate Euclidean and Manhattan distance between observed RSS values and db entry.
    #     c. Store the distances along with the coordinates in a list.

    # Sort list of distances by Euclidean, using Manhattan to break ties.

    # Select K closest points from the sorted list.

    # Take weighted average of coordinates of K closest points, using inverse of distance as weight
    #     If any points have 0 distance, assign infinite weight to that point

    # return the weighted average of [ x, y ] as location of asset
```

As previously discussed, WiFi fingerprinting will be less precise due to environmental factors. We could add sensors to record humidity on assets and input this into the model for computing location. But a more static way of tackling this is to have less granular tracking using zoning in the warehouse. Exact dropoff and pickup points are already known. With wider zones and recorded flow of traffic, we would have enough log data to apply meaningful optimizations.

## How do you track inventory?

Now that we know the location of the asset, how do we know what inventory is on the asset?

A lot of facilities already use RFID tech to store package information, detect misloads, etc... We can continue to use these by adding an RFID reader to each asset. The asset will periodically scan items on it and report these items to the centralized system.

The challenge here is to know when an item is removed from or added to the asset. This is an issue because the RFID antenna isn't guaranteed to scan all items on the asset in each scan. One way is to add a weight sensor to determine if an item is added or removed from the asset in addition to what is scanned by the RFID reader. To minimize overhead though, time can be used as a proxy for weight. By setting a threshold, we can guartee an item has been added or removed from the asset.

## What's next?

- Implement WiFi fingerprinting pipeline on a small scale
- Designing a robust centralized system to handle inventory trackign and log management
- Discuss optimizations enabled by log data
