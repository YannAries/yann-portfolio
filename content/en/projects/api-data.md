+++
title = "Unofficial Tapology API"
date = "2025-01-18"
description = "API for MMA fighter data and event schedules"
demo = "https://api.tapology-unofficial.com"
tags = ["api", "mma", "sports", "fastapi", "python"]
categories = ["api"]
featured = true
+++

REST API that retrieves comprehensive MMA fighter information and event schedules (past and future) from Tapology.com, featuring optimized Redis caching.

## Overview

The Unofficial Tapology API provides programmatic access to detailed MMA fighter profiles, fight histories, and event schedules. Built for performance and reliability, it serves data with sub-second response times through intelligent caching and failover mechanisms.

### Key features

- **Fighter profiles** — Detailed information including records, statistics, and biographical data
- **Fight results** — Comprehensive fight history with outcomes, methods, and records
- **Event schedules** — Upcoming MMA events with full fight cards by region
- **Past events** — Historical events with complete results and details
- **Bulk requests** — Retrieve multiple fighters or regions in a single call (up to 3-5 per request)
- **Optimized caching** — Redis with compression for fast response times
- **High availability** — Circuit breaker pattern maintains stability under load
- **Field filtering** — Request only the fields you need to minimize bandwidth

## Technical architecture

### Backend stack

- **FastAPI** — Modern async Python web framework
- **Redis** — Distributed caching with compression
- **BeautifulSoup4** — Efficient HTML parsing
- **Python 3.12.8** — Latest Python with performance improvements
- **Gunicorn + Uvicorn** — Production-grade ASGI server

### Performance features

- **Connection pooling** — Reuses connections efficiently
- **Retry logic** — Exponential backoff with tenacity
- **Compression** — Automatic response compression for large payloads
- **Structured logging** — Comprehensive request tracking with structlog

## API endpoints

### Fighter Profiles

Get detailed fighter information including records, stats, and bio data.

**Single Fighter:**
```http
GET /api/fighter/{fighter_id}
```

**Bulk Request (up to 3 fighters):**
```http
GET /api/fighter/profiles/{fighter_ids}
```

**Response example:**
```json
{
  "data": {
    "firstname": "Georges",
    "lastname": "St-Pierre",
    "nickname": "Rush",
    "age": "43",
    "date_of_birth": "May 19, 1981",
    "weight_class": "Welterweight",
    "full_record": "26-2-0",
    "wins": 26,
    "losses": 2,
    "tko_ko": {"wins": 8, "losses": 1},
    "submission": {"wins": 6, "losses": 0},
    "decision": {"wins": 12, "losses": 1}
  }
}
```

### Fighter Results

Retrieve complete fight history with detailed outcomes.

**Single Fighter:**
```http
GET /api/fighter/result/{fighter_id}
```

**Bulk Request (up to 3 fighters):**
```http
GET /api/fighter/results/{fighter_ids}
```

**Response example:**
```json
{
  "results": [
    {
      "opponent_name": "Michael Bisping",
      "event_date": "2017-11-04",
      "method": "Submission, Technical Choke",
      "event": "UFC 217",
      "result": "win",
      "fighter_record_before_fight": "25-2-0"
    }
  ]
}
```

### Event Schedules

Access upcoming events by region (1-121).

**Single Region:**
```http
GET /api/schedule/events/{region_id}
```

**Bulk Request (up to 3 regions):**
```http
GET /api/schedule/events/{region_ids}
```

**Response example:**
```json
{
  "events": [
    {
      "organization": "UFC",
      "main_event": "Islam Makhachev vs. Dustin Poirier",
      "datetime": "June 01, 2025",
      "city": "Las Vegas, Nevada",
      "fight_card": {
        "fight_1": {
          "fight": "Islam Makhachev vs. Dustin Poirier",
          "weight_class": "155",
          "title_bout": true
        }
      }
    }
  ]
}
```

### Past Events

Browse historical events with complete results.

**Single Region:**
```http
GET /api/schedule/past-events/{region_id}?page=1
```

**Bulk Request (up to 3 regions):**
```http
GET /api/schedule/past-events/{region_ids}?page=1
```

## Query Parameters

### Field Filtering

Request only specific fields to reduce response size:
```http
GET /api/fighter/117305-alex-pereira?fields=firstname,lastname,full_record,wins,losses
```

**Valid fields by endpoint:**

**Fighter Profiles:** firstname, lastname, nickname, age, date_of_birth, born, weight_class, last_weigh_in, full_record, current_streak, last_fight, wins, losses, draws, no_contest, tko_ko, submission, decision, disqualification

**Fighter Results:** fighter_name, opponent_name, result, method, event, event_date, level, title_bout, billing, weight, duration, odds, referee

**Events:** organization, main_event, weight_class, datetime, city, subregion, broadcast, title_bout_desc, fight_card

### Pagination

Past events support pagination:
```http
GET /api/schedule/past-events/1?page=2
```

## Configuration

### Environment variables
```bash
# Application
APP_ENV=production
PORT=8080
WORKERS=4

# Redis
REDIS_URL=redis://localhost:6379/0
CACHE_EXPIRY=3600

# Scraping
SCRAPING_TIMEOUT=30
SCRAPING_RETRIES=3

# Circuit Breaker
CIRCUIT_BREAKER_THRESHOLD=5
CIRCUIT_BREAKER_TIMEOUT=30
```

### Cache behavior

- **TTL:** 3600 seconds (1 hour) in production
- **Compression:** Automatic for responses > 1KB
- **Strategy:** allkeys-lru eviction policy

## Rate limiting & best practices

### Recommended usage

- **Respect cache:** Repeated requests for same data return cached responses
- **Bulk endpoints:** Use bulk endpoints instead of multiple single requests
- **Field filtering:** Request only needed fields to reduce bandwidth
- **Error handling:** Implement exponential backoff for retries

### Response times

- **Cached requests:** < 50ms
- **Scraping:** 2-5 seconds
- **Bulk requests:** 3-8 seconds (depending on cache state)

## Error handling

### HTTP status codes

- `200` — Success
- `400` — Invalid request (bad fighter ID, invalid fields, etc.)
- `404` — Fighter or resource not found
- `500` — Internal server error

### Error response format
```json
{
  "detail": "Fighter not found: invalid-fighter-id"
}
```

## Deployment

Built for containerized deployment:

### Docker
```bash
docker build -t tapology-api .
docker run -p 8080:8080 \
  -e REDIS_URL=redis://redis:6379/0 \
  tapology-api
```

### Docker compose
```yaml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      - REDIS_URL=redis://redis:6379/0
    depends_on:
      - redis
  
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
```

## Health checks

Monitor API health:
```http
GET /api/health
```

**Response:**
```json
{
  "status": "ok",
  "version": "1.3.0",
  "environment": "production"
}
```

## Technical highlights

### Optimization strategies

- **Connection pooling** — Reuses HTTP connections (pool size: 45)
- **Async architecture** — Non-blocking I/O throughout
- **Smart caching** — Hash-based storage with compression
- **Circuit breaking** — Fails fast during outages
- **Structured logging** — Request IDs for debugging

### Reliability features

- **Automatic retries** — 3 attempts with exponential backoff
- **Timeout Management** — Configurable per operation
- **Graceful Degradation** — Returns cached data when scraping fails
- **Health Monitoring** — Built-in health check endpoints

## Use cases

Perfect for:

- **Fan Applications** — Build fighter comparison tools
- **Research Projects** — Analyze MMA trends and statistics
- **Content Aggregators** — Display upcoming events and results

---

**API Demo:** [https://api.tapology-unofficial.com](https://api.tapology-unofficial.com)

**Documentation:** Interactive API docs at `/api/docs`

**Version:** 1.3.0