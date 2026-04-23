# Runbook

## Common Failures and Recovery

| Failure | Cause | Fix |
|---|---|---|
| Google returns `REQUEST_DENIED` | Invalid or restricted `GMAPS_API_KEY` | Verify key in [Google Cloud Console](https://console.cloud.google.com/apis/credentials); ensure Directions API is enabled |
| Google returns `OVER_QUERY_LIMIT` | Rate limit / billing issue | Check billing status; implement exponential backoff |
| TollGuru returns `401` | Invalid `TOLLGURU_API_KEY` | Regenerate key at [TollGuru dashboard](https://tollguru.com/developers/get-api-key) |
| `KeyError: 'routes'` or `Cannot read property 'legs'` | No route found for origin/destination | Verify addresses are valid and geocodable |
| `ConnectionError` / `ETIMEDOUT` | Network issue or API downtime | Retry; check API status pages |
| Go: `index out of range` | Empty response from Google | Add nil/length checks before accessing `Routes[0]` |
| PHP: returns approximate tolls | Uses `overview_polyline` not stitched steps | Known limitation; consider porting the stitching logic from Python/JS |
| Ruby: wrong tolls | `source` is set to `"bing"` instead of `"google"` | Fix line 39 in `main.rb` |

## Rollback Basics

Standalone scripts with no deployment. Rollback = `git checkout`:

```bash
git log --oneline -5
git checkout <commit-hash> -- python/google_maps_polyline.py
```

## On-Call / Logs / Monitoring

- No deployed service; no alerting or monitoring
- Pipe script output to a log file for auditing:
  ```bash
  python python/google_maps_polyline.py >> output.log 2>&1
  ```
- For production use, wrap API calls with structured logging and integrate with your observability stack

## Cron Jobs / Scheduled Jobs

None defined. If scheduling:
- Ensure both `GMAPS_API_KEY` and `TOLLGURU_API_KEY` are in the cron environment
- Set the working directory to the repo root
- Be mindful of Google Maps API quotas

Example:
```
0 8 * * * cd /path/to/repo && GMAPS_API_KEY=xxx TOLLGURU_API_KEY=yyy python python/google_maps_polyline.py >> /var/log/route-tolls.log 2>&1
```

## Data Backfill Scripts

None. Scripts are stateless. For bulk route toll calculations, loop over an origin/destination CSV and call the main functions. See `python/Testing/testCases.csv` for an example input format.
