# OpenELIS-Global-2 Setup and Configuration Instructions

## Overview
This document provides instructions for setting up and configuring the OpenELIS-Global-2 application, including Docker-based development environment, UI rebranding, FHIR integration for patient data retrieval, and current implementation status.

## Prerequisites
- Docker and Docker Compose installed
- Git for version control
- Node.js and npm for frontend development (if needed)
- Java 21 and Maven for backend builds

## Environment Setup

### 1. Clone and Setup Repository
```bash
git clone https://github.com/pc-dissanayake/OpenELIS-Global-NHSL.git
cd OpenELIS-Global-NHSL
git checkout NHSL
```

### 2. Start Development Environment
```bash
# Start all services using Docker Compose
docker compose -f dev.docker-compose.yml up -d
```

This will start:
- OpenELIS web application (Tomcat)
- PostgreSQL database
- FHIR server (HAPI FHIR)
- Nginx proxy

### 3. Access the Application
- Main application: https://localhost
- FHIR server: http://localhost:8080/fhir

## Configuration Changes

### UI Rebranding
The application has been rebranded from "Test LIMS" to "NHSL LIS":
- Updated `BANNER_TEXT` in `/opt/workspace/OpenELIS-Global-2/volume/properties/SystemConfiguration.properties`
- Set for both English (`en`) and French (`fr`) locales

### External Orders Configuration
- Set `ACCEPT_EXTERNAL_ORDERS=false` in SystemConfiguration.properties

## FHIR Integration

### Backend Implementation
A new REST controller `FhirProxyController.java` has been added to proxy FHIR requests:
- Location: `/opt/workspace/OpenELIS-Global-2/src/main/java/org/openelisglobal/common/rest/FhirProxyController.java`
- Endpoint: `GET /rest/fhir/patients`
- Proxies to local FHIR server: `http://fhir.openelis.org:8080/fhir/Patient?`

### Frontend Implementation
The `AddOrder.js` component has been modified to display recent patients:
- Fetches last 50 patients from FHIR via `/rest/fhir/patients`
- Displays table with ID, Name, and Last Updated columns
- Handles loading states and error cases

### Current Status
- Backend proxy controller implemented and compiled
- Frontend UI updated with patient table
- Code committed and pushed to NHSL branch
- Testing shows endpoint returns data, but currently serving HTML instead of JSON (potential nginx routing issue)

## Building and Deployment

### Build Backend (if needed)
```bash
# Build dataexport modules first
cd dataexport
mvn clean install -DskipTests

# Build main application
cd ..
mvn clean package -DskipTests

# Update Docker container with new WAR
docker compose -f dev.docker-compose.yml up --build -d oe.openelis.org
```

### Restart Services
```bash
# Restart web application
docker compose -f dev.docker-compose.yml restart oe.openelis.org

# Restart all services
docker compose -f dev.docker-compose.yml restart
```

## Testing

### Test FHIR Endpoint
```bash
# Test proxy endpoint
curl -k 'https://localhost/rest/fhir/patients'
```

Expected: JSON response with FHIR Bundle containing patient data
Current: Returns HTML (React app fallback) - indicates routing issue

### Verify Configuration
- Check banner text displays "NHSL LIS"
- Verify external orders are disabled
- Confirm patient table loads in Sample Patient Entry page

## Known Issues and Next Steps

### Current Issues
1. `/rest/fhir/patients` endpoint returns HTML instead of JSON
   - Likely nginx configuration routing API calls to frontend
   - May need to update nginx-prod.conf to proxy `/rest/` paths correctly

### Pending Tasks
1. Debug and fix nginx routing for `/rest/` API endpoints
2. Verify patient data parsing and display
3. Test end-to-end patient selection functionality
4. Optimize patient loading (limit to 50 most recent)

## File Structure
```
/opt/workspace/OpenELIS-Global-2/
├── volume/properties/SystemConfiguration.properties  # Configuration
├── src/main/java/org/openelisglobal/common/rest/FhirProxyController.java  # FHIR proxy
├── frontend/src/components/addOrder/AddOrder.js     # Updated UI
├── docker-compose.yml                                # Production setup
├── dev.docker-compose.yml                           # Development setup
└── dataexport/                                      # FHIR data export modules
```

## Git Status
- Current branch: NHSL
- Remote: origin (https://github.com/pc-dissanayake/OpenELIS-Global-NHSL.git)
- Latest commit: Update FHIR proxy to use local FHIR server endpoint

## Troubleshooting
- If builds fail, ensure dataexport modules are built first
- For SSL issues, use local FHIR URL instead of external
- Check Docker logs: `docker compose -f dev.docker-compose.yml logs [service]`
- Verify Java version: `java -version` (should be 21)


## Server Status
 Currently host in server 38.242.212.117
 