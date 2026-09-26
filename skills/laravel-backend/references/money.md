# Money

Never use float/double for financial calculations. Prefer integer minor units: `105050` means PHP 1,050.50, with an ISO currency code such as `PHP`, `USD`, or `SGD`. Keep formatting separate from arithmetic and persistence.

Centralize rounding rules for percentages and financial calculations. Use a Money value object only when complexity or reuse warrants it; do not add a monetary abstraction to a trivial project. Test rounding, currency mismatch, negative values, and boundaries relevant to the domain.
