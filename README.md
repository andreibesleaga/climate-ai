# Climate AI - Mobile Mesh EWS - AI Decision Brain

Final project for Building AI course, AI Decision Brain of the EWS System

## Summary

This project implements an AI-driven Early Warning System (EWS) that combines real-time data from a mobile sensor mesh (EVs, Drones, IoT) with satellite imagery. It uses Google BigQuery and Gemini AI to detect climate risks like wildfires and floods, providing hyper-local alerts to citizens and emergency responders and the AI Decision Brain is an important part of the system.


## Background

Climate disasters such as wildfires and floods are becoming more frequent and intense, often catching communities off guard due to gaps in traditional monitoring. Satellite data provides a global view but lacks real-time, ground-level granularity. My personal motivation comes from seeing how delayed information can lead to preventable loss of life and property.

This project solves the problem of "blind spots" in climate monitoring by:
*   Crowdsourcing hyper-local weather data from vehicles and personal devices.
*   Fusing this ground truth with satellite imagery for a complete picture.
*   Automating risk analysis and alerting to reduce response times.


## How is it used?

The solution is used by emergency response centers and individual users in disaster-prone areas.
1.  **Data Collection**: Mobile sensors (vehicles, IoT) continuously stream temperature, pressure, and humidity data.
2.  **Processing**: The system aggregates this data in BigQuery and correlates it with satellite fire/flood indices.
3.  **Action**: If a risk threshold is breached, the system generates a context-aware alert.

Generic overview of the whole mobile-mesh-ews system (including AI Decision Brain)
![Swarm System Overview](SwarmSystem.png)

The system uses AI/ML decision brain for risk/classifications/etc. from Big Data DB sources 
(BigQueryAI SQL Logic Examples):
```sql
-- 1. Generate embeddings for real-time satellite imagery
SELECT uri, ml_generate_embedding_result
FROM ML.GENERATE_EMBEDDING(
  MODEL `climate_ai.multimodal_embedding_model`,
  (SELECT uri, ref FROM `climate_ai.earth_images` WHERE timestamp > TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 1 HOUR))
);

-- 2. Detect Wildfire Anomalies via Vector Search (Distance to "Fire" concept)
SELECT base.uri AS suspicious_image_uri, distance
FROM VECTOR_SEARCH(
  TABLE `climate_ai.earth_image_embeddings`,
  'ml_generate_embedding_result',
  (
    SELECT ml_generate_embedding_result
    FROM ML.GENERATE_EMBEDDING(
      MODEL `climate_ai.multimodal_embedding_model`,
      (SELECT "wildfire smoke and thermal signature" AS content)
    )
  ),
  top_k => 5
);
```


## Data sources and AI methods
The project relies on a hybrid data approach:
*   **Mobile Mesh**: Live telemetry from simulated EVs and IoT devices.
*   **Google Earth Engine**: Satellite imagery for environmental indices.
*   **Global Weather APIs**: Baseline weather data for calibration.

| Syntax         | Description |
| -------------- | ----------- |
| BigQuery AI/ML | Used for predictive modeling and risk classification |
| Gemini AI      | Generates natural language alerts and user guidance |
| Vector Search  | Finds historical analogues for current climate patterns |

## Challenges

What does your project _not_ solve?
*   **Connectivity**: The mobile mesh relies on cellular or ad-hoc network availability, which can fail during disasters.
*   **Adoption**: The system is effective only with a sufficiently dense network of participating sensors (critical mass).

**Ethical Considerations**:
*   **Privacy**: Collecting data from personal vehicles raises privacy concerns. We must ensure data is anonymized and users opt-in.
*   **False Alarms**: AI hallucinations could trigger false panic; human-in-the-loop verification is essential for critical alerts.

## What next?

The project could grow into a global, decentralized safety network.
*   **Edge AI**: Move more processing to the devices to reduce latency and dependency on the cloud.
*   **Blockchain**: Incentivize data sharing with token rewards on a distributed ledger.
*   **Partnerships**: Integrate with municipal smart city infrastructures.

To move on, there is a need for development of the whole system and **hardware integration** (standardizing vehicle APIs), **legal frameworks** for data privacy, **AI testing**, given that this project focuses only on the concept and Big Data BigQuery AI Decision Brain.


## Acknowledgments

*   Based on the **Mobile Mesh EWS** project: [mobile-mesh-ews](../mobile-mesh-ews/)
*   Example project for [Building AI course](https://buildingai.elementsofai.com/)
*   Swarm Architecture Article: [Innovative Swarm System Architecture](https://medium.com/@andrei-besleaga/innovative-swarm-system-architecture-with-live-mobile-edge-sensors-for-climate-monitoring-and-eb0124e7b451)

## License
Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)
