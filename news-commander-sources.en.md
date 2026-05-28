# News Commander — Source Catalog

> Status: **v1.2.1** · 2026-05-28 · **42 active sources** across 14 categories
> All sources are public Microsoft domains (blogs.microsoft.com,
> techcommunity.microsoft.com, devblogs.microsoft.com, learn.microsoft.com,
> news.microsoft.com, msrc.microsoft.com, adoption.microsoft.com, …) and
> are consumed either via RSS/Atom, an official JSON API, or HTML scrape.

## Overview by Category

| Category | Active sources | Focus |
|----------|----------------|-------|
| `de-lokal` | 2 | DE-specific community content and events |
| `corporate` | 8 | Strategy, leadership, policy, AI news |
| `m365-copilot-core` | 5 | M365 + Copilot core (TOP-PRIO) |
| `workload` | 6 | Excel / Outlook / Teams / SharePoint / EOS |
| `security` | 4 | Defender, MSRC, CVE advisories |
| `identity-iam` | 1 | Entra / identity |
| `dev-platform` | 4 | Visual Studio, PowerShell, Mechanics, Azure Updates |
| `agents-core` | 1 | Agents, MCP, Agent Framework |
| `power-platform` | 3 | Power Platform / Apps / Automate |
| `browser-web` | 1 | Edge, Copilot Mode, agentic browsing |
| `windows-tooling` | 1 | Sysinternals |
| `adoption-learn` | 3 | Adoption portal, release notes, Customer Hub webinars |
| `sustainability-a11y` | 2 | Accessibility, Sustainability |
| `public-sector-ft` | 2 | FastTrack, Community News Desk |
| `events` | 1 | Central DE events portal |

## Full source list

| ID | Title | Category | Tags | Link | Description |
|----|-------|----------|------|------|-------------|
| `techwiese-events` | Techwiese Events | `de-lokal` | events, de, community, training, virtual-training-day, meetup | [Link](https://www.microsoft.com/de-de/techwiese/feeds/rss/events.aspx) | DE events feed (must-have); mix of DE community events and global trainings in German |
| `techwiese-aktuell` | Techwiese Aktuell | `de-lokal` | news, de, mixed, azure, foundry, agents, dev | [Link](https://www.microsoft.com/de-de/techwiese/feeds/rss/aktuell.aspx) | DE umbrella feed; broad mix of Azure / Foundry / Agents / Dev content in German |
| `official-msft-blog` | Official Microsoft Blog | `corporate` | corporate, strategy, leadership | [Link](https://blogs.microsoft.com/) | Brad / Satya / Judson posts; official corporate voice |
| `news-signal` | News Signal | `corporate` | strategy, opinion, leadership, ai, cloud, chair-cto | [Link](https://news.microsoft.com/signal/home/) | HTML scrape; strategic opinion pieces from the chair / CTO level |
| `news-source` | The Source (Innovation Stories) | `corporate` | innovation, longread | [Link](https://news.microsoft.com/source/) | HTML scrape; long-form innovation storytelling |
| `unlocked` | Microsoft Unlocked | `corporate` | storytelling, brand | [Link](https://unlocked.microsoft.com/) | HTML scrape; brand storytelling |
| `on-the-issues` | On The Issues | `corporate` | policy, legal, regulation, eu, eu-data-boundary, dma, ai-act | [Link](https://blogs.microsoft.com/on-the-issues/) | High priority for EU / compliance — DMA, AI Act, EU Data Boundary |
| `microsoft-ai-news` | Microsoft AI News | `corporate` | ai, copilot, announcements, azure-openai, copilot-studio | [Link](https://microsoft.ai/news/) | HTML scrape; central AI announcements |
| `accessibility` | Microsoft Accessibility | `sustainability-a11y` | accessibility, inclusion | [Link](https://blogs.microsoft.com/accessibility/) | Accessibility / inclusion |
| `sustainability` | Microsoft Sustainability | `sustainability-a11y` | sustainability, esg | [Link](https://www.microsoft.com/en-us/sustainability) | HTML scrape; ESG / sustainability |
| `tc-m365copilot` | M365 Copilot Blog (TechCommunity) | `m365-copilot-core` | copilot, m365, adoption, copilot-chat, business-chat, m365-admin-center, agents, agent-builder, copilot-studio | [Link](https://techcommunity.microsoft.com/category/microsoft365copilot/blog/microsoft365copilotblog) | **TOP-PRIO** for CSA role |
| `m365-blog` | Microsoft 365 Blog | `m365-copilot-core` | m365, productivity, copilot, loop, clipchamp, whiteboard, designer, outlook, teams | [Link](https://www.microsoft.com/en-us/microsoft-365/blog/) | Must-have; HTML scrape (no real RSS) |
| `m365-dev-blog` | M365 Developer Blog | `m365-copilot-core` | dev, graph, apis, m365, graph-api, teams-toolkit, copilot-extensibility, agents, agent-builder | [Link](https://devblogs.microsoft.com/microsoft365dev/) | Graph API, Teams Toolkit, Copilot extensibility |
| `m365-roadmap` | Microsoft 365 Roadmap | `m365-copilot-core` | roadmap, future-features, copilot, teams, outlook, sharepoint, exchange, purview | [Link](https://www.microsoft.com/en-us/microsoft-365/roadmap) | Public JSON API v1; per-item URL = `…/roadmap?id={id}` |
| `tc-m365blog` | TechCommunity M365 Blog | `m365-copilot-core` | m365, blog, copilot, viva, loop | [Link](https://techcommunity.microsoft.com/category/microsoft365/blog/microsoft_365blog) | Cross-cutting M365 blog (Copilot / Viva / Loop) |
| `tc-excel` | Excel Blog | `workload` | excel, productivity, copilot-in-excel, python-in-excel | [Link](https://techcommunity.microsoft.com/category/excel/blog/excelblog) | Copilot in Excel + Python in Excel |
| `tc-outlook` | Outlook Blog | `workload` | outlook, mail, copilot-in-outlook, new-outlook | [Link](https://techcommunity.microsoft.com/category/outlook/blog/outlook) | New Outlook + Copilot in Outlook |
| `tc-teams` | Microsoft Teams Blog | `workload` | teams, collaboration, teams-rooms, teams-premium, copilot-in-teams | [Link](https://techcommunity.microsoft.com/category/microsoftteams/blog/microsoftteamsblog) | Must-have; Teams + Rooms + Premium + Copilot in Teams |
| `tc-teams-community` | Microsoft Teams Community Blog | `workload` | teams, community | [Link](https://techcommunity.microsoft.com/category/microsoftteams/blog/microsoftteamscommunityblog) | Teams community / tips |
| `tc-sharepoint` | SharePoint Blog | `workload` | sharepoint, content, syntex, viva, copilot-in-sharepoint, lists | [Link](https://techcommunity.microsoft.com/category/microsoft365/blog/spblog) | SharePoint / Syntex / Lists / Copilot |
| `tc-eos` | Office EOS Blog | `workload` | office, eos, lifecycle, windows-10-eos, office-2019-eos, office-2021-eos, microsoft-365-apps | [Link](https://techcommunity.microsoft.com/category/microsoft365/blog/officeeos) | Lifecycle / EOS relevance (Win10 EOS, Office 2019/2021) |
| `tc-security` | Microsoft Security Blog (TechCommunity) | `security` | security, defender, defender-xdr, sentinel, purview, defender-for-cloud | [Link](https://techcommunity.microsoft.com/category/security/blog/microsoft-security-blog) | **TOP-PRIO** security |
| `security-blog` | Microsoft Security Blog (corp) | `security` | security, xdr, defender, defender-xdr, sentinel, purview, copilot-for-security | [Link](https://www.microsoft.com/en-us/security/blog) | HTML scrape; check for duplicates against tc-security |
| `msrc-blog` | MSRC Blog | `security` | msrc, vulnerability, advisories, cve, patch-tuesday | [Link](https://www.microsoft.com/en-us/msrc/blog) | HTML scrape (the msrc feed is bot-blocked) |
| `sec-advisories` | MSRC Security Updates (CVRF) | `security` | cve, advisory, msrc, patch-tuesday | [Link](https://msrc.microsoft.com/update-guide/) | MSRC CVRF v3.0 JSON API; monthly Patch Tuesday releases |
| `tc-entra` | Microsoft Entra Blog | `identity-iam` | entra, identity, aad, entra-id, conditional-access, verified-id, external-id, privileged-identity | [Link](https://techcommunity.microsoft.com/category/microsoftsecurityandcompliance/blog/microsoft-entra-blog) | v1.2.1 re-bucketed security → identity-iam |
| `vs-blog` | Visual Studio Blog | `dev-platform` | vs, dev, dotnet, agents, copilot | [Link](https://devblogs.microsoft.com/visualstudio/) | Low priority; increasingly Copilot / Agents content for dev workflows |
| `powershell-blog` | PowerShell Team Blog | `dev-platform` | powershell, automation, powershell-gallery, cross-platform | [Link](https://devblogs.microsoft.com/powershell/) | PowerShell + Gallery + cross-platform |
| `tc-mechanics` | Microsoft Mechanics Blog | `dev-platform` | mechanics, deepdive, video, cross-workload, copilot, azure-openai, fabric, purview, defender-xdr | [Link](https://techcommunity.microsoft.com/category/microsoftmechanics/blog/microsoftmechanicsblog) | Cross-workload tech deep-dives, video-first |
| `azure-updates` | Azure Updates | `dev-platform` | azure, release, roadmap, azure-openai, fabric, ai-foundry, arc | [Link](https://azure.microsoft.com/en-us/updates/) | MS Release Communications JSON API v1; per-item URL = `…?id={id}` |
| `agents-hub` | Agents Hub (Learn) | `agents-core` | agents, agentic, copilot-studio, agent-builder, mcp, agent-framework | [Link](https://learn.microsoft.com/en-us/agents/) | v1.2.1 re-bucketed dev-platform → agents-core; static Learn section |
| `pp-blog` | Power Platform Blog | `power-platform` | power-platform, low-code, copilot-studio, agent-builder, mcp, dataverse | [Link](https://www.microsoft.com/en-us/power-platform/blog/) | HTML scrape; Copilot Studio + Agent Builder + MCP + Dataverse |
| `pp-apps` | Power Apps Blog | `power-platform` | power-apps, canvas, agents, mcp | [Link](https://www.microsoft.com/en-us/power-platform/blog/power-apps/) | HTML scrape; increasingly Agent Builder / MCP content |
| `pp-automate` | Power Automate Blog | `power-platform` | power-automate, flow, copilot, ai-builder, cloud-flows, desktop-flows | [Link](https://www.microsoft.com/en-us/power-platform/blog/power-automate/) | HTML scrape; cloud + desktop flows + AI Builder |
| `edge-blog` | Microsoft Edge Dev Blog | `browser-web` | edge, browser, web, agentic-browsing, copilot, copilot-mode, webview2, pwa, manifest-v3 | [Link](https://blogs.windows.com/msedgedev/) | v1.2.1 re-bucketed windows → browser-web (Copilot Mode + agentic browsing) |
| `tc-sysinternals` | Sysinternals Blog | `windows-tooling` | sysinternals, troubleshoot, process-explorer, procmon, autoruns, windows-server | [Link](https://techcommunity.microsoft.com/category/windows-server/blog/sysinternals-blog) | v1.2.1 re-bucketed dev-platform → windows-tooling |
| `adoption-portal` | Adoption.microsoft.com | `adoption-learn` | adoption, methodology, events, webinars, training | [Link](https://adoption.microsoft.com/) | HTML scrape; portal with adoption content + linked events / webinars / trainings |
| `adoption-release` | Adoption Release Notes | `adoption-learn` | release, adoption, events, webinars, training | [Link](https://adoption.microsoft.com/en-us/release-notes/) | HTML scrape; release notes + Customer Hub events / webinars |
| `adoption-customer-hub` | Adoption Customer Hub Webinars | `adoption-learn` | webinars, copilot, copilot-chat, agents, adoption, training, events | [Link](https://adoption.microsoft.com/en-us/customer-hub/) | **Must-have for event queries**; 315+ Copilot / Agents sessions paginated |
| `tc-fasttrack` | FastTrack Blog | `public-sector-ft` | fasttrack, deployment, copilot, migration | [Link](https://techcommunity.microsoft.com/category/fasttrack/blog/fasttrackblog) | Must-have (pairs with the fasttrack skill); deployment guidance |
| `tc-community-news` | Community News Desk | `public-sector-ft` | community, events, webinars, de, ama | [Link](https://techcommunity.microsoft.com/category/communitynewsdesk/blog/communitynewsdesk) | TC community news + AMAs |
| `msft-de-events` | Microsoft Events, Webinars & Conferences (DE) | `events` | events, webinars, de, copilot, ai, training | [Link](https://www.microsoft.com/de-de/events) | Central DE events portal; HTML scrape returns title + date + language reliably |

## Feed types

| `feed_type` | Used for | Sources |
|-------------|----------|---------|
| `rss` | Standard RSS 2.0 (XML) | TechCommunity boards, DevBlogs, Techwiese, Edge, Accessibility, On The Issues, Official Blog |
| `atom` | Atom 1.0 | none currently active |
| `html` | BeautifulSoup scrape | M365 Blog, Power Platform Blog, Microsoft AI News, MSRC Blog, Security Blog (corp), Adoption Portal, Sustainability, News Signal / Source, Unlocked, msft-de-events, adoption-customer-hub, agents-hub |
| `json_m365_roadmap` | Official Release Communications API v1 | m365-roadmap |
| `json_azure_updates` | Official Release Communications API v1 | azure-updates |
| `json_msrc_cvrf` | MSRC CVRF v3.0 JSON API | sec-advisories |

---

_Generated from `data/sources.json` (v1.2.1). Catalog changes are tracked in_
_[RELEASE_NOTES.md](RELEASE_NOTES.md). License: MIT (skill); source content is_
_subject to the respective Microsoft terms._
