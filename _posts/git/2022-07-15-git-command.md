---
layout: post
title: git commands!
published: false
---
- `git remote prune origin`: 
  This helper deletes all outdated local branches, which don't exist on the remote anymore. 
- `git config remote.origin.prune true`
  You can also configure git to remove all outdated branches when you call git fetch via