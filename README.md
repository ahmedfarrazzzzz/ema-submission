# Offboarding Analyst

**Live demo:** [add your link here]

When someone leaves a company, HR, IT, Payroll and their manager each own a piece of the exit, and nobody owns the whole thing. So access stays open for weeks, the final settlement drags on, and the person who left ends up chasing their relieving letter months later.

This is a working prototype of an AI employee that owns the whole exit. You give it an employee ID and it:

- finds every account the person has, including the ones outside single sign-on that IT usually misses
- hands over what they own (repos, customers, approvals) to named people before anything is switched off
- removes access on time, or within the hour if it's a termination
- works out the final settlement line by line, citing the policy behind every rupee
- prepares the letters, payslips and tax documents and sends them to the person's personal email

A human approves every step that touches money or access. The agent does the legwork; people make the calls.

## Why I built it

I put this together as my answer to Ema's APM case prompt. Ema builds "AI employees" for HR, IT and Finance, and an exit touches all three. When I first pitched the idea, someone at Ema asked me, fairly, whether it was just a checklist. This prototype is my answer: it doesn't list the steps, it does them.

It's built for India first (the 2-working-day settlement rule under the Code on Wages, gratuity, Form 16), but the policy library is the only part that's country-specific.

## Try it in three minutes

Open the demo and click **Guided demo** in the top right. It walks you through ten moments that matter:

- someone whose last day was yesterday but whose accounts are still live
- a gratuity edge case where the policy and court rulings disagree, so the agent asks a human instead of guessing
- a settlement mistake that a second model catches before Payroll sees it
- what the person leaving actually receives
- a termination where every access change is timed to the HR meeting

Or just open **Live workspace** and click any name. The agent runs live each time and ticks off what it's doing as it goes.

## What's in here

- **Overview:** the problem, what the agent does, and what changes for each team
- **Live workspace:** eight exits, each with very different problems. For each one you'll see:
  - the plan and the agent's steps side by side
  - the accounts, the settlement, documents you can open, the email the employee gets
  - an audit of the settlement, a full run log and an audit trail
- **Exit insights:** what 38 exits a month reveal: accounts outside SSO, apps nobody approved, passwords nobody rotated, a team people keep leaving
- **Under the hood:** the actual prompts the agent runs, what the model sees, and a small eval that scores the output

It works on a phone too.

## What's real and what isn't

The company, the people, the accounts and the money are all made up. Northwind Labs doesn't exist, and every ID and account number is fictional and masked.

The design choices are real, though, and they're the part I care about most:

- **The model never does arithmetic.** It picks the formula and its inputs and cites the policy. Code calculates the amount, and a second model checks the choice.
- **Hand over first, then remove access.** Accounts are suspended before they're deleted, so mistakes can be undone.
- **Legal text comes from templates.** The model fills in fields; it doesn't write the letter.
- **Customer-written text is data, not instructions.** One exit survey in the demo tries to talk the agent into adding a bonus. It doesn't work.
- **Each person only sees their own data.**

I also checked whether every action could actually be built today. Each account in the workspace says how it would run:

- **API** for 36 of the 48 actions
- **Admin console** (browser automation) for 9, where the vendor has no usable API
- **Owner task** for 3, where only the person leaving can do it (Figma, for example, only lets the owner transfer files)

The legal rules are simplified for a demo and aren't legal advice. Lawyers still debate how far the 2-day rule reaches beyond "wages", and the prototype says so.

## Running it locally

There's no build step. Clone the repo and open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8766
```

Then go to http://localhost:8766.

To host it, drag the folder onto [Netlify Drop](https://app.netlify.com/drop), or turn on GitHub Pages for the repo.

## Scoring it against a real model (optional)

The prompts in "Under the hood" are the ones the agent would actually run. If you have an Anthropic API key, `eval/run_eval.py` runs them against Claude on all eight exits. It scores four things:

- the status matches what an experienced HR person would decide
- the net settlement is exact to the rupee
- every high-risk account gets an action
- every line cites a source

```bash
pip install anthropic
export ANTHROPIC_API_KEY=your-key
python eval/run_eval.py
```

## How it's put together

```
index.html          the page
assets/data.js      the made-up company, its policies and the eight exits
assets/live.js      the prompts and the output formats the model must follow
assets/app.js       everything that moves: the agent runs, settlements, documents, tour
assets/styles.css   layout and theme
assets/fonts/       DM Sans (Open Font License)
eval/               the scoring script and its test cases
docs/               research notes and the feasibility check
```

Plain HTML, CSS and JavaScript. No framework, no dependencies.

## A note

This is an independent concept I made for Ema's APM case. It isn't an Ema product and isn't affiliated with or endorsed by Ema. If you're from Ema and want to talk about it, I'd love to hear what I got wrong.

Ahmed Faraz
