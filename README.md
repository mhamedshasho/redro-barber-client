# Redro Barber Client

Customer booking portal for Redro Barber. Each booking link identifies a barber shop, and customer access is linked to a customer record within that shop.

## Customer access

1. Open the booking link supplied by the barber. The link includes the shop identifier, for example: `https://redro-barber-client.vercel.app/?shop=SHOP_SLUG`.
2. Enter the customer number and the phone number already registered with the barber.
3. The app restores an existing Supabase session when available. Otherwise, it starts an anonymous Supabase Auth session and calls the `claim_customer_login` RPC to securely link that session to the matching customer record.
4. After access is granted, the customer can view their profile and appointments, check their queue information, and request an available appointment slot.

The optional customer number in the URL can prefill the customer-number field. It is not a substitute for the registered phone number.

## Authentication configuration

Customer access uses Supabase Anonymous Sign-ins together with the database RPC `claim_customer_login`. Google OAuth is not used by this client.

The client uses the Redro Barber Supabase project. Anonymous Sign-ins must be enabled in Supabase Authentication for first-time customer sessions. The client persists and refreshes the Supabase session.

Barber/manager authentication is handled separately by the manager application.

## Booking behavior

- Customers choose a date and an available time slot supplied by the shop.
- The client submits the selected slot through `book_my_appointment_slot`; it does not let customers choose the service, appointment duration, or appointment status.
- New booking requests are subject to the barber's approval.
- Customers can view their own appointment status, duration, barber notes, and queue information.
- The client does not create or manage slot inventory.

## Shop settings and availability

Shop information and public settings are loaded from Supabase. The page uses the shop's saved name, contact details, logo, theme colors, chair capacity, and working-hours settings when available.

Availability is based on the shop's configured working hours, weekly day off, and the individual slot records created for the shop. The client only offers slots marked available and asks the server to book the selected slot. Overnight schedules are supported by the scheduling logic.

Shop settings and appointment changes can be reflected through Supabase Realtime subscriptions where configured.

## Privacy and security

- Customer identity is linked through the server-side `claim_customer_login` RPC.
- Customer profile and appointment operations are scoped to the authenticated customer and shop.
- Queue information is requested for the signed-in customer's own appointment through `get_my_queue`.
- The public shop information endpoint is `get_public_shop_info`.
- The client does not contain a Supabase service-role key.
- The application is cloud-only and requires an internet connection; it does not provide offline booking.

Database access policies and RPC permissions must be maintained and reviewed in the Redro Barber Supabase project. Client-side checks alone are not a security boundary.

## Deployment

The client is deployed on Vercel:

- Production URL: https://redro-barber-client.vercel.app
- Source repository: https://github.com/mhamedshasho/redro-barber-client

Deployments are managed through the repository's connected Vercel project. This README does not assume a GitHub Pages deployment or a GitHub Actions publishing workflow.

## Performance and user experience

The client loads shop and customer data from the cloud on demand, uses guarded actions to reduce duplicate submissions, and displays feedback for network and request failures. Because booking and customer data are cloud-based, an internet connection is required.
