---
mode: 'agent'
tools: ['codebase', 'runCommands']
description: 'Generate a new git commit based on all the changes in the codebase since the last commit. The commit should be descriptive.'
---
Your goal is to generate a git commit for this repository based on all the changes since the last git commit. Ultimately, you just need to:
1) Run `git add .` to make sure everything is in place for the commit
2) Run `git diff HEAD` to see the changes since the last commit
3) Generate a clear and descriptive commit message based on the changes

Be clear and descriptive, but concise. Focus on the changes made and provide any important context that will help understand the commit.

Note: If you cannot run the commands, please stop and ask me to fix that for you.