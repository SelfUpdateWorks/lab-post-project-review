# Post-Project Review – Science in Plain Words

## Part 1: Project snapshot

## Part 2: Stakeholder impact

## Part 3: From demo to "real project"

## Part 4: Revision brief

## Checklist
- [ ] Recognize
- [ ] Apply
- [ ] Integrate
- [ ] Verify
- [ ] Stretch

Part 1: Project snapshot (3–5 sentences)

Scientific articles share valuable new knowledge, but their academic language keeps them from reaching people outside research. Our project, Science in Plain Words, was meant to close that gap. The intended users are non-academic but curious people who want to follow scientific developments without a research background. We built an MVP Gradio app that takes a link or a PDF of a scientific article and turns it into a title and three plain-language takeaways. The summary can be generated in 8 languages and in a serious or funny tone, and the app converts it into a short audio podcast.


Part 2: Stakeholder table
| Stakeholder | Direct / Indirect | Role / relationship | What they need | Risk if ignored | Influence | Interest |
|---|---|---|---|---|---|---|
| Curious non-academic readers | Direct | Primary users | Accurate, easy summaries they can trust | Oversimplified or wrong summaries → misinformation, users leave | Low | High |
| Students | Direct | Secondary users (learning, quick overview) | Summaries that point back to the original source | They cite the summary instead of the paper → academic errors | Low | High |
| Scientists (article authors) | Indirect | Their work is being represented | Faithful representation of findings and limits (e.g. preprint status) | Misrepresented research → public criticism, loss of credibility | High | High |
| Journals & publishers | Indirect | Own the copyright of the articles | Respect for copyright, paywalls and attribution | Legal takedown, blocked access to PDFs | High | Low |
| Newspapers / science journalists | Indirect | Potential partners or competitors | Reliable, fast summaries as a starting point | Seen as a low-quality competitor instead of a partner | High | Low |
| API provider (OpenAI) | Indirect | Provides LLM and TTS models | Usage within terms, paid usage | Price changes, rate limits or outages stop the whole app | High | Low |
| Dev team / maintainers ("future you") | Direct | Build and maintain the app | Clear code, docs and monitoring | Nobody can fix it when it breaks; API key exposed | High | High |
| **Stretch:** a reader making a health decision | Direct | Reads a medical study summary in a stressful moment | A clear warning that a summary is not advice | Acts on a simplified finding (e.g. a preprint) → real harm | Low | High |


## Part 3: From demo to "real project"

### 1. Security & compliance
**Now:** The OpenAI API key lives in a local `.env` file that is excluded by `.gitignore`, and the app runs only on our own laptops with no user accounts. Anyone with access to the running app could use it without limits, so our API key pays for every request.
**For production:** The key would move to a secrets manager on the hosting platform, and we would add rate limiting per user so one person can't drain the API budget. Because users upload PDFs, we would also need a privacy notice and a clear statement that uploads are not stored.

### 2. Operations
**Now:** If the OpenAI API is down or slow, the user sees a generic `gr.Error` message, and nobody is notified. There is no monitoring, no logging of failures, and the "on-call person" is whichever teammate happens to notice.
**For production:** We would log every request (success, failure, duration, cost), set an alert when the error rate rises, and name one owner for incidents. A status message like "The audio service is busy – here is your text summary" would keep the app useful when only the TTS step fails.

### 3. Data lifecycle & copyright
**Now:** Article text is only cached in memory (`lru_cache`) and the MP3 files are written to an `outputs/` folder that is never cleaned up. We never checked whether summarizing and converting paywalled articles to audio is allowed.
**For production:** Generated audio would be deleted automatically after a fixed time (e.g. 24 hours). We would start with open-access sources only (arXiv, PLOS, bioRxiv) and check publisher terms before supporting any paywalled journal.

### 4. Scope beyond the demo: edge cases and accuracy
**Now:** We tested mostly with arXiv papers. Long papers are cut at 30,000 characters, so findings late in a paper can be missed, and the only quality check is a length check with one retry. Nobody verified that the takeaways are faithful to the paper.
**For production:** We would build a test set of ~30 articles from different fields and have a scientist rate each summary for accuracy. Health and medical articles would get a visible disclaimer ("This is a simplified summary, not medical advice"), and preprints would always be labelled as not yet peer-reviewed.

### 5. Handoff
**Now:** The README explains how to install and run the app, and each of the four files has one clear job. There are no automated tests, and knowledge of why the prompt rules exist (e.g. the date-wording rule) lives only in our heads.
**For production:** We would add basic tests for each pipeline step, document the prompt design decisions, and define "done" for support: who fixes a broken summary, and how fast.


## Part 4: Revision brief

**Before:** We wanted to build an app that turns any scientific article, from a link or a PDF, into three plain-language takeaways and an audio podcast, in 8 languages and two tones. Success meant that the full pipeline worked end to end in our demo and that the output sounded easy to understand.

**After:** We would narrow the MVP to open-access articles and two languages (English and German) and make accuracy the main success criterion, not just readability. Success would mean: at least 90% of summaries rated "faithful to the paper" by a scientist reviewer on a 30-article test set, a preprint or health disclaimer shown wherever relevant, and a cost per summary that we track and cap. We would also add a security and copyright review as a gate before any public launch. More languages and tones would come only after the core summary is proven trustworthy.


## Checklist
- [x] **Recognize:** Project named (Science in Plain Words); intended beneficiary: curious non-academic readers.
- [x] **Apply:** Stakeholder table with 8 rows, each with need, risk and influence/interest.
- [x] **Integrate:** "Demo → real" covers 5 dimensions with concrete changes.
- [x] **Verify:** Revision brief shifts success from "it works and sounds simple" to "faithful, safe and affordable".
- [x] **Stretch:** Added a reader making a health decision in a stressful moment, a quiet but critical stakeholder.