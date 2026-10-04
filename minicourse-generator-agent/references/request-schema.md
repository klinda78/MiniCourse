# Agent Request Schema

The request file contains only user intent, optional source baseline, and bounded generation constraints. It must not contain API keys, License text, provider response envelopes, prompts, session state, or temporary asset URLs.

```json
{
  "learner": {
    "foundation": "currentFoundation",
    "goal": "learningGoal",
    "knowledgeType": "conceptual_understanding | memory_accumulation | problem_solving | operational_skill | situational_application",
    "applicationScenario": "applicationScenario"
  },
  "source": { "kind": "text | file | url", "value": "optional source reference" },
  "constraints": {
    "lessonCount": 1,
    "targetMinutesPerLesson": 5,
    "language": "optional language tag"
  },
  "providerPreset": "low-cost | simple | advanced",
  "providerModel": "configured model identifier"
}
```

All four learner fields are required and must be explicitly confirmed. `lessonCount` is between 1 and 20; `targetMinutesPerLesson` is between 1 and 60. Provider credentials are resolved by the installed CLI and are never placed in this JSON.
