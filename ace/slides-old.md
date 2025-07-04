# Define CSS for Google Style
style: |
  /* Basic Theme Approximation */
  :root {
    --gslide-font-family: 'Roboto', sans-serif;
    --gslide-title-color: #4285F4; /* Google Blue */
    --gslide-text-color: #3c4043;  /* Dark Gray */
    --gslide-bg-color: #ffffff;    /* White */
    --gslide-placeholder-color: #757575; /* Medium Gray */
  }

  /* Apply base font and color */
  .slidev-layout {
    font-family: var(--gslide-font-family);
    color: var(--gslide-text-color);
    background-color: var(--gslide-bg-color);
    padding: 2rem 4rem; /* Typical padding */
  }

  /* Title Styling */
  .slidev-layout h1 {
    color: var(--gslide-title-color);
    font-weight: 500; /* Medium weight */
    margin-bottom: 1.5rem;
  }

  /* Body Text Styling */
  .slidev-layout p,
  .slidev-layout li {
    font-size: 1.1rem;
    line-height: 1.6;
  }

  .slidev-layout ul {
    margin-left: 1.5rem; /* Indent lists */
  }

  /* Title Slide Specifics */
  .gslides-title-slide h1 {
    font-size: 2.8em; /* Larger title */
    color: var(--gslide-title-color);
  }
  .gslides-title-slide p { /* Style presenter/date if they are paragraphs */
    font-size: 1.2em;
    color: var(--gslide-text-color);
    margin-top: 0.5em;
  }

  /* Content Slide Specifics */
  .gslides-content-slide h1 {
     font-size: 2em; /* Standard content title size */
     text-align: left; /* Align title left */
  }

  /* Placeholder Text Styling */
  .placeholder-text {
    color: var(--gslide-placeholder-color);
    font-style: italic;
  }

  /* Center align class helper */
  .text-center {
    text-align: center;
  }

head: |
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet">




---
class: gslides-content-slide
---

# IaaS, PaaS and SaaS

image: /content/week1/imgs/iaas-paas-saas.jpg

<!--
Speaker Notes

Briefly define cloud computing models (IaaS, PaaS, SaaS) with examples relevant to banking/finance environments.
-->




src: ./content/week1/03-global-infra.md
---
src: ./content/week1/04-resource-hierarchy.md
---
src: ./content/week1/05-interacting.md
---
src: ./content/week1/06-billing.md
---
src: ./content/week1/07-compute-engine.md
---
src: ./content/week1/08-cloud-storage.md
---
src: ./content/week1/09-vpc.md
---
src: ./content/week1/10-iam.md
---
src: ./content/week1/11-takeaways.md
---
src: ./content/week1/12-next-steps.md
---
