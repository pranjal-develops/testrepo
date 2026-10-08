## Overview
The OneDriveLoginTool module implements Microsoft OAuth2 Device Code Flow for authentication, permanently altering the application's authentication pipeline to eliminate manual token management. Introduced via PR #25, it provides an integrated approach to token acquisition and lifecycle management.

## System Architecture
The module is built around `OneDriveLoginTool.java`, which orchestrates the Microsoft OAuth2 Device Code Flow. It integrates a new `OneDriveTokenStore` dependency to handle persistent token storage. The architecture shifts token management from manual application logic to an internal, persistent system, ensuring `access_token`, `refresh_token`, and expiry are handled transparently across sessions.

## Endpoints
Two new external endpoints support the Device Code Flow:
- `devicecode`: Returns a device code and user verification URL for out-of-band authentication.
- `token`: Exchanges the user code for `access_token`, `refresh_token`, and expiry data.
These are the only external authentication endpoints introduced by this module.

## Data Model
A new internal token data model persists the following fields:
- `access_token`
- `refresh_token`
- `expiry`
This model is stored and retrieved via `OneDriveTokenStore`, serving as the single source of truth for authentication state within the module.

## Dependencies
- `OneDriveLoginTool.java`: The core implementation class integrating the OAuth2 Device Code Flow.
- `OneDriveTokenStore`: New dependency responsible for persisting the token data model, enabling automatic token storage and retrieval, and eliminating the need for manual token management elsewhere in the application.