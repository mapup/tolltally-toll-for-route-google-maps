# Architecture

## Major Services

- **Google Maps Directions API** - provides route geometry (per-step polylines) for an origin/destination pair
- **TollGuru Polyline API v2** (`/complete-polyline-from-mapping-service`) - accepts a stitched polyline and returns toll costs by payment method

## Datastore Choices

None. All scripts are stateless; no persistence layer.

## Queues / Jobs

None. All processing is synchronous and single-request.

## Third-Party Dependencies

- **Python**: `requests`, `polyline` (see `python/requirements.txt`)
- **JavaScript**: `request`, `polyline`, `dotenv` (see `javascript/package.json`)
- **Ruby**: `httparty`, `fast_polylines` (see `ruby/Gemfile`)
- **Go**: stdlib only (no external modules)
- **PHP**: built-in `curl` extension

## Auth Model

Both APIs use key-based authentication:
- Google Maps: `key` query parameter
- TollGuru: `x-api-key` HTTP header

Keys are read from environment variables. No OAuth, no token refresh.

## Tenancy Model

Not applicable. Single-user scripts; no multi-tenancy.
