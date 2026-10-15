# Rooted Rupees — Website

A plain static HTML/CSS/JS website — no build step, no framework, no server
required. Upload the whole folder to any static host and it works.

## Folder structure

```
rooted-rupees/
├── index.html              Home page
├── about-us.html           About Us
├── what-we-do.html         What We Do
├── resources.html          Resources hub + all calculators
├── learn.html               Learn hub (case study library)
│
├── learn/
│   ├── play.html            Generic case-study game player (reads ?study=slug)
│   └── case-studies/
│       ├── index.js         REGISTRY — one entry per case study, shown on the Learn hub
│       ├── _template/       Copy this folder to start a new case study
│       │   └── data.js
│       └── emergency-fund-101/     Example case study (Level 1)
│           └── data.js
│
├── assets/
│   ├── css/
│   │   ├── style.css        Core design system (colors, type, nav, footer, shared sections)
│   │   ├── calculators.css  Resources / calculator page styling
│   │   └── learn.css        Learn hub + game player styling
│   ├── js/
│   │   ├── main.js          Mobile nav + newsletter form
│   │   ├── calculators.js   All calculator logic (EMI, SIP, FD, tax, etc.)
│   │   ├── learn-hub.js     Renders case-study cards on learn.html
│   │   └── learn-play.js    Generic engine that runs any case study
│   └── images/
│       └── logo-mark.jpg    Your logo
└── README.md                This file
```

## Deploying

There is nothing to build. Any static host works — Netlify, Vercel, GitHub
Pages, Cloudflare Pages, S3 + CloudFront, etc. Just upload the `rooted-rupees`
folder as-is, with `index.html` at the root.

Because pages are linked with **relative paths** (e.g. `resources.html`,
`assets/css/style.css`), the site works whether it's deployed at a root
domain (`rootedrupees.com`) or a sub-path.

## Adding a new page

Copy the closest existing page (e.g. `about-us.html`) as a starting point —
it already has the header, footer, and shared classes wired up. Add a new
link to it in the `<nav>` block (and `.mobile-menu`) across every page.

## Adding a new calculator

All calculator UI lives in **one page**, `resources.html`, with a left-hand
list of calculators and a matching panel for each (`#panel-xxx`). To add one:

1. Add a `<button data-calc="yourcalc">` in `.calc-list` in `resources.html`.
2. Add a `<div class="calc-panel" id="panel-yourcalc">` with your inputs
   and a `.calc-results` block (copy an existing panel as a template).
3. Add a `computeYourCalc()` function in `assets/js/calculators.js` that
   reads your input values by `id` and writes results back into the DOM.
4. Call your new function inside `runAll()` at the bottom of `calculators.js`.

## Adding a new "Learn" case study

This is the part designed to grow every day without touching any other code.

1. **Duplicate the template folder:**
   `learn/case-studies/_template/` → `learn/case-studies/your-case-study-slug/`
   (use lowercase-with-hyphens — this becomes the URL).

2. **Fill in `data.js`** inside your new folder with your chapters and
   questions. Structure:

   ```js
   window.CASE_STUDY = {
     slug: "your-case-study-slug",
     title: "Your Case Study Title",
     subtitle: "One line describing the takeaway.",
     level: 1,
     estTime: "10 min",
     chapters: [
       {
         id: "chapter-1",
         title: "Chapter Title",
         story: "The narrative / scenario shown before the questions.",
         questions: [
           {
             prompt: "Your question?",
             options: ["Option A", "Option B", "Option C", "Option D"],
             correct: 1,               // zero-based index of the right answer
             explanation: "Why that answer is correct."
           }
         ]
       }
       // add as many chapters as you like — they become "sub-levels"
     ]
   };
   ```

3. **Register it** by adding one entry to
   `learn/case-studies/index.js`:

   ```js
   {
     slug: "your-case-study-slug",       // must match the folder name exactly
     title: "Your Case Study Title",
     description: "Shown on the card in the Learn hub.",
     tag: "Personal Finance Basics",     // used for the filter pills
     level: 1,
     estTime: "10 min",
     chapterCount: 3,                    // how many chapters you wrote above
     icon: "shield"                      // shield | piggy | chart | card | target
   }
   ```

That's it — no other file changes. The new case study will automatically
appear as a card on `learn.html`, and `learn/play.html?study=your-case-study-slug`
will run it. Chapters unlock in order as the learner completes them, and
progress is remembered per-browser via `localStorage`.

## Notes

- The logo image doubles as the hero monogram, the nav mark, and the favicon.
  Swap `assets/images/logo-mark.jpg` for a higher-resolution or transparent
  PNG whenever you have one, no code changes needed.
- All calculators run entirely client-side — nothing is sent to a server.
  The tax calculator uses simplified, illustrative slabs and should not be
  treated as tax advice.
- Colors, type, spacing and radii are all defined once as CSS variables at
  the top of `assets/css/style.css` — change them there to re-theme the
  whole site.
