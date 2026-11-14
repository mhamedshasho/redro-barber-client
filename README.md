# Redro Barber Client

Customer booking portal. Each booking link is tied to one barber shop, and customer IDs are unique within that shop only.

## How customers use it
1. Open the unique booking link supplied by the barber: `https://redro-barber-client.vercel.app/?shop=SHOP_SLUG&customer=CUSTOMER_NUMBER`.
2. Sign in using Google.
3. The first visit creates a customer profile for that shop and assigns its next available number (01, 02, 03...).
4. Request appointments by available time and view appointment duration, status and queue position; no service catalog is used.

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


## Availability rules

Customer availability respects the shop's configured weekly day off, opening time and closing time. Slots after closing are not shown, the selected day off shows no slots, and slots that have already passed today cannot be booked.

## Coordinated UI refinement

Added a shared visual refinement layer for the customer app: clearer mobile header and hero, consistent card shadows, stronger field labels and focus states, and a more comfortable sticky booking action on small screens.

Overnight schedules are supported: a shop open from 12:00 to 01:00 accepts slots up to 00:30, while 01:00 and later are treated as closed.

## Direct time booking

The customer no longer chooses from a pre-generated available-times list. The customer enters a requested time, and the app accepts it only when it is inside the barber's opening/closing range, not on the weekly day off, and not already booked.

Shop settings are synchronized live from the manager through the shared `shop_settings` record. Open customer pages receive updates for the shop name, phone, logo, theme colors, and working-hours rules without needing a manual refresh.

Appointments use whole hours only: 1:00, 2:00, 3:00, and so on. The customer app no longer tries to create internal booking records, so registration is handled through the manager-owned booking data safely.

Customer appointment time uses a 12-hour Arabic selector: hours 1–12, صباحاً/مساءً, and minutes 00/15/30/45. The internal conversion remains server-compatible while the customer never sees 13–24 hour labels.

## Shared manager/customer behavior

The customer app now follows the same cloud-only and live-sync policy as the manager: no local shop cache, live shop-settings updates, shared theme variables, and the same motion/interaction polish. It blocks use when the network is unavailable.

## Queue alerts

Customers now see the number of people ahead of them. When the queue reaches the shop's chair capacity, the app shows that the turn is near; when no one is ahead, it tells the customer that their turn is now and asks them to come to the shop. Completed appointments are removed from the active list but the customer account remains.

Chair capacity is now read from each shop's cloud settings. Queue alerts use the current shop's saved `chair_count` automatically, with a safe fallback of three when a legacy public response omits the field.
