---
version: 1.0
created: 2025-01-27
---

# Detection Patterns

## How Skills Are Identified

### Pattern 1: Keyword + Action
When an action verb appears with a technical keyword, the relevant skill is identified.

**Examples:**
- "configured Drupal" → WEBA Level 3
- "maintained 99.9% uptime" → ITOP Level 4
- "secured SSL certificates" → SCAD Level 3

### Pattern 2: Special Achievements
Certain achievements automatically indicate skill levels.

**Automatic Level 4 Triggers:**
- "99.9% uptime" or "99.99% uptime" → ITOP/SLMO Level 4
- "AI pilot program" → INCA Level 4
- "$50,000+ savings" → FMIT Level 4
- "Drupal 10 migration" → WEBA Level 4

### Pattern 3: Complexity Indicators
Words that modify the skill level assessment.

**Simple (Level 2):** basic, simple, routine, assisted
**Standard (Level 3):** independently, resolved, managed
**Complex (Level 4):** architected, designed, led, strategic
**Strategic (Level 5):** transformed, established, governed

### Pattern 4: Project ID Components
CAIRN project IDs indicate skills.

**System Codes (xxxx):**
- drpl (Drupal) → WEBA, CFMG
- wprs (WordPress) → WEBA, CFMG
- api → PROG, SINT
- sec → SCAD, SCTY

**Action Codes (yyyy):**
- migr (migration) → CHMG, SINT
- fix (fixes) → PBMG, ITOP
- docs (documentation) → KNOW
- bkup (backup) → BPRE, DLMG

## Evidence Quality Scoring

Confidence is assigned based on:

**HIGH Confidence:**
- Clear action + outcome
- Measurable metrics (99.99% uptime, $50K+ savings)
- Independent work
- Production impact
- Strategic/transformational results

**MEDIUM Confidence:**
- Standard task completion
- Some assistance
- Testing/staging work
- Process improvements

**LOW Confidence:**
- Vague descriptions
- Heavy assistance
- No clear outcome
- Basic task execution

## Skill Detection Keywords

### PROG - Programming
**Keywords:** Python, JavaScript, PHP, bash, script, code, API, automation, development
**Actions:** wrote, coded, developed, automated, refactored, programmed
**Context:** "Created Python script", "Developed API integration"

### ITOP - IT Operations
**Keywords:** server, infrastructure, deployment, monitoring, uptime, performance
**Actions:** managed, maintained, deployed, monitored, operated
**Metrics:** 99.9% uptime, response time, availability

### SCAD - Security Administration
**Keywords:** SSL, certificate, firewall, vulnerability, patch, security
**Actions:** secured, patched, hardened, encrypted, protected
**Compliance:** GDPR, HIPAA, security standards

### WEBA - Web Architecture
**Keywords:** Drupal, WordPress, CMS, architecture, web, site
**Actions:** architected, designed, migrated, optimized, built
**Special:** "Drupal 10 migration" = Level 4

### DLMG - Data Management
**Keywords:** DAM, digital assets, metadata, cloud storage, backup, data
**Actions:** organized, cataloged, archived, managed, stored
**Scale:** terabytes, thousands of files

### INCA - Innovation
**Keywords:** AI, automation, Claude, GPT, innovative, pilot, transformation
**Actions:** automated, transformed, integrated, innovated
**Special:** "AI pilot program" = Level 4

### FMIT - Financial Management
**Keywords:** cost, budget, savings, ROI, financial, optimization
**Actions:** reduced costs, saved, optimized, budgeted
**Metrics:** Dollar amounts, percentages

### CFMG - Configuration Management
**Keywords:** configuration, settings, deployment, staging, setup
**Actions:** configured, deployed, standardized, managed
**Context:** CMS configurations, server setups

### SLMO - Service Level Management
**Keywords:** uptime, performance, SLA, monitoring, availability
**Actions:** monitored, maintained, improved, measured
**Metrics:** 99.9% uptime, response times

### BPRE - Business Process Re-engineering
**Keywords:** workflow, process, automation, efficiency, streamline
**Actions:** streamlined, automated, redesigned, improved
**Impact:** time savings, efficiency gains

### KNOW - Knowledge Management
**Keywords:** documentation, wiki, training, knowledge base, guides
**Actions:** documented, created guides, trained, wrote
**Context:** SOPs, training materials, documentation

### PBMG - Problem Management
**Keywords:** troubleshooting, debugging, root cause, issue, problem
**Actions:** diagnosed, resolved, fixed, investigated
**Context:** System issues, bugs, failures

### CHMG - Change Management
**Keywords:** migration, upgrade, deployment, rollback, change
**Actions:** planned, migrated, deployed, upgraded
**Context:** System changes, version upgrades

### SINT - Systems Integration
**Keywords:** API, integration, sync, connector, interface
**Actions:** integrated, connected, synchronized, interfaced
**Context:** System connections, data flows

### SCTY - Information Security
**Keywords:** security, encryption, access control, audit, compliance
**Actions:** secured, protected, audited, complied
**Compliance:** Security standards, regulations

## Additional Platform-Specific Keywords

### Platform Tools
```yaml
hosting_platforms:
  - Pantheon (Drupal)
  - DreamHost (WordPress)
  - Azure, AWS
  - Cloudflare

development_tools:
  - Lando, Composer, Git
  - Xcode, Android Studio
  - Google Colab, Jupyter

media_platforms:
  - JW Player, Vimeo
  - SoundCloud
  - Livestream

analytics_tools:
  - Google Analytics, GTM
  - New Relic, Google Search Console

payment_systems:
  - PayPal, Stripe
  - Salesforce integration

ai_ml_tools:
  - Stable Diffusion
  - Google Colab
  - AI prompting, generative AI

creative_tools:
  - Photoshop
  - Audio/video production
  - Content creation
```

### Special Achievement Patterns
```yaml
automatic_level_4_triggers:
  - "99.99% uptime" → AVMT Level 4
  - "AI pilot program" → ARTB Level 4
  - "$50,000+ savings" → FMIT Level 4
  - "1M+ audience reach" → AUVP/METL Level 4
  - "Drupal 10 migration" → CHMG Level 4

financial_impact_indicators:
  - Cost savings with dollar amounts
  - ROI measurements
  - Budget management
  - Vendor cost optimization

strategic_impact_indicators:
  - "transformational"
  - "organization-wide"
  - "strategic collaboration"
  - "setting standards"
```
