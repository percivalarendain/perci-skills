# Notifications and mail

Use Laravel Notifications for user notifications, including multiple delivery channels. Use a Mailable when the email itself is the primary email-specific artifact. Use database notifications for an in-app notification center when needed.

Queue slow or external delivery. A notification delivery failure generally should not roll back a primary successful business operation; persist the business result first and make delivery retryable/observable. Avoid putting large mutable domain workflows inside notification classes.
