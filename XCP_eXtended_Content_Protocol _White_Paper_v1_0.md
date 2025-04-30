# XCP – eXtended Content Protocol  
**Author:** Rogério Figurelli  
**White Paper Version:** 1.0 (Revised)  

**Filename:** `XCP_eXtended_Content_Protocol_White_Paper_v1_0_Revised.md`  
**Repository:** `XCP-eXtended-Content-Protocol`  
**License:** [MIT License](https://github.com/rfigurelli/XCP-eXtended-Content-Protocol/blob/main/LICENSE) [14]

---

## Executive Summary

For readers looking for a quick overview, here are the key highlights of the eXtended Content Protocol (XCP):

- **Objective:** Create a unified, transport‑agnostic, semantic‑first protocol that integrates content modeling, session management, and security to simplify interoperability across heterogeneous systems.
- **Core Benefits:**
  - **Semantic Interoperability:** Machine‑readable messages with embedded ontology links enable automated reasoning and context‑aware processing.
  - **Dynamic Extensibility:** Runtime schema discovery and negotiation remove the need for brittle adapters and manual mappings.
  - **Built‑in Security:** OAuth2/JWT authentication, message integrity checks, and optional end‑to‑end encryption safeguard communications by design.
- **Gateway Functionality:** XCP gateways translate between legacy protocols (MQTT, HTTP, CoAP, gRPC, MCP) and the XCP envelope, logging conversion metadata for auditability.
- **High‑Impact Use Cases:**
  1. **Industrial IoT:** Zero‑touch provisioning and semantic telemetry consolidation.
  2. **Smart Cities:** Real‑time, semantically consistent data feeds for urban management.
  3. **Autonomous Systems:** Decentralized robotic swarms with runtime capability negotiation.
  4. **Healthcare:** Enriched, semantically tagged wearable and diagnostic data for clinical analytics.
  5. **Cloud & Edge Microservices:** Schema‑flexible orchestration enabling blue‑green and canary deployments.
  6. **DAOs & Smart Contracts:** Machine‑interpretable service contracts with verifiable provenance.

## Abstract

In an increasingly heterogeneous digital landscape—spanning microcontrollers, mobile apps, cloud services, and autonomous agents—existing communication protocols struggle to provide deep semantic interoperability, dynamic extensibility, and integrated security. The eXtended Content Protocol (XCP) builds on the principles of the Model Content Protocol (MCP) to deliver a unified, transport-agnostic framework that embeds semantics, session management, and dynamic negotiation directly into the protocol. By acting as a semantic gateway to legacy protocols like HTTP, MQTT, CoAP, and gRPC, XCP enables seamless interoperability, future-proof extensibility, and robust trust across distributed ecosystems. The design aligns with industry best practices and emerging trends [15].

---

## 1. Introduction and Motivation

### 1.1 The Interoperability Challenge

As digital infrastructures span billions of edge devices, mobile applications, industrial controllers, and cloud-native services, a growing semantic gulf has emerged: payloads are syntactically transmittable but lack machine-readable meaning. Integrators rely on brittle adapters, custom mappings, and manual schema translations, inflating development time, increasing maintenance burdens, and introducing subtle interoperability errors. For example, in smart manufacturing environments, consolidating MQTT telemetry streams with HTTP-based enterprise systems often demands extensive custom code and ongoing updates whenever device schemas evolve. Similarly, in healthcare, inconsistent data models across wearable monitors complicate real-time patient monitoring and analytics.

### 1.2 From Content Models to Full-Stack Semantics

The Model Content Protocol (MCP) [7] introduced standardized content models for telemetry, commands, and properties, enabling devices to publish self-descriptive payloads and simplifying data interpretation. However, MCP deliberately omits transport orchestration, session management, and runtime schema negotiation—critical components for truly autonomous, adaptive ecosystems. XCP extends MCP’s vision by integrating:

- **Unified Transport**: Global session identifiers, uniform addressing, and support for publish/subscribe as well as request/response messaging patterns.
- **Dynamic Schema Discovery**: Decentralized registry-driven model fetching, versioning, modular inheritance, and compatibility negotiation at runtime.
- **Semantic Enforcement**: In-message ontology linking (e.g., W3C WoT [11], SOSA/SSN), mandatory `@context` references, and validation rules that enable machines to interpret and reason over data without external configuration.
- **Built-in Security**: Native authentication tokens (OAuth2/JWT), message integrity checks (HMAC or digital signatures), and optional end-to-end encryption.

These enhancements ensure that XCP is not merely a container for semantic content but a fully orchestrated, interoperable communication fabric.

## 2. Evolution of Communication Protocols

| Protocol    | Era           | Strength                           | Limitation                                |
|:------------|:--------------|:-----------------------------------|:-------------------------------------------|
| FTP [2]     | 1980s         | Reliable file exchange             | No structured metadata                     |
| SMTP [3]    | 1980s–1990s   | Asynchronous messaging             | Opaque bodies, no dynamic discovery        |
| HTTP [1]    | 1990s–2000s   | Ubiquitous, human-readable         | No machine semantics                       |
| MQTT [4]    | 2010s         | Lightweight IoT messaging          | Undefined payload semantics                |
| CoAP [5]    | 2010s         | RESTful for constrained networks   | Lacks semantic standards                   |
| gRPC [6]    | 2010s         | High-performance RPC               | Static schemas, no runtime negotiation     |
| HTTP/2 [8]  | 2015          | Multiplexed streams, header compression | More complex framing                   |
| HTTP/3 [9]  | 2022          | QUIC-based, reduced latency        | UDP-based; requires new infrastructure      |
| MCP [7]     | 2024          | Standardized content models        | No transport/session management            |

Integration today often relies on manual mappings, adapters, APIs, and human agreements [10].

Each protocol solved domain-specific challenges but created silos of functionality. domain-specific challenges but created silos of functionality.

---

## 3. Design Principles of XCP

XCP’s core strength derives from five foundational principles that ensure seamless, secure, and semantically rich communication across heterogeneous systems.

### 3.1 Semantic First
Every message field in XCP is explicitly linked to a formal ontology (e.g., W3C WoT [11], SOSA/SSN). By embedding `@context` references and `semantic_type` annotations, XCP enables automated reasoning, semantic validation, and unambiguous interpretation of data. This approach eliminates guesswork in data integration and empowers downstream services to perform context-aware processing without manual mappings.

### 3.2 Self-Descriptive Messages
XCP envelopes carry comprehensive metadata—model identifiers, schema versions, timestamps, units of measure, and provenance information—alongside the payload. The `xcp_header` and `xcp_body` structures are designed so that any endpoint can validate, interpret, and audit messages solely from their contents. This self-description fosters robust interoperability and traceability, reducing the need for out-of-band documentation.

### 3.3 Transport Agnostic
Rather than tying semantics to a single transport, XCP abstracts the underlying layer so messages can traverse TCP, UDP, MQTT, WebSockets, or proprietary transports without modification. Uniform URI-based addressing and session identifiers provide a consistent communication interface, allowing XCP to act as a universal envelope over existing protocols and simplifying cross-protocol workflows.

### 3.4 Dynamic Discovery
At runtime, XCP endpoints can discover and negotiate capabilities, schemas, and quality-of-service parameters. Through a decentralized model registry, clients fetch and cache content models on demand; versioning and compatibility rules ensure graceful evolution of data contracts. This dynamic discovery eliminates brittle pre-deployment agreements and supports plug-and-play extensibility in evolving ecosystems.

### 3.5 Integrated Security
Security is baked into the protocol rather than bolted on. XCP supports OAuth2/JWT authentication tokens, per-message HMAC or digital signatures for integrity, and optional end-to-end encryption of sensitive fields. Fine-grained access control policies can be enforced at the message, field, or method level, ensuring that only authorized entities can publish, subscribe, or interpret semantic content.

---

## 4. Protocol Architecture

### 4.1 Transport Layer
- **Session Management**: Global session identifiers, timeout/reconnect semantics.  
- **Addressing**: Uniform URIs (`xcp://`, `mqtt://`, `coap://`).  
- **Messaging Patterns**: Built-in support for request/response and publish/subscribe.

### 4.2 Content Modeling Layer (Extended MCP)
- **Model Registry**: Decentralized model repository with version control (e.g., Eclipse Vorto [13]).
- **Schema Modularity**: Inheritance, mixins, and dynamic schema links.
- **Payload Formats**: JSON-LD, CBOR, and optional Protobuf extensions.
- **Model Registry**: Decentralized model repository with version control.  
- **Schema Modularity**: Inheritance, mixins, and dynamic schema links.  
- **Payload Formats**: JSON-LD, CBOR, and optional Protobuf extensions.

### 4.3 Semantic Layer
- **Ontology Integration**: Mandatory `@context` URIs, semantic type annotations.  
- **Capability Discovery**: Queryable endpoint profiles, runtime negotiation APIs.

### 4.4 Security & Governance
- **Authentication**: OAuth2/JWT tokens in headers.  
- **Integrity**: HMAC or digital signature.  
- **Access Control**: Policy-driven at message, field, or method granularity.

### 4.5 Gateway Layer
XCP gateways are the translators and traffic directors of the protocol. They work in three simple steps:

1. **Receive** a message from any protocol (e.g., MQTT, HTTP, CoAP, gRPC).
2. **Normalize** the data into a standard XCP envelope:
   - Record where it came from (`origin_protocol`).
   - Wrap the payload in `xcp_body` with semantic tags.
3. **Forward** the enriched message:
   - To an XCP endpoint over the chosen transport, or
   - Translate it back into another protocol by setting `target_protocol` and outputting the native format.

Inside the envelope, the `gateway_metadata` section logs:
- `conversion_timestamp`: when the translation happened.
- `conversion_status`: success or error.
- `notes`: any extra details for auditing.

With an XCP gateway, you can connect existing systems to XCP with no changes to your application code.---

## 5. Example Messages

### 5.1 MQTT → XCP
**Input (MQTT Telemetry):**
```json
{
  "topic": "sensor001/telemetry",
  "payload": {
    "temperature": 23.7,
    "unit": "Celsius",
    "humidity": 56.2,
    "unit": "Percent",
    "timestamp": "2025-04-28T15:29:58Z"
  }
}
```
**Output (XCP Message):**
```jsonc
{
  // XCP envelope for MQTT data
  "xcp_header": {
    "version": "1.0",                      // Protocol version
    "message_id": "123e4567-e89b-12d3-a456-426614174000",  // Unique ID
    "session_id": "session-mqtt-001",       // Session context
    "timestamp": "2025-04-28T15:30:00Z",     // Processing time
    "from": "mqtt://sensor001/telemetry",   // Source URI
    "to": "xcp://weather_collector/ingest", // Target URI
    "origin_protocol": "MQTT",
    "target_protocol": "XCP",
    "method": "POST",
    "priority": "normal",
    "auth_token": "Bearer eyJhbGciOi..."
  },
  "xcp_body": {
    "model_id": "sensor.temperature.v1",   // Content model
    "schema_version": "1.2",
    "telemetry": {
      "ambient_temperature": {
        "value": 23.7,
        "unit": "Celsius",
        "semantic_type": "AmbientTemperature",
        "timestamp": "2025-04-28T15:29:58Z",
        "accuracy": "±0.5°C"
      },
      "humidity": {
        "value": 56.2,
        "unit": "Percent",
        "semantic_type": "RelativeHumidity",
        "timestamp": "2025-04-28T15:29:58Z"
      }
    },
    "gateway_metadata": {
      "conversion_timestamp": "2025-04-28T15:30:00Z",
      "conversion_status": "Success",
      "notes": "MQTT → XCP normalization"
    }
  }
}
```

### 5.2 HTTP POST → XCP
**Input (HTTP JSON POST):**
```http
POST /api/v1/readings HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "deviceId": "sensorA",
  "metrics": { "vibration": 5.3, "unit": "m/s²" },
  "ts": "2025-04-28T16:00:00Z"
}
```
**Output (XCP Message):**
```jsonc
{
  // XCP envelope for HTTP data
  "xcp_header": {
    "version": "1.0",
    "message_id": "223e4567-e89b-12d3-a456-426614174001",
    "session_id": "session-http-002",
    "timestamp": "2025-04-28T16:00:05Z",
    "from": "http://api.example.com/api/v1/readings",
    "to": "xcp://factory_monitor/ingest",
    "origin_protocol": "HTTP",
    "target_protocol": "XCP",
    "method": "POST",
    "priority": "high",
    "auth_token": "Bearer eyJtc29m..."
  },
  "xcp_body": {
    "model_id": "sensor.vibration.v1",
    "schema_version": "2.0",
    "telemetry": {
      "vibration": {
        "value": 5.3,
        "unit": "m/s²",
        "semantic_type": "VibrationAcceleration",
        "timestamp": "2025-04-28T16:00:00Z"
      }
    },
    "gateway_metadata": {
      "conversion_timestamp": "2025-04-28T16:00:05Z",
      "conversion_status": "Success",
      "notes": "HTTP → XCP normalization"
    }
  }
}
```

### 5.3 CoAP → XCP
**Input (CoAP PUT):**
```coap
PUT coap://node123/data
{
  "soilMoisture": 0.42,
  "timestamp": "2025-04-28T16:05:00Z"
}
```
**Output (XCP Message):**
```jsonc
{
  // XCP envelope for CoAP data
  "xcp_header": {
    "version": "1.0",
    "message_id": "323e4567-e89b-12d3-a456-426614174002",
    "session_id": "session-coap-003",
    "timestamp": "2025-04-28T16:05:02Z",
    "from": "coap://node123/data",
    "to": "xcp://agri_processor/ingest",
    "origin_protocol": "CoAP",
    "target_protocol": "XCP",
    "method": "PUT",
    "priority": "normal"
  },
  "xcp_body": {
    "model_id": "sensor.soilMoisture.v1",
    "schema_version": "1.0",
    "telemetry": {
      "soilMoisture": {
        "value": 0.42,
        "unit": "Ratio",
        "semantic_type": "SoilMoisture",
        "timestamp": "2025-04-28T16:05:00Z"
      }
    },
    "gateway_metadata": {
      "conversion_timestamp": "2025-04-28T16:05:02Z",
      "conversion_status": "Success",
      "notes": "CoAP → XCP normalization"
    }
  }
}
```

### 5.4 gRPC → XCP
**Input (gRPC Request):**
```protobuf
service SensorService {
  rpc SendData(DataRequest) returns (Ack) {}
}
message DataRequest {
  string id = 1;
  double pressure = 2;
  string ts = 3;
}
```
```jsonc
// DataRequest payload
{ "id": "sensorB", "pressure": 101.4, "ts": "2025-04-28T16:10:00Z" }
```
**Output (XCP Message):**
```jsonc
{
  // XCP envelope for gRPC data
  "xcp_header": {
    "version": "1.0",
    "message_id": "423e4567-e89b-12d3-a456-426614174003",
    "session_id": "session-grpc-004",
    "timestamp": "2025-04-28T16:10:01Z",
    "from": "grpc://SensorService.SendData",
    "to": "xcp://pressure_collector/ingest",
    "origin_protocol": "gRPC",
    "target_protocol": "XCP",
    "method": "SendData",
    "priority": "normal"
  },
  "xcp_body": {
    "model_id": "sensor.pressure.v1",
    "schema_version": "1.1",
    "telemetry": {
      "pressure": {
        "value": 101.4,
        "unit": "kPa",
        "semantic_type": "AtmosphericPressure",
        "timestamp": "2025-04-28T16:10:00Z"
      }
    },
    "gateway_metadata": {
      "conversion_timestamp": "2025-04-28T16:10:01Z",
      "conversion_status": "Success",
      "notes": "gRPC → XCP normalization"
    }
  }
}
```

$1
### 5.6 XCP → Native Protocol

#### 5.6.1 XCP → MQTT
**Input (XCP Message):**
```jsonc
{
  "xcp_header": {
    "from": "mqtt://sensor001/telemetry",
    "origin_protocol": "MQTT",
    "target_protocol": "XCP"
  },
  "xcp_body": {
    "telemetry": {
      "ambient_temperature": {
        "value": 23.7,
        "unit": "Celsius",
        "timestamp": "2025-04-28T15:29:58Z"
      },
      "humidity": {
        "value": 56.2,
        "unit": "Percent",
        "timestamp": "2025-04-28T15:29:58Z"
      }
    }
  }
}
```
**Output (MQTT Publish):**
```json
{
  "topic": "sensor001/telemetry",
  "payload": {
    "temperature": 23.7,
    "unit": "Celsius",
    "humidity": 56.2,
    "unit": "Percent",
    "timestamp": "2025-04-28T15:29:58Z"
  }
}
```

#### 5.6.2 XCP → HTTP
**Input (XCP Message):**
```jsonc
{
  "xcp_header": {
    "from": "http://api.example.com/api/v1/readings",
    "origin_protocol": "HTTP",
    "target_protocol": "XCP"
  },
  "xcp_body": {
    "telemetry": {
      "vibration": { "value": 5.3, "unit": "m/s²", "timestamp": "2025-04-28T16:00:00Z" }
    }
  }
}
```
**Output (HTTP POST):**
```http
POST /api/v1/readings HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "vibration": 5.3,
  "unit": "m/s²",
  "timestamp": "2025-04-28T16:00:00Z"
}
```

#### 5.6.3 XCP → CoAP
**Input (XCP Message):**
```jsonc
{
  "xcp_header": { "from": "coap://node123/data", "origin_protocol": "CoAP", "target_protocol": "XCP" },
  "xcp_body": { "telemetry": { "soilMoisture": { "value": 0.42, "unit": "Ratio", "timestamp": "2025-04-28T16:05:00Z" } } }
}
```
**Output (CoAP PUT):**
```coap
PUT coap://node123/data
{
  "soilMoisture": 0.42,
  "timestamp": "2025-04-28T16:05:00Z"
}
```

#### 5.6.4 XCP → gRPC
**Input (XCP Message):**
```jsonc
{
  "xcp_header": { "from": "grpc://SensorService.SendData", "origin_protocol": "gRPC", "target_protocol": "XCP" },
  "xcp_body": { "telemetry": { "pressure": { "value": 101.4, "unit": "kPa", "timestamp": "2025-04-28T16:10:00Z" } } }
}
```
**Output (gRPC DataRequest):**
```protobuf
DataRequest {
  id: "sensorB"
  pressure: 101.4
  ts: "2025-04-28T16:10:00Z"
}
```

## 6. Deployment Scenarios

Below are illustrative use cases demonstrating how XCP’s features enable robust, semantically rich communication across diverse domains.

### 6.1 Industrial IoT (Factory Automation)
In a modern manufacturing facility, hundreds of sensors and actuators from multiple vendors must interoperate seamlessly. An XCP gateway on the edge automatically ingests raw MQTT telemetry from temperature, vibration, and pressure sensors, enriches each message with semantic annotations (e.g., `MachineState`, `ProcessTemperature`), and forwards them via XCP to a centralized monitoring service. When a new device is added, its self-descriptive model is fetched from the registry, eliminating custom adapter development.

### 6.2 Smart Cities (Urban Infrastructure)
Traffic lights, environmental monitors, and public transit systems publish data over HTTP and CoAP. An XCP gateway normalizes these varying transports into a unified semantic stream, tagging each message with location ontology (e.g., `geo:Point`) and context (e.g., `AirQualityIndex`, `VehicleCount`). City planners subscribe to an XCP topic to receive real-time, semantically consistent feeds, enabling dynamic traffic signal adjustments and pollution alerts without manual data wrangling.

### 6.3 Autonomous Systems (Robotic Swarms)
In a warehouse robotics swarm, each robot exposes its capabilities (navigation, payload capacity, battery level) via XCP models. Task coordinators query robot profiles at runtime, negotiating optimal assignments based on `semantic_type` priorities (e.g., `HighPayload`, `LowBattery` handling). Commands are issued over publish/subscribe XCP channels, and robots respond with self-descriptive status updates, facilitating resilient, decentralized orchestration.

### 6.4 Healthcare (Wearables and Diagnostics)
Wearable health monitors stream ECG, blood oxygen, and motion data. An XCP gateway at the hospital boundary validates each measurement against its schema, flags out-of-range values, and routes the enriched telemetry to clinical decision support systems. Semantic metadata (e.g., `HeartRateVariability`, `Oximetry`) ensures that downstream analytics pipelines can automatically interpret and correlate multi-modal data for patient monitoring and alerting.

### 6.5 Cloud & Edge Microservices
Microservices in a hybrid cloud/edge deployment exchange configuration and telemetry via REST and gRPC. XCP sidecars attached to each service negotiate schemas and dynamically update models when new fields are introduced. This enables blue-green deployments and canary releases without downtime, as each service instance negotiates its capabilities and adapts to evolving data contracts in real time.

### 6.6 DAOs & Smart Contracts (Blockchain-Driven Services)
Distributed Autonomous Organizations define service-level agreements as XCP semantic contracts rather than hard-coded code. Smart contracts query off-chain XCP gateways to verify service capabilities and negotiate terms (e.g., `ComputeUnits`, `StorageCapacity`). Once agreed, the contracts autonomously invoke services via XCP, embedding cryptographic proofs in `gateway_metadata` for auditability.

## 7. Conclusion

XCP unifies transport, content modeling, and semantics into a single, extensible protocol, providing a “semantic Esperanto” for machines. By leveraging existing protocols as transport backbones and adding a robust semantic gateway layer, XCP delivers future-proof interoperability, dynamic extensibility, and built-in trust—empowering the next generation of intelligent, decentralized ecosystems.

---

## References

1. [Hypertext Transfer Protocol – HTTP/1.1](https://datatracker.ietf.org/doc/html/rfc2616) (Fielding et al., 1999).
2. [File Transfer Protocol (FTP)](https://datatracker.ietf.org/doc/html/rfc959) (Postel, 1985).
3. [Simple Mail Transfer Protocol (SMTP)](https://datatracker.ietf.org/doc/html/rfc5321) (Postel, 2008).
4. [MQTT Version 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html) (OASIS, 2014).
5. [The Constrained Application Protocol (CoAP)](https://datatracker.ietf.org/doc/html/rfc7252) (Shelby et al., 2014).
6. [gRPC: A High-Performance, Open-Source Universal RPC Framework](https://grpc.io/docs/) (Google, 2015).
7. [Model Content Protocol Specification Draft](#) (MCP Initiative, 2024).
8. [Hypertext Transfer Protocol Version 2 (HTTP/2)](https://datatracker.ietf.org/doc/html/rfc7540) (Belshe et al., 2015).
9. [Hypertext Transfer Protocol Version 3 (HTTP/3)](https://datatracker.ietf.org/doc/html/rfc9114) (Bishop, 2022).
10. [State of the Connected World: 2023 Edition](https://www.weforum.org/reports/state-of-the-connected-world-2023-edition) (World Economic Forum).
11. [Web of Things (WoT) Architecture and Thing Description](https://www.w3.org/TR/wot-architecture/) (W3C, 2020).
12. [Deterministic Networking Use Cases](https://datatracker.ietf.org/doc/html/rfc8578) (IETF, 2019).
13. [Eclipse Vorto: Information Model Repository and Toolset](https://vorto.eclipse.org/) (Eclipse Foundation, 2021).
14. [The MIT License (MIT)](https://opensource.org/licenses/MIT) (Open Source Initiative).
15. [Future Trends in Internet of Things Standardization](https://www.comsoc.org/publications/ctn/future-trends-internet-things-standardization) (IEEE Communications Society, 2023).

---

## License

Creative Commons Attribution 4.0 International (CC BY 4.0)

Copyright © 2025 Rogério Figurelli

This repository contains original written and graphical materials (the “Work”),
including—but not limited to—white papers, articles, diagrams, and supporting files
that disclose conceptual frameworks and reference architectures.

You are free to:

• Share — copy and redistribute the Work in any medium or format  
• Adapt — remix, transform, and build upon the Work for any purpose, even commercially  

Under the following terms:

1. Attribution — Cite “Rogério Figurelli”, link to this license, and state if
   changes were made.  
   Preferred citation: Figurelli, R. “<Title>”, v <version>, <year>, URL/DOI.

2. No additional restrictions — You may not apply legal terms or technological
   measures that legally restrict others from doing anything the license permits.

The full legal text of CC BY 4.0 is available at:  
<https://creativecommons.org/licenses/by/4.0/legalcode>

THE WORK IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED,
INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A
PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHOR OR COPYRIGHT
HOLDER BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
WORK OR THE USE OR OTHER DEALINGS IN THE WORK.


