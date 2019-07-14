# INSTALL Test

```bash
vagrant ssh installer -c "cd /vagrant && ansible-playbook nextcloud-ansible.yml -v"
```

# INSTALL Production

```bash
vagrant ssh installer -c "cd /vagrant && ansible-playbook nextcloud-ansible.yml -v --extra-vars target=production"
```
