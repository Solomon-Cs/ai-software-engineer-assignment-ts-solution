# EXPLANATION

## What was the bug?

The HttpClient.request method only refreshed the token if it was null or an expired OAuth2Token instance. If oauth2Token was a plain object (not an OAuth2Token instance), the code neither refreshed the token nor set the Authorization header.

## Why did it happen?

The logic relied on a truthiness check combined with an instanceof check. A plain object token is truthy but not an OAuth2Token instance, so the refresh condition was skipped and no Authorization header was set.

## Why does your fix solve it?

The fix treats any non-OAuth2Token value as invalid by refreshing the token when:
- oauth2Token is null
- oauth2Token is not an instance of OAuth2Token
- oauth2Token is expired

This guarantees a valid OAuth2Token is always used for API requests.

## One realistic case not covered

Tests do not cover the case where oauth2Token is an OAuth2Token instance but expires exactly at the current second boundary. A race condition around expiration timing is not tested.
