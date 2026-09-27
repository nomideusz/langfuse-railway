# Deploy and Host Langfuse 4 on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/langfuse-4?utm_medium=integration&utm_source=button&utm_campaign=langfuse-4)

[Langfuse](https://langfuse.com/) is the open-source LLM engineering platform: tracing for every model call, tool call and agent step, prompt management, evaluations, datasets and cost tracking. This template runs Langfuse v4, the current major version, with the same architecture Langfuse Cloud uses: web, worker, ClickHouse, Postgres, Redis and object storage. Your organization, project and API keys exist the moment the deploy finishes.

## About Hosting Langfuse 4

- **The full v4 stack, pinned.** Langfuse web and worker 4.46.0, ClickHouse 25.12, Postgres 17 and Redis 8, all on Railway's private network. Nothing but the web UI has a public address.
- **Traces in a Railway bucket.** Raw events, media attached to traces and batch exports go to the bundled bucket instead of a MinIO container, so there is no storage service to size or back up.
- **API keys on first boot.** The first boot creates your admin user, an organization, a project and its public and secret keys. Copy the keys from the Variables tab into your app, no clicking through the UI first.
- **Closed by default.** Public sign-up is off: only the admin created on first boot can log in until you invite others.
- **Upgrades that migrate themselves.** The web service runs the Postgres and ClickHouse migrations on every start, so bumping the image tag is the whole upgrade.
- **A quiet ClickHouse.** Logs go to Railway's log view instead of gigabytes of files on the container disk, and the internal metric tables that write every second are off. That leaves more of your plan's memory and disk for traces.

## Common Use Cases

- Tracing LLM apps and agents built with OpenAI, Anthropic, LangChain, LlamaIndex, the Vercel AI SDK or any OpenTelemetry exporter
- Versioning prompts and rolling them out without a redeploy of your app
- Scoring outputs with LLM-as-a-judge evaluators and running experiments on datasets
- Tracking token usage, latency and cost per user, session or feature

## Dependencies for Langfuse 4 Hosting

- ClickHouse 25.12 (included, private network only)
- Postgres 17 (included, private network only)
- Redis 8 (included, private network only)
- A Railway bucket for events, media and exports (included)

### Deployment Dependencies

- [Langfuse documentation](https://langfuse.com/docs)
- [Langfuse self-hosting guide](https://langfuse.com/self-hosting)
- [Template source on GitHub](https://github.com/nomideusz/langfuse-railway)

### Implementation Details

**Sign in** at the Langfuse service's Railway domain with the email you entered at deploy time and the `LANGFUSE_INIT_USER_PASSWORD` value from the Langfuse service's Variables tab. The first boot runs the database migrations, so give it a minute or two.

**Send traces.** Point your app at the deployment with the keys from the same Variables tab:

```sh
LANGFUSE_BASE_URL=https://your-langfuse.up.railway.app
LANGFUSE_PUBLIC_KEY=<LANGFUSE_INIT_PROJECT_PUBLIC_KEY>
LANGFUSE_SECRET_KEY=<LANGFUSE_INIT_PROJECT_SECRET_KEY>
```

Langfuse v4 ingests over OpenTelemetry: use the Python SDK v3 or later, the JS/TS SDK v4 or later, or any OTLP exporter pointed at `/api/public/otel`. Older SDKs (Python v2, JS v3) are rejected.

**Teammates.** Invite them under Organization settings → Members. Railway only allows outbound SMTP on the Pro plan, so without email, set `AUTH_DISABLE_SIGNUP` to `false` while they sign up with the invited address, then set it back to `true`. On Pro, set `SMTP_CONNECTION_URL` and `EMAIL_FROM_ADDRESS` on both Langfuse services and invitations arrive by email.

**Memory.** Around 1.8 GB at idle: about 800 MB each for the web and worker services, 250 MB for ClickHouse, 100 MB for Postgres. That is more than the Trial plan gives, so deploy on Hobby or above.

**Custom domain.** Add it in the Langfuse service's Settings → Networking, then set `NEXTAUTH_URL` to `https://your.domain`. The worker reads the same value.

**Upgrading.** Change the image tag of both the Langfuse and Worker services to the same new version and redeploy. Keep `SALT` and `ENCRYPTION_KEY` as they are: changing them invalidates API keys and stored LLM credentials.

## Why Deploy Langfuse 4 on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying Langfuse 4 on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
