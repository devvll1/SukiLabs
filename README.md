# SukiLabs

SukiLabs is an Expo React Native app for managing installment phone customers. It stores records locally with SQLite, supports customer/account/payment tracking, and can back up data to Supabase after Google sign-in.

Created to help my siblings small business.

## Features

- Customer and installment account management
- Payment recording and payment history
- Dashboard stats, overdue tracking, and analytics
- Local-first SQLite storage
- Optional Supabase cloud backup and restore
- Google sign-in for cloud sync
- Android APK/AAB builds with EAS

## Tech Stack

- Expo SDK 54
- React Native 0.81
- Expo Router
- Expo SQLite
- Zustand
- Supabase
- TypeScript

## Local Setup

Install dependencies:

```bash
npm install
```

Create local environment files from the example:

```bash
cp .env.example .env.local
```

Set these values:

```env
EXPO_PUBLIC_SUPABASE_URL=https://your-project-ref.supabase.co
EXPO_PUBLIC_SUPABASE_KEY=your-publishable-or-anon-key
```

Start the app:

```bash
npm run start
```

## Supabase Setup

Run the SQL in `supabase.schema.sql` inside the Supabase SQL editor. The script creates:

- `customers`
- `installment_accounts`
- `payments`
- row-level security policies
- `attachments` storage bucket and policies

In Supabase Auth, enable Google as a provider and add this redirect URL:

```text
sukilabs://auth/callback
```

The app has an Expo Router callback screen at `app/auth/callback.tsx`. After successful Google sign-in, it returns to Settings.

## Cloud Backup Flow

The app saves customer/account/payment data locally first. Cloud backup is manual:

1. Go to Settings.
2. Tap Continue with Google.
3. After successful sign-in, tap Sync Now.

This keeps the core app usable even when Supabase, Google auth, or the network is unavailable.

## EAS Environment Variables

Local `.env` files are gitignored and are not automatically available in EAS builds. Add Supabase values to EAS before building:

```bash
eas env:create --name EXPO_PUBLIC_SUPABASE_URL --value "https://your-project-ref.supabase.co" --environment production
eas env:create --name EXPO_PUBLIC_SUPABASE_KEY --value "your-publishable-or-anon-key" --environment production
```

If using the `preview` profile, add them to the environment used by that profile as well.

## Build

Preview APK:

```bash
eas build -p android --profile preview --clear-cache
```

Production app bundle:

```bash
eas build -p android --profile production --clear-cache
```

## Verification

Run TypeScript checks:

```bash
npx tsc --noEmit
```

Verify Android bundling:

```bash
npx expo export --platform android --output-dir .expo-export-test --clear
```

Remove the generated export folder afterward:

```bash
rm -rf .expo-export-test
```

## Notes

`@supabase/supabase-js@2.106.1` includes an optional OpenTelemetry dynamic import that Hermes cannot parse in release builds. The `postinstall` script patches Supabase's installed bundle so Android release bundling succeeds.

