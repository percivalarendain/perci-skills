# Dates and timezones

Use a canonical UTC timeline for exact timestamps in the backend/database. Use Laravel/Carbon APIs and cast date/datetime attributes appropriately. Naming: `*_at` is an exact moment; `*_date` is a calendar date.

Return API datetimes as ISO 8601. Convert to a user/application timezone only at presentation boundaries. Business rules tied to a civil timezone must preserve that timezone as part of the rule rather than scattering hard-coded conversions. Freeze or travel time in time-sensitive tests; never depend on the real clock.
