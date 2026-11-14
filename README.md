# Redro Barber Client

Customer booking portal. Each booking link is tied to one barber shop, and customer IDs are unique within that shop only.

## How customers use it
1. Open the unique booking link supplied by the barber: `https://redro-barber-client.vercel.app/?shop=SHOP_SLUG&customer=CUSTOMER_NUMBER`.
2. Sign in using Google.
3. The first visit creates a customer profile for that shop and assigns its next available number (01, 02, 03...).
4. Request appointments and view appointment status and queue position.

The same Google account can have a separate customer profile and separate customer number at each shop.

## Google OAuth setup
Google sign-in uses the Supabase Google provider. The shop owner configures the Google OAuth Client ID and Client Secret once in Supabase Authentication → Sign In / Providers → Google.

Authorized redirect URI:
`https://kqllombatzcpxjyltrsn.supabase.co/auth/v1/callback`

Allowed app redirect:
`https://mhamedshasho.github.io/**`

No SMS provider or paid messaging service is used.

## Deployment
The GitHub Actions workflow publishes the repository root to GitHub Pages. In repository Settings → Pages, select GitHub Actions.

## Privacy and security
- Customer records are scoped by shop_id and user_id.
- A customer can only access their own profile and appointments.
- The database allocates customer numbers per shop atomically.
- Queue lookup returns only the signed-in customer's own position and count ahead.
- No offline booking or local business-data cache.

## Performance and cost
No realtime subscriptions, polling, or background refresh loops. Data is loaded on demand with bounded result sets. Use Supabase Free and GitHub's free public-repository/Pages allowances only; never upgrade to paid services. Pause usage if free quotas are reached.

## Customer booking UX

The booking page uses the shop's saved theme colors for surfaces, panels, fields, focus rings and buttons instead of overriding them with fixed colors. Booking and settings panels close each other cleanly, mobile controls are easier to tap, inputs have clear focus feedback, and mutation buttons show an Arabic three-second cooldown to prevent duplicate submissions. The date/time flow remains cloud-only and loads availability on demand.
