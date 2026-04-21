# WhatsApp OTP Auth System

A multi-step onboarding and authentication flow powered by WhatsApp OTP, designed to reduce signup friction, improve lead quality, and activate users inside a SaaS product built around WhatsApp operations.

This public repository exists as a portfolio case study. Its purpose is to showcase the product thinking, architecture, UX decisions, and implementation strategy behind the feature. The full source code is intentionally kept private.

## Overview

This feature turns a free trial landing page into a validated onboarding funnel:

- The user submits their name and WhatsApp number.
- The system checks whether that number is actually on WhatsApp before moving forward.
- A 6-digit OTP is sent through WhatsApp itself.
- After OTP verification, the user completes the business signup form.
- The system provisions a trial account, creates the tenant, and prepares the account for activation.
- The final activation happens within the same onboarding journey, without relying on email as the primary verification channel.

## Problem It Solves

Self-serve onboarding often breaks down because of:

- invalid or unreachable phone numbers;
- low-intent signups;
- activation drop-off;
- long onboarding flows with too many disconnected steps;
- weak validation of the user's real communication channel.

This implementation addresses those issues by validating the user's main channel upfront and combining verification, account creation, and trial provisioning into a single cohesive flow.

## What Was Built

- High-conversion free trial landing page
- Multi-step onboarding wizard
- WhatsApp OTP delivery
- Pre-validation to confirm the number exists on WhatsApp
- Rate limiting to reduce abuse and repeated attempts
- Signed temporary session token after successful OTP validation
- Automatic creation of account, company, and trial plan
- Idempotent trial activation after password creation
- OTP resend and activation resend flows
- User-facing error handling without leaking internal system details

## Technical Flow

### 1. Lead capture

The user enters their name and WhatsApp number on the free trial page. The UI handles masking, client-side validation, and immediate feedback.

### 2. WhatsApp number validation

Before generating the OTP, the backend checks whether the submitted number is actually registered on WhatsApp. This helps prevent invalid signups and improves funnel quality.

### 3. OTP delivery through WhatsApp

If the number is valid, the system generates a 6-digit OTP with a short expiration window and sends it through a connected WhatsApp instance. The flow also includes:

- resend cooldown protection;
- per-phone attempt limits;
- additional IP-based rate limiting on sensitive steps.

### 4. Verified session

Once the OTP is confirmed, the backend issues a temporary signed token that allows the user to continue the onboarding flow securely.

### 5. Trial provisioning

After the business form is completed, the system:

- checks for duplicate email addresses;
- finds or creates the "Free Trial" plan;
- creates the tenant in trial mode;
- sets the trial expiration window;
- creates the auth user;
- links the user profile to the new tenant.

### 6. Final activation

In the last step, the user creates a password and the trial environment is activated. The flow was designed to be resilient, including cleanup logic for partial failures.

## Free Trial Rules

The trial provisioned by this feature includes:

- 3 free days;
- 1 WhatsApp instance;
- 2 contact lists;
- 500 contacts;
- 3 campaigns;
- 100 sends per day;
- 500 group or address-book extractions.

## Why This Solution Is Strong

- Uses WhatsApp itself as the verification channel, which matches the core product behavior.
- Filters invalid numbers before account creation.
- Reduces onboarding friction without removing validation safeguards.
- Combines conversion, authentication, and provisioning in a single flow.
- Supports a multi-tenant SaaS model with controlled trial limits.
- Includes defensive mechanisms against spam, abuse, and repeated retries.

## Stack

- Nuxt 4
- Vue 3
- Supabase Auth
- Supabase Database
- Nitro server routes
- WhatsApp API integration for number validation and OTP delivery

## High-Level Architecture

```text
Landing page / Onboarding wizard
              |
              v
        Nuxt frontend
              |
              v
       Nitro API routes
              |
              +--> WhatsApp number validation
              +--> OTP generation and verification
              +--> Rate limiting and signed temporary tokens
              +--> Trial tenant provisioning
              |
              v
   Supabase Auth + Database
```

## Project Media

I will add:

- product screenshots;
- short demo videos;
- WhatsApp OTP validation walkthroughs;
- onboarding and trial activation clips.

## About This Repository

This public repository was created to showcase:

- product thinking;
- backend and onboarding architecture;
- UX decisions for conversion and activation;
- the ability to integrate authentication, WhatsApp workflows, and SaaS provisioning.

The complete source code, internal integrations, secrets, and infrastructure details remain private.

## My Role

I designed and implemented this feature end-to-end, including:

- onboarding UX;
- validation rules;
- OTP authentication flow;
- WhatsApp integration;
- automatic trial provisioning;
- activation flow;
- failure handling and abuse-prevention scenarios.

## Contact

If you would like to see a live walkthrough or discuss product engineering, SaaS onboarding, or backend architecture, feel free to reach out.
