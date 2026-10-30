# AI Features

Read this when the screen includes AI: a chat or assistant, suggestions or autocomplete, recommendations, generated text or images, automatic actions, or anything that adapts to the user. AI features fail in a different way from normal UI: they are sometimes wrong, and users can't see why. Design for that.

## Contents
1. Decide how much the AI should do on its own
2. Microsoft's 18 guidelines for human-AI interaction
3. Adaptive interfaces: keep the backbone stable
4. Explanations that users can act on
5. Chat and generated-content UX
6. Trust, tone and provenance
7. Checklist
8. Sources

## 1. Decide how much the AI should do on its own

Eric Horvitz's mixed-initiative model (CHI 1999): for each AI service, compare three options by their expected cost and benefit.

| AI confidence the user wants this | Cost if wrong | Do |
|---|---|---|
| Low | Any | Nothing. Stay out of the way |
| Medium | Any | Ask or suggest, lightly (an inline suggestion, ghost text, a chip) |
| High | Low and reversible | Act, and show what was done with Undo |
| High | High or irreversible | Still ask first |

Horvitz's principles that matter most for builders:
- Automation must add real value over doing it by hand.
- Time suggestions around attention: don't pop up while the user is typing, dragging or in a focused task.
- Make services quick to invoke and quick to dismiss (one tap or Esc).
- Keep the cost of a wrong guess low: suggestions are optional, never block the main path.
- When unsure, do less: scope the service down rather than guessing big.
- Let users refine the result instead of starting over.

## 2. Microsoft's 18 guidelines for human-AI interaction

Amershi et al., CHI 2019. Use as a checklist for any AI feature.

**Initially**
1. Make clear what the system can do.
2. Make clear how well it can do it (it will make mistakes).

**During interaction**
3. Time services based on context.
4. Show contextually relevant information.
5. Match relevant social norms.
6. Mitigate social biases.

**When wrong**
7. Support efficient invocation.
8. Support efficient dismissal.
9. Support efficient correction.
10. Scope services when in doubt.
11. Make clear why the system did what it did.

**Over time**
12. Remember recent interactions.
13. Learn from user behavior.
14. Update and adapt cautiously.
15. Encourage granular feedback.
16. Convey the consequences of user actions.
17. Provide global controls (turn features off, privacy settings).
18. Notify users about changes.

Guidelines 1–2 in practice: a one-line capability note near the AI entry point ("Drafts replies from your notes. Check facts before sending.") calibrates trust better than a long onboarding.

## 3. Adaptive interfaces: keep the backbone stable

Interfaces that rearrange themselves break spatial memory. Gajos et al. (CHI 2008) found that both predictability and accuracy raise satisfaction, and accuracy had the stronger effect on performance and on how much people used the adaptive part.

Pattern from that research line, the **split interface**:
- Keep the full menu, navigation and toolbar fixed in place.
- Put predicted or recently used items in a separate, clearly marked area ("Suggested", "Recent").
- Copy items there; don't move them out of their original place.
- If a prediction is wrong, the user loses nothing; the original is where it always was.

Apply the same idea to AI-generated layouts and dashboards: generated parts can change, the frame and navigation can't. Show what changed and let users revert.

## 4. Explanations that users can act on

Kulesza, Burnett et al., "Principles of Explanatory Debugging" (IUI 2015). Their approach increased participants' understanding of the learning system by 52% and let them correct its mistakes up to twice as efficiently as a traditional approach.

Explanations should be:
- **Iterative**: shown in context, while the user works, not in a separate help page.
- **Sound**: true to how the model actually decided.
- **Complete**: show all the main factors, not a flattering subset.
- **Not overwhelming**: summarize first, details on demand (progressive disclosure).

Corrections should be:
- **Actionable**: the explanation is also the control. If the app says "Recommended because you liked X", let the user remove X from the reasons.
- **Reversible**: every correction can be undone.
- **Always honored**: user corrections persist; the next retraining or refresh must not silently undo them.
- **Visible**: after a correction, briefly highlight what changed so the user sees the effect.

Showing reasons against as well as reasons for a recommendation improved trust, engagement and persuasion in a human-subject study (Polleti et al., 2020). Prefer "Good for: … / Watch out for: …" over one-sided praise.

## 5. Chat and generated-content UX

These are common practice in current AI products rather than single studies; they apply the general rules in `states-and-feedback.md` to AI latency and errors.

- **Stream** output as it generates. The first words appearing quickly matter more than total time.
- **Stop** button while generating; **Regenerate** and **Edit prompt** after.
- **Never lose the user's input**: on error, keep the prompt in the box and offer Retry. Say what failed in plain words (rate limit, timeout, content too long), not a raw API error.
- **Long tasks** (agents, file processing): show steps or progress, allow cancel, and let the user keep working elsewhere.
- **Empty state** for a chat or AI tool: 3–4 example prompts that show what it does well, not a blank box.
- **Copy, share, and insert** actions on generated results; one tap to put the result where it's needed.
- **Cite sources** when the answer draws on documents or the web; let users open them.
- **Feedback controls** (thumbs, "wrong because…") that are quick and optional (guideline 15).
- **Mark AI-generated content** clearly, especially anything that will be sent to other people.
- **Don't auto-rewrite personal messages** without the user seeing and approving the change.

## 6. Trust, tone and provenance

- Productivity tools: keep the assistant's voice neutral and direct. Avoid fake human cues (name, age, gender, "I feel"), overconfident wording, and apology loops. State uncertainty plainly ("I'm not sure; check the dates").
- Don't overclaim. Calibrated trust is the goal: users should rely on the AI where it's good and check it where it isn't (guidelines 1, 2, 11).
- For AI-generated images, audio or video that leave the app, content-provenance labels (the C2PA standard, "Content Credentials") help others know how the content was made.
- Global controls: a clear way to turn AI features off or limit what data they use (guideline 17).

## 7. Checklist

- [ ] The UI says what the AI can do and that it can be wrong
- [ ] Suggestions are optional, dismissible in one step, and don't interrupt typing
- [ ] Automatic actions are reversible and announced with Undo
- [ ] Core navigation never moves; adaptive items live in their own area
- [ ] "Why" is available for recommendations and decisions, and doubles as a control
- [ ] Generation streams, can be stopped, regenerated and edited
- [ ] Errors keep the user's input and offer Retry
- [ ] AI output is labeled where it matters; sources are linkable
- [ ] Corrections persist and the user can see their effect
- [ ] Users can turn the feature off

## 8. Sources

- Horvitz, Principles of Mixed-Initiative User Interfaces (CHI 1999) — https://www.erichorvitz.com/chi99horvitz.pdf
- Amershi et al., Guidelines for Human-AI Interaction (CHI 2019) — https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/ and summary https://medium.com/microsoft-design/guidelines-for-human-ai-interaction-9aa1535d72b9
- Gajos et al., Predictability and Accuracy in Adaptive User Interfaces (CHI 2008) — https://www.eecs.harvard.edu/~kgajos/papers/2008/gajos08predictability.shtml
- Kulesza et al., Principles of Explanatory Debugging to Personalize Interactive Machine Learning (IUI 2015) — https://openaccess.city.ac.uk/id/eprint/13819/
- Polleti, de Souza, Cozman, Why should I not follow you? Reasons for and reasons against in responsible recommender systems (2020) — https://arxiv.org/abs/2009.01953
