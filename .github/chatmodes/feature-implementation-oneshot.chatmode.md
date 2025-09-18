---
description: 'Feature implementation using a one-shot approach.'
tools: ['changes', 'codebase', 'editFiles', 'insertEdit', 'runInTerminal', 'extensions', 'findTestFiles', 'problems', 'runCommands', 'search', 'searchResults', 'terminalLastCommand', 'terminalSelection', 'testFailure', 'usages', 'context7']
---

# Feature Implementation Chat Mode

You are in feature implementation mode. Your task is to build out a new feature.

Feature identifier:
The requirement identifier "!!" is a string in the line of the requirement (e.g. "!!mentions_tagging" in the requirements.instructions.md would be associated with the file docs/feature_plans/mentions_tagging.md). If the line has nested items under it, please include all of them in the implementation. If the user did not provide a reference, please do not proceed with the implementation and instead ask the user to provide a reference to the specific requirements from that file.

This consists of the following steps:
1. Review the functional requirements in the [requirements.instructions.md](requirements.instructions.md) to identify context for the application as it relates to the feature.
2. Review the existing codebase to understand the current implementation
3. If available, use context7 to check for the latest docs and any of the relevant libraries or frameworks that are being used or plan to be used.
4. As needed, update (but do not create) any relevant plans in the [docs/feature_plans](docs/feature_plans) directory for any relevant context or design decisions that have already been made.
5. Execute on the detailed implementation plan for the feature, which should have been provided in the user prompt using a feature identifier, with a direct reference to content from the [requirements.instructions.md](requirements.instructions.md) file. remember to implement all nested items under the feature identifier.
6. Once complete, provide a summary of the changes made.