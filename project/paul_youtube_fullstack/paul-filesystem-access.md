---
publish: true
---

# Paul Filesystem Access - chatterbox-runpod
[[client-paul]]

## Context
- **Issue:** DaveX2001/deliverable-tracking#34
- **Date:** 2025-12-01
- **Server:** Remote SSH instance (hetzner)
- **Purpose:** Paul needs read-only access to review code changes

## Implementation

**Group created:** `paul_faceless_yt`

```bash
# Create shared group
sudo groupadd paul_faceless_yt

# Add users to group
sudo usermod -aG paul_faceless_yt marius
sudo usermod -aG paul_faceless_yt paul

# Change directory ownership
sudo chown -R marius:paul_faceless_yt /home/marius/projects/chatterbox-runpod

# Set group read permissions + SGID for inheritance
sudo chmod -R g+rX /home/marius/projects/chatterbox-runpod
sudo find /home/marius/projects/chatterbox-runpod -type d -exec chmod g+s {} \;

# Allow traversal through parent directories
sudo chmod o+x /home/marius
sudo chmod o+x /home/marius/projects
```

## Verification

```bash
# Check group exists
getent group paul_faceless_yt

# Check user membership
groups paul
groups marius

# Test paul can read
su - paul -c "ls /home/marius/projects/chatterbox-runpod"
```

## Notes
- SGID bit ensures new files inherit `paul_faceless_yt` group
- No workflow change needed for marius - files auto-inherit
- Parent directories (`/home/marius`, `/home/marius/projects`) need execute bit for traversal

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

