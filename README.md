n8n Workflow Collection

This repository is a collection of professional automation workflows built with n8n. Each project focuses on solving specific backend engineering challenges, such as data consistency, fault tolerance, and API optimization.

Table of Contents

Stripe Idempotent Billing Automation

General Prerequisites

1. Stripe Idempotent Billing Automation
An advanced workflow designed to handle Stripe billing events with high reliability. This system ensures data consistency through idempotency checks, optimizes performance via Redis caching, and guarantees delivery to downstream services using a PostgreSQL-backed retry mechanism.

Purpose
This project addresses three common failures in simple webhook handlers:

Duplicate Events: Prevents double-counting when Stripe sends the same webhook multiple times.

API Rate Limits: Reduces dependency on the Stripe API by caching customer data.

Service Downtime: Prevents data loss if downstream apps (Notion/Airtable) go offline.

Architecture and Flow
The system consists of two main logical flows:

Live Event Processor

Ingestion: Listens for Stripe POST requests.

Idempotency: Queries PostgreSQL to verify if the event ID already exists. Duplicate events are logged and discarded.

Caching: Checks Redis for customer details. If missing, fetches from Stripe and caches the result (60s TTL).

Broadcast: Syncs enriched data to Slack, Notion, and Airtable.

Error Handling: Failed operations are captured with their specific payload and error message, then inserted into a database table for retries.

Retry Worker (Scheduled)

Trigger: Runs on a cron schedule to process the "Dead Letter Queue".

Route: Fetches unresolved rows from the database and routes them to the correct service (Notion or Airtable) based on the failed context.

Update: Marks successful retries as resolved and increments the retry count for repeated failures.

Tech Stack
Workflow Engine: n8n

Trigger: Stripe Webhooks

Database: PostgreSQL (Event logging and Retry queue)

Caching: Redis

Destinations: Notion, Airtable, Slack

Visuals

<img width="841" height="363" alt="stripe-idempotent-billing" src="https://github.com/user-attachments/assets/2a5e6bdf-2f0b-4ea1-b31b-b6f326a0691d" />

Execution Demo

![stripe-idempotent-billing](https://github.com/user-attachments/assets/c991c3a9-3d75-4f9c-8f90-ee8d5d42e295)

General Prerequisites
To run the workflows in this repository, you typically need:

n8n (Self-hosted or Cloud)

PostgreSQL database

Redis instance

Relevant API credentials for the services integrated

Usage
Download the JSON file for the specific workflow from this repository.

Import the file into your n8n instance.

Configure the credential nodes with your own API keys.

Activate the workflow and ensure your database/cache connections are reachable.

