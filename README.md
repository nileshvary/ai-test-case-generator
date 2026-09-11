# AI Test Case Generator

Generate detailed QA test cases from a short description and push them straight into Jira — built as a self-hosted [n8n](https://n8n.io) workflow powered by Google Gemini.

Writing test cases was never the hard part — **remembering all of them** was. This automation turns the enumeration work over to a model: you describe the feature, topology and build under test, and it expands that into a full set of structured test cases and files them as tickets. Deciding *what's worth testing* stays with you.

> Originally built for hardware & networking QA (VPN, QNAP/Synology NAS, multicast/IGMP), but it works for **any** kind of test-case generation.

---

## How it works

```mermaid
flowchart LR
    A["📝 Form<br/>Device · Scope · Count"] --> B["🤖 Google Gemini<br/>generate structured test cases"]
    B --> C["🧩 Parse JSON<br/>one item per test case"]
    C --> D["🎫 Jira<br/>create a Task per test case"]
```

1. **Form** — you enter the *Device/Topic*, the *Scope* (feature, topology, build under test) and how many cases you want.
2. **Google Gemini** — prompted as a senior QA engineer, it returns test cases as strict JSON (covering functional, negative, boundary, security, performance and interoperability).
3. **Parse JSON** — a small code step splits the AI output into one item per test case (`test_id`, `title`, `category`, `priority`, `preconditions`, `steps`, `test_data`, `expected_result`).
4. **Jira** — each test case is created as a Task issue, with all details mapped into the description.

### Screenshot

> _Add a screenshot of the workflow canvas here (`assets/workflow.png`)._

![Workflow](assets/workflow.png)

---

## Who is this for?

- **QA / test engineers** who write repetitive test cases for hardware, networking, storage, APIs or web apps.
- **Product & platform teams** who want consistent, documented test coverage per feature or firmware build.
- **Anyone** who wants to skip the blank page and start from a solid first draft.

---

## Requirements

- A running **n8n** instance (self-hosted or cloud) — tested on n8n **2.35**.
- A **Google Gemini API key** — free tier is enough. Get one at [aistudio.google.com](https://aistudio.google.com).
- A **Jira Cloud** account + **API token** — create one at [id.atlassian.com/manage-profile/security/api-tokens](https://id.atlassian.com/manage-profile/security/api-tokens).

> 🔐 Your keys live in **n8n's own encrypted Credentials store** — they are never part of this workflow file.

---

## Setup — use it with your own API keys

**1. Import the workflow**
- In n8n: **Workflows → Import from File** → choose [`workflow/ai-test-case-generator.json`](workflow/ai-test-case-generator.json).
  _(Or open the JSON, copy it, and paste directly onto the canvas.)_

**2. Add your credentials** (n8n → **Credentials → Add credential**)
- **Google Gemini(PaLM) API** → paste your Gemini API key.
- **Jira SW Cloud API** → your Atlassian email + API token + domain (e.g. `https://your-org.atlassian.net`).

**3. Configure the two nodes**
- **Google Gemini** node → select your Gemini credential, and pick an available **Model** (see note below).
- **Create Test Case in Jira** node → select your Jira credential, then choose your **Project** and **Issue Type** (e.g. `Task`).

**4. Run it**
- Click **Publish**, open the form's **Production URL**, fill in *Device / Scope / Count*, and submit.
- Watch results in the **Executions** tab, then check your Jira project for the new issues.

---

## Notes & gotchas

- **Gemini model name:** Google retires older models for new accounts (e.g. `gemini-2.0-flash` / `gemini-2.5-flash` may return `404 – no longer available to new users`). Pick a **current "flash" model** your account offers, such as `models/gemini-3.6-flash`. Any fast "flash" model works; avoid "pro"/"deep-research"/"preview" for cost and speed.
- **Jira free plan:** the free tier has no Xray/Zephyr "Test" issue type, so test cases are created as **Task** issues with the steps/expected result in the description. Upgrade later to remap to a dedicated Test type.
- **Privacy:** everything runs on your own n8n; nothing is exposed publicly unless you choose to.

---

## Roadmap

- **v2 — Research step:** add live web research (Gemini Google-Search grounding) so each test case is grounded in current device docs, protocols and known issues.
- **Alternative outputs:** Google Sheets, Excel, or TestRail instead of (or alongside) Jira.

---

## Author

**Nileshwari Kadgale**

## License

[MIT](LICENSE)
