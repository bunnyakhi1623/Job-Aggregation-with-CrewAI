Overview
This repository contains two distinct approaches for scraping job postings: a high-level agentic framework (CrewAI) and a low-level browser automation tool (Playwright). The goal was to extract job listings from the career pages of Google, Meta, and Amazon.

Results
The CrewAI agent successfully identified and extracted over 20 software engineering job postings from the Google Careers page. In contrast, the custom Playwright script failed to extract any job data from Google, Meta, or Amazon, resulting in an empty dataset.

Breakdown by site:

Google: >20 jobs (via CrewAI)

Meta: 0 jobs

Amazon: 0 jobs

Observations
Why CrewAI Succeeded 🚀
High Abstraction: CrewAI's ScrapeWebsiteTool automatically handles complex tasks like running a full browser instance and waiting for dynamic content to render. This makes it resilient to the JavaScript-heavy nature of modern websites.

LLM-based Parsing: The LLM-based parsing makes the extraction process flexible and less dependent on fragile CSS selectors.

Why Playwright Failed 💔
Dynamic Rendering: The primary issue was that the targeted career pages are Single-Page Applications (SPAs), and the job listings are loaded dynamically via JavaScript after the initial page load.

Fragile Waiting Strategy: The Playwright script's fixed sleep time and domcontentloaded event were insufficient, as they fired before the dynamic content had been fetched and rendered.

Incorrect Selectors: Because the dynamic content never loaded, the CSS selectors in the Playwright script found zero matching elements.

Lessons Learned
Framework	Strengths	Weaknesses
CrewAI	🚀 Rapid Prototyping: Excellent for quickly achieving a result by abstracting away complex mechanics.	⚫ Black Box & Dependency: Difficult to debug why a tool might fail. Relies on external API keys, adding costs and points of failure.
Playwright	🔧 Granular Control: Offers precise control for building highly efficient scrapers when the site structure is well-understood.	Brittle & High Maintenance: Highly dependent on the target website's structure. Small front-end updates can break the script, requiring constant, site-specific maintenance.

Export to Sheets
Next Steps: A Hybrid Approach
The optimal solution is a hybrid model that leverages the strengths of both frameworks.

1. Data Retrieval Layer: Use a robust, managed browser service (like the one CrewAI uses) to reliably fetch the fully rendered HTML. This solves the primary failure point of the manual Playwright script.

2. Primary Parsing Layer (Rules): Use specific, efficient selectors (e.g., with Playwright or BeautifulSoup) for known website structures. This is fast, cheap, and reliable.

3. Fallback Parsing Layer (LLM Agent): If the primary rule-based parser fails, automatically fall back to an LLM-based agent (similar to CrewAI's) to perform a "best-effort" extraction from the raw HTML. This provides resilience against unexpected site layout changes.

Potential Improvements
DB Storage: Store all extracted job data in a structured database (e.g., PostgreSQL) to enable tracking, analysis, and prevent duplicates.

Concurrency: Re-architect the code to run scraping tasks for multiple companies in parallel to significantly improve speed.

Caching: Implement a caching layer to store HTML and avoid redundant scraping.
