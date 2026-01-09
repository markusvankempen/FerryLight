# FerryLight System: A Comprehensive Briefing

## Executive Summary

FerryLight is a comprehensive, real-time community information system designed for the Englishtown ↔ Jersey Cove ferry route in Nova Scotia, Canada. The system's primary objective is to eliminate travel uncertainty by providing live ferry status, vessel tracking, and wait time estimations, thereby helping users decide between waiting for the 3-5 minute ferry ride or taking the alternative 25-minute St. Ann's Loop. Beyond ferry monitoring, FerryLight functions as a central community hub, integrating local weather data, community event calendars, a directory of local businesses, and even marine wildlife tracking.

The system is built on a modern, layered architecture featuring a React Progressive Web App (PWA) for user access and a unique physical WLED matrix display for at-a-glance status, aimed particularly at older residents. Data is ingested from diverse sources, including a local AIS receiver, a weather station, Google Sheets, and various APIs. This data is processed and orchestrated by a Node-RED integration hub, with an Express.js backend and a PostgreSQL database providing persistence and an API gateway. The entire system is containerized with Docker and deployed on a VPS, showcasing a robust, scalable, and resilient design for delivering critical, real-time information to a local community.

---

## 1. Project Overview and Purpose

FerryLight is a multi-faceted real-time monitoring and information system focused on the Englishtown ↔ Jersey Cove ferry service in Nova Scotia. The project, currently in Beta as version 5.2.5, was created by Markus van Kempen to address the practical challenge faced by the local community: the uncertainty of the ferry's operational status. A service disruption requires a 22-25 minute detour via the St. Ann's Loop, making real-time information highly valuable.

The system has evolved from a simple status checker into a comprehensive community resource, combining official government data with information from a custom-built DIY weather station and local sensors.

| Property | Value |
|----------|-------|
| **Project Name** | FerryLight |
| **Version** | 5.2.5 (Beta) |
| **Author** | Markus van Kempen |
| **Production URL** | https://ferrylight.online |
| **GitHub Repository** | markusvankempen-ai/FerryLight |

---

## 2. Core Features and Functionality

The FerryLight system offers a wide array of features that extend beyond basic ferry tracking, establishing it as a central hub for community information.

| Feature Category | Description |
|------------------|-------------|
| 🚢 **Real-Time Ferry Tracking** | Utilizes AIS (Automatic Identification System) data to provide live status updates and position tracking of the primary ferry, the TORQUIL MACLEAN (MMSI: 316023888). An interactive Leaflet map displays the vessel's real-time position. |
| 🌤 **Weather Integration** | Aggregates data from a local weather station and supplements it with critical alerts from Environment Canada. Users can view temperature, humidity, wind speed/direction, UV index, and pressure. |
| 📅 **Community Events** | Acts as a community calendar, integrating with Google Calendar and Google Sheets to display local events, garbage schedules (Mon for Englishtown, Tue for Northshore), Nova Scotia holidays, church services, and music events. |
| 💡 **LED Physical Display** | A key feature is a physical WLED-based LED matrix display designed to show ferry status, wait times, and weather alerts. This component specifically targets older community members who may not use the web application. |
| 📱 **Progressive Web App (PWA)** | The primary user interface is an installable PWA with offline support, ensuring access to cached data even without an internet connection. It is compatible with desktop, Android, and iOS devices. |
| 🔔 **Push Notifications** | A smart, multi-category notification system sends alerts for critical events like ferry service suspensions/resumptions, new weather warnings, and new community announcements. |
| 🦈 **Maritime Environment Monitoring** | The system tracks all AIS-equipped vessels in the area, including tour boats and fishing vessels. It also integrates with the OCEARCH API to display the last known positions of tagged marine animals, primarily sharks. |
| ✈️ **Aircraft Tracking** | As an additional feature, the application tracks and displays planes in the St. Ann's Bay area using ADS-B (Automatic Dependent Surveillance–Broadcast) data. |
| 🎵 **Cape Breton Music** | A "gimmick" feature offers a selection of traditional Celtic and Fiddler tunes for users to listen to while waiting in the ferry lineup. |
| 🏪 **Local Business Directory** | Provides a directory of local shops and services in the Northshore area, categorized by type (restaurants, accommodations, attractions, etc.). |

---

## 3. System Architecture and Technology Stack

FerryLight is built on a layered architecture designed for real-time data flow, resilience, and scalability. The architecture can be broken down into four primary stages: data ingestion, integration/processing, backend/persistence, and client presentation.

### 3.1. Architectural Flow: From Signal to Screen

1. **Data Sources**: Real-world signals are captured from diverse sources like an AIS antenna, a local weather station, and web APIs.
2. **Integration & Processing**: Node-RED acts as a central hub to ingest, process, enrich, and route this raw data.
3. **Backend & Persistence**: An Express.js server provides an API gateway and business logic, while a PostgreSQL database stores historical data and current state.
4. **Clients & Consumers**: The processed information is delivered to end-users via the React PWA, the physical WLED display, and push notifications.

### 3.2. Data Ingestion Sources

- **AIS Radio Receiver**: A local RTL-SDR receiver captures real-time AIS signals at 161.975/162.025 MHz.
- **Local Weather Station**: Provides real-time local weather metrics.
- **Environment Canada**: Public feeds are used for official weather alerts.
- **Google Sheets & Calendar**: Serve as a simple CMS for community events, announcements, and schedules.
- **OCEARCH API**: Provides live tracking data for tagged marine wildlife.
- **511 Ferry Status API**: An external source for ferry status information.

### 3.3. Integration Hub: Node-RED

Node-RED is the "central nervous system" of the architecture, handling critical data orchestration tasks.

- **Data Ingestion & Routing**: Acts as the primary endpoint for AIS signals and weather data.
- **Real-Time Logic**: Performs location-based status determination using a 30m geofence around ports to automatically track trip starts and ends.
- **Data Enrichment**: Processes raw AIS data to extract key fields (MMSI, position, speed) and formats it for storage and API delivery.
- **Analytics Engine**: Calculates operational metrics like hourly trip counts.
- **Communication Gateway**: Manages push notification subscriptions and bridges data to the MQTT message bus for IoT devices.

### 3.4. Backend and Persistence

#### Express.js Backend Server
- **API Gateway**: Provides a set of RESTful endpoints for the frontend application.
- **Proactive Monitoring**: A background process polls for system state changes every 2 minutes to identify events and trigger notifications.
- **Business Logic**: Executes server-side logic, including the 3-factor Smart Busyness Analysis Algorithm.
- **Proxy**: Includes a WebSocket proxy to the WLED display.

#### PostgreSQL Database

Serves as the system's memory, providing a structured repository for both ephemeral real-time positions and long-term service records.

| Table Name | Purpose |
|------------|---------|
| `ais_data` | Stores raw, historical AIS messages for all tracked vessels. |
| `ais_latest_positions` | An optimized table holding only the most recent position for fast lookups. |
| `ferry_outages` | A dedicated log of all service interruptions and resumptions with timestamps. |
| `push_subscriptions` | Securely stores user subscription endpoints for the Web Push API. |
| `orders` | Manages e-commerce order information. |
| `audit_trail` | Logs significant system changes and events. |

### 3.5. Technology Stack Summary

| Layer | Technology |
|-------|------------|
| Frontend | React 18, Styled Components, Framer Motion, Leaflet |
| Backend | Node.js, Express.js |
| Integration | Node-RED |
| Database | PostgreSQL |
| Messaging | MQTT, WebSockets |
| Push | Web Push API, VAPID |
| Containerization | Docker, Docker Compose |
| Reverse Proxy | Traefik |
| PWA | Service Workers, Web App Manifest |

---

## 4. Key Technical Implementations

### 4.1. Push Notification System

The system uses the Web Push API with VAPID authentication. A server-side monitoring loop checks for status changes every two minutes and sends deduplicated notifications.

| Category | Notification Types | Priority |
|----------|-------------------|----------|
| **Ferry** | Suspended, Resumed, Daily Outage Reminder | Critical/High |
| **Weather** | New Alert Issued, Alert Cleared | Medium |
| **Events** | New Community Announcements (via EventsSheetEmail) | Medium |
| **Orders** | E-commerce Order Confirmation, Shipping Updates | Low |

### 4.2. Physical WLED Display

This IoT component extends the digital service into the physical world.

- **Purpose**: To provide at-a-glance ferry status for residents, especially older individuals, who may not use the web app. It is designed to be placed at key junctions roughly a 10-minute drive from the ferry.
- **Hardware**: WLED-compatible ESP32 or ESP8266 controller with a 32x8 or 64x8 pixel LED matrix.
- **Setup**: Users connect to a "FerryLight" WiFi network broadcast by the device to configure it for their home network via a dedicated setup web app. Status LEDs indicate the mode:
  - 🔴 **RED** - Setup mode
  - 🔵 **BLUE** - Test mode
  - 🟢 **GREEN** - Complete
- **Updates**: An MQTT-to-WebSocket bridge in Node-RED pushes real-time status updates as JSON commands, which the display shows as scrolling text.

### 4.3. Business Logic and Algorithms

#### Weather Cancellation Rules

Ferry service is automatically flagged for cancellation under specific conditions:
- Wind speed > 50 km/h
- Visibility < 1 km
- Wave height > 3 meters
- Presence of ice conditions

#### Smart Busyness Algorithm

A three-factor scoring system estimates wait times:

| Factor | Weight | Description |
|--------|--------|-------------|
| Trip Frequency | 60% | Compares current trips to the expected number |
| Docking Efficiency | 30% | Measures turnaround times at the dock |
| Historical Context | 10% | Compares current activity to the weekly average |

**Busyness Score Mapping:**
- **0-25**: 🟢 Quiet
- **26-50**: 🟡 Normal
- **51-75**: 🟠 Busy
- **76-100**: 🔴 Very Busy

---

## 5. Deployment and Operations

The entire system is containerized for resilience and ease of management, deployed on a Virtual Private Server (VPS).

- **Containerization**: Docker and Docker Compose are used to orchestrate the services (`ferrylight-app`, `postgres`, `nodered`, `traefik`, `mailserver`).
- **Docker Images**: Multiple Dockerfiles are available for different use cases, ranging from a ~250MB development image to a secure, ~100MB distroless production image.
- **Deployment**: A `deploy.sh` script handles the deployment process, which involves cloning the GitHub repository, configuring environment variables (`.env`), and running `docker-compose`.
- **Reverse Proxy**: Traefik is used for SSL termination and domain routing.

---

## 6. Operational Challenges and Future Developments

### 6.1. Identified Challenges

- **Hardware Reliability**: The local weather station is prone to freezing during winter.
- **Infrastructure**: The system is vulnerable to local internet connection and power outages.
- **Data Integrity**: The ferry captain occasionally forgets to turn on the AIS signal, requiring intervention from authorities (e.g., Coast Guard).
- **Data Sourcing**: Finding reliable government RSS feeds for information like weather alerts and fire bans is a persistent challenge.

### 6.2. Future Development: PAX Counters

To improve the accuracy of wait time estimations, a system of PAX counters is being developed.

- **Problem**: The ferry has a limited capacity of 15 cars. During peak tourist season, lineups can be long, and AIS data alone cannot determine the number of waiting vehicles.
- **Solution**: PAX counters will be deployed at each port to count Bluetooth and WiFi signals from devices in waiting cars, providing an indication of how busy each port is.
- **Technology**: Due to a lack of power and internet at the ports, the counters will transmit data via LoRaWAN. This data will be combined with AIS trip counts to calculate more precise wait times.

---

*FerryLight - Real-Time Ferry Status & Community Information for Cape Breton*
