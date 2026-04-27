# WalkSafe

WalkSafe is a mobile safety app for iOS and Android that allows users to share their live location and planned route with trusted friends while they are on the move. The app is designed for situations where someone wants extra safety and reassurance while walking, cycling, or driving from their current location to a destination.

The core idea is simple:

> Let trusted friends follow your journey in real time, see your planned route, and get automatically notified when something does not look right.

WalkSafe is not just a location-sharing app. It combines live location, route planning, route deviation detection, automatic arrival notifications, emergency alerts, and a 24-hour location timeline into one privacy-focused safety platform.

---

## Table of Contents

- [Overview](#overview)
- [Core Features](#core-features)
- [How WalkSafe Works](#how-walksafe-works)
- [Transport Modes](#transport-modes)
- [Technical Stack](#technical-stack)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Mobile App](#mobile-app)
- [Backend](#backend)
- [Routing Server](#routing-server)
- [Database Design](#database-design)
- [Live Location](#live-location)
- [Safety Engine](#safety-engine)
- [Notifications](#notifications)
- [Privacy and Security](#privacy-and-security)
- [Environment Variables](#environment-variables)
- [Local Development](#local-development)
- [Deployment](#deployment)
- [Roadmap](#roadmap)
- [Important Notes](#important-notes)

---

## Overview

WalkSafe allows a user to start a safety trip by selecting a destination and a transport mode. The app automatically uses the user's current location as the starting point. Once the trip starts, selected friends receive a notification and can follow the user live on a map.

Friends can see:

- The user's live location
- The planned route from current location to destination
- The selected transport mode
- The estimated arrival time
- Whether the user is still on the planned route
- Whether the user is delayed
- Whether the user has arrived
- Whether an emergency has been triggered

WalkSafe automatically notifies trusted contacts when:

- A user starts a trip
- A user arrives at the destination
- A user takes longer than expected
- A user strongly deviates from the route
- A user stops for too long outside the destination area
- A user activates the emergency button
- A user's battery is low during an active trip
- A user's location can no longer be updated

---

## Core Features

### 1. Live Location Sharing

Users can share their live location with selected friends during an active trip. Live location is shown on a map and updated in real time.

### 2. Planned Route Visibility

When a trip starts, WalkSafe calculates a route from the user's current location to the selected destination. Friends can see this planned route as a line on the map.

### 3. Automatic Start Location

The start location is always based on the user's current GPS location. The user only needs to enter a destination.

### 4. Transport Mode Selection

Users can choose how they are travelling:

- Walking
- Cycling
- Driving

The selected mode affects route calculation, estimated arrival time, allowed route deviation, and delay detection.

### 5. Route Deviation Detection

WalkSafe checks whether the user is still within an acceptable distance from the planned route. If the user strongly deviates for too long, trusted friends are notified.

### 6. Automatic Arrival Detection

When the user reaches the destination area, WalkSafe automatically marks the trip as arrived and notifies selected friends.

### 7. Emergency Button

A dedicated emergency button allows the user to instantly alert trusted contacts and share their current location with high priority.

### 8. Status Updates

Users can send quick updates while travelling, such as:

- "I am taking another route"
- "I am waiting somewhere"
- "I am safe"
- "Call me"
- "My battery is low"

### 9. 24-Hour Location Timeline

With explicit permission, selected trusted contacts can view the user's location timeline from the last 24 hours.

### 10. Privacy-First Permissions

Users control who can see their location, active trips, emergency alerts, and 24-hour location history.

---

## How WalkSafe Works

A typical WalkSafe trip follows this flow:

```text
1. User opens WalkSafe
2. App gets the user's current GPS location
3. User enters a destination
4. User selects walking, cycling, or driving
5. User selects which friends can follow the trip
6. User taps "I'm on my way"
7. Backend requests a route from the routing server
8. Route polyline, distance, and ETA are returned
9. Trip is stored in Firestore
10. Live location is written to Firebase Realtime Database
11. Friends receive a push notification
12. Friends can follow the user live on the map
13. Safety Engine monitors route deviation, delay, arrival, and emergency state
14. Friends receive automatic alerts when needed
```

Example notification:

```text
Leon is on his way to Home.
Mode: Cycling
Estimated arrival: 23:42
You can follow the route live in WalkSafe.
```

---

## Transport Modes

WalkSafe supports three transport modes.

### Walking

Used for short personal routes such as walking home, walking from a station, or walking through an unfamiliar area.

Typical settings:

```text
Route corridor: 50-100 meters
Arrival radius: 30-50 meters
Delay threshold: 5-10 minutes
```

### Cycling

Used for bike routes, especially in cities or between school, work, home, and friends.

Typical settings:

```text
Route corridor: 75-150 meters
Arrival radius: 50-100 meters
Delay threshold: 7-15 minutes
```

### Driving

Used for car routes. Driving allows larger route deviations because of traffic, road closures, and alternative roads.

Typical settings:

```text
Route corridor: 150-300 meters
Arrival radius: 100-200 meters
Delay threshold: 10-20 minutes
```

---

## Technical Stack

### Mobile App

```text
React Native
TypeScript
Expo Dev Client
EAS Build
react-native-maps
Google Maps SDK for iOS and Android
```

### Backend

```text
Firebase Authentication
Cloud Firestore
Firebase Realtime Database
Firebase Cloud Messaging
Cloud Functions for Firebase
Cloud Storage for Firebase
Firebase App Check
Firebase Crashlytics
Firebase Remote Config
```

### Routing

```text
OSRM for the first production routing server
Valhalla later for advanced routing and map matching
OpenStreetMap data
Docker
Node.js / TypeScript API wrapper
```

### Development Tools

```text
Cursor or VS Code
Android Studio
Xcode
Node.js LTS
Git
Docker Desktop
Firebase CLI
Expo CLI
EAS CLI
```

### Hosting

```text
Firebase for app backend
Google Cloud Run or VPS for API services
Google Compute Engine or VPS for OSRM / Valhalla routing server
```

---

## Architecture

High-level architecture:

```text
[Mobile App: iOS / Android]
React Native + Google Maps SDK
        |
        | Auth, trips, friends, permissions
        v
[Firebase Backend]
Auth + Firestore + Realtime Database + FCM
        |
        | Route request
        v
[WalkSafe API]
Node.js / TypeScript
        |
        | Calculate route
        v
[Routing Server]
OSRM / Valhalla + OpenStreetMap data
        |
        | Polyline + distance + ETA
        v
[Mobile App]
Draw route + track live location
        |
        v
[Safety Engine]
Deviation + delay + arrival + emergency detection
        |
        v
[Push Notifications]
Friends receive alerts automatically
```

WalkSafe separates visual map rendering from route calculation.

Google Maps SDK is used to display the map, markers, and polylines. Route calculation is handled by our own routing server to avoid expensive third-party route APIs and to keep full control over route logic.

---

## Project Structure

Recommended monorepo structure:

```text
walksafe/
  apps/
    mobile/
      src/
        modules/
          auth/
          friends/
          trips/
          maps/
          live-location/
          safety/
          emergency/
          timeline/
          notifications/
          settings/
        components/
        screens/
        navigation/
        services/
        utils/
      app.json
      package.json

    admin/
      src/
      package.json

  services/
    api/
      src/
        routes/
        controllers/
        services/
        safety/
        notifications/
        validation/
      package.json
      Dockerfile

    routing/
      osrm/
        docker-compose.yml
        profiles/
        data/
      valhalla/
        docker-compose.yml

  packages/
    shared/
      src/
        types/
        constants/
        validation/

    geo/
      src/
        polyline/
        distance/
        routeDeviation/
        arrivalDetection/

    ui/
      src/
        components/

  infra/
    firebase/
      firestore.rules
      database.rules.json
      storage.rules
      functions/

    docker/
    terraform/

  docs/
    architecture.md
    privacy.md
    api.md
    routing.md
    safety-engine.md

  README.md
```

---

## Mobile App

The mobile app is responsible for:

- User authentication
- Current location detection
- Destination input
- Transport mode selection
- Starting and ending trips
- Displaying the map
- Drawing the planned route
- Sending live location updates
- Showing friends' active trips
- Handling emergency actions
- Receiving push notifications
- Displaying the 24-hour timeline

Main screens:

```text
LoginScreen
HomeScreen
DestinationSearchScreen
TransportModeScreen
StartTripScreen
ActiveTripScreen
FriendLiveTrackingScreen
EmergencyScreen
TimelineScreen
FriendsScreen
PrivacySettingsScreen
NotificationSettingsScreen
```

Main app modules:

```text
auth
friends
trips
maps
live-location
safety
emergency
timeline
notifications
settings
privacy
```

---

## Backend

The backend handles trusted logic that should not run only on the client.

Backend responsibilities:

- Creating trips
- Validating permissions
- Managing friendships
- Sending notifications
- Activating SOS state
- Processing trip events
- Running safety checks
- Writing audit logs
- Protecting sensitive fields from client manipulation

Important backend endpoints:

```http
POST /trips/start
POST /trips/:tripId/cancel
POST /trips/:tripId/arrive
POST /trips/:tripId/update-status
POST /trips/:tripId/sos
POST /routes/calculate
POST /location/update
GET  /trips/:tripId
GET  /timeline/24h
```

Some of these endpoints can be implemented as Firebase Cloud Functions. Others can live in a separate Node.js API service.

---

## Routing Server

WalkSafe uses its own routing server instead of Google Routes or Mapbox Navigation.

The routing server returns:

- Encoded route polyline
- Route distance
- Estimated duration
- Transport mode
- Route provider
- Route metadata

Example request:

```json
{
  "origin": {
    "lat": 52.0907,
    "lng": 5.1214
  },
  "destination": {
    "lat": 52.3702,
    "lng": 4.8952
  },
  "mode": "cycling"
}
```

Example response:

```json
{
  "polyline": "encoded_polyline_here",
  "distanceMeters": 48200,
  "durationSeconds": 7350,
  "mode": "cycling",
  "provider": "osrm",
  "routeVersion": "2026-04-27"
}
```

Recommended routing phases:

```text
Phase 1: OSRM for walking, cycling, and driving
Phase 2: Add better route profiles
Phase 3: Test Valhalla next to OSRM
Phase 4: Use Valhalla for map matching and safety-aware routing
```

---

## Database Design

WalkSafe uses Firestore for structured data and Firebase Realtime Database for live location.

### Firestore Collections

```text
users/{uid}
friendships/{friendshipId}
sharePermissions/{permissionId}
trips/{tripId}
tripEvents/{eventId}
locationHistory/{pointId}
notificationTokens/{uid}/tokens/{tokenId}
emergencyContacts/{uid}/contacts/{contactUid}
auditLogs/{logId}
```

### Realtime Database Paths

```text
liveTrips/{tripId}/location
liveTrips/{tripId}/status
liveTrips/{tripId}/viewers/{viewerUid}
presence/{uid}
```

---

## Live Location

Live location is stored in Firebase Realtime Database so friends can see updates quickly.

Example live location object:

```json
{
  "lat": 52.0907,
  "lng": 5.1214,
  "accuracy": 12,
  "speed": 4.2,
  "heading": 80,
  "batteryLevel": 0.42,
  "updatedAt": 1777300000000,
  "distanceFromRoute": 34,
  "distanceToDestination": 1800,
  "isMoving": true
}
```

Location update strategy:

```text
Active trip, app open: every 3-5 seconds
Active trip, background: every 10-20 seconds
User is stationary: every 30-60 seconds
24/7 sharing without active trip: every 2-5 minutes
SOS active: every 2-5 seconds if possible
Battery low: reduce frequency except during SOS
```

Location history is stored less frequently than live location to reduce cost, preserve privacy, and avoid unnecessary data storage.

---

## Safety Engine

The Safety Engine is responsible for detecting whether something unusual is happening during a trip.

It checks:

- Distance from the planned route
- Distance to destination
- Current speed
- Direction of movement
- GPS accuracy
- Time outside route corridor
- Time since last movement
- Time compared to ETA
- Battery level
- Last successful location update

Example route corridor settings:

```text
Walking: 50-100 meters
Cycling: 75-150 meters
Driving: 150-300 meters
```

Deviation logic:

```text
User inside route corridor
→ status: on route

User briefly outside route corridor
→ status: minor deviation

User outside route corridor for multiple minutes
and not moving toward destination
→ status: strong deviation
→ notify trusted friends

User exceeds ETA by threshold
→ status: delayed
→ notify trusted friends

User enters destination radius
and remains there briefly
→ status: arrived
→ notify trusted friends
```

The client may calculate quick local status for UX, but final alerts should be decided by the backend to prevent manipulation.

---

## Notifications

WalkSafe uses Firebase Cloud Messaging for push notifications.

Notification types:

```ts
type NotificationType =
  | "TRIP_STARTED"
  | "ARRIVED"
  | "DELAYED"
  | "ROUTE_DEVIATION"
  | "STOPPED_TOO_LONG"
  | "SOS"
  | "BATTERY_LOW"
  | "LOCATION_LOST"
  | "USER_UPDATE"
  | "ROUTE_RECALCULATED";
```

Example notifications:

```text
Leon is on his way to Home.

Leon has arrived at the destination.

Leon is taking longer than expected.

Leon has strongly deviated from the planned route.

SOS: Leon needs help. Open WalkSafe to view live location.
```

---

## Privacy and Security

WalkSafe must be privacy-first by design.

Core privacy principles:

- Users decide who can see their location
- Friends must be accepted before access is granted
- Active trip sharing is separate from 24-hour timeline sharing
- Users can stop sharing at any time
- Users can block or remove friends
- Location history should expire automatically
- Sensitive actions should be logged
- No hidden tracking
- No public location profiles
- No silent location sharing

Security measures:

```text
Firebase Security Rules
Firebase App Check
Server-side validation
Rate limiting
Audit logs
Permission checks
Friend request limits
Blocked user enforcement
Secure token storage
Encrypted transport over HTTPS
Minimal location history retention
```

The app should always show who can currently view the user's location.

Example UI message:

```text
Your location is currently shared with:
Sara, Milan, Noor
```

There should always be a clear option to stop sharing.

---

## Environment Variables

Example environment variables:

```env
# Firebase
EXPO_PUBLIC_FIREBASE_API_KEY=
EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN=
EXPO_PUBLIC_FIREBASE_PROJECT_ID=
EXPO_PUBLIC_FIREBASE_STORAGE_BUCKET=
EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=
EXPO_PUBLIC_FIREBASE_APP_ID=
EXPO_PUBLIC_FIREBASE_DATABASE_URL=

# Google Maps
EXPO_PUBLIC_GOOGLE_MAPS_IOS_API_KEY=
EXPO_PUBLIC_GOOGLE_MAPS_ANDROID_API_KEY=

# API
EXPO_PUBLIC_WALKSAFE_API_URL=http://localhost:3000

# Routing
ROUTING_PROVIDER=osrm
OSRM_DRIVING_URL=http://localhost:5000
OSRM_CYCLING_URL=http://localhost:5001
OSRM_WALKING_URL=http://localhost:5002

# App
APP_ENV=development
```

Never commit production secrets to GitHub.

---

## Local Development

### Prerequisites

Install:

```text
Node.js LTS
Git
Docker Desktop
Firebase CLI
Expo CLI
EAS CLI
Android Studio
Xcode, macOS only
Cursor or VS Code
```

### Clone the repository

```bash
git clone https://github.com/your-org/walksafe.git
cd walksafe
```

### Install dependencies

```bash
npm install
```

Or, if using pnpm:

```bash
pnpm install
```

### Start the mobile app

```bash
cd apps/mobile
npx expo start
```

For development builds:

```bash
npx expo run:ios
npx expo run:android
```

### Start Firebase emulators

```bash
firebase emulators:start
```

### Start the API service

```bash
cd services/api
npm run dev
```

### Start OSRM locally

Example placeholder command:

```bash
cd services/routing/osrm
docker compose up
```

Actual OSRM setup requires OpenStreetMap data extraction and profile configuration.

---

## Deployment

### Mobile App

Use EAS Build:

```bash
cd apps/mobile
eas build --platform ios
eas build --platform android
```

For internal testing:

```bash
eas submit --platform ios
eas submit --platform android
```

### Firebase

Deploy Firestore rules, Realtime Database rules, Cloud Functions, and Storage rules:

```bash
firebase deploy
```

### API Service

Build and deploy the Node.js API as a container:

```bash
docker build -t walksafe-api .
docker run -p 3000:3000 walksafe-api
```

Production options:

```text
Google Cloud Run
VPS
Google Compute Engine
Kubernetes later if needed
```

### Routing Server

Production routing server options:

```text
Single VPS for Netherlands / Benelux MVP
Google Compute Engine for scalable deployment
Separate OSRM instances per transport mode
Valhalla cluster later for advanced routing
```

---

## Roadmap

### Phase 1: Foundation

- Set up React Native app
- Set up Firebase project
- Add authentication
- Add Google Maps SDK
- Show current user location
- Set up Firestore and Realtime Database rules
- Set up push notification infrastructure

### Phase 2: Basic Trip Flow

- Enter destination
- Select transport mode
- Calculate route using routing server
- Draw route polyline on map
- Start trip
- Share live location with selected friends
- Show active trip to friends

### Phase 3: Safety Engine

- Detect route deviation
- Detect delay
- Detect long stops
- Detect arrival
- Send automatic notifications
- Add alert levels

### Phase 4: Emergency Mode

- Add emergency button
- Notify emergency contacts
- Increase location update priority during SOS
- Add emergency screen for friends
- Add false alarm / resolved flow

### Phase 5: Timeline and Privacy

- Add 24-hour location history
- Add per-friend timeline permissions
- Add automatic data cleanup
- Add privacy dashboard
- Add audit logs

### Phase 6: Production Hardening

- Improve background location reliability
- Optimize battery usage
- Add Crashlytics
- Add analytics
- Add App Check
- Add rate limiting
- Add abuse prevention
- Prepare App Store and Play Store release

### Phase 7: Advanced Features

- Valhalla routing
- Map matching
- Safer route recommendations
- Safe zones
- Fake call
- Code word system
- Emergency audio/video recording
- Temporary web tracking link
- Group trips
- Festival mode

---

## Important Notes

WalkSafe is a safety-support app, not a replacement for emergency services.

The app should clearly communicate:

- Location sharing depends on phone GPS, internet, battery, permissions, and operating system limitations.
- Background location may be limited by iOS, Android, battery saver settings, or user permissions.
- Emergency alerts depend on push notification delivery and network availability.
- Users should still contact local emergency services in dangerous situations.

WalkSafe should be designed to improve safety and reassurance, but it cannot guarantee safety in every situation.

---

## License

This project is currently private. License information should be added before public release.

---

## Status

Current status:

```text
Concept and technical architecture phase
```

Planned next step:

```text
Build the first vertical slice:
current location → destination → transport mode → route calculation → route display → live sharing → arrival notification
```
