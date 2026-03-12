# Changelog

All notable changes to idle-less are documented here.

## [3.0.0] - 2026-03-12

### Changed
- **Wakeforce migrated from vanilla PHP to Laravel 12** — full rewrite with proper routing, dependency injection, middleware, and Blade views
- All API endpoints now use clean URLs without `.php` extension (`/api/status` instead of `/api/status.php`)
- Nginx location blocks updated to match new Laravel routing

### Added
- **Persona-based localization** — Danish and English with three tone variants (neutral, technical, humorous) via `TransHelper` and `@persona` Blade directives
- **Configurable no-wake patterns** — `DOMAIN_{i}_NO_WAKE_PATTERNS` env var prevents background API polling from triggering Wake-on-LAN (returns 503 directly)
- **Dev environment** — `docker-compose.dev.yml` with mock backend, license bypass, and hot-reload
- Feature tests for TransHelper

### Fixed
- **Critical: inverted WoL logic** — `KnockController` was sending wake packets when the backend was already online and skipping them when offline
- **NO_WAKE_PATTERNS examples too broad** — `/api/agents/` blocked user-initiated chat requests; narrowed to specific polling endpoints

## [2.8.1] - 2026-03-10

### Added
- **CORS support on alive endpoint**: Cross-origin requests now work out of the box, enabling browser-based dashboards to check server status without `no-cors` mode

## [2.8.0] - 2026-03-10

### Added
- **Server alive endpoint**: New `/api/alive.php` endpoint for checking backend server status without sending Wake-on-LAN packets — ideal for monitoring dashboards, health checks, and pre-flight probes
- Returns `online` boolean, `state`, and `response_time_ms` for latency tracking

## [2.7.0] - 2026-03-10

### Added
- **Server-side wake-and-wait API**: New `/api/wake-and-wait.php` endpoint that blocks until the backend is online, enabling non-browser clients (MCP, curl, APIs) to transparently wake sleeping servers
- **Per-domain backend config via nginx headers**: Each domain's backend settings (IP, MAC, port, health path) are passed as `X-Backend-*` headers to Wakeforce, supporting multi-domain WoL routing from a single Wakeforce instance
- **Accept-header routing**: Nginx automatically routes non-browser requests (`Accept: application/json`) to the wake-and-wait API instead of the browser splash page
- **Shared API utilities**: Extracted common validation, network probing, and HTTP health check functions into `common.php`

### Fixed
- **PHP 8.2 TypeError**: Cast port values to `int` before passing to `isValidPort()` in `status.php` and `knock.php`
- **Nginx if+proxy_pass incompatibility**: Restructured location blocks to avoid the "evil if" anti-pattern with `proxy_pass`
- **JavaScript syntax error**: Replaced Unicode smart quotes with ASCII quotes in gateway splash page
- **Redundant log prefix**: Removed duplicate function name in `http_ok` log message

## [2.6.0] - 2026-03-02

### Changed
- **Enhanced savings calculator**: Added "Number of servers" field, dynamic tier recommendation (Personal/Pro/Business), and direct buy CTA button below savings result for improved conversion

## [2.5.0] - 2026-03-02

### Added
- **Launch urgency banner**: Green "Just launched! First 50 customers get priority support free" banner at top of landing page with scroll-to-pricing click handler

## [2.4.0] - 2026-03-02

### Changed
- **Live checkout links**: Landing page buy buttons now point to real LemonSqueezy checkout (Personal, Pro, Business)

## [2.1.0] - 2026-03-02

### Added
- **License gate**: Wakeforce container now starts even without a valid license, showing a professional "license required" page with purchase CTA instead of crashing
- API endpoints return `403` with pricing URL when license is missing/invalid
- "Pays for itself" ROI message in savings calculator (links to Wakeforce pricing)
- Trust signals under pricing section (secure checkout, instant delivery, no subscription)
- Schema.org structured data updated with all 4 pricing tiers

### Changed
- Entrypoint no longer crashes on license failure — writes status file and starts Apache in limited mode
- Missing LICENSE_KEY now logs as INFO instead of ERROR

## [1.10.0] - 2026-03-02

### Added
- Quick Start section on landing page with one-click install commands
- Visual FAQ section with 4 expandable questions (WoL basics, server support, wake times, Raspberry Pi)
- Container smoke test in CI (builds and health-checks reverse-proxy)
- Install script `--help` validation in CI

### Changed
- CI workflow now triggers on `docker/**` file changes

## [1.9.2] - 2026-03-02

### Security
- Replaced `eval` with bash indirect variable expansion in reverse-proxy entrypoint (eliminates shell injection vector)

### Documentation
- Added variable mapping table to Wakeforce Docker Hub README
- Corrected misleading CHANGELOG entry about DOMAIN_CONFIG values

## [1.9.1] - 2026-03-02

### Fixed
- Removed hardcoded personal domain (`chat.christianogfars.online`) from Wakeforce PHP files
- Added temp file cleanup trap to reverse-proxy entrypoint
- nginx config test now outputs generated config on failure for debugging

### Changed
- All Danish code comments translated to English

## [1.9.0] - 2026-03-02

### Added
- Wakeforce health check endpoint (`/__gateway/api/health.php`) with Docker HEALTHCHECK
- `DOMAIN_{i}_IDLE_SERVICE` environment variable auto-configured during installation

### Fixed
- **Critical**: WoL routing broken — installer set `DOMAIN_CONFIG=backend` but nginx expects `wakeforce` for gateway redirect locations
- `DOMAIN_IDLE_SERVICE` was never written to `.env`, preventing reverse-proxy from redirecting to Wakeforce on backend errors

## [1.8.0] - 2026-03-02

### Added
- Health check endpoint (`/health` on port 8080) with Docker HEALTHCHECK
- `--help` / `-h` flag for install script with usage documentation
- IP address validation during installation
- Custom 404 page for GitHub Pages ("This server is sleeping")

### Changed
- Wakeforce splash page: professional status messages replacing casual text
- Wakeforce about.php: removed broken AI endpoint and debug features
- Wakeforce CSS: deduplicated (842 → 200 lines)
- Changed `lang="da"` to `lang="en"` across Wakeforce pages

## [1.7.1] - 2026-03-02

### Added
- Interactive power savings calculator on landing page (shows annual EUR/kWh savings)
- Smooth scroll behavior for anchor navigation

## [1.7.0] - 2026-03-02

### Added
- MIT license for open-source components (install scripts, docs, configuration)
- Contributing guide (CONTRIBUTING.md) for community engagement
- Mobile hamburger menu on landing page
- Links section in README with landing page, demo, changelog, and security policy

### Fixed
- Updated `DOMAIN_1_CONFIG` example in README (see v1.9.0 for correct value: `wakeforce`)
- Pre-filled Wakeforce inquiry email with qualifying questions (use case, server count)
- Bottom CTA differentiated from pricing CTA (free GitHub start vs contact)

## [1.6.1] - 2026-03-02

### Added
- Landing page with professional dark theme at [tvup.github.io/idle-less](https://tvup.github.io/idle-less/)
- Interactive Wakeforce demo page simulating the boot sequence
- Social sharing card (Open Graph + Twitter Card meta tags)
- FAQ structured data for Google rich snippets
- Docker Hub README documentation for both images
- GitHub issue templates (bug report, feature request)
- CI workflow for install script validation and Docker build testing
- Security policy (SECURITY.md) with vulnerability disclosure process
- SEO files: robots.txt, sitemap.xml, canonical URL, favicon

### Fixed
- Installer default hostname changed from personal domain to `app.example.com`
- Pages workflow removed from private repo (only runs in public repo)

## [1.5.0] - 2026-03-02

### Added
- Static IP assignment for Wakeforce containers on macvlan networks
- Automatic LAN subnet and gateway detection during install
- `DOMAIN_{i}_WF_IP`, `DOMAIN_{i}_SUBNET`, `DOMAIN_{i}_GATEWAY` configuration variables
- Professional ASCII banner in installer

### Fixed
- Use `upstream_name` variable instead of hardcoded "backend" in wf locations
- Pass correct parameters to `generate_wf_locations`

### Changed
- Modularized nginx config generation into separate library scripts
- Renamed variables for clarity across reverse-proxy scripts
- Simplified upstream and location configuration

## [1.4.0] - 2026-03-01

### Added
- Wakeforce Wake-on-LAN gateway integration
- Multi-domain support with independent WoL configuration per domain
- License key validation against validate.torbenit.dk
- Macvlan Docker network for Layer 2 WoL packet delivery
- `--wakeforce` and `--wakeforce-only` install modes
- Interactive installer with domain configuration prompts

### Changed
- Reverse proxy now supports error-triggered gateway redirect
- Docker Compose generation includes Wakeforce service definitions
