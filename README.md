# Ansible Role: pastir.users

Manages Linux users: accounts, authorized SSH keys, passwordless sudo, an optional SSH key pair and Git (GitHub)
SSH access. Users can also be removed.

## Requirements

- ansible-core >= 2.16, collections `ansible.posix`, `community.general` (`requirements.yml` in the role root)

## Variables

| Variable | Default | Description |
|---|---|---|
| `ssh_directory_users_keys` | `/etc/ansible/files/pair_key` | Directory **on the controller** with `<user>/<user>.pub` and `<user>/<user>` |
| `ssh_users` | `user_names` or `[]` | Users, see below (`user_names` is accepted as before) |
| `users_default_shell` | `/bin/bash` | |
| `users_remove_home` | `true` | Remove the home directory of `state: absent` users |

### User item

| Key | Default | Description |
|---|---|---|
| `user` | — | **Required.** Login name |
| `state` | `present` | `absent` removes the user, their home and their sudoers file |
| `shell` | `users_default_shell` | |
| `groups` | `[]` | Extra groups. The legacy `append: "a,b"` string still works |
| `comment` | — | GECOS |
| `sudo` | `false` | `true` creates `/etc/sudoers.d/<user>` with `NOPASSWD: ALL`. `false` removes it |
| `auth_key` | `false` | Install `<dir>/<user>/<user>.pub` |
| `keys` | `[]` | Additional public keys |
| `exclusive_keys` | `false` | `authorized_keys` keeps only the managed keys |
| `path_key_pair` | — | Copy the key pair to `<path>/id_rsa`, `<path>/id_rsa.pub` |
| `git` | `false` | Private key as `~/.ssh/id_rsa.git` plus a `github.com` block in `~/.ssh/config` |

Private keys are copied with `no_log`.

## Example

```yaml
- hosts: all
  become: true
  vars:
    ssh_directory_users_keys: /etc/ansible/files/pair_key
    user_names:
      - user: alice
        sudo: true
        auth_key: true
      - user: deploy
        auth_key: true
        git: true
      - user: former.employee
        state: absent
  roles:
    - pastir.users
```

## Testing

`molecule test` (24.04, 26.04, test-only keys in `molecule/default/keys`): creation, sudo, git access, exclusive
keys, revoking sudo, removing a user.

## Installation

```yaml
# requirements.yml
roles:
  - name: pastir.users
    src: git+git@github.com:Pastir/ansible-role-users.git
    version: main  # or a tag
# A role cannot install collections by itself: the project lists them (see requirements.yml in the role root).
collections:
  - name: ansible.posix
  - name: community.general
```

```bash
ansible-galaxy install -r requirements.yml   # roles and collections
```

## License

MIT
