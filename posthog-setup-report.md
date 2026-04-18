# PostHog post-wizard report

The wizard has completed a deep integration of PostHog analytics into the Subly (Recurrly) Expo app. Here is a summary of all changes made:

- **`app.config.js`** — Created from `app.json`, adding `extra.posthogProjectToken` and `extra.posthogHost` fields so the PostHog token and host are injected at build time from environment variables.
- **`.env`** — Created with `POSTHOG_PROJECT_TOKEN` and `POSTHOG_HOST` environment variables.
- **`lib/posthog.ts`** — New file: initializes the PostHog client using `expo-constants` to read config from `app.config.js` extras. Disables analytics gracefully if the token is not set.
- **`app/_layout.tsx`** — Wrapped the app in `PostHogProvider` (with autocapture enabled for touches), and added manual screen tracking via `usePathname` / `useGlobalSearchParams` + `posthog.screen()`.
- **`app/(auth)/sign-in.tsx`** — Captures `user_signed_in` with `method` property on successful login. Calls `posthog.identify()` with the user's email. Captures `$exception` on sign-in errors.
- **`app/(auth)/sign-up.tsx`** — Captures `user_signed_up` with `method` property and calls `posthog.identify()` with the user's email on successful registration and verification.
- **`app/(tabs)/settings.tsx`** — Captures `user_signed_out` and calls `posthog.reset()` before sign-out to clear the anonymous/identified session.
- **`app/(tabs)/index.tsx`** — Captures `add_subscription_tapped` when the user taps the add (+) button on the home screen.
- **`app/(tabs)/insights.tsx`** — Captures `insights_viewed` on mount, tracking navigation to the Insights tab.
- **`components/SubscriptionCard.tsx`** — Captures `subscription_card_expanded` (with `subscription_name`, `category`, `billing`, `currency` properties) when a card is expanded for the first time.

## Events

| Event | Description | File |
|---|---|---|
| `user_signed_in` | User successfully completes sign-in with email/password | `app/(auth)/sign-in.tsx` |
| `user_signed_up` | User successfully completes account creation and email verification | `app/(auth)/sign-up.tsx` |
| `user_signed_out` | User signs out from the Settings screen | `app/(tabs)/settings.tsx` |
| `add_subscription_tapped` | User taps the add (+) button to open the create subscription modal | `app/(tabs)/index.tsx` |
| `subscription_card_expanded` | User taps a subscription card to expand its details | `components/SubscriptionCard.tsx` |
| `insights_viewed` | User navigates to the Insights tab | `app/(tabs)/insights.tsx` |

## Next steps

We've built some insights and a dashboard for you to keep an eye on user behavior, based on the events we just instrumented:

- **Dashboard — Analytics basics**: https://eu.posthog.com/project/161117/dashboard/627938
- **Auth Conversion Funnel** (sign-up → sign-in): https://eu.posthog.com/project/161117/insights/3R4ZAhLA
- **New User Sign-ups Over Time**: https://eu.posthog.com/project/161117/insights/L37Eqoj5
- **User Churn — Sign-outs Over Time**: https://eu.posthog.com/project/161117/insights/ohG6P4UZ
- **Subscription Add Funnel**: https://eu.posthog.com/project/161117/insights/IQVheoaT
- **Feature Engagement Comparison**: https://eu.posthog.com/project/161117/insights/kqK6YDK1

### Agent skill

We've left an agent skill folder in your project. You can use this context for further agent development when using Claude Code. This will help ensure the model provides the most up-to-date approaches for integrating PostHog.
