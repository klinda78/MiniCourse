# Request Schema

```json
{
  "learner": {
    "foundation": "required text",
    "goal": "required text",
    "knowledgeType": "conceptual_understanding | memory_accumulation | problem_solving | operational_skill | situational_application",
    "applicationScenario": "required text"
  },
  "source": { "kind": "text | file | url", "value": "optional" },
  "constraints": { "lessonCount": 1, "targetMinutesPerLesson": 5, "language": "optional" },
  "providerPreset": "low-cost | simple | advanced",
  "providerModel": "configured model identifier"
}
```

The four learner fields require explicit confirmation. `lessonCount` is 1–20. Never include provider credentials, License text, prompts, provider envelopes, Session state, or temporary URLs.
