# WhatsApp OTP Auth System

A production-oriented onboarding and authentication flow powered by WhatsApp OTP, built to reduce signup friction, improve lead quality, verify that the user actually controls the submitted phone number, and provision free-trial accounts inside a multi-tenant SaaS product centered around WhatsApp operations.

This public repository exists as a portfolio case study. It showcases the product thinking, architecture, UX decisions, and implementation strategy behind the feature. The full source code is intentionally kept private.

## Executive Summary

This project turns a free-trial landing page into a validated onboarding funnel:

- The user submits their name and WhatsApp number.
- The system validates whether that number is actually registered on WhatsApp.
- A 6-digit OTP is delivered through WhatsApp itself.
- After OTP verification, the user completes the company signup form.
- The backend provisions a trial account, creates the tenant, and prepares activation.
- The user creates a password and enters the product within the same onboarding journey.

The result is a more trustworthy, lower-friction signup flow that validates the product's real communication channel before account creation and gives the system stronger confidence that the trial user submitted a real number they actually control.

## The Problem

Self-serve onboarding tends to underperform when:

- users sign up with invalid or unreachable numbers;
- the verification channel is disconnected from the product's main use case;
- activation depends on too many steps across different channels;
- low-intent signups inflate the funnel but do not convert;
- trial provisioning is loosely controlled.

Because this SaaS product is built around WhatsApp operations, validating the user's WhatsApp presence at the start of the funnel creates a stronger and more relevant onboarding process than a traditional email-first flow.

The core idea is simple but powerful: if the user can receive and confirm a WhatsApp OTP on that number, the system has much stronger evidence that the submitted number is real and belongs to the person starting the trial. That same principle is useful across many systems that need better trust during onboarding.

## What I Built

- A free-trial landing page optimized for conversion
- A multi-step onboarding wizard
- WhatsApp OTP delivery and verification
- Number existence validation before OTP generation
- Temporary signed session flow after OTP validation
- Automatic company and trial provisioning
- Password setup and final activation inside the same journey
- OTP resend and activation resend flows
- Abuse-prevention rules such as cooldowns and rate limits
- Error handling designed for users, without exposing internal implementation details

## Technical Flow

### 1. Lead capture

The user lands on the free-trial page and submits their name and WhatsApp number. The frontend handles input masking, basic validation, and step-by-step feedback.

### 2. WhatsApp number validation

Before generating the OTP, the backend checks whether the submitted number is actually on WhatsApp. This prevents invalid signups from entering the funnel and improves downstream account quality.

### 3. OTP delivery via WhatsApp

If the number is valid, the system generates a 6-digit OTP with a short expiration window and sends it through a connected WhatsApp instance. The flow also includes:

- resend cooldowns;
- per-phone attempt limits;
- additional IP-based rate limiting on sensitive endpoints.

### 4. Verified session token

After successful OTP verification, the backend issues a temporary signed token so the user can continue onboarding securely without re-verifying the phone number at every step.

### 5. Trial provisioning

Once the business form is completed, the backend:

- checks for duplicate email addresses;
- finds or creates the `Free Trial` plan;
- creates a tenant in trial mode;
- sets the trial expiration date;
- creates the auth user;
- links the profile to the tenant.

### 6. Final activation

In the final step, the user sets a password and the trial environment is activated. The flow was designed to be resilient, including cleanup logic for partial failures during provisioning.

## Free Trial Rules

The trial provisioned by this feature includes:

- 3 free days
- 1 WhatsApp instance
- 2 contact lists
- 500 contacts
- 3 campaigns
- 100 sends per day
- 500 group or address-book extractions

## Key Engineering Decisions

### Use WhatsApp as the verification channel

Instead of relying on email as the primary verification path, the flow verifies users through the exact channel the product depends on. This makes the onboarding experience more aligned with actual product usage and creates stronger confidence that the user truly controls the number they submitted.

### Validate the number before generating the OTP

The system checks whether the phone number is present on WhatsApp before sending the code. This reduces waste, improves lead quality, and avoids provisioning accounts for unreachable users.

### Separate OTP validation from account provisioning

OTP verification grants a temporary signed session, while provisioning happens later. This separation keeps the flow flexible and easier to reason about, while still enforcing security boundaries between steps.

### Keep activation idempotent

The final activation step is designed so repeated requests do not break account state. This matters in real-world onboarding where users refresh pages, retry actions, or return from interrupted flows.

### Fail safely during provisioning

The flow includes cleanup behavior for partial failures so the system does not leave behind inconsistent trial tenants or orphaned auth records.

## Challenges and Trade-Offs

### Friction vs. trust

Adding verification always introduces some friction. The trade-off here was intentional: slightly more effort at signup in exchange for much higher trust in the quality of created accounts.

### Reliability vs. dependency on external messaging infrastructure

Using WhatsApp as the OTP channel creates a stronger product fit, but it also introduces operational dependency on messaging infrastructure. To reduce risk, the flow includes retries, instance fallback behavior, and user-facing recovery paths.

### Fast onboarding vs. abuse prevention

A frictionless trial funnel can be abused. This implementation balances conversion with cooldowns, per-number limits, and IP-based protections, aiming to reduce spam without making legitimate users feel punished.

### Product clarity vs. implementation secrecy

Because this repository is public and intended for portfolio use, the documentation focuses on architecture and decision-making rather than exposing internal source code, secrets, or operational details.

## Security and Abuse Prevention

This project includes several defensive layers:

- short-lived OTP expiration;
- max attempt rules for incorrect codes;
- resend cooldowns;
- phone-based rate limiting;
- IP-based rate limiting on sensitive endpoints;
- signed temporary tokens after verification;
- user-safe error messages that avoid leaking internals.

## Why This Project Matters

From a portfolio perspective, this project demonstrates more than just implementation:

- product-aware backend design;
- onboarding architecture for SaaS;
- integration with external communication channels;
- multi-tenant provisioning logic;
- defensive engineering for user-facing systems;
- the ability to think in terms of conversion, trust, and operational reliability.

It also demonstrates a reusable onboarding pattern: verifying real ownership of a submitted WhatsApp number before granting access, creating a trial, or allowing the user deeper into the system.

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
              +--> Final account activation
              |
              v
   Supabase Auth + Database
```

## Demo and Media

This repository is meant to be supported by visual proof of execution. I will add:

- product screenshots;
- short demo videos;
- walkthroughs of the OTP validation flow;
- onboarding and trial activation clips.

Recommended media structure:

- `Screenshot 1`: free-trial landing page
- `Screenshot 2`: OTP verification step
- `Screenshot 3`: business form and provisioning step
- `Screenshot 4`: final activation state
- `Video demo`: end-to-end onboarding walkthrough

## Media Placeholders

Use this section as soon as the assets are uploaded to the repository or attached through GitHub-friendly URLs.

### Screenshots

![Free Trial Landing Page](./media/free-trial-landing-page.png)

![WhatsApp OTP Verification](./media/whatsapp-otp-verification.png)

![Business Signup Form](./media/business-signup-form.png)

![Trial Activation State](./media/trial-activation-state.png)

### Video Demo

[Watch the full demo](https://github.com/user-attachments/assets/your-video-id)

### Suggested Asset Structure

```text
media/
  free-trial-landing-page.png
  whatsapp-otp-verification.png
  business-signup-form.png
  trial-activation-state.png
```

## About This Repository

This repository was created to present the feature as a portfolio case study for product engineering and SaaS-focused roles.

It is public so recruiters, founders, and hiring teams can evaluate the scope and quality of the work. The complete source code, internal integrations, secrets, and infrastructure details remain private for commercial and security reasons.

## My Role

I designed and implemented this feature end-to-end, including:

- onboarding UX;
- validation rules;
- OTP authentication logic;
- WhatsApp integration;
- trial provisioning flow;
- final activation flow;
- failure handling and abuse-prevention strategy.

## Contact

If you would like a live walkthrough or want to discuss product engineering, backend architecture, SaaS onboarding, or full-stack roles, feel free to reach out.
