# Changelog

## 2.0.1 — 2026-11

### Fixed
- `ansible-galaxy role install` no longer fails with "Expected role dependencies to be a list": the collection
  list moved from `meta/requirements.yml` (galaxy reads that file as role dependencies) to `requirements.yml` in the
  role root. A role cannot install collections; the project lists them in its own `requirements.yml`
  (see Installation in the README).

## 2.0.0 — 2026-11

### Changed
- Variables stay compatible (`user_names` / `ssh_users`, `ssh_directory_users_keys`, `user`, `sudo`, `auth_key`,
  `append`, `shell`, `git`, `path_key_pair`). `shell` and `append` are optional now.

### Fixed
- `lookup('file', '{{ ... }}')` with nested templating (deprecated).
- `~/.ssh` for git was assumed to be `/home/<user>` and was not created. The user's real home is used now.
- `sudo: false` did not remove an existing sudoers entry.
- Private keys could appear in logs (no `no_log`). `~/.ssh/config` was `0400` and overwritten as a whole (now an
  Ansible-managed block, `0600`).

### Added
- `state: absent`, `groups` list, `comment`, extra `keys`, `exclusive_keys`, molecule tests.
