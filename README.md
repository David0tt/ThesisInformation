# Thesis Information (Cognitive Systems Chair)
This repository collects practical information to help you get started with a thesis at the Cognitive Systems Chair.

## Related Tutorials
- If you are going to work with the Franka Emika Panda robot arm, also look at: [TUTORIAL_PANDA.md](TUTORIAL_PANDA.md)
- Docker setup tips: [TUTORIAL_DOCKER.md](TUTORIAL_DOCKER.md)
- Miscellaneous tips: [MISCELLANEOUS_TIPS.md](MISCELLANEOUS_TIPS.md)

## Thesis Registration Checklist
To actually register and start your thesis, you need three forms:

- **Anmeldung B.Sc./M.Sc Abschlussarbeit (PDF)**: for the Prüfungssekretariat (download the `"Anmeldung B.Sc./M.Sc Abschlussarbeit (PDF)"`  from [Informationen und Formulare](https://uni-tuebingen.de/fakultaeten/mathematisch-naturwissenschaftliche-fakultaet/fachbereiche/informatik/studium/studierende/downloads/informationen-und-formulare/))
- **New Member Registration Form**: to register with our chair (PC account, keys, ...): [NewMemberRegistrationForm](Documents/new_member_v2.11.odt)
- **Thesis Description**: a written description to clearly specify the thesis scope and goals.
    - Make sure everything you want to do is included.
    - Make sure the goals required to succeed are clearly specified (and not too simple or too complicated).
    - Make sure you like the title.
    - Examples:
        - [BA Topic Description Skeleton](https://docs.google.com/document/d/1BST6j_XuPu2jJa8qoJgMplgKemBLvLRgrG3axsLMd18/edit?usp=sharing)
        - [MA Topic Description Skeleton](https://docs.google.com/document/d/1KSHxpYyuzGTeAS9YtfURSxvGVGhJHYPVFkRXBwS_OX4/edit?usp=sharing)
        - [Example MA Topic Description](https://docs.google.com/document/d/1_Occ33t0WR2acv4TvGPmU-Hl5YFcavM_-p3D6-A3g9c/edit?usp=sharing)

### Hand-in / Signatures
After you have filled out all three documents:

1. Come to the chair and bring the **Anmeldung B.Sc./M.Sc Abschlussarbeit (PDF)** and the **Thesis Description** so the professor can sign. If you are registering a M.Sc. Thesis, you also need the signature of your second examinor.
2. Make three copies of the signed **Anmeldung B.Sc./M.Sc Abschlussarbeit (PDF)**:
     - one for the professor (and your second examinor)
     - one for your supervisor
     - one for yourself
3. Hand in the original signed **Anmeldung B.Sc./M.Sc Abschlussarbeit (PDF)** to the Prüfungssekretariat.
4. Give the **New Member Registration Form** to our IT-Administrator.

Send me a quick message before you come, so we can hand things in together.

## Supervision
- We typically meet weekly at most. Depending on preference and progress, we can meet less often.
- Please prepare a short update for meetings: what you did, what you plan to do next, and (most importantly) questions. This can be notes or a small slide deck.
- We use Discord to communicate within the group. You will get an invite link, and we will create a channel for your thesis. Please contact me sooner rather than later via Discord if you have questions (I usually reply quickly).
- In an early meeting you should create a planned timeline for the whole project.

## General Recommendations

### Note-taking
Keep notes of what you have done and your progress. This makes it easier to estimate progress and will make writing at the end go more smoothly. Some people like to use Kanban/Trello/Obsidian/etc. Personally, I use a simple text/Markdown file and tag entries (LEARNING, TODO, FINDING, RESULT, ...).

### Writing the thesis
You should follow general academic writing tips, e.g.:
- Use active voice
- Stick to a consistent tense
- Be concise
- Be formal

Your thesis should roughly follow this outline (deviations are fine if you have good reasons):
1. Introduction
2. Related Work
3. Method
4. Experiments
5. Results
6. Discussion
7. Conclusion and Future Work

**Literature review:** Use search engines like [Google Scholar](https://scholar.google.com/) or [Semantic Scholar](https://www.semanticscholar.org/). **Introductory resources on how to read papers:** [How To Read A Paper (by S. Keshav)](https://web.stanford.edu/class/cs114/reading-keshav.pdf) and [Reading Research Papers by Andrew Ng](https://youtu.be/733m6qBH-jI)

### Formatting Basics
- LaTeX templates can be found in [Documents](Documents)
- Figures should be legible (large enough text, sensible colors, ...). If possible, store figures as PDF (e.g., export from matplotlib as PDF). For consistent plotting you might want to use the [tueplots](https://github.com/pnkraemer/tueplots) library.
- Every figure, table, and pseudo-code you add should be mentioned and cited in the text.
- If you make claims, back them up with scientific literature (papers) or experimental results.
- Captions (the text under figures) should be self-contained.
- Citations should follow a consistent citation style (LaTeX + BibTeX).
- You are required to append and sign the `Erklärung Abschlussarbeit (PDF)` found at [Informationen und Formulare](https://uni-tuebingen.de/fakultaeten/mathematisch-naturwissenschaftliche-fakultaet/fachbereiche/informatik/studium/studierende/downloads/informationen-und-formulare/). I generally recommend selecting `3.2` and `3.3`. 


### Handing in the Thesis
Please refer to [Wo und wie gebe ich meine Bachelor- oder Masterarbeit ab?](https://uni-tuebingen.de/de/268383#c2073306) for specific instructions on handing in your thesis. 
Additionally you need to hand in a printed version of your thesis with "Schwarze Klebebindung" since professor generally requires this. 

### Printing
You may print your thesis on one of our printers for free. You then only need to bind it at a copy shop ("Schwarze Klebebindung" is required).

## Tool Recommendations
- [Git](https://git-scm.com/): robust versioning (GitHub or our [GitLab](https://gitlab.cs.uni-tuebingen.de/)). If you are new to Git: [Git Guide](https://github.com/git-guides), [Git tutorial](https://git-scm.com/docs/gittutorial)
- [Visual Studio Code](https://code.visualstudio.com): simple and highly customizable (however you can use any IDE you prefer)
- [Docker](https://www.docker.com/): stable, independent environments. Consider a [Docker development environment workflow](https://learn.microsoft.com/en-us/training/modules/use-docker-container-dev-env-vs-code/). See also: [TUTORIAL_DOCKER.md](TUTORIAL_DOCKER.md)
- [Zotero](https://www.zotero.org/): manage papers, highlights, notes, and citations

## AI Assisted Coding

Generative AI tools are becoming increasingly important for modern software development. Your thesis presents an excellent opportunity to explore these technologies and integrate them into your workflow. However, given the rapid pace of development in this field, strategies and tools evolve quickly, so these tips should be taken as my personal opinion.

### General Guidelines
- **Avoid "Vibe-coding"**: You will quickly find that pure "vibe-coding" (generating code without true understanding and oversight) will not work well in solving actually complicated problems and will produce extremely large amounts of unwieldy code and slow you down immensely. **You are solely responsible for the code you create and commit!**
- **IDE-based Workflow**: I personally like to use an IDE-based workflow that allows you to review proposed changes (diffs) meticulously. I recommend [GitHub Copilot Pro](https://education.github.com/pack) which is free for students and provides access to state-of-the-art models (e.g., GPT-5.3, Claude Sonnet/Opus, Gemini 3).
- **CLI Tools**: You can also try cli-based systems like [OpenAI Codex](https://openai.com/codex/), [Gemini-CLI](https://github.com/google-gemini/gemini-cli), and [claude-code](https://github.com/anthropics/claude-code) can work better for vibe-coding style development, since the models are finetuned to work in these specific environments. However, they give me too little precise control (e.g. diff views) for high-quality production grade code, or code which will live for a longer time. 
- **Agentic tools** are very powerful. If possible, let the AI iterate based on tool feedback until the desired output is achieved.
- You can attach images or screenshots (e.g., to sketch a figure you want the AI to create in tikz/matplotlib).
- Make sure to sufficiently **sandbox** the AI agents. A docker development container workflow works very well for this.
- Make sure to **save and backup your codebase** well for iteration by using git. 
- You can create custom prompts for common tasks with `.prompt.md` files and general instructions with `.instruction.md` files. To do this press the settings wheel and select `Prompt Files` or `Instructions & Rules`. 

### Model Recommendations
- **General Tasks**: **Gemini 3 Flash** This is my go-to model for almost all daily tasks, since it is fast, cheap and quite capable.
- **In-depth Coding**: **Claude Sonnet** is excellent for coding (e.g., refactoring), while **Claude Opus** has strong high-level abstraction and planning capabilities but is more expensive. For an initial draft of a vibe-coded project I would highly recommend to start with **Claude Opus** and then use cheaper models for the refinement.
- **High Level Planning and knowledge**: I find that **GPT-5.3** has the best knowledge of software libraries, while it is lacking in coding in comparison to **Claude Sonnet**. **Gemini 3 Pro** is somewhere in the middle between these two models in both regards.
- **GPT-(...)-Codex** sadly does not work well for me in VSCode, however this model is reported to be very good in OpenAI-Codex.

### Uses where I find AI assisted coding tools highly useful:
- **Creating initial prototypes** which will not be iterated on further. Here the AI allows you to find whether something is in principle feasible or useful. 
- **Implementing changes from precise human specifications** under strict oversight for "production-grade" codebases.
- **Iterating in an agentic tool call fashion** to find a prototype or to fix a specific problem (e.g. build issues).
- **As a sparring partner** to examine or explain code or discuss potential options. However, anything needs to be verified with your own intelligence. If you really want to understand some code, you need to read it yourself, the AI only provides you with an initial lay of the land.
- **to clean up written text, e.g. documentation or academic writing** (but with high amounts of oversight).
- **For refactoring, to implement style guides or type hints.**


#### Uses where AI assisted coding does not work for me:
- To implement critical code.
- To one-shot full complex tasks. It is your job to break this down into feasible smaller tasks.
- To solve specific tasks, which have not been solved often in the training data.
- any application where real world state needs to be managed (e.g. robotics, multi-threaded applications).

